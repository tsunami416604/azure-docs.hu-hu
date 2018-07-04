---
title: Az Azure IoT Edge szimulálása Windowson | Microsoft Docs
description: Az Azure IoT Edge-futtatókörnyezet telepítése szimulált eszközre Windowson, valamint az első modul telepítése
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 06/08/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5863a8edbb20b2b0c231834259f1bb7b0423a8f6
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37033800"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Rövid útmutató: Az első IoT Edge-modul üzembe helyezése az Azure Portal segítségével egy Windows-eszközön – előzetes verzió

Az Azure IoT Edge segítségével elemzéseket és adatfeldolgozást végezhet el az eszközén anélkül, hogy az összes adatot a felhőbe kellene továbbítania. Az IoT Edge-oktatóanyagok ismertetik, hogyan helyezhetők üzembe a különböző típusú modulok, de először rendelkeznie kell egy eszközzel a teszteléshez. 

Ennek a rövid útmutatónak a segítségével megtanulhatja az alábbiakat:

1. IoT Hub létrehozása
2. IoT Edge-eszköz regisztrálása
3. Az IoT Edge-futtatókörnyezet elindítása
4. Modul üzembe helyezése

![Bemutató architektúra][2]

A jelen rövid útmutatóban üzembe helyezett modul egy szimulált érzékelő, amely hőmérséklet-, páratartalom- és nyomásadatokat állít elő. A további Azure IoT Edge-oktatóanyagok az itt elvégzett munkára építkeznek olyan modulok üzembe helyezésével, amelyek a szimulált adatok elemzésével üzleti megállapításokat hoznak létre. 

>[!NOTE]
>Windows rendszeren az IoT Edge-futtatókörnyezet [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) érhető el.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot][lnk-account].

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató azt feltételezi, hogy Windows rendszerű számítógépet vagy virtuális gépet használ az IoT-eszközök szimulálására. Ha a Windows virtuális gépen fut, engedélyezze a [beágyazott virtualizálást][lnk-nested], és foglaljon le legalább 2 GB memóriát. 

Az IoT Edge-eszközként használni kívánt számítógépen a következő előfeltételeknek kell rendelkezésre állniuk:

1. Győződjön meg arról, hogy a Windows támogatott verzióját használja:
   * Windows 10 vagy újabb
   * Windows Server 2016 vagy újabb
2. Telepítse a [Windowshoz készült Dockert][lnk-docker], és ellenőrizze, hogy fut-e.
3. A Docker konfigurálása [Linux-tárolók](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) használatára

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

