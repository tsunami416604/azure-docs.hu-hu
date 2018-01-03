---
title: "Az Azure IoT peremhálózati futásidejű megértése |} Microsoft Docs"
description: "További tudnivalók az Azure IoT peremhálózati futásidejű, és hogyan lehetővé teszi az edge-eszközök"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4727560df897f6c1a0aaa6d7f5d4e1c76fc02a46
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture---preview"></a>Az Azure IoT peremhálózati futásidejű és az architektúra – előzetes

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

## <a name="iot-edge-hub"></a>Peremhálózati IoT hub

A peremhálózati hub a két Azure IoT peremhálózati futásidejű alkotó modulok egyike. Jelentkezik, mintha az IoT-központ azonos protokollvégpontokat IoT-központ helyi proxyjaként működik. A konzisztencia azt jelenti, hogy az ügyfelek (e eszközök vagy modulok) az IoT-Edge futásidejű kapcsolódhat, hogy az IoT hubhoz. 

>[!NOTE]
> Nyilvános előzetes peremhálózati Hub MQTT használatával csatlakozó ügyfelek csak támogatja.

A peremhálózati hub nincs helyben fut az IoT-központ teljes verzióját. Néhány dolgot IoT-központ csendes gazdakiszolgálója a peremhálózati központ. Például biztonsági központ továbbítja a hitelesítési kérelmeket az IoT hubhoz amikor egy eszköz először próbál csatlakozni. Az első kapcsolat létrejötte után biztonsági adatokat a helyi gyorsítótárba helyezi peremhálózati hubból. Az adott eszközről kapcsolataihoz engedélyezettek anélkül, hogy a felhő felé történő hitelesítésre. 

>[!NOTE]
> Nyilvános előzetes a futtatókörnyezet csatlakoztatva kell lennie minden alkalommal, amikor megpróbálja egy eszköz hitelesítéséhez.

Az IoT-peremhálózati megoldás sávszélesség csökkentése érdekében használja, a peremhálózati hub optimalizálja hány tényleges kapcsolatok hozhatók létre a felhőbe. Biztonsági központ logikai kapcsolatok fogad az ügyfelektől, mint a modulok vagy levél eszközök, és a felhő egy egyetlen fizikai kapcsolat egyesíti őket. Ez a folyamat részletei a megoldás a többi átlátszóak. Ügyfelek gondolja, hogy saját kapcsolatukat a felhőbe rendelkeznek, annak ellenére, hogy azok összes küldési az azonos kapcsolaton keresztül. 

![Biztonsági központ átjáróként több fizikai eszköz és a felhő között][2]

Biztonsági központ meg tudja állapítani, hogy csatlakozik az IoT-központ. Ha megszakad a kapcsolat, a peremhálózati hub üzenetek vagy kettős frissítések helyi menti. Amennyiben a kapcsolat helyreállt, a szinkronizált adatok. Az ideiglenes gyorsítótár használt helyet a biztonsági központ modul iker tulajdonsága határozza meg. A gyorsítótár méretének nem fedett, és az eszköz tárkapacitása is nő. 

>[!NOTE]
>Ellenőrzése alatt tartja a gyorsítótárazási további paraméterek hozzáadásával hozzáadódik a termék előtt általánosan rendelkezésre álló lép.

### <a name="module-communication"></a>A modul kommunikáció

Biztonsági központ elősegíti a modult a modul kommunikációt. Biztonsági központ használatával üzenet közvetítőként tartja a modulok egymástól független. Modulok csak kell megadnia a bemenetek, amelyen elfogadják a üzenetek és a kimenetek, amelyhez a üzenetet írni. A megoldás fejlesztő ezután összefűzi a bemeneti adatokat, és kiírja együtt, hogy a modulok feldolgozni az adatokat, hogy a megoldáshoz megadott sorrendben. 

![Biztonsági központ modul-modul a kommunikáció lehetővé tétele.][3]

A peremhálózati hub adatokat küldeni, a modul meghívja a SendEventAsync metódust. Adja meg az első argumentum mely kimeneti elküldeni az üzenetet. A következő pseudocode output1 üzenetet küld:

    DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
    await client.OpenAsync(); 
    await client.SendEventAsync(“output1”, message); 

