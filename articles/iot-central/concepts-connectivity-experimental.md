---
title: Eszköz csatlakoztatása az Azure IoT Central |} A Microsoft Docs
description: Ez a cikk bemutatja a eszköz csatlakoztatása az Azure IoT Central kapcsolatos alapfogalmak
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: edffc6677609537d8a07aeae45a57c5e88449099
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882654"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Eszköz csatlakoztatása az Azure IoT Central

Ez a cikk az alapfogalmak kapcsolódik az eszközök csatlakoztatása a Microsoft Azure IoT Central be.

Használja az Azure IoT Central [Azure IoT Hub Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) regisztrálhatja és csatlakoztathatja a nagy mennyiségű eszközt.

A Device Provisioning Service:

- Ügyfelei mostantól készítése az eszköz hitelesítő adatait, és konfigurálhatja a kapcsolat nélküli eszközöket először regisztrálja az eszközöket az Azure IoT Central nélkül.
- Az Azure IoT Central X.509-tanúsítványokat, miközben továbbra is támogatja, és javíthatják a kapcsolatot, közös hozzáférésű jogosultságkódok használata eszköz kapcsolatot támogat.
- Az Azure IoT Central-ügyfelek most már használhatja a saját eszközazonosítókat regisztrálható az eszköz az Azure IoT Central, amely lehetővé teszi az egyszerű integráció meglévő háttérrendszerhez rendszerekkel.
- Az eszközök csatlakoztatása Azure IoT Central egy egységes módon van.

>[!NOTE]
>Az Azure IoT Central IoT Hub Device Provisioning Service regisztrálni az eszközt, és kapcsolatot használ. [További információk](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

A használati eset alapján, hajtsa végre az eszközök csatlakoztatása Azure IoT Central megfelelő utasításokat:

- [Csatlakozás egy adott eszköz gyorsan (közös hozzáférésű jogosultságkódok használata)](#connect-a-single-device)
- [Csatlakozás a nagy mennyiségű eszközt közös hozzáférésű jogosultságkódok használatával](#connect-devices-at-scale-using-shared-access-signatures)
- [X.509-tanúsítványok használatával csatlakoznak a nagy mennyiségű eszközt](#connect-devices-using-x509-certificates) (éles számítási feladatok esetében ajánlott)
- [Csatlakozzon az első regisztráló eszközök nélkül](#connect-without-first-registering-devices) 

>[!NOTE]
>A globális eszközök csatlakoztatása és üzembe helyezése-végpont **global.azure-eszközök – provisioning.net**.

## <a name="connect-a-single-device"></a>Egyetlen eszköz csatlakoztatása

Csatlakozás egyetlen eszközt az Azure IoT Central közös hozzáférési aláírások használatával:

1. Adjon hozzá egy **valós eszköz** a **Device Explorer**. Válassza ki **+ új** > **valós** valós eszköz hozzáadásához.
    - Adja meg a **Eszközazonosító** (kisbetűnek kell lennie) vagy a javasolt eszközazonosítót használ.
    - Adja meg a **eszköznév** vagy javasolt nevét használja.

    ![Eszköz hozzáadása](media/concepts-connectivity/add-device.png)

1. Kérheti le a kapcsolat adatait, például a **hatókör azonosítója**, **Eszközazonosító**, és **elsődleges kulcs**, az új eszköz kiválasztásával **Connect** a a eszközök oldal.

    - **[Hatókör azonosítója](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)**  történik az Azure IoT Central-alkalmazást és a Device Provisioning Service által létrehozott. Ez az azonosító segítségével győződjön meg, hogy egy eszköz egyedi azonosítója az alkalmazáson belül.
    - **Eszközazonosító** alkalmazásonként eszköz egyedi azonosítója. Az eszköz kell küldenie az eszköz azonosítója a regisztrációs hívás részeként.
    - **Elsődleges kulcs** egy közös hozzáférésű jogosultságkód van az adott eszköz által az Azure IoT Central létrehozott.
 
    ![Kapcsolat adatai](media/concepts-connectivity/device-connect.png)

1. Használja a kapcsolat adatai **Eszközidentitás**, **eszköznév**, és az eszköz **elsődleges kulcs** a eszköz kiépítése, és csatlakoztassa az eszközt, és azokat a kódban a adatfolyam keresztül azonnal. Ha az MXChip IoT fejlesztői készlet (DevKit) eszközt használ, kövesse az [lépésenkénti](howto-connect-devkit-experimental.md#add-a-real-device)kezdődően a szakasz "Felkészülés az DevKit eszköz."

    Az alábbiakban a hivatkozások más nyelven, akkor érdemes használni.

    - **C nyelv:** Hajtsa végre a [a minta C ügyfél](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) egy minta-eszközt. A minta használja a következő beállításokat:

         ```c
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```

    - **Node.js:**  Hajtsa végre a [lépésenkénti](tutorial-add-device-experimental.md#prepare-the-client-code)kezdődően a szakasz "Felkészülés az Ügyfélkód."

## <a name="connect-devices-at-scale-by-using-shared-access-signatures"></a>Csatlakozás a nagy mennyiségű eszközt közös hozzáférésű jogosultságkódok használatával

Csatlakozás a nagy mennyiségű eszközt az Azure IoT Central közös hozzáférési aláírások használatával, két általános lépésből áll:

1. **Eszközregisztráció:** Regisztrálható az eszköz által importálná őket az Azure IoT Central egy CSV-fájl használatával. Ezután a **exportálása** művelet exportálja az eszközök és az eszköz-kapcsolat részleteinek beolvasása.
1. **Eszköz beállítása:** A program a megadott kapcsolati adatokkal az eszközök (**hatókör azonosítója**, **Eszközazonosító**, és **elsődleges kulcs**). Egyes eszközök bekapcsolásakor Device Provisioning Service a kapcsolati adatok vagy az Azure IoT központi alkalmazás-hozzárendelés bude volat.

>[!NOTE]
>Egy speciális beállítás érhető el, csatlakozhatnak az eszközök először regisztrálja az eszközöket az Azure IoT Central nélkül. [További információk](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

### <a name="device-registration"></a>Eszközregisztráció

Az alkalmazás egy nagy számú csatlakozni, Azure IoT Central támogatja a tömeges importálása CSV-fájl keresztül eszközök. 

1. Eszközök importálása regisztrálja őket az alkalmazásban:

   1. Válassza ki **Device Explorer** a bal oldali menüben.
   1. A bal oldali panelen válassza ki az eszköz sablont, amelyet szeretne tömeges létrehozásához az eszközök. 
   1. Válassza ki **importálás**, majd válassza ki a CSV-fájl, amely az importálni kívánt eszközazonosítókat listája szerepel. A CSV-fájlt a következő oszlopokat (és a fejlécek) kell rendelkeznie:

       - IOTC_DeviceID (kisbetűnek kell lennie)
       - IOTC_DeviceName (nem kötelező)

   1. Az importálás befejezése után az eszköz rács sikert jelző üzenet jelenik meg.

1. A kapcsolat részleteinek eszköz exportálása:

   A **exportálása** a művelet létrehoz egy CSV-fájlt az eszköz azonosítója, az eszköz nevét és az eszköz kulcsok. Ezen adatok segítségével az eszköz csatlakoztatása Azure IoT Central. A tömeges-exportálás eszköz az alkalmazásból:

   1. Válassza ki **Device Explorer** a bal oldali menüben.
   1. Válassza ki azokat az eszközöket, amelyet szeretne exportálni, és válassza a **exportálása** művelet.
   1. Az exportálás befejezése után a sikert jelző üzenet jelenik meg és a egy hivatkozás a létrehozott fájl letöltéséhez.
   1. Kattintson a sikert jelző üzenet letölteni a fájlt egy helyi mappába a lemezen.
   1. Az exportált CSV-fájl lesz a következő oszlopokat, amelyek tartalmazzák a kapcsolat adatai **Eszközazonosító**, **eszköznév**, **eszköz elsődleges vagy másodlagos kulcsok**, és  **Elsődleges vagy másodlagos tanúsítvány-ujjlenyomatok**:

       - IOTC_DEVICEID
       - IOTC_DEVICENAME
       - IOTC_SASKEY_PRIMARY
       - IOTC_SASKEY_SECONDARY
       - IOTC_X509THUMBPRINT_PRIMARY
       - IOTC_X509THUMBPRINT_SECONDARY

### <a name="device-setup"></a>Eszköz beállítása

 Kiépítése, és csatlakoztassa az eszközt, használja a kapcsolat adatai **Device-Identity (IOTC_DEVICEID)**, **eszköz elsődleges kulcs (IOTC_SASKEY_PRIMARY)**, és **hatókör azonosítója** a az eszköz kódot. Ha ezt még nem tette meg, a **hatókör azonosítója** kiválasztásával az Azure IoT Central alkalmazásból **felügyeleti** > **eszköz kapcsolat**  >  **Hatókör azonosítója**.

Ha egy fejlesztői készlet eszköz csatlakozik, hajtsa végre a [részletes utasításokat](howto-connect-devkit-experimental.md#add-a-real-device)kezdődően a szakasz "Felkészülés az DevKit eszköz."

Az alábbiakban a hivatkozások más nyelven, akkor érdemes használni.

- **C nyelv:** Hajtsa végre a [a minta C ügyfél](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) egy minta-eszközt. A minta használja a következő beállításokat:

     ```c
     hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

     ## Enter the Device Id and Symmetric keys 
     prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
    ```

- **Node.js:** Hajtsa végre a [lépésenkénti](tutorial-add-device-experimental.md#prepare-the-client-code)kezdődően a szakasz "Felkészülés az Ügyfélkód."

## <a name="connect-devices-by-using-x509-certificates"></a>Eszközök csatlakoztatása az X.509-tanúsítványok használatával

X.509-tanúsítványokat használ az igazolási mechanizmusként szolgáltatás kiváló éles és egyszerűsítheti az eszközök kiépítését. X.509-tanúsítványokat általában egy megbízhatósági kapcsolat, amelyben a lánc minden tanúsítványt a titkos kulcsot a következő nagyobb tanúsítványt, és így tovább, egy önaláírt főtanúsítványt a végződésű által aláírt tanúsítvány láncában vannak elrendezve. Ez a struktúra egy meghatalmazott a főtanúsítványból létrehozott egy megbízható legfelső szintű-hitelesítésszolgáltató (CA) által le minden közbenső hitelesítésszolgáltató végfelhasználói "levél" tanúsítványnak, telepítve van egy eszközön keresztül megbízhatósági láncot létesít. További tudnivalókért lásd: [x.509-es Hitelesítésszolgáltatói tanúsítványok használatával eszközhitelesítés](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Eszközök csatlakoztatása Azure IoT Central X.509-tanúsítványok használatával, hogy három fő lépésből áll részt vevő:
 
1. **Adja meg a kapcsolati beállításokat** hozzáadásával, illetve annak ellenőrzésére, az X.509 legfelső szintű vagy köztes tanúsítványt, amely az eszköztanúsítványok létrehozásához használja az Azure IoT Central alkalmazásban. Az X.509-tanúsítványokat biztonságoskapcsolat-beállításainak konfigurálása három lépésből áll:  

    - **Adja meg a X.509 legfelső szintű vagy köztes tanúsítványt** , hogy a levél eszköztanúsítványok létrehozásához használ. Lépjen a **felügyeleti** > **eszköz kapcsolat** > **tanúsítványok**. 
    
       ![Kapcsolati beállítások](media/concepts-connectivity/connection-settings.png)

    - **A tanúsítvány ellenőrzéséhez.** Tanúsítvány tulajdonjogának ellenőrzése biztosítja, hogy a tanúsítvány feltöltése a tanúsítvány titkos kulcsa. A tanúsítvány ellenőrzéséhez:

        - Az ellenőrző kód létrehozása. Válassza ki a gomb mellett a **ellenőrzőkódot** mező a kódot. 
        - Hozzon létre egy X.509-hitelesítési tanúsítvány az ellenőrző kódot. Mentse a tanúsítványt egy .cer fájlba. 
        - Töltse fel az aláírt hitelesítési tanúsítványt, és válassza ki **ellenőrizze**.

        ![Kapcsolati beállítások](media/concepts-connectivity/verify-cert.png)

    - **Másodlagos tanúsítvány hozzáadásához.** Az IoT-megoldás élettartama során kell visszaállítani a tanúsítványokat. Két fő oka a működés közbeni tanúsítványok a biztonsági problémák és a tanúsítvány lejárhat. Másodlagos tanúsítvány csökkenthető a leállások eszközök vannak épít ki, míg az elsődleges tanúsítvány Amikor frissít.

      **Csak tesztelési célokra:**
    
      Az alábbiakban néhány segédprogram parancssori eszközök, CA-tanúsítványok és az eszköztanúsítványok létrehozásához használhatja.

      - Ha DevKit eszközt használ, egy [parancssori eszköz](https://aka.ms/iotcentral-docs-dicetool) CA-tanúsítványok előállításához. Adja hozzá az Azure IoT Central-alkalmazást, és a tanúsítványok ellenőrzése. 

      - Használat [a parancssori eszköz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) való:

          - Hozza létre a tanúsítványlánc (kövesse 2. lépés a GitHub-dokumentumokhoz). Mentse a tanúsítványok .cer fájlként, és feltölti őket az az Azure IoT Central (mint elsődleges tanúsítványokat).
          - Az ellenőrző kód lekérése az Azure IoT Central-alkalmazást, és a tanúsítvány (kövesse 3. lépés a GitHub-dokumentumokhoz) Győződjön meg arról, hogy a tanúsítvány feltöltése. 
          - Hozzon létre levél tanúsítványok paraméterként az eszköz (kövesse 4. lépés) az eszköz azonosítója. Mentse a tanúsítványt, és használja az eszközön.

1. **Eszközök regisztrálása** szerint importálja őket az Azure IoT Central egy CSV-fájl használatával.

1. **Eszközbeállítások**: A levél tanúsítványokat létrehozni a feltöltött legfelső szintű tanúsítvány használatával. Ellenőrizze, hogy a CNAME REKORDOT a levél tanúsítványok használja az eszköz azonosítója, és győződjön meg arról, hogy kisbetűs. Íme egy [parancssori eszköz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) levél/eszköz tanúsítványainak előállításához **csak tesztelési célokat**.

1. **Az eszközt a kiépítési szolgáltatás információkat**, azt beírva kérheti le a kapcsolat adatait, és az Azure IoT központi alkalmazás-hozzárendelés, bekapcsolt engedélyezése.

    További információval a következő cikkek szolgálnak:

    - [Raspberry Pi minta megvalósítása](https://aka.ms/iotcentral-docs-Raspi-releases)  

    - [Eszközügyfél minta C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-first-registering-devices"></a>Csatlakozzon az első regisztráló eszközök nélkül

A főbb forgatókönyvek megvalósítását támogatja az Azure IoT Central egyik célja, hogy háttértár-gyártás eszközök, hitelesítő adatok előállítása, és konfigurálja az eszközök gyári először regisztrálja az eszközöket az Azure IoT Central nélkül. Után az eszközök be vannak kapcsolva, és azok megkísérlik az Azure IoT Central csatlakozni, az üzemeltető jóváhagyja az eszközök csatlakoztatása az Azure IoT Central alkalmazáshoz.

Itt látható a folyamat csatlakoztatni az eszközöket a szolgáltatással:

![Kapcsolati beállítások](media/concepts-connectivity/device-connection-flow.png)

Kövesse a lépéseket, a választott eszköz-hitelesítési sémát (X.509-tanúsítványokat vagy közös hozzáférésű jogosultságkódok) alapján:

1. **Konfigurálja, vagy szerezze be a kapcsolat beállításait:**

    - **X.509-tanúsítványokat:** [Adja hozzá és ellenőrizze a legfelső szintű vagy köztes tanúsítványt](#connect-devices-using-x509-certificates) , amellyel az eszköztanúsítványok létrehozásához a következő lépésben.
    - **Közös hozzáférésű jogosultságkódok:** Másolja az elsődleges kulcsot (ezt a kulcsot nem a csoport közös hozzáférésű jogosultságkód kulcs az Azure IoT Central alkalmazáshoz), és ezzel hozzon létre a közösen használt eszközök hozzáférési aláírási kulcsokat, a következő lépésben.

       ![Kapcsolati beállításokat engedélyezte a közös hozzáférésű jogosultságkódok](media/concepts-connectivity/connection-settings-sas.png)

1. **Hozzon létre az eszköz hitelesítő adatait:**

    - **X.509-tanúsítványokat:** A legfelső szintű vagy köztes tanúsítványt az alkalmazáshoz hozzáadott eszközök levél tanúsítványainak előállításához. Ellenőrizze, hogy egy olyan CNAME REKORDOT a levél tanúsítványok használja az eszköz azonosítója, és ellenőrizze, hogy kis. Íme egy [parancssori eszköz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) tesztelési levél-vagy eszköztanúsítványok létrehozásához.
    - **Közös hozzáférésű jogosultságkódok:** Megosztott eszköz hozzáférési aláírási Kulcslétrehozási, ezzel [parancssori eszköz](https://www.npmjs.com/package/dps-keygen). Használja az elsődleges közös hozzáférési aláírási kulcs (közös hozzáférésű jogosultságkód csoportkulcs) az előző lépésben. Győződjön meg arról, hogy az eszköz csomagazonosítója kisbetűs.

        A következő paranccsal kérje le az eszköz kapcsolati karakterláncot: 

        ```
        npm i -g dps-keygen
        ```
    
        A következő paranccsal hozzon létre egy eszköz közös hozzáférésű jogosultságkód kulcsot:
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **Állítsa be az eszközöket:** Mindegyik eszközhöz Flash a **hatókör azonosítója**, **Eszközazonosító**, és **eszköz tanúsítvány/SAS-kulcsának**, majd kapcsolja be az eszköz az Azure IoT Central alkalmazáshoz csatlakozhat.

1. **Az eszközök csatlakoztatása Azure IoT Central:** Miután az eszközök bekapcsolt állapotban van, azok csatlakozni Device Provisioning Service vagy az Azure IoT Central a regisztrációhoz.

1. **Társítsa az eszközöket egy sablont:** A csatlakoztatott eszközök jelennek meg **nem kapcsolódó eszközök** a **Device Explorer**. Kiépítési állapot eszközük **regisztrált**. **Társítsa** az eszközök a megfelelő eszköz sablont, és hagyja jóvá az eszközök csatlakoztatása az Azure IoT Central alkalmazáshoz. Az eszközök kapják meg a kapcsolódási adatok az Azure IoT Central-alkalmazást, és azok csatlakozzon, és adatokat küldjön. Eszközkiépítési most már befejeződött, és a **előkészítési állapotát** az eszközök kerül, **kiépített**.

## <a name="device-provisioning-status"></a>Eszköz kiépítési állapota

Valós eszközök Azure IoT Central való csatlakozásakor a következő lépések történnek:

1. **Regisztrált**: Regisztrálja az eszközt először, ami azt jelenti, az eszköz az Azure IoT Central jön létre, és az eszköz azonosítója, az eszköz rendelkezik. Egy eszköz regisztrálása során:

    * Valódi új eszközt adnak hozzá a **Device Explorer**
    * Az eszközök használatával adnak **importálás** a **Device Explorer**
    * Olyan eszköz, amely nem lett regisztrálva érvényes hitelesítő adatokkal csatlakozik, és látható a **nem hozzárendelt eszközök**

    Minden fenti esetben a **előkészítési állapotát** van **regisztrált**.

1. **Üzembe helyezett**: Amikor az eszköz érvényes hitelesítő adatokkal csatlakozik, az Azure IoT Central fejezheti be a kiépítési lépés (létrehozásakor az eszközt az IoT Hub). Az Azure IoT Central, képes csatlakozni és adatokat küldjön majd visszaadja a kapcsolati karakterláncot az eszközön. Az eszköz **előkészítési állapotát** bekapcsolja a **regisztrált** való **kiépített**.

1. **Blokkolt**: Az operátor is letiltja az eszköz. Után az eszköz zárolva van, azt nem lehet adatokat küldeni az Azure IoT Central, és meg kell állítani. Kitiltott eszközök rendelkeznek a **előkészítési állapotát** , **letiltott**. Az operátor is feloldhatja az eszköz. Miután azt van feloldva, az eszköz **előkészítési állapotát** állapotát adja vissza (**regisztrált** vagy **kiépített**). 

## <a name="get-the-device-connection-string"></a>Az eszköz kapcsolati sztringjének beszerzése

Az Azure IoT Hub az Iot Hub eszköz kapcsolati karakterláncának megkaphassa az alábbi lépéseket követve:

1. Részletekért az eszköz kapcsolatot, például a **hatókör azonosítója**, **Eszközazonosító**, és **elsődleges kulcs** a a **eszköz kapcsolat** lap. Ezeket az adatokat, keresse fel a **eszköz** lapon, és válassza **Connect**. 

    ![Kapcsolat adatai](media/concepts-connectivity/device-connect.png)

1. Az eszköz kapcsolati karakterláncának beszerzése a dps-keygen parancssori eszköz használatával. A következő paranccsal kérje le az eszköz kapcsolati karakterláncot:  

    ```cmd/sh
    npm i -g dps-keygen
    ```

    Hozzon létre egy kapcsolati karakterláncot, keresse meg a bináris alatt a *bin /* mappa:

    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```

    [További információ a dps-keygen eszközzel](https://www.npmjs.com/package/dps-keygen).

## <a name="sdk-support"></a>SDK-támogatás

Az Azure IoT SDK-k kínálnak a legegyszerűbb kódot használva az eszközök, amelyek kapcsolódnak az Azure IoT Central alkalmazáshoz. A következő SDK-k érhetők el:

- [A c nyelvhez készült Azure IoT-SDK](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK for Python](https://github.com/azure/azure-iot-sdk-python)
- [Az Azure IoT SDK for node.js használatával](https://github.com/azure/azure-iot-sdk-node)
- [A Javához készült Azure IoT-SDK](https://github.com/azure/azure-iot-sdk-java)
- [Az Azure IoT SDK for .NET csomaggal](https://github.com/azure/azure-iot-sdk-csharp)

Minden egyes eszköz csatlakozik, amely azonosítja az eszköz egyedi kapcsolati karakterlánc használatával. Egy eszköz csak csatlakozhat az IoT hub, ha regisztrálva van. Valós eszközöknek az Azure IoT Central-alkalmazást hoz létre, amikor az alkalmazás, amelyet használhat egy kapcsolati karakterláncot állít elő.

## <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-funkciók és az IoT Hub-kapcsolat

Az összes eszköz kommunikációt az IoT Hub használja a következő IoT Hub kapcsolati lehetőségek:

- [Eszközről-a-felhőbe üzenetkezelés](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Ikereszközök](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

A következő táblázat összefoglalja, hogyan Azure IoT Central eszközfunkciók leképezése az IoT Hub-funkciókat:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Mérték: Telemetria | Eszközről-a-felhőbe üzenetkezelés |
| Eszköztulajdonságok | Ikereszköz jelentett tulajdonságait |
| Beállítások | Ikereszköz kívánt és a jelentett tulajdonságok |

További információt az Azure IoT SDK-k használatával, tekintse meg a mintakódot az alábbi cikkeket:

- [Általános Node.js-ügyfél csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-nodejs-experimental.md)
- [A Raspberry Pi-eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz](howto-connect-raspberry-pi-python.md)
- [Az MXChip IoT DevKit eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz](howto-connect-devkit-experimental.md)


## <a name="protocols"></a>Protokollok

Az Azure IoT SDK-k támogatják a következő hálózati protokollok egy IoT hubhoz való csatlakozáshoz:

- MQTT
- AMQP
- HTTPS

Szerinti kiválaszt egyet ezek a protokollok és útmutatást kapcsolatos információkért lásd: [kommunikációs protokoll kiválasztása](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Ha az eszköz nem tudja használni a támogatott protokollok, az Azure IoT Edge segítségével protokoll átalakítás. IoT Edge feldolgozás kiszervezése az Edge-ben az Azure IoT Central-alkalmazás más üzletiintelligencia-az-a-edge forgatókönyvek támogatja.

## <a name="security"></a>Biztonság

Eszközök és az Azure IoT központi alkalmazás között továbbított összes adat titkosítva van. Az IoT Hub olyan eszköz, amely csatlakozik az IoT Hub eszköz felé néző végpontok bármelyikét, érkező minden kérés hitelesíti. Elkerülheti a hitelesítő adatok cseréje a hálózaton keresztül, az eszköz a aláírt jogkivonatokat használ az hitelesítéséhez. További információt [az IoT Hub-hozzáférés szabályozásával](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security) kapcsolatos cikkben találhat.

## <a name="next-steps"></a>További lépések

- [Készítse elő és eszköz MXChip IoT DevKit csatlakoztatása](howto-connect-devkit-experimental.md)
- [Készítse elő és a egy Raspberry Pi-eszköz csatlakoztatása](howto-connect-raspberry-pi-python.md)
- [Általános Node.js-ügyfél csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-nodejs-experimental.md)
