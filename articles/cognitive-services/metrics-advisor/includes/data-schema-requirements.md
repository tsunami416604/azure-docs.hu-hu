---
title: Az Adatséma követelményei
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: a64bb5b28a06d9a013d59e022047f5e2841126ab
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940137"
---
A metrikák figyelő a Time Series anomália észlelésének, diagnosztizálásának és elemzésének egyik szolgáltatása. AI-alapú szolgáltatásként az adatait a felhasznált modell betanítására használják. A szolgáltatás az összesített adatokat tartalmazó táblákat a következő oszlopokkal fogadja el:

* **Mérték** (kötelező): egy vagy több numerikus értékeket tartalmazó oszlop.
* **Időbélyeg** (nem kötelező): nulla vagy egy oszlop, amelynek típusa `DateTime` vagy `String` . Ha ez az oszlop nincs beállítva, az időbélyegző az egyes betöltési időszakok kezdő időpontjaként van beállítva. Az időbélyeg formázása: `yyyy-MM-ddTHH:mm:ssZ` . 
  * Az **időbélyegnek meg kell egyeznie a metrika részletességével. A napi metrika például biztosítja az óra, a perc és a másodperc értékét a következőként megjelölt `00:00:00` időbélyegen: **.
* **Dimenzió** (nem kötelező): az oszlopok bármilyen adattípus lehet. Legyen körültekintő, ha nagy mennyiségű oszlopot és értéket használ, hogy megakadályozza a dimenziók feldolgozásának túlzott számát.

> [!Note]
> Minden metrika esetében csak egy időbélyeg lehet, amely egy dimenzió kombinációnak felel meg. Összesítheti az adatokat a bevezetést megelőzően, vagy a lekérdezés használatával megadhatja a betöltött adatokat.