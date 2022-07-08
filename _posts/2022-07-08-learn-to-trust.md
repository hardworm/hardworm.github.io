---
layout: post
title: Учись доверять, а не проверять
---

Расскажу об интересном кейсе по взаимодействию между своими системами под высокой нагрузкой. 
Есть система 1 - ERP для управления заказами. Есть система 2 - система закупок. 
Каждое утро во 2 системе массово создаются закупки - пополняются склады, клиентские заказы заказываются у поставщиков.
И все прекрасно, но с ростом закупок и заказов клиентские заказы начинают долго формироваться. 
А, поставщики любят получить закупку с утра, всё провести и оправить машину до обеда.

В результате отладки находим тормозящую функцию

```php
public function mapFromResponse(ClientOrderCommodityResponse $response, ?Supplier $supplier): array
    {
        $commodities = [];
        foreach ($response->getCommodities() as $commodityResponseItem) {
            $this->validateResponseItem($commodityResponseItem);
            $commodities[] = $this->mapResponseItemToCommodity(
                $response->getProcurementGuidFake(),
                $commodityResponseItem,
                $supplier
            );
        }

        $this->clientOrderCommodityRepository->flush();

        return $commodities;
    }
```

А тормозит тут, внезапно, валидация json. Это ответ api ERP системы с клиентскими заказами. В функции валидации нет ничего странного и выдающегося.

```php
private function validateResponseItem(ClientOrderCommodityResponseItem $item): void
    {
        $validationErrors = ValidationHelper::getViolationMessages($this->validator->validate($item));
        if (!empty($validationErrors)) {
            throw new RuntimeException('ERP вернул некорректные данные: ' . implode('', $validationErrors));
        }
    }
```

А дальше стандартный [symfony validation](https://symfony.com/doc/current/validation.html). 


Дело в объеме - каждый день стало создавать около 1 тысячи закупок, приблизительно 400 будет клиентских.
Ответ от 1 системы будет содержать в среднем 5000 json объектов. Валидация такого json будет занимать 2.5-3 сек.
И получается 3 * 400 = 1200 секунд на валидацию или целых 20 минут. Объемы закупок постоянно растут, бывают праздники и акции.
Т.е. время на валидацию может стать x2-x3.

Но это общение двух наших систем. Можно убрать валидацию и более качественно поработать над API системы 1 системы ERP.

Чаще проще доверять своим системам. Естественно, этот API никогда не должен быть доступен снаружи.