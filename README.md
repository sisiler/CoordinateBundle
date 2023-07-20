# Installation

Добавить в kernel

    Evrinoma\CoordinateBundle\EvrinomaCoordinateBundle::class => ['all' => true],

Добавить в routes

    coordinate:
        resource: "@EvrinomaCoordinateBundle/Resources/config/routes.yml"

Добавить в composer

    composer config repositories.dto vcs https://github.com/evrinoma/DtoBundle.git
    composer config repositories.dto-common vcs https://github.com/evrinoma/DtoCommonBundle.git
    composer config repositories.utils vcs https://github.com/evrinoma/UtilsBundle.git

# Configuration

преопределение штатного класса сущности

    coordinate:
        db_driver: orm модель данных
        factory: App\Coordinate\Factory\Coordinate\Factory фабрика для создания объектов,
                 недостающие значения можно разрешить только на уровне Mediator
        entity: App\Coordinate\Entity\Coordinate сущность
        constraints: Вкл/выкл проверки полей сущности по умолчанию 
        dto: App\Coordinate\Dto\CoordinateDto класс dto с которым работает сущность
        decorates:
          command - декоратор mediator команд координаты 
          query - декоратор mediator запросов координаты
        serializer:
          enabled - подключить конфиги сериализатора
          path - относительный путь до конфигов 
        services:
          pre_validator - переопределение сервиса валидатора координаты
          handler - переопределение сервиса обработчика сущностей

# CQRS model

Actions в контроллере разбиты на две группы
создание, редактирование, удаление данных

        1. putAction(PUT), postAction(POST), deleteAction(DELETE)
получение данных

        2. getAction(GET), criteriaAction(GET)

каждый метод работает со своим менеджером

        1. CommandManagerInterface
        2. QueryManagerInterface

При переопределении штатного класса сущности, дополнение данными осуществляется декорированием, с помощью MediatorInterface


группы  сериализации

    1. API_GET_COORDINATE, API_CRITERIA_COORDINATE - получение координаты
    2. API_POST_COORDINATE - создание координаты
    3. API_PUT_COORDINATE -  редактирование координаты

# Статусы:

    создание:
        координата создана HTTP_CREATED 201
    обновление:
        координата обновление HTTP_OK 200
    удаление:
        координата удален HTTP_ACCEPTED 202
    получение:
        координата(и) найдены HTTP_OK 200
    ошибки:
        если координата не найдена CoordinateNotFoundException возвращает HTTP_NOT_FOUND 404
        если координата не уникальна UniqueConstraintViolationException возвращает HTTP_CONFLICT 409
        если координата не прошла валидацию CoordinateInvalidException возвращает HTTP_UNPROCESSABLE_ENTITY 422
        если координата не может быть сохранена CoordinateCannotBeSavedException возвращает HTTP_NOT_IMPLEMENTED 501
        все остальные ошибки возвращаются как HTTP_BAD_REQUEST 400

# Constraint

Для добавления проверки поля сущности coordinate нужно описать логику проверки реализующую интерфейс Evrinoma\UtilsBundle\Constraint\Property\ConstraintInterface и зарегистрировать сервис с этикеткой evrinoma.coordinate.constraint.property

    evrinoma.coordinate.constraint.property.custom:
        class: App\Coordinate\Constraint\Property\Custom
        tags: [ 'evrinoma.coordinate.constraint.property' ]

## Description
Формат ответа от сервера содержит статус код и имеет следующий стандартный формат
```text
    [
        TypeModel::TYPE => string,
        PayloadModel::PAYLOAD => array,
        MessageModel::MESSAGE => string,
    ];
```
где
TYPE - типа ответа

    ERROR - ошибка
    NOTICE - уведомление
    INFO - информация
    DEBUG - отладка

MESSAGE - от кого пришло сообщение
PAYLOAD - массив данных

## Notice

показать проблемы кода

```bash
vendor/bin/php-cs-fixer fix --config=.php-cs-fixer.dist.php --verbose --diff --dry-run
```

применить исправления

```bash
vendor/bin/php-cs-fixer fix --config=.php-cs-fixer.dist.php
```

# Тесты:

    COMPOSER_NO_DEV=0 composer install

### run all tests

    /usr/bin/php vendor/phpunit/phpunit/phpunit --bootstrap src/Tests/bootstrap.php --configuration phpunit.xml.dist src/Tests --teamcity

### run personal test for example testPost

    /usr/bin/php vendor/phpunit/phpunit/phpunit --bootstrap src/Tests/bootstrap.php --configuration phpunit.xml.dist src/Tests/Functional/Controller/ApiControllerTest.php --filter "/::testPost( .*)?$/" 

## Thanks

## Done

## License
    PROPRIETARY