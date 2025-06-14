---
## Front matter
title: "Лабораторная работа №15"
subtitle: "Динамическая маршрутизация"
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

Настроить динамическую маршрутизацию между территориями организации с использованием протокола OSPF и организовать прямое соединение между сетью квартала 42 в Москве и филиалом в Сочи.

# Задание

1. Настроить динамическую маршрутизацию по протоколу OSPF на маршрутизаторах
2. Настроить прямое соединение между Москвой и Сочи
3. Проверить маршрутизацию с помощью ICMP-пакетов
4. Проанализировать изменение маршрутов при отключении VLAN

# Теоретическое введение

**OSPF (Open Shortest Path First)** - протокол динамической маршрутизации, использующий алгоритм Дейкстры для построения кратчайших путей. Основные особенности:
- Работает на основе состояния каналов (link-state)
- Использует разделение на зоны (areas)
- Требует назначения Router ID
- Автоматически перестраивает маршруты при изменениях топологии

# Выполнение работы

## Настройка OSPF на маршрутизаторах

### Настройка msk-donskaya-gw-1
msk-donskaya-gw-1(config)#router ospf 1
msk-donskaya-gw-1(config-router)#router-id 10.128.254.1
msk-donskaya-gw-1(config-router)#network 10.0.0.0 0.255.255.255 area 0
### Проверка соседей OSPF
cisco
msk-donskaya-gw-1#show ip ospf neighbor

## Настройка прямого линка Москва-Сочи
### Создание VLAN 7
cisco
provider-sw-1(config)#vlan 7
provider-sw-1(config-vlan)#name q42-sochi
### Настройка интерфейсов
cisco
msk-q42-gw-1(config)#interface f0/1.7
msk-q42-gw-1(config-subif)#ip address 10.128.255.9 255.255.255.252

## Проверка маршрутизации
### Трассировка до отключения VLAN 6
cisco
Laptop-PT admin> traceroute 10.130.0.200
### Трассировка после отключения VLAN 6
cisco
provider-sw-1(config)#interface vlan6
provider-sw-1(config-if)#shutdown

## Результаты
Успешно настроен OSPF на всех маршрутизаторах

Создан прямой линк между Москвой и Сочи

Проверена отказоустойчивость:

При отключении VLAN 6 трафик перенаправляется через прямой линк

После восстановления VLAN 6 маршрут возвращается к исходному

## Итоговый вид топологии сети

![Итог](image/1.png){#fig:004 width=100%}

![Итог2](image/2.png){#fig:004 width=100%}

## Выводы
В ходе работы была успешно настроена динамическая маршрутизация OSPF, обеспечивающая:

Автоматическое построение маршрутов

Отказоустойчивость сети

Оптимальное распределение трафика
Все требования задания выполнены в полном объеме.

## Ответы на контрольные вопросы
Какие протоколы относятся к динамической маршрутизации?

OSPF, EIGRP, RIP, BGP

Принцип работы OSPF:

Обмен LSA-пакетами

Построение топологической карты

Расчет кратчайших путей алгоритмом Дейкстры

Просмотр таблицы маршрутизации:
show ip route