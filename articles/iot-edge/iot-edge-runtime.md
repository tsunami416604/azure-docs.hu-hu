---
title: Ismerje meg, hogyan kezeli a a modul az Azure IoT Edge-eszközök – |} A Microsoft Docs
description: Ismerje meg, hogy a modulok, biztonsági, kommunikációs és az eszközök reporting kezeli az Azure IoT Edge-futtatókörnyezet
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: bb2df9c32d5adc8160da82148e4a66a4ab68d182
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311599"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Az Azure IoT Edge-futtatókörnyezet és architektúrájának ismertetése

Az IoT Edge-futtatókörnyezet, amely szükség van ahhoz, hogy figyelembe kell venni az IoT Edge-eszköz egy eszközön telepített programok gyűjteménye. Együttesen az IoT Edge-futtatókörnyezet összetevői kattintva kérhet kódot futtatása a peremhálózaton IoT Edge-eszközök és a kommunikációhoz az eredményeket. 

Az IoT Edge-futtatókörnyezet az IoT Edge-eszközökön a következő funkciókat hajtja végre:

* Telepítése és frissítése a számítási feladatokat az eszközön.
* Az Azure IoT Edge biztonsági szabványait az eszközön karbantartása.
* Ügyeljen arra, hogy [IoT Edge-modulok](iot-edge-modules.md) folyamatos futását.
* Modulok állapotát a felhőnek a távoli monitorozáshoz jelentéseket.
* Alárendelt levéleszközök és az IoT Edge-eszközök közötti kommunikáció támogatására.
* Az IoT Edge-eszközön a modulok közötti kommunikáció támogatására.
* Az IoT Edge-eszköz és a felhő közötti kommunikáció támogatására.

![Modul kommunikál az elemzések és a modulok állapotát az IoT hubhoz](./media/iot-edge-runtime/Pipeline.png)

Az IoT Edge-futtatókörnyezet feladatai két kategóriába sorolhatók: kommunikációs és modul-kezelés. E két szerepkör két összetevőből, az IoT Edge-futtatókörnyezet végzi. A *IoT Edge hubot* felelős a kommunikációs, amíg a *IoT Edge-ügynök* helyez üzembe, és a modulok figyeli. 

Az IoT Edge-központ és az IoT Edge-ügynök is olyan modulok, csakúgy, mint egy IoT Edge-eszközön futó bármely egyéb modult. 

## <a name="iot-edge-hub"></a>IoT Edge hub

Az IoT Edge hub két, az Azure IoT Edge-futtatókörnyezet alkotó modulok egyike. Azt proxyként funkcionál, helyi az IoT Hub, IoT Hub azonos protokollvégpontokat teszi elérhetővé. A konzisztencia azt jelenti, hogy az ügyfelek (e eszközök vagy modulok) is csatlakozhat az IoT Edge-futtatókörnyezet, ugyanúgy, mint az IoT hubhoz. 

>[!NOTE]
> Az IoT Edge Hub az MQTT vagy AMQP használatával csatlakozó ügyfelek támogatja. Nem támogatja a HTTP Protokollt használó ügyfeleket. 

Az IoT Edge hub nem áll a helyileg futó IoT Hub teljes verzióját. Néhány dolog, amely az IoT Edge-központ csendes delegálja az IoT hubnak. Például az IoT Edge hubot továbbítja a hitelesítési kérelmeket az IoT hubhoz, ha egy eszköz először megpróbálja való csatlakozáshoz. Miután az első kapcsolat létrejött, biztonsági adatokat a helyi gyorsítótárba helyezi az IoT Edge hub által. Kapcsolatait, erről az eszközről a felhőbe való hitelesítéséhez nélkül engedélyezve. 

>[!NOTE]
>A runtime csatlakoztatva kell lennie minden alkalommal, amikor egy eszköz hitelesítéséhez próbálkozik.

