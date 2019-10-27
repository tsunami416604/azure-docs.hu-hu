---
title: Eszköz csatlakoztatása az Azure IoT Centralban | Microsoft Docs
description: Ez a cikk bemutatja az Azure-beli eszközök csatlakoztatásával kapcsolatos főbb fogalmakat IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 99efad08819ffb28ebcbd8462a1085b8e69ba003
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951782"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Eszköz csatlakoztatása az Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Ez a cikk a Microsoft Azure IoT Central eszközhöz való csatlakoztatásával kapcsolatos főbb fogalmakat ismerteti.

Az Azure IoT Central az [azure IoT hub Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) használatával kezeli az összes eszköz regisztrációját és kapcsolódását.

A DPS használata lehetővé teszi a következőket:

- IoT Central az eszközök nagy léptékű bevezetésének és csatlakoztatásának támogatásához.
- Az eszköz hitelesítő adatainak előállításához és az eszközök offline konfigurálásához az eszközök IoT Central felhasználói felületen keresztül történő regisztrálása nélkül.
- Megosztott hozzáférési aláírások (SAS) használatával csatlakozó eszközök.
- Az iparági szabványnak megfelelő X. 509 tanúsítványok használatával csatlakoztatható eszközök.
- A saját eszközök azonosítóinak használatával regisztrálhatja az eszközöket a IoT Centralban. A saját eszköz-azonosítók használatával egyszerűbbé válik a meglévő Back-Office rendszerekkel való integráció.
- Egyetlen, egységes módszer az eszközök IoT Centralhoz való csatlakoztatására.

Ez a cikk a következő négy felhasználási esetet ismerteti:

