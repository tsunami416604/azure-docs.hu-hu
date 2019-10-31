---
title: Ismerje meg, hogyan kezeli a futtatókörnyezet az eszközöket – Azure IoT Edge | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az eszközökön a modulokat, a biztonságot, a kommunikációt és a jelentéskészítést az Azure IoT Edge futtatókörnyezet
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 49abd9e5ecee8637d830604028463650071c0198
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163161"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>A Azure IoT Edge futtatókörnyezet és az architektúrájának ismertetése

A IoT Edge Runtime olyan programok gyűjteménye, amelyek egy eszközt IoT Edge eszközre kapcsolnak be. Együttesen a IoT Edge futásidejű összetevői lehetővé teszik, hogy IoT Edge eszközök megkapják a kódot a peremhálózati futtatáshoz, és közlik az eredményeket. 

A IoT Edge Runtime a következő függvényeket hajtja végre IoT Edge eszközökön:

* A számítási feladatok telepítése és frissítése az eszközön.
* Azure IoT Edge biztonsági szabványok fenntartása az eszközön.
* Győződjön meg arról, hogy [IoT Edge modulok](iot-edge-modules.md) mindig futnak.
* Jelentési modul állapota a felhőben távoli figyeléshez.
* Az alárendelt levelek eszközeinek és IoT Edge eszközök közötti kommunikáció elősegítése.
* A IoT Edge eszközön található modulok közötti kommunikáció elősegítése.
* A IoT Edge eszköz és a felhő közötti kommunikáció elősegítése.

![A Runtime a bepillantást és a modul állapotát közli IoT Hub](./media/iot-edge-runtime/Pipeline.png)

A IoT Edge futtatókörnyezet feladatai két kategóriába sorolhatók: a kommunikáció és a modul kezelése. Ezt a két szerepkört két, a IoT Edge futtatókörnyezet részét képező összetevő hajtja végre. Az *IoT Edge hub* a kommunikációért felelős, míg a *IoT Edge-ügynök* üzembe helyezi és figyeli a modulokat. 

Az IoT Edge hub és a IoT Edge-ügynök is a modulok, ugyanúgy, mint bármely más, IoT Edge eszközön futó modulhoz. 

## <a name="iot-edge-hub"></a>IoT Edge hub

Az IoT Edge hub a Azure IoT Edge futtatókörnyezetet alkotó két modul egyike. A IoT Hub helyi proxyként működik, ha a protokoll-végpontokat IoT Hubként teszi elérhetővé. Ez a konzisztencia azt jelenti, hogy az ügyfelek (függetlenül attól, hogy az eszközök vagy modulok) képesek-e csatlakozni a IoT Edge futtatókörnyezethez ugyanúgy, ahogy IoT Hub. 

>[!NOTE]
> IoT Edge hub a MQTT vagy a AMQP használatával csatlakozó ügyfeleket támogatja. Nem támogatja a HTTP protokollt használó ügyfeleket. 

Az IoT Edge hub nem a helyileg futó IoT Hub teljes verziója. Van néhány dolog, amit a IoT Edge hub csendesen delegál IoT Hubnak. Például IoT Edge hub továbbítja a hitelesítési kéréseket IoT Hub, amikor egy eszköz először próbál csatlakozni. Az első összekapcsolást követően a biztonsági adatokat IoT Edge hub helyileg gyorsítótárazza. Az eszközről érkező további kapcsolatok engedélyezése nem szükséges a felhőben való hitelesítéshez. 

A IoT Edge-megoldás által használt sávszélesség csökkentése érdekében a IoT Edge hub azt optimalizálja, hogy hány tényleges kapcsolat legyen elérhető a felhőben. IoT Edge hub logikai kapcsolatokat fogad az ügyfelektől, például a modulokból vagy a levelekből származó eszközökről, és egyetlen fizikai kapcsolattal ötvözi a felhőt. A folyamat részletei transzparensek a megoldás többi részén. Az ügyfelek úgy gondolják, hogy saját kapcsolattal rendelkeznek a felhővel, annak ellenére, hogy mindegyiket ugyanazon a kapcsolaton keresztül küldik el. 

