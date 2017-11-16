---
title: "Hozzon létre egy átjáróeszköz Azure IoT peremhálózati |} Microsoft Docs"
description: "Azure IoT peremhálózati segítségével hozzon létre egy átjáróeszköz, amely képes több eszközökre vonatkozó információkat"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e1337ddf5ed84a06a62e2faa198f3e8fb49bc3bd
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-iot-edge-gateway-device-to-process-data-from-other-iot-devices---preview"></a>Hozzon létre egy IoT peremhálózati átjáró eszköz adatfeldolgozásra történő más IoT-eszközökről származó – előzetes

Átjárók IoT peremeszközök használandó két módja van:

* Csatlakozás használó eszközöket [Azure IoT-eszközök SDK][lnk-devicesdk];
* Az IoT-központ protokollt nem használó eszközök csatlakoztatásához.

IoT peremhálózati eszköz egy átjáró használatakor töltse le a következő előnyökkel jár:

* **Kapcsolat multiplexáló**. Az IoT hubhoz IoT peremhálózati eszköz keresztül csatlakozó összes eszköz ugyanazt az alapul szolgáló kapcsolatot fogja használni.
* **Forgalom simítás**. Az IoT-peremhálózati eszköz automatikusan megvalósítandó exponenciális leállítási szabályozás, az üzenetek a helyi megőrzése közben az IoT-központ esetén. Ezáltal a megoldás rugalmas a forgalmat a teljesítményt.
* **Korlátozott offline támogatást**. Az átjáró eszköz helyileg tárolja, amely az IoT-központ nem kézbesíthető üzeneteket.

Ebben a cikkben azt fel fogja hívni *IoT peremhálózati átjáró* átjáróként használt IoT peremhálózati eszköz.

>[!NOTE]
>Jelenleg:
> * Alsóbb rétegbeli beléptetett mobileszközök nem tudja hitelesíteni az átjáró, ha az átjáró le van választva az IoT-központ; és
> * Az IoT-peremeszközök IoT peremhálózati átjáró nem tud kapcsolódni.

## <a name="use-the-azure-iot-device-sdk"></a>Az Azure IoT-eszközök SDK használata

A peremhálózati hub, minden IoT peremhálózati eszköz a telepített eszközöket a következő primitívek mutatja:

* eszköz-felhő és a felhő eszközre üzenetek;
* közvetlen módszerek; és
* eszköz iker műveletek.

>[!NOTE]
>Alsóbb rétegbeli eszközök jelenleg nem képes fájlfeltöltés használják, amikor egy IoT peremhálózati átjárón keresztül csatlakoznak.

Ha eszközök csatlakoznak az Azure IoT eszközt SDK IoT átjárónak, gondoskodni kell:

* Állítsa be az alsóbb rétegbeli eszközt egy olyan kapcsolati karakterlánccal hivatkozik az átjáró eszköz állomásnév; és
* Győződjön meg arról, hogy az alsóbb rétegbeli eszköz Megbízhatóságok elfogadja a kapcsolatot az átjáró eszköz által használt tanúsítványt.

Az Azure IoT peremhálózati futásidejű a vezérlő parancsfájl használatával történő telepítésekor egy tanúsítvány jön létre biztonsági központ úgy, ahogy az az oktatóanyag telepítése IoT Edge szimulált eszköz a [Windows] [ lnk-tutorial1-win] és [Linux][lnk-tutorial1-lin]. A tanúsítvány bejövő TLS-kapcsolatok fogadására a peremhálózati hub használják, és az alsóbb rétegbeli eszköz megbízhatónak, amikor az átjáró eszköz csatlakozik kell.

A tanúsítvány-infrastruktúra, amely lehetővé teszi a megbízhatósági kapcsolat szükséges az eszköz-átjáró topológiát hozhat létre. Ez a cikk azt feltételezzük, hogy az ugyanazon tanúsítványt telepítő engedélyezése szeretné használni [X.509 hitelesítésszolgáltató biztonsági] [ lnk-iothub-x509] az IoT-központ, amely magában foglalja a konkrét IoT-központ társított X.509 CA-tanúsítvány (a  *Az IoT hub tulajdonos hitelesítésszolgáltató*), és a tanúsítványok, a CA, az IoT-peremeszközök telepített aláírva.

>[!IMPORTANT]
>Jelenleg, IoT peremeszközök és az alsóbb rétegbeli eszközök csak használhatják [SAS-tokenje] [ lnk-iothub-tokens] az IoT-központ való hitelesítéshez szükséges. A tanúsítványok használandó csak a levél és az átjáró eszköz között, a TLS-kapcsolat ellenőrzése.

