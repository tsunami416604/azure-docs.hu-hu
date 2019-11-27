---
title: Ismerje meg, hogyan modulok futtatása az Azure IoT Edge - eszközökön logikai |} A Microsoft Docs
description: Az Azure IoT Edge-modulok olyan tárolóalapú egységet logika, amely telepíthető és kezelhető távolról, hogy futtathatja üzleti logikát az IoT Edge-ben eszközök
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 744a901c6b0260f4fc14a2f06b88dfb36973b0f8
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456581"
---
# <a name="understand-azure-iot-edge-modules"></a>Az Azure IoT Edge-modulok ismertetése

Azure IoT Edge lehetővé teszi az üzleti logikák üzembe helyezését és kezelését *modulok*formájában. Az Azure IoT Edge-modulok a legkisebb számítási egységek, IoT Edge által felügyelt, és az Azure-szolgáltatások (például az Azure Stream Analytics) vagy a saját megoldásspecifikus kódját is tartalmazhat. A modulok fejlesztésének, üzembe helyezésének és karbantartásának megismeréséhez segít a modul négy fogalmi elemének meggondolása:

* A **modul képe** egy olyan csomag, amely tartalmazza a modult meghatározó szoftvert.
* A **Module-példányok** az adott számítási egység, amely a modul rendszerképét futtatja egy IoT Edge eszközön. A modul példány van indíthatja el az IoT Edge-futtatókörnyezet.
* A **modul identitása** a IoT hub tárolt adatok (beleértve a biztonsági hitelesítő adatokat is), amelyek az egyes modulok példányaihoz vannak társítva.
* A **Twin modul** egy IOT hub tárolt JSON-dokumentum, amely a modul példányaira vonatkozó állapotinformációkat tartalmaz, beleértve a metaadatokat, a konfigurációkat és a feltételeket. 

## <a name="module-images-and-instances"></a>A modul képek és példányok

IoT Edge-modul lemezképek szerepelnek, felügyeleti, biztonsági és az IoT Edge-futtatókörnyezet, kommunikációs szolgáltatások előnyeit. Fejlesszen saját modul rendszerképét, vagy exportálhatja egy támogatott Azure-szolgáltatás, például az Azure Stream Analytics közül.
A képek a felhőben található, és azok frissíthetők, megváltozott, és különböző megoldások üzembe helyezve. Például egy modul által használt gépi tanulás segítségével megkönnyíti a gyártósor kimeneti létezik egy modul által használt számítógépes látástechnológiai egy drónt szabályozhatja, mint egy különálló lemezképet. 

Minden alkalommal, amikor egy modul rendszerképének központi telepítése egy eszközön, és az IoT Edge-futtatókörnyezet használatának lépései egy adott modul új példányát jön létre. A világ különböző részein két eszköz használhatja ugyanazt a modul-rendszerképet. Azonban minden eszköz saját modul-példánnyal rendelkezik, ha a modul elindult az eszközön. 

![Diagram - modul képek a felhőben, a modul példányok az eszközökön](./media/iot-edge-modules/image_instance.png)

Végrehajtására, a modulok képek jelen vannak egy tárházban lévő tárolórendszerképek, és modul példányok olyan tárolók, az eszközökön. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>A modul identitások

Az IoT Edge-futtatókörnyezet új modul példányt létrehozza, ha a példány egy megfelelő modul identitás társítva. A modul identitását az IoT Hub tárolja, és a címzési és biztonsági hatókör összes helyi és felhőalapú kommunikációt az eszközspecifikus modul példányhoz alkalmazzák.

Az identitás, a modul-példányhoz társított függ az eszköz az identitással fut-e a példány és a megoldás adja át, hogy a modul nevét. Ha például olyan modult hív meg `insight`, amely egy Azure Stream Analyticst használ, és a `Hannover01`nevű eszközön telepíti azt, akkor a IoT Edge futtatókörnyezet létrehoz egy `/devices/Hannover01/modules/insight`nevű megfelelő modul-identitást.

Természetesen forgatókönyvek üzembe kell helyeznie egy modul rendszerképének többször ugyanazon az eszközön, amikor üzembe helyezéséhez használható többször, különböző neveket ugyanazt a lemezképet.

![Diagram - modul identitások egyediek-e az eszközök és az eszközök között](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Ikermodulokkal

Minden egyes modul példány is rendelkezik egy megfelelő ikermodul, amely a modul példány konfigurálásához használhatja. A példány és az ikereszköz kapcsolódnak egymással a modul identitás keresztül. 

Egy ikermodul egy JSON-dokumentum, amely a modul információkat és konfigurációs tulajdonságok tárolja. Ez a koncepció párhuzamosan az [eszköz kettős](../iot-hub/iot-hub-devguide-device-twins.md) fogalmát IoT hub. A Twin modul szerkezete megegyezik az eszköz Twin szolgáltatásával. Az API-kat, és mindkét típusú twins kommunikációhoz használható is ugyanezek. Az egyetlen különbség a kettő között az identitásnak az hozza létre az ügyfél-SDK. 

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

Azure IoT Edge modulok legalább egyszer tudnak működni az IoT Hubekkel való szinkronizálás után. IoT Edge eszközök az offline képességet más IoT-eszközökre is kiterjeszthetik. További információ: [IoT Edge eszközök, modulok és alárendelt eszközök kibővített offline képességeinek ismertetése](offline-capabilities.md).

## <a name="next-steps"></a>Következő lépések
 - [A IoT Edge modulok fejlesztéséhez szükséges követelmények és eszközök ismertetése](module-development.md)
 - [A Azure IoT Edge futtatókörnyezet és az architektúrájának ismertetése](iot-edge-runtime.md)

