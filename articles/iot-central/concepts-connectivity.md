---
title: Eszköz csatlakoztatása az Azure IoT Central |} A Microsoft Docs
description: Ez a cikk bemutatja a eszköz csatlakoztatása az Azure IoT Central kapcsolatos alapfogalmak
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 9e1e85d1ab1c5e7ce0cbd96c64137309c2e2916a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59425967"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Eszköz csatlakoztatása az Azure IoT Central

Ez a cikk az eszközök csatlakoztatása a Microsoft Azure IoT Central kapcsolatos alapfogalmakat mutatja be.

Az Azure IoT Central használja a [Azure IoT Hub Device Provisioning service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) regisztrálni az eszközt, és a kapcsolat kezeléséhez.

A DPS használatával a következőket:

- A bevezetési és csatlakoztatott eszközök nagy mennyiségű IoT-központ.
- Hogy hozzon létre az eszköz hitelesítő adatait, és az eszközök kapcsolat nélküli konfigurálhatja IoT-központ felhasználói felületen keresztül az eszközök regisztrálása nélkül.
- A közös hozzáférésű jogosultságkódot (SAS) eszközök használatával csatlakozzanak.
- Az eszközök csatlakozni, iparági szabványnak megfelelő X.509 tanúsítványok használatával.
- Az, hogy a saját eszközazonosítókat eszközök regisztrálása az IoT-központ. A saját eszköz azonosítók használata leegyszerűsíti a háttérrendszerhez rendszerekkel való integrációt.
- Egyetlen, egységes módon csatlakoztatni az eszközöket az IoT-központ.

Ez a cikk ismerteti a következő négy használati esetek:

