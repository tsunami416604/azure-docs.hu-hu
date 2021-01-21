---
title: A Windows Azure IoT Edge telepítése | Microsoft Docs
description: Windows-tárolók telepítése Azure IoT Edge Windows-eszközökön
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
ms.openlocfilehash: 7857f93e8c767f270041bb6bf041447786ce19ff
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633991"
---
# <a name="install-and-manage-azure-iot-edge-for-windows"></a>A Windows Azure IoT Edge telepítése és kezelése

A Windows Azure IoT Edge közvetlenül a gazdagépen futó Windows-eszközön fut, és Windows-tárolók használatával futtatja az üzleti logikát az Edge-ben.

Az Azure IoT Edge futtatókörnyezet az eszköz IoT Edge eszközre való bekapcsolása. A futtatókörnyezet az eszközökön kisméretű, málna PI-ként vagy ipari kiszolgálóként is telepíthető. Miután konfigurált egy eszközt az IoT Edge-futtatókörnyezettel, üzembe helyezhet rajta üzleti logikát a felhőből. További információ: [a Azure IoT Edge futtatókörnyezet és az architektúrájának megismerése](iot-edge-runtime.md).

>[!NOTE]
>A Windows rendszerhez készült Azure IoT Edge a Azure IoT Edge 1.2.0 kezdődően nem támogatott.
>
>Érdemes lehet az új módszert használni a IoT Edge futtatásához Windows-eszközökön, Azure IoT Edge Linux rendszeren.

<!-- TODO: link to EFLOW-->

Egy IoT Edge eszköz beállításának két lépése van. Első lépésként telepítse a futtatókörnyezetet és annak függőségeit. A második lépés az eszköz identitáshoz csatlakoztatása a felhőben, valamint a IoT Hub hitelesítés beállítása.

Ez a cikk a Azure IoT Edge futtatókörnyezet Windows-eszközökön való telepítésének lépéseit sorolja fel. A futtatókörnyezet telepítésekor a Linux-tárolók vagy a Windows-tárolók használata is választható. Jelenleg csak Windows-tárolók támogatottak éles környezetekben. A Windowson futó Linux-tárolók fejlesztési és tesztelési forgatókönyvekhez hasznosak, különösen akkor, ha Windows rendszerű számítógépen fejlesztenek Linux rendszerű eszközökön való üzembe helyezéshez.

## <a name="prerequisites"></a>Előfeltételek

