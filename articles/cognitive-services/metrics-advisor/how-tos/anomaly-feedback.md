---
title: Anomália-visszajelzés küldése a metrikai tanácsadó szolgáltatásnak
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan küldhet visszajelzést a metrikai tanácsadó példány által észlelt rendellenességekről, és hogyan hangolhatja be az eredményeket.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 50d422edf1a4b45132d0b86eac9d4947cef5e5bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935429"
---
# <a name="adjust-anomaly-detection-using-feedback"></a>Anomáliadetektálás beállítása visszajelzés használatával

Ha nem teljesíti a metrikák figyelő által biztosított rendellenességek észlelési eredményeinek némelyikét, manuálisan is hozzáadhat visszajelzést, amelyek hatással lesznek az adatokra alkalmazott modellre. 

Az oldal jobb felső sarkában található gombokkal aktiválhatja a visszajelzési jegyzet módját.

:::image type="content" source="../media/feedback/annotation-mode.png" alt-text="Visszajelzési jegyzet mód.":::

A visszajelzési jegyzet üzemmód aktiválása után egy vagy több folytonos pontra is küldhet visszajelzést.

## <a name="give-feedback-for-one-point"></a>Visszajelzés küldése egy pontról 

Ha a visszajelzési jegyzet mód aktiválva van, kattintson egy pontra a **visszajelzés hozzáadása** panel megnyitásához. Beállíthatja, hogy milyen típusú visszajelzést szeretne alkalmazni. Ez a visszajelzés a jövőbeli pontok észleléséhez lesz beépítve.  

* Válassza a **anomália** lehetőséget, ha úgy gondolja, hogy a pontot a metrikák figyelője helytelenül jelölte meg. Megadhatja, hogy egy adott pontnak vagy ne legyen anomália. 
* Válassza a **ChangePoint** lehetőséget, ha úgy gondolja, hogy a pont jelzi a trend változásának kezdetét.
* Válassza ki az **időszakot** a szezonális megjelenítéshez. A metrikai figyelő automatikusan képes azonosítani a szezonális intervallumokat, vagy megadhatja ezt manuálisan is. 

Vegye **fontolóra a Megjegyzés szövegmezőben** való kilépését egy időben, és kattintson a **Save (Mentés** ) gombra a visszajelzés mentéséhez.

:::image type="content" source="../media/feedback/feedback-menu.png" alt-text="Visszajelzési jegyzet mód.":::

## <a name="give-feedback-for-multiple-continuous-points"></a>Visszajelzés küldése több folyamatos pontról

Egyszerre több folyamatos pontra is küldhet visszajelzést. ehhez kattintson a lefelé gombra, és húzza az egérmutatót a jegyzetként használni kívánt pontokra. Ugyanazt a visszajelzési menüt fogja látni, mint a fentieket. A **Save (Mentés**) gombra kattintás után ugyanez a visszajelzés lesz alkalmazva az összes kiválasztott pontra.

:::image type="content" source="../media/feedback/continuous-points.png" alt-text="Visszajelzési jegyzet mód.":::

## <a name="how-to-view-my-feedback"></a>Visszajelzés megtekintése

Ha meg szeretné tekinteni, hogy módosult-e a pont anomália-észlelése, vigye a kurzort a pont fölé. Az elemleírás a **visszajelzések szerint jelenik meg: igaz,** ha az észlelés megváltozott. Ha **Hamis értéket**jelenít meg, akkor a visszajelzések kiszámítása a ponton megtörtént, de a rendellenességek észlelésének eredménye nem módosult.

:::image type="content" source="../media/feedback/affected-point.png" alt-text="Visszajelzési jegyzet mód.":::

## <a name="when-should-i-annotate-an-anomaly-as-normal"></a>Mikor kell a "NORMAL" értékre jegyzett anomáliát

Számos oka lehet annak, ha egy anomália hamis riasztás. Ha az alábbi forgatókönyvek valamelyike alkalmazható, érdemes lehet a következő metrikai tanácsadói funkciókat használni:


|Forgatókönyv  |Ajánlás |
|---------|---------|
|Az anomáliát az ismert adatforrás-változás okozza, például egy rendszerváltozás.     | Ne jegyezze fel ezt a anomáliát, ha ez a forgatókönyv nem várható rendszeresen újra.        |
|A rendellenességet a nyaralás okozta.     | A anomáliák észlelésének megjelölése megadott időpontokban az [előre beállított események](configure-metrics.md#preset-events) használatával.       |
|Az észlelt rendellenességek (például hétvégeken) rendszeres mintázattal rendelkeznek, és nem lehetnek anomáliák.      |Használja a visszajelzés funkciót vagy az előre beállított eseményeket.        |

## <a name="next-steps"></a>Következő lépések
- [Incidens diagnosztizálása](diagnose-incident.md).
- [Metrikák konfigurálása és a konfiguráció észlelésének finomhangolása](configure-metrics.md)
- [Riasztások konfigurálása és értesítések fogadása beavatkozási pont segítségével](../how-tos/alerts.md)