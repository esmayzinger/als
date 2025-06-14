---
## Front matter
title: "Лабораторная работа №13"
subtitle: "Статическая маршрутизация в Интернете. Планирование"
author: "Майзингер Эллина Сергеевна"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
lot: true # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
## Fonts
mainfont: PT Serif
romanfont: PT Serif
sansfont: PT Sans
monofont: PT Mono
mainfontoptions: Ligatures=TeX
romanfontoptions: Ligatures=TeX
sansfontoptions: Ligatures=TeX,Scale=MatchLowercase
monofontoptions: Scale=MatchLowercase,Scale=0.9
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Pandoc-crossref LaTeX customization
figureTitle: "Рис."
tableTitle: "Таблица"
listingTitle: "Листинг"
lofTitle: "Список иллюстраций"
lotTitle: "Список таблиц"
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Организация взаимодействия между локальной сетью основного здания (42-й квартал Москвы) и филиалом в г. Сочи через сеть провайдера с использованием статической маршрутизации.

# Задание

1. Дополнить схемы L1-L3 информацией о новых площадках:
   - Основная территория (Москва, 42-й квартал)
   - Филиал (г. Сочи)
2. Настроить сетевое оборудование:
   - Маршрутизаторы и коммутаторы
   - VLAN и IP-адресацию
3. Реализовать статическую маршрутизацию между площадками.

# Теоретические сведения

**Статическая маршрутизация** - ручное назначение маршрутов в таблице маршрутизации. Используется:
- В небольших сетях
- Для фиксированных соединений
- Когда требуется полный контроль над маршрутами

Преимущества:
- Минимум нагрузки на оборудование
- Предсказуемость работы

# Выполнение лабораторной работы

## 1. Доработка схем сети

1. Добавлены в схему L1:
   - Основная территория:
     - Маршрутизатор `msk-q42-gw-1`
     - Коммутатор `msk-q42-sw-1`
     - Общежития:
       - Маршрутизатор `msk-hostel-gw-1`
       - Коммутатор `msk-hostel-sw-1`
   - Филиал в Сочи:
     - Маршрутизатор `sch-sochi-gw-1`
     - Коммутатор `sch-sochi-sw-1`

2. Назначены VLAN и IP-адреса согласно таблицам:

| Локация       | VLAN  | IP-подсеть       | Устройства         |
|---------------|-------|------------------|--------------------|
| Москва (осн.) | 201   | 10.129.0.0/24    | pc-q42-1 (10.129.0.200) |
| Общежития     | 301   | 10.129.128.0/24  | pc-hostel-1 (10.129.128.200) |
| Сочи          | 401   | 10.130.0.0/24    | pc-sochi-1 (10.130.0.200) |

## 2. Настройка оборудования

1. Базовые настройки маршрутизатора Москва:
   msk-q42-gw-1(config)#interface FastEthernet0/0.5
     encapsulation dot1Q 5
     ip address 10.128.255.2 255.255.255.252
   msk-q42-gw-1(config)#interface FastEthernet0/1.201
     encapsulation dot1Q 201
     ip address 10.129.0.1 255.255.255.0
     
2. Настройка маршрутизатора Сочи:
sch-sochi-gw-1(config)#interface FastEthernet0/0.6
  encapsulation dot1Q 6
  ip address 10.128.255.6 255.255.255.252
sch-sochi-gw-1(config)#interface FastEthernet0/1.401
  encapsulation dot1Q 401
  ip address 10.130.0.1 255.255.255.0
  
## Настройка статической маршрутизации

На основном маршрутизаторе (Донская):
msk-donskaya-gw-1(config)#ip route 10.129.0.0 255.255.0.0 10.128.255.2
msk-donskaya-gw-1(config)#ip route 10.130.0.0 255.255.0.0 10.128.255.6

На маршрутизаторе Москвы:
msk-q42-gw-1(config)#ip route 0.0.0.0 0.0.0.0 10.128.255.1
msk-q42-gw-1(config)#ip route 10.130.0.0 255.255.0.0 10.128.255.1

На маршрутизаторе Сочи:
sch-sochi-gw-1(config)#ip route 0.0.0.0 0.0.0.0 10.128.255.5
sch-sochi-gw-1(config)#ip route 10.129.0.0 255.255.0.0 10.128.255.5

## Проверка связности
Проверка маршрутов:
show ip route
Проверка ping между узлами:
pc-q42-1> ping 10.130.0.200  # Успешно
pc-sochi-1> ping 10.129.0.200  # Успешно

## Итоговый вид топологии сети

![Итог](image/1.png){#fig:004 width=100%}
![Итог](image/2.png){#fig:004 width=100%}

## Выводы
Построена распределенная сеть с основным офисом и филиалом.

Настроена статическая маршрутизация между всеми площадками.

Проверена работоспособность сети:

Обеспечена связность между всеми узлами

Маршруты работают корректно

Все требования задания выполнены.

## Ответы на контрольные вопросы

Когда использовать статическую маршрутизацию?
В небольших сетях с фиксированной топологией, для критически важных маршрутов, когда нужно минимизировать нагрузку на оборудование.

Принципы маршрутизации между VLAN
Требуется маршрутизирующее устройство (роутер или L3-коммутатор) с настроенными интерфейсами для каждого VLAN и прописанными маршрутами.
