---
title: Transzparens átjáró-eszköz létrehozása – Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge-eszköz használata transzparens átjáróként, amely adatokat dolgozhat fel az alsóbb rétegbeli eszközökről
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1deeb17e4d55c81d6161855ee2e6dc4766bdcdca
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772465"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>IoT Edge-eszköz konfigurálása transzparens átjáróként

Ez a cikk részletesen ismerteti, hogyan konfigurálhat egy IoT Edge eszközt úgy, hogy az más eszközök számára transzparens átjáróként működjön, hogy az IoT Hub kommunikáljon. Ez a cikk az *IoT Edge átjáró* kifejezést használja az átlátszó átjáróként konfigurált IoT Edge eszközre való hivatkozáshoz. További tudnivalókért tekintse meg a [IoT Edge-eszköz átjáróként való használatát](./iot-edge-as-gateway.md)ismertető témakört.

>[!NOTE]
>Jelenleg
> * Az Edge-kompatibilis eszközök nem csatlakozhatnak IoT Edge átjáróhoz. 
> * Az alsóbb rétegbeli eszközök nem használhatják a fájl feltöltését.

A sikeres transzparens átjáró-kapcsolatok létrehozásához három általános lépés szükséges. Ez a cikk az első lépést ismerteti:

1. **Az átjáró-eszköznek képesnek kell lennie az alsóbb rétegbeli eszközökhöz való biztonságos kapcsolódásra, az alárendelt eszközökről érkező kommunikáció fogadására és az üzenetek megfelelő célhelyre való továbbítására.**
2. Az alsóbb rétegbeli eszköznek rendelkeznie kell egy eszköz-identitással, hogy képes legyen hitelesíteni a IoT Hub, és tudnia kell kommunikálni az átjáró eszközén keresztül. További információ: [alsóbb rétegbeli eszköz hitelesítése az Azure IoT Hubban](how-to-authenticate-downstream-device.md).
3. Az alsóbb rétegbeli eszköznek biztonságosan kell csatlakoznia az átjáró eszközéhez. További információ: [alsóbb rétegbeli eszköz csatlakoztatása Azure IoT Edge átjáróhoz](how-to-connect-downstream-device.md).


Ahhoz, hogy egy eszköz átjáróként működjön, az informatikai részlegnek képesnek kell lennie az alsóbb rétegbeli eszközökhöz való biztonságos kapcsolódásra. Azure IoT Edge lehetővé teszi, hogy egy nyilvános kulcsokra épülő infrastruktúrát (PKI) használjon az eszközök közötti biztonságos kapcsolatok beállításához. Ebben az esetben lehetővé tesszük, hogy egy alsóbb rétegbeli eszköz olyan IoT Edge-eszközhöz kapcsolódjon, amely transzparens átjáróként működik. Az ésszerű biztonság fenntartása érdekében az alsóbb rétegbeli eszköznek meg kell erősítenie az átjáró-eszköz identitását. Ez az identitás-ellenőrzési szolgáltatás megakadályozza, hogy az eszközök esetlegesen rosszindulatú átjáróhoz csatlakozzanak.