Az IoT Edge-megoldás sávszélesség csökkentése érdekében használja, az IoT Edge hubot optimalizálja hány tényleges kapcsolatok hozhatók létre a felhőben. IoT Edge hubot felveszi azt a logikai kapcsolatok ügyfelek, mint a modulok vagy levéleszközök és egyesíti azokat a felhőbe egyetlen fizikai kapcsolathoz. Ez a folyamat részletei transzparensek a megoldás többi részétől. Az ügyfelek úgy gondolja, hogy hozzáadhatják saját kapcsolatukat a felhőbe rendelkeznek, annak ellenére, hogy azok minden távozási ugyanazon a kapcsolaton keresztül. 

![IoT Edge hubot a fizikai eszközök és az IoT Hub közötti átjáró](./media/iot-edge-runtime/Gateway.png)

IoT Edge hubot megállapíthatja, hogy csatlakozik az IoT Hub. Ha megszakad a kapcsolat, IoT Edge hubot menti az üzenetek vagy a helyi ikereszköz-frissítések. Miután a kapcsolat helyreállt, a szinkronizált összes adatot. Az átmeneti gyorsítótár használt helyet az IoT Edge hubot ikermodul tulajdonsága határozza meg. A gyorsítótár méretét nem maximumon, és az eszköz tárkapacitása is növekszik. 

### <a name="module-communication"></a>A modul kommunikáció

IoT Edge hubot modult a modul kommunikációt létesít. IoT Edge használatával üzenetközvetítőként hub tartja a modulok egymástól független. Modulok csak adja meg, amelyen a üzenetek és a kimeneteket, amelyhez a üzeneteket írhat elfogadják a bemeneteket kell. A megoldás fejlesztő ezután összefűzi ezeket a bemeneteket, és kiírja együtt, hogy a modulok a rendelés megoldásra jellemző adatok feldolgozása. 

![IoT Edge hubot modul-modul kommunikációt létesít](./media/iot-edge-runtime/module-endpoints.png)