- [Egyetlen eszköz gyors csatlakoztatása SAS használatával](#connect-a-single-device)
- [Eszközök csatlakoztatása nagy méretekben SAS használatával](#connect-devices-at-scale-using-sas)
- Az [eszközök méretezése az X. 509 tanúsítvánnyal](#connect-devices-using-x509-certificates) . Ez az ajánlott módszer az éles környezetekben.
- [Csatlakoztatás az eszközök első regisztrációja nélkül](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Egyetlen eszköz csatlakoztatása

Ez a megközelítés akkor lehet hasznos, ha IoT Central vagy tesztelési eszközökkel kísérletezik. A IoT Central alkalmazás eszköz kapcsolati adataival csatlakoztathat egy eszközt a IoT Central alkalmazáshoz az eszköz kiépítési szolgáltatása (DPS) használatával. A következő nyelveken talál minta DPS-eszköz ügyféloldali kódját:

- [C\#](./howto-connect-raspberry-pi-csharp.md)
- [Node.js](./howto-connect-nodejs.md)

## <a name="connect-devices-at-scale-using-sas"></a>Eszközök csatlakoztatása nagy méretekben SAS használatával

Az eszközök SAS-vel való IoT Centralhoz való csatlakoztatásához regisztrálnia kell, majd be kell állítania az eszközöket:

### <a name="register-devices-in-bulk"></a>Eszközök tömeges regisztrálása

Ha nagy számú eszközt szeretne regisztrálni a IoT Central alkalmazással, használjon egy CSV-fájlt az eszközök [azonosítóinak és az eszközök nevének importálásához](howto-manage-devices.md#import-devices).

Az importált eszközökhöz tartozó kapcsolódási adatok lekéréséhez [exportáljon egy CSV-fájlt a IoT Central alkalmazásból](howto-manage-devices.md#export-devices).

> [!NOTE]
> Ha szeretné megtudni, hogy az eszközök hogyan csatlakoztathatók a IoT Centralba való első regisztrációja nélkül, tekintse meg a [Kapcsolódás az eszközök első regisztrációja nélkül](#connect-without-registering-devices)című témakört.

### <a name="set-up-your-devices"></a>Az eszközök beállítása

Az eszköz kódjában található exportálási fájl kapcsolati adataival lehetővé teheti, hogy az eszközök csatlakozzanak a IoT, és adatokat küldjenek a IoT Central alkalmazásnak. További információ az eszközök csatlakoztatásáról: [következő lépések](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Eszközök csatlakoztatása X. 509 tanúsítványok használatával

Éles környezetben az X. 509 tanúsítványok használata az ajánlott eszköz hitelesítési mechanizmusa IoT Central számára. További információért lásd: [az eszközök hitelesítése X. 509 hitelesítésszolgáltatói tanúsítványokkal](../../iot-hub/iot-hub-x509ca-overview.md).

Az alábbi lépések azt ismertetik, hogyan csatlakoztathatók az eszközök az X. 509 tanúsítványokkal IoT Centralhoz:

1. A IoT Central alkalmazásban adja hozzá és ellenőrizze az eszköz tanúsítványának létrehozásához használt _köztes vagy gyökér X. 509 tanúsítványt_ :

    - Navigáljon az **adminisztráció > az eszközök közötti kapcsolatok > a tanúsítványok (x. 509)** elemre, és adja hozzá az x. 509 típusú gyökér-vagy köztes tanúsítványt, amelyet Ön használ a levél típusú tanúsítványok létrehozásához.

      ![Kapcsolati beállítások](media/concepts-connectivity/connection-settings.png)

      Ha biztonsági problémákba ütközik, vagy ha az elsődleges tanúsítvány lejár, a másodlagos tanúsítvány használatával csökkentheti az állásidőt. Az elsődleges tanúsítvány frissítésekor továbbra is kiépítheti az eszközöket a másodlagos tanúsítvány használatával.

    - A tanúsítvány tulajdonjogának ellenőrzése biztosítja, hogy a tanúsítvány feltöltője rendelkezik a tanúsítvány titkos kulcsával. A tanúsítvány ellenőrzése:
        - A kód létrehozásához kattintson az **ellenőrző kód** melletti gombra.
        - Hozzon létre egy X. 509 ellenőrző tanúsítványt az előző lépésben létrehozott ellenőrző kóddal. Mentse a tanúsítványt. cer fájlként.
        - Töltse fel az aláírt ellenőrző tanúsítványt, és válassza az **ellenőrzés**lehetőséget.

          ![Kapcsolati beállítások](media/concepts-connectivity/verify-cert.png)

1. Használjon CSV-fájlt a IoT Central alkalmazásban lévő _eszközök importálásához és regisztrálásához_ .

1. _Állítsa be az eszközöket._ Létrehozza a levél tanúsítványait a feltöltött főtanúsítvány használatával. Használja az **eszköz azonosítóját** a levél tanúsítványainak CNAME értékeként. Az eszköz AZONOSÍTÓjának az összes kisbetűsnek kell lennie. Ezután kiépítheti az eszközöket a kiépítési szolgáltatás adataival. Amikor egy eszköz először van bekapcsolva, lekéri a IoT Central alkalmazás kapcsolati adatait a DPS-ból.

### <a name="further-reference"></a>További tudnivalók

- Példa a RaspberryPi megvalósítására [.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Példa a C-beli eszköz-ügyfélre.](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Csak tesztelési célokra

Csak teszteléshez használhatja ezeket a segédprogramokat a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok és az eszközök tanúsítványainak létrehozásához.

- Ha fejlesztői készlet eszközt használ, a [parancssori eszköz](https://aka.ms/iotcentral-docs-dicetool) létrehoz egy hitelesítésszolgáltatói tanúsítványt, amelyet hozzáadhat a IoT Central alkalmazáshoz a tanúsítványok ellenőrzéséhez.

- A következő [parancssori eszköz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) használatával:
  - Hozzon létre egy tanúsítványláncot. Kövesse a GitHub-cikkben a 2. lépést.
  - Mentse a tanúsítványokat. cer fájlként a IoT Central alkalmazásba való feltöltéshez.
  - Az ellenőrző tanúsítvány létrehozásához használja a IoT Central alkalmazásban található ellenőrző kódot. Kövesse a GitHub-cikk 3. lépését.
  - Hozzon létre Leaf-tanúsítványokat az eszközökhöz az eszköz azonosítói alapján az eszköz paraméterének megfelelően. Kövesse a 4. lépést a GitHub-cikkben.

## <a name="connect-without-registering-devices"></a>Csatlakoztatás eszközök regisztrálása nélkül

A fő forgatókönyv IoT Central lehetővé teszi, hogy az OEM-eszközök tömeges előállítsák azokat az eszközöket, amelyek regisztráció nélkül csatlakozhatnak egy IoT Central alkalmazáshoz. A gyártónak megfelelő hitelesítő adatokat kell kiállítania, és konfigurálnia kell az eszközöket a gyárban. Amikor egy eszköz első alkalommal bekapcsol, automatikusan csatlakozik egy IoT Central alkalmazáshoz. Az IoT Central operátornak jóvá kell hagynia az eszközt, mielőtt az adatokat tudja küldeni.

A következő ábra a folyamatot vázolja:

![Kapcsolati beállítások](media/concepts-connectivity/device-connection-flow1.png)

A következő lépések részletesen ismertetik ezt a folyamatot. A lépések kis mértékben eltérnek attól függően, hogy SAS vagy X. 509 tanúsítványokat használ az eszköz hitelesítéséhez:

1. Konfigurálja a kapcsolatok beállításait:

    - **X. 509 tanúsítványok:** [adja hozzá és ellenőrizze a gyökér/köztes tanúsítványt](#connect-devices-using-x509-certificates) , és használja az eszköz tanúsítványának létrehozásához a következő lépésben.
    - **Sas:** Másolja az elsődleges kulcsot. Ez a kulcs a IoT Central-alkalmazáshoz tartozó SAS-kulcs. A kulcs használatával az eszköz SAS-kulcsait a következő lépésben generálhatja.
    ![a kapcsolatok beállításai SAS](media/concepts-connectivity/connection-settings-sas.png)

1. Az eszköz hitelesítő adatainak előállítása
    - **Tanúsítványok X. 509:** Az eszközökhöz tartozó levél-tanúsítványok létrehozása a IoT Central alkalmazáshoz hozzáadott legfelső szintű vagy köztes tanúsítvány használatával. Győződjön meg arról, hogy a kisbetűs **eszköz azonosítóját** használja CNAME-ként a levél tanúsítványokban. Csak tesztelési célra használja ezt a [parancssori eszközt](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) az eszközök tanúsítványainak létrehozásához.
    - **Sas:** Az eszköz SAS-kulcsainak létrehozásához használja ezt a [parancssori eszközt](https://www.npmjs.com/package/dps-keygen) . Használja az előző lépésben a csoport **elsődleges kulcsát** . Az eszköz AZONOSÍTÓjának kisbetűnek kell lennie.

      A [Key Generator segédprogram](https://github.com/Azure/dps-keygen)telepítéséhez futtassa a következő parancsot:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      A következő parancs futtatásával hozhatja elő a kulcsokat a csoportos SAS elsődleges kulcsból:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Az eszközök beállításához a Flash minden eszközt a hatókör- **azonosító**, az **eszköz azonosítója**és az **X. 509 eszköz tanúsítványa** vagy **sas-kulcsa**alapján kell beállítani.

1. Ezután kapcsolja be az eszközt a IoT Central alkalmazáshoz való kapcsolódáshoz. Amikor bekapcsol egy eszközt, először csatlakozik a DPS-hez, hogy beolvassa a IoT Central regisztrációs adatait.

1. A csatlakoztatott eszköz kezdetben egy nem **társított eszközként** jelenik meg a **Device Explorer** oldalon. Az eszköz kiépítési állapota **regisztrálva**van. **Rendelje** hozzá az eszközt a megfelelő eszköz-sablonhoz, és hagyja jóvá az eszközt a IoT Central alkalmazáshoz való kapcsolódáshoz. Az eszköz lekérheti a kapcsolódási karakterláncot a IoT Hubről, és megkezdheti az adatok küldését. Az eszköz üzembe helyezése befejeződött, és a létesítési állapot már **kiépítve**.

## <a name="provisioning-status"></a>Kiépítési állapot

Amikor egy valódi eszköz csatlakozik a IoT Central alkalmazáshoz, a kiépítési állapot a következőképpen változik:

1. Az eszköz üzembe helyezési állapota először **regisztrálva**van. Ez az állapot azt jelenti, hogy az eszköz a IoT Centralban jön létre, és rendelkezik egy eszköz azonosítójával. Az eszköz regisztrálása az alábbiak szerint történik:
    - A **Device Explorer** oldalon új valódi eszköz lesz hozzáadva.
    - A **Device Explorer** lapon az **Importálás** használatával adhat hozzá eszközöket.
    - Az eszköz nem lett manuálisan regisztrálva a **Device Explorer** lapon, de érvényes hitelesítő adatokkal van csatlakoztatva, és nem **társított** eszközként jelenik meg a **Device Explorer** oldalon.

1. Az eszköz kiépítési **állapota akkor változik, ha a** IoT Central alkalmazáshoz az érvényes hitelesítő adatokkal csatlakozó eszköz befejezi a kiépítési lépést. Ebben a lépésben az eszköz lekérdezi a IoT Hubból a kapcsolatok karakterláncát. Az eszköz most már csatlakozhat IoT Hubhoz, és megkezdheti az adatok küldését.

1. Az operátor letilthat egy eszközt. Ha egy eszköz le van tiltva, nem tud adatküldést küldeni a IoT Central alkalmazásnak. A letiltott eszközök kiépítési állapota **Letiltva**. Az operátornak vissza kell állítania az eszközt az adatok küldésének folytatásához. Ha egy operátor feloldja egy eszköz blokkolását, a kiépítési állapot az előző értékre, **regisztrálva** vagy **kiépítve**tér vissza.

## <a name="sdk-support"></a>SDK-támogatás

Az Azure-eszközök SDK-k az eszköz kódjának megvalósítására szolgáló legegyszerűbb megoldást nyújtanak. A következő eszköz-SDK-k érhetők el:

- [C-hez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [Pythonhoz készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [Node. js-hez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [Javához készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK a .NET-hez](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Az SDK szolgáltatásai és IoT Hub kapcsolat

A IoT Hub az összes eszköz kommunikációja a következő IoT Hub kapcsolódási lehetőségeket használja:

- [Eszközről a felhőbe irányuló üzenetkezelés](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Eszköz ikrek](../../iot-hub/iot-hub-devguide-device-twins.md)

Az alábbi táblázat összefoglalja, hogy az Azure IoT Central-eszköz funkciói hogyan képezhetők le IoT Hub funkciókra:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Mérték: telemetria | Eszközről a felhőbe irányuló üzenetkezelés |
| Eszköztulajdonságok | Eszköz kettős jelentett tulajdonságai |
| Beállítások | Az eszköz Twin kívánt és jelentett tulajdonságai |

Ha többet szeretne megtudni az eszköz SDK-k használatáról, tekintse meg a következő cikkek egyikét, például a kódot:

- [Általános Node.js-ügyfél csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-nodejs.md)
- [Málna PI-eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-raspberry-pi-python.md)
- [Csatlakoztasson egy DevDiv-készlet eszközt az Azure IoT Central-alkalmazáshoz](howto-connect-devkit.md).

### <a name="protocols"></a>Protokollok

Az eszköz SDK-k a következő hálózati protokollokat támogatják az IoT hubhoz való csatlakozáshoz:

- MQTT
- AMQP
- HTTPS

További információ ezekről a különbségi protokollokról és az első kiválasztásáról: [kommunikációs protokoll kiválasztása](../../iot-hub/iot-hub-devguide-protocols.md).

Ha az eszköz nem tudja használni a támogatott protokollokat, az Azure IoT Edge használatával végezheti el a protokollok átalakítását. A IoT Edge támogatja a más, az Azure IoT Central alkalmazásban az Edge-be való kiszervezéshez szükséges egyéb hírszerzési forgatókönyveket.

## <a name="security"></a>Biztonság

Az eszközök és az Azure-IoT Central között kicserélt összes adatforgalom titkosítva van. IoT Hub minden olyan eszközről hitelesíti a kérelmet, amely az eszközre irányuló IoT Hub végpontokhoz csatlakozik. A hitelesítő adatok vezetéken keresztüli cseréjének elkerüléséhez az eszköz aláírt jogkivonatokat használ a hitelesítéshez. További információ: [IoT hub hozzáférésének szabályozása](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az eszköz kapcsolatát az Azure IoT Centralban, a következő lépéseket javasoljuk:

- [Fejlesztői készlet-eszköz előkészítése és csatlakoztatása](howto-connect-devkit.md)
- [Raspberry Pi előkészítése és csatlakoztatása](howto-connect-raspberry-pi-python.md)
- [Általános Node.js-ügyfél csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-nodejs.md)
- [C SDK: kiépítési eszköz ügyféloldali SDK-je](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
