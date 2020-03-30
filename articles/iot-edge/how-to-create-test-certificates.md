---
title: Teszttanúsítványok létrehozása - Azure IoT Edge | Microsoft dokumentumok
description: Hozzon létre teszttanúsítványokat, és ismerje meg, hogyan telepítheti őket egy Azure IoT Edge-eszközön az éles környezet előkészítése érdekében.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5afb9b7a6ba1ffb99df064c9f92780dc820b2e8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535986"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Demótanúsítványok létrehozása az IoT Edge-eszköz funkcióinak teszteléséhez

Az IoT Edge-eszközök tanúsítványokat igényelnek a futásidejű, a modulok és az alsóbb rétegbeli eszközök közötti biztonságos kommunikációhoz.
Ha nem rendelkezik hitelesítésszolgáltatóval a szükséges tanúsítványok létrehozásához, demótanúsítványok használatával kipróbálhatja az IoT Edge-szolgáltatásokat a tesztkörnyezetben.
Ez a cikk a tanúsítvány-létrehozási parancsfájlok, amelyek ioT Edge tesztelési funkcióit ismerteti.

Ezek a tanúsítványok 30 napon belül lejárnak, és nem használhatók semmilyen éles környezetben.

Tanúsítványokat hozhat létre bármely gépen, majd másolja őket az IoT Edge-eszközre.
Egyszerűbb az elsődleges gép használatával a tanúsítványok létrehozása, nem pedig az IoT Edge-eszközön.
Az elsődleges gép használatával beállíthatja a parancsfájlokat egyszer, majd megismételheti a folyamatot, hogy tanúsítványokat hozzon létre több eszközhöz.

Az alábbi lépésekkel demótanúsítványokat hozhat létre az IoT Edge-forgatókönyv teszteléséhez:

