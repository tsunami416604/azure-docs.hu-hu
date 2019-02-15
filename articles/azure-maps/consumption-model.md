---
title: Az Azure Maps-felhasználási modellből |} A Microsoft Docs
description: További tudnivalók az Azure Maps a használatalapú modell
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5f75f656312c11a4668ca9ef9fe7b2a61a7d13e8
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301139"
---
# <a name="consumption-model"></a>Használatalapú modell

Online útválasztás biztosít a különböző paraméterek jármű-specifikus használati modellre részletes leírását.
Attól függően, értékét **vehicleEngineType**, két egyszerű felhasználás-modellek támogatottak: _Égésű_ és _Electric_. Ha ugyanazon kérésben különböző modellekhez tartozó paramétereket ad meg, az hibát eredményez.
A fogyasztási modell nem használható, ha a **travelMode** értéke _bicycle_ vagy _pedestrian_.

## <a name="parameter-constraints-for-consumption-model"></a>A használatalapú modell paraméter megkötései

Mindkét fogyasztási modell explicit módon adja meg az egyes paraméterek használatához szükséges megadása mások is. Ezeket a függőségeket a következők:

* Összes paraméter szükséges **constantSpeedConsumption** a felhasználó által meghatározott. Bármely más felhasználási modell paraméter megadásához, a hiba **vehicleWeight**, ha **constantSpeedConsumption** nincs megadva.
* **accelerationEfficiency** és **decelerationEfficiency** (vagyis mindkét vagy nincs) párban mindig meg kell adni.
* Ha **accelerationEfficiency** és **decelerationEfficiency** meg van adva, az értékek szorzatát nem lehet nagyobb, mint 1 (perpetual mozgásban lévő adatoknak egyaránt megakadályozására).
* **uphillEfficiency** és **downhillEfficiency** (vagyis mindkét vagy nincs) párban mindig meg kell adni.
* Ha **uphillEfficiency** és **downhillEfficiency** meg van adva, az értékek szorzatát nem lehet nagyobb, mint 1 (perpetual mozgásban lévő adatoknak egyaránt megakadályozására).
* Ha a \* __hatékonyság__ paraméterek vannak a felhasználó által megadott, majd **vehicleWeight** is meg kell adni. a a **vehicleEngineType értéke** _combustion_, akkor a **fuelEnergyDensityInMJoulesPerLiter** értékét is meg kell adnia.
* **maxChargeInkWh** és **currentChargeInkWh** (vagyis mindkét vagy nincs) párban mindig meg kell adni.

> [!NOTE]
> Ha csak **constantSpeedConsumption** van megadva, nincs más felhasználási szempontokat például lejtők és gyorsítása fogyasztás számítások esetében figyelembe kell venni.

## <a name="combustion-consumption-model"></a>Égésű fogyasztási modell

A Belső égésű fogyasztási modellt akkor használja a rendszer, ha a **vehicleEngineType** értéke _combustion_.
Ez a modell tartoznak paraméterek listája az alábbiakban olvashatják. Tekintse meg a Paraméterek szakaszban részletes leírást.

* constantSpeedConsumptionInLitersPerHundredkm
* VehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Electric fogyasztási modell

Az Elektromos fogyasztási modellt akkor használja a rendszer, ha a **vehicleEngineType** értéke _electric_.
Ez a modell tartoznak paraméterek listája az alábbiakban olvashatják. Tekintse meg a Paraméterek szakaszban részletes leírást.

* constantSpeedConsumptionInkWhPerHundredkm
* VehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Késleltetésnél paraméterek értékeit, használatalapú

Egy adott készletét fogyasztás paraméterek el lehet utasítani, annak ellenére, előfordulhat, hogy teljesíti a fent megadott összes explicit követelményeket. Ez történik, ha egy bizonyos paraméterét, vagy a paraméterek számos kombinációjából értékének kell tekinteni feldühítheti massively fogyasztás értékek vezetnek. Ha ez történik, azt valószínűleg azt jelzi, hogy bemeneti hiba, megfelelő ügyelni arra, hogy megfeleljen az összes késleltetésnél paraméterek értékeit, használatalapú. Abban az esetben, ha egy adott fogyasztás paraméterek készletét elutasítva, a hibaüzenetben a OK(ok) szöveges leírását tartalmazza.
A részletes leírását a paramétereket, van néhány lehetséges késleltetésnél érték mind a két modellben.