A konfigurációban **IoT hub tulajdonos hitelesítésszolgáltató** mint:
* a telepítő minden IoT peremhálózati eszköz, és is IoT peremhálózati futásidejű aláíró tanúsítvány
* a nyilvános kulcsú tanúsítvány, az alsóbb rétegbeli eszközök telepítése.

Ennek eredményeképpen az olyan megoldás, amely lehetővé teszi, hogy minden IoT peremhálózati eszköz használata az átjáró az eszközöket, amíg az azonos IoT hub csatlakoznak.

### <a name="create-the-certificates-for-test-scenarios"></a>A tesztelési forgatókönyvek tanúsítványok létrehozása

Használhatja a Powershell sample és Bash parancsfájlok ismertetett [Hitelesítésszolgáltatói tanúsítvány minta kezelése] [ lnk-ca-scripts] létrehozni egy önaláírt **IoT hub tulajdonos hitelesítésszolgáltató** és eszköztanúsítványok az aláírt vele.

1. Hajtsa végre az 1. lépésében [Hitelesítésszolgáltatói tanúsítvány minta kezelése] [ lnk-ca-scripts] a parancsfájlok telepítéséhez.
2. A 2 létrehozásához a **IoT hub tulajdonos hitelesítésszolgáltató**, ezt a fájlt az alárendelt eszközök által használandó kapcsolat.

Az átjáró eszköz tanúsítvány létrehozásához kövesse az alábbi utasításokat.

#### <a name="bash"></a>Bash

* Futtatás `./certGen.sh create_edge_device_certificate myGateway` az új eszköz tanúsítvány létrehozásához.  
  Ez a nyilvános kulcs és PFX és.\private\new-edge-device.key.pem, amely tartalmazza az eszköz titkos kulcsot tartalmazó fájlokat.\certs\new-edge-device.* hoz létre.  
* `cat new-edge-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-edge-device-full-chain.cert.pem`a nyilvános kulcs beszerzése.
* `./private/new-edge-device.cert.pem`az eszköz titkos kulcsot tartalmaz.

#### <a name="powershell"></a>PowerShell

* Futtatás `New-CACertsEdgeDevice myGateway` az új eszköz tanúsítvány létrehozásához.
  Ezzel létrehoz fájlok myEdgeDevice *, amely tartalmazza a nyilvános kulcs titkos kulcs és a tanúsítvány PFX.  Ha az aláírási folyamat során a jelszó megadását kéri, adja meg "1234".


>[!IMPORTANT]
>Célja, hogy ez a minta csak tesztelési célokra. Éles környezetben, tekintse meg [IoT üzembe] [ lnk-iothub-secure-deployment] az Azure IoT készletén az IoT-megoldásból biztonságos, és ennek megfelelően a tanúsítvány kiépítéséhez.

### <a name="configure-an-iot-edge-device-as-a-gateway"></a>IoT peremhálózati eszköz egy átjáró konfigurálása

Az IoT-peremhálózati eszköz most konfigurálnia kell az eszköz tanúsítványát az előző szakaszban létrehozott használandó átjáró konfigurálásához.

Feltételezzük, hogy a következő fájlneveket, a fenti mintaparancsfájlok:

| Kimenet | Bash parancsfájlok | PowerShell |
| ------ | ----------- | ---------- |
| Eszköz tanúsítványa | `certs/new-edge-device.cert.pem` | `certs/new-edge-device.cert.pem` |
| Titkos kulcs | `private/new-edge-device.cert.pem` | `private/new-edge-device.cert.pem` |
| Eszköz tanúsítványlánc | `certs/new-edge-device-full-chain.cert.pem` | `certs/new-edge-device-full-chain.cert.pem` |
| Az IoT hub tulajdonos hitelesítésszolgáltató | `certs/azure-iot-test-only.root.ca.cert.pem` | `RootCA.pem` |

 Adatproblémák történő telepítésére a szimulált eszköz telepítése Azure IoT peremhálózati ismertetett [Windows] [ lnk-tutorial1-win] vagy [Linux][lnk-tutorial1-lin], hogy Adja meg a fenti adatokat az IoT-Edge futásidejű. 
 
 A Linux:

        sudo iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem

A Windows rendszerben:

        iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/RootCA.pem

Alapértelmezés szerint a mintaparancsfájlok számára ne adja egy jelszót a titkos kulcs. Ha egy jelszót, adja hozzá a következő paramétert:

        --device-ca-passphrase {passphrase}

A parancsfájl kérni fogja a jelszó, a peremhálózati ügynök tanúsítványt.
Ez a parancs után indítsa újra az IoT-Edge futásidejű rendelkezik.

