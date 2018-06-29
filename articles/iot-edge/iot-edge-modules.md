---
title: Azure IoT peremhálózati modulok megértése |} Microsoft Docs
description: 'Tudnivalók Azure IoT Edge-modulokat és azok miként vannak konfigurálva:'
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 02/15/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9c196ec92fc7997617fa464d676dc93ca9fe84f0
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029090"
---
# <a name="understand-azure-iot-edge-modules"></a>Azure IoT peremhálózati modulok ismertetése

Az Azure IoT peremhálózati lehetővé teszi, hogy telepíthetnek és kezelhetnek olyan üzleti logika formájában oldal *modulok*. Azure IoT peremhálózati modulok a legkisebb egység kezeli az IoT-Edge-számítás, és Azure-szolgáltatásokhoz (például az Azure Stream Analytics) vagy a saját Megoldásfüggő kódot tartalmazhatnak. Hogyan modulok kifejlesztett, telepített, és karbantartott, négy fogalmi alkotóelemeit modul gondol segít megérteni:

* A **modul kép** , amely meghatározza egy modult a szoftvert tartalmazó csomag.
* A **modul példány** számítási modul kép futó IoT peremhálózati eszköz a meghatározott egysége. A modul példánya a IoT peremhálózati futtatókörnyezet elindult.
* A **modul identitás** olyan kódrészletek, IoT-központ, a modul-példányokhoz tartozó tárolt (beleértve a biztonsági hitelesítő adatok) információit.
* A **modul iker** tárolva az IoT-központ, a modul példányához, beleértve a metaadatok, a konfiguráció és a feltételek állapotadatait tartalmazó JSON-dokumentumból. 

## <a name="module-images-and-instances"></a>A modul képek és példányok

IoT peremhálózati modul lemezképek olyan alkalmazásokat tartalmaznak, a felügyeleti, a biztonsági és a kommunikációs szolgáltatások IoT peremhálózati futásidejű előnyeit. A saját modul lemezképek fejlesztése, vagy exportálja egy támogatott Azure szolgáltatás, például az Azure Stream Analytics közül.
A lemezképek a felhőben vannak, és azok frissítése, megváltozott, és a különböző megoldások rendszerbe. A modul, amely gépi tanulással termelési sor kimeneti előrejelzése például egy különálló lemezképet, mint a modul, amely egy dron szabályozásához használja a számítógép stratégiai létezik. 

Minden alkalommal, amikor egy modul lemezképet központi telepítése egy eszközön, és az IoT peremhálózati futásidejű indította modult egy új példánya jön létre. A világ különböző pontjain két eszköz használhatja ugyanazt a modul lemezképet; azonban mindegyik rendelkezik saját modul példány a modul indításakor az eszközön. 

![A felhő - modul példányok eszközökön modul lemezképek][1]

Megvalósításban modulok lemezképek nem létezik-e a tároló képek tárházban, és eszközök tárolói modul példányok. Azure IoT peremhálózati alkalmazási helyzetei növekedésével modul képek és példányok új típusú jön létre. Korlátozott erőforrás eszközök például nem futtatható, előfordulhat, hogy létezik-e dinamikus csatolású kódtárakat és a futtatható példányok modul lemezképeket tárolók. 

## <a name="module-identities"></a>A modul identitások

Új modul példány létrehozásakor az IoT-Edge-futtatókörnyezet példány társítva van egy megfelelő modul identitást. A modul identitás-IoT-központ tárolja, és a címzési és a biztonsági hatókör összes helyi és a felhő kommunikációt az adott modult példánynak alkalmazzák.
A modul-példányhoz társított identitása függ az eszköz identitásának a fut-e a példány és a megoldásban Megadja, hogy a modul nevét. Például ha meghívja a `insight` modul, amely egy Azure Stream Analytics, és használja azt telepítette nevezett eszköz `Hannover01`, az IoT-Edge futásidejű létrehoz egy megfelelő modul identitás nevű `/devices/Hannover01/modules/insight`.

Egyértelműen esetekben ha egy modul lemezkép többször ugyanarra az eszközre telepítendő telepítheti eltérő nevű több alkalommal ugyanazt a lemezképet.

![A modul identitások egyediek.][2]

## <a name="module-twins"></a>A modul twins

Minden egyes modul példány is rendelkezik a megfelelő modul iker a modul példány konfigurálásához használható. A példány és a kettős társított egymással a modul identitás keresztül. 

Egy modul iker modul információkat és konfigurációs tulajdonságok tárolt JSON-dokumentumhoz. A fogalom fekvő a [eszköz iker] [ lnk-device-twin] koncepció az IoT-központot. A modul iker szerkezete megegyezik pontosan egy eszköz iker. Az API-khoz való twins mindkét típusú interakcióra használatos is azonosak. Az egyetlen különbség a kettő között az az ügyfél SDK megjelenítéséhez használt identitása. 

```csharp
// Create a DeviceClient object. This DeviceClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
await client.OpenAsync(); 
 
// Get the model twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="offline-capabilities"></a>Kapcsolat nélküli képességek

Azure IoT peremhálózati támogatja az IoT-Edge eszközök offline műveleteket. Ezek a képességek legfeljebb a lépést, és további helyzeteket is vannak fejlesztés alatt áll. 

IoT peremhálózati modulok hosszabb ideig offline állapotban lehet, mindaddig, amíg a következő feltételek teljesülnek: 

* **Üzenet-élettartama (TTL) még mindig érvényes**. Üzenet TTL alapértelmezett értéke két óra, de módosított magasabb vagy alacsonyabb lehet a tárolóban és továbbítsa az IoT él konfiguráció központ beállításait. 
* **Modulok nem kell újból hitelesítésre a peremhálózati IoT hubbal nélküli**. Modulok csak hitelesítheti a peremhálózati hubs, amelyek rendelkeznek egy aktív kapcsolatot létesít az IoT-központ. Modulok kell újra hitelesíteni, ha bármilyen okból újraindítást. Modulok továbbra is küldhet üzeneteket a peremhálózati hub után a SAS-token érvényessége lejárt. Ha folytatja a kapcsolatot, a peremhálózati hub egy új jogkivonatot kér a modul, és és az IoT hub érvényesíti azt. Ha sikeres, a peremhálózati hub továbbítja modul tárolt, még akkor is a távozó üzeneteinek során a modul token lejárt. 
* **A modult, amely során az üzenetek küldése offline működőképességét amikor a kapcsolat folytatása**. Az IoT-központ csatlakozni, akkor a peremhálózati hub kell érvényesítenie egy új modul token (ha az előzőre lejárt) előtt továbbíthatja a modul üzenetek. Ha a modul nem egy új jogkivonatot kínálnak, nem a modul tárolt üzenetek hajthat végre a peremhálózati központ. 
* **Az Edge-hubhoz hely a lemezen tárolja az üzeneteket**. Alapértelmezés szerint üzenetek a peremhálózati hub tároló filesystem vannak tárolva. Nincs olyan konfigurációs beállítás határozza meg az üzenetek tárolásához helyette csatlakoztatott kötetet. Mindkét esetben kell adni a késleltetett kézbesítéséhez az IoT-központ üzenetek tárolásához rendelkezésre álló terület.  

## <a name="next-steps"></a>További lépések
 - [Az Azure IoT peremhálózati futásidejű és az architektúra][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md