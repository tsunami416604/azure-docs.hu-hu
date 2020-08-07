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
ms.date: 04/27/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 0dc79132ae75be511b53bf272e578ff0ce7a7775
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986814"
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
| Az egyes dokumentumok maximális mérete | 5 120 karakter a [StringInfo. LengthInTextElements](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements)alapján mérve. |
| Teljes kérés maximális mérete | 1 MB |

Az egyetlen kérelemben elküldhető dokumentumok maximális száma a használt API-verziótól és szolgáltatástól függ.

#### <a name="version-3"></a>[3\. verzió](#tab/version-3)

A következő korlátok módosultak az API v3-as verziójában. Az alábbi korlátok túllépése a HTTP 400 hibakódot eredményezi.


| Funkció | Dokumentumok maximális száma kérelem szerint | 
|----------|-----------|
| Nyelvfelismerés | 1000 |
| Hangulatelemzés | 10 |
| Kulcskifejezések kinyerése | 10 |
| Megnevezett entitások felismerése | 5 |
| Entitáskapcsolás | 5 |

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

A díjszabási korlátja az Ön [díjszabási szintjétől](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)függ. Ezek a korlátok ugyanazok, mint az API mindkét verziójában.

| Szint          | Kérések száma másodpercenként | Percenkénti kérelmek |
|---------------|---------------------|---------------------|
| S/több szolgáltatás | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

A kérelmeket külön kell mérni az egyes Text Analytics szolgáltatásokhoz. Elküldheti például az árképzési szintjére vonatkozó kérések maximális számát az egyes szolgáltatásokhoz, egyszerre.  


## <a name="see-also"></a>További információ

* [Mi a Text Analytics API](../overview.md)
* [Díjszabás részletei](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
