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
ms.openlocfilehash: c1572eddf78ca2d5f8f4e3ee9f1fe47b0d43f5aa
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190249"
---
# <a name="consumption-model"></a>Használatalapú modell

Az útválasztási szolgáltatás a járműre jellemző használati modell részletes leírására szolgáló paramétereket tartalmaz.
A **vehicleEngineType**értékétől függően két fő felhasználási modell támogatott: az _égés_ és az _elektromos_. Nem megfelelő a különböző modellekhez tartozó paraméterek megadása ugyanabban a kérésben. Emellett a használati modell paraméterei nem használhatók a következő **travelMode** -értékekkel: _kerékpár_ és _gyalogos_.

## <a name="parameter-constraints-for-consumption-model"></a>A használati modell paramétereinek megkötései

Mindkét felhasználási modellben vannak függőségek a paraméterek megadásakor. Ami azt jelenti, hogy az egyes paraméterek explicit módon történő megadása más paraméterek megadását is szükségessé teheti. A következő függőségek ismerete:

* Minden paraméterhez a felhasználónak meg kell adni a **constantSpeedConsumption** . Hiba a más felhasználási modell paraméterének megadásakor, ha a **constantSpeedConsumption** nincs megadva. A **vehicleWeight** paraméter kivételt jelent ennek a követelménynek.
* a **accelerationEfficiency** és a **decelerationEfficiency** mindig párosként kell megadni (azaz mindkettőt vagy egyiket sem).
* Ha a **accelerationEfficiency** és a **decelerationEfficiency** meg van adva, az értékük szorzata nem lehet nagyobb, mint 1 (az örökös mozgás megakadályozása érdekében).
* a **uphillEfficiency** és a **downhillEfficiency** mindig párosként kell megadni (azaz mindkettőt vagy egyet).
* Ha a **uphillEfficiency** és a **downhillEfficiency** meg van adva, az értékük szorzata nem lehet nagyobb, mint 1 (az örökös mozgás megakadályozása érdekében).
* Ha a felhasználó a \*__hatékonysági__ paramétereket adja meg, akkor a **vehicleWeight** is meg kell adni. A **vehicleEngineType** _égés_esetén a **fuelEnergyDensityInMJoulesPerLiter** is meg kell adni.
* a **maxChargeInkWh** és a **currentChargeInkWh** mindig párosként kell megadni (azaz mindkettőt vagy egyiket sem).

> [!NOTE]
> Ha csak a **constantSpeedConsumption** van megadva, a használati számítások során nem számítunk fel más felhasználási szempontokat, például a lejtőket és a járművek gyorsítását.

## <a name="combustion-consumption-model"></a>Égési fogyasztás modellje

Az égési használat modelljét akkor kell használni, ha a **vehicleEngineType** _égési_értékre van állítva.
A modellhez tartozó paraméterek listája alább látható. A Részletes leírásért tekintse meg a parameters (paraméterek) szakaszt.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
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
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>A felhasználási paraméterek ésszerű értékei

A használati paraméterek egy adott készlete elutasítható, még akkor is, ha a készlet megfelel az összes kifejezett követelménynek. Akkor következik be, amikor egy adott paraméter értéke vagy a különböző paraméterek értékeinek kombinációja a használati értékek nem ésszerű mértékű nagyságrendjét eredményezi. Ha ez történik, valószínűleg bemeneti hibát jelez, mivel a használati paraméterek minden ésszerű értékének megfelelő gondossággal kell eljárni. Ha a rendszer elutasítja a használati paraméterek egy adott készletét, a mellékelt hibaüzenet az OK (ok) szöveges magyarázatát fogja tartalmazni.
A paraméterek részletes leírása példákat mutat be mindkét modell ésszerű értékeire.
