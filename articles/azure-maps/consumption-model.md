---
title: A Maps-Azure felhasználási modell |} Microsoft Docs
description: További tudnivalók a Maps-Azure felhasználási modell
services: azure-maps
keywords: ''
author: subbarayudukamma
ms.author: skamma
ms.date: 5/8/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: 146ea084c02bb3de0c74da79ca85021589207de8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="consumption-model"></a>Felhasználás modell

Online útválasztás paraméterek biztosít részletes leírása a vehicle-specifikus fogyasztás modell.
Attól függően, hogy értékének **vehicleEngineType**, két fő felhasználási modellek támogatottak: _égésű_ és _elektromos_. Adja meg a paraméterek a kérésben különböző modell tartozó hiba.
Felhasználás modell nem használható **travelMode** értékek _kerékpárgyártó_ és _gyalogosok_.

## <a name="parameter-constraints-for-consumption-model"></a>Felhasználás modell paraméteréhez kielégíthetetlen korlátozások tartoznak

Mindkét fogyasztás modellek explicit megadása az egyes paraméterek meg kell más is. Ezeket a függőségeket a következők:

* Minden paraméter szükséges **constantSpeedConsumption** a felhasználó által meghatározott. Bármely más fogyasztás modell paramétert meg a hiba **vehicleWeight**, ha **constantSpeedConsumption*** nincs megadva.
* **accelerationEfficiency** és **decelerationEfficiency** (azaz mind "vagy" none) párban mindig meg kell adni.
* Ha **accelerationEfficiency** és **decelerationEfficiency** van adva, az értékek szorzatát nem lehet nagyobb, mint 1 (az perpetual mozgásérzékelési elkerülése érdekében).
* **uphillEfficiency** és **downhillEfficiency** (azaz mind "vagy" none) párban mindig meg kell adni.
* Ha **uphillEfficiency** és **downhillEfficiency** van adva, az értékek szorzatát nem lehet nagyobb, mint 1 (az perpetual mozgásérzékelési elkerülése érdekében).
* Ha \* **hatékonyságát** paraméterek vannak megadva a felhasználó, majd **vehicleWeight** is meg kell adni. Ha **vehicleEngineType** van _égésű_, **fuelEnergyDensityInMJoulesPerLiter** is meg kell adni.
* **maxChargeInkWh** és **currentChargeInkWh** (azaz mind "vagy" none) párban mindig meg kell adni.

> [!NOTE]
> Ha csak **constantSpeedConsumption** van megadva, nincs más fogyasztás szempontokat lejtők és gyorsítása hasonlóan a fogyasztás számítások figyelembe kell venni.

## <a name="combustion-consumption-model"></a>Égésű fogyasztás modell

A égésű felhasználás a modell amikor **vehicleEngineType** értéke _égésű_.
Ez a modell tartozó paraméterek listáját a rendszer alatt. Tekintse meg a Paraméterek szakaszban részletes leírását.

* constantSpeedConsumptionInLitersPerHundredkm
* VehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Elektromos fogyasztás modell

Az elektromos felhasználás a modell amikor **vehicleEngineType** értéke _elektromos_.
Ez a modell tartozó paraméterek listáját a rendszer alatt. Tekintse meg a Paraméterek szakaszban részletes leírását.

* constantSpeedConsumptionInkWhPerHundredkm
* VehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Felhasználás paraméterek nem ésszerű értékét

Egy meghatározott fogyasztás paraméterek el lehet utasítani, annak ellenére, akkor előfordulhat, hogy teljesíti explicit fent megadott. Akkor történik, ha egy adott paraméterről, vagy akár kombinálhatja is több paraméterek értékének minősül, előfordulhat, hogy ésszerűtlenül értékek felhasználása értékek. Ebben az esetben, ha azt valószínűleg jelzi bemeneti hiba, megfelelő ügyelni arra, hogy megfeleljen az összes ésszerű fogyasztás paraméterek értékét. Abban az esetben egy meghatározott fogyasztás paraméterek elutasítva, a hozzá tartozó hibaüzenet szöveges magyarázata a OK(ok) fogja tartalmazni.
A részletes leírását, a paraméterek rendelkezik néhány lehetséges ésszerű érték mind a két modellben.
