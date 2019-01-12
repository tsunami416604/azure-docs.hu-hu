---
title: Ismerje meg, hogyan modulok futtatása az Azure IoT Edge - eszközökön logikai |} A Microsoft Docs
description: Az Azure IoT Edge-modulok olyan tárolóalapú egységet logika, amely telepíthető és kezelhető távolról, hogy futtathatja üzleti logikát az IoT Edge-ben eszközök
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 976b46a26d95b5e252b0df2383ea94b4dd280d24
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229625"
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

Minden alkalommal, amikor egy modul rendszerképének központi telepítése egy eszközön, és az IoT Edge-futtatókörnyezet használatának lépései egy adott modul új példányát jön létre. A világ különböző részein két eszközt használhatja ugyanazt a modul a lemezképet. Azonban minden egyes eszköz áll a saját modulpéldány a modul indításakor az eszközön. 

![Diagram - modul képek a felhőben, a modul példányok az eszközökön](./media/iot-edge-modules/image_instance.png)

Végrehajtására, a modulok képek jelen vannak egy tárházban lévő tárolórendszerképek, és modul példányok olyan tárolók, az eszközökön. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>A modul identitások

Az IoT Edge-futtatókörnyezet új modul példányt létrehozza, ha a példány egy megfelelő modul identitás társítva. A modul identitását az IoT Hub tárolja, és a címzési és biztonsági hatókör összes helyi és felhőalapú kommunikációt az eszközspecifikus modul példányhoz alkalmazzák.
Az identitás, a modul-példányhoz társított függ az eszköz az identitással fut-e a példány és a megoldás adja át, hogy a modul nevét. Például ha felhívja `insight` olyan modul, amely egy Azure Stream Analytics, és használja telepítése egy eszközön nevű `Hannover01`, az IoT Edge-futtatókörnyezet létrehoz egy megfelelő nevű modul identitást `/devices/Hannover01/modules/insight`.

Természetesen forgatókönyvek üzembe kell helyeznie egy modul rendszerképének többször ugyanazon az eszközön, amikor üzembe helyezéséhez használható többször, különböző neveket ugyanazt a lemezképet.

![Diagram - modul identitások egyediek-e az eszközök és az eszközök között](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Ikermodulokkal

Minden egyes modul példány is rendelkezik egy megfelelő ikermodul, amely a modul példány konfigurálásához használhatja. A példány és az ikereszköz kapcsolódnak egymással a modul identitás keresztül. 

Egy ikermodul egy JSON-dokumentum, amely a modul információkat és konfigurációs tulajdonságok tárolja. A fogalom a parallels a [ikereszköz](../iot-hub/iot-hub-devguide-device-twins.md) koncepció az IoT hubról. Egy ikermodul struktúrája megegyezik az ikereszközök. Az API-kat, és mindkét típusú twins kommunikációhoz használható is ugyanezek. Az egyetlen különbség a kettő között az identitásnak az hozza létre az ügyfél-SDK. 

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
* **Modulok hitelesítse magát újra, az offline állapotban az IoT Edge hub nem kell**. Modulok csak hitelesítheti az IoT hub-aktív kapcsolattal rendelkező IoT Edge-hubs. Modulok kell hitelesítse magát újra, ha azok bármilyen okból újra lesz indítva. Modulok továbbra is küldhet üzeneteket az IoT Edge hubot követően a SAS-token érvényessége lejárt. Amikor visszatér a kapcsolatot, ha az IoT Edge hubot egy új jogkivonatot kér a modul, és érvényesíti azt az IoT hubbal. Ha sikeres, az IoT Edge hubon tárolt modul üzeneteket továbbít, még a során a modul jogkivonat lejárt küldött üzeneteket. 
* **A modul közben az üzeneteket küldő offline esetén továbbra is működőképes kapcsolat folytatja**. Után az IoT hubhoz való csatlakozáshoz, az IoT Edge hubot kell érvényesítenie modul új jogkivonatot (Ha egy korábbi lejárt) előtt továbbíthatja a modul üzeneteket. A modul nem érhető el új jogkivonatot biztosítani, ha az IoT Edge hubon tárolt üzenetek a modul nem cselekedhet. 
* **Az üzenetek tárolására lemezterülettel rendelkezik-e az IoT Edge hubot**. Alapértelmezés szerint üzeneteket az IoT Edge hubot tároló fájlrendszer vannak tárolva. Nincs olyan konfigurációs beállítást adja meg az üzenetek tárolására helyette egy csatlakoztatott kötetre. Mindkét esetben szükség van a késleltetett kézbesítéséhez az IoT Hub az üzenetek tárolásához rendelkezésre álló terület.  

További offline funkciók is elérhetők a nyilvános előzetes verzióban érhető el. További információkért lásd: [ismertetése az IoT Edge kiterjesztett offline képességeiről, eszközök, a modulok és a gyermek eszközök](offline-capabilities.md).

## <a name="next-steps"></a>További lépések
 - [A követelmények és az eszközök IoT Edge-modulok megismerése](module-development.md)
 - [Az Azure IoT Edge-futtatókörnyezet és architektúrájának ismertetése](iot-edge-runtime.md)

<!-- Images -->
[2]: ./media/iot-edge-modules/identity.png
