---
title: Ismerje meg, hogyan futnak a modulok az eszközökön – Azure IoT Edge | Microsoft dokumentumok
description: Az Azure IoT Edge-modulok olyan logikai tárolók, amelyek távolról telepíthetők és kezelhetők, így üzleti logikát futtathat az IoT Edge-eszközökön
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1c625e628f53d156ad56a1c69df1c23aec9120ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548713"
---
# <a name="understand-azure-iot-edge-modules"></a>Az Azure IoT Edge-modulok ismertetése

Az Azure IoT Edge lehetővé teszi az üzleti logika üzembe helyezését és kezelését a peremhálózaton *modulok*formájában. Az Azure IoT Edge-modulok az IoT Edge által kezelt legkisebb számítási egység, és tartalmazhatnak Azure-szolgáltatásokat (például az Azure Stream Analytics- et) vagy a saját megoldásspecifikus kódot. A modulok kifejlesztése, üzembe helyezése és karbantartása érdekében vegye figyelembe a modul négy koncepcionális elemét:

* A **modulkép** egy olyan csomag, amely a modult meghatározó szoftvert tartalmazza.
* A **modulpéldány** a modulkép IoT Edge-eszközön futó számítási egysége. A modulpéldányt az IoT Edge futásidejű indítja el.
* A **modulidentitás** az IoT Hubban tárolt információ (beleértve a biztonsági hitelesítő adatokat is), amely az egyes modulpéldányokhoz van társítva.
* Az **ikermodul** az IoT Hubban tárolt JSON-dokumentum, amely egy modulpéldány állapotadatait tartalmazza, beleértve a metaadatokat, konfigurációkat és feltételeket.

## <a name="module-images-and-instances"></a>Modulképek és -példányok

Az IoT Edge-modullemezei olyan alkalmazásokat tartalmaznak, amelyek kihasználják az IoT Edge-futásidejű felügyeleti, biztonsági és kommunikációs funkcióit. Fejlesztheti saját modullemezképeit, vagy exportálhat egyet egy támogatott Azure-szolgáltatásból, például az Azure Stream Analytics szolgáltatásból.
A lemezképek léteznek a felhőben, és frissíthetők, módosíthatók és telepíthetők különböző megoldásokban. Például egy modul, amely gépi tanulás segítségével előre a termelési sor kimenetlétezik egy külön kép, mint egy modul, amely számítógépes látást használ a drone vezérlésére.

Minden alkalommal, amikor egy modulrendszerkép telepítve van egy eszközre, és az IoT Edge futásidejű, egy új példányát, hogy a modul jön létre. A világ különböző részein két eszköz használhatja ugyanazt a modulképet. Azonban minden eszköz saját modulpéldányt, amikor a modul elindul az eszközön.

![Diagram - Modulképek a felhőben, modulpéldányok az eszközökön](./media/iot-edge-modules/image_instance.png)

A megvalósításban a modulok lemezképei tárolórendszerképekként léteznek egy tárházban, és a modulpéldányok tárolók az eszközökön.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Modulidentitások

Amikor egy új modulpéldányt hoz létre az IoT Edge futásidő, leáll egy megfelelő modulidentitást. A modulidentitás az IoT Hubban van tárolva, és az adott modulpéldány összes helyi és felhőbeli kommunikációjának címzési és biztonsági hatóköreként használatos.

A modulpéldányhoz társított identitás attól függ, hogy az eszköz, amelyen a példány fut, és a nevet adja meg, hogy a modul a megoldásban. Ha például egy `insight` Azure Stream Analytics-et használó modult hív meg, és egy nevű `Hannover01`eszközön telepíti, `/devices/Hannover01/modules/insight`az IoT Edge futásidejű létrehoz egy megfelelő modulidentitást.

Nyilvánvaló, hogy olyan esetekben, amikor egy modullemezképet többször kell telepítenie ugyanazon az eszközön, ugyanazt a lemezképet többször is telepítheti különböző nevekkel.

![Diagram – A modulidentitások egyediek az eszközökön belül és az eszközök között](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Modul ikrek

Minden modulpéldány rendelkezik egy megfelelő ikermodullal is, amely a modulpéldány konfigurálásához használható. A példány és az iker a modul identitásán keresztül kapcsolódnak egymáshoz.

A modul iker egy JSON-dokumentum, amely tárolja a modul adatait és konfigurációs tulajdonságait. Ez a koncepció párhuzamot von az [iot hubból az ikereszköz-koncepcióval.](../iot-hub/iot-hub-devguide-device-twins.md) Az ikermodul szerkezete megegyezik az ikereszközével. Az API-k használt kölcsönhatásba lépnek mindkét típusú ikrek is azonosak. Az egyetlen különbség a kettő között az ügyfél SDK-jának példányosítására használt identitás.

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a
// module since it is created with a module’s connection string instead
// of a device connection string.
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings);
await client.OpenAsync();

// Get the module twin
Twin twin = await client.GetTwinAsync();
```

## <a name="offline-capabilities"></a>Offline képességek

Az Azure IoT Edge-modulok az IoT Hubbal való legalább egyszeri szinkronizálás után határozatlan ideig offline módban is működhetnek. Az IoT Edge-eszközök ezt az offline funkciót más IoT-eszközökre is kiterjeszthetik. További [információ: Az IoT Edge-eszközök, modulok és gyermekeszközök kiterjesztett offline képességeinek ismertetése.](offline-capabilities.md)

## <a name="next-steps"></a>További lépések

* [Ismerje meg az IoT Edge-modulok fejlesztéséhez szükséges követelményeket és eszközöket](module-development.md)
* [Ismerje meg az Azure IoT Edge futásidejét és architektúráját](iot-edge-runtime.md)
