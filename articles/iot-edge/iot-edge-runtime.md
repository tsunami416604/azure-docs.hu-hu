---
title: Ismerje meg, hogyan kezeli a a modul az Azure IoT Edge-eszközök – |} A Microsoft Docs
description: Ismerje meg, hogyan kezeli a IoT Edge Runtime a modulokat, a biztonságot, a kommunikációt és a jelentéskészítést az eszközökön
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4bdf496995e8b466f1346bfe16365b251c6853c3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076059"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Az Azure IoT Edge-futtatókörnyezet és architektúrájának ismertetése

A IoT Edge Runtime olyan programok gyűjteménye, amelyek egy eszközt IoT Edge eszközre kapcsolnak be. Együttesen a IoT Edge futtatókörnyezet-összetevők lehetővé teszik, hogy IoT Edge eszközök megkapják a kód futtatását a peremhálózat szélén, és közlik az eredményeket. 

A IoT Edge futtatókörnyezet felelős a következő függvényekért IoT Edge eszközökön:

* A számítási feladatok telepítése és frissítése az eszközön.
* Azure IoT Edge biztonsági szabványok fenntartása az eszközön.
* Győződjön meg arról, hogy [IoT Edge modulok](iot-edge-modules.md) mindig futnak.
* Jelentési modul állapota a felhőben távoli figyeléshez.
* Az alsóbb rétegbeli eszközök és IoT Edge eszközök közötti kommunikáció kezelése.
* A IoT Edge eszközön található modulok közötti kommunikáció kezelése.
* Kezelheti a IoT Edge eszköz és a felhő közötti kommunikációt.

![Modul kommunikál az elemzések és a modulok állapotát az IoT hubhoz](./media/iot-edge-runtime/Pipeline.png)

Az IoT Edge-futtatókörnyezet feladatai két kategóriába sorolhatók: kommunikációs és modul-kezelés. Ezt a két szerepkört két, a IoT Edge futtatókörnyezet részét képező összetevő hajtja végre. Az *IoT Edge hub* a kommunikációért felelős, míg a *IoT Edge-ügynök* üzembe helyezi és figyeli a modulokat. 

Az IoT Edge hub és a IoT Edge-ügynök is a modulok, ugyanúgy, mint bármely más, IoT Edge eszközön futó modulhoz. Más néven *futtatókörnyezeti modulok*. 

## <a name="iot-edge-hub"></a>IoT Edge hub

Az IoT Edge hub a Azure IoT Edge futtatókörnyezetet alkotó két modul egyike. Azt proxyként funkcionál, helyi az IoT Hub, IoT Hub azonos protokollvégpontokat teszi elérhetővé. A konzisztencia azt jelenti, hogy az ügyfelek (e eszközök vagy modulok) is csatlakozhat az IoT Edge-futtatókörnyezet, ugyanúgy, mint az IoT hubhoz. 

>[!NOTE]
> IoT Edge hub a MQTT vagy a AMQP használatával csatlakozó ügyfeleket támogatja. Nem támogatja a HTTP Protokollt használó ügyfeleket. 

Az IoT Edge hub nem a helyileg futó IoT Hub teljes verziója. Van néhány dolog, amit a IoT Edge hub csendesen delegál IoT Hubnak. Például IoT Edge hub továbbítja a hitelesítési kéréseket IoT Hub, amikor egy eszköz először próbál csatlakozni. Az első összekapcsolást követően a biztonsági adatokat IoT Edge hub helyileg gyorsítótárazza. Kapcsolatait, erről az eszközről a felhőbe való hitelesítéséhez nélkül engedélyezve. 

A IoT Edge-megoldás által használt sávszélesség csökkentése érdekében a IoT Edge hub azt optimalizálja, hogy hány tényleges kapcsolat legyen elérhető a felhőben. IoT Edge hub logikai kapcsolatokat fogad az ügyfelektől, például modulokból vagy alsóbb rétegbeli eszközökről, és egyetlen fizikai kapcsolattal kombinálja őket a felhőhöz. Ez a folyamat részletei transzparensek a megoldás többi részétől. Az ügyfelek úgy gondolja, hogy hozzáadhatják saját kapcsolatukat a felhőbe rendelkeznek, annak ellenére, hogy azok minden távozási ugyanazon a kapcsolaton keresztül. 