![IoT Edge hub a fizikai eszközök és a IoT Hub közötti átjáró.](./media/iot-edge-runtime/Gateway.png)

IoT Edge hub képes megállapítani, hogy csatlakozik-e IoT Hubhoz. Ha a kapcsolat megszakad, IoT Edge hub üzeneteket ment helyileg, vagy két különálló frissítést. A kapcsolatok újbóli létrehozása után szinkronizálja az összes adatforrást. Az ehhez az ideiglenes gyorsítótárhoz használt helyet az IoT Edge hub-modul Twin-moduljának egyik tulajdonsága határozza meg. A gyorsítótár mérete nem megengedett, és addig nő, amíg az eszköz tárolókapacitással rendelkezik. További információ: [Offline képességek](offline-capabilities.md).

### <a name="module-communication"></a>Modul kommunikációja

IoT Edge hub megkönnyíti a modul kommunikációját. IoT Edge hub használata az üzenetsor-átvitelszervezőként a modulok egymástól függetlenek maradnak. A moduloknak csak azokat a bemeneteket kell megadniuk, amelyeken üzeneteket fogadnak, valamint azokat a kimeneteket, amelyekhez üzeneteket írnak. A megoldás fejlesztője összevonhatja ezeket a bemeneteket és kimeneteket, így a modulok az adott megoldáshoz tartozó sorrendben dolgozzák fel az adatokat. 

![IoT Edge hub elősegíti a modul – modul kommunikációt](./media/iot-edge-runtime/module-endpoints.png)