Adatok küldését az IoT Edge hub, a modul meghívja a SendEventAsync metódust. Az első argumentum meghatározza a milyen kimenetet az üzenet elküldéséhez. A következő pseudocode output1 üzenetet küld:

   ```csharp
   ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Egy üzenet jelenik meg, hogy regisztráljon egy visszahívást, amelyet a megadott bemeneti érkező üzeneteket dolgoz fel. A következő pseudocode regisztrálja a függvény messageProcessor input1 fogadott összes üzenetek feldolgozására használható:

   ```csharp
   await client.SetInputMessageHandlerAsync(“input1”, messageProcessor, userContext);
   ```

A ModuleClient osztály és a kommunikációs módszer kapcsolatos további információkért tekintse meg az API-referencia elsődleges SDK nyelv: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C és Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), vagy [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

A megoldás fejlesztő feladata a szabályok, amelyek meghatározzák, hogyan IoT Edge hubot továbbítja moduljai közötti üzenetek megadására. Útválasztási szabályok meghatározva a felhőben, és leküldte a IoT Edge hub az ikereszközön. Ugyanazt a szintaxist, az IoT Hub útvonalakat az Azure IoT Edge moduljai közötti útvonalak meghatározására szolgál. 

<!--- For more info on how to declare routes between modules, see []. --->   

![Útvonalak moduljai közötti nyissa meg az IoT Edge hubon keresztül](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge-ügynök

Az IoT Edge-ügynök a többi modul, amely az Azure IoT Edge-futtatókörnyezet. Ez felelős modulok hárítható el, biztosítva, hogy továbbra is futni és a modulok állapotát megkezdheti az IoT Hub. Bármely egyéb modult, mint az IoT Edge-ügynök az ikermodul használja a konfigurációs adatok tárolására. 

A [IoT Edge biztonsági démon](iot-edge-security-manager.md) az IoT Edge agent elindul az eszköz indításakor. Az ügynök az ikermodul lekéri az IoT hubról, és megvizsgálja a manifest nasazení. Manifest nasazení egy JSON-fájlt, amely deklarálja a modulokat, amelyek kell elindítani. 

Manifest nasazení minden eleme egy modul kapcsolatos információkat tartalmaz, és a modul életciklus szabályozása az IoT Edge-ügynök által használt. Az érdekesebb tulajdonságok a következők: 

* **Settings.Image** – a tároló rendszerképét, amely a modul indításához használja az IoT Edge-ügynök. Az IoT Edge-ügynök a tárolójegyzék hitelesítő adatokkal kell konfigurálni, ha a kép jelszóval védett. Hitelesítő adatok a tároló-beállításjegyzék konfigurálható manifest nasazení segítségével távolról, vagy maga az IoT Edge-eszköz frissítésével a `config.yaml` az IoT Edge program mappában található fájl.
* **settings.createOptions** – egy karakterlánc, amely az átadott közvetlenül a Docker-démon indítása egy modul tároló esetén. Ezt a tulajdonságot a Docker-beállítások hozzáadása lehetővé teszi a speciális beállításokat, például a továbbítási vagy kötetek csatlakoztatása egy modul tárolóba port.  
* **állapot** – az állam, amelyben az IoT Edge-ügynök a modul helyezi el. Ez az érték beállítása általában *futó* , a legtöbb ember szeretné azonnal elindítani az eszközön lévő összes modult az IoT Edge-ügynök. Azonban megadhatja a modul le kell állítani, és várja meg, hogy az IoT Edge-ügynök modul indítása egy jövőbeli időpontot kezdeti állapota. Az IoT Edge-ügynök az egyes modulok állapotát biztonsági jelentések a jelentett tulajdonságok a felhőbe. Különbség a kívánt tulajdonság és a jelentett tulajdonság azt jelzi, hogy a rosszul eszköz. A támogatott állapotok a következők:
   * Letöltés folyamatban
   * Fut
   * Nem kifogástalan
   * Meghiúsult
   * Leállítva
* **restartPolicy** – az IoT Edge-ügynök újraindítása modul. A lehetséges értékek:
   * Az IoT Edge-ügynök sosem – soha nem a modul indul újra.
   * onFailure – Ha a modul összeomlik, az IoT Edge-ügynök újraindítja. Ha a modul szabályszerűen álljon le, az IoT Edge-ügynök nem indítja újra azt.
   * Nem megfelelő – Ha a modul összeomlik, vagy kifogásolhatónak, az IoT Edge-ügynök újraindítja.
   * Mindig – Ha a modul összeomlik, sikertelennek sérült, vagy bármilyen módon leáll, az IoT Edge-ügynök újraindítja. 

Az IoT Edge-ügynök futtatókörnyezeti válasz az IoT hub küldi. A következő lehetséges válaszok listáját:
  * 200 - OK
  * 400 – telepítési konfigurációja helytelen formátumú vagy érvénytelen.
  * 417 – az eszköz nem rendelkezik egy központi telepítési konfigurációt.
  * 412 – a telepítési konfigurációban a sémaverzió érvénytelen.
  * 406 – az IoT Edge-eszköz kapcsolat nélküli vagy nem küld állapotjelentéseket.
  * 500 – hiba történt az IoT Edge-futtatókörnyezet.

### <a name="security"></a>Biztonság

Az IoT Edge-ügynök kritikus szerepet játszik az IoT Edge-eszköz biztonságát. Ha például hajtja végre műveleteket, például egy modul rendszerképének ellenőrzése az újraindítás előtt. 

További információ az Azure IoT Edge biztonsági keretrendszert, olvassa el a [IoT Edge-biztonságkezelő](iot-edge-security-manager.md)

## <a name="next-steps"></a>További lépések

[Megismerheti az Azure IoT Edge-tanúsítványok](iot-edge-certs.md)
