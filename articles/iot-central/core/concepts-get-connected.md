---
title: Eszköz csatlakoztatása az Azure IoT Centralban | Microsoft Docs
description: Ez a cikk bemutatja az Azure-beli eszközök csatlakoztatásával kapcsolatos főbb fogalmakat IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: aa6aa7a8d98ae756a65a2618371c320118875c42
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84710439"
---
# <a name="get-connected-to-azure-iot-central"></a>Csatlakozás az Azure IoT Centralhoz

*Ez a cikk az operátorokra és az eszközök fejlesztőire vonatkozik.*

Ez a cikk az eszközök Azure IoT Central-alkalmazáshoz való csatlakoztatásának lehetőségeit ismerteti.

Általában regisztrálnia kell egy eszközt az alkalmazásban, mielőtt kapcsolódni tud. A IoT Central azonban olyan forgatókönyveket támogat, amelyekben az [eszközök az első regisztráció nélkül csatlakozhatnak](#connect-without-registering-devices).

A IoT Central az [Azure IoT hub Device Provisioning szolgáltatást (DPS)](../../iot-dps/about-iot-dps.md) használja a kapcsolódási folyamat kezeléséhez. Az eszköz először csatlakozik egy DPS-végponthoz, hogy lekérje az alkalmazáshoz való csatlakozáshoz szükséges információkat. Belsőleg a IoT Central-alkalmazás egy IoT hub használatával kezeli az eszköz kapcsolatát. A DPS használata lehetővé teszi a következőket:

- IoT Central az eszközök nagy léptékű bevezetésének és csatlakoztatásának támogatásához.
- Az eszköz hitelesítő adatainak előállításához és az eszközök offline konfigurálásához az eszközök IoT Central felhasználói felületen keresztül történő regisztrálása nélkül.
- A saját eszközök azonosítóinak használatával regisztrálhatja az eszközöket a IoT Centralban. A saját eszköz-azonosítók használatával egyszerűbbé válik a meglévő Back-Office rendszerekkel való integráció.
- Egyetlen, egységes módszer az eszközök IoT Centralhoz való csatlakoztatására.

Az eszköz és az alkalmazás közötti kommunikáció biztonságossá tételéhez IoT Central támogatja a közös hozzáférésű aláírásokat (SAS) és az X. 509 tanúsítványokat is. Az X. 509 tanúsítványokat éles környezetekben ajánlott használni.

Ez a cikk a következő használati eseteket ismerteti:

- [Egyetlen eszköz csatlakoztatása SAS használatával](#connect-a-single-device)
- [Eszközök csatlakoztatása nagy méretekben SAS használatával](#connect-devices-at-scale-using-sas)
- [Eszközök csatlakoztatása méretezéssel X. 509 tanúsítványok használatával](#connect-devices-using-x509-certificates) – az éles környezetek javasolt megközelítése.
- [Eszközök csatlakoztatása az első regisztráció nélkül](#connect-without-registering-devices)
- [A DPS egyéni regisztrációt használó eszközök csatlakoztatása](#individual-enrollment-based-device-connectivity)
- [Eszköz automatikus hozzárendelése egy eszköz sablonnal](#automatically-associate-with-a-device-template)

## <a name="connect-a-single-device"></a>Egyetlen eszköz csatlakoztatása

Ez a megközelítés akkor lehet hasznos, ha IoT Central vagy tesztelési eszközökkel kísérletezik. A IoT Central alkalmazásban található eszköz kapcsolat SAS-kulcsaival csatlakoztathat egy eszközt a IoT Central alkalmazáshoz. Másolja az _eszköz sas-kulcsát_ a regisztrált eszközhöz tartozó kapcsolatok adataiból:

![SAS-kulcsok egy adott eszközhöz](./media/concepts-get-connected/single-device-sas.png)

További információ: [Node.js ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central Application](./tutorial-connect-device-nodejs.md) oktatóanyagban.

## <a name="connect-devices-at-scale-using-sas"></a>Eszközök csatlakoztatása nagy méretekben SAS használatával

Ahhoz, hogy az eszközöket az SAS-kulcsok használatával IoT Centralhoz lehessen kötni, regisztrálnia kell, majd be kell állítania az eszközöket:

### <a name="register-devices-in-bulk"></a>Eszközök tömeges regisztrálása

Ha nagy számú eszközt szeretne regisztrálni a IoT Central alkalmazással, használjon egy CSV-fájlt az eszközök [azonosítóinak és az eszközök nevének importálásához](howto-manage-devices.md#import-devices).

Az importált eszközökhöz tartozó kapcsolódási adatok lekéréséhez [exportáljon egy CSV-fájlt a IoT Central alkalmazásból](howto-manage-devices.md#export-devices). Az exportált CSV-fájl tartalmazza az eszközök azonosítóit és a SAS-kulcsokat.

### <a name="set-up-your-devices"></a>Az eszközök beállítása

Az eszköz kódjában található exportálási fájl kapcsolati adataival lehetővé teheti, hogy az eszközök csatlakozzanak a IoT, és adatokat küldjenek a IoT Central alkalmazásnak. Szüksége lesz a DPS- **azonosító hatókörére** is az alkalmazáshoz. Ez az érték az **adminisztráció > eszköz-kapcsolatok**szolgáltatásban található.

> [!NOTE]
> Ha szeretné megtudni, hogy az eszközök hogyan csatlakoztathatók a IoT Centralba való első regisztrációja nélkül, tekintse meg a [Kapcsolódás az eszközök első regisztrációja nélkül](#connect-without-registering-devices)című témakört.

## <a name="connect-devices-using-x509-certificates"></a>Eszközök csatlakoztatása X. 509 tanúsítványok használatával

Éles környezetben az X. 509 tanúsítványok használata az ajánlott eszköz hitelesítési mechanizmusa IoT Central számára. További információért lásd: [az eszközök hitelesítése X. 509 hitelesítésszolgáltatói tanúsítványokkal](../../iot-hub/iot-hub-x509ca-overview.md).

Az X. 509 tanúsítvánnyal rendelkező eszköz csatlakoztatása előtt adjon hozzá egy közbenső vagy gyökérszintű X. 509 tanúsítványt az alkalmazáshoz. Az eszközöknek a gyökér-vagy köztes tanúsítványból generált levél X. 509 tanúsítványokat kell használniuk.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Gyökér-vagy köztes tanúsítvány hozzáadása és ellenőrzése

Navigáljon az **adminisztráció > az eszköz csatlakoztatása > az elsődleges tanúsítvány kezelése** elemre, és adja hozzá az eszköz tanúsítványának létrehozásához használt X. 509 gyökér-vagy köztes tanúsítványt.

![Kapcsolati beállítások](media/concepts-get-connected/manage-x509-certificate.png)

A tanúsítvány tulajdonjogának ellenőrzése biztosítja, hogy a tanúsítványt feltöltő személy rendelkezik a tanúsítvány titkos kulcsával. A tanúsítvány ellenőrzése:

  1. A kód létrehozásához kattintson az **ellenőrző kód** melletti gombra.
  1. Hozzon létre egy X. 509 ellenőrző tanúsítványt az előző lépésben létrehozott ellenőrző kóddal. Mentse a tanúsítványt. cer fájlként.
  1. Töltse fel az aláírt ellenőrző tanúsítványt, és válassza az **ellenőrzés**lehetőséget. A tanúsítvány az ellenőrzés sikeressége után **ellenőrzöttként** van megjelölve.

Ha biztonsági problémákba ütközik, vagy ha az elsődleges tanúsítvány lejár, a másodlagos tanúsítvány használatával csökkentheti az állásidőt. Az elsődleges tanúsítvány frissítésekor továbbra is kiépítheti az eszközöket a másodlagos tanúsítvány használatával.

### <a name="register-and-connect-devices"></a>Eszközök regisztrálása és csatlakoztatása

Az eszközök X. 509 tanúsítvánnyal való tömeges csatlakoztatásához először regisztrálja az eszközeit az alkalmazásban, egy CSV-fájllal, hogy [importálja az eszköz azonosítóit és az eszközök nevét](howto-manage-devices.md#import-devices). Az eszközök azonosítóinak mind kisbetűvel kell rendelkezniük.

X. 509 Leaf-tanúsítványok előállítása az eszközökön a feltöltött gyökér vagy köztes tanúsítvány használatával. Használja az **eszköz azonosítóját** a `CNAME` levél tanúsítványainak értékeként. Az eszköz kódjának szüksége van az alkalmazás **azonosító hatókörének** értékére, az **eszköz azonosítójára**és a megfelelő eszköz tanúsítványára.

#### <a name="sample-device-code"></a>Minta eszköz kódja

Az [Azure IoT Node.JS SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/device/samples/register_x509.js) következő mintája azt mutatja be, hogy egy Node.js-eszköz ügyfele hogyan használ X. 509 levél-tanúsítványt és DPS-t egy IoT Central alkalmazásban való regisztráláshoz:

:::code language="nodejs" source="~/azure-iot-sdk-node/provisioning/device/samples/register_x509.js":::

Az egyenértékű C minta esetében lásd: [prov_dev_client_sample. C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c) az [Azure IoT c kiépítési eszköz ügyféloldali SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)-ban.

### <a name="for-testing-purposes-only"></a>Csak tesztelési célokra

Csak tesztelés esetén a következő segédprogramok segítségével hozhatja ki a gyökér-, köztes és eszköz-tanúsítványokat:

- [Eszközök az Azure IoT Device kiépítési eszköz SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)-hoz: Node.js eszközök gyűjteménye, amelyek X. 509 tanúsítványok és kulcsok létrehozásához és ellenőrzéséhez használhatók.
- Ha fejlesztői készlet eszközt használ, a [parancssori eszköz](https://aka.ms/iotcentral-docs-dicetool) létrehoz egy hitelesítésszolgáltatói tanúsítványt, amelyet hozzáadhat a IoT Central alkalmazáshoz a tanúsítványok ellenőrzéséhez.
- [Tesztelési hitelesítésszolgáltatói tanúsítványok kezelése mintákhoz és oktatóanyagokhoz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): PowerShell-és bash-parancsfájlok gyűjteménye a következőhöz:
  - Hozzon létre egy tanúsítványláncot.
  - Mentse a tanúsítványokat. cer fájlként a IoT Central alkalmazásba való feltöltéshez.
  - Az ellenőrző tanúsítvány létrehozásához használja a IoT Central alkalmazásban található ellenőrző kódot.
  - Hozzon létre Leaf-tanúsítványokat az eszközökhöz az eszköz azonosítói alapján az eszköz paraméterének megfelelően.

## <a name="connect-without-registering-devices"></a>Csatlakoztatás eszközök regisztrálása nélkül

A korábban leírt forgatókönyvek mindegyike megköveteli az eszközök regisztrálását az alkalmazásban a kapcsolódás előtt. A IoT Central azt is lehetővé teszi, hogy a számítógépgyártók tömeges előállítsák az olyan eszközöket, amelyek regisztráció nélkül csatlakozhatnak. Az OEM a megfelelő eszköz hitelesítő adatait hozza létre, és konfigurálja az eszközöket a gyárban. Amikor az ügyfél első alkalommal bekapcsol egy eszközt, az csatlakozik a DPS-hez, amely ezután automatikusan összekapcsolja az eszközt a helyes IoT Central alkalmazással. Egy IoT Central operátornak jóvá kell hagynia az eszközt, mielőtt elkezdi elküldeni az adatokat az alkalmazásnak.

A folyamat némileg eltér attól függően, hogy az eszközök SAS-jogkivonatokat vagy X. 509 tanúsítványokat használnak-e:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>SAS-tokeneket használó eszközök csatlakoztatása regisztráció nélkül

1. Másolja a IoT Central alkalmazás csoportjának elsődleges kulcsát:

    ![Alkalmazáscsoport elsődleges SAS-kulcsa](media/concepts-get-connected/group-sas-keys.png)

1. A [DPS-keygen](https://www.npmjs.com/package/dps-keygen) eszközzel előállíthatja az eszköz sas-kulcsait. Használja az előző lépésben a csoport elsődleges kulcsát. Az eszköz azonosítóinak kisbetűvel kell rendelkezniük:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. Az OEM minden eszközt egy eszköz-AZONOSÍTÓval, egy generált eszköz SAS-kulccsal és az alkalmazás- **azonosító hatókörének** értékével villan fel.

1. Amikor bekapcsol egy eszközt, először csatlakozik a DPS-hez, hogy beolvassa a IoT Central regisztrációs adatait.

    Az eszközhöz kezdetben nincs **társítva** az eszköz az **eszközök** lapon, és nincs hozzárendelve egy eszköz sablonhoz. Az **eszközök** lapon **telepítse át** az eszközt a megfelelő eszköz sablonba. Az eszköz üzembe helyezése befejeződött, az eszköz állapota most **kiépítve**, és az eszköz megkezdheti az adatok küldését.

    Az **adminisztráció > eszköz kapcsolatai** lapon az **automatikus jóváhagyás** beállítás azt határozza meg, hogy kell-e manuálisan jóváhagyni az eszközt az adatok küldésének megkezdése előtt.

    > [!NOTE]
    > Ha meg szeretné tudni, hogyan társítja automatikusan az eszközt egy eszköz sablonnal, tekintse meg az [eszköz automatikus hozzárendelése](#automatically-associate-with-a-device-template)az eszközhöz című témakört.

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>X. 509 tanúsítványokat használó eszközök csatlakoztatása regisztráció nélkül

1. [Adjon hozzá és ellenőrizzen egy root vagy Intermediate X. 509 tanúsítványt](#connect-devices-using-x509-certificates) a IoT Central alkalmazáshoz.

1. Az eszközökhöz tartozó levél-tanúsítványok létrehozása a IoT Central alkalmazáshoz hozzáadott legfelső szintű vagy köztes tanúsítvány használatával. A kisbetűket használó eszközök azonosítóit használja a `CNAME` levél tanúsítványainak.

1. Az OEM minden eszközt egy eszköz-AZONOSÍTÓval, egy generált levél X. 509 tanúsítvánnyal és az alkalmazás- **azonosító hatókörének** értékével villan fel.

1. Amikor bekapcsol egy eszközt, először csatlakozik a DPS-hez, hogy beolvassa a IoT Central regisztrációs adatait.

    Az eszközhöz kezdetben nincs **társítva** az eszköz az **eszközök** lapon, és nincs hozzárendelve egy eszköz sablonhoz. Az **eszközök** lapon **telepítse át** az eszközt a megfelelő eszköz sablonba. Az eszköz üzembe helyezése befejeződött, az eszköz állapota most **kiépítve**, és az eszköz megkezdheti az adatok küldését.

    Az **adminisztráció > eszköz kapcsolatai** lapon az **automatikus jóváhagyás** beállítás azt határozza meg, hogy kell-e manuálisan jóváhagyni az eszközt az adatok küldésének megkezdése előtt.

    > [!NOTE]
    > Ha meg szeretné tudni, hogyan társítja automatikusan az eszközt egy eszköz sablonnal, tekintse meg az [eszköz automatikus hozzárendelése](#automatically-associate-with-a-device-template)az eszközhöz című témakört.

## <a name="individual-enrollment-based-device-connectivity"></a>Egyéni regisztráció-alapú eszközök kapcsolata

Azok az ügyfelek, akik a saját hitelesítési hitelesítő adataival rendelkező eszközöket csatlakoztatnak, egyéni regisztrációkat használhatnak. Az egyéni regisztráció egyetlen, a csatlakozáshoz engedélyezett eszközre vonatkozó bejegyzés. Az egyéni regisztrációk X. 509 levél-tanúsítványokat vagy SAS-jogkivonatokat (fizikai vagy virtuális platformmegbízhatósági modulból) is használhatnak igazolási mechanizmusként. Az eszköz azonosítója (más néven regisztrációs azonosító) egy egyéni regisztrációban alfanumerikus, kisbetűs, és tartalmazhat kötőjeleket. További információ: [DPS egyéni regisztráció](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Amikor egyéni regisztrációt hoz létre egy eszközhöz, az a IoT Central alkalmazás alapértelmezett csoportos regisztrálási lehetőségeivel szemben elsőbbséget élvez.

### <a name="create-individual-enrollments"></a>Egyéni regisztrációk létrehozása

IoT Central a következő igazolási mechanizmusokat támogatja az egyéni regisztrációk esetén:

- **Szimmetrikus kulcs igazolása:** A szimmetrikus kulcs igazolása egyszerű módszer egy eszköz a DPS-példánnyal való hitelesítésére. Szimmetrikus kulcsokat használó egyéni regisztráció létrehozásához nyissa meg az **eszköz kapcsolat** lapját, válassza az **Egyéni regisztráció** lehetőséget a kapcsolódási módszerként, valamint a **közös hozzáférésű aláírást (SAS)** mechanizmusként. Adja meg a Base64 kódolású elsődleges és másodlagos kulcsot, és mentse a módosításokat. Az eszköz csatlakoztatásához használja az **azonosító hatókörét**, az **eszköz azonosítóját**és az elsődleges vagy a másodlagos kulcsot.

    > [!TIP]
    > A teszteléshez az **OpenSSL** használatával Base64 kódolású kulcsokat hozhatja elő:`openssl rand -base64 64`

- **X. 509 tanúsítványok:** X. 509 tanúsítvánnyal rendelkező egyéni regisztráció létrehozásához nyissa meg az **eszköz kapcsolódása** lapot, válassza az **Egyéni regisztráció** lehetőséget a kapcsolódási módszerként, valamint a **tanúsítványok (X. 509)** módszert. Az egyéni beléptetési bejegyzésekhez használt eszközök tanúsítványainak követelménye, hogy a kiállító és a tulajdonos CN az eszköz AZONOSÍTÓját adja meg.

    > [!TIP]
    > A teszteléshez használhatja [Az Azure IoT Device kiépítési eszköz SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) -t a Node.jsszámára egy önaláírt tanúsítvány létrehozásához:`node create_test_cert.js device "mytestdevice"`

- **Platformmegbízhatósági modul (TPM) igazolása:** A [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) a hardveres biztonsági modul típusa. Az eszköz csatlakoztatásának egyik legbiztonságosabb módja a TPM használata. Ez a cikk feltételezi, hogy diszkrét, belső vezérlőprogramot vagy integrált TPM-t használ. A szoftveresen emulált TPM a prototípus-készítéshez és a teszteléshez megfelelőek, de nem biztosítják ugyanazt a biztonsági szintet, mint a diszkrét, belső vezérlőprogram vagy integrált TPM. Ne használja éles környezetben a szoftver TPM. TPM-t használó egyéni regisztráció létrehozásához nyissa meg az **eszköz kapcsolódása** lapot, válassza az **Egyéni regisztráció** a kapcsolódási módszerként és a **TPM** mechanizmusként lehetőséget. Adja meg a TPM-záradék kulcsát, és mentse az eszköz csatlakoztatási adatait.

## <a name="automatically-associate-with-a-device-template"></a>Automatikus hozzárendelés egy eszköz sablonnal

A IoT Central egyik fő funkciója az, hogy az eszközök automatikusan az eszköz kapcsolatain legyenek hozzárendelve. Az eszköz hitelesítő adataival együtt az eszközök **CapabilityModelId** is küldhetnek az eszköz regisztrációs hívásának részeként. A **CapabilityModelID** egy urn, amely az eszköz által megvalósított képességi modellt azonosítja. A IoT Central alkalmazás a **CapabilityModelID** segítségével azonosítja a használni kívánt sablont, majd automatikusan társítja az eszközt az eszköz sablonnal. A felderítési folyamat a következőképpen működik:

1. Ha az eszköz sablonja már közzé van téve a IoT Central alkalmazásban, az eszköz társítva van az eszköz sablonnal.
1. Az előre hitelesített IoT Plug and Play eszközök esetében, ha az eszköz sablonja még nincs közzétéve a IoT Central alkalmazásban, a rendszer beolvassa az eszköz sablonját a nyilvános tárházból.

Az alábbi kódrészletek a további hasznos adatok formátumát jelenítik meg, amelyeket az eszköznek a DPS regisztrációs hívása során kell elküldeni az automatikus társítás működéséhez.

Ez a formátum olyan eszközök esetében, amelyek az általánosan elérhető SDK-t használják, amely nem támogatja a IoT Plug and Play:

```javascript
    iotcModelId: '< this is the URN for the capability model>';
```

A IoT Plug and Playt támogató nyilvános előzetes verziójú SDK-t használó eszközök formátuma:

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> Az **automatikus jóváhagyás** lehetőséget a **felügyelet > az eszköz kapcsolatát** engedélyezni kell az eszközök automatikus csatlakoztatásához, az eszköz-sablon felderítéséhez és az adatok küldésének megkezdéséhez.

## <a name="device-status-values"></a>Eszköz állapotának értékei

Ha egy valós eszköz csatlakozik a IoT Central alkalmazáshoz, az eszköz állapota a következőképpen változik:

1. Az eszköz állapota először **regisztrálva**van. Ez az állapot azt jelenti, hogy az eszköz a IoT Centralban jön létre, és rendelkezik egy eszköz azonosítójával. Az eszköz regisztrálása az alábbiak szerint történik:
    - A rendszer új valódi eszközt ad hozzá az **eszközök** lapon.
    - Az **eszközök** lapon az **Importálás** használatával adhat hozzá eszközöket.

1. Az **Eszközállapot akkor változik, ha a** IoT Central alkalmazáshoz az érvényes hitelesítő adatokkal csatlakozó eszköz befejezi a kiépítési lépést. Ebben a lépésben az eszköz a DPS használatával automatikusan lekéri a IoT Central alkalmazás által használt IoT Hub egy kapcsolódási karakterláncot. Az eszköz most már csatlakozhat IoT Centralhoz, és megkezdheti az adatok küldését.

1. Az operátor letilthat egy eszközt. Ha egy eszköz le van tiltva, nem tud adatküldést küldeni a IoT Central alkalmazásnak. A letiltott eszközök állapota **Letiltva**. Az operátornak vissza kell állítania az eszközt az adatok küldésének folytatásához. Ha egy operátor feloldja egy eszköz zárolását, az állapot visszatér az előző értékre, **regisztrálva** vagy **kiépítve**.

1. Ha az eszköz **jóváhagyásra vár**, azt jelenti, hogy az **automatikus jóváhagyás** lehetőség le van tiltva. Az operátornak explicit módon jóvá kell hagynia az eszközt az adatok küldésének megkezdése előtt. Az **Eszközök lapon nem** regisztrált eszközök, de az érvényes hitelesítő adatokkal való kapcsolat az eszköz állapota **jóváhagyásra vár**. Az operátorok a **jóváhagyás** gomb használatával hagyhatják jóvá ezeket az eszközöket az **eszközök** lapról.

1. Ha az eszköz állapota nincs **társítva**, az azt jelenti, hogy a IoT Centralhoz csatlakozó eszközhöz nincs társítva eszköz sablonja. Ez a helyzet általában a következő esetekben fordul elő:

    - A Devices ( **eszközök** ) lapon található **Importálás** használatával az eszközök egy készlete lesz hozzáadva az eszköz sablonjának megadása nélkül.
    - Egy eszköz regisztrálása manuálisan megtörténjen az **eszközök** lapon az eszköz sablonjának meghatározása nélkül. Az eszköz ezután érvényes hitelesítő adatokkal csatlakozik.  

    Az operátor az **eszközök** lapon az **áttelepítés** gomb használatával rendelheti hozzá az eszközt az eszközhöz.

## <a name="best-practices"></a>Ajánlott eljárások

Ne maradjon meg vagy ne gyorsítótárazza azt az eszköz-kapcsolati karakterláncot, amelyet a DPS az eszköz első csatlakoztatásakor ad vissza. Ha újra szeretne csatlakozni egy eszközhöz, folytassa a szabványos eszköz regisztrációs folyamatán a megfelelő eszköz kapcsolati karakterlánc beszerzéséhez. Ha az eszköz gyorsítótárazza a kapcsolódási karakterláncot, az eszköz szoftvere egy elavult kapcsolódási karakterláncot használ, ha IoT Central frissíti a mögöttes Azure IoT hub-t.

## <a name="sdk-support"></a>SDK-támogatás

Az Azure-eszközök SDK-k az eszköz kódjának megvalósítására szolgáló legegyszerűbb megoldást nyújtanak. A következő eszköz-SDK-k érhetők el:

- [C nyelvhez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [Pythonhoz készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [Node.js-hez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [Javához készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [.NET-hez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Az SDK szolgáltatásai és IoT Hub kapcsolat

A IoT Hub az összes eszköz kommunikációja a következő IoT Hub kapcsolódási lehetőségeket használja:

- [Eszközről a felhőbe irányuló üzenetkezelés](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Eszköz ikrek](../../iot-hub/iot-hub-devguide-device-twins.md)

Az alábbi táblázat összefoglalja, hogy az Azure IoT Central-eszköz funkciói hogyan képezhetők le IoT Hub funkciókra:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetria | Eszközről a felhőbe irányuló üzenetkezelés |
| Tulajdonság | Eszköz kettős jelentett tulajdonságai |
| Tulajdonság (írható) | Az eszköz Twin kívánt és jelentett tulajdonságai |
| Parancs | Közvetlen metódusok |

Ha többet szeretne megtudni az eszköz SDK-k használatáról, tekintse meg a [MXChip IoT fejlesztői készlet-eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-devkit.md) például kód című témakört.

### <a name="protocols"></a>Protokollok

Az eszköz SDK-k a következő hálózati protokollokat támogatják az IoT hubhoz való csatlakozáshoz:

- MQTT
- AMQP
- HTTPS

További információ ezekről a különbségi protokollokról és az első kiválasztásáról: [kommunikációs protokoll kiválasztása](../../iot-hub/iot-hub-devguide-protocols.md).

Ha az eszköz nem tudja használni a támogatott protokollokat, az Azure IoT Edge használatával végezheti el a protokollok átalakítását. A IoT Edge támogatja a más, az Azure IoT Central alkalmazásban az Edge-be való kiszervezéshez szükséges egyéb hírszerzési forgatókönyveket.

## <a name="security"></a>Biztonság

Az eszközök és az Azure-IoT Central között kicserélt összes adatforgalom titkosítva van. IoT Hub minden olyan eszközről hitelesíti a kérelmet, amely az eszközre irányuló IoT Hub végpontokhoz csatlakozik. A hitelesítő adatok vezetéken keresztüli cseréjének elkerüléséhez az eszköz aláírt jogkivonatokat használ a hitelesítéshez. További információ: [IoT hub hozzáférésének szabályozása](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>További lépések

Ha Ön egy eszköz fejlesztője, néhány javasolt lépés a következő:

- Ismerje meg, hogyan [figyelheti az eszközök kapcsolatát az Azure CLI használatával](./howto-monitor-devices-azure-cli.md)
- Ismerje meg, hogyan [határozhat meg új IoT-típust az Azure IoT Central-alkalmazásban](./howto-set-up-template.md)
- További információ a [Azure IoT Edge eszközökről és az Azure IoT Central](./concepts-iot-edge.md)
