---
title: Ismerje meg, hogyan kezeli a futásidejű eszközök - Azure IoT Edge | Microsoft dokumentumok
description: Ismerje meg, hogyan kezeli az IoT Edge futásidejű a modulokat, a biztonságot, a kommunikációt és a jelentéseket az eszközökön
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c184972789c412406f264f725f8b94e1f7f162ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284901"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Ismerje meg az Azure IoT Edge futásidejét és architektúráját

Az IoT Edge-futtatókörnyezet olyan programok gyűjteménye, amelyek az eszközt IoT Edge-eszközzé alakítják. Együttesen az IoT Edge futásidejű összetevői lehetővé teszik, hogy az IoT Edge-eszközök kódot fogadjanak a peremhálózaton, és kommunikálják az eredményeket.

Az IoT Edge-futásidejű felelős a következő függvények IoT Edge-eszközökön:

* Telepítse és frissítse a számítási feladatokat az eszközön.
* Az Azure IoT Edge biztonsági szabványainak karbantartása az eszközön.
* Győződjön meg arról, hogy [az IoT Edge-modulok](iot-edge-modules.md) mindig futnak.
* Modul állapotának jelentése a felhőbe a távoli figyeléshez.
* Az alsóbb rétegbeli eszközök és az IoT Edge-eszközök közötti kommunikáció kezelése.
* Az IoT Edge-eszközön lévő modulok közötti kommunikáció kezelése.
* Az IoT Edge-eszköz és a felhő közötti kommunikáció kezelése.

![A Runtime kommunikálaz elemzésekről és a modul állapotáról az IoT Hubnak](./media/iot-edge-runtime/Pipeline.png)

Az IoT Edge futásidejű feladatok két kategóriába sorolhatók: kommunikáció és a modul kezelés. Ezt a két szerepkört két összetevő hajtja végre, amelyek az IoT Edge-futásidejű részét képezik.Az *IoT Edge hub* felelős a kommunikációért, míg az *IoT Edge-ügynök* telepíti és figyeli a modulokat.

Az IoT Edge-hub és az IoT Edge-ügynök is modulok, mint bármely más, az IoT Edge-eszközön futó modul. Ezeket néha *futásidejű moduloknak is nevezik.*

## <a name="iot-edge-hub"></a>IoT edge hub

Az IoT Edge hub egyike az Azure IoT Edge-futásidejű két modulnak. Az IoT Hub helyi proxyjaként működik, és ugyanazokat a protokollvégpontokat teszi ki, mint az IoT Hub. Ez a konzisztencia azt jelenti, hogy az ügyfelek (akár eszközök, akár modulok) ugyanúgy csatlakozhatnak az IoT Edge-futásidőhez, mint az IoT Hubhoz.

>[!NOTE]
> Az IoT Edge hub támogatja az MQTT vagy AMQP használatával csatlakozó ügyfeleket. Nem támogatja a HTTP protokollt használó ügyfeleket.

Az IoT Edge hub nem az IoT Hub helyileg futó teljes verziója. Az IoT Edge hub csendesen delegál bizonyos feladatokat az IoT Hub. Például az IoT Edge hub továbbítja a hitelesítési kérelmeket az IoT Hub, amikor egy eszköz először próbál csatlakozni. Az első kapcsolat létrehozása után az IoT Edge hub helyileg gyorsítótárazza a biztonsági információkat. Az eszközről a jövőbeli kapcsolatok anélkül engedélyezettek, hogy újra hitelesíteniük kellene magukat a felhőben.

Az IoT Edge-megoldás által használt sávszélesség csökkentése érdekében az IoT Edge hub optimalizálja, hogy hány tényleges kapcsolat jön létre a felhőben. Az IoT Edge hub logikai kapcsolatokat vesz igénybe a modulokból vagy az alsóbb rétegbeli eszközökről, és egyesíti őket a felhőhöz való egyetlen fizikai kapcsolathoz. Ennek a folyamatnak a részletei a megoldás többi része számára átláthatóak. Az ügyfelek úgy gondolják, hogy saját kapcsolattal rendelkeznek a felhővel, még akkor is, ha mindegyiket ugyanazon a kapcsolaton keresztül küldik.

