---
title: "Лабораторная работа №16"
subtitle: "Настройка VPN"
author:
  - "Майзингер Э. С."
institute:
  - "Российский университет дружбы народов, Москва, Россия"
date: "14 июня 2025"
lang: ru-RU
babel-lang: russian
babel-otherlangs: english
toc: false
toc-title: "Содержание"
slide_level: 2
aspectratio: 169
section-titles: true
theme: default
header-includes:
  - \usepackage{fontspec}
  - \setmainfont{DejaVu Sans}
  - \setsansfont{DejaVu Sans}
---

# Информация

## Докладчик

:::::::::::::: {.columns align=center}
::: {.column width="70%"}

* Майзингер Эллина Сергеевна  
* студент  
* НПИбд-02-22  
* Российский университет дружбы народов  
* [1132226489@pfur.ru](mailto:1132226489@pfur.ru)  

:::
::::::::::::::

# Цель работы

Создание защищенного VPN-соединения между:
- Сетью "Донская" (Москва)
- Университетом г. Пиза (Италия)

# Архитектура решения

## Параметры туннеля
| Параметр          | Москва          | Пиза            |
|-------------------|-----------------|-----------------|
| Tunnel IP         | 10.128.255.253  | 10.128.255.254  |
| Источник          | 198.51.100.2    | 192.0.2.20      |
| Назначение        | 192.0.2.20      | 198.51.100.2    |

# Ключевые настройки

## Конфигурация туннеля (Москва)
interface Tunnel0
 ip address 10.128.255.253 255.255.255.252
 tunnel source f0/1.4
 tunnel destination 192.0.2.20
## Маршрутизация через туннель
ip route 10.131.0.0 255.255.255.0 10.128.255.254

# Проверка работы
## Доступность узлов
Laptop-PT admin> ping 10.131.0.100
Reply from 10.131.0.100: bytes=32 time=45ms TTL=127
## Состояние туннеля
msk-donskaya-gw-1#show interface tunnel0
Tunnel0 is up, line protocol is up

# Итоговая топология 

![](./image/1.png) 

# Итоговая топология 

![](./image/2.png) 

# Выводы
GRE-туннель успешно установлен

Обеспечена безопасная связь между сетями

Все узлы доступны через VPN-соединение

