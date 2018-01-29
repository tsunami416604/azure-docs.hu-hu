---
title: "Hozzon létre egy átlátszó átjáróeszköz Azure IoT peremhálózati |} Microsoft Docs"
description: "Azure IoT peremhálózati segítségével hozzon létre egy átlátszó átjáróeszköz, amely képes több eszközökre vonatkozó információkat"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/04/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 1a35a6adae1eec88b314252ea0e3f2eb86f10c1d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>Hozzon létre egy IoT peremhálózati eszköz, amely átlátszó átjáróként – előzetes

Ez a cikk átlátszó átjáróként IoT peremhálózati eszköz használatára vonatkozó részletes útmutatást. Ez a cikk a többi kifejezés *IoT peremhálózati átjáró* átlátszó átjáróként használt IoT peremhálózati eszköz hivatkozik. Részletes információkat lásd: [hogyan egy IoT-peremhálózati eszköz egy átjáró használható][lnk-edge-as-gateway], ami fogalmi áttekintése. 

>[!NOTE]
>Currently:
> * Az átjáró le van választva az IoT-központ, ha az eszközöket az átjáró nem hitelesíthető.
> * Az IoT-peremeszközök IoT peremhálózati átjáró nem tud kapcsolódni.

## <a name="understand-the-azure-iot-device-sdk"></a>Az Azure IoT-eszközök SDK ismertetése


A peremhálózati hub, minden IoT peremhálózati eszköz a telepített eszközöket a következő primitívek mutatja:

* eszköz-felhő és a felhő eszközre üzenetek
* közvetlen módszer
* eszköz iker műveletek

Alsóbb rétegbeli eszközök jelenleg nem képes fájlfeltöltés használják, amikor egy IoT peremhálózati átjárón keresztül csatlakoznak.

Ha eszközök csatlakoznak az Azure IoT eszközt SDK IoT átjárónak, gondoskodni kell:

* Állítsa be az alsóbb rétegbeli eszközt egy olyan kapcsolati karakterlánccal hivatkozik az átjáró eszköz állomásnév; és
* Győződjön meg arról, hogy az alsóbb rétegbeli eszköz Megbízhatóságok elfogadja a kapcsolatot az átjáró eszköz által használt tanúsítványt.

Az Azure IoT peremhálózati futásidejű a vezérlő parancsfájl használatával történő telepítésekor egy tanúsítvány jön létre biztonsági központ az oktatóanyag a [IoT Edge telepítse a Windows a szimulált eszköz] [ lnk-tutorial1-win] és [Linux][lnk-tutorial1-lin]. A tanúsítvány bejövő TLS-kapcsolatok fogadására a peremhálózati hub használják, és az alsóbb rétegbeli eszköz megbízhatónak, amikor az átjáró eszköz csatlakozik kell.

A tanúsítvány-infrastruktúra, amely lehetővé teszi a megbízhatósági kapcsolat szükséges az eszköz-átjáró topológiát hozhat létre. Ez a cikk azt feltételezzük, hogy az ugyanazon tanúsítványt telepítő engedélyezése szeretné használni [X.509 hitelesítésszolgáltató biztonsági] [ lnk-iothub-x509] az IoT-központ, amely magában foglalja egy adott IoT-központ társított X.509 Hitelesítésszolgáltatói tanúsítvány (a *IoT hub tulajdonos hitelesítésszolgáltató*), és a tanúsítványok, a CA, az IoT-peremeszközök telepített aláírva.

>[!IMPORTANT]
>Jelenleg, IoT peremeszközök és az alsóbb rétegbeli eszközök csak használhatják [SAS-tokenje] [ lnk-iothub-tokens] az IoT-központ való hitelesítéshez szükséges. A tanúsítványok használandó csak a levél és az átjáró eszköz között, a TLS-kapcsolat ellenőrzése.

A konfigurációban **IoT hub tulajdonos hitelesítésszolgáltató** is:
* A telepítő minden IoT peremhálózati eszköz; IoT peremhálózati futásidejű aláíró tanúsítvány és
* a nyilvános kulcsú tanúsítvány, az alsóbb rétegbeli eszközök telepítése.

Az eredmény olyan megoldás, amely lehetővé teszi, hogy minden IoT peremhálózati eszköz használata az átjáró az eszközöket, amíg az azonos IoT hub csatlakoznak.

## <a name="create-the-certificates-for-test-scenarios"></a>A tesztelési forgatókönyvek tanúsítványok létrehozása

Használhatja a Powershell sample és Bash parancsfájlok ismertetett [Hitelesítésszolgáltatói tanúsítvány minta kezelése] [ lnk-ca-scripts] létrehozni egy önaláírt **IoT hub tulajdonos hitelesítésszolgáltató** és eszköztanúsítványok az aláírt vele.

>[!IMPORTANT]
>Célja, hogy ez a minta csak tesztelési célokra. Éles környezetben, tekintse meg [IoT üzembe] [ lnk-iothub-secure-deployment] az Azure IoT készletén az IoT-megoldásból biztonságos, és ennek megfelelően a tanúsítvány kiépítéséhez.


1. A Microsoft Azure IoT SDK-k és a szalagtárak klónozza a Githubról C-hez:

   ```
   git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
   ```

