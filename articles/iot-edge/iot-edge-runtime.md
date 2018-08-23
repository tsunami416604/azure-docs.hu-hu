---
title: Megismerheti az Azure IoT Edge-futtatókörnyezet |} A Microsoft Docs
description: Ismerje meg az Azure IoT Edge-futtatókörnyezet, és hogyan lehetővé teszi a peremhálózati eszközökre
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 08/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f832b05969c028880f6e375ff4a2ee8dc7a7eaf4
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "42054081"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Az Azure IoT Edge-futtatókörnyezet és architektúrájának ismertetése

Az IoT Edge-futtatókörnyezet, amely szükség van ahhoz, hogy figyelembe kell venni az IoT Edge-eszköz egy eszközön telepített programok gyűjteménye. Együttesen az IoT Edge-futtatókörnyezet összetevői kattintva kérhet kódot futtatása a peremhálózaton IoT Edge-eszközök és a kommunikációhoz az eredményeket. 

Az IoT Edge-futtatókörnyezet az IoT Edge-eszközökön a következő funkciókat hajtja végre:

* Telepíti és frissíti az eszközökön a számítási feladatokat.
* Fenntartja Azure IoT Edge biztonsági szabványait az eszközön.
* Biztosítja, hogy [IoT Edge-modulok] [ lnk-modules] folyamatos futását.
* Jelenti a modulok állapotát a felhőnek a távoli monitorozáshoz.
* Kommunikációt létesít alárendelt levéleszközök és az IoT Edge-eszközök között.
* Kommunikációt létesít a modulok és az IoT Edge-eszköz között.
* Kommunikációt létesít az IoT Edge-eszközök és a felhő között.

![IoT Edge-futtatókörnyezet elemzéseket és a modulok állapotát az IoT hub kommunikál][1]

Az IoT Edge-futtatókörnyezet feladatai két kategóriába sorolhatók: modul felügyeleti és kommunikációs. E két szerepkör két összetevőből, az IoT Edge-futtatókörnyezet végzi. Az IoT Edge hubot felelős közötti kommunikáció, míg az IoT Edge-ügynök üzembe helyezése és figyelése a modulok kezeli. 

Az Edge agent és az Edge hub is olyan modulok, csakúgy, mint egy IoT Edge-eszközön futó bármely egyéb modult. 

## <a name="iot-edge-hub"></a>IoT Edge hub

Az Edge hub két, az Azure IoT Edge-futtatókörnyezet alkotó modulok egyike. Azt proxyként funkcionál, helyi az IoT Hub, IoT Hub azonos protokollvégpontokat teszi elérhetővé. A konzisztencia azt jelenti, hogy az ügyfelek (e eszközök vagy modulok) is csatlakozhat az IoT Edge-futtatókörnyezet, ugyanúgy, mint az IoT hubhoz. 

>[!NOTE]
>Edge Hub az MQTT vagy AMQP használatával csatlakozó ügyfelek támogatja. Nem támogatja a HTTP Protokollt használó ügyfeleket. 

Az Edge hub nem áll a helyileg futó IoT Hub teljes verzióját. Néhány dolog, amely csendes delegálja az Edge hub az IoT hubnak. Például Edge hubot továbbítja a hitelesítési kérések az IoT hubhoz, ha egy eszköz először megpróbálja való csatlakozáshoz. Az első kapcsolat létrejötte után biztonsági adatokat a helyi gyorsítótárba Edge hub által. Kapcsolatait, erről az eszközről a felhőbe való hitelesítéséhez nélkül engedélyezve. 

>[!NOTE]
>A runtime csatlakoztatva kell lennie minden alkalommal, amikor egy eszköz hitelesítéséhez próbálkozik.

Az IoT Edge-megoldás sávszélesség csökkentése érdekében használja, az Edge hub optimalizálja hány tényleges kapcsolatok hozhatók létre a felhőben. Edge hubot felveszi azt a logikai kapcsolatok ügyfelek, mint a modulok vagy levéleszközök és egyesíti azokat a felhőbe egyetlen fizikai kapcsolathoz. Ez a folyamat részletei transzparensek a megoldás többi részétől. Az ügyfelek úgy gondolja, hogy hozzáadhatják saját kapcsolatukat a felhőbe rendelkeznek, annak ellenére, hogy azok minden távozási ugyanazon a kapcsolaton keresztül. 

![Edge hubot átjáróként működik, több fizikai eszköz és a felhő között][2]

Edge hubot megállapíthatja, hogy csatlakozik az IoT Hub. Ha megszakad a kapcsolat, Edge hubot menti az üzenetek vagy a helyi ikereszköz-frissítések. Miután a kapcsolat helyreállt, a szinkronizált összes adatot. Az átmeneti gyorsítótár használt helyet az Edge hub ikermodul tulajdonsága határozza meg. A gyorsítótár méretét nem maximumon, és az eszköz tárkapacitása is növekszik. 

### <a name="module-communication"></a>A modul kommunikáció

Edge hubot modult a modul kommunikációt létesít. Használja az Edge Hub üzenetközvetítőként tartja a modulok egymástól független. Modulok csak adja meg, amelyen a üzenetek és a kimeneteket, amelyhez a üzeneteket írhat elfogadják a bemeneteket kell. A megoldás fejlesztő ezután összefűzi ezeket a bemeneteket, és kiírja együtt, hogy a modulok a rendelés megoldásra jellemző adatok feldolgozása. 

![Edge hubot modul-modul kommunikációt létesít][3]