![IoT Edge hub a fizikai eszközök és a IoT Hub közötti átjáró.](./media/iot-edge-runtime/Gateway.png)

IoT Edge hub képes megállapítani, hogy csatlakozik-e IoT Hubhoz. Ha a kapcsolat megszakad, IoT Edge hub üzeneteket ment helyileg, vagy két különálló frissítést. Miután a kapcsolat helyreállt, a szinkronizált összes adatot. Az ehhez az ideiglenes gyorsítótárhoz használt helyet az IoT Edge hub-modul Twin-moduljának egyik tulajdonsága határozza meg. A gyorsítótár méretét nem maximumon, és az eszköz tárkapacitása is növekszik. További információ: [Offline képességek](offline-capabilities.md).

### <a name="module-communication"></a>A modul kommunikáció

IoT Edge hub megkönnyíti a modul kommunikációját. IoT Edge hub használata az üzenetsor-átvitelszervezőként a modulok egymástól függetlenek maradnak. Modulok csak adja meg, amelyen a üzenetek és a kimeneteket, amelyhez a üzeneteket írhat elfogadják a bemeneteket kell. A megoldás fejlesztője összevonhatja ezeket a bemeneteket és kimeneteket, így a modulok az adott megoldáshoz tartozó sorrendben dolgozzák fel az adatokat. 

![IoT Edge hub elősegíti a modul – modul kommunikációt](./media/iot-edge-runtime/module-endpoints.png)