A rövid útmutató első lépéseként hozza létre az IoT Hubot az Azure Portalon.
![IoT Hub létrehozása][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

Regisztráljon egy IoT Edge-eszközt az újonnan létrehozott IoT Hubon.
![Eszköz regisztrálása][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése és elindítása

Telepítse és indítsa el IoT Edge-eszközén az Azure IoT Edge-futtatókörnyezetet. 
![Eszköz regisztrálása][5]

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Három összetevőből áll. Az **IoT Edge biztonsági démon** az Edge-eszközök indulásakor lép működésbe, és az IoT Edge-ügynök elindításával elvégzi az eszköz rendszerindítását. Az **IoT Edge-ügynök** a modulok üzembe helyezését és monitorozását segíti az IoT Edge-eszközön, beleértve az IoT Edge-központot is. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli. 

>[!NOTE]
>Ennek a szakasznak a lépéseit egyelőre manuálisan kell elvégezni, a telepítési szkript fejlesztése folyamatban van. 

Ebben a szakaszban az IoT Edge-futtatókörnyezet Linux-tárolókkal történő konfigurálásához talál útmutatást. Ha Windows-tárolókat kíván használni, tekintse meg az [Azure IoT Edge-futtatókörnyezet Windows rendszeren, Windows-tárolókhoz történő telepítését](how-to-install-iot-edge-windows-with-windows.md) ismertető cikket.

### <a name="download-and-install-the-iot-edge-service"></a>Az IoT Edge-szolgáltatás letöltése és telepítése

1. IoT Edge-eszközén futtassa a PowerShellt rendszergazdaként.

2. Töltse le az IoT Edge-szervizcsomagot.

   ```powershell
   Invoke-WebRequest https://conteng.blob.core.windows.net/iotedged/iotedge.zip -o .\iotedge.zip
   Expand-Archive .\iotedge.zip C:\ProgramData\iotedge -f
   $env:Path += ";C:\ProgramData\iotedge"
   SETX /M PATH "$env:Path"
   ```

3. Hozza létre és indítsa el az IoT Edge-szolgáltatást.

   ```powershell
   New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
   Start-Service iotedge
   ```

4. Adjon hozzá tűzfalkivételeket azokhoz a portokhoz, amelyeket az IoT Edge-szolgáltatás használ.

   ```powershell
   New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
   ```

5. Hozzon létre egy **iotedge.reg** nevű új fájlt, és nyissa meg egy szövegszerkesztőben. 

6. Adja hozzá a következő tartalmat, és mentse a fájlt. 

   ```input
   Windows Registry Editor Version 5.00
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
   "CustomSource"=dword:00000001
   "EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
   "TypesSupported"=dword:00000007
   ```

7. Keresse meg a fájlt a Fájlkezelőben, és kattintson rá duplán a módosítások a Windows-beállításjegyzékbe történő importálásához. 

### <a name="configure-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet konfigurálása 

Konfigurálja a futtatókörnyezetet az IoT Edge-eszköz kapcsolati sztringjével, amelyet az új eszköz regisztrálásakor másolt ki. Ezután konfigurálja a futtatókörnyezet hálózatát. 

1. Nyissa meg az IoT Edge konfigurációs fájlját, amely itt található: `C:\ProgramData\iotedge\config.yaml`. A fájl védett, ezért a megnyitásához futtasson rendszergazdaként egy szövegszerkesztőt, például a Jegyzettömböt. 

2. Keresse meg a fájl **provisioning** szakaszát, és frissítse a **device_connection_string** értékét arra a sztringre, amelyet az IoT Edge-eszköz részletes adatai közül kimásolt. 

3. A rendszergazdai PowerShell-ablakban kérje le az IoT Edge-eszköz gazdanevét, és másolja ki a kapott eredményt. 

   ```powershell
   hostname
   ```

4. A konfigurációs fájlban keresse meg az **Edge-eszköz gazdanevét** tartalmazó szakaszt. Frissítse a **hostname** (gazdanév) értéket a PowerShellből kimásolt gazdanévre.

5. A rendszergazdai PowerShell-ablakban kérje le az IoT Edge-eszköz IP-címét. 

   ```powershell
   ipconfig
   ```

6. Másolja az **IPv4-cím** értékét a kimenet **vEthernet (DockerNAT)** szakaszába. 

7. Hozzon létre egy **IOTEDGE_HOST** nevű környezeti változót, és cserélje le az *\<ip_address\>* paraméter értékét az IoT Edge-eszköz IP-címére. 

   ```powershell
   [Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<ip_address>:15580")
   ```

8. A `config.yaml` fájlban keresse meg a **Connect settings** (Kapcsolati beállítások) szakaszt. Frissítse a **management_uri** és a **workload_uri** paraméter értékét az IP-címre (a **\<GATEWAY_ADDRESS\>**-t lecserélve) és az előző szakaszban megnyitott portokra. 

   ```yaml
   connect: 
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

9. Keresse meg a **Listen settings** (Figyelési beállítások) szakaszt, és adja meg ugyanezeket az értékeket a **management_uri** és a **workload_uri** paraméter esetén. 

   ```yaml
   listen:
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

10. Keresse meg a **Moby Container Runtime settings** (Moby-tároló futtatókörnyezeti beállításai) szakaszt. Távolítsa el a megjegyzés jelölését a **network** (hálózat) sor mellől, és győződjön meg arról, hogy annak értéke `nat`.

   ```yaml
   moby_runtime:
     uri: "npipe://./pipe/docker_engine"
     network: "nat"
   ```

11. Mentse a konfigurációs fájlt. 

12. A PowerShellben indítsa újra az IoT Edge-szolgáltatást.

   ```powershell
   Stop-Service iotedge
   Start-Service iotedge
   ```

### <a name="view-the-iot-edge-runtime-status"></a>Az IoT Edge-futtatókörnyezet állapotának megtekintése

Ellenőrizze, hogy a futtatókörnyezet megfelelően lett-e telepítve és konfigurálva.

1. Ellenőrizze az IoT Edge-szolgáltatás állapotát.

   ```powershell
   Get-Service iotedge
   ```

2. Ha hibaelhárításra van szükség, kérje le a szolgáltatás naplóit. 

   ```powershell
   # Displays logs from today, newest at the bottom.

   Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Today} |
  select TimeCreated, Message | Sort-Object -Descending
   ```

3. Tekintse meg az IoT Edge-eszközön futó összes modult. Mivel első alkalommal indította el ezt a szolgáltatást, csak az **edgeAgent** modulnak szabad futnia. Az edgeAgent alapértelmezés szerint fut, és segíti az eszközön esetlegesen üzembe helyezett további modulok telepítését és indítását. 

   ```powershell
   iotedge list
   ```

   ![Egy modul megtekintése az eszközön](./media/quickstart/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Modul üzembe helyezése

Azure IoT Edge-eszközeit kezelheti a felhőből, és üzembe helyezhet egy olyan modult, amely telemetriaadatokat küld az IoT Hubra.
![Eszköz regisztrálása][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és telepítette rajta az IoT Edge-futtatókörnyezetet. Ezután az Azure Portal segítségével úgy futtatta az IoT Edge-modult az eszközön, hogy magát az eszközt nem kellett módosítania. Ebben az esetben az Ön által továbbított modul az oktatóanyagokhoz használható környezeti adatokat hoz létre. 

Győződjön meg arról, hogy a felhőből üzembe helyezett modul fut az IoT Edge-eszközön. 

```powershell
iotedge list
```

   ![Három modul megtekintése az eszközön](./media/quickstart/iotedge-list-2.png)

Tekintse meg a tempSensor modul által a felhőbe küldött üzeneteket. 

```powershell
iotedge logs tempSensor -f
```

  ![A modulból származó adatok megtekintése](./media/quickstart/iotedge-logs.png)

Az IoT Hub által fogadott üzeneteket az [IoT Hub Explorer eszközzel][lnk-iothub-explorer] vagy a [Visual Studio Code Azure IoT Toolkit bővítményével](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) is megtekintheti. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az IoT Edge-oktatóanyagok teszteléséhez használhatja az ebben a rövid útmutatóban konfigurált, szimulált eszközt is. Ha nem szeretné, hogy a tempSensor modul adatokat küldjön az IoT Hubnak, a következő paranccsal állíthatja le az IoT Edge-szolgáltatást, és törölheti az eszközön létrehozott tárolókat. Ha esetleg ismét IoT Edge-eszközként kívánja használni számítógépét, ne felejtse el elindítani a szolgáltatást. 

   ```powershell
   Stop-Service iotedge -NoWait
   docker rm -f $(docker ps -aq)
   ```

Ha már nincs szüksége a létrehozott IoT Hubra, az Azure Portalon eltávolíthatja az erőforrást és a vele társított eszközöket. Nyissa meg az IoT Hub áttekintési lapját, és kattintson a **Törlés** elemre. 

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és az Azure IoT Edge felhőalapú felülettel kódot telepített az eszközre. Most már van egy teszteszköze, amely nyers adatokat állít elő a környezetéről. 

Továbbléphet bármely másik oktatóanyagra, és megtudhatja, hogyan alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Érzékelők adatainak szűrése Azure Functions-függvényekkel](tutorial-deploy-function.md)

<!-- Images -->
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md
[lnk-account]: https://azure.microsoft.com/free