![Az IoT Edge hub egy átjáró a fizikai eszközök és az IoT Hub között](./media/iot-edge-runtime/Gateway.png)

Az IoT Edge hub meg állapíthatja meg, hogy csatlakozik-e az IoT Hubhoz. Ha a kapcsolat megszakad, az IoT Edge hub menti az üzeneteket vagy a két frissítést helyben. A kapcsolat újbóli létrehozása után szinkronizálja az összes adatot. Az ideiglenes gyorsítótárhoz használt helyet az IoT Edge hub ikermoduljának egy tulajdonsága határozza meg. A gyorsítótár mérete nincs korlátozva, és addig növekszik, amíg az eszköz rendelkezik tárolókapacitással.További információt az Offline lehetőségek című [témakörben talál.](offline-capabilities.md)

### <a name="module-communication"></a>Modulkommunikáció

Az IoT Edge hub megkönnyíti a modul-kommunikációt. Az IoT Edge hub üzenetközvetítőként való használata a modulokat egymástól függetlenné teszi. A moduloknak csak azokat a bemeneteket kell megadniuk, amelyekre üzeneteket fogadnak, és azokat a kimeneteket, amelyekre üzeneteket írnak. A megoldás fejlesztői összefűzheti ezeket a bemeneteket és kimeneteket úgy, hogy a modulok az adott megoldásra jellemző sorrendben dolgozzák fel az adatokat.

![Az IoT Edge Hub megkönnyíti a modulok közötti kommunikációt](./media/iot-edge-runtime/module-endpoints.png)

Adatok küldése az IoT Edge hub, egy modul meghívja a SendEventAsync metódust. Az első argumentum határozza meg, hogy melyik kimeneten küldje el az üzenetet. A következő pszeudokód üzenetet küld a **output1-en:**

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Üzenet fogadásához regisztráljon egy visszahívást, amely egy adott bemeneten érkező üzeneteket dolgoz fel. A következő pszeudokód regisztrálja a bemeneten kapott összes üzenet feldolgozásához használandóProcessor **függvényüzenetet1:**

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