### <a name="configure-an-iot-hub-device-application-as-a-downstream-device"></a>IoT Hub eszköz alkalmazások konfigurálása alsóbb rétegbeli eszközként

Alsóbb rétegbeli lehet bármely alkalmazás használata a [Azure IoT-eszközök SDK][lnk-devicesdk], például az egyszerű egy ismertetett [csatlakoztassa az eszközt az IoT hub .NET használatával] [ lnk-iothub-getstarted].

Először egy alsóbb rétegbeli alkalmazást rendelkezik megbízzanak a **IoT hub tulajdonos hitelesítésszolgáltató** ellenőrizni fogja az átjáró eszközökre a TLS-kapcsolatokhoz tanúsítvány. Ezt a lépést általában két módon hajtható végre: az operációs rendszer szintjén, vagy (az egyes nyelvek) az alkalmazás szintjén.

Például a .NET-alkalmazásokban, adhat hozzá a következő kódrészletet a hitelesítés a PEM-formátumba elérési útját `certPath`.

        using System.Security.Cryptography.X509Certificates;
        
        ...

        X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
        store.Open(OpenFlags.ReadWrite);
        store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
        store.Close();

Vegye figyelembe, hogy a fentiekben említett mintaparancsfájlok hoz létre a nyilvános kulcsot a fájl `certs/azure-iot-test-only.root.ca.cert.pem` (Bash), vagy `RootCA.pem` (Powershell).

Ehhez a lépéshez az operációs rendszer szintjén nem egyezik, Linux terjesztésekről és a Windows között.

A második lépés, hogy az IoT Hub eszköz sdk inicializálni az átjáróeszköz állomásnév hivatkozik egy olyan kapcsolati karakterlánccal.
Hozzáfűzésével ebben az esetben a `GatewayHostName` tulajdonság az eszköz kapcsolati karakterlánc módosításait. Például Íme egy minta eszköz kapcsolati karakterláncot egy eszközhöz, azt hozzáfűzi a `GatewayHostName` tulajdonság:

        HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com

Ezeket a lépéseket két lehetővé teszi az eszköz alkalmazás csatlakozni az átjáróeszközhöz.

## <a name="use-other-protocols"></a>Egyéb protokollok

Az átjáró eszköz fő funkcióinak egyik alsóbb rétegbeli eszközök protokollok értelmezése. Az IoT-központ protokollt nem használó eszközök csatlakoztatásához, létrehozhat egy IoT biztonsági modult, amely elvégzi ezt a fordítást, és csatlakozik az IoT-központ alárendelt eszköz nevében.

Beállíthatja úgy, hogy hozzon létre egy *átlátszó* vagy *nem átlátszó* átjáró:

| &nbsp; | Transzparens átjáró | Nem átlátszó átjáró|
|--------|-------------|--------|
| Az IoT-központ identitásjegyzékhez tárolt identitások | A minden csatlakoztatott eszközön azonosítói | Csak az átjáró eszköz identitása |
| Ikereszközök | Minden csatlakoztatott eszközön van a saját eszköz iker | Csak az átjáró eszköz és a modul twins rendelkezik |
| Közvetlen módszerek és a felhő-eszközre küldött üzenetek | A felhő külön-külön lehet oldani minden csatlakoztatott eszközön | A felhő csak lehet oldani az átjáró eszköz |
| [Az IoT-központ szabályozások és kvóták][lnk-iothub-throttles-quotas] | Minden eszköz alkalmazása | Az átjáró eszköz alkalmazása |

Ha egy nem átlátszó átjáró mintát használ, adott átjárón keresztül csatlakozó összes eszközök azonos felhő eszközre beolvasása, amely legfeljebb 50 üzenetek tartalmazhat osztják meg. Ez azt jelenti, hogy az átjáró nem átlátszó mintát kell használni, csak akkor, ha nagyon néhány olyan eszközt minden mező átjárón keresztül kapcsolódik, és a felhő eszközre forgalmukat értéke alacsony.

Egy nem átlátszó átjáró végrehajtásakor a protokoll fordítási modul használja a modul kapcsolati karakterláncot.

Transzparens átjáró végrehajtásakor a modul az IoT Hub eszköz ügyfelet, a kapcsolati karakterláncok az alsóbb rétegbeli eszközök több példányt hoz létre.

## <a name="next-steps"></a>Következő lépések

- [Követelmények és eszközök IoT peremhálózati modulok adattárházzal][lnk-module-dev].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/CACertToolEdge/tools/CACertificates/CACertificateOverview.md