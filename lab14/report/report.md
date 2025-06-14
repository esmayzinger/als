---
## Front matter
title: "Лабораторная работа №14"
subtitle: "Статическая маршрутизация в Интернете. Настройка"
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

Настроить взаимодействие между локальной сетью организации в Москве (42-й квартал) и филиалом в Сочи через сеть провайдера с использованием статической маршрутизации.

# Задание

1. Настроить связь между территориями
2. Настроить оборудование в Москве (42-й квартал)
3. Настроить оборудование в Сочи
4. Настроить статическую маршрутизацию между площадками
5. Настроить маршрутизацию внутри 42-го квартала
6. Настроить NAT на маршрутизаторе msk-donskaya-gw-1

# Теоретическое введение

**Статическая маршрутизация** - метод маршрутизации, при котором маршруты указываются вручную администратором сети. Основные особенности:

- Используется в небольших сетях с предсказуемой топологией
- Требует ручного обновления при изменениях в сети
- Создает минимальную нагрузку на оборудование
- Обеспечивает полный контроль над трафиком

**VLAN (Virtual LAN)** - логическая изоляция сетевых сегментов на канальном уровне. Позволяет:

- Разделять трафик между группами устройств
- Повышать безопасность сети
- Оптимизировать использование полосы пропускания

# Выполнение работы

##  Настройка линка между площадками

### Настройка коммутатора provider-sw-1
provider-sw-1(config)#vlan 5
provider-sw-1(config-vlan)#name q42
provider-sw-1(config)#interface f0/3
provider-sw-1(config-if)#switchport mode trunk
### Настройка маршрутизатора msk-donskaya-gw-1
cisco
msk-donskaya-gw-1(config)#interface f0/1.5
msk-donskaya-gw-1(config-subif)#encapsulation dot1Q 5
msk-donskaya-gw-1(config-subif)#ip address 10.128.255.1 255.255.255.252

## Настройка площадки 42-го квартала
### Настройка маршрутизатора msk-q42-gw-1
msk-q42-gw-1(config)#interface f0/0.201
msk-q42-gw-1(config-subif)#encapsulation dot1Q 201
msk-q42-gw-1(config-subif)#ip address 10.129.0.1 255.255.255.0
### Настройка коммутатора msk-q42-sw-1
msk-q42-sw-1(config)#interface f0/1
msk-q42-sw-1(config-if)#switchport access vlan 201
## Настройка статической маршрутизации
### На маршрутизаторе msk-donskaya-gw-1
msk-donskaya-gw-1(config)#ip route 10.129.0.0 255.255.0.0 10.128.255.2
msk-donskaya-gw-1(config)#ip route 10.130.0.0 255.255.0.0 10.128.255.6
## Проверка работоспособности
### Проверка маршрутов
msk-donskaya-gw-1#show ip route
### Проверка связности
pc-q42-1> ping 10.130.0.200

## Результаты
Успешно настроены все сетевые устройства

Реализована статическая маршрутизация между площадками

Проверена работоспособность сети:

Успешный ping между узлами

Корректная таблица маршрутизации

## Итоговый вид топологии сети

![Итог](image/1.png){#fig:004 width=100%}
![Итог](image/2.png){#fig:004 width=100%}

## Выводы
В ходе лабораторной работы была успешно настроена статическая маршрутизация между основной площадкой в Москве и филиалом в Сочи. Все задачи выполнены в полном объеме, работоспособность сети подтверждена тестами.

## Ответы на контрольные вопросы

Пример настройки статической маршрутизации:
ip route 192.168.1.0 255.255.255.0 10.0.0.1

Обращение между VLAN:
Трафик передается на маршрутизатор
Маршрутизатор перенаправляет его в другой VLAN
Требуется наличие маршрута между VLAN

Проверка работоспособности маршрута:
ping <целевой_адрес>
traceroute <целевой_адрес>
show ip route

Просмотр таблицы маршрутизации:
show ip route