---
title: Eszköz csatlakoztatása az Azure IoT Centralban | Microsoft Docs
description: Ez a cikk bemutatja az Azure-beli eszközök csatlakoztatásával kapcsolatos főbb fogalmakat IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 26f05e60761af0b8f0db9508488f28613b82293f
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880240"
---
# <a name="device-connectivity-in-azure-iot-central-preview-features"></a>Eszköz csatlakoztatása az Azure IoT Central (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ez a cikk a Microsoft Azure IoT Central eszközhöz való csatlakoztatásával kapcsolatos főbb fogalmakat ismerteti.

Az Azure IoT Central az [azure IoT hub Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) használatával kezeli az összes eszköz regisztrációját és kapcsolódását.

A DPS használata lehetővé teszi a következőket:

- IoT Central az eszközök nagy léptékű bevezetésének és csatlakoztatásának támogatásához.
- Az eszköz hitelesítő adatainak előállításához és az eszközök offline konfigurálásához az eszközök IoT Central felhasználói felületen keresztül történő regisztrálása nélkül.
- Megosztott hozzáférési aláírások (SAS) használatával csatlakozó eszközök.
- Az iparági szabványnak megfelelő X. 509 tanúsítványok használatával csatlakoztatható eszközök.
- A saját eszközök azonosítóinak használatával regisztrálhatja az eszközöket a IoT Centralban. A saját eszköz-azonosítók használatával egyszerűbbé válik a meglévő Back-Office rendszerekkel való integráció.
- Egyetlen, egységes módszer az eszközök IoT Centralhoz való csatlakoztatására.

Ez a cikk a következő használati eseteket ismerteti:

1. [Egyetlen eszköz gyors csatlakoztatása SAS használatával](#connect-a-single-device)
1. [Eszközök csatlakoztatása nagy méretekben SAS használatával](#connect-devices-at-scale-using-sas)
1. Az [eszközök méretezése az X. 509 tanúsítvánnyal](#connect-devices-using-x509-certificates) . Ez az ajánlott módszer az éles környezetekben.
1. [Csatlakoztatás az eszközök első regisztrációja nélkül](#connect-without-registering-devices)
1. [Eszközök csatlakoztatása a IoT Plug and Play funkcióival](howto-connect-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="connect-a-single-device"></a>Egyetlen eszköz csatlakoztatása

Ez a megközelítés akkor lehet hasznos, ha IoT Central vagy tesztelési eszközökkel kísérletezik. Az eszközhöz tartozó kapcsolódási karakterlánc létrehozásához használhatja a IoT Central-alkalmazás eszköz csatlakoztatási adatait. Részletes útmutatást a következő témakörben talál: [eszköz kapcsolati karakterláncának létrehozása Azure IoT Central-alkalmazáshoz való kapcsolódáshoz](howto-generate-connection-string.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

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

Éles környezetben az X. 509 tanúsítványok használata az ajánlott eszköz hitelesítési mechanizmusa IoT Central számára. További információért lásd: [az eszközök hitelesítése X. 509 hitelesítésszolgáltatói tanúsítványokkal](../iot-hub/iot-hub-x509ca-overview.md).

Az alábbi lépések azt ismertetik, hogyan csatlakoztathatók az eszközök az X. 509 tanúsítványokkal IoT Centralhoz:

1. A IoT Central alkalmazásban adja hozzá és ellenőrizze az eszköz tanúsítványának létrehozásához használt _köztes vagy gyökér X. 509 tanúsítványt_ :

    - Navigáljon az **adminisztráció > az eszközök közötti kapcsolatok > a tanúsítványok (x. 509)** elemre, és adja hozzá az x. 509 típusú gyökér-vagy köztes tanúsítványt, amelyet Ön használ a levél típusú tanúsítványok létrehozásához.

      ![Kapcsolati beállítások](media/concepts-connectivity-pnp/connection-settings.png)

      Ha biztonsági problémákba ütközik, vagy ha az elsődleges tanúsítvány lejár, a másodlagos tanúsítvány használatával csökkentheti az állásidőt. Az elsődleges tanúsítvány frissítésekor továbbra is kiépítheti az eszközöket a másodlagos tanúsítvány használatával.

    - A tanúsítvány tulajdonjogának ellenőrzése biztosítja, hogy a tanúsítvány feltöltője rendelkezik a tanúsítvány titkos kulcsával. A tanúsítvány ellenőrzése:
        - A kód létrehozásához kattintson az **ellenőrző kód** melletti gombra.
        - Hozzon létre egy X. 509 ellenőrző tanúsítványt az előző lépésben létrehozott ellenőrző kóddal. Mentse a tanúsítványt. cer fájlként.
        - Töltse fel az aláírt ellenőrző tanúsítványt, és válassza az **ellenőrzés**lehetőséget.

          ![Kapcsolati beállítások](media/concepts-connectivity-pnp/verify-cert.png)

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

![Kapcsolati beállítások](media/concepts-connectivity-pnp/device-connection-flow1.png)

A következő lépések részletesen ismertetik ezt a folyamatot. A lépések kis mértékben eltérnek attól függően, hogy SAS vagy X. 509 tanúsítványokat használ az eszköz hitelesítéséhez:

1. Konfigurálja a kapcsolatok beállításait:

    - **X. 509 tanúsítványok:** [Adja hozzá és ellenőrizze a gyökér/köztes tanúsítványt](#connect-devices-using-x509-certificates) , és használja az eszköz tanúsítványainak létrehozásához a következő lépésben.
    - **SAS:** Másolja az elsődleges kulcsot. Ez a kulcs a IoT Central-alkalmazáshoz tartozó SAS-kulcs. A kulcs használatával az eszköz SAS-kulcsait a következő lépésben generálhatja.
    ![Kapcsolatbeállítások SAS](media/concepts-connectivity-pnp/connection-settings-sas.png)

1. Az eszköz hitelesítő adatainak előállítása
    - **Tanúsítványok X. 509:** Az eszközökhöz tartozó levél-tanúsítványok létrehozása a IoT Central alkalmazáshoz hozzáadott legfelső szintű vagy köztes tanúsítvány használatával. Győződjön meg arról, hogy a kisbetűs **eszköz azonosítóját** használja CNAME-ként a levél tanúsítványokban. Csak tesztelési célra használja ezt a [parancssori eszközt](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) az eszközök tanúsítványainak létrehozásához.
    - **SAS:** Az eszköz SAS-kulcsainak létrehozásához használja ezt a [parancssori eszközt](https://www.npmjs.com/package/dps-keygen) . Használja az előző lépésben a csoport **elsődleges kulcsát** . Az eszköz AZONOSÍTÓjának kisbetűnek kell lennie.

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

1. A csatlakoztatott eszköz kezdetben az **eszközök** lapon nem társítottként jelenik meg. Az eszköz kiépítési állapota regisztrálvavan. **Telepítse át** az eszközt a megfelelő eszköz sablonba, és hagyja jóvá az eszközt a IoT Central alkalmazáshoz való kapcsolódáshoz. Az eszköz lekérheti a kapcsolódási karakterláncot a IoT Hubről, és megkezdheti az adatok küldését. Az eszköz üzembe helyezése befejeződött, és a létesítési állapot már kiépítve.

## <a name="connect-devices-with-iot-plug-and-play"></a>Eszközök csatlakoztatása a IoT Plug and Play

A IoT Plug and Play és a IoT Central egyik fő funkciója, hogy az eszközök automatikusan társítva legyenek az eszköz kapcsolatain. Az eszköz hitelesítő adataival együtt az eszközök most már elküldhetik a **CapabilityModelId** az eszköz regisztrációs hívásának részeként, IoT Central pedig felderíti és társítja az eszközt. A felderítési folyamat a következő sorrendet követi:

1. Társítja az eszköz sablonját, ha az IoT Central alkalmazásban már közzé van téve.
1. Beolvassa a közzétett és tanúsított képességi modellek nyilvános tárházát.

Alább látható az eszköz által a DPS regisztrációs hívás során elküldött további hasznos adatok formátuma

```javascript
'__iot:interfaces': {
              CapabilityModelId: <this is the URN for the capability model>
          }
```

Ha többet szeretne megtudni a IoT Plug and Play eszköz csatlakoztatásáról, tekintse meg [az IoT Plug and Play eszköz csatlakoztatását](howto-connect-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)ismertető témakört.

> [!NOTE]
> Vegye figyelembe, hogy az automatikus jóváhagyás beállítást engedélyezni kell az eszközök automatikus csatlakoztatásához, a modell felderítéséhez és az adatok küldésének megkezdéséhez.

## <a name="device-status"></a>Eszköz állapota

Ha egy valós eszköz csatlakozik a IoT Central alkalmazáshoz, az eszköz állapota a következőképpen változik:

1. Az eszköz állapota először **regisztrálva**van. Ez az állapot azt jelenti, hogy az eszköz a IoT Centralban jön létre, és rendelkezik egy eszköz azonosítójával. Az eszköz regisztrálása az alábbiak szerint történik:
    - A rendszer új valódi eszközt ad hozzá az **eszközök** lapon.
    - Az **eszközök** lapon az **Importálás** használatával adhat hozzá eszközöket.

1. Az Eszközállapot akkor változik, ha a IoT Central alkalmazáshoz az érvényes hitelesítő adatokkal csatlakozó eszköz befejezi a kiépítési lépést. Ebben a lépésben az eszköz lekérdezi a IoT Hubból a kapcsolatok karakterláncát. Az eszköz most már csatlakozhat IoT Hubhoz, és megkezdheti az adatok küldését.

1. Az operátor letilthat egy eszközt. Ha egy eszköz le van tiltva, nem tud adatküldést küldeni a IoT Central alkalmazásnak. A letiltott eszközök állapota letiltva. Az operátornak vissza kell állítania az eszközt az adatok küldésének folytatásához. Ha egy operátor feloldja egy eszköz zárolását, az állapot visszatér az előző értékre, **regisztrálva** vagy kiépítve.

1. Az eszköz állapota **jóváhagyásra vár** , ami azt jelenti, hogy az **automatikus jóváhagyás** lehetőség le van tiltva, és az IoT Centralhoz csatlakozó összes eszközt explicit módon jóvá kell hagyni egy operátor. Az eszközök lapon nem regisztrált eszközök , de az érvényes hitelesítő adatokkal való kapcsolat az eszköz állapota **jóváhagyásra vár**. Az operátorok a **jóváhagyás** gomb használatával hagyhatják jóvá ezeket az eszközöket az **eszközök** lapról.

1. Az eszköz állapota nem **társított** , ami azt jelenti, hogy a IoT Centralhoz csatlakozó eszközökhöz nem tartozik sablon társítva. Ez általában a következő esetekben fordul elő:
    - A Devices ( **eszközök** ) lapon az **Importálás** használatával bővülnek az eszközök egy készlete az eszköz sablonjának megadása nélkül
    - Azok az eszközök, amelyek nem lettek kézzel regisztrálva az **eszközök** lapon érvényes hitelesítő adatokkal, de nem határozzák meg a sablon azonosítóját a regisztráció során.  
Az operátor a Migrálás gomb használatával rendelheti hozzá az eszközöket egy sablonhoz az **eszközök** lapról.

## <a name="sdk-support"></a>SDK-támogatás

Az Azure-eszközök SDK-k az eszköz kódjának megvalósítására szolgáló legegyszerűbb megoldást nyújtanak. A következő eszköz-SDK-k érhetők el:

- [C-hez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [Pythonhoz készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [Node. js-hez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [Javához készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK a .NET-hez](https://github.com/azure/azure-iot-sdk-csharp)

Minden eszköz egy egyedi kapcsolati karakterlánc használatával csatlakozik, amely azonosítja az eszközt. Egy eszköz csak akkor tud csatlakozni az IoT hubhoz, ahol regisztrálva van. Amikor valódi eszközt hoz létre az Azure IoT Central alkalmazásban, az alkalmazás létrehozza a kapcsolódási karakterlánc létrehozásához szükséges adatokat a használatával `dps-keygen`.

### <a name="sdk-features-and-iot-hub-connectivity"></a>Az SDK szolgáltatásai és IoT Hub kapcsolat

A IoT Hub az összes eszköz kommunikációja a következő IoT Hub kapcsolódási lehetőségeket használja:

- [Eszközről a felhőbe irányuló üzenetkezelés](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Eszköz ikrek](../iot-hub/iot-hub-devguide-device-twins.md)

Az alábbi táblázat összefoglalja, hogy az Azure IoT Central-eszköz funkciói hogyan képezhetők le IoT Hub funkciókra:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Mérési Telemetria | Eszközről a felhőbe irányuló üzenetkezelés |
| Eszköztulajdonságok | Eszköz kettős jelentett tulajdonságai |
| Beállítások | Az eszköz Twin kívánt és jelentett tulajdonságai |

Ha többet szeretne megtudni az eszköz SDK-k használatáról, tekintse meg a [DevDiv-készlet eszköz csatlakoztatása az Azure IoT Central](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) -alkalmazáshoz, például kód című témakört.

### <a name="protocols"></a>Protokollok

Az eszköz SDK-k a következő hálózati protokollokat támogatják az IoT hubhoz való csatlakozáshoz:

- MQTT
- AMQP
- HTTPS

További információ ezekről a különbségi protokollokról és az első kiválasztásáról: [kommunikációs protokoll kiválasztása](../iot-hub/iot-hub-devguide-protocols.md).

Ha az eszköz nem tudja használni a támogatott protokollokat, az Azure IoT Edge használatával végezheti el a protokollok átalakítását. A IoT Edge támogatja a más, az Azure IoT Central alkalmazásban az Edge-be való kiszervezéshez szükséges egyéb hírszerzési forgatókönyveket.

## <a name="security"></a>Biztonság

Az eszközök és az Azure-IoT Central között kicserélt összes adatforgalom titkosítva van. IoT Hub minden olyan eszközről hitelesíti a kérelmet, amely az eszközre irányuló IoT Hub végpontokhoz csatlakozik. A hitelesítő adatok vezetéken keresztüli cseréjének elkerüléséhez az eszköz aláírt jogkivonatokat használ a hitelesítéshez. További információ: [IoT hub hozzáférésének szabályozása](../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az eszköz kapcsolatát az Azure IoT Centralban, a következő lépéseket javasoljuk:

- [IoT Plug and Play eszköz csatlakoztatása](howto-connect-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- [Fejlesztői készlet-eszköz előkészítése és csatlakoztatása](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- [C SDK: Az ügyfél-SDK kiépítési eszköze](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
