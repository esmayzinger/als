---
## Front matter
title: "Лабораторная работа №9"
subtitle: "Использование протокола STP. Агрегирование каналов"
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

Изучение возможностей протокола STP и его модификаций для обеспечения отказоустойчивости сети, агрегирования интерфейсов и балансировки нагрузки.

# Задание

1. Сформировать резервное соединение между коммутаторами `msk-donskaya-sw-1` и `msk-donskaya-sw-3`.
2. Настроить балансировку нагрузки между резервными соединениями.
3. Настроить режим Portfast на интерфейсах, подключенных к серверам.
4. Изучить отказоустойчивость резервного соединения.
5. Сформировать и настроить агрегированное соединение между коммутаторами `msk-donskaya-sw-1` и `msk-donskaya-sw-4` на интерфейсах `Fa0/20 – Fa0/23`.

# Выполнение лабораторной работы

## 1. Настройка резервного соединения

1. Заменили соединение между `msk-donskaya-sw-1` (Gig0/2) и `msk-donskaya-sw-4` (Gig0/1) на соединение между `msk-donskaya-sw-1` (Gig0/2) и `msk-donskaya-sw-3` (Gig0/2).
2. Настроили порт Gig0/2 на `msk-donskaya-sw-3` как транковый:
   ```
   msk-donskaya-sw-3(config)#int g0/2
   msk-donskaya-sw-3(config-if)#switchport mode trunk
Соединение между msk-donskaya-sw-1 и msk-donskaya-sw-4 перенесли на интерфейсы Fa0/23, активировав их в транковом режиме.

## Проверка движения пакетов
С хоста dk-donskaya-1 выполнили ping серверов mail и web. В режиме симуляции убедились, что пакеты ICMP проходят через коммутатор msk-donskaya-sw-2.

## Анализ состояния STP для VLAN 3
На msk-donskaya-sw-2 выполнили команду:

msk-donskaya-sw-2#show spanning-tree vlan 3
Результат показал, что устройство является корневым для VLAN 3.

## Настройка корневого коммутатора
Настроили msk-donskaya-sw-1 как корневой коммутатор для VLAN 3:


msk-donskaya-sw-1(config)#spanning-tree vlan 3 root primary

## Проверка маршрутов
Убедились, что пакеты ICMP от dk-donskaya-1 до mail идут через msk-donskaya-sw-1 и msk-donskaya-sw-3, а до web — через msk-donskaya-sw-1 и msk-donskaya-sw-2.

## Настройка режима Portfast
Режим Portfast активирован на интерфейсах, подключенных к серверам:
msk-donskaya-sw-2(config)#interface f0/1
msk-donskaya-sw-2(config-if)#spanning-tree portfast
msk-donskaya-sw-2(config)#interface f0/2
msk-donskaya-sw-2(config-if)#spanning-tree portfast
msk-donskaya-sw-3(config)#interface f0/1
msk-donskaya-sw-3(config-if)#spanning-tree portfast
msk-donskaya-sw-3(config)#interface f0/2
msk-donskaya-sw-3(config-if)#spanning-tree portfast


## Проверка отказоустойчивости STP
Выполнили команду на хосте dk-donskaya-1:


ping -n 1000 mail.donskaya.rudn.ru
Разрыв соединения имитировали командой shutdown на соответствующем интерфейсе. Время восстановления составило менее 5 секунд.

## Переход на Rapid PVST+
Перевели все коммутаторы в режим Rapid PVST+:


msk-donskaya-sw-1(config)#spanning-tree mode rapid-pvst
msk-donskaya-sw-2(config)#spanning-tree mode rapid-pvst
msk-donskaya-sw-3(config)#spanning-tree mode rapid-pvst
msk-donskaya-sw-4(config)#spanning-tree mode rapid-pvst

## Настройка агрегированного соединения
Настроили EtherChannel между msk-donskaya-sw-1 и msk-donskaya-sw-4:


msk-donskaya-sw-1(config)#interface range f0/20 - 23
msk-donskaya-sw-1(config-if-range)#channel-group 1 mode on
msk-donskaya-sw-1(config-if-range)#exit
msk-donskaya-sw-1(config)#interface port-channel 1
msk-donskaya-sw-1(config-if)#switchport mode trunk

msk-donskaya-sw-4(config)#interface range f0/20 - 23
msk-donskaya-sw-4(config-if-range)#channel-group 1 mode on
msk-donskaya-sw-4(config-if-range)#exit
msk-donskaya-sw-4(config)#interface port-channel 1
msk-donskaya-sw-4(config-if)#switchport mode trunk

## Итоговый вид топологии сети

![Итог](image/1.png){#fig:004 width=100%}

## Выводы
Настроено резервное соединение между коммутаторами с использованием STP.

Реализована балансировка нагрузки между резервными соединениями.

Активирован режим Portfast для ускорения работы серверных интерфейсов.

Проверена отказоустойчивость сети при использовании STP и Rapid PVST+.

Настроено агрегированное соединение EtherChannel для увеличения пропускной способности.

## Ответы на контрольные вопросы
Команда show spanning-tree vlan 3
Показывает состояние портов, роль коммутатора (корневой или нет), приоритет, стоимость пути и другие параметры STP для указанного VLAN.

Режим работы STP
Проверяется командой show spanning-tree summary. Пример вывода:


Switch is in rapid-pvst mode
Режим Portfast
Настраивается на интерфейсах, подключенных к конечным устройствам (серверам, ПК), чтобы избежать задержек при переходе порта в состояние Forwarding.

Агрегированный интерфейс
Объединяет несколько физических каналов в один логический для увеличения пропускной способности и отказоустойчивости.

Отличия LACP, PAgP и статического агрегирования

LACP (IEEE 802.3ad) — стандартный протокол.

PAgP — проприетарный протокол Cisco.

Статическое агрегирование не требует протоколов, но менее гибкое.

Проверка состояния EtherChannel
Команда show etherchannel summary выводит список агрегированных каналов и их статус.