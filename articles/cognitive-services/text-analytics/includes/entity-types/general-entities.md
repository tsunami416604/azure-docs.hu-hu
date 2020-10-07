---
title: Általános névvel ellátott entitások
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 630f04bf2cc9e7de6331f9d25754a20fe2327d76
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779543"
---
A rendszer a következő entitás-kategóriákat adja vissza a végpontra irányuló kérelmek küldésekor `/entities/recognition/general` .

| Kategória   | Alkategória | Leírás                          | Modell verziójának indítása                                                    | Jegyzetek |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| Személy     | N/A         | Személyek nevei.  | `2019-10-01`  | Visszaadott |
| PersonType | N/A         | Egy személy által birtokolt feladattípusok vagy szerepkörök. | `2020-02-01` | |
|Hely    | N/A         | Természetes és emberi által készített tereptárgyak, struktúrák, földrajzi funkciók és geopolitikai entitások     |  `2019-10-01` | Visszaadott |
|Hely     | Geopolitikai entitás (GPE)        | Városok, országok/régiók, Államok.      | `2020-02-01` | |
|Hely     | Szerkezeti                       | Az ember által okozott struktúrák. | `2020-04-01` | |
|Hely     | Földrajzi       | Földrajzi és természeti adottságok, mint például a folyók, az óceánok és a sivatagok. |  `2020-04-01` | |
|Szervezet  | N/A | Vállalatok, politikai csoportok, zenei zenekarok, sport klubok, kormányzati szervek és nyilvános szervezetek.  | `2019-10-01` | A nemzetiségek és a vallások nem szerepelnek ebben az entitás-típusban. Visszaadott |
|Szervezet | Orvosi | Orvosi vállalatok és csoportok. | `2020-04-01` |  |
|Szervezet | Értéktőzsde | Tőzsdei csoportok. | `2020-04-01` | |
| Szervezet | Sport | Sporttal kapcsolatos szervezetek. | `2020-04-01` |  |
| Esemény  | N/A | A korábbi, a közösségi és a természetben előforduló események. | `2020-02-01` |  |
| Esemény  | Kulturális | Kulturális események és ünnepek. | `2020-04-01` | |
| Esemény  | Természetes | Természetesen előforduló események. | `2020-04-01` |  |
| Esemény  | Sport | Sportesemények.  | `2020-04-01` | |
| Product | N/A | Különböző kategóriák fizikai objektumai. | `2020-02-01` | |
| Product | Számítástechnikai termékek | Számítástechnikai termékek. |  `2020-02-01 ` | |
| Ügyességi | N/A | Képesség, szakértelem vagy szakértelem. | `2020-02-01` |  |
| Cím | N/A | Teljes levelezési címek.  | `2020-04-01` |  |
| PhoneNumber | N/A | Telefonszámok (csak az USA-beli és az EU-telefonszámok). | `2019-10-01` | Visszaadott |
| E-mail | N/A | E-mail-címek. | `2019-10-01` | Visszaadott |
| URL-cím | N/A | Webhelyek URL-címei. | `2019-10-01` | Visszaadott  |
| IP | N/A | Hálózati IP-címek. | `2019-10-01` | Visszaadott |
| DateTime | N/A | Dátum és napszakok. | `2019-10-01` | Visszaadott | 
| Dátum/idő | Date | Naptári dátumok. | `2019-10-01` | Visszaadott |
| Dátum/idő | Idő | Napszakok | `2019-10-01` | Visszaadott |
| Dátum/idő | Dátumtartomány | Dátumtartomány. | `2019-10-01` | Visszaadott |
| Dátum/idő | Időtartomány | Időtartományok. | `2019-10-01` | Visszaadott |
| Dátum/idő | Időtartam | Időtartamok. | `2019-10-01` | Visszaadott |
| Dátum/idő | Beállítás | Beállítás, ismétlődő időpontok. |  `2019-10-01` | Visszaadott |
| Mennyiség | N/A | Számok és numerikus mennyiségek. | `2019-10-01` | Visszaadott  |
| Mennyiség | Szám | Számok. | `2019-10-01` | Visszaadott |
| Mennyiség | Százalék | Százalékos.| `2019-10-01` | Visszaadott |
| Mennyiség | Sorszám | Sorszámok száma | `2019-10-01` | Visszaadott |
| Mennyiség | Életkor | Alábbi korhatárt szabja. | `2019-10-01` |  Visszaadott |
| Mennyiség | Pénznem | Valuták. | `2019-10-01` | Visszaadott |
| Mennyiség | Méret | Méretek és mérések. | `2019-10-01` | Visszaadott |
| Mennyiség | Hőmérséklet | Hőmérsékletek. | `2019-10-01` | Visszaadott |