1. [Gyorsan kapcsolatba léphet egy adott eszköz SAS használatával](#connect-a-single-device)
1. [SAS használatával nagy mennyiségű eszköz csatlakoztatása](#connect-devices-at-scale-using-sas)
1. [X.509-tanúsítványok használatával nagy mennyiségű eszköz csatlakoztatása](#connect-devices-using-x509-certificates) Ez az az ajánlott módszer az éles környezetekhez.
1. [Csatlakozzon az első regisztráló eszközök nélkül](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Egyetlen eszköz csatlakoztatása

Ez a megközelítés akkor hasznos, amikor kísérleteztek az IoT-központ vagy a tesztelési eszközök. A kapcsolati információkat használhatja az IoT-központ alkalmazás készítése az eszköz kapcsolati karakterláncára. Részletes lépéseiért lásd: [hogyan hozhat létre az eszköz kapcsolati karakterláncának csatlakozhat egy Azure IoT Central alkalmazáshoz](howto-generate-connection-string.md).

## <a name="connect-devices-at-scale-using-sas"></a>SAS használatával nagy mennyiségű eszköz csatlakoztatása

Eszközök csatlakoztatása IoT-központ a SAS használatával nagy mennyiségű, meg kell regisztrálni, és hozzon létre az eszközök:

### <a name="register-devices-in-bulk"></a>Eszközök tömeges regisztrálása

Az IoT-központ alkalmazását egy nagy számú regisztrálásához használja a CSV-fájl [eszközazonosítókat és eszköznevek importálása](howto-manage-devices.md#import-devices).

Az importált eszközök kapcsolati adatainak lekéréséhez [CSV-fájlba exportálhat az IoT-központ alkalmazás](howto-manage-devices.md#export-devices).

> [!NOTE]
> Ha szeretné megtudni, hogyan kapcsolódhatnak eszközök nélkül első regisztrálja őket az IoT-központ, lásd: [első regisztráló eszközök nélkül Connect](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Az eszközök beállítása

Az exportálási fájlból, az eszköz kódjában a kapcsolatadatok segítségével engedélyezze az eszközök csatlakoztatása és az IoT-adatok küldése az IoT Central alkalmazáshoz. További információ az eszközök csatlakoztatásáról: [további lépések](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>X.509-tanúsítványok használatával eszközök csatlakoztatása

Éles környezetben az X.509-tanúsítványok használatával az IoT-központ ajánlott eszköz hitelesítési mechanizmusa. További tudnivalókért lásd: [X.509 Hitelesítésszolgáltatói tanúsítványok használatával Eszközhitelesítés](../iot-hub/iot-hub-x509ca-overview.md).

Az alábbi lépések bemutatják, hogyan eszközök csatlakoztatása az IoT-központ X.509 tanúsítványok használatával:

1. Az IoT-központ alkalmazás _hozzáadása és a köztes ellenőrzése vagy X.509-tanúsítvány legfelső szintű_ eszköztanúsítványok létrehozásához használ:

    - Navigáljon a **Adminisztráció > eszköz kapcsolat > tanúsítványok (X.509)** , és adja hozzá az X.509 legfelső szintű vagy köztes tanúsítványt a levél eszköztanúsítványok létrehozásához használ.

      ![Kapcsolati beállítások](media/concepts-connectivity/connection-settings.png)

      Ha megsértették a biztonsági vagy az elsődleges tanúsítvány lejár, a másodlagos tanúsítvány használatával csökkentheti az állásidőt. Továbbra is a másodlagos tanúsítványt használ, míg az elsődleges tanúsítvány frissítéséhez eszközök kiépítése.

    - Tanúsítvány tulajdonjogának ellenőrzése biztosítja, hogy a tanúsítvány feltöltése a tanúsítvány titkos kulcsa. A tanúsítvány ellenőrzéséhez:
        - Válassza ki a gomb melletti **ellenőrzőkódot** egy kódot.
        - Hozzon létre egy X.509-hitelesítési tanúsítvány az előző lépésben létrehozott ellenőrzőkódot. Mentse a tanúsítványt egy .cer fájlba.
        - Töltse fel az aláírt hitelesítési tanúsítványt, és válassza ki **ellenőrizze**.

          ![Kapcsolati beállítások](media/concepts-connectivity/verify-cert.png)

1. A CSV-fájl használata _importálása és az eszközök regisztrálása_ az IoT-központ alkalmazásában.

1. _Állítsa be az eszközöket._ A feltöltött legfelső szintű tanúsítvány használatával levél tanúsítványainak előállításához. Használja a **Eszközazonosító** a levél tanúsítványok CNAME értéket. Az eszköz Azonosítójával kell csak kisbetűkből állhat. Ezután a program az eszközök a kiépítési szolgáltatás információkat. Egy eszköz az első bekapcsolt, a DPS lekéri a kapcsolatadatokat az IoT Central alkalmazáshoz.

### <a name="further-reference"></a>További referencia

- A minta megvalósítása [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Eszközügyfél minta c-hez](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Csak tesztelési célokra

Csak tesztelési, ezek a segédprogramok Hitelesítésszolgáltatói tanúsítványok és az eszköztanúsítványok létrehozásához használhatja.

- Ha egy fejlesztői készlet eszközt használ ez [parancssori eszköz](https://aka.ms/iotcentral-docs-dicetool) állít elő, hogy az IoT Central-alkalmazást, hogy a tanúsítványok ellenőrzése hozzáadhat Hitelesítésszolgáltatói tanúsítvány.

- Ezzel [parancssori eszköz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) való:
  - Tanúsítványláncolat kialakításához. Hajtsa végre a 2. lépés a GitHub-cikkben.
  - Mentse a tanúsítványok .cer fájlokat tölthet fel az IoT Central alkalmazáshoz.
  - Az IoT Central-alkalmazást az ellenőrző kód segítségével hozza létre az ellenőrző tanúsítványt. Hajtsa végre a 3. lépés a GitHub-cikkben.
  - Hozzon létre az eszközök azonosítói eszköz használata az eszköz paraméterként levél tanúsítványai. 4. lépés kövesse a GitHub-cikkben talál.

## <a name="connect-without-registering-devices"></a>Eszközök regisztrálása nélkül csatlakoztatása

A legfontosabb előny, IoT Central lehetővé teszi, hogy van egy IoT Central alkalmazáshoz, első nélkül csatlakoztatható eszközök tömeges előállításához számítógépgyártók regisztrálta. Egy gyártó készítése a megfelelő hitelesítő adatokat, és konfigurálnia kell az eszközök gyári. Amikor első alkalommal bekapcsolja egy eszközt, azt automatikusan IoT Central alkalmazáshoz kapcsolódik. Az IoT-központ operátor, stat adatküldés előtt jóvá kell hagynia az eszközön.

A következő ábra ezt a folyamatot ismerteti:

![Kapcsolati beállítások](media/concepts-connectivity/device-connection-flow.png)

A következő lépések írják le részletesebben a folyamat. A lépések kissé eltér attól függően, hogy használ az SAS- vagy X.509 tanúsítványok eszközhitelesítési:

1. A kapcsolat beállításainak konfigurálása:

    - **X.509-tanúsítványokat:** [Adja hozzá és ellenőrizze a legfelső szintű vagy köztes tanúsítványt](#connect-devices-using-x509-certificates) , amellyel az eszköztanúsítványok létrehozásához a következő lépésben.
    - **SAS:** Másolja az elsődleges kulcsot. Ezt a kulcsot nem a csoport SAS-kulcsot az IoT Central alkalmazáshoz. A kulcs használatával hozzon létre az eszköz SAS-kulcsokat, a következő lépésben.
    ![Kapcsolatbeállítások SAS](media/concepts-connectivity/connection-settings-sas.png)

1. Az eszköz hitelesítő adatok előállítása
    - **X.509-tanúsítványok:** Az eszközök használatával, a legfelső szintű vagy köztes tanúsítványt, az IoT Central alkalmazáshoz hozzáadott levél-tanúsítványainak előállításához. Ellenőrizze, hogy használja a kisbetűs **Eszközazonosító** , a CNAME REKORDOT a levél tanúsítványok. Tesztelési célra használja, csak ez [parancssori eszköz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) eszköztanúsítványok létrehozásához.
    - **SAS:** Ezzel [parancssori eszköz](https://www.npmjs.com/package/dps-keygen) eszköz SAS-kulcs létrehozásához. A csoport **elsődleges kulcs** az előző lépésben. Az eszköz azonosítója kisbetűs kell lennie.

      Telepítése a [key generator segédprogram](https://github.com/Azure/dps-keygen), futtassa a következő parancsot:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      A csoport elsődleges SAS-kulcs egy device kulcs létrehozásához futtassa a következő parancsot:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Állítsa be az eszközök, flash mindegyik eszközhöz a **hatókör azonosítója**, **Eszközazonosító**, és **X.509-eszköztanúsítvány** vagy **SAS-kulcsának**.

1. Ezután kapcsolja be az eszközt ahhoz, hogy csatlakozik az IoT Central alkalmazáshoz. Amikor egy eszközön, először csatlakozik az IoT-központ regisztrációs információk lekéréséhez a DPS.

1. A csatlakoztatott eszköz kezdetben megjelenik-e egy **társítatlan eszköz** a a **Device Explorer** lapot. Az eszköz kiépítési állapot **regisztrált**. **Társítsa** az eszköz számára a megfelelő eszköz sablont, és hagyja jóvá az eszköz csatlakoztatása az IoT Central alkalmazáshoz. Ezután az IoT Hub kapcsolati karakterlánc lekérése és adatokat küldjön az eszköz. Eszközök üzembe helyezését a művelet befejeződött, a kiépítési állapota ezután **kiépített**.

## <a name="provisioning-status"></a>Létesítés állapota

Ha egy valódi eszköz csatlakozik az IoT Central alkalmazáshoz, a kiépítési állapot módosul a következő:

1. Az eszköz-üzembehelyezési állapota az első **regisztrált**. Ez az állapot azt jelenti, az eszköz IoT-központ jön létre, és eszköz-azonosítóval. Egy eszköz regisztrálása során:
    - Az új valós eszköz bekerül a **Device Explorer** lapot.
    - Az eszközök segítségével hozzáadott **importálás** a a **Device Explorer** lapot.
    - Egy eszköz nem lett regisztrálva a manuálisan a **Device Explorer** lapon, de kapcsolatban az érvényes hitelesítő adatokat, és látható, mint egy **Unassociated** az eszköz a **Device Explorer**lapot.

1. Az eszköz-üzembehelyezési állapota változik **kiépített** az eszközt, hogy az IoT-központ alkalmazás érvényes hitelesítő adatokkal csatlakozik a kiépítési lépés befejezését. Ebben a lépésben az eszközt az IoT Hub kapcsolati karakterlánc kérdezi le. Az eszköz most már csatlakoztatása az IoT hubhoz, és adatokat küldjön.

1. Az operátor is letiltja az eszköz. Ha egy eszköz le van tiltva, azt nem küldenek adatokat az IoT Central alkalmazáshoz. Letiltott eszközök rendelkeznek egy kiépítési állapota **letiltott**. Az operátornak kell visszaállítani az eszközt, mielőtt folytathatja az adatok küldésének. Az operátornak feloldja egy eszközt a kiépítési állapotot ad vissza az előző értékre, ha **regisztrált** vagy **kiépített**.

## <a name="sdk-support"></a>SDK-támogatás

Az Azure eszközoldali SDK-k ajánlat legegyszerűbb módja az Ön számára az eszköz kód megvalósítását. A következő eszközoldali SDK-k érhetők el:

- [A c nyelvhez készült Azure IoT-SDK](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK for Python](https://github.com/azure/azure-iot-sdk-python)
- [Az Azure IoT SDK for node.js használatával](https://github.com/azure/azure-iot-sdk-node)
- [A Javához készült Azure IoT-SDK](https://github.com/azure/azure-iot-sdk-java)
- [Az Azure IoT SDK for .NET csomaggal](https://github.com/azure/azure-iot-sdk-csharp)

Minden egyes eszköz kapcsolódik, amely azonosítja az eszköz egyedi kapcsolati karakterlánc használatával. Egy eszköz csak képes csatlakozni az IoT hub, ha regisztrálva van. Valós eszközöknek az Azure IoT Central-alkalmazást hoz létre, amikor az alkalmazás állít elő, az adatokat, hozhatnak létre egy kapcsolati karakterlánc használatával kell `dps-keygen`.

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-funkciók és az IoT Hub-kapcsolat

Az összes eszköz kommunikációt az IoT Hub használja a következő IoT Hub kapcsolati lehetőségek:

- [Eszközről-a-felhőbe üzenetkezelés](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Ikereszközök](../iot-hub/iot-hub-devguide-device-twins.md)

A következő táblázat összefoglalja, hogyan Azure IoT Central eszközfunkciók leképezése az IoT Hub-szolgáltatások be:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Mérték: Telemetria | Eszközről-a-felhőbe üzenetkezelés |
| Eszköztulajdonságok | Ikereszköz jelentett tulajdonságait |
| Beállítások | Ikereszköz kívánt és a jelentett tulajdonságok |

Az eszközoldali SDK-k használatával kapcsolatos további tudnivalókért lásd: egyet az alábbi cikkekben például kód:

- [Általános Node.js-ügyfél csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-nodejs.md)
- [A Raspberry Pi-eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz](howto-connect-raspberry-pi-python.md)
- [A DevDiv kit eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz](howto-connect-devkit.md).

### <a name="protocols"></a>Protokollok

Az eszközoldali SDK-k támogatják a következő hálózati protokollok egy IoT hubhoz való csatlakozáshoz:

- MQTT
- AMQP
- HTTPS

Szerinti kiválaszt egyet ezek a különbség a protokollok és útmutatást kapcsolatos információkért lásd: [kommunikációs protokoll kiválasztása](../iot-hub/iot-hub-devguide-protocols.md).

Ha az eszköz nem tudja használni a támogatott protokollok, az Azure IoT Edge segítségével protokoll átalakítás. IoT Edge feldolgozás kiszervezése az Edge-ben az Azure IoT Central-alkalmazás más üzletiintelligencia-az-a-edge forgatókönyvek támogatja.

## <a name="security"></a>Biztonság

Eszközök és az Azure IoT Central között továbbított összes adat titkosítva van. Az IoT Hub olyan eszköz, amely csatlakozik az IoT Hub eszköz felé néző végpontok bármelyikét, érkező minden kérés hitelesíti. Elkerülheti a hitelesítő adatok cseréje a hálózaton keresztül, az eszköz a aláírt jogkivonatokat használ az hitelesítéséhez. További információkért tekintse meg, [férhet hozzá az IoT Hub](../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett az Azure IoT Central eszközkapcsolat, az alábbiakban a javasolt következő lépések:

- [Készítse elő és DevKit eszköz csatlakoztatása](howto-connect-devkit.md)
- [Raspberry Pi előkészítése és csatlakoztatása](howto-connect-raspberry-pi-python.md)
- [Általános Node.js-ügyfél csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-nodejs.md)
- [C SDK: Eszköz ügyfél-SDK kiépítése](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
