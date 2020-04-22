---
title: Eszközkapcsolat az Azure IoT Centralban | Microsoft dokumentumok
description: Ez a cikk az Azure IoT Central eszközkapcsolatával kapcsolatos legfontosabb fogalmakat ismerteti
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
ms.openlocfilehash: f37b070c74abd8511fc597f9b159312d91281083
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759112"
---
# <a name="get-connected-to-azure-iot-central"></a>Csatlakozzon az Azure IoT Centralhoz

*Ez a cikk az operátorokra és az eszközfejlesztőkre vonatkozik.*

Ez a cikk ismerteti az eszközöket egy Azure IoT Central alkalmazáshoz való csatlakoztatásának lehetőségei.

Általában regisztrálnia kell egy eszközt az alkalmazásban, mielőtt csatlakozhatna. Az IoT Central azonban támogatja azokat a forgatókönyveket, amelyekben [az eszközök anélkül csatlakozhatnak, hogy először regisztrálnák](#connect-without-registering-devices)őket.

Az IoT Central az [Azure IoT Hub-eszközkiépítési szolgáltatás (DPS)](../../iot-dps/about-iot-dps.md) segítségével kezeli a csatlakozási folyamatot. Az eszköz először csatlakozik a DPS-végponthoz, hogy lekérje az alkalmazáshoz való csatlakozáshoz szükséges információkat. Belsőleg az IoT Central-alkalmazás egy IoT hubot használ az eszközkapcsolat kezeléséhez. A DPS használata lehetővé teszi:

- IoT Central támogatja a bevezetési és csatlakozó eszközök nagy méretekben.
- Az eszközök hitelesítő adatainak létrehozásához és az eszközök offline konfigurálásához az eszközök IoT Central felhasználói felületen keresztültörténő regisztrálása nélkül.
- Használhatja a saját eszközazonosítók eszközök regisztrálásához az IoT Central. A saját eszközazonosítók használata leegyszerűsíti a meglévő háttérirodai rendszerekkel való integrációt.
- Egyetlen, egységes módja az eszközök ioT Centralhoz való csatlakoztatásának.

Az eszköz és az alkalmazás közötti kommunikáció biztonságossá tétele érdekében az IoT Central támogatja a megosztott hozzáférésű aláírásokat (SAS) és az X.509-tanúsítványokat. Az X.509 tanúsítványok használata éles környezetben ajánlott.

Ez a cikk a következő használati eseteket ismerteti:

- [Egyetlen eszköz csatlakoztatása SAS használatával](#connect-a-single-device)
- [Eszközök csatlakoztatása a SAS használatával](#connect-devices-at-scale-using-sas)
- [Az eszközök nagy méretekben való csatlakoztatása X.509-es tanúsítványokkal](#connect-devices-using-x509-certificates) – ez az éles környezetek ajánlott megközelítése.
- [Eszközök csatlakoztatása regisztráció nélkül](#connect-without-registering-devices)
- [A DPS egyéni regisztrációit használó eszközök csatlakoztatása](#individual-enrollment-based-device-connectivity)
- [Eszközök csatlakoztatása az IoT Plug and Play (előzetes verzió) funkcióival](#connect-devices-with-iot-plug-and-play-preview)

## <a name="connect-a-single-device"></a>Egyetlen eszköz csatlakoztatása

Ez a megközelítés akkor hasznos, ha az IoT Central vagy a tesztelési eszközök kísérletezik. Az IoT Central alkalmazásból származó eszközkapcsolat SAS-kulcsaival csatlakoztathat egy eszközt az IoT Central-alkalmazáshoz. Másolja az _eszköz SAS-kulcsát_ a regisztrált eszköz csatlakozási adataiból:

![SAS-kulcsok egy adott eszközhöz](./media/concepts-get-connected/single-device-sas.png)

További információkért tekintse meg a [Node.js ügyfélalkalmazás létrehozása és csatlakoztatása az Azure IoT Central alkalmazás](./tutorial-connect-device.md) oktatóanyagát.

## <a name="connect-devices-at-scale-using-sas"></a>Eszközök csatlakoztatása a SAS használatával

Ha az eszközöket nagy méretekben szeretné csatlakoztatni az IoT Centralhoz A SAS-kulcsok használatával, regisztrálnia kell, majd be kell állítania az eszközöket:

### <a name="register-devices-in-bulk"></a>Eszközök regisztrálása ömlesztve

Ha nagyszámú eszközt szeretne regisztrálni az IoT Central alkalmazással, használjon CSV-fájlt [az eszközazonosítók és az eszköznevek importálásához.](howto-manage-devices.md#import-devices)

Az importált eszközök kapcsolati adatainak beolvasásához [exportáljon egy CSV-fájlt az IoT Central alkalmazásból.](howto-manage-devices.md#export-devices) Az exportált CSV-fájl tartalmazza az eszközazonosítókat és a SAS-kulcsokat.

### <a name="set-up-your-devices"></a>Az eszközök beállítása

Az eszközkódban lévő exportálási fájlból származó kapcsolati adatok segítségével lehetővé teszi az eszközök számára, hogy adatokat csatlakoztassanak és küldjenek az IoT-nek az IoT-alkalmazásba. Az **alkalmazásdps-azonosító hatókörére** is szüksége van. Ezt az értéket a **Felügyeleti > Eszközkapcsolat ban**találja.

> [!NOTE]
> Ha meg szeretné tudni, hogyan csatlakoztathatja az eszközöket anélkül, hogy először regisztrálna őket az IoT Centralban, olvassa el a [Csatlakozás az eszközök regisztrálása nélkül.](#connect-without-registering-devices)

## <a name="connect-devices-using-x509-certificates"></a>Eszközök csatlakoztatása X.509-tanúsítványokkal

Éles környezetben az X.509-es tanúsítványok használata az IoT Central ajánlott eszközhitelesítési mechanizmusa. További információ: [Device Authentication using X.509 CA Certificates](../../iot-hub/iot-hub-x509ca-overview.md).

Mielőtt x.509-es tanúsítvánnyal rendelkező eszközt csatlakoztatna, vegyen fel és ellenőrizzen egy köztes vagy gyökér X.509 tanúsítványt az alkalmazásban. Az eszközöknek a legfelső szintű vagy a köztes tanúsítványból létrehozott X.509-es levéltanúsítványokat kell használniuk.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Legfelső szintű vagy köztes tanúsítvány hozzáadása és ellenőrzése

Nyissa meg **a Felügyeleti > az elsődleges tanúsítvány kezelése >,** és adja hozzá az eszköztanúsítványok létrehozásához használt X.509-es legfelső vagy köztes tanúsítványt.

![Kapcsolati beállítások](media/concepts-get-connected/manage-x509-certificate.png)

A tanúsítvány tulajdonjogának ellenőrzése biztosítja, hogy a tanúsítványt feltöltő személy rendelkezik a tanúsítvány személyes kulccsal. A tanúsítvány ellenőrzése:

  1. A kód létrehozásához válassza az **Ellenőrző kód** melletti gombot.
  1. Hozzon létre egy X.509-es ellenőrző tanúsítványt az előző lépésben létrehozott ellenőrző kóddal. Mentse a tanúsítványt .cer fájlként.
  1. Töltse fel az aláírt igazolási tanúsítványt, és válassza **az Ellenőrzés lehetőséget.** A tanúsítvány **ellenőrzöttként** van megjelölve, ha az ellenőrzés sikeres.

Ha biztonsági rés tátong, vagy az elsődleges tanúsítvány lejár, használja a másodlagos tanúsítványt az állásidő csökkentéséhez. Az elsődleges tanúsítvány frissítése közben továbbra is üzembe helyezheti az eszközöket a másodlagos tanúsítvánnyal.

### <a name="register-and-connect-devices"></a>Eszközök regisztrálása és csatlakoztatása

Az Eszközök X.509-es tanúsítványokkal történő tömeges csatlakoztatásához először regisztrálja az eszközöket az alkalmazásban egy CSV-fájl segítségével [az eszközazonosítók és az eszköznevek importálásához.](howto-manage-devices.md#import-devices) Az eszközazonosítóknak kisbetűsnek kell lenniük.

X.509 levéltanúsítványokat hozhat létre az eszközökszámára a feltöltött legfelső szintű vagy köztes tanúsítvány használatával. Használja az **eszközazonosítót** a `CNAME` levéltanúsítványok ban megadott értékként. Az eszközkódnak szüksége van az alkalmazás **azonosító hatókörének** értékére, az **eszközazonosítóra**és a megfelelő eszköztanúsítványra.

### <a name="for-testing-purposes-only"></a>Csak tesztelési célokra

Csak tesztelésre használhatja a következő segédprogramokat gyökér-, köztes és eszköztanúsítványok létrehozásához:

- [Az Azure IoT-eszközkiépítési eszköz SDK:](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)Node.js eszközök gyűjteménye, amely x.509-es tanúsítványok és kulcsok létrehozásához és ellenőrzéséhez használható.
- Ha DevKit-eszközt használ, ez a [parancssori eszköz](https://aka.ms/iotcentral-docs-dicetool) létrehoz egy hitelesítésszolgáltatói tanúsítványt, amelyet hozzáadhat az IoT Central alkalmazáshoz a tanúsítványok ellenőrzéséhez.
- [Teszthitelesítésszolgáltatói tanúsítványok kezelése mintákhoz és oktatóanyagokhoz:](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)PowerShell- és Bash-parancsfájlok gyűjteménye:
  - Hozzon létre egy tanúsítványláncot.
  - Mentse a tanúsítványokat .cer fájlként az IoT Central alkalmazásba való feltöltéshez.
  - Használja az IoT Central alkalmazás ellenőrző kódját az ellenőrzési tanúsítvány létrehozásához.
  - Hozzon létre levéltanúsítványokat az eszközökszámára az eszköz azonosítóinak paramétereként használva.

### <a name="further-reference"></a>További hivatkozás

- [Minta implementációja a RaspberryPi-hez](https://aka.ms/iotcentral-docs-Raspi-releases)
- [Mintaeszköz-ügyfél C-ben](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-registering-devices"></a>Csatlakozás eszközök regisztrálása nélkül

A korábban leírt forgatókönyvek mindegyike megköveteli, hogy regisztrálja az eszközöket az alkalmazásban, mielőtt azok csatlakoztatása. Az IoT Central lehetővé teszi a számítógépgyártók számára, hogy tömeggyártású eszközöket gyártsanak, amelyek anélkül tudnak csatlakozni, hogy először regisztrálnák őket. A oem megfelelő eszközhitelesítő adatokat hoz létre, és konfigurálja az eszközöket a gyárban. Amikor egy ügyfél először kapcsolja be az eszközt, csatlakozik a DPS-hez, amely automatikusan csatlakoztatja az eszközt a megfelelő IoT Central alkalmazáshoz. Az IoT Central operátornak jóvá kell hagynia az eszközt, mielőtt adatokat küldene az alkalmazásnak.

A folyamat némileg eltér attól függően, hogy az eszközök SAS-jogkivonatokat vagy X.509-es tanúsítványokat használnak-e:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>SAS-jogkivonatokat használó eszközök csatlakoztatása regisztráció nélkül

1. Másolja az IoT Central alkalmazás elsődleges kulcsának másolását:

    ![Alkalmazáscsoport elsődleges SAS-kulcsa](media/concepts-get-connected/group-sas-keys.png)

1. Használja a [dps-keygen](https://www.npmjs.com/package/dps-keygen) eszközt az eszköz SAS-kulcsainak létrehozásához. Használja a csoport elsődleges kulcsát az előző lépésben. Az eszközazonosítóknak kisbetűseknek kell lenniük:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. Az oem villog minden eszköz egy eszköz azonosító, egy generált eszköz SAS kulcs, és az alkalmazás **azonosító hatókör** értéke.

1. Amikor bekapcsol egy eszközt, először csatlakozik a DPS-hez az IoT Central regisztrációs adatainak lekéréséhez.

    Az eszköz kezdetben rendelkezik egy eszköz állapota **Nincs társítva** az **Eszközök** lapon, és nincs hozzárendelve egy eszközsablonhoz. Az **Eszközök** lapon **telepítse át** az eszközt a megfelelő eszközsablonra. Az eszközkiépítés befejeződött, az eszköz állapota most **kivan építve,** és az eszköz megkezdheti az adatok küldését.

    A **Felügyeleti > eszközkapcsolat** lapján az Automatikus jóváhagyás beállítás **szabályozza,** hogy manuálisan kell-e jóváhagynia az eszközt az adatok küldésének megkezdése előtt.

    > [!NOTE]
    > Ha meg szeretné tudni, hogy egy eszköz automatikusan hogyan kapcsolódik egy eszközsablonhoz, olvassa el az [Eszközök csatlakoztatása IoT Plug and Play (előzetes verzió) című témakört.](#connect-devices-with-iot-plug-and-play-preview)

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Az X.509-es tanúsítványokat használó eszközök csatlakoztatása regisztráció nélkül

1. [Gyökér- vagy köztes X.509-es tanúsítvány hozzáadása és ellenőrzése](#connect-devices-using-x509-certificates) az IoT Central alkalmazáshoz. (#connect-eszközök-használó-x509-tanúsítványok)

1. Az IoT Central-alkalmazáshoz hozzáadott gyökér- vagy köztes tanúsítvány használatával hozza létre az eszközök levéltanúsítványait. Használjon kisméretű eszközazonosítókat `CNAME` a levéltanúsítványokban.

1. Az oem villog minden eszköz egy eszköz azonosító, a generált bal X.509 tanúsítvány, és az alkalmazás **azonosító hatókör** értéke.

1. Amikor bekapcsol egy eszközt, először csatlakozik a DPS-hez az IoT Central regisztrációs adatainak lekéréséhez.

    Az eszköz kezdetben rendelkezik egy eszköz állapota **Nincs társítva** az **Eszközök** lapon, és nincs hozzárendelve egy eszközsablonhoz. Az **Eszközök** lapon **telepítse át** az eszközt a megfelelő eszközsablonra. Az eszközkiépítés befejeződött, az eszköz állapota most **kivan építve,** és az eszköz megkezdheti az adatok küldését.

    A **Felügyeleti > eszközkapcsolat** lapján az Automatikus jóváhagyás beállítás **szabályozza,** hogy manuálisan kell-e jóváhagynia az eszközt az adatok küldésének megkezdése előtt.

    > [!NOTE]
    > Ha meg szeretné tudni, hogy egy eszköz automatikusan hogyan kapcsolódik egy eszközsablonhoz, olvassa el az [Eszközök csatlakoztatása IoT Plug and Play (előzetes verzió) című témakört.](#connect-devices-with-iot-plug-and-play-preview)

## <a name="individual-enrollment-based-device-connectivity"></a>Egyéni beléptetésen alapuló eszközkapcsolat

Azoknak az ügyfeleknek, amelyek saját hitelesítési hitelesítő adatokkal rendelkeznek, egyéni regisztrációkat kell használniuk. Az egyéni regisztráció egyetlen eszköz, amely csatlakozhat. Az egyéni beléptetések x.509 levéltanúsítványokat vagy SAS-jogkivonatokat (fizikai vagy virtuális platformmodulból) használhatnak tanúsítványmechanizmusokként. Az egyes regisztrációkban az eszközazonosító (más néven regisztrációs azonosító) alfanumerikus, kisbetűs, és kötőjeleket tartalmazhat. További információ: [DPS egyéni regisztráció](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Amikor egyéni regisztrációt hoz létre egy eszközhöz, az elsőbbséget élvez az IoT Central alkalmazás alapértelmezett csoportregisztrációs beállításaival szemben.

### <a name="creating-individual-enrollments"></a>Egyéni regisztrációk létrehozása

Az IoT Central a következő tanúsítványmechanizmusokat támogatja az egyéni regisztrációkhoz:

- **Szimmetrikus kulcsigazolás:** A szimmetrikus kulcsigazolás egy egyszerű módszer egy eszköz hitelesítésére a DPS-példányokkal. Szimmetrikus kulcsokat használó egyéni regisztráció létrehozásához nyissa meg az **Eszközkapcsolat** lapot, válassza az **Egyéni regisztráció** csatlakozási módszerként lehetőséget, a **megosztott hozzáférésű aláírás (SAS)** pedig a mechanizmust. Írja be a base64 kódolású elsődleges és másodlagos kulcsokat, és mentse a módosításokat. Használja az **Azonosító hatókört**, **az Eszközazonosítót**és az elsődleges vagy másodlagos kulcsot az eszköz csatlakoztatásához.

    > [!TIP]
    > A teszteléshez az **OpenSSL** segítségével létrehozhat base64 kódolású kulcsokat:`openssl rand -base64 64`

- **X.509 bizonyítványok:** Ha egyéni regisztrációt szeretne létrehozni az X.509-es tanúsítványokkal, nyissa meg az **Eszközkapcsolat** lapot, válassza az **Egyéni igénylés** lehetőséget csatlakozási módként, és a **Tanúsítványok (X.509)** lehetőséget. Az egyéni beléptetési bejegyzéshez használt eszköztanúsítványokban követelmény, hogy a kibocsátó és a tulajdonos KN az eszközazonosítóra legyen beállítva.

    > [!TIP]
    > A teszteléshez [használhatja az Eszközöket az Azure IoT-eszköz kiépítési eszköz SDK node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) önaláírt tanúsítvány létrehozásához:`node create_test_cert.js device "mytestdevice"`

- **Platformmegbízhatósági modul (TPM) tanúsítványa:** A [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) egyfajta hardveres biztonsági modul. A TPM használata az egyik legbiztonságosabb módja az eszköz csatlakoztatásának. Ez a cikk feltételezi, hogy különálló, belső vezérlőprogramot vagy integrált TPM-et használ. A szoftveremulátortutánozta TPM-ek kiválóan alkalmasak prototípus-készítésre vagy tesztelésre, de nem nyújtanak ugyanolyan szintű biztonságot, mint a különálló, a belső vezérlőprogram vagy az integrált TPM-ek. Ne használjon szoftveres TPM-eket éles környezetben. Ha tpm-et használó egyéni regisztrációt szeretne létrehozni, nyissa meg az **Eszközkapcsolat** lapot, válassza az **Egyéni regisztráció** csatlakozási módszerként lehetőséget, a **TPM pedig** a mechanizmust. Adja meg a TPM-ellenőrző kulcsot, és mentse az eszköz kapcsolatának adatait.

## <a name="connect-devices-with-iot-plug-and-play-preview"></a>Eszközök csatlakoztatása IoT Plug and Play segítségével (előzetes verzió)

Az IoT Plug and Play (előzetes verzió) ioT Central egyik legfontosabb funkciója az eszközsablonok automatikus társítása az eszközkapcsolaton. Az eszköz hitelesítő adataival együtt az eszközök most már elküldhetik a **CapabilityModelId-et** az eszköz regisztrációs hívásának részeként. Ez a funkció lehetővé teszi az IoT Central számára az eszközsablon felderítését és társítást az eszközhöz. A felderítési folyamat a következőképpen működik:

1. Társítja az eszközsablont, ha már közzé lett téve az IoT Central alkalmazásban.
1. Lekéri a nyilvános tárházból közzétett és hitelesített képességmodellek.

Az alábbiakban a formátum a további hasznos teher a készülék küld során a DPS regisztrációs hívás

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> Vegye figyelembe, hogy a **felügyeleti > eszközkapcsolat** **automatikus jóváhagyása** beállítást engedélyezni kell ahhoz, hogy az eszközök automatikusan csatlakozzanak, felderítsék az eszközsablont, és megkezdhessék az adatok küldését.

## <a name="device-status-values"></a>Eszköz állapotértékei

Amikor egy valódi eszköz csatlakozik az IoT Central alkalmazáshoz, az eszköz állapota a következőképpen változik:

1. Az eszköz állapota először **Regisztrált**. Ez az állapot azt jelenti, hogy az eszköz az IoT Centralban jön létre, és rendelkezik egy eszközazonosítóval. Az eszköz akkor van regisztrálva, ha:
    - Egy új valódi eszköz kerül az **Eszközök** lapra.
    - Az eszközök készlete az **Importálás** az **Eszközök** lapon lapon jelenik meg.

1. Az eszköz állapota **kiépített,** ha az eszköz, amely csatlakozik az IoT Central alkalmazás érvényes hitelesítő adatokkal befejezi a kiépítési lépést. Ebben a lépésben az eszköz a DPS segítségével automatikusan lekéri a kapcsolati karakterláncot az IoT Central-alkalmazás által használt IoT Hub. Az eszköz most már csatlakozhat az IoT Centralhoz, és megkezdheti az adatok küldését.

1. Az operátor blokkolhat egy eszközt. Ha egy eszköz le van tiltva, nem tud adatokat küldeni az IoT Central alkalmazásnak. A letiltott eszközök állapota **Letiltott**. Az operátornak alaphelyzetbe kell állítania az eszközt, mielőtt folytathasa az adatok küldését. Amikor egy operátor felold egy eszközt, az állapot visszatér a korábbi értékéhez, **a Regisztrált** vagy **a Kiépítve**értékhez.

1. Ha az eszköz állapota **Jóváhagyásra vár,** az azt jelenti, hogy az **Automatikus jóváhagyás** beállítás le van tiltva. Az operátornak explicit módon jóvá kell hagynia egy eszközt, mielőtt megkezdene az adatok küldését. Az eszközök nem regisztráltak manuálisan az **Eszközök** lapon, de az érvényes hitelesítő adatokkal összekapcsolteszközök eszközállapota **Várakozás jóváhagyásra**. Az operátorok jóváhagyhatják ezeket az eszközöket az **Eszközök** lapon a **Jóváhagyás** gombbal.

1. Ha az eszköz állapota **nincs társítva,** az azt jelenti, hogy az IoT Centralhoz csatlakozó eszköz nem rendelkezik társított eszközsablonnal. Ez a helyzet általában a következő esetekben fordul elő:

    - Az eszközök egy készlete az **Importálás** az **Eszközök** lapon az eszközsablon megadása nélkül kerül hozzáadásra.
    - Egy eszköz manuálisan lett regisztrálva az **Eszközök** lapon az eszközsablon megadása nélkül. Az eszköz ezután érvényes hitelesítő adatokkal csatlakozik.  

    Az Operátor az Áttelepítés **gombbal** társíthat egy eszközt egy eszközsablonhoz az **Eszközök** lapon.

## <a name="best-practices"></a>Ajánlott eljárások

Ne őrizzük meg, és ne gyorsítótárazd azt az eszközkapcsolati karakterláncot, amelyet a DPS az eszköz első csatlakoztatásakor ad vissza. Eszköz újracsatlakoztatásához menjen át a szabványos eszközregisztrációs folyamaton a megfelelő eszközkapcsolati karakterlánc beírásához. Ha az eszköz gyorsítótárazza a kapcsolati karakterláncot, az eszközszoftver egy elavult kapcsolati karakterlánc kockázatának indul, ha az IoT Central frissíti az alapul szolgáló Azure IoT hubot, amelyet használ.

## <a name="sdk-support"></a>SDK-támogatás

Az Azure Device SDK-k a legegyszerűbb módja az eszközkód megvalósításához. A következő eszköz SDK-k érhetők el:

- [C nyelvhez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [Pythonhoz készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [Node.js-hez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [Javához készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [.NET-hez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Az SDK-funkciók és az IoT Hub-kapcsolat

Az IoT Hubbal folytatott összes eszközkommunikáció az alábbi IoT Hub-csatlakozási lehetőségeket használja:

- [Eszközről felhőbe irányuló üzenetküldés](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Eszköz ikrek](../../iot-hub/iot-hub-devguide-device-twins.md)

Az alábbi táblázat összefoglalja, hogy az Azure IoT Central eszköz funkciók hogyan felelnek meg az IoT Hub funkcióinak:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetria | Eszközről felhőbe irányuló üzenetküldés |
| Tulajdonság | Az ikereszköz jelentett tulajdonságai |
| Tulajdonság (írható) | Az ikereszköz kívánt és jelentett tulajdonságai |
| Parancs | Közvetlen metódusok |

Ha többet szeretne megtudni az Eszköz SDK-k használatáról, olvassa [el a DevDiv-készlet eszközcsatlakoztatása az Azure IoT Central alkalmazáshoz](howto-connect-devkit.md) például.

### <a name="protocols"></a>Protokollok

Az Eszköz SDK-k a következő hálózati protokollokat támogatják az IoT-központhoz való csatlakozáshoz:

- MQTT
- AMQP
- HTTPS

Ezekről a különbségekről szóló protokollokról és a választható protokollról a [Kommunikációs protokoll kiválasztása című](../../iot-hub/iot-hub-devguide-protocols.md)témakörben talál további információt.

Ha az eszköz nem tudja használni a támogatott protokollok, használhatja az Azure IoT Edge protokollátalakítás. Az IoT Edge támogatja a többi intelligencia-on-the-edge forgatókönyvek a feldolgozás kiürítése a peremhálózati azure IoT Central alkalmazásból.

## <a name="security"></a>Biztonság

Az eszközök és az Azure IoT Central között kicserélt összes adat titkosítva van. Az IoT Hub hitelesíti az eszközről érkező minden kérelmet, amely az eszköz felé néző IoT Hub-végpontokhoz csatlakozik. A hitelesítő adatok cseréje a vezetéken keresztül, egy eszköz aláírással aláírt jogkivonatokat használ a hitelesítéshez. További információt az [IoT Hubhoz való hozzáférés szabályozása](../../iot-hub/iot-hub-devguide-security.md)című témakörben talál.

## <a name="next-steps"></a>További lépések

Ha Ön eszközfejlesztő, néhány javasolt következő lépés a következő:

- Az [azure CLI használatával az eszközkapcsolatok figyelése](./howto-monitor-devices-azure-cli.md)
- Ismerje meg, hogyan [definiálhat egy új IoT-eszköztípust az Azure IoT Central alkalmazásban](./howto-set-up-template.md)
- Olvasson az [Azure IoT Edge-eszközökről és az Azure IoT Centralról](./concepts-iot-edge.md)