1. [Parancsfájlok beállítása](#set-up-scripts) az eszközön a tanúsítványok létrehozásához.
2. [Hozza létre a legfelső szintű hitelesítésszolgáltatói tanúsítványt,](#create-root-ca-certificate) amelyet a forgatókönyvhöz tartozó összes többi tanúsítvány aláírására használ.
3. Hozza létre a tesztelni kívánt forgatókönyvhöz szükséges tanúsítványokat:
   * [IoT Edge-eszköz identitástanúsítványok létrehozása](#create-iot-edge-device-identity-certificates) az IoT Hub-eszközkiépítési szolgáltatásautomatikus kiépítés teszteléséhez.
   * [Hozzon létre IoT Edge-eszköz hitelesítésszolgáltatói tanúsítványokat](#create-iot-edge-device-ca-certificates) az éles forgatókönyvek vagy az átjáró-forgatókönyvek teszteléséhez.
   * [Alsóbb rétegbeli eszköztanúsítványok at](#create-downstream-device-certificates) hozhat létre az alsóbb rétegbeli eszközök hitelesítésének teszteléséhez az IoT Hubba egy átjáró-forgatókönyvben.

## <a name="prerequisites"></a>Előfeltételek

Egy fejlesztőgép Git-tel telepítve.

## <a name="set-up-scripts"></a>Parancsfájlok beállítása

Az IoT Edge-tárház a GitHubon tanúsítványgenerálási parancsfájlokat tartalmaz, amelyek segítségével demótanúsítványokat hozhat létre.
Ez a rész a parancsfájlok windowsos vagy Linuxos futtatására való előkészítésének előkészítését ismerteti.
Ha Egy Linux gép, ugorj [előre,](#set-up-on-linux)hogy állítsa be a Linux .

### <a name="set-up-on-windows"></a>Beállítás a Windows rendszerben

Ha demótanúsítványokat szeretne létrehozni egy Windows-eszközön, telepítenie kell az OpenSSL-t, majd klónoznia kell a generációs parancsfájlokat, és be kell állítania őket úgy, hogy helyileg fussanak a PowerShellben.

#### <a name="install-openssl"></a>OpenSSL telepítése

Telepítse az OpenSSL for Windows rendszert arra a számítógépre, amelyet a tanúsítványok létrehozásához használ.
Ha már telepítve van az OpenSSL a Windows-eszközön, kihagyhatja ezt a lépést, de biztosíthatja, hogy az openssl.exe elérhető legyen a PATH környezeti változóban.

Az OpenSSL telepítésének számos módja van, többek között a következő lehetőségek:

* **Egyszerűbb:** Töltse le és telepítse na a [külső OpenSSL binárisfájlokat,](https://wiki.openssl.org/index.php/Binaries)például [az OpenSSL-ből a SourceForge-on.](https://sourceforge.net/projects/openssl/) Adja hozzá az openssl.exe teljes elérési útját a PATH környezeti változóhoz.

* **Ajánlott:** Töltse le az OpenSSL forráskódját, és építse fel a binárisokat a gépén egyedül vagy [a vcpkg-on](https://github.com/Microsoft/vcpkg)keresztül. Az alábbi utasítások vcpkg használatával tölthetik le a forráskódot, fordítják le és telepítik az OpenSSL-t windowsos gépére egyszerű lépésekkel.

   1. Keresse meg azt a könyvtárat, amelyen telepíteni szeretné a vcpkg-ot. Kövesse az utasításokat a [vcpkg](https://github.com/Microsoft/vcpkg)letöltéséhez és telepítéséhez.

   2. A vcpkg telepítése után futtassa a következő parancsot egy PowerShell-parancsból a Windows x64-es OpenSSL csomag telepítéséhez. A telepítés általában körülbelül 5 percet vesz igénybe.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Adja `<vcpkg path>\installed\x64-windows\tools\openssl` hozzá a PATH környezeti változóhoz, hogy az openssl.exe fájl meghívásra is elérhető.

#### <a name="prepare-scripts-in-powershell"></a>Parancsfájlok előkészítése a PowerShellben

Az Azure IoT Edge git-tárház parancsfájlokat tartalmaz, amelyek segítségével teszttanúsítványok at hozhat létre.
Ebben a szakaszban klónozza az IoT Edge-tárt, és végrehajtja a parancsfájlokat.

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.

2. Klónozza az IoT Edge git-tárta, amely parancsfájlokat tartalmaz a demó tanúsítványok létrehozásához. Használja `git clone` a parancsot, vagy [töltse le a ZIP](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Nyissa meg azt a könyvtárat, amelyben dolgozni szeretne. Ebben a cikkben, fogjuk hívni ezt a könyvtárat * \<WRKDIR>*. Ebben a munkakönyvtárban minden tanúsítvány és kulcs létrejön.

4. Másolja a konfigurációs és parancsfájlfájlokat a klónozott tárból a munkakönyvtárba.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Ha a tármunkát ZIP-ként töltötte le, `iotedge-master` akkor a mappa neve, és az elérési út többi része ugyanaz.

5. Engedélyezze a PowerShell a parancsfájlok futtatásához.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Hozza be a parancsfájlok által használt funkciókat a PowerShell globális névterébe.

   ```powershell
   . .\ca-certs.ps1
   ```

   A PowerShell-ablak ban megjelenik egy figyelmeztetés, hogy a parancsfájl által létrehozott tanúsítványok csak tesztelési célokra, és nem használható éles környezetben.

7. Ellenőrizze, hogy az OpenSSL megfelelően lett-e telepítve, és győződjön meg arról, hogy nem lesznévütközés a meglévő tanúsítványokkal. Ha problémák merülnek fel, a parancsfájl kimenetének le kell írnia, hogyan javíthatja őket a rendszeren.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Beállítás Linuxon

Ha demótanúsítványokat szeretne létrehozni egy Windows-eszközön, klónoznia kell a generációs parancsfájlokat, és be kell állítania őket, hogy helyileg fussanak a bash-ben.

1. Klónozza az IoT Edge git-tárta, amely parancsfájlokat tartalmaz a demó tanúsítványok létrehozásához.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Nyissa meg azt a könyvtárat, amelyben dolgozni szeretne. Majd olvassa el ezt a könyvtárat az egész cikket * \<WRKDIR>*. Ebben a könyvtárban minden tanúsítvány és kulcsfájl létrejön.
  
3. Másolja a konfigurációs és parancsfájlfájlokat a klónozott IoT Edge tárműből a munkakönyvtárába.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>Legfelső szintű hitelesítésszolgáltatói tanúsítvány létrehozása

A legfelső szintű hitelesítésszolgáltatói tanúsítvány az összes többi demótanúsítvány t ioT Edge-forgatókönyv teszteléséhez használható.
Ugyanazt a legfelső szintű hitelesítésszolgáltatói tanúsítványt továbbra is használhatja több IoT Edge- vagy alsóbb rétegbeli eszköz demótanúsítványainak kiállításához.

Ha már van egy legfelső szintű hitelesítésszolgáltatói tanúsítvány a munkamappában, ne hozzon létre újat.
Az új legfelső szintű hitelesítésszolgáltatói tanúsítvány felülírja a régit, és a régiből származó alsóbb rétegbeli tanúsítványok nem fognak működni.
Ha több legfelső szintű hitelesítésszolgáltatói tanúsítványt szeretne, ügyeljen arra, hogy azokat külön mappákban kezelje.

Mielőtt folytatná az ebben a szakaszban ismertetett lépéseket, kövesse a [Parancsfájlok beállítása](#set-up-scripts) szakasz lépéseit a demótanúsítvány-létrehozási parancsfájlokkal rendelkező munkakönyvtár előkészítéséhez.

### <a name="windows"></a>Windows

1. Nyissa meg azt a munkakönyvtárat, ahová a tanúsítványgenerálási parancsfájlokat helyezte.

1. Hozza létre a legfelső szintű hitelesítésszolgáltatói tanúsítványt, és írja alá egy köztes tanúsítványt. A tanúsítványok mind a munkakönyvtárba kerülnek.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Ez a parancsfájlparancs több tanúsítványt és kulcsfájlt hoz létre, de amikor a cikkek a **legfelső szintű hitelesítésszolgáltató tanúsítványát kérik,** használja a következő fájlt:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Nyissa meg azt a munkakönyvtárat, ahová a tanúsítványgenerálási parancsfájlokat helyezte.

1. Hozza létre a legfelső szintű hitelesítésszolgáltatói tanúsítványt és egy köztes tanúsítványt.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Ez a parancsfájlparancs több tanúsítványt és kulcsfájlt hoz létre, de amikor a cikkek a **legfelső szintű hitelesítésszolgáltató tanúsítványát kérik,** használja a következő fájlt:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>IoT Edge-eszköz hitelesítésszolgáltatói nak létrehozása

Minden, éles környezetben lévő IoT Edge-eszköznek szüksége van egy eszköz hitelesítésszolgáltatói tanúsítványára, amely a config.yaml fájlból hivatkozik.
Az eszköz hitelesítésszolgáltatói tanúsítványa felelős az eszközön futó modulok tanúsítványainak létrehozásáért.
Az IoT Edge-eszköz is így ellenőrzi az identitását, amikor alsóbb rétegbeli eszközökhöz csatlakozik.

Az eszközhitelesítési tanúsítványok az IoT Edge-eszközön található config.yaml fájl **Tanúsítvány** szakaszába lépnek.

Mielőtt folytatná az ebben a szakaszban ismertetett lépéseket, kövesse a [Parancsfájlok beállítása](#set-up-scripts) és a [legfelső szintű hitelesítésszolgáltató tanúsítványának létrehozása](#create-root-ca-certificate) szakaszlépéseit.

### <a name="windows"></a>Windows

1. Keresse meg azt a munkakönyvtárat, amely rendelkezik a tanúsítványgenerálási parancsfájlokkal és a legfelső szintű hitelesítésszolgáltatói tanúsítvánnyal.

2. Hozza létre az IoT Edge-eszköz hitelesítésszolgáltatói tanúsítványát és személyes kulcsát a következő paranccsal. Adja meg a hitelesítésszolgáltatói tanúsítvány nevét, például **a MyEdgeDeviceCA**nevet, amely a kimeneti fájlok elnevezésére szolgál.

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   Ez a parancsfájlparancs több tanúsítványt és kulcsfájlt hoz létre. A következő tanúsítványt és kulcspárt át kell másolni egy IoT Edge-eszközre, és hivatkozni kell a config.yaml fájlra:

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

Az átjáró eszköz neve átadott ezeket a parancsfájlokat nem lehet ugyanaz, mint a "hostname" paraméter config.yaml, vagy az eszköz azonosítóját az IoT Hub.
A parancsfájlok segítségével elkerülheti a problémákat azáltal, hogy egy ".ca" karakterláncot fűz az átjáróeszköz nevéhez, hogy megakadályozza a név ütközését abban az esetben, ha a felhasználó az IoT Edge-et ugyanazt a nevet használja mindkét helyen.
Azonban célszerű elkerülni az azonos név használatát.

### <a name="linux"></a>Linux

1. Keresse meg azt a munkakönyvtárat, amely rendelkezik a tanúsítványgenerálási parancsfájlokkal és a legfelső szintű hitelesítésszolgáltatói tanúsítvánnyal.

2. Hozza létre az IoT Edge-eszköz hitelesítésszolgáltatói tanúsítványát és személyes kulcsát a következő paranccsal. Adja meg a hitelesítésszolgáltatói tanúsítvány nevét, például **a MyEdgeDeviceCA**nevet, amely a kimeneti fájlok elnevezésére szolgál.

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   Ez a parancsfájlparancs több tanúsítványt és kulcsfájlt hoz létre. A következő tanúsítványt és kulcspárt át kell másolni egy IoT Edge-eszközre, és hivatkozni kell a config.yaml fájlra:

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

Az átjáró eszköz neve átadott ezeket a parancsfájlokat nem lehet ugyanaz, mint a "hostname" paraméter config.yaml, vagy az eszköz azonosítóját az IoT Hub.
A parancsfájlok segítségével elkerülheti a problémákat azáltal, hogy egy ".ca" karakterláncot fűz az átjáróeszköz nevéhez, hogy megakadályozza a név ütközését abban az esetben, ha a felhasználó az IoT Edge-et ugyanazt a nevet használja mindkét helyen.
Azonban célszerű elkerülni az azonos név használatát.

## <a name="create-iot-edge-device-identity-certificates"></a>IoT Edge-eszközidentitás-tanúsítványok létrehozása

Az eszközidentitás-tanúsítványok az [IoT Hub-eszközök azure IoT Hub-eszközkiépítési szolgáltatáson (DPS)](../iot-dps/index.yml)keresztül történő kiépítéséhez használatosak.

Az eszközidentitás-tanúsítványok a config.yaml fájl **kiépítés** szakaszába lépnek az IoT Edge-eszközön.

Mielőtt folytatná az ebben a szakaszban ismertetett lépéseket, kövesse a [Parancsfájlok beállítása](#set-up-scripts) és a [legfelső szintű hitelesítésszolgáltató tanúsítványának létrehozása](#create-root-ca-certificate) szakaszlépéseit.

### <a name="windows"></a>Windows

Hozza létre az IoT Edge-eszköz identitástanúsítványát és a személyes kulcsot a következő paranccsal:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

A parancsnak adott nevet az IoT Edge-eszköz az IoT Hub eszközazonosítója lesz.

Az új eszközidentitás-parancs több tanúsítványt és kulcsfájlt hoz létre, köztük kettőt, amelyet a DPS-ben való egyéni regisztráció létrehozásakor és az IoT Edge futásidejének telepítésekor fog használni:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Hozza létre az IoT Edge-eszköz identitástanúsítványát és a személyes kulcsot a következő paranccsal:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

A parancsnak adott nevet az IoT Edge-eszköz az IoT Hub eszközazonosítója lesz.

A parancsfájl több tanúsítványt és kulcsfájlt hoz létre, köztük kettőt, amelyet a DPS-ben való egyéni igénylések létrehozásakor és az IoT Edge futásidejének telepítésekor fog használni:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-downstream-device-certificates"></a>Alsóbb rétegbeli eszköztanúsítványok létrehozása

Ha egy átjáró-forgatókönyvhöz állít be egy alsóbb rétegbeli IoT-eszközt, demótanúsítványokat hozhat létre az X.509-es hitelesítéshez.
Az IoT-eszközök X.509-es tanúsítványokkal kétféleképpen hitelesíthetik: önaláírt tanúsítványok vagy hitelesítésszolgáltatói (CA) aláírt tanúsítványok használatával.
Az X.509 önaláírt hitelesítés, más néven ujjlenyomat-hitelesítés, létre kell hoznia az új tanúsítványokat elhelyezni az IoT-eszközön.
Ezek a tanúsítványok egy ujjlenyomatot rendelkeznek bennük, amelyet az IoT Hubhitelesítéshez megoszt.
Az X.509-es hitelesítésszolgáltató (CA) aláírt hitelesítési hitelesítésesetén az IoT Hubban regisztrált legfelső szintű hitelesítésszolgáltatói tanúsítványra van szükség, amelyet az IoT-eszköz tanúsítványainak aláírásához használ.
A legfelső szintű hitelesítésszolgáltatói tanúsítvány vagy annak bármely köztes tanúsítványa által kiállított tanúsítványt használó bármely eszköz hitelesítése engedélyezett.

A tanúsítvány-létrehozási parancsfájlok segítségével demótanúsítványokat állíthat ki a hitelesítési forgatókönyvek bármelyikének teszteléséhez.

Mielőtt folytatná az ebben a szakaszban ismertetett lépéseket, kövesse a [Parancsfájlok beállítása](#set-up-scripts) és a [legfelső szintű hitelesítésszolgáltató tanúsítványának létrehozása](#create-root-ca-certificate) szakaszlépéseit.

### <a name="self-signed-certificates"></a>Önaláírt tanúsítványok

Ha önaláírt tanúsítványokkal hitelesít egy IoT-eszközt, a megoldás legfelső szintű hitelesítésszolgáltatói tanúsítványa alapján kell létrehoznia az eszköztanúsítványokat.
Ezután lekéri a hexadecimális "ujjlenyomatot" a tanúsítványokból, amelyeket az IoT Hubnak biztosít.
Az IoT-eszköz is szüksége van egy másolatot az eszköz tanúsítványok, hogy hitelesíthető az IoT Hub.

#### <a name="windows"></a>Windows

1. Keresse meg azt a munkakönyvtárat, amely rendelkezik a tanúsítványgenerálási parancsfájlokkal és a legfelső szintű hitelesítésszolgáltatói tanúsítvánnyal.

2. Hozzon létre két (elsődleges és másodlagos) tanúsítványt az alsóbb rétegbeli eszközhöz. Egy egyszerű elnevezési konvenció használata a tanúsítványok létrehozása az IoT-eszköz nevével, majd az elsődleges vagy másodlagos címke. Példa:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Ez a parancsfájlparancs több tanúsítványt és kulcsfájlt hoz létre. A következő tanúsítványt és kulcspárokat át kell másolni az alsóbb rétegbeli IoT-eszközre, és hivatkozni kell az IoT Hubhoz csatlakozó alkalmazásokban:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Az SHA1-ujjlenyomat (az IoT Hub-környezetekben található ujjlenyomat) lekérése az egyes tanúsítványokból. Az ujjlenyomat egy 40 hexadecimális karakterlánc. A tanúsítvány megtekintéséhez és az ujjlenyomat megkereséséhez használja a következő openssl parancsot:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Futtassa ezt a parancsot kétszer, egyszer az elsődleges tanúsítványhoz, és egyszer a másodlagos tanúsítványhoz. Mindkét tanúsítványhoz ujjlenyomatot ad, ha önaláírt X.509-es tanúsítványokkal regisztrál egy új IoT-eszközt.

#### <a name="linux"></a>Linux

1. Keresse meg azt a munkakönyvtárat, amely rendelkezik a tanúsítványgenerálási parancsfájlokkal és a legfelső szintű hitelesítésszolgáltatói tanúsítvánnyal.

2. Hozzon létre két (elsődleges és másodlagos) tanúsítványt az alsóbb rétegbeli eszközhöz. Egy egyszerű elnevezési konvenció használata a tanúsítványok létrehozása az IoT-eszköz nevével, majd az elsődleges vagy másodlagos címke. Példa:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Ez a parancsfájlparancs több tanúsítványt és kulcsfájlt hoz létre. A következő tanúsítványt és kulcspárokat át kell másolni az alsóbb rétegbeli IoT-eszközre, és hivatkozni kell az IoT Hubhoz csatlakozó alkalmazásokban:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Az SHA1-ujjlenyomat (az IoT Hub-környezetekben található ujjlenyomat) lekérése az egyes tanúsítványokból. Az ujjlenyomat egy 40 hexadecimális karakterlánc. A tanúsítvány megtekintéséhez és az ujjlenyomat megkereséséhez használja a következő openssl parancsot:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Az elsődleges és a másodlagos ujjlenyomatot is megadja, amikor önaláírt X.509-es tanúsítványokkal regisztrál egy új IoT-eszközt.

### <a name="ca-signed-certificates"></a>Hitelesítésszolgáltató által aláírt tanúsítványok

Ha önaláírt tanúsítványokkal hitelesít egy IoT-eszközt, fel kell töltenie a megoldás legfelső szintű hitelesítésszolgáltatói tanúsítványát az IoT Hubra.
Ezután egy ellenőrzést hajt végre annak igazolására, hogy az IoT Hub, hogy a legfelső szintű hitelesítésszolgáltató-tanúsítvány a saját.
Végül ugyanazt a legfelső szintű hitelesítésszolgáltatói tanúsítványt használja az IoT-eszközön való üzembe helyezéshez szükséges eszköztanúsítványok létrehozásához, hogy hitelesíthesse magát az IoT Hubbal.

Az ebben a szakaszban található tanúsítványok az [X.509-es biztonság beállítása az Azure IoT-központban](../iot-hub/iot-hub-security-x509-get-started.md)című szakaszban található lépéseket ismertetik.

#### <a name="windows"></a>Windows

1. Töltse fel a legfelső szintű hitelesítésszolgáltató `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`tanúsítványfájlját a munkakönyvtárból az IoT-központba.

2. Használja az Azure Portalon megadott kódot annak ellenőrzéséhez, hogy ön a legfelső szintű hitelesítésszolgáltató-tanúsítvány-e.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Hozzon létre egy tanúsítványláncot az alsóbb rétegbeli eszközhöz. Használja ugyanazt az eszközazonosítót, amely az eszköz regisztrálva van az IoT Hubban.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Ez a parancsfájlparancs több tanúsítványt és kulcsfájlt hoz létre. A következő tanúsítványt és kulcspárokat át kell másolni az alsóbb rétegbeli IoT-eszközre, és hivatkozni kell az IoT Hubhoz csatlakozó alkalmazásokban:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Töltse fel a legfelső szintű hitelesítésszolgáltató `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`tanúsítványfájlját a munkakönyvtárból az IoT-központba.

2. Használja az Azure Portalon megadott kódot annak ellenőrzéséhez, hogy ön a legfelső szintű hitelesítésszolgáltató-tanúsítvány-e.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Hozzon létre egy tanúsítványláncot az alsóbb rétegbeli eszközhöz. Használja ugyanazt az eszközazonosítót, amely az eszköz regisztrálva van az IoT Hubban.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Ez a parancsfájlparancs több tanúsítványt és kulcsfájlt hoz létre. A következő tanúsítványt és kulcspárokat át kell másolni az alsóbb rétegbeli IoT-eszközre, és hivatkozni kell az IoT Hubhoz csatlakozó alkalmazásokban:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