Üzenet jelenik meg, regisztráljon egy visszahívást, amelyet az adott bevitel várható üzeneteket dolgozza fel. A következő pseudocode regisztrálja a függvény messageProcessor összes input1 fogadott üzenetek feldolgozásához használható:

    await client.SetEventHandlerAsync(“input1”, messageProcessor, userContext);
    
A megoldás fejlesztője felelős a szabályok, amelyek meghatározzák, hogyan peremhálózati hub továbbítja modulok között üzenetek megadására. Útválasztási szabályokat a felhőben definiált és leküldeni az eszköz iker peremhálózati csomópontjában. Ugyanazt a szintaxist, az IoT-központ útvonalak az Azure IoT Edge modulok között útvonalak meghatározására szolgál. 

<!--- For more info on how to declare routes between modules, see []. --->   

![Útvonalak modulok között][4]

## <a name="iot-edge-agent"></a>Az IoT-Edge ügynök

Az IoT peremhálózati ügynök a modul, az Azure IoT peremhálózati futásidejű alkotó nem. Ez felelős a modulok példányának, győződjön meg arról, hogy továbbra is futni, és a modulok állapotának megkezdheti az IoT-központ. Csakúgy, mint bármely más modul a peremhálózati ügynök használatával a modul iker konfigurációs adatainak tárolásához. 

A peremhálózati ügynök végrehajtásának megkezdésére, futtassa az azure-iot-edge-futtatókörnyezet-ctl.py indítási parancsot. Az ügynök a modul iker lekéri az IoT-központ, és megvizsgálja a modulok szótárban. A modulok szótár nem kell elindítani modulok gyűjteménye. 

A modulok szótárban egyes elemek modul vonatkozó információkat tartalmaz, és van az ügynök által használt biztonsági a modul életciklus vezérlése. Az ennél is érdekesebb megoldást tulajdonságai vannak: 

* **Settings.Image** – a tároló lemezképet, a peremhálózati ügynök használja a modul elindításához. A peremhálózati ügynök tároló beállításjegyzék hitelesítő adatokkal kell konfigurálni, ha a kép jelszóval védett. A peremhálózati ügynök, használja a következő parancsot:`azure-iot-edge-runtime-ctl.py –configure`
* **settings.createOptions** – karakterlánc, amely közvetlenül átadódik a Docker démon a modul tároló indításakor. Ebben a tulajdonságban Docker beállítások hozzáadása lehetővé teszi a Speciális beállítások, például a továbbítási vagy kötetek csatlakoztatása egy modul tárolóba port.  
* **állapot** – pedig a peremhálózati ügynök helyezi el a modul állapotát. Ez általában értéke *futtató* , a legtöbben szeretné a peremhálózati ügynök azonnal elindítani az eszközön lévő összes modul. Azonban kell megadni egy modul kell állítani, és várja meg, hogy egy modul elindításához a peremhálózati ügynök később bármikor kezdeti állapotában. A peremhálózati ügynök jelent modulokhoz állapotának vissza a jelentett tulajdonságaiban a felhőben. A kívánt tulajdonságot és a jelentett tulajdonság egy kijelző vagy átirányítóban eszköz. A támogatott állapotok az alábbiak:
   * Letöltés
   * Fut
   * Nem kifogástalan
   * Sikertelen
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

Az IoT-Edge ügynök kritikus szerepet játszik IoT peremhálózati eszköz biztonságát. Például hajtja végre műveletek, például egy modul kép ellenőrzése az újraindítás előtt. Ezek a funkciók általánosan rendelkezésre álló V2 funkciók lesz hozzáadva. 

<!-- For more information about the Azure IoT Edge security framework, see []. -->

## <a name="next-steps"></a>További lépések

- [Azure IoT peremhálózati modulok megértéséhez][lnk-modulok]

<!-- Images -->
[1]: ./media/iot-edge-runtime/pipeline.png
[2]: ./media/iot-edge-runtime/gateway.png
[3]: ./media/iot-edge-runtime/ModuleEndpoints.png
[4]: ./media/iot-edge-runtime/ModuleEndpointsWithRoutes.png

<!-- Links -->
[lnk-modulok]: iot-edge-modules.md