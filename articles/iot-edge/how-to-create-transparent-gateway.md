---
title: Transzparens átjáró-eszköz létrehozása – Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge-eszköz használata transzparens átjáróként, amely adatokat dolgozhat fel az alsóbb rétegbeli eszközökről
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0155294777e1d732e5ff3874102b90049d9a123d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782585"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez

Ez a cikk részletesen ismerteti, hogyan konfigurálhat egy IoT Edge eszközt úgy, hogy az más eszközök számára transzparens átjáróként működjön, hogy az IoT Hub kommunikáljon. Ez a cikk az *IoT Edge átjáró* kifejezést használja az átlátszó átjáróként konfigurált IoT Edge eszközre való hivatkozáshoz. További tudnivalókért tekintse meg a [IoT Edge-eszköz átjáróként való használatát](./iot-edge-as-gateway.md)ismertető témakört.

>[!NOTE]
>Jelenleg
>
> * Az Edge-kompatibilis eszközök nem csatlakozhatnak IoT Edge átjáróhoz.
> * Az alsóbb rétegbeli eszközök nem használhatják a fájl feltöltését.

A sikeres transzparens átjáró-kapcsolatok létrehozásához három általános lépés szükséges. Ez a cikk az első lépést ismerteti:

1. **Konfigurálja az átjáró-eszközt kiszolgálóként, hogy az alsóbb rétegbeli eszközök biztonságosan kapcsolódjanak hozzá. Állítsa be az átjárót, hogy üzeneteket kapjon az alsóbb rétegbeli eszközökről, és irányítsa azokat a megfelelő helyre.**
2. Hozzon létre egy eszköz-identitást az alsóbb rétegbeli eszköz számára, hogy a hitelesítés a IoT Hub használatával történjen. Az alsóbb rétegbeli eszköz konfigurálásával üzeneteket küldhet az átjáró eszközön keresztül. További információ: [alsóbb rétegbeli eszköz hitelesítése az Azure IoT Hubban](how-to-authenticate-downstream-device.md).
3. Az alárendelt eszköz csatlakoztatása az átjáró eszközhöz és az üzenetek küldésének megkezdése. További információ: [alsóbb rétegbeli eszköz csatlakoztatása Azure IoT Edge átjáróhoz](how-to-connect-downstream-device.md).

Ahhoz, hogy egy eszköz átjáróként működjön, biztonságosan csatlakoznia kell az alsóbb rétegbeli eszközökhöz. Azure IoT Edge lehetővé teszi, hogy egy nyilvános kulcsokra épülő infrastruktúrát (PKI) használjon az eszközök közötti biztonságos kapcsolatok beállításához. Ebben az esetben lehetővé tesszük, hogy egy alsóbb rétegbeli eszköz olyan IoT Edge-eszközhöz kapcsolódjon, amely transzparens átjáróként működik. Az ésszerű biztonság fenntartása érdekében az alsóbb rétegbeli eszköznek meg kell erősítenie az átjáró-eszköz identitását. Ez az identitás-ellenőrzési szolgáltatás megakadályozza, hogy az eszközök esetlegesen rosszindulatú átjáróhoz csatlakozzanak.

Egy alsóbb rétegbeli eszköz lehet bármely olyan alkalmazás vagy platform, amely rendelkezik az [Azure IoT hub](https://docs.microsoft.com/azure/iot-hub) Cloud Service szolgáltatással létrehozott identitással. Ezek az alkalmazások gyakran az [Azure IoT-eszköz SDK](../iot-hub/iot-hub-devguide-sdks.md)-t használják. Egy alsóbb rétegbeli eszköz is lehet a IoT Edge átjáró eszközön futó alkalmazás. Egy IoT Edge eszköz azonban nem lehet egy IoT Edge átjárón.

Létrehozhat olyan tanúsítvány-infrastruktúrát is, amely lehetővé teszi az eszköz-átjáró topológiához szükséges megbízhatóságot. Ez a cikk azt feltételezi, hogy ugyanazt a tanúsítványt használja, amelyet az [x. 509 hitelesítésszolgáltatói biztonság](../iot-hub/iot-hub-x509ca-overview.md) engedélyezéséhez használ IoT Hubban, amely egy adott IoT hubhoz (az IoT hub legfelső szintű hitelesítésszolgáltatója) társított x. 509 hitelesítésszolgáltatói tanúsítvánnyal, a hitelesítésszolgáltatóval aláírt tanúsítványok sorozatával, valamint a IoT Edge eszközhöz tartozó hitelesítésszolgáltatóval is rendelkezik.

>[!NOTE]
>Az ezekben a cikkekben használt *legfelső szintű hitelesítésszolgáltatói tanúsítvány* kifejezés a PKI-tanúsítványlánc legfelső szintű nyilvános tanúsítványára hivatkozik, nem feltétlenül a konzorciális hitelesítésszolgáltató tanúsítványának gyökerére. Sok esetben valójában egy közbenső HITELESÍTÉSSZOLGÁLTATÓ nyilvános tanúsítványa.

A következő lépések végigvezetik a tanúsítványok létrehozásának és telepítésének folyamatán az átjáró megfelelő helyein. A tanúsítványok létrehozásához bármilyen gépet használhat, majd átmásolhatja őket a IoT Edge eszközre.

## <a name="prerequisites"></a>Előfeltételek

Linux vagy Windows rendszerű eszköz, amelyen IoT Edge telepítve van.

## <a name="set-up-the-device-ca-certificate"></a>Az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának beállítása

Minden IoT Edge átjáróhoz telepítve kell lennie egy eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának. A IoT Edge Security Daemon a IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát használja a számítási feladatok HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának aláírásához, ami aláírja az IoT Edge hub kiszolgálói tanúsítványát. Az átjáró a kapcsolat megkezdése során megjeleníti a kiszolgálói tanúsítványát az alárendelt eszközön. Az alsóbb rétegbeli eszköz ellenőrzi, hogy a kiszolgálói tanúsítvány egy olyan tanúsítványlánc részét képezi-e, amely a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványba összesíti. Ez a folyamat lehetővé teszi, hogy az alsóbb rétegbeli eszköz ellenőrizze, hogy az átjáró megbízható forrásból származik-e. További információ: a [Azure IoT Edge tanúsítványok használatának](iot-edge-certs.md)ismertetése.

![Átjáró tanúsítványának beállítása](./media/how-to-create-transparent-gateway/gateway-setup.png)

A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványnak és az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának (titkos kulcsával) jelen kell lennie az IoT Edge átjáró eszközön, és konfigurálni kell a IoT Edge config. YAML fájlban. Ne feledje, hogy ebben az esetben a *legfelső szintű hitelesítésszolgáltatói tanúsítvány* a legfelső szintű hitelesítésszolgáltatót jelenti ehhez a IoT Edge-forgatókönyvhöz. Az átjáró-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának és az alsóbb rétegbeli eszköz tanúsítványának azonos legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra van szüksége.

>[!TIP]
>A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány és az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának IoT Edge-eszközre történő telepítésének folyamatát a [IoT Edge-eszközök tanúsítványainak kezelése](how-to-manage-device-certificates.md)című részben is ismertetjük.

A következő fájlok készen állnak:

* Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány
* Eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa
* Eszköz HITELESÍTÉSSZOLGÁLTATÓI titkos kulcsa

Éles környezetekben ezeket a fájlokat saját hitelesítésszolgáltatóval kell előállítani. Fejlesztési és tesztelési helyzetekben használhat bemutató tanúsítványokat.

1. Ha bemutató tanúsítványokat használ, a következő lépésekkel hozhatja létre a fájlokat:
   1. [Hozzon létre egy legfelső szintű hitelesítésszolgáltatói tanúsítványt](how-to-create-test-certificates.md#create-root-ca-certificate). Ezen utasítások végén egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványfájl fog rendelkezni:
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.

   2. [Hozzon létre IoT Edge eszköz hitelesítésszolgáltatói tanúsítványát](how-to-create-test-certificates.md#create-iot-edge-device-ca-certificates). Ezen utasítások végén két fájl, egy eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa és a titkos kulcsa lesz:
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem`és
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

2. Ha ezeket a fájlokat egy másik gépen hozta létre, másolja át őket a IoT Edge eszközre.

3. A IoT Edge eszközön nyissa meg a biztonsági démon konfigurációs fájlját.
   * Windows`C:\ProgramData\iotedge\config.yaml`
   * Linux`/etc/iotedge/config.yaml`

4. Keresse meg a fájl **tanúsítványok** szakaszát, és adja meg a fájl URI-fájljait a három fájl számára a következő tulajdonságok értékének megfelelően:
   * **device_ca_cert**: eszköz hitelesítésszolgáltatói tanúsítványa
   * **device_ca_pk**: eszköz hitelesítésszolgáltatói titkos kulcsa
   * **trusted_ca_certs**: legfelső szintű hitelesítésszolgáltatói tanúsítvány

5. Mentse és zárja be a fájlt.

6. IoT Edge újraindítása.
   * Windows`Restart-Service iotedge`
   * Linux`sudo systemctl restart iotedge`

## <a name="deploy-edgehub-to-the-gateway"></a>EdgeHub üzembe helyezése az átjárón

Amikor először telepíti a IoT Edget egy eszközön, a rendszer csak egy rendszermodult kezd automatikusan: a IoT Edge-ügynököt. Miután létrehozta az első központi telepítést egy eszközhöz, a második rendszermodul, az IoT Edge hub is elindul.

Az IoT Edge hub feladata, hogy fogadja a bejövő üzeneteket az alárendelt eszközökről, és átirányítsa azokat a következő célhelyre. Ha a **edgeHub** modul nem fut az eszközön, hozzon létre egy kezdeti központi telepítést az eszköz számára. Az üzemelő példány üresen jelenik meg, mert nem ad hozzá modulokat, de gondoskodni fog arról, hogy mindkét rendszer-modul fusson.

Megtekintheti, hogy mely modulok futnak az eszközön, ha megtekinti az eszköz adatait a Azure Portalban, megtekinti az eszköz állapotát a Visual Studióban vagy a Visual Studio Code-ban, vagy maga az eszközön futtatja a parancsot `iotedge list` .

Ha a **edgeAgent** modul a **edgeHub** modul nélkül fut, kövesse az alábbi lépéseket:

1. Az Azure Portalon keresse meg az IoT-központot.

2. Lépjen **IoT Edge** , és válassza ki az átjáróként használni kívánt IoT Edge-eszközt.

3. Válassza a **modulok beállítása**lehetőséget.

4. Válassza a **Tovább: útvonalak**lehetőséget.

5. Az **útvonalak** lapon olyan alapértelmezett útvonalon kell lennie, amely minden üzenetet küld, akár modulból, akár egy alsóbb rétegbeli eszközről, hogy IoT hub. Ha nem, adjon hozzá egy új útvonalat a következő értékekkel, majd válassza a **felülvizsgálat + létrehozás**lehetőséget:
   * **Név**:`route`
   * **Érték**:`FROM /messages/* INTO $upstream`

6. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás**lehetőséget.

## <a name="open-ports-on-gateway-device"></a>Portok megnyitása átjáró eszközön

A standard IoT Edge eszközök nem igényelnek semmilyen bejövő kapcsolatot a működéshez, mert az IoT Hubkel folytatott kommunikáció kimenő kapcsolatokon keresztül történik. Az átjáró-eszközök különböznek, mert üzeneteket kell kapniuk az alsóbb rétegbeli eszközökről. Ha a tűzfal az alárendelt eszközök és az átjáró-eszköz között van, akkor a kommunikációnak a tűzfalon keresztül is elérhetőnek kell lennie.

Az átjáró-forgatókönyvek működéséhez az IoT Edge hub által támogatott protokollok közül legalább az egyiknek nyitva kell lennie az alárendelt eszközökről érkező bejövő forgalom számára. A támogatott protokollok a MQTT, a AMQP, a HTTPS, a MQTT over WebSockets és a AMQP WebSocket-en keresztül.

| Port | Protokoll |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="route-messages-from-downstream-devices"></a>Üzenetek továbbítása az alárendelt eszközökről

A IoT Edge futtatókörnyezet képes továbbítani az alárendelt eszközökről küldött üzeneteket ugyanúgy, mint a modulok által küldött üzenetek. Ez a funkció lehetővé teszi az elemzést az átjárón futó modulban, mielőtt adatokat küldene a felhőbe.

Jelenleg az alárendelt eszközök által küldött üzenetek továbbításának módja a modulok által küldött üzenetektől való megkülönböztetés. A modulok által küldött üzenetek tartalmazzák a **connectionModuleId** nevű rendszertulajdonságot, de az alárendelt eszközök által küldött üzenetek nem. Az útvonal WHERE záradékával kizárhatja az adott System tulajdonságot tartalmazó üzeneteket.

Az alábbi útvonal egy példa arra, hogy bármely alsóbb rétegbeli eszközről üzeneteket küldjön egy nevű modulba `ai_insights` , majd a `ai_insights` IoT hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Az üzenetek útválasztásával kapcsolatos további információkért lásd: [modulok üzembe helyezése és útvonalak létrehozása](./module-composition.md#declare-routes).

## <a name="enable-extended-offline-operation"></a>Kiterjesztett offline művelet engedélyezése

Az IoT Edge Runtime [1.0.4-kiadásával](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) kezdődően az átjáró-eszköz és a hozzá csatlakozó alsóbb rétegbeli eszközök konfigurálható a kiterjesztett offline művelethez.

Ezzel a képességgel a helyi modulok és az alsóbb rétegbeli eszközök szükség szerint újra hitelesíthetők a IoT Edge eszközzel, és kommunikálhatnak egymással az üzenetek és a metódusok használatával, még akkor is, ha az IoT hub le van választva. További információ: [IoT Edge eszközök, modulok és alárendelt eszközök kibővített offline képességeinek ismertetése](offline-capabilities.md).

A kibővített offline képességek engedélyezéséhez létre kell hoznia egy szülő-gyermek kapcsolatot egy IoT Edge átjáró-eszköz és a hozzá kapcsolódó alsóbb szintű eszközök között. Ezeket a lépéseket részletesebben ismertetjük a sorozat következő cikkében, amely [egy alsóbb rétegbeli eszközt hitelesít az Azure IoT Hubban](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik egy IoT Edge eszközzel, amely transzparens átjáróként van beállítva, konfigurálnia kell az alsóbb rétegbeli eszközöket az átjáró megbízhatóságához és az üzenetek küldéséhez. Folytassa a következő lépéssel, hogy [hitelesítse egy alsóbb rétegbeli eszközt az Azure IoT Hubban](how-to-authenticate-downstream-device.md) az átlátszó átjáró beállításának további lépéseihez.