2. A tanúsítvány parancsfájlok telepítéséhez kövesse az utasításokat a **1. lépés - az első telepítés** a [Hitelesítésszolgáltatói tanúsítvány minta kezelése][lnk-ca-scripts]. 
3. Létrehozásához a **IoT hub tulajdonos hitelesítésszolgáltató**, kövesse az utasításokat a **2. lépés – a tanúsítványlánc létrehozása**. Ez a fájl-k segítségével az eszközöket a kapcsolat ellenőrzése.
4. Az átjáró eszköz tanúsítványt létrehozni, használja a Bash vagy a PowerShell-utasításokat:

### <a name="bash"></a>Bash

Az új eszköz-tanúsítvány létrehozásához:

   ```
   ./certGen.sh create_edge_device_certificate myGateway
   ```

Új fájlok jönnek létre:.\certs\new-edge-device.* tartalmazza a nyilvános kulcs és a PFX és.\private\new-edge-device.key.pem tartalmazza az eszköz titkos kulcsot.
 
Az a `certs` directory, a teljes lánc eszköz nyilvános kulcsának eléréséhez a következő parancsot:

   ```
   cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem
   ```

### <a name="powershell"></a>PowerShell

Az új eszköz-tanúsítvány létrehozásához: 
   ```
   New-CACertsEdgeDevice myGateway
   ```

MyEdgeDevice * új fájlok jönnek létre, amely a nyilvános kulcs titkos kulcs és a tanúsítvány PFX tartalmaz. 

Ha az aláírási folyamat során a jelszó megadását kéri, adja meg "1234".

## <a name="configure-a-gateway-device"></a>Átjáró eszköz konfigurálása

Az IoT-peremhálózati eszköz most konfigurálnia kell az eszköz tanúsítványát az előző szakaszban létrehozott használandó átjáró konfigurálásához.

Feltételezzük, hogy a következő fájlneveket, a fenti mintaparancsfájlok:

| Kimenet | Bash parancsfájlok | PowerShell |
| ------ | ----------- | ---------- |
| Eszköz tanúsítványa | `certs/new-edge-device.cert.pem` | `certs/new-edge-device.cert.pem` |
| Titkos kulcs | `private/new-edge-device.cert.pem` | `private/new-edge-device.cert.pem` |
| Eszköz tanúsítványlánc | `certs/new-edge-device-full-chain.cert.pem` | `certs/new-edge-device-full-chain.cert.pem` |
| Az IoT hub tulajdonos hitelesítésszolgáltató | `certs/azure-iot-test-only.root.ca.cert.pem` | `RootCA.pem` |

Az eszköz és a tanúsítvány az IoT-Edge futásidejű információt tartalmaznak. 
 
A Linux a Bash kimeneti használatával:

   ```
   sudo iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem
   ```

A Windows PowerShell kimenete használatával:

   ```
   iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/RootCA.pem
   ```

Alapértelmezés szerint a mintaparancsfájlok számára ne adja egy jelszót a titkos kulcs. Ha egy jelszót, adja hozzá a következő paramétert:

   ```
   --device-ca-passphrase {passphrase}
   ```

A parancsfájl kérni fogja, meg kell adnia egy jelszót a peremhálózati ügynök tanúsítványt. Ez a parancs után indítsa újra az IoT-Edge futásidejű:

   ```
   iotedgectl restart
   ```

## <a name="configure-a-downstream-device"></a>Egy alsóbb rétegbeli eszközök konfigurálása

Alsóbb rétegbeli lehet bármely alkalmazás használata a [Azure IoT-eszközök SDK][lnk-devicesdk], például az egyszerű egy ismertetett [csatlakoztassa az eszközt az IoT hub .NET használatával] [ lnk-iothub-getstarted].

Először egy alsóbb rétegbeli alkalmazást rendelkezik megbízzanak a **IoT hub tulajdonos hitelesítésszolgáltató** ellenőrizni fogja az átjáró eszközökre a TLS-kapcsolatokhoz tanúsítvány. Ezt a lépést általában két módon hajtható végre: az operációs rendszer szintjén, vagy (az egyes nyelvek) az alkalmazás szintjén.

Például a .NET-alkalmazásokban, adhat hozzá a következő kódrészletet a hitelesítés a PEM-formátumba elérési útját `certPath`. Attól függően, hogy melyik verzióját használja a parancsfájl elérési hivatkozik, vagy `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) vagy `RootCA.pem` (Powershell).

   ```
   using System.Security.Cryptography.X509Certificates;
   
   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

Ehhez a lépéshez az operációs rendszer szintjén nem egyezik, Linux terjesztésekről és a Windows között.

A második lépés, hogy az IoT Hub eszköz sdk inicializálni az átjáróeszköz állomásnév hivatkozik egy olyan kapcsolati karakterlánccal.
Hozzáfűzésével ebben az esetben a `GatewayHostName` tulajdonság az eszköz kapcsolati karakterlánc módosításait. Például Íme egy minta eszköz kapcsolati karakterláncot egy eszközhöz, azt hozzáfűzi a `GatewayHostName` tulajdonság:

   ```
   HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com
   ```

Ezeket a lépéseket két lehetővé teszik az eszköz csatlakozni az átjáróeszközhöz.

## <a name="next-steps"></a>További lépések
[Követelmények és eszközök IoT peremhálózati modulok adattárházzal][lnk-module-dev].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/modules-preview/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus