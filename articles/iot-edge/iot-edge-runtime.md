---
title: Az Azure IoT peremhálózati futásidejű megértése |} Microsoft Docs
description: További tudnivalók az Azure IoT peremhálózati futásidejű, és hogyan lehetővé teszi az edge-eszközök
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: aa371ef2ebad01fba379675e8438f56dca9ce356
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096967"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Az Azure IoT peremhálózati futásidejű és az architektúra

Az IoT-Edge futásidejű programok, amelyek telepítve kell lennie ahhoz, hogy a tekinthető IoT peremhálózati eszköz egy eszközön gyűjteménye. IoT peremhálózati futásidejű összetevők együttesen, IoT peremeszközök a peremhálózaton futtatásához szükséges kódot fogadásához engedélyezze, és az eredmények kommunikációhoz. 

Az IoT-Edge futásidejű IoT peremhálózati eszközön hajtja végre a következő funkciókat:

* Telepíti és frissíti az eszközökön a számítási feladatokat.
* Fenntartja Azure IoT Edge biztonsági szabványait az eszközön.
* Biztosítja, hogy [IoT peremhálózati modulok][lnk-modulok] mindig futnak.
* Jelenti a modulok állapotát a felhőnek a távoli monitorozáshoz.
* Kommunikációt létesít az alárendelt levéleszközök és az IoT Edge-eszköz között.
* Kommunikációt létesít a modulok és az IoT Edge-eszköz között.
* Kommunikációt létesít az IoT Edge-eszközök és a felhő között.

![IoT peremhálózati futásidejű kommunikál az elemzések és az IoT-központ modul állapotát][1]

Az IoT-Edge futásidejű feladatai két kategóriába sorolhatók: modul kezelése és a kommunikáció. Ez a két szerepkör két összetevőből az IoT-Edge futásidejű alkotó hajtja végre. A peremhálózati IoT hub való kommunikációért felelős, amíg az IoT-Edge ügynök telepítését és megfigyelését a modulok kezeli. 

A peremhálózati ügynök és a peremhálózati hub egyaránt olyan modulok, csakúgy, mint bármely más, az IoT-peremhálózati eszközön futó modul. Modulok működésével kapcsolatos további információkért lásd: [lnk-modulok]. 

## <a name="iot-edge-hub"></a>IoT Edge hub

A peremhálózati hub a két Azure IoT peremhálózati futásidejű alkotó modulok egyike. Jelentkezik, mintha az IoT-központ azonos protokollvégpontokat IoT-központ helyi proxyjaként működik. A konzisztencia azt jelenti, hogy az ügyfelek (e eszközök vagy modulok) az IoT-Edge futásidejű kapcsolódhat, hogy az IoT hubhoz. 

>[!NOTE]
>Biztonsági központ MQTT vagy AMQP használatával csatlakozó ügyfelek támogatja. Nem támogatja a HTTP Protokollt használó ügyfeleket. 

A peremhálózati hub nincs helyben fut az IoT-központ teljes verzióját. Néhány dolgot IoT-központ csendes gazdakiszolgálója a peremhálózati központ. Például biztonsági központ továbbítja a hitelesítési kérelmeket az IoT hubhoz amikor egy eszköz először próbál csatlakozni. Az első kapcsolat létrejötte után biztonsági adatokat a helyi gyorsítótárba helyezi peremhálózati hubból. Az adott eszközről kapcsolataihoz engedélyezettek anélkül, hogy a felhő felé történő hitelesítésre. 

>[!NOTE]
>A futtatókörnyezet minden alkalommal, amikor egy eszköz hitelesítéséhez megkísérli csatlakoztatva kell lennie.

Az IoT-peremhálózati megoldás sávszélesség csökkentése érdekében használja, a peremhálózati hub optimalizálja hány tényleges kapcsolatok hozhatók létre a felhőbe. Biztonsági központ logikai kapcsolatok fogad az ügyfelektől, mint a modulok vagy levél eszközök, és a felhő egy egyetlen fizikai kapcsolat egyesíti őket. Ez a folyamat részletei a megoldás a többi átlátszóak. Ügyfelek gondolja, hogy saját kapcsolatukat a felhőbe rendelkeznek, annak ellenére, hogy azok összes küldési az azonos kapcsolaton keresztül. 

![Biztonsági központ átjáróként több fizikai eszköz és a felhő között][2]

Biztonsági központ meg tudja állapítani, hogy csatlakozik az IoT-központ. Ha megszakad a kapcsolat, a peremhálózati hub üzenetek vagy kettős frissítések helyi menti. Amennyiben a kapcsolat helyreállt, a szinkronizált adatok. Az ideiglenes gyorsítótár használt helyet a biztonsági központ modul iker tulajdonsága határozza meg. A gyorsítótár méretének nem fedett, és az eszköz tárkapacitása is nő. 

>[!NOTE]
>Ellenőrzése alatt tartja a gyorsítótárazási további paraméterek hozzáadásával hozzáadódik a termék előtt általánosan rendelkezésre álló lép.

### <a name="module-communication"></a>A modul kommunikáció

Biztonsági központ elősegíti a modult a modul kommunikációt. Biztonsági központ használatával üzenet közvetítőként tartja a modulok egymástól független. Modulok csak kell megadnia a bemenetek, amelyen elfogadják a üzenetek és a kimenetek, amelyhez a üzenetet írni. A megoldás fejlesztő ezután összefűzi a bemeneti adatokat, és kiírja együtt, hogy a modulok feldolgozni az adatokat, hogy a megoldáshoz megadott sorrendben. 

![Biztonsági központ modul-modul a kommunikáció lehetővé tétele.][3]

A peremhálózati hub adatokat küldeni, a modul meghívja a SendEventAsync metódust. Adja meg az első argumentum mely kimeneti elküldeni az üzenetet. A következő pseudocode output1 üzenetet küld:

   ```csharp
   DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Üzenet jelenik meg, regisztráljon egy visszahívást, amelyet az adott bevitel várható üzeneteket dolgozza fel. A következő pseudocode regisztrálja a függvény messageProcessor összes input1 fogadott üzenetek feldolgozásához használható:

   ```csharp
   await client.SetEventHandlerAsync(“input1”, messageProcessor, userContext);
   ```

A megoldás fejlesztője felelős a szabályok, amelyek meghatározzák, hogyan peremhálózati hub továbbítja modulok között üzenetek megadására. Útválasztási szabályokat a felhőben definiált és leküldeni az eszköz iker peremhálózati csomópontjában. Ugyanazt a szintaxist, az IoT-központ útvonalak az Azure IoT Edge modulok között útvonalak meghatározására szolgál. 

<!--- For more info on how to declare routes between modules, see []. --->   

![Útvonalak modulok között][4]

## <a name="iot-edge-agent"></a>Az IoT-Edge ügynök

Az IoT peremhálózati ügynök a modul, az Azure IoT peremhálózati futásidejű alkotó nem. Ez felelős a modulok példányának, győződjön meg arról, hogy továbbra is futni, és a modulok állapotának megkezdheti az IoT-központ. Csakúgy, mint bármely más modul a peremhálózati ügynök használatával a modul iker konfigurációs adatainak tárolásához. 

A peremhálózati ügynök végrehajtásának megkezdésére, futtassa az azure-iot-edge-futtatókörnyezet-ctl.py indítási parancsot. Az ügynök a modul iker lekéri az IoT-központ, és megvizsgálja a modulok szótárban. A modulok szótár nem kell elindítani modulok gyűjteménye. 

A modulok szótárban egyes elemek modul vonatkozó információkat tartalmaz, és van az ügynök által használt biztonsági a modul életciklus vezérlése. Az ennél is érdekesebb megoldást tulajdonságai vannak: 

* **Settings.Image** – a tároló lemezképet, a peremhálózati ügynök használja a modul elindításához. A peremhálózati ügynök tároló beállításjegyzék hitelesítő adatokkal kell konfigurálni, ha a kép jelszóval védett. A peremhálózati ügynök konfigurálásához módosítsa a `config.yaml` fájlt. A Linux használja a következő parancsot: `sudo nano /etc/iotedge/config.yaml`
* **settings.createOptions** – karakterlánc, amely közvetlenül átadódik a Docker démon a modul tároló indításakor. Ebben a tulajdonságban Docker beállítások hozzáadása lehetővé teszi a Speciális beállítások, például a továbbítási vagy kötetek csatlakoztatása egy modul tárolóba port.  
* **állapot** – pedig a peremhálózati ügynök helyezi el a modul állapotát. Ez általában értéke *futtató* , a legtöbben szeretné a peremhálózati ügynök azonnal elindítani az eszközön lévő összes modul. Azonban kell megadni egy modul kell állítani, és várja meg, hogy egy modul elindításához a peremhálózati ügynök később bármikor kezdeti állapotában. A peremhálózati ügynök jelent modulokhoz állapotának vissza a jelentett tulajdonságaiban a felhőben. A kívánt tulajdonság és a jelentett tulajdonság közötti különbség azt jelzi, hogy egy átirányítóban eszközt. A támogatott állapotok az alábbiak:
   * Letöltés folyamatban
   * Fut
   * Nem kifogástalan
   * Meghiúsult
   * Leállítva
* **restartPolicy** – hogyan a peremhálózati ügynök modul újraindul. A lehetséges értékek:
   * A peremhálózati ügynök sosem – soha nem indít a modult.
   * onFailure - Ha a modul összeomlik, a peremhálózati ügynök újraindítja. Ha a modul szabályszerűen leáll, a peremhálózati ügynök nem az újraindítást.
   * Nem kifogástalan állapotú - Ha a modul összeomlik vagy kifogásolhatónak, a peremhálózati ügynök újraindítja.
   * Mindig – Ha a modul összeomlik, akkor számít elértnek sérült, vagy bármely olyan módon leáll, a peremhálózati ügynök újraindítja. 

Az IoT-Edge ügynök IoT-központ futásidejű választ küld. Lehetséges válaszok listája itt található:
  * 200 - OK
  * 400 - telepítési konfigurációja helytelen formátumú vagy érvénytelen.
  * 417 – az eszköz nem rendelkezik a telepítési konfiguráció.
  * 412 – az a telepítési konfigurációban sémaverziója érvénytelen.
  * 406 – a kapcsolat nélküli vagy nem küldő állapotjelentések.
  * 500 - hiba történt az edge futtatókörnyezetben.

### <a name="security"></a>Biztonság

Az IoT-Edge ügynök kritikus szerepet játszik IoT peremhálózati eszköz biztonságát. Például hajtja végre műveletek, például egy modul kép ellenőrzése az újraindítás előtt. Ezek a funkciók általánosan rendelkezésre álló lesz hozzáadva. 

<!-- For more information about the Azure IoT Edge security framework, see []. -->

## <a name="next-steps"></a>További lépések

- [Azure IoT peremhálózati modulok megértéséhez][lnk-modulok]

<!-- Images -->
[1]: ./media/iot-edge-runtime/Pipeline.png
[2]: ./media/iot-edge-runtime/Gateway.png
[3]: ./media/iot-edge-runtime/ModuleEndpoints.png
[4]: ./media/iot-edge-runtime/ModuleEndpointsWithRoutes.png

<!-- Links -->
[lnk-modulok]: iot-edge-modules.md