* Egy Windows-eszköz

  A Windows-tárolókkal IoT Edge a Windows rendszerhez 1809/Build 17762, amely a [Windows hosszú távú támogatásának](/windows/release-information/)legújabb verziója. Fejlesztési és tesztelési forgatókönyvek esetén a tárolók funkciót támogató bármely SKU (Pro, Enterprise, Server stb.) működni fog. Azonban mindenképpen tekintse át a [támogatott rendszerek listáját](support.md#operating-systems) az éles környezetbe állítás előtt.

  A Linux-tárolókkal rendelkező IoT Edge a Windows bármely olyan verzióját futtathatja, amely megfelel a [Docker Desktop követelményeinek](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

* Tárolók támogatása az eszközön

  A Azure IoT Edge egy [OCI-kompatibilis](https://www.opencontainers.org/) tároló motorra támaszkodik. Győződjön meg arról, hogy az eszköz támogatja a tárolókat.

  Ha virtuális gépen telepíti a IoT Edget, engedélyezze a beágyazott virtualizációt, és foglaljon le legalább 2 GB memóriát. A Hyper-V esetében a 2. generációs virtuális gépekhez alapértelmezés szerint engedélyezve van a beágyazott virtualizálás. A VMware esetében van egy váltógomb, amely engedélyezi a szolgáltatást a virtuális gépen.

  Ha a IoT Edge IoT Core-eszközre telepíti, a [távoli PowerShell-munkamenetben](/windows/iot-core/connect-your-device/powershell) a következő paranccsal ellenőrizze, hogy a Windows-tárolók támogatottak-e az eszközön:

  ```powershell
  Get-Service vmcompute
  ```

* [Regisztrált eszköz azonosítója](how-to-register-device.md)

  Ha szimmetrikus kulcsos hitelesítéssel regisztrálta az eszközt, az eszköz csatlakoztatási karakterlánca készen áll.

  Ha X. 509 önaláírt tanúsítvány-hitelesítéssel regisztrálta az eszközt, legalább egy olyan identitás-tanúsítvánnyal rendelkezik, amelyet az eszköz regisztrálásához és a hozzá tartozó titkos titkos kulcshoz használt.

## <a name="install-a-container-engine"></a>Tároló-motor telepítése

Azure IoT Edge egy OCI-kompatibilis tároló-futtatókörnyezetre támaszkodik. Éles környezetekben javasolt a Moby-alapú motor használata. A Moby motor az egyetlen tároló motor, amelyet hivatalosan támogat a Azure IoT Edge. A Docker CE/EE tároló lemezképei kompatibilisek a Moby Runtime szolgáltatással.

Éles forgatókönyvek esetén használja a telepítési parancsfájlban található Moby-alapú motort. A motor telepítéséhez nincs szükség további lépésekre.

A Linux-tárolókkal rendelkező IoT Edge a saját tároló-futtatókörnyezetet kell megadnia. Telepítse a [Docker Desktopot](https://docs.docker.com/docker-for-windows/install/) az eszközére, és konfigurálja úgy, hogy a folytatás előtt [Linux-tárolókat használjon](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) .

## <a name="install-the-iot-edge-security-daemon"></a>A IoT Edge biztonsági démon telepítése

>[!TIP]
>A IoT Core-eszközök esetében javasoljuk, hogy a telepítési parancsokat távoli PowerShell-munkamenet használatával futtassa. További információ: a [PowerShell használata a Windows IoT](/windows/iot-core/connect-your-device/powershell).

1. Futtassa a PowerShellt rendszergazdaként.

   Használjon a PowerShell AMD64-munkamenetét, ne a PowerShellt (x86). Ha nem tudja biztosan, hogy melyik munkamenet-típust használja, futtassa a következő parancsot:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Futtassa az [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) parancsot, amely a következő feladatokat hajtja végre:

   * Ellenőrzi, hogy a Windows rendszerű számítógép támogatott verziójú-e.
   * A tárolók funkció bekapcsolása.
   * Letölti a Moby Engine-t és a IoT Edge futtatókörnyezetet.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   A `Deploy-IoTEdge` parancs alapértelmezés szerint Windows-tárolókat használ. Ha Linux-tárolókat szeretne használni, adja hozzá a következő `ContainerOs` paramétert:

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. Ezen a ponton a IoT Core-eszközök automatikusan újraindulnak. Előfordulhat, hogy a Windows 10 vagy Windows Server rendszerű eszközök újraindítást kérik. Ha igen, indítsa újra az eszközt.

Ha IoT Edge telepít egy eszközre, további paramétereket is használhat a folyamat módosításához, beleértve a következőket:

* Egy proxykiszolgálón keresztüli közvetlen forgalom
* A telepítőt egy helyi könyvtárba irányítsa offline telepítéshez.

További információ ezekről a további paraméterekről: [PowerShell-parancsfájlok IoT Edge Windows rendszeren](reference-windows-scripts.md).

## <a name="provision-the-device-with-its-cloud-identity"></a>Az eszköz kiépítése a Felhőbeli identitással

Most, hogy telepítette a tároló motort és a IoT Edge futtatókörnyezetet az eszközre, készen áll a következő lépésre, amely az eszköz Felhőbeli identitás-és hitelesítési adataival való beállítására szolgál.

Válassza ki a következő szakaszt a használni kívánt hitelesítési típus alapján:

* [1. lehetőség: hitelesítés szimmetrikus kulcsokkal](#option-1-authenticate-with-symmetric-keys)
* [2. lehetőség: hitelesítés X. 509 tanúsítvánnyal](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>1. lehetőség: hitelesítés szimmetrikus kulcsokkal

Ezen a ponton a IoT Edge Runtime telepítve van a Windows-eszközön, és az eszközt a Felhőbeli identitás-és hitelesítési adataival kell kiépíteni.

Ez a szakasz végigvezeti az eszköz szimmetrikus kulcsos hitelesítéssel történő kiépítésének lépésein. Regisztrálnia kell az eszközét IoT Hubban, és le kell kérnie a kapcsolatok karakterláncát az eszköz adataiból. Ha nem, kövesse az [IoT Edge eszköz regisztrálása IoT Hubban](how-to-register-device.md)című témakör lépéseit.

1. A IoT Edge eszközön futtassa a PowerShellt rendszergazdaként.

2. Az [inicializálás-IoTEdge](reference-windows-scripts.md#initialize-iotedge) parancs használatával konfigurálja a IoT Edge futtatókörnyezetet a gépen. A parancs alapértelmezett értéke a Windows-tárolók manuális kiépítés.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Ha Linux-tárolókat használ, adja hozzá a `-ContainerOs` paramétert a jelzőhöz. Konzisztensnek kell lennie a `Deploy-IoTEdge` korábban futtatott paranccsal kiválasztott tároló beállítással.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * Ha a IoTEdgeSecurityDaemon.ps1 szkriptet offline vagy adott verzióra történő telepítésre töltötte le az eszközön, akkor ügyeljen arra, hogy a parancsfájl helyi példányára hivatkozzon.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Ha a rendszer kéri, adja meg az előző szakaszban lekért eszköz-kapcsolódási karakterláncot. Az eszköz-összekapcsolási karakterlánc a fizikai eszközt a IoT Hub eszköz azonosítójával társítja, és hitelesítési adatokat biztosít.

   Az eszköz-kapcsolatok karakterlánca a következő formátumot veszi figyelembe, és nem tartalmazhat idézőjeleket: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

Amikor manuálisan épít ki egy eszközt, további paramétereket is használhat a folyamat módosításához, beleértve a következőket:

* Egy proxykiszolgálón keresztüli közvetlen forgalom
* Egy adott edgeAgent-tároló rendszerképének deklarálása, és a hitelesítő adatok megadása, ha azok egy privát beállításjegyzékben találhatók

További információ ezekről a további paraméterekről: [PowerShell-parancsfájlok IoT Edge Windows rendszeren](reference-windows-scripts.md).

### <a name="option-2-authenticate-with-x509-certificates"></a>2. lehetőség: hitelesítés X. 509 tanúsítvánnyal

Ezen a ponton a IoT Edge Runtime telepítve van a Windows-eszközön, és az eszközt a Felhőbeli identitás-és hitelesítési adataival kell kiépíteni.

Ez a szakasz végigvezeti az eszköz X. 509 tanúsítvánnyal történő hitelesítésének lépésein. Regisztrálnia kell az eszközét IoT Hubban, és meg kell adnia a IoT Edge eszközön található tanúsítvány-és titkos kulccsal egyező ujjlenyomatai megfelelnek. Ha nem, kövesse az [IoT Edge eszköz regisztrálása IoT Hubban](how-to-register-device.md)című témakör lépéseit.

1. A IoT Edge eszközön futtassa a PowerShellt rendszergazdaként.

2. Az [inicializálás-IoTEdge](reference-windows-scripts.md#initialize-iotedge) parancs használatával konfigurálja a IoT Edge futtatókörnyezetet a gépen.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Ha Linux-tárolókat használ, adja hozzá a `-ContainerOs` paramétert a jelzőhöz. Konzisztensnek kell lennie a `Deploy-IoTEdge` korábban futtatott paranccsal kiválasztott tároló beállítással.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * Ha a IoTEdgeSecurityDaemon.ps1 szkriptet offline vagy adott verzióra történő telepítésre töltötte le az eszközön, akkor ügyeljen arra, hogy a parancsfájl helyi példányára hivatkozzon.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Ha a rendszer kéri, adja meg az alábbi információkat:

   * **IotHubHostName**: az az IoT hub állomásneve, amelyhez az eszköz csatlakozni fog. Például: `{IoT hub name}.azure-devices.net`.
   * **DeviceID**: az eszköz regisztrálása során megadott azonosító.
   * **X509IdentityCertificate**: az eszközön lévő identitási tanúsítvány abszolút elérési útja. Például: `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: a megadott identitási tanúsítványhoz tartozó titkos kulcs fájljának abszolút elérési útja. Például: `C:\path\identity_key.pem`.

Amikor manuálisan épít ki egy eszközt, további paramétereket is használhat a folyamat módosításához, beleértve a következőket:

* Egy proxykiszolgálón keresztüli közvetlen forgalom
* Egy adott edgeAgent-tároló rendszerképének deklarálása, és a hitelesítő adatok megadása, ha azok egy privát beállításjegyzékben találhatók

További információ ezekről a további paraméterekről: [PowerShell-parancsfájlok IoT Edge Windows rendszeren](reference-windows-scripts.md).

## <a name="offline-or-specific-version-installation-optional"></a>Offline vagy adott verzió telepítése (nem kötelező)

Az ebben a szakaszban ismertetett lépések a normál telepítési lépések által nem tárgyalt forgatókönyvekre vonatkoznak. Ilyen lehet például a következők:

* IoT Edge telepítése kapcsolat nélküli üzemmódban
* Release Candidate verziójának telepítése
* Telepítsen a legújabbtól eltérő verziót

A telepítés során a rendszer letölti a három fájlt:

* Egy PowerShell-parancsfájl, amely tartalmazza a telepítési utasításokat
* Microsoft Azure IoT Edge CAB, amely tartalmazza a IoT Edge biztonsági démont (iotedged), a Moby Container Engine-t és a Moby CLI-t
* Visual C++ Újraterjeszthető csomag (VC Runtime) telepítője

Ha az eszköz a telepítés során offline állapotba kerül, vagy ha a IoT Edge egy adott verzióját szeretné telepíteni, akkor a fájlokat előre letöltheti az eszközre. A telepítés ideje alatt a letöltött fájlokat tartalmazó könyvtárban mutasson a telepítési parancsfájlra. A telepítő először ellenőrzi a könyvtárat, és csak azokat az összetevőket tölti le, amelyek nem találhatók. Ha az összes fájl offline állapotban érhető el, az internetkapcsolat nélkül is telepíthető.

1. A legújabb IoT Edge telepítési fájlokról a korábbi verziók mellett lásd: [Azure IoT Edge kiadások](https://github.com/Azure/azure-iotedge/releases).

2. Keresse meg a telepíteni kívánt verziót, majd töltse le a következő fájlokat a kibocsátási megjegyzések a IoT-eszközön lévő **eszközök** részéből:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab a 1.0.9 vagy újabb kiadásokból, vagy Microsoft-Azure-IoTEdge.cab a 1.0.8 és régebbi kiadásokból.

   A Microsoft-Azure-IotEdge-arm32.cab a 1.0.9-től kezdődően is elérhető tesztelési célokra. A IoT Edge jelenleg nem támogatott Windows ARM32-eszközökön.

   Fontos, hogy a PowerShell-parancsfájlt ugyanazzal a kiadással használja, mint a. cab-fájllal, amelyet a funkció az egyes kiadásokban lévő funkciók támogatásához módosít.

3. Ha a letöltött. cab-fájlhoz architektúra utótag tartozik, nevezze át a fájlt csak **Microsoft-Azure-IoTEdge.cab**.

4. Igény szerint a Visual C++ újraterjeszthető csomagjának telepítőjét is letöltheti. A PowerShell-parancsfájl például a következő verziót használja: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Mentse a telepítőt ugyanabban a mappában a IoT-eszközön, mint a IoT Edge fájlokat.

5. Ha offline összetevőkkel szeretné telepíteni a programot, a [dot forrás](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) a PowerShell-parancsfájl helyi példányát adja meg.

6. Futtassa az [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) parancsot a (z `-OfflineInstallationPath` ) paraméterrel. Adja meg a fájl könyvtárának abszolút elérési útját. Példa:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   A központi telepítési parancs a megadott helyi fájl könyvtárában található összetevőket fogja használni. Ha a. cab-fájl vagy a Visual C++-telepítő hiányzik, a rendszer megkísérli a letöltést.

## <a name="update-iot-edge"></a>IoT Edge frissítése

A `Update-IoTEdge` parancs segítségével frissítse a biztonsági démont. A parancsfájl automatikusan lekéri a biztonsági démon legújabb verzióját.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

A Update-IoTEdge parancs futtatása eltávolítja és frissíti a biztonsági démont az eszközről, valamint a két futásidejű tároló lemezképét. A config. YAML fájlt a rendszer az eszközön tárolja, valamint a Moby Container Engine-ből származó adatokkal (ha Windows-tárolókat használ). A konfigurációs adatok megőrzése azt jelenti, hogy a frissítési folyamat során nem kell megadnia a kapcsolódási karakterlánc vagy az eszköz kiépítési szolgáltatásának adatait az eszközhöz.

Ha a biztonsági démon egy adott verziójára szeretne frissíteni, keresse meg a célként használni kívánt verziót [IoT Edge kiadásokból](https://github.com/Azure/azure-iotedge/releases). Ebben a verzióban töltse le a **Microsoft-Azure-IoTEdge.cab** fájlt. Ezután a paraméter használatával `-OfflineInstallationPath` mutasson a helyi fájl helyére. Például:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>A `-OfflineInstallationPath` paraméter egy **Microsoft-Azure-IoTEdge.cab** nevű fájlt keres a megadott könyvtárban. A IoT Edge 1.0.9-rc4-es verziótól kezdődően két. cab fájl használható, egyet az AMD64-eszközökhöz, egyet pedig a ARM32. Töltse le az eszközének megfelelő fájlt, majd nevezze át a fájlt az architektúra utótagjának eltávolításához.

Ha offline módban szeretné frissíteni az eszközt, keresse meg a célként használni kívánt verziót [Azure IoT Edge kiadásokból](https://github.com/Azure/azure-iotedge/releases). Ebben a verzióban töltse le a *IoTEdgeSecurityDaemon.ps1* és *Microsoft-Azure-IoTEdge.cab* fájlokat. Fontos, hogy a PowerShell-parancsfájlt ugyanazzal a kiadással használja, mint a. cab-fájllal, amelyet a funkció az egyes kiadásokban lévő funkciók támogatásához módosít.

Ha a letöltött. cab-fájlhoz architektúra utótag tartozik, nevezze át a fájlt csak **Microsoft-Azure-IoTEdge.cab**.

Ha offline összetevőkkel szeretné frissíteni a frissítést, a [dot forrás](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) a PowerShell-parancsfájl helyi példányát adja meg. Ezután használja a `-OfflineInstallationPath` paramétert a parancs részeként, `Update-IoTEdge` és adja meg a fájl könyvtárának abszolút elérési útját. Példa:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

A frissítési lehetőségekkel kapcsolatos további információkért használja az parancsot, `Get-Help Update-IoTEdge -full` vagy tekintse [meg IoT Edge Windows rendszeren a PowerShell-parancsfájlt](reference-windows-scripts.md).

## <a name="uninstall-iot-edge"></a>IoT Edge eltávolítása

Ha el szeretné távolítani a IoT Edge telepítését az eszközről, használja a következő parancsokat.

Ha el szeretné távolítani a IoT Edge telepítését a Windows-eszközről, használja az [uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) parancsot egy felügyeleti PowerShell-ablakból. Ez a parancs eltávolítja a IoT Edge futtatókörnyezetet, valamint a meglévő konfigurációt és a Moby Engine-adatait.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

A `Uninstall-IoTEdge` parancs nem működik a Windows IoT Core-on. IoT Edge eltávolításához újra kell telepítenie a Windows IoT Core-lemezképet.

Az eltávolítási lehetőségekkel kapcsolatos további információkért használja az parancsot `Get-Help Uninstall-IoTEdge -full` .

## <a name="next-steps"></a>Következő lépések

Folytassa a [IoT Edge-modulok üzembe](how-to-deploy-modules-portal.md) helyezésével, hogy megtudja, hogyan helyezhet üzembe modulokat az eszközön.