Az IoT Edge hubhoz való adatküldéshez egy modul hívja meg a SendEventAsync metódust. Az első argumentum meghatározza a milyen kimenetet az üzenet elküldéséhez. A következő pseudocode üzenetet küld a **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync("output1", message); 
   ```

Egy üzenet jelenik meg, hogy regisztráljon egy visszahívást, amelyet a megadott bemeneti érkező üzeneteket dolgoz fel. A következő pseudocode regisztrálja a **input1**fogadott összes üzenet feldolgozásához használni kívánt messageProcessor-függvényt:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

A ModuleClient osztályról és a hozzá tartozó kommunikációs módszerekről a következő témakörben talál további információt: API-hivatkozás [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)az előnyben részesített SDK-nyelvhez:, [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)vagy [Node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

A megoldás fejlesztői feladata azoknak a szabályoknak a meghatározása, amelyek meghatározzák, hogy IoT Edge hub hogyan továbbít üzeneteket a modulok között. Az útválasztási szabályok a felhőben vannak meghatározva, és leküldve IoT Edge hubhoz a moduljában. Ugyanazt a szintaxist, az IoT Hub útvonalakat az Azure IoT Edge moduljai közötti útvonalak meghatározására szolgál. További információkért lásd: [modulok központi telepítése és útvonalak létrehozása IoT Edgeban](module-composition.md).   

![A modulok közötti útvonalak IoT Edge hub-on keresztül haladnak](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge-ügynök

Az IoT Edge-ügynök a többi modul, amely az Azure IoT Edge-futtatókörnyezet. Ez felelős modulok hárítható el, biztosítva, hogy továbbra is futni és a modulok állapotát megkezdheti az IoT Hub. Ezek a konfigurációs adatkészletek a IoT Edge Agent modul Twin. 

A [IoT Edge biztonsági démon](iot-edge-security-manager.md) elindítja a IoT Edge ügynököt az eszköz indításakor. Az ügynök az ikermodul lekéri az IoT hubról, és megvizsgálja a manifest nasazení. Manifest nasazení egy JSON-fájlt, amely deklarálja a modulokat, amelyek kell elindítani. 

Az üzembe helyezési jegyzék minden eleme tartalmaz konkrét információkat egy modulról, és a IoT Edge ügynök használja a modul életciklusának szabályozására. Az érdekesebb tulajdonságok a következők: 

* **Settings. rendszerkép** – a IoT Edge ügynök által a modul elindításához használt tároló képe. A IoT Edge-ügynököt a tároló-beállításjegyzék hitelesítő adataival kell konfigurálni, ha a képet jelszó védi. A tároló-beállításjegyzék hitelesítő adatai távolról konfigurálhatók a telepítési jegyzékfájl használatával vagy maga a IoT Edge eszközön a IoT Edge program mappájában található `config.yaml` fájl frissítésével.
* **Settings. createOptions** – a modul tárolójának indításakor közvetlenül a Moby Container Daemon-hoz átadott karakterlánc. Az ebben a tulajdonságban található beállítások megadása lehetővé teszi olyan speciális konfigurációk használatát, mint a port továbbítása vagy a kötetek csatlakoztatása egy modul tárolójába.  
* **status (állapot** ) – az a állapot, amelyben a IoT Edge ügynök elhelyezi a modult. Ez az érték általában a *futtatásra* van beállítva, mivel a legtöbb felhasználó szeretné, hogy a IoT Edge ügynök azonnal elindítsa az összes modult az eszközön. Azonban megadhatja a leállítani kívánt modul kezdeti állapotát, és megvárhatja, hogy a jövőbeli idő alapján a IoT Edge ügynök elindítson egy modult. A IoT Edge ügynök az egyes modulok állapotát a felhőbe jelenti a jelentett tulajdonságok között. Különbség a kívánt tulajdonság és a jelentett tulajdonság azt jelzi, hogy a rosszul eszköz. A támogatott állapotok a következők:
   * Letöltés folyamatban
   * Fut
   * Nem kifogástalan
   * Meghiúsult
   * Leállítva
* **restartPolicy** – azt, hogy a IoT Edge ügynök hogyan indítja újra a modult. A lehetséges értékek:
   * `never` – a IoT Edge ügynök soha nem indítja újra a modult.
   * `on-failure` – ha a modul összeomlik, a IoT Edge ügynök újraindul. Ha a modul tisztaan leáll, a IoT Edge ügynök nem indítja újra.
   * `on-unhealthy` – ha a modul összeomlik vagy nem kifogástalannak minősül, a IoT Edge-ügynök újraindul.
   * `always` – ha a modul összeomlik, nem megfelelő állapotba kerül, vagy bármilyen módon leáll, a IoT Edge-ügynök újraindul. 
* **imagePullPolicy** – azt határozza meg, hogy a IoT Edge ügynök automatikusan megpróbálja-e lekérni a modul legújabb rendszerképét. Ha nem ad meg értéket, az alapértelmezett érték a *onCreate*. A lehetséges értékek: 
   * `on-create` – ha egy modul indításakor vagy egy új üzembe helyezési jegyzék alapján frissít egy modult, akkor a IoT Edge ügynök megpróbálja lekérni a modul rendszerképét a tároló beállításjegyzékből.
   * `never` – a IoT Edge ügynök soha nem kísérli meg a modul rendszerképének lekérését a tároló-beállításjegyzékből. A várt érték az, hogy a rendszer a modul rendszerképét gyorsítótárazza az eszközön, és a modulok rendszerképének frissítéseit manuálisan vagy egy harmadik féltől származó megoldás kezeli. 

Az IoT Edge-ügynök futtatókörnyezeti válasz az IoT hub küldi. A következő lehetséges válaszok listáját:
  * 200 - OK
  * 400 – telepítési konfigurációja helytelen formátumú vagy érvénytelen.
  * 417 – az eszköz nem rendelkezik telepítési konfigurációs készlettel.
  * 412 – a telepítési konfigurációban a sémaverzió érvénytelen.
  * 406 – a IoT Edge eszköz offline állapotban van, vagy nem küld állapotjelentéseket.
  * 500 – hiba történt a IoT Edge futtatókörnyezetben.

További információkért lásd: [modulok központi telepítése és útvonalak létrehozása IoT Edgeban](module-composition.md).   

### <a name="security"></a>Biztonság

Az IoT Edge-ügynök kritikus szerepet játszik az IoT Edge-eszköz biztonságát. Ha például hajtja végre műveleteket, például egy modul rendszerképének ellenőrzése az újraindítás előtt. 

A Azure IoT Edge biztonsági keretrendszerével kapcsolatos további információkért olvassa el a [IoT Edge Security Manager](iot-edge-security-manager.md)című témakört.

## <a name="next-steps"></a>További lépések

[Az Azure IoT Edge-modulok megismerése](iot-edge-modules.md)