A ModuleClient osztályról és annak kommunikációs módszereiről további információt a kívánt SDK-nyelv API-hivatkozási útmutatójában talál: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C,](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h) [Python,](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python) [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)vagy [Node.js.](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

A megoldás fejlesztője felelős a szabályok meghatározásáért, amelyek meghatározzák, hogy az IoT Edge hub hogyan továbbítja az üzeneteket a modulok között. Útválasztási szabályok vannak definiálva a felhőben, és leküldéses az IoT Edge hub a modul iker. Az IoT Hub-útvonalak azonos szintaxisa az Azure IoT Edge-ben lévő modulok közötti útvonalak meghatározására szolgál. További [információ: Ismerje meg, hogyan telepítheti a modulokat, és hogyan hozhat létre útvonalakat az IoT Edge-ben.](module-composition.md)

![A modulok közötti útvonalak az IoT Edge hubon keresztül haladnak](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT edge ügynök

Az IoT Edge-ügynök a másik modul, amely az Azure IoT Edge futásidejű. A modulok példányosításáért, annak biztosításáért felelős, hogy azok továbbra is fussanak, és a modulok állapotának jelentésével az IoT Hubnak. Ez a konfigurációs adatok az IoT Edge-ügynök ikertulajdonságaként vannak megírva.

Az [IoT Edge biztonsági démon elindítja](iot-edge-security-manager.md) az IoT Edge-ügynököt az eszköz indításakor. Az ügynök lekéri a modul iker az IoT Hubból, és megvizsgálja a központi telepítési jegyzék. A központi telepítési jegyzékfájl egy JSON-fájl, amely deklarálja az elindítandó modulokat.

A központi telepítési jegyzékben minden egyes elem tartalmaz konkrét információkat egy modult, és az IoT Edge-ügynök a modul életciklusának szabályozásához használja. Néhány érdekesebb tulajdonságok:

* **settings.image** – A tárolólemezkép, amelyet az IoT Edge-ügynök a modul indításához használ. Az IoT Edge-ügynök kell konfigurálni a tároló beállításjegyzék hitelesítő adatait, ha a rendszerképet jelszó védi. A tároló beállításjegyzékhitelesítő adatai távolról konfigurálhatók a központi telepítési jegyzékfájl használatával, `config.yaml` vagy az IoT Edge-eszközön az IoT Edge programmappában lévő fájl frissítésével.
* **settings.createOptions** – Olyan karakterlánc, amely közvetlenül a Moby-tároló démonnak kerül átad a modul tárolójának indításakor. A tulajdonságban lévő beállítások hozzáadása lehetővé teszi olyan speciális konfigurációk kialakítását, mint a porttovábbítás vagy a kötetek csatlakoztatása a modul tárolójába.  
* **állapot** – Az az állapot, amelyben az IoT Edge-ügynök elhelyezi a modult. Ez az érték általában úgy van beállítva, hogy *fut,* mivel a legtöbb ember szeretné, hogy az IoT Edge-ügynök azonnal indítsa el az összes modult az eszközön. Azonban megadhatja a leállítandó modul kezdeti állapotát, és megvárhatja a jövőbeli időt, hogy az IoT Edge-ügynök elindítson egy modult.Az IoT Edge-ügynök minden egyes modul állapotát jelenti vissza a felhőbe a jelentett tulajdonságokban. A kívánt tulajdonság és a jelentett tulajdonság közötti különbség a helytelenül viselkedő eszköz mutatója. A támogatott állapotok a következők:

  * Letöltés
  * Fut
  * Nem kifogástalan
  * Sikertelen
  * Leállítva

* **restartPolicy** – Hogyan indítja újra az IoT Edge-ügynök a modult. A lehetséges értékek a következők:
  
  * `never`– Az IoT Edge-ügynök soha nem indítja újra a modult.
  * `on-failure`- Ha a modul összeomlik, az IoT Edge-ügynök újraindítja azt. Ha a modul leáll, az IoT Edge-ügynök nem indítja újra.
  * `on-unhealthy`- Ha a modul összeomlik, vagy nem megfelelő állapotúnak minősül, az IoT Edge-ügynök újraindítja azt.
  * `always`- Ha a modul összeomlik, nem megfelelő állapotúnak minősül, vagy bármilyen módon leáll, az IoT Edge-ügynök újraindítja azt.

* **imagePullPolicy** – Függetlenül attól, hogy az IoT Edge-ügynök megpróbálja-e automatikusan lekérni a modul legújabb lemezképét. Ha nem ad meg értéket, az alapértelmezett beállítás *a Create .* A lehetséges értékek a következők:

  * `on-create`- Amikor egy modult indít, vagy egy modul frissítése egy új központi telepítési jegyzék, az IoT Edge-ügynök megpróbálja lekéri a modul lemezképet a tároló rendszerleíró adatbázisból.
  * `never`- Az IoT Edge-ügynök soha nem próbálja meg lekéri a modullemezt a tároló beállításjegyzékből. Ezzel a konfigurációval, akkor te vagy a felelős a modul kép az eszközre, és kezelése minden képfrissítéseket.

Az IoT Edge-ügynök futásidejű választ küld az IoT Hubnak. Itt van egy lista a lehetséges válaszok:
  
* 200 - OK
* 400 – A központi telepítési konfiguráció hibás vagy érvénytelen.
* 417 – Az eszköz nem rendelkezik központi telepítési konfigurációkészlettel.
* 412 – A központi telepítési konfigurációséma verziója érvénytelen.
* 406 – Az IoT Edge-eszköz offline állapotjelentéseket küld.
* 500 – Hiba történt az IoT Edge futásidőben.

További [információ: Ismerje meg, hogyan telepítheti a modulokat, és hogyan hozhat létre útvonalakat az IoT Edge-ben.](module-composition.md)

### <a name="security"></a>Biztonság

Az IoT Edge-ügynök kritikus szerepet játszik az IoT Edge-eszköz biztonsága. Például olyan műveleteket hajt végre, mint például egy modul lemezképének ellenőrzése a indítás előtt.

Az Azure IoT Edge biztonsági keretrendszeréről az [IoT Edge biztonsági kezelőjéről](iot-edge-security-manager.md)olvashat bővebben.

## <a name="next-steps"></a>További lépések

[Az Azure IoT Edge-modulok ismertetése](iot-edge-modules.md)
