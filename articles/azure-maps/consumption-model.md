---
title: Járműfogyasztási modellek az útvonaltervezéshez | Microsoft Azure Maps
description: Ebben a cikkben a Microsoft Azure Maps útválasztáshoz való járműfogyasztási modellekről olvashat.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: c1572eddf78ca2d5f8f4e3ee9f1fe47b0d43f5aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190249"
---
# <a name="consumption-model"></a>Használatalapú modell

Az Útválasztási szolgáltatás a járműspecifikus fogyasztási modell részletes leírásához paramétereket biztosít.
A **járműEngineType**értékétől függően két fő fogyasztási modell támogatott: _égés_ és _elektromos_. Helytelen olyan paramétereket megadni, amelyek ugyanabban a kérelemben különböző modellekhez tartoznak. Továbbá, fogyasztási modell paraméterei nem használhatók a következő **travelMode** értékek: _kerékpár_ és _gyalogos_.

## <a name="parameter-constraints-for-consumption-model"></a>A felhasználási modell paraméterkényszerei

Mindkét felhasználási modellben vannak bizonyos függőségek a paraméterek megadásakor. Ami azt jelenti, hogy bizonyos paraméterek kifejezett megadása más paraméterek megadását is szükségessé teheti. Az alábbi függőségeket kell figyelembe venni:

* Minden paraméter megköveteli **constantSpeedConsumption** meg kell adnia a felhasználó. Hiba bármely más fogyasztási modell paramétert megadni, ha nincs megadva **constantSpeedConsumption.** A **vehicleWeight** paraméter kivétel tetsző.
* **gyorsulásHatékonyság** és **lassulásA hatékonyságot** mindig párként kell megadni (azaz mindkettőt vagy egyet sem).
* Ha **gyorsulásHatékonyság** és **lassulásHatékonyság van** megadva, az értékük szorzata nem lehet nagyobb 1-nél (az állandó mozgás megakadályozása érdekében).
* **uphillEfficiency** és **downhillEfficiency** mindig meg kell adni, mint egy pár (ez, mindkettő vagy nincs).
* Ha **felfeléhatékonyság** és **downhillEfficiency** van megadva, az értékük szorzata nem lehet nagyobb 1-nél (az örökös mozgás megakadályozása érdekében).
* Ha \*a felhasználó a __Hatékonyság__ paramétereket adja meg, akkor a **vehicleWeight** paramétert is meg kell adni. Amikor **vehicleEngineType** _az égés,_ **üzemanyagEnergyDensityInMJoulesPerLiter** meg kell adni is.
* **A maxChargeInkWh-t** és **a currentChargeInkWh-t** mindig párként kell megadni (azaz mindkettőt vagy egyiket sem).

> [!NOTE]
> Ha csak **constantSpeedConsumption** van megadva, a fogyasztási számításoknál nincs más fogyasztási szempont, például a lejtők és a jármű gyorsulása.

## <a name="combustion-consumption-model"></a>Égési fogyasztási modell

Az égési fogyasztási modell akkor használatos, ha a **vehicleEngineType** _égésre_van beállítva.
A modellhez tartozó paraméterek listája az alábbiakban található. A részletes leírást a Paraméterek részben található.

* constantSpeedConsumptionInLitersPerHundredkm
* járműsúlya
* currentFuelInLiters
* kiegészítőPowerInLitersPerHour
* üzemanyagEnergyDensityInMJoulesPerLiter
* gyorsulásHatékonyság
* lassulásHatékonyság
* felfelé hatékonyság
* downhillEfficiency (downhillEfficiency)

## <a name="electric-consumption-model"></a>Elektromos fogyasztási modell

Az elektromos fogyasztási modell akkor használatos, ha a **vehicleEngineType** _elektromos_.
A modellhez tartozó paraméterek listája az alábbiakban található. A részletes leírást a Paraméterek részben található.

* constantSpeedConsumptionInkWhPerHundredkm
* járműsúlya
* currentChargeInkWh
* maxChargeInkWh
* kiegészítőPowerInkW
* gyorsulásHatékonyság
* lassulásHatékonyság
* felfelé hatékonyság
* downhillEfficiency (downhillEfficiency)

## <a name="sensible-values-of-consumption-parameters"></a>A fogyasztási paraméterek ésszerű értékei

A felhasználási paraméterek egy adott készlete elutasítható, még akkor is, ha a készlet minden explicit követelménynek megfelelhet. Ez akkor fordul elő, ha egy adott paraméter értéke, vagy több paraméter értékeinek kombinációja a felhasználási értékek ésszerűtlen magnitúdóihoz vezet. Ha ez megtörténik, akkor valószínűleg bemeneti hibát jelez, mivel a felhasználási paraméterek minden ésszerű értékének megfelelő gondossággal történik. A felhasználási paraméterek egy adott készletének elutasítása esetén a mellékelt hibaüzenet az ok(ok) szöveges magyarázatát tartalmazza.
A paraméterek részletes leírása mindkét modell ésszerű értékeit tartalmazza.
