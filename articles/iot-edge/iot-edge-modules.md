---
title: Az Azure IoT Edge-modulok megismerése |} A Microsoft Docs
description: 'További tudnivalók az Azure IoT Edge-modulok és azok miként vannak konfigurálva:'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3d76f5931e3636f19c2030c4090116a0791db819
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567332"
---
# <a name="understand-azure-iot-edge-modules"></a>Az Azure IoT Edge-modulok ismertetése

Az Azure IoT Edge lehetővé teszi, hogy központi telepítésére és felügyeletére az üzleti logikát az Edge formájában *modulok*. Az Azure IoT Edge-modulok a legkisebb számítási egységek, IoT Edge által felügyelt, és az Azure-szolgáltatások (például az Azure Stream Analytics) vagy a saját megoldásspecifikus kódját is tartalmazhat. Tudni, hogyan modulok kifejlesztett, telepített, és karbantartott, segít a négy fogalmi alkotóelemeit modul gondol:

* A **modul rendszerképének** a szoftvert, amely meghatározza egy modult tartalmazó csomag.
* A **modulpéldány** az adott számítási egységek, a modul rendszerképének futó IoT Edge-eszköz van. A modul példány van indíthatja el az IoT Edge-futtatókörnyezet.
* A **modul identitás** olyan információk (beleértve a biztonsági hitelesítő adatok) az IoT hubon, minden egyes modul-példányhoz tartozó tárolt.
* A **ikermodul** az IoT hubon, egy modul példányt, beleértve a metaadatokat, konfigurációkat és állapotokat állapotinformációinak tartalmazó tárolt JSON-dokumentumok. 

## <a name="module-images-and-instances"></a>A modul képek és példányok

IoT Edge-modul lemezképek szerepelnek, felügyeleti, biztonsági és az IoT Edge-futtatókörnyezet, kommunikációs szolgáltatások előnyeit. Fejlesszen saját modul rendszerképét, vagy exportálhatja egy támogatott Azure-szolgáltatás, például az Azure Stream Analytics közül.
A képek a felhőben található, és azok frissíthetők, megváltozott, és különböző megoldások üzembe helyezve. Például egy modul által használt gépi tanulás segítségével megkönnyíti a gyártósor kimeneti létezik egy modul által használt számítógépes látástechnológiai egy drónt szabályozhatja, mint egy különálló lemezképet. 

Minden alkalommal, amikor egy modul rendszerképének központi telepítése egy eszközön, és az IoT Edge-futtatókörnyezet használatának lépései egy adott modul új példányát jön létre. A világ különböző részein két eszközt használhatja ugyanazt a modul lemezképet; azonban minden egyes kellene saját modulpéldány a modul indításakor az eszközön. 

![A modul képeket a felhőben – modul példányok az eszközökön](./media/iot-edge-modules/image_instance.png)

Végrehajtására, a modulok képek jelen vannak egy tárházban lévő tárolórendszerképek, és modul példányok olyan tárolók, az eszközökön. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>A modul identitások

Az IoT Edge-futtatókörnyezet új modul példányt létrehozza, ha a példány egy megfelelő modul identitás társítva. A modul identitását az IoT Hub tárolja, és a címzési és biztonsági hatókör összes helyi és felhőalapú kommunikációt az eszközspecifikus modul példányhoz alkalmazzák.
Az identitás, a modul-példányhoz társított függ az eszköz az identitással fut-e a példány és a megoldás adja át, hogy a modul nevét. Például ha felhívja `insight` olyan modul, amely egy Azure Stream Analytics, és használja telepítése egy eszközön nevű `Hannover01`, az IoT Edge-futtatókörnyezet létrehoz egy megfelelő nevű modul identitást `/devices/Hannover01/modules/insight`.

Természetesen forgatókönyvek üzembe kell helyeznie egy modul rendszerképének többször ugyanazon az eszközön, amikor üzembe helyezéséhez használható többször, különböző neveket ugyanazt a lemezképet.

![A modul identitások egyediek.](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Ikermodulokkal

Minden egyes modul példány is rendelkezik egy megfelelő ikermodul, amely a modul példány konfigurálásához használhatja. A példány és az ikereszköz kapcsolódnak egymással a modul identitás keresztül. 

Egy ikermodul egy JSON-dokumentum, amely a modul információkat és konfigurációs tulajdonságok tárolja. A fogalom a parallels a [ikereszköz](../iot-hub/iot-hub-devguide-device-twins.md) koncepció az IoT hubról. Egy ikermodul struktúrája megegyezik pontosan egy ikereszközt. Az API-kat, és mindkét típusú twins kommunikációhoz használható is ugyanezek. Az egyetlen különbség a kettő között az identitásnak az hozza létre az ügyfél-SDK. 

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

Az Azure IoT Edge az IoT Edge-eszközök offline műveleteket támogatja. Most ezeket a képességeket korlátozva. 

IoT Edge-modulok mindaddig, amíg az alábbi követelmények teljesülnek-e hosszabb ideig offline állapotban lehet: 

* **Üzenet time-to-live (Élettartam TTL) nem járt le**. Üzenet Élettartama alapértelmezett értéke két óra, de módosított magasabb vagy alacsonyabb lehet a Store az és továbbítási konfiguráció az IoT Edge-ben a hub beállításai. 
* **Modulok hitelesítse magát újra, az offline állapotban az IoT Edge hub nem kell**. Modulok csak hitelesítheti az Edge hub, amely az IoT hub-aktív internetkapcsolat. Modulok kell újra hitelesíteni kell, ha azok bármilyen okból újra lesz indítva. Modulok továbbra is küldhet üzeneteket az Edge hub követően a SAS-token érvényessége lejárt. Amikor visszatér a kapcsolatot, ha az Edge hub egy új jogkivonatot kér a modult, és érvényesíti azt az IoT hubbal. Sikeres művelet esetén az Edge hub továbbítja a modul üzeneteket tárolt, még a üzeneteinek során a modul jogkivonat lejárt. 
* **A modul közben az üzeneteket küldő offline esetén továbbra is működőképes kapcsolat folytatja**. Után az IoT hubhoz való csatlakozáshoz, az Edge hub kell érvényesítenie modul új jogkivonatot (Ha egy korábbi lejárt) előtt továbbíthatja a modul üzeneteket. Ha a modul nem érhető el új jogkivonatot biztosítani, az Edge hub tárolt üzenetek a modul nem cselekedhet. 
* **Az Edge hub az üzenetek tárolására lemezterülettel rendelkezik**. Alapértelmezés szerint üzeneteket az Edge hub tároló fájlrendszer vannak tárolva. Nincs olyan konfigurációs beállítást adja meg az üzenetek tárolására helyette egy csatlakoztatott kötetre. Mindkét esetben szükség van a késleltetett kézbesítéséhez az IoT Hub az üzenetek tárolásához rendelkezésre álló terület.  

További offline funkciók is elérhetők a nyilvános előzetes verzióban érhető el. További információkért lásd: [ismertetése az IoT Edge kiterjesztett offline képességeiről, eszközök, a modulok és a gyermek eszközök](offline-capabilities.md).

## <a name="next-steps"></a>További lépések
 - [A követelmények és az eszközök IoT Edge-modulok megismerése](module-development.md)
 - [Az Azure IoT Edge-futtatókörnyezet és architektúrájának ismertetése](iot-edge-runtime.md)

<!-- Images -->
[2]: ./media/iot-edge-modules/identity.png
