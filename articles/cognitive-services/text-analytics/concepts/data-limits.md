---
title: A Text Analytics API vonatkozó adatkorlátozások
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services Text Analytics API vonatkozó adatkorlátozások.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/19/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 2adca03a820d02731bca252dee99c76debc85e2e
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028131"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Az Text Analytics API vonatkozó adatforgalmi és díjszabási korlátok
<a name="data-limits"></a>

Ebből a cikkből megtudhatja, hogy mekkora a mérete, és milyen arányban küldhet adatText Analytics API. 

## <a name="data-limits"></a>Adatkorlátok

> [!NOTE]
> * Ha nagyobb méretű dokumentumokat kell elemezni, mint amennyit a korlát engedélyez, akkor a szöveget az API-nak való elküldés előtt megszakíthatja kisebb adattömbökbe. 
> * A dokumentum egyetlen szöveges karakterből álló karakterlánc.  

| Korlát | Érték |
|------------------------|---------------|
| Az egyes dokumentumok maximális mérete | 5 120 karakter a [StringInfo. LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)alapján mérve. A Text Analytics az állapotra is érvényes. |
| Egyetlen dokumentum maximális mérete ( `/analyze` végpont)  | a 125K a [StringInfo. LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)alapján mért karaktereket. Nem vonatkozik az állapotra Text Analytics. |
| Teljes kérés maximális mérete | 1 MB. A Text Analytics az állapotra is érvényes. |


Ha egy dokumentum túllépi a karakteres korlátot, az API a használt végponttól függően eltérően fog működni:

* `/analyze` végpont
  * Az API elutasítja a teljes kérelmet, és hibát ad vissza, `400 bad request` Ha az abban található bármelyik dokumentum meghaladja a maximális méretet.
* Az összes többi végpont:  
  * Az API nem dolgoz fel olyan dokumentumot, amely meghaladja a maximális méretet, és érvénytelen dokumentum-hibát ad vissza. Ha egy API-kérelem több dokumentummal rendelkezik, az API a karakteres korláton belül folytatja a feldolgozást.

Az egyetlen kérelemben elküldhető dokumentumok maximális száma a használt API-verziótól és szolgáltatástól függ, amelyet az alábbi táblázatban talál.

#### <a name="version-3"></a>[3\. verzió](#tab/version-3)

Az aktuális V3 API-ra a következő korlátozások vonatkoznak. Az alábbi korlátok túllépése a HTTP 400 hibakódot eredményezi.


| Funkció | Dokumentumok maximális száma kérelem szerint | 
|----------|-----------|
| Nyelvfelismerés | 1000 |
| Hangulatelemzés | 10 |
| Vélemény bányászata | 10 |
| Kulcskifejezések kinyerése | 10 |
| Megnevezett entitások felismerése | 5 |
| Entitáskapcsolás | 5 |
| Egészségügyi Text Analytics  | 10 a web-alapú API-hoz, 1000 a tárolóhoz. |
| Végpont elemzése | 25 az összes művelethez. |

#### <a name="version-2"></a>[2-es verzió](#tab/version-2)

| Funkció | Dokumentumok maximális száma kérelem szerint | 
|----------|-----------|
| Nyelvfelismerés | 1000 |
| Hangulatelemzés | 1000 |
| Kulcskifejezések kinyerése | 1000 |
| Megnevezett entitások felismerése | 1000 |
| Entitáskapcsolás | 1000 |

---

## <a name="rate-limits"></a>Sebességkorlátok

A díjszabási korlátja az Ön [díjszabási szintjétől](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)függ. Ezek a korlátok ugyanazok, mint az API mindkét verziójában. Ezek a díjszabási korlátok nem vonatkoznak az állapot-tárolóra vonatkozó Text Analyticsra, amely nem rendelkezik beállított díjszabási korláttal.

| Szint          | Kérések száma másodpercenként | Percenkénti kérelmek |
|---------------|---------------------|---------------------|
| S/több szolgáltatás | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

A kérelmek díjait külön kell mérni az egyes Text Analytics szolgáltatások esetében. Az árképzési szintjére vonatkozó kérések maximális számát az egyes szolgáltatásokra vonatkozóan küldheti el egyszerre. Ha például a szinten van, és az `S` 1000-es kérelmeket egyszerre küldi el, akkor nem fog tudni másik kérelmet küldeni a 59 másodpercre.


## <a name="see-also"></a>Lásd még

* [Mi a Text Analytics API](../overview.md)
* [Díjszabás részletei](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
