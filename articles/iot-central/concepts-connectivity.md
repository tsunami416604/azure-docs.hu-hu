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
ms.openlocfilehash: ad77f1f0a99fbdb355163e1bc83461c8c4eb75fa
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157700"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Eszköz csatlakoztatása az Azure IoT Central

Ez a cikk az eszközök csatlakoztatása a Microsoft Azure IoT Central kapcsolatos alapfogalmakat mutatja be.

Használja az Azure IoT Central [Azure IoT Hub Device Provisioning service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps), IoT-központ bevezetési támogatása engedélyezése, és nagy mennyiségű eszköz csatlakoztatása.

-   Ügyfelei mostantól eszköz hitelesítő adatok előállítása és az eszközök kapcsolat nélküli konfigurálásához először regisztrálnia az eszközöket az IoT-központ nélkül
-   IoT-központ eszköz kapcsolatot támogat az ajánlott X509 iparági tanúsítvány alapú kapcsolatot, miközben továbbra is támogatja, és javíthatják a kapcsolatot a közös hozzáférésű Jogosultságkódok (SAS)
-   IoT-központ az ügyfelek kihasználhatják most voltát a saját eszköz azonosítóját, az eszközök regisztrálása az IoT-központ háttérrendszerhez rendszerekkel való egyszerű integráció engedélyezése
-   Eszközök csatlakoztatása IoT Central egy egységes módon 

