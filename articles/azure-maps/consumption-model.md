---
title: Gépjármű-felhasználási modellek az útválasztáshoz | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan használhatók a Microsoft Azure Maps-ben az útválasztáshoz használt gépjárművek fogyasztási modelljei.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5a8a0778ce279846b0d7a66b1729b6898e80a4b5
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911712"
---
# <a name="consumption-model"></a>Használatalapú modell

Az online útválasztás számos paramétert biztosít a jármű-specifikus fogyasztási modell részletes leírásához.
A **vehicleEngineType**értékétől függően két fő felhasználási modell támogatott: az _égés_ és az _elektromos_. A különböző modellekhez tartozó paraméterek megadása egy adott kérelemben hiba.
A használati modell nem használható a **travelMode** és _a_ _gyalogos_értékkel.

## <a name="parameter-constraints-for-consumption-model"></a>A használati modell paramétereinek megkötései

Mindkét felhasználási modellben explicit módon meg kell határozni néhány paramétert, és meg kell adni másokat is. Ezek a függőségek a következők:

* Minden paraméterhez a felhasználónak meg kell adni a **constantSpeedConsumption** . Hiba, ha a **vehicleWeight**kivételével más felhasználási modell paramétert is meg kell adni, ha a **constantSpeedConsumption** nincs megadva.
* a **accelerationEfficiency** és a **decelerationEfficiency** mindig pár (azaz mindkettő vagy none) értékként kell megadni.
* Ha a **accelerationEfficiency** és a **decelerationEfficiency** meg van adva, az értékük szorzata nem lehet nagyobb, mint 1 (az örökös mozgás megakadályozása érdekében).
* a **uphillEfficiency** és a **downhillEfficiency** mindig pár (azaz mindkettő vagy none) értékként kell megadni.
* Ha a **uphillEfficiency** és a **downhillEfficiency** meg van adva, az értékük szorzata nem lehet nagyobb, mint 1 (az örökös mozgás megakadályozása érdekében).
* Ha a felhasználó a \*__hatékonysági__ paramétereket adja meg, akkor a **vehicleWeight** is meg kell adni. A **vehicleEngineType** _égés_esetén a **fuelEnergyDensityInMJoulesPerLiter** is meg kell adni.
* a **maxChargeInkWh** és a **currentChargeInkWh** mindig pár (azaz mindkettő vagy none) értékként kell megadni.

> [!NOTE]
> Ha csak a **constantSpeedConsumption** van megadva, a használati számítások során nem számítunk fel más felhasználási szempontokat, például a lejtőket és a járművek gyorsítását.

## <a name="combustion-consumption-model"></a>Égési fogyasztás modellje

Az égési használat modelljét akkor kell használni, ha a **vehicleEngineType** _égési_értékre van állítva.
A modellhez tartozó paraméterek listája alább látható. A Részletes leírásért tekintse meg a parameters (paraméterek) szakaszt.

* constantSpeedConsumptionInLitersPerHundredkm
* VehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Elektromos fogyasztási modell

Az elektromos fogyasztási modellt akkor kell használni, ha a **vehicleEngineType** az _Electric_értékre van állítva.
A modellhez tartozó paraméterek listája alább látható. A Részletes leírásért tekintse meg a parameters (paraméterek) szakaszt.

* constantSpeedConsumptionInkWhPerHundredkm
* VehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>A felhasználási paraméterek ésszerű értékei

A használati paraméterek egy adott készlete elutasítható, még ha az is előfordulhat, hogy a fent megadott összes explicit követelmény teljesül. Ez akkor történik meg, ha egy adott paraméter értéke vagy több paraméter értékének kombinációja a használati értékek nem ésszerű mértékű nagyságrendjét eredményezi. Ha ez történik, valószínűleg bemeneti hibát jelez, mivel a használati paraméterek minden ésszerű értékének megfelelő gondossággal kell eljárni. Ha a rendszer elutasítja a használati paraméterek egy adott készletét, a mellékelt hibaüzenet az OK (ok) szöveges magyarázatát fogja tartalmazni.
A paraméterek részletes leírása példákat mutat be mindkét modell ésszerű értékeire.
