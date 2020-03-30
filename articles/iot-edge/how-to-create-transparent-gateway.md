---
title: Átlátható átjáróeszköz létrehozása - Azure IoT Edge | Microsoft dokumentumok
description: Azure IoT Edge-eszköz használata átlátszó átjáróként, amely az alsóbb rétegbeli eszközökről származó adatokat képes feldolgozni
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6069e0782f69d0dfb73d9be2998cbb11d59d7d22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529169"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez

Ez a cikk részletes útmutatást nyújt az IoT Edge-eszközök konfigurálásához, hogy más eszközök számára az IoT Hubbal való kommunikáció hozható kontrórendszer ként működjön. Ez a cikk az *IoT Edge-átjáró* kifejezést használja egy átlátszó átjáróként konfigurált IoT Edge-eszközre való hivatkozáshoz. További információ: [Hogyan használható egy IoT Edge-eszköz átjáróként.](./iot-edge-as-gateway.md)

>[!NOTE]
>Jelenleg:
>
> * A peremhálózati eszközök nem tudnak csatlakozni az IoT Edge-átjárókhoz.
> * Az alsóbb rétegbeli eszközök nem használhatják a fájlfeltöltést.

A sikeres transzparens átjárókapcsolat beállításának három általános lépése van. Ez a cikk az első lépést ismerteti:

1. **Az átjáróeszköznek képesnek kell lennie biztonságosan csatlakozni az alsóbb rétegbeli eszközökhöz, fogadnia kell a kommunikációt az alsóbb rétegbeli eszközökről, és üzeneteket kell irányítania a megfelelő célhelyre.**
2. Az alsóbb rétegbeli eszköznek rendelkeznie kell egy eszközidentitással ahhoz, hogy hitelesítse magát az IoT Hubbal, és ismerje az átjáróeszközön keresztüli kommunikációt. További információ: [Hitelesítette az alsóbb rétegbeli eszközt az Azure IoT Hubhoz.](how-to-authenticate-downstream-device.md)
3. Az alsóbb rétegbeli eszköznek biztonságosan kell csatlakoznia az átjáróeszközhöz. További információ: [Alsóbb rétegbeli eszköz csatlakoztatása egy Azure IoT Edge-átjáróhoz.](how-to-connect-downstream-device.md)

Ahhoz, hogy egy eszköz átjáróként működjön, biztonságosan csatlakoznia kell az alsóbb rétegbeli eszközeihez. Az Azure IoT Edge lehetővé teszi, hogy egy nyilvános kulcsú infrastruktúra (PKI) használatával biztonságos kapcsolatokat létesíteni az eszközök között. Ebben az esetben engedélyezzük, hogy egy alsóbb rétegbeli eszköz egy átlátszó átjáróként működő IoT Edge-eszközhöz csatlakozzon. Az ésszerű biztonság fenntartása érdekében az alsóbb rétegbeli eszköznek meg kell erősítenie az átjáróeszköz identitását. Ez az identitás-ellenőrzés megakadályozza, hogy az eszközök potenciálisan rosszindulatú átjárókhoz csatlakozzanak.

Az átlátszó átjáróforgatókönyvben lévő alsóbb rétegbeli eszköz bármely olyan alkalmazás vagy platform lehet, amely az [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) felhőszolgáltatással létrehozott identitással rendelkezik. Ezek az alkalmazások sok esetben az [Azure IoT-eszköz SDK-t](../iot-hub/iot-hub-devguide-sdks.md)használják. Minden gyakorlati szempontból egy alsóbb rétegbeli eszköz akár az IoT Edge átjáróeszközön futó alkalmazás is lehet. Azonban egy IoT Edge-eszköz nem lehet egy IoT Edge-átjáró.

Bármilyen tanúsítvány-infrastruktúrát létrehozhat, amely lehetővé teszi az eszköz-átjáró topológiához szükséges megbízhatóságot. Ebben a cikkben ugyanazt a tanúsítvány-beállítást feltételezzük, amelyet az [X.509 hitelesítésszolgáltató biztonságának](../iot-hub/iot-hub-x509ca-overview.md) engedélyezéséhez használna az IoT Hubban, amely egy adott IoT-központhoz (az IoT hub legfelső szintű hitelesítésszolgáltatóhoz), a hitelesítésszolgáltatóhoz aláírt tanúsítványok sorozatához és az IoT Edge-eszköz hitelesítésszolgáltatójának hitelesítésszolgáltatóját foglalja magában.

![Átjárótanúsítvány beállítása](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>A cikkben használt "legfelső szintű hitelesítésszolgáltató" kifejezés a PKI tanúsítványlánc legfelső hatósági nyilvános tanúsítványára utal, és nem feltétlenül egy konzorciális hitelesítésszolgáltató tanúsítványának főtanúsítványára. Sok esetben ez valójában egy köztes hitelesítésszolgáltató nyilvános tanúsítvány.

Az átjáró bemutatja az IoT Edge-eszköz hitelesítésszolgáltatói tanúsítványát az alsóbb rétegbeli eszköznek a kapcsolat kezdeményezése során. Az alsóbb rétegbeli eszköz ellenőrzi, hogy az IoT Edge-eszköz hitelesítésszolgáltatói tanúsítványát a legfelső szintű hitelesítésszolgáltatói tanúsítvány írta-e alá. Ez a folyamat lehetővé teszi, hogy az alsóbb rétegbeli eszköz ellenőrizze, hogy az átjáró megbízható forrásból származik-e.

A következő lépések végigvezetik a tanúsítványok létrehozásának és az átjáró megfelelő helyén történő telepítésének folyamatán. Bármelyik gép segítségével létrehozhatja a tanúsítványokat, majd másolja őket az IoT Edge-eszközre.

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz, [éles tanúsítványokkal](how-to-manage-device-certificates.md)konfigurálva.

## <a name="deploy-edgehub-to-the-gateway"></a>EdgeHub üzembe helyezése az átjáróra

Amikor először telepíti az IoT Edge-et egy eszközre, csak egy rendszermodul indul el automatikusan: az IoT Edge-ügynök. Miután létrehozta az első központi telepítés több eszköz, a második rendszermodul, az IoT Edge hub, elindul is.

Az IoT Edge hub felelős a bejövő üzenetek fogadásáért az alsóbb rétegbeli eszközökről, és útválasztása őket a következő helyre. Ha az **edgeHub** modul nem fut az eszközön, hozzon létre egy kezdeti központi telepítést az eszközhöz. A központi telepítés üresnek fog tűnni, mert nem ad hozzá modulokat, de gondoskodik arról, hogy mindkét rendszermodul fut.The deployment will look empty because you don't add any modules, but it will make sure that both system modules are running.

Ellenőrizheti, hogy mely modulok futnak egy eszközön, ha ellenőrzi az eszköz adatait az Azure Portalon, megtekinti `iotedge list` az eszköz állapotát a Visual Studio vagy a Visual Studio-kódban, vagy futtathatja a parancsot magán az eszközön.

Ha az **edgeAgent** modul az **edgeHub** modul nélkül fut, kövesse a következő lépéseket:

1. Az Azure Portalon keresse meg az IoT-központot.

2. Nyissa meg az **IoT Edge-et,** és válassza ki az ioT Edge-eszközt, amelyet átjáróként szeretne használni.

3. Válassza **a Modulok beállítása**lehetőséget.

4. Válassza a **Tovább lehetőséget.**

5. Az **útvonalak megadása** lapon rendelkeznie kell egy alapértelmezett útvonal, amely elküldi az összes üzenetet az összes modul az IoT Hub. Amennyiben nem így lenne, adja hozzá a következő kódot, és kattintson a **Tovább** gombra.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. A **Véleményezés sablon** lapon válassza a **Küldés lehetőséget.**

## <a name="open-ports-on-gateway-device"></a>Portok megnyitása az átjáróeszközön

A szabványos IoT Edge-eszközök működéséhez nincs szükség bejövő kapcsolatra, mivel az IoT Hubbal folytatott összes kommunikáció kimenő kapcsolatokon keresztül történik. Az átjáróeszközök különböznek, mert üzeneteket kell fogadniuk az alsóbb rétegbeli eszközeikről. Ha a tűzfal az alsóbb rétegbeli eszközök és az átjáróeszköz között van, akkor a tűzfalon keresztül is lehetségesnek kell lennie a kommunikációnak.

Egy átjáró forgatókönyv működéséhez legalább az ioT Edge hub támogatott protokollok nyitva kell lennie a bejövő forgalmat az alsóbb rétegbeli eszközökről. A támogatott protokollok az MQTT, az AMQP, a HTTPS, az MQTT a WebSockets felett és az AMQP a WebSockets-en keresztül.

| Port | Protocol (Protokoll) |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>Üzenetek átirányítása alsóbb rétegbeli eszközökről

Az IoT Edge futásidejű az alsóbb rétegbeli eszközökről küldött üzeneteket ugyanúgy továbbíthatja, mint a modulok által küldött üzeneteket. Ez a funkció lehetővé teszi, hogy az átjárón futó modulban elemzéseket hajtson végre, mielőtt bármilyen adatot küldene a felhőbe.

Jelenleg az alsóbb rétegbeli eszközök által küldött üzenetek et úgy továbbítja, hogy megkülönbözteti őket a modulok által küldött üzenetektől. A modulok által küldött üzenetek mindegyike egy **connectionModuleId** nevű rendszertulajdonságot tartalmaz, de az alsóbb rétegbeli eszközök által küldött üzenetek nem. Az útvonal WHERE záradékával kizárhatja az adott rendszertulajdonságot tartalmazó üzeneteket.

Az alábbi útvonal egy példa, amely üzeneteket küld `ai_insights`bármely alsóbb `ai_insights` rétegbeli eszközről egy modul nevű , majd az IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Az üzenet-útválasztásról a [Modulok telepítése és útvonalak létrehozása című](./module-composition.md#declare-routes)témakörben talál további információt.

## <a name="enable-extended-offline-operation"></a>Kiterjesztett kapcsolat nélküli művelet engedélyezése

Az IoT Edge-futásidejű [1.0.4-es v1.0.4-es kiadásával](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) kezdve az átjáróeszköz és a hozzá csatlakozó alsóbb rétegbeli eszközök konfigurálhatók a kiterjesztett offline működéshez.

Ezzel a funkcióval a helyi modulok vagy az alsóbb rétegbeli eszközök szükség szerint újra hitelesíthetik magukat az IoT Edge-eszközzel, és üzenetek és metódusok használatával kommunikálhatnak egymással, még akkor is, ha le vannak választva az IoT hubról. További [információ: Az IoT Edge-eszközök, modulok és gyermekeszközök kiterjesztett offline képességeinek ismertetése.](offline-capabilities.md)

A kiterjesztett offline képességek engedélyezéséhez hozzon létre egy szülő-gyermek kapcsolatot az IoT Edge átjáróeszköz és a hozzá csatlakozó alsóbb rétegbeli eszközök között. Ezeket a lépéseket részletesebben ismerteti [az Azure IoT Hubnak egy alsóbb rétegbeli eszköz hitelesítése.](how-to-authenticate-downstream-device.md)

## <a name="next-steps"></a>További lépések

Most, hogy egy IoT Edge-eszköz átlátszó átjáróként működik, konfigurálnia kell az alsóbb rétegbeli eszközöket, hogy megbízzon az átjáróban, és üzeneteket küldjön hozzá. Folytassa [az Azure IoT Hubra való hitelesítése az azure IoT Hubra](how-to-authenticate-downstream-device.md) a következő lépésekhez az átlátszó átjáróforgatókönyv beállításához.