Az Edge hub adatokat küldeni, a modul meghívja a SendEventAsync metódust. Az első argumentum meghatározza a milyen kimenetet az üzenet elküldéséhez. A következő pseudocode output1 üzenetet küld:

   ```csharp
   ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Egy üzenet jelenik meg, hogy regisztráljon egy visszahívást, amelyet a megadott bemeneti érkező üzeneteket dolgoz fel. A következő pseudocode regisztrálja a függvény messageProcessor input1 fogadott összes üzenetek feldolgozására használható:

   ```csharp
   await client.SetEventHandlerAsync(“input1”, messageProcessor, userContext);
   ```

A megoldás fejlesztő feladata a szabályok, amelyek meghatározzák, hogyan Edge hubot továbbítja moduljai közötti üzenetek megadására. Útválasztási szabályok meghatározva a felhőben, és leküldte a az Edge hub az ikereszközön. Ugyanazt a szintaxist, az IoT Hub útvonalakat az Azure IoT Edge moduljai közötti útvonalak meghatározására szolgál. 

<!--- For more info on how to declare routes between modules, see []. --->   

![Útvonalak modulok között][4]

## <a name="iot-edge-agent"></a>IoT Edge-ügynök

Az IoT Edge-ügynök a többi modul, amely az Azure IoT Edge-futtatókörnyezet. Ez felelős modulok hárítható el, biztosítva, hogy továbbra is futni és a modulok állapotát megkezdheti az IoT Hub. Csakúgy, mint bármely egyéb modult az Edge agent az ikermodul használja a konfigurációs adatok tárolására. 

A [IoT Edge biztonsági démon](iot-edge-security-manager.md) az Edge agent elindul az eszköz indításakor. Az ügynök az ikermodul lekéri az IoT hubról, és megvizsgálja a manifest nasazení. Manifest nasazení egy JSON-fájlt, amely deklarálja a modulokat, amelyek kell elindítani. 

Manifest nasazení lévő egyes elemek modul kapcsolatos információkat tartalmaz, és a modul életciklus szabályozni az Edge agent használják. Az érdekesebb tulajdonságok a következők: 

* **Settings.Image** – a tároló rendszerképét, amely a modul indításához használja az Edge agent. Ha a kép jelszó védi az Edge agent kell konfigurálni a container Registry hitelesítő adataival. Hitelesítő adatok a tároló-beállításjegyzék konfigurálható manifest nasazení segítségével távolról, vagy maga az Edge-eszköz frissítésével a `config.yaml` az IoT Edge program mappában található fájl.
* **settings.createOptions** – egy karakterlánc, amely az átadott közvetlenül a Docker-démon indítása egy modul tároló esetén. Ezt a tulajdonságot a Docker-beállítások hozzáadása lehetővé teszi a speciális beállításokat, például a továbbítási vagy kötetek csatlakoztatása egy modul tárolóba port.  
* **állapot** – az Edge agent helyezi el a modul állapota. Ez az érték beállítása általában *futó* , a legtöbb ember szeretné azonnal elindítani az eszközön lévő összes modult az Edge agent. Azonban megadhatja a modul le kell állítani, és várja meg, hogy az Edge agent modul indítása egy jövőbeli időpontot kezdeti állapota. Az Edge agent visszajelzést az egyes modulok állapotát a jelentett tulajdonságok a felhőbe. Különbség a kívánt tulajdonság és a jelentett tulajdonság azt jelzi, hogy a rosszul eszköz. A támogatott állapotok a következők:
   * Letöltés folyamatban
   * Fut
   * Nem kifogástalan
   * Meghiúsult
   * Leállítva
* **restartPolicy** – az Edge agent modul újraindul. A lehetséges értékek:
   * Soha nem – az Edge agent újraindítja a modul soha nem.
   * onFailure – Ha a modul összeomlik, az Edge agent újraindítja. Ha a modul szabályszerűen álljon le, az Edge agent nem indítja újra azt.
   * Nem megfelelő – Ha a modul összeomlik, vagy kifogásolhatónak, az Edge agent újraindítja.
   * Mindig – Ha a modul összeomlik, sikertelennek sérült, vagy bármilyen módon leáll, az Edge agent újraindítja. 

Az IoT Edge-ügynök futtatókörnyezeti válasz az IoT hub küldi. A következő lehetséges válaszok listáját:
  * 200 - OK
  * 400 – telepítési konfigurációja helytelen formátumú vagy érvénytelen.
  * 417 – az eszköz nem rendelkezik egy központi telepítési konfigurációt.
  * 412 – a telepítési konfigurációban a sémaverzió érvénytelen.
  * 406 – az edge-eszköz kapcsolat nélküli vagy nem küld állapotjelentéseket.
  * 500 – hiba történt az edge-futtatókörnyezet.

### <a name="security"></a>Biztonság

Az IoT Edge-ügynök kritikus szerepet játszik az IoT Edge-eszköz biztonságát. Ha például hajtja végre műveleteket, például egy modul rendszerképének ellenőrzése az újraindítás előtt. 

További információ az Azure IoT Edge biztonsági keretrendszert, olvassa el a [IoT Edge-biztonságkezelő](iot-edge-security-manager.md)

## <a name="next-steps"></a>További lépések

[Az Azure IoT Edge-modulok megismerése][lnk-modules]

<!-- Images -->
[1]: ./media/iot-edge-runtime/Pipeline.png
[2]: ./media/iot-edge-runtime/Gateway.png
[3]: ./media/iot-edge-runtime/ModuleEndpoints.png
[4]: ./media/iot-edge-runtime/ModuleEndpointsWithRoutes.png

<!-- Links -->
[lnk-modules]: iot-edge-modules.md