Egy transzparens átjáró-forgatókönyvben lévő alsóbb rétegbeli eszköz lehet bármely olyan alkalmazás vagy platform, amely rendelkezik az [Azure IoT hub](https://docs.microsoft.com/azure/iot-hub) Cloud Service szolgáltatással létrehozott identitással. Sok esetben ezek az alkalmazások az [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md)-t használják. Az alárendelt eszköz minden gyakorlati célra akár egy, a IoT Edge átjáró eszközön futó alkalmazás is lehet. Egy IoT Edge eszköz azonban nem lehet egy IoT Edge átjárón. 

Létrehozhat olyan tanúsítvány-infrastruktúrát is, amely lehetővé teszi az eszköz-átjáró topológiához szükséges megbízhatóságot. Ez a cikk azt feltételezi, hogy ugyanazt a tanúsítványt használja, amelyet az [x. 509 hitelesítésszolgáltatói biztonság](../iot-hub/iot-hub-x509ca-overview.md) engedélyezéséhez használ IoT Hubban, amely egy adott IoT hubhoz (az IoT hub legfelső szintű hitelesítésszolgáltatója) társított x. 509 hitelesítésszolgáltatói tanúsítvánnyal, a hitelesítésszolgáltatóval aláírt tanúsítványok sorozatával, valamint a IoT Edge eszközhöz tartozó hitelesítésszolgáltatóval is rendelkezik.

![Átjáró tanúsítványának beállítása](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Az ebben a cikkben használt "legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ" kifejezés a PKI-tanúsítványlánc legfelső szintű nyilvános tanúsítványára vonatkozik, nem feltétlenül a konzorciális hitelesítésszolgáltató tanúsítványának gyökerére. Sok esetben valójában egy közbenső HITELESÍTÉSSZOLGÁLTATÓ nyilvános tanúsítványa. 

Az átjáró megjeleníti a IoT Edge eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát az alárendelt eszközön a kapcsolat megkezdése során. Az alárendelt eszköz ellenőrzi, hogy az IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány alá van-e írva. Ez a folyamat lehetővé teszi, hogy az alsóbb rétegbeli eszköz ellenőrizze, hogy az átjáró megbízható forrásból származik-e.

A következő lépések végigvezetik a tanúsítványok létrehozásának és telepítésének folyamatán az átjáró megfelelő helyein. A tanúsítványok létrehozásához bármilyen gépet használhat, majd átmásolhatja őket a IoT Edge eszközre. 

## <a name="prerequisites"></a>Előfeltételek

[Éles tanúsítvánnyal](how-to-install-production-certificates.md)konfigurált Azure IoT Edge-eszköz.

## <a name="deploy-edgehub-to-the-gateway"></a>EdgeHub üzembe helyezése az átjárón

Amikor először telepíti a IoT Edget egy eszközön, a rendszer csak egy rendszermodult kezd automatikusan: a IoT Edge-ügynököt. Miután létrehozta az első üzembe helyezési eszközt, a második rendszermodul, az IoT Edge hub is elindul. 

Az IoT Edge hub feladata, hogy fogadja a bejövő üzeneteket az alárendelt eszközökről, és átirányítsa azokat a következő célhelyre. Ha a **edgeHub** modul nem fut az eszközön, hozzon létre egy kezdeti központi telepítést az eszköz számára. Az üzemelő példány üresen jelenik meg, mert nem ad hozzá modulokat, de gondoskodni fog arról, hogy mindkét rendszer-modul fusson. 

Megtekintheti, hogy mely modulok futnak az eszközön, ha megtekinti az eszköz adatait a Azure Portalban, megtekinti az eszköz állapotát a Visual Studióban vagy a Visual Studio Code-ban, vagy a parancs `iotedge list` az eszközön való futtatásával. 

Ha a **edgeAgent** modul a **edgeHub** modul nélkül fut, kövesse az alábbi lépéseket:

1. Az Azure Portalon keresse meg az IoT-központot.

2. Lépjen **IoT Edge** , és válassza ki az átjáróként használni kívánt IoT Edge-eszközt.

3. Válassza a **Modulok beállítása** lehetőséget.

4. Kattintson a **Tovább** gombra.

5. Az **útvonalak meghatározása** lapon olyan alapértelmezett útvonalon kell lennie, amely az összes modul összes üzenetét elküldi IoT hub. Amennyiben nem így lenne, adja hozzá a következő kódot, és kattintson a **Tovább** gombra.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. A **sablon áttekintése** lapon válassza a **Küldés**lehetőséget.

## <a name="open-ports-on-gateway-device"></a>Portok megnyitása átjáró eszközön

A standard IoT Edge eszközök nem igényelnek semmilyen bejövő kapcsolatot a működéshez, mert az IoT Hubkel folytatott kommunikáció kimenő kapcsolatokon keresztül történik. Az átjáró-eszközök különböznek, mert üzeneteket kell kapniuk az alsóbb rétegbeli eszközökről. Ha a tűzfal az alárendelt eszközök és az átjáró-eszköz között van, akkor a kommunikációnak a tűzfalon keresztül is elérhetőnek kell lennie.

Az átjáró-forgatókönyvek működéséhez az IoT Edge hub által támogatott protokollok közül legalább az egyiknek nyitva kell lennie az alárendelt eszközökről érkező bejövő forgalom számára. A támogatott protokollok a MQTT, a AMQP, a HTTPS, a MQTT over WebSockets és a AMQP WebSocket-en keresztül. 

| Port | Protocol (Protokoll) |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS | 

## <a name="route-messages-from-downstream-devices"></a>Üzenetek továbbítása az alárendelt eszközökről
A IoT Edge futtatókörnyezet képes továbbítani az alárendelt eszközökről küldött üzeneteket ugyanúgy, mint a modulok által küldött üzenetek. Ez a funkció lehetővé teszi az elemzést az átjárón futó modulban, mielőtt adatokat küldene a felhőbe. 

Jelenleg az alárendelt eszközök által küldött üzenetek továbbításának módja a modulok által küldött üzenetektől való megkülönböztetés. A modulok által küldött üzenetek tartalmazzák a **connectionModuleId** nevű rendszertulajdonságot, de az alárendelt eszközök által küldött üzenetek nem. Az útvonal WHERE záradékával kizárhatja az adott System tulajdonságot tartalmazó üzeneteket. 

Az alábbi útvonal egy példa arra, hogy bármely alsóbb rétegbeli eszközről üzenetet küldjön egy `ai_insights`nevű modulra, majd `ai_insights`ról IoT Hubre.

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

Az IoT Edge futtatókörnyezet [v 1.0.4 kiadásával](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) kezdődően az átjáró-eszköz és a hozzá csatlakozó alsóbb rétegbeli eszközök konfigurálható a kiterjesztett offline művelethez. 

Ezzel a képességgel a helyi modulok és az alsóbb rétegbeli eszközök szükség szerint újra hitelesíthetők a IoT Edge eszközzel, és kommunikálhatnak egymással az üzenetek és a metódusok használatával, még akkor is, ha az IoT hub le van választva. További információ: [IoT Edge eszközök, modulok és alárendelt eszközök kibővített offline képességeinek ismertetése](offline-capabilities.md).

A kibővített offline képességek engedélyezéséhez létre kell hoznia egy szülő-gyermek kapcsolatot egy IoT Edge átjáró-eszköz és a hozzá kapcsolódó alsóbb szintű eszközök között. Ezeket a lépéseket részletesen ismertetjük az [alsóbb rétegbeli eszközök Azure-IoT hub történő hitelesítésével](how-to-authenticate-downstream-device.md)kapcsolatban.

## <a name="next-steps"></a>Következő lépések

Most, hogy rendelkezik egy IoT Edge eszközzel, amely transzparens átjáróként működik, konfigurálnia kell az alsóbb rétegbeli eszközöket az átjáró megbízhatóságához és az üzenetek küldéséhez. Folytassa a következő lépéssel, hogy [hitelesítse egy alsóbb rétegbeli eszközt az Azure IoT Hubban](how-to-authenticate-downstream-device.md) az átlátszó átjáró beállításának további lépéseihez. 