Az IoT Edge hubhoz való adatküldéshez egy modul hívja meg a SendEventAsync metódust. Az első argumentum azt adja meg, hogy melyik kimenetre kell elküldeni az üzenetet. A következő pseudocode üzenetet küld a **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync("output1", message); 
   ```

Ha üzenetet szeretne kapni, regisztráljon egy olyan visszahívást, amely egy adott bemeneten lévő üzeneteket dolgoz fel. A következő pseudocode regisztrálja a **input1**fogadott összes üzenet feldolgozásához használni kívánt messageProcessor-függvényt:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

A ModuleClient osztályról és a hozzá tartozó kommunikációs módszerekről a következő témakörben talál további információt: API-hivatkozás [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)az előnyben részesített SDK-nyelvhez:, [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)vagy [Node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

A megoldás fejlesztői feladata azoknak a szabályoknak a meghatározása, amelyek meghatározzák, hogy IoT Edge hub hogyan továbbít üzeneteket a modulok között. Az útválasztási szabályok a felhőben vannak meghatározva, és a IoT Edge hubhoz lesznek leküldve az eszköz Twin. A IoT Hub útvonalakhoz tartozó szintaxis a Azure IoT Edge moduljai közötti útvonalak definiálására szolgál. További információkért lásd: [modulok központi telepítése és útvonalak létrehozása IoT Edgeban](module-composition.md).   

![A modulok közötti útvonalak IoT Edge hub-on keresztül haladnak](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge ügynök

A IoT Edge ügynök az a másik modul, amely a Azure IoT Edge futtatókörnyezetet hozza létre. A modulok létrehozásához, valamint a modulok állapotának a IoT Hub való visszajelentéséhez felelős. Ugyanúgy, mint bármely más modulhoz, a IoT Edge-ügynök a modulját használja a konfigurációs adattároláshoz. 

A [IoT Edge biztonsági démon](iot-edge-security-manager.md) elindítja a IoT Edge ügynököt az eszköz indításakor. Az ügynök lekéri a modult a IoT Hub, és megvizsgálja az üzembe helyezési jegyzéket. Az üzembe helyezési jegyzékfájl egy olyan JSON-fájl, amely deklarálja az elindításához szükséges modulokat. 

Az üzembe helyezési jegyzék minden eleme tartalmaz konkrét információkat egy modulról, és a IoT Edge ügynök használja a modul életciklusának szabályozására. A további érdekes tulajdonságok a következők: 

* **Settings. rendszerkép** – a IoT Edge ügynök által a modul elindításához használt tároló képe. A IoT Edge-ügynököt a tároló-beállításjegyzék hitelesítő adataival kell konfigurálni, ha a képet jelszó védi. A tároló-beállításjegyzék hitelesítő adatai távolról konfigurálhatók a telepítési jegyzékfájl használatával vagy maga a IoT Edge eszközön a IoT Edge program mappájában található `config.yaml` fájl frissítésével.
* **Settings. createOptions** – a modul tárolójának indításakor közvetlenül a Moby Container Daemon-hoz átadott karakterlánc. Az ebben a tulajdonságban található beállítások megadása lehetővé teszi olyan speciális konfigurációk használatát, mint a port továbbítása vagy a kötetek csatlakoztatása egy modul tárolójába.  
* **status (állapot** ) – az a állapot, amelyben a IoT Edge ügynök elhelyezi a modult. Ez az érték általában a *futtatásra* van beállítva, mivel a legtöbb felhasználó szeretné, hogy a IoT Edge ügynök azonnal elindítsa az összes modult az eszközön. Azonban megadhatja a leállítani kívánt modul kezdeti állapotát, és megvárhatja, hogy a jövőbeli idő alapján a IoT Edge ügynök elindítson egy modult. A IoT Edge ügynök az egyes modulok állapotát a felhőbe jelenti a jelentett tulajdonságok között. A kívánt tulajdonság és a jelentett tulajdonság közötti különbség egy helytelenül viselkedő eszköz jelzője. A támogatott állapotok a következők:
   * Letöltés
   * Fut
   * Sérült
   * Meghiúsult
   * Leállítva
* **restartPolicy** – azt, hogy a IoT Edge ügynök hogyan indítja újra a modult. A lehetséges értékek:
   * `never` – a IoT Edge ügynök soha nem indítja újra a modult.
   * `on-failure` – ha a modul összeomlik, a IoT Edge ügynök újraindul. Ha a modul tisztaan leáll, a IoT Edge ügynök nem indítja újra.
   * `on-unhealthy` – ha a modul összeomlik vagy nem kifogástalannak minősül, a IoT Edge-ügynök újraindul.
   * `always` – ha a modul összeomlik, nem megfelelő állapotba kerül, vagy bármilyen módon leáll, a IoT Edge-ügynök újraindul. 

A IoT Edge ügynök futásidejű választ küld a IoT Hubnak. Itt látható a lehetséges válaszok listája:
  * 200 – OK
  * 400 – a telepítési konfiguráció helytelen formátumú vagy érvénytelen.
  * 417 – az eszköz nem rendelkezik telepítési konfigurációs készlettel.
  * 412 – a séma verziója érvénytelen a telepítési konfigurációban.
  * 406 – a IoT Edge eszköz offline állapotban van, vagy nem küld állapotjelentéseket.
  * 500 – hiba történt a IoT Edge futtatókörnyezetben.

További információkért lásd: [modulok központi telepítése és útvonalak létrehozása IoT Edgeban](module-composition.md).   

### <a name="security"></a>Biztonság

A IoT Edge ügynök kritikus szerepet játszik egy IoT Edge-eszköz biztonságában. Például végrehajtja az olyan műveleteket, mint a modul rendszerképének ellenőrzése a megkezdése előtt. 

A Azure IoT Edge biztonsági keretrendszerével kapcsolatos további információkért olvassa el a [IoT Edge Security Manager](iot-edge-security-manager.md)című témakört.

## <a name="next-steps"></a>Következő lépések

[Azure IoT Edge modulok ismertetése](iot-edge-modules.md)