>[!NOTE]
>IoT-központ használja az Azure IoT Device Provisioning service (DPS) alá regisztrálni az eszközt, és a kapcsolat, [tudjon meg többet a DPS](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

A használati eset kövesse az utasításokat követve eszközök csatlakoztatása az IoT-központ alapján
1. [Gyors csatlakozás egy adott eszköz (közös hozzáférési aláírások használatával)](#connect-a-single-device)
1. [Közös hozzáférésű Jogosultságkódok (SAS) használatával nagy mennyiségű eszköz csatlakoztatása](#connect-devices-at-scale-using-shared-access-signatures)
1. [Csatlakozás X509 használatával nagy mennyiségű eszközt tanúsítványok](#connect-devices-using-x509-certificates) **ajánlottak éles számítási feladatokra**
1. [Csatlakozzon az első regisztráló eszközök nélkül](#connect-without-first-registering-devices) 


>[!NOTE]
>Itt van a globális eszközök csatlakoztatása és üzembe helyezése célpont **global.azure-eszközök – provisioning.net**.

## <a name="connect-a-single-device"></a>Egyetlen eszköz csatlakoztatása
Egy eszköz csatlakozik az IoT-központ SAS használatával egyszerűen, mindössze néhány lépésben 
1. Adjon hozzá egy **valós eszköz** Device Explorer, kattintson a **+ új > valós** valós eszköz hozzáadásához.
    * Adja meg az eszköz azonosítója **<span style="color:Red">(kell lennie a kisbetűs)</span>** vagy a javasolt eszközazonosítót használ.
    * Adja meg az eszköz nevét, vagy használja a javasolt név   
    ![Eszköz hozzáadása](media\concepts-connectivity\add-device.png)
1. Például beolvasni a kapcsolat adatait **hatókör azonosítója, az eszköz Azonosítóját és az elsődleges kulcs** kattintva az új eszköz **Connect** az eszköz oldalon.
    * **[Hatókör azonosítója](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)**  kiszolgálónként IoT Central-alkalmazást, és hozza létre a DPS biztosítja az eszköz egyedi azonosítója az alkalmazáson belül.
    * **Eszközazonosító** eszköz egyedi azonosítója az egyes alkalmazások a eszközt kell küldenie a eszközazonosító a regisztrációs hívás részeként van.   
    * **Elsődleges kulcs** SAS-tokent, akkor az adott eszköz IoT-központ által generált. 
    ![A kapcsolat részletei](media\concepts-connectivity\device-connect.PNG)
1. Használja az alábbi kapcsolati adatokat **Eszközidentitás eszköz nevét és az eszköz elsődleges kulcs** kiépítése, és csatlakoztassa az eszközt, és azokat az adatokat, azonnal áramlása az eszköz kódjában. Az MxChip eszköz kövesse használatakor [részletes utasításokat itt](howto-connect-devkit.md#add-a-real-device), indítsa el a szakaszban leírt **az DevKit eszköz előkészítése**.   

    Az alábbiakban a hivatkozások más nyelven, akkor érdemes használni.

    *   **C nyelvi:** C használja, hajtsa végre a [a minta C ügyfél](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md) egy minta-eszközt. A minta használja a következő beállításokat.   

         ```
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
         
         static const char* const SYMMETRIC_KEY_VALUE = "Enter Primary Symmetric key here";

         static const char* const REGISTRATION_NAME = "Enter Device Id here";
        ```

    *   **NODE.js:** Ha használható a Node.js [részletes utasításokat itt használhassa](tutorial-add-device.md#prepare-the-client-code), indítsa el a szakaszban leírt **előkészítése az Ügyfélkód**.



## <a name="connect-devices-at-scale-using-shared-access-signatures"></a>Közös hozzáférési aláírások használatával nagy mennyiségű eszköz csatlakoztatása

Nagy mennyiségű eszközt az IoT Centrallal SAS használatával csatlakozhat, még két lépésre van érintett 
1. **Eszközök regisztrálása** IoT Central egy CSV-n keresztül történő importálásával fájlt, és az eszköz kapcsolati adatai használatával csatlakoztathatja eszközeit az eszköz exportálása
1. **Eszközbeállítások** az eszköz a megadott kapcsolati adatokkal van programozva ( **hatókör azonosítója, az eszköz Azonosítóját és az elsődleges kulcs**), hogy meghívjon beolvasni a kapcsolat adatai/IoT központi alkalmazás-hozzárendelés, ha a kiépítési szolgáltatás engedélyezése kapcsolni.

>[!NOTE]
>Egy speciális beállítás is érhető el, ahol az eszközök először az IoT-központ, az eszközök regisztrálása nélkül csatlakoztathatja [további információk itt](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

**Eszközök regisztrálása**

A nagy mennyiségű eszköz csatlakoztatása az Azure IoT Central alkalmazásnak ajánlatok tömeges importáló eszközöket egy CSV-fájlt. 

CSV-fájl követelmények: A CSV-fájl a következő oszlopokat (és a fejlécek) kell rendelkeznie.
1.  IOTC_DeviceID  **<span style="color:Red">(a kis kell lennie)</span>**
1.  IOTC_DeviceName (nem kötelező)



Eszköz is rögzítheti őket az alkalmazás importálása
1.  Válasszon **Explorer** a bal oldali navigációs menüben.
1.  A bal oldali panelen válassza az eszköz sablon, amely a tömeges szeretné létrehozni az eszközök. 
1.  Kattintson a **importálás**, válassza ki a CSV-fájl, amely az importálni kívánt eszközazonosítókat listája szerepel.
A CSV-fájlt a következő oszlopokat (és a fejlécek) kell rendelkeznie.
    *   IOTC_DeviceID  **<span style="color:Red">(a kis kell lennie)</span>**
    *   IOTC_DeviceName (nem kötelező)
1.  Az importálás befejezése után az eszköz rács sikert jelző üzenet jelenik meg.

A kapcsolat részleteinek eszköz exportálása exportálási hoz létre a CSV-fájl, az eszköz azonosítója, eszköz nevét és az eszköz kulcsát. Ezen adatok segítségével az eszköz csatlakoztatása az IoT-központ.
Tömeges exportálása eszközök az alkalmazásból:
1.  Válasszon **Explorer** a bal oldali navigációs menüben.
1.  Jelölje ki az eszközöket, amelyeket szeretne exportálni, majd kattintson a **exportálása** művelet.
1.  Az exportálás befejezése után a sikert jelző üzenet jelenik meg együtt egy hivatkozás a létrehozott fájl letöltéséhez.
1.  Kattintson a letölteni a fájlt egy helyi mappába a lemezen a sikert jelző üzenet.
1.  Az exportált CSV-fájlt a következő oszlopok adatokat fog rendelkezni: **eszköz azonosítója, az eszköz neve, a eszköz elsődleges és másodlagos kulcsok és a elsődleges és másodlagos tanúsítvány-ujjlenyomatai**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY


**Eszköz beállítása**

Használja az alábbi kapcsolati adatokat **Device-Identity (IOTC_DEVICEID), az eszköz elsődleges kulcs (IOTC_SASKEY_PRIMARY) és a hatókör azonosítója** és az eszköz csatlakoztatása az eszköz kódjában. Ha még nem tette meg, a **hatókör azonosítója** a IoT központi alkalmazás **Adminisztráció > eszköz kapcsolat > hatókör azonosítója**.
Ha használja a **MxChip** való kapcsolódáshoz kövesse az eszköz [részletes utasításokat itt](howto-connect-devkit.md#add-a-real-device), indítsa el a szakaszban leírt **az DevKit eszköz előkészítése**.   

Az alábbiakban a hivatkozások más nyelven, akkor érdemes használni.

   *   **C nyelvi:** C kövesse használatakor [a minta C ügyfél](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md) egy minta-eszközt. A minta használja a következő beállításokat.   
         ```
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         static const char* const SYMMETRIC_KEY_VALUE = "Enter Primary Symmetric key here";
         static const char* const REGISTRATION_NAME = "Enter Device Id here";
        ```
    * **NODE.js:** Ha használható a Node.js [részletes utasításokat itt használhassa](tutorial-add-device.md#prepare-the-client-code), indítsa el a szakaszban leírt **előkészítése az Ügyfélkód**.


## <a name="connect-devices-using-x509-certificates"></a>Csatlakoztatásához X509 használatával tanúsítványok

Használatával az X.509-tanúsítványok szerint skálázhatja igazolási mechanizmus egy remek módja a **éles** és egyszerűsítheti az eszközök kiépítését. X.509-tanúsítványokat általában egy tanúsítványlánc bizalmi kapcsolat, amelyben a lánc minden tanúsítvány aláírásával rendelkezik a titkos kulcsot a következő nagyobb tanúsítványt, és így tovább, egy önaláírt főtanúsítványt a megszakítást okozó vannak elrendezve. Ezzel létrejön egy meghatalmazott a megbízható legfelső szintű hitelesítésszolgáltató (CA) le segítségével telepítve az eszközön, a végfelhasználói "levél" tanúsítványt minden közbenső hitelesítésszolgáltató által létrehozott főtanúsítványból megbízhatósági láncában. További tudnivalókért lásd: [X.509 Hitelesítésszolgáltatói tanúsítványok használatával Eszközhitelesítés](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Csatlakoztatni az eszközöket az IoT-X509 használatával központi tanúsítványok, ott is részt vevő három kulcsfontosságú lépésnek 
1. **Adja meg a kapcsolati beállításokat** ellenőrzésével hozzáadásával és a legfelső szintű vagy köztes tanúsítványt az eszköztanúsítványok létrehozásához használt X509 IoT-központ alkalmazásban.  Két lépést X509 biztonságoskapcsolat-beállításainak konfigurálása a tanúsítványok.  

    *   **Adja hozzá a X509 legfelső szintű vagy köztes tanúsítványt** a levél eszköztanúsítványok létrehozásához használ. Nyissa meg a felügyelet > eszköz kapcsolat > tanúsítványok. 
    
        ![Kapcsolati beállítások](media\concepts-connectivity\connection-settings.PNG)
    *   **Tanúsítvány-ellenőrzés:** ellenőrzése a tanúsítvány tulajdonosa biztosítja, hogy a tanúsítvány titkos kulcs birtokában van a tanúsítvány feltöltése. A tanúsítvány
        *  Ellenőrző kód létrehozása, a gombra kattintva az ellenőrző kód mezőben az ellenőrző kód létrehozása mellett. 
        *  Hozzon létre egy X.509-hitelesítési tanúsítvány az ellenőrzőkódot, mentse el a tanúsítványt egy .cer fájlba. 
        *  Töltse fel az aláírt hitelesítési tanúsítványt, és kattintson ellenőrizze.

        ![Kapcsolati beállítások](media\concepts-connectivity\verify-cert.png)
    *   **Másodlagos tanúsítvány:** szeretné visszaállítani a tanúsítványokat kell az IoT-megoldás élettartama során. Két fő oka a működés közbeni tanúsítványok lenne a biztonsági incidensek és a tanúsítvány lejárhat. Másodlagos tanúsítványok segítségével csökkenthető a leállások a megfelelő eszközök kiépítése az elsődleges tanúsítvány frissítése során.

    **CSAK TESZTELÉSI CÉLRA** 
    
    Az alábbiakban néhány parancssori segédeszközök készítése a CA-tanúsítványok és az eszköz-tanúsítványok használatával.

    * Az MxChip itt használ-e egy [parancssori eszköz](http://aka.ms/iotcentral-docs-dicetool) létrehozni a CA tanúsítványok adja hozzá az IoT Central-alkalmazást, és a tanúsítványok ellenőrzése. 

    *   Ezzel [parancssori eszköz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) ,
        * Hozza létre a tanúsítványlánc (2. lépés. kövesse a GitHub-dokumentumokhoz). 
         Mentse a tanúsítványok .cer fájlként, és töltse fel az IoT-központ (elsődleges).   
        * Az ellenőrző kód lekérése az IoT Central-alkalmazást, és (3. lépés. kövesse a GitHub-dokumentumokhoz) tanúsítvány feltöltése, ellenőrzése. 
        * Hozzon létre levél tanúsítványok paraméterként az eszköz (kövesse a 4. lépés) az eszköz azonosítója. Mentse a tanúsítványt, és használja az eszközön.     

1. **Eszközök regisztrálása** importálja azokat az IoT-központ be CSV-fájl használatával.

1. **Eszközbeállítások** : a feltöltött legfelső szintű tanúsítvány használatával levél tanúsítványainak előállításához. Győződjön meg arról, hogy használja a **Eszközazonosító** , a levél a CNAME-tanúsítványok és **kisbetűs**. Íme egy [parancssori eszköz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) levél/eszköz tanúsítványainak előállításához **csak tesztelési célokra**.

    A program az eszközt a kiépítési szolgáltatás információkat lehetővé teszi a lekérése a kapcsolat adatait, és az IoT-központ alkalmazás-hozzárendelés, ha be van kapcsolva.    

    **További referene** 
    *   A minta megvalósítása [RaspberryPi.](http://aka.ms/iotcentral-docs-Raspi-releases)  

    *   [Eszközügyfél minta c-hez](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

>[!NOTE]
>Használja a **Eszközazonosító** , egy olyan CNAME rekordot a levél tanúsítványokat eszközök készítésekor.

>[!NOTE]
>A **Eszközazonosító** kisbetűs kell lennie. 
 
## <a name="connect-without-first-registering-devices"></a>Csatlakozzon az első regisztráló eszközök nélkül
IoT Central lehetővé teszi azon kulcsfontosságú forgatókönyvek egyike OEM-ek tömeges gyártás eszközökre, a hitelesítő adatok előállítása, és konfigurálja őket az előállító először regisztrálja őket az IoT-központ nélkül. Az eszközök be vannak kapcsolva, és csatlakozik az IoT-központ az operátor jóváhagyja az eszköz csatlakoztatása az IoT Central alkalmazáshoz.

Alább van a flow csatlakoztatni az eszközöket a szolgáltatással

![Kapcsolati beállítások](media\concepts-connectivity\device-connection-flow.PNG)


Kövesse a lépéseket, az Ön által választott eszköz hitelesítési sémát (X509/SAS-) alapú

1. **Kapcsolati beállítások** 
    * **X509 tanúsítványok:** [hozzáadása, és ellenőrizze a legfelső szintű vagy köztes tanúsítványt](#connect-devices-using-x509-certificates) , amellyel az eszköztanúsítványok létrehozásához a következő lépésben.
    * **SAS:** másolja az elsődleges kulcsot (ezt a kulcsot nem a csoport az IoT Central alkalmazáshoz tartozó SAS-kulcsot), és ezzel hozzon létre az eszköz SAS-kulcsokat, a következő lépésben. 
![Kapcsolatbeállítások SAS](media\concepts-connectivity\connection-settings-sas.png)

1. **Eszköz hitelesítő adatok előállítása** 
    *   **Tanúsítványok X509:** az eszközöket a legfelső szintű vagy köztes tanúsítványt az alkalmazáshoz hozzáadott segítségével levél-tanúsítványainak előállításához. Győződjön meg arról, hogy használja a **Eszközazonosító** , egy olyan CNAME rekordot a levél tanúsítványok és  **<span style="color:Red">(kell lennie a kisbetűs)</span>**. Íme egy [parancssori eszköz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) tesztelés levél/eszköz tanúsítványainak előállításához.
    *   **SAS** eszköz SAS-kulcsok használatával Ez hozható [parancssori eszköz](https://www.npmjs.com/package/dps-keygen). Használja az elsődleges SAS-kulcsot (csoport SAS-kulcs) az előző lépésben. Győződjön meg arról, hogy az eszköz azonosítója  **<span style="color:Red">kisbetű szerepel</span>**.

        Használja az alábbi utasítások végrehajtásával eszköz SAS-kulcs létrehozása           

        ```
        npm i -g dps-keygen
        ```
    
        **Használat**
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **Eszköz beállítása** 
    
     Az eszközt az Flash **hatókör azonosítója, az eszköz azonosítója, a eszköz tanúsítvány/SAS-kulcsának** majd kapcsolja be az eszköz IoT Central alkalmazáshoz csatlakozhat.

1. **Eszköz csatlakoztatása az IoT-központ:** egyszer be van kapcsolva az eszközök csatlakoznak a DPS/IoT Central a regisztrációhoz.

1. **Eszköz társítása a sablonhoz:** a csatlakoztatott eszközre alatt jelennek meg **társítatlan eszközök** a **Device Explorer**. Az eszköz kiépítési állapot **regisztrált**. **Társítsa** az eszköz számára a megfelelő eszköz sablont, és hagyja jóvá az eszköz csatlakoztatása az IoT Central alkalmazáshoz. Az eszköz lekérdezi a megadott kapcsolatot információk az IoT-központ, kapcsolódik, és elindítja az adatok küldése. Eszköz provioning befejeződött, és a *előkészítési állapotát* kerül, **kiépített**.

## <a name="device-provisioning-status"></a>Eszköz kiépítési állapota
Replikálásában van lépések sorozatát valós eszköz csatlakozik az Azure IoT Central 
1. **Regisztrált**: az eszköz az első **regisztrált**, ami azt jelenti, az eszköz jön létre az IoT-központ és az eszköz azonosítója, az eszköz rendelkezik.
Eszköz Registeretd során  
    *   Új valós eszköz bekerül a **Explorer**
    *   Eszközök készlete segítségével hozzáadott **importálás** a **Explorer**
    *   Egy eszköz, amely nincs regisztrálva, de érvényes hitelesítő adatokkal kapcsolódik, és alatt látható **nem kapcsolódó** eszközök. 

    A fenti esetek mindegyikét a *előkészítési állapotát* van **regisztrált**

1. **Üzembe helyezett**: A következő lépés akkor, ha az eszköz kapcsolódik, érvényes hitelesítő adatokat az IoT-központ fejezheti be a létesítési lépés (az eszköz létrehozásakor az IoT Hub). Majd visszaadja a kapcsolati karakterlánc csatlakozzon, és adatokat küldjön az eszközre. Az eszköz *előkészítési állapotát* most bekapcsolja a **regisztrált** való **kiépített**.

1.  **Blokkolt**: az operátor is letiltja az eszköz, ha egy eszköz le van tiltva, nem adatokat küldeni IoT-központ rendelkeznek, és alaphelyzetbe kell állítani. Kitiltott eszközök rendelkeznek a *előkészítési állapotát* , **letiltott**. Az operátor is feloldhatja az eszköz. Az eszköz egyszer feloldva *előkészítési állapotát* lépjen vissza az előző *előkészítési állapotát* (regisztrálva, vagy kiépített). 

## <a name="getting-device-connection-string"></a>Eszköz kapcsolati karakterláncának beolvasása
Iot hub eszköz kapcsolati karakterláncát az Azure IoT Hub használatával lekérheti az alábbi lépéseket 
1. Például beolvasni a kapcsolat adatait **hatókör azonosítója, az eszköz Azonosítóját, a eszköz elsődleges kulcs** az eszköz oldalról (itt van az eszköz lapon > kattintson a csatlakozás) 

    ![Kapcsolat adatai](media\concepts-connectivity\device-connect.PNG)

1. Az alábbi parancs parancssori eszköz használatával, az eszköz kapcsolati karakterláncának beolvasása.
    Használja az alábbi utasításokat követve az eszköz kapcsolati karakterláncának lekérése  

    ```cmd/sh
    npm i -g dps-keygen
    ```
    **Használat**

    Annak érdekében, hogy hozzon létre egy kapcsolati karakterláncot, keresse meg a bináris alatt bin / mappa
    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```
    Tudjon meg többet a [Itt a dps-keygen eszközzel.](https://www.npmjs.com/package/dps-keygen)

## <a name="sdk-support"></a>SDK-támogatás

Az Azure eszközoldali SDK-k ajánlat legegyszerűbb módja az Ön számára a kódot, amely az Azure IoT Central alkalmazáshoz kapcsolódik az eszközök hajtja végre. A következő eszközoldali SDK-k érhetők el:

- [A c nyelvhez készült Azure IoT-SDK](https://github.com/azure/azure-iot-sdk-c)
- [Pythonhoz készült Azure IoT-SDK](https://github.com/azure/azure-iot-sdk-python)
- [Az Azure IoT SDK for node.js használatával](https://github.com/azure/azure-iot-sdk-node)
- [A Javához készült Azure IoT-SDK](https://github.com/azure/azure-iot-sdk-java)
- [Az Azure IoT SDK for .NET csomaggal](https://github.com/azure/azure-iot-sdk-csharp)

Minden egyes eszköz kapcsolódik, amely azonosítja az eszköz egyedi kapcsolati karakterlánc használatával. Egy eszköz csak képes csatlakozni az IoT hub, ha regisztrálva van. Valós eszközöknek az Azure IoT Central-alkalmazást hoz létre, amikor az alkalmazás, amelyet használhat egy kapcsolati karakterláncot állít elő.

## <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-funkciók és az IoT Hub-kapcsolat

Az összes eszköz kommunikációt az IoT Hub használja a következő IoT Hub kapcsolati lehetőségek:

- [Eszközről-a-felhőbe üzenetkezelés](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Ikereszközök](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

A következő táblázat összefoglalja, hogyan Azure IoT Central eszközfunkciók leképezése az IoT Hub-szolgáltatások be:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Mérési: Telemetria | Eszközről-a-felhőbe üzenetkezelés |
| Eszköztulajdonságok | Ikereszköz jelentett tulajdonságait |
| Beállítások | Ikereszköz kívánt és a jelentett tulajdonságok |

Az eszközoldali SDK-k használatával kapcsolatos további tudnivalókért lásd: egyet az alábbi cikkekben például kód:

- [Általános Node.js-ügyfél csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-nodejs.md)
- [A Raspberry Pi-eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz](howto-connect-raspberry-pi-python.md)
- [A DevDiv kit eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz](howto-connect-devkit.md).


## <a name="protocols"></a>Protokollok

Az eszközoldali SDK-k támogatják a következő hálózati protokollok egy IoT hubhoz való csatlakozáshoz:

- MQTT
- AMQP
- HTTPS

Szerinti kiválaszt egyet ezek a különbség a protokollok és útmutatást kapcsolatos információkért lásd: [kommunikációs protokoll kiválasztása](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Ha az eszköz nem tudja használni a támogatott protokollok, az Azure IoT Edge segítségével protokoll átalakítás. IoT Edge feldolgozás kiszervezése az Edge-ben az Azure IoT Central-alkalmazás más üzletiintelligencia-az-a-edge forgatókönyvek támogatja.

## <a name="security"></a>Biztonság

Eszközök és az Azure IoT Central között továbbított összes adat titkosítva van. Az IoT Hub olyan eszköz, amely csatlakozik az IoT Hub eszköz felé néző végpontok bármelyikét, érkező minden kérés hitelesíti. Elkerülheti a hitelesítő adatok cseréje a hálózaton keresztül, az eszköz a aláírt jogkivonatokat használ az hitelesítéséhez. További információkért tekintse meg, [férhet hozzá az IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> Jelenleg az Azure IoT Central csatlakozó eszközök SAS-tokeneket kell használnia. X.509-tanúsítványok nem támogatottak az Azure IoT Central csatlakozó eszközökön.

## <a name="next-steps"></a>További lépések

Most, hogy az eszköz csatlakoztatása az Azure IoT Central megismerkedett az alábbiakban a javasolt következő lépések:

- [Készítse elő és DevKit eszköz csatlakoztatása](howto-connect-devkit.md)
- [Raspberry Pi előkészítése és csatlakoztatása](howto-connect-raspberry-pi-python.md)
- [Általános Node.js-ügyfél csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-nodejs.md)
