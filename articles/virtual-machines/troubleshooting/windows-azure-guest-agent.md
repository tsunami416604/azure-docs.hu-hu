---
title: A Windows Azure Guest Agent hibaelhárítása
description: A Windows Azure vendég ügynökkel kapcsolatos hibák elhárítása nem működik
services: virtual-machines-windows
ms.service: virtual-machines-windows
author: dkdiksha
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/15/2020
ms.author: genli
ms.openlocfilehash: 597ea6e7ff7dbcfcb8a99d4e4de3c1b82915ee07
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561261"
---
# <a name="troubleshooting-windows-azure-guest-agent"></a>A Windows Azure Guest Agent hibaelhárítása

A Windows Azure Guest Agent egy virtuális gép (VM) ügynöke. Lehetővé teszi a virtuális gép számára, hogy kommunikáljon a háló vezérlővel (a mögöttes fizikai kiszolgáló, amelyen a virtuális gép fut) az IP-168.63.129.16. Ez egy virtuális nyilvános IP-cím, amely megkönnyíti a kommunikációt. További információ: [Mi az IP-168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

 Az Azure-ba a helyszíni vagy a testreszabott rendszerkép használatával létrehozott virtuális gép nem rendelkezik telepített Windows Azure Guest Agent-ügynökkel. Ezekben a forgatókönyvekben manuálisan kell telepítenie a virtuálisgép-ügynököt. A virtuálisgép-ügynök telepítésével kapcsolatos további információkért lásd: az [Azure Virtual Machine Agent áttekintése](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows).

A Windows Azure Guest Agent-ügynök sikeres telepítése után a következő, a virtuális gépen található Services. msc-ben felsorolt szolgáltatások láthatók:
 
- Windows Azure Guest Agent szolgáltatás
- Telemetria szolgáltatás
- Távoli asztali ügynök szolgáltatás

**Windows Azure Guest Agent szolgáltatás**: Ez a szolgáltatás a WAppAgent. log naplófájl összes naplózási feladata. Ez a szolgáltatás felelős a különböző bővítmények és a vendégtől a gazdagép felé irányuló kommunikáció konfigurálásában. 

**Telemetria szolgáltatás**: Ez a szolgáltatás felelős a virtuális gép telemetria-adatoknak a háttér-kiszolgálóra való küldéséhez.

**Távoli asztali ügynök szolgáltatás**: Ez a szolgáltatás felelős a vendég ügynök telepítéséhez. Az átlátszó telepítő a távoli asztali ügynök egyik összetevője is, amely segít a vendég ügynök egyéb összetevőinek és szolgáltatásainak frissítésében. A RDAgent feladata továbbá szívverések küldése a vendég virtuális gépről a fizikai kiszolgálón lévő ügynökre.

> [!NOTE]
> A virtuálisgép-2.7.41491.971 verziójától kezdődően a telemetria összetevő a RDAgent szolgáltatás részét képezi, ezért előfordulhat, hogy ez a telemetria szolgáltatás nem jelenik meg az újonnan létrehozott virtuális gépeken.

## <a name="checking-agent-status-and-version"></a>Az ügynök állapotának és verziójának ellenőrzése

Nyissa meg Azure Portal a virtuális gép tulajdonságlapját, és keresse meg az **ügynök állapotát**. Ha a Windows Azure vendég ügynöke megfelelően működik, az állapot **készen áll**. Ha a virtuálisgép-ügynök **nem üzemkész** állapotban van, a Azure Portal nem fog működni a bővítmények és a **Run parancs** .

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Nem üzemkész állapotú virtuálisgép-ügynök hibaelhárítása

### <a name="step-1-check-whether-the-windows-azure-guest-agent-service-is-installed"></a>1. lépés azt vizsgálja, hogy telepítve van-e a Windows Azure Guest Agent ügynök szolgáltatás

- A csomag keresése

    Keresse meg a C:\WindowsAzure mappát. Ha a verziószámot megjelenítő Vendégügynök mappa jelenik meg, az azt jelenti, hogy a Windows Azure Guest Agent ügynök telepítve lett a virtuális gépen. A telepített csomagot is megkeresheti.  Ha a Windows Azure Guest Agent ügynök telepítve van a virtuális gépen, a csomag a következő helyre lesz mentve: `C:\windows\OEM\GuestAgent\VMAgentPackage.zip` .
    
    A következő PowerShell-parancs futtatásával ellenőrizhető, hogy a virtuálisgép-ügynök telepítve van-e a virtuális gépen:
    
    `Get-AzVM -ResourceGroupName "RGNAME" -Name "VMNAME" -DisplayHint expand`
    
    A kimenetben keresse meg a **ProvisionVMAgent** tulajdonságot, és győződjön meg arról, hogy az érték **true (igaz**). Ha igen, ez azt jelenti, hogy az ügynök telepítve van a virtuális gépen.
    
- Szolgáltatások és folyamatok keresése

   Nyissa meg a szolgáltatások konzolt (Services. msc), és győződjön meg a következő szolgáltatások állapotáról: Windows Azure vendég ügynök szolgáltatás, RDAgent szolgáltatás, Windows Azure telemetria szolgáltatás és Windows Azure hálózati ügynök szolgáltatás.
 
    Azt is megtudhatja, hogy a szolgáltatások futnak-e, ha a Feladatkezelő vizsgálatával a következő folyamatokat vizsgálja:
       
    - WindowsAzureGuestAgent.exe: Windows Azure Guest Agent szolgáltatás
    - WaAppAgent.exe: RDAgent szolgáltatás
    - WindowsAzureNetAgent.exe: Windows Azure hálózati ügynök szolgáltatás
    - WindowsAzureTelemetryService.exe: Windows Azure telemetria szolgáltatás

   Ha nem találja ezeket a folyamatokat és szolgáltatásokat, ez azt jelzi, hogy nincs telepítve a Windows Azure Guest Agent ügynök.

- A program és a szolgáltatás keresése

    A Vezérlőpulton lépjen a **programok és szolgáltatások elemre** annak megállapításához, hogy telepítve van-e a Windows Azure Guest Agent szolgáltatás.

Ha nem talál olyan csomagot, szolgáltatást és folyamatot, amelyen fut, és még nem látja a Windows Azure Guest Agent ügynököt a programok és szolgáltatások területen, próbálja meg [telepíteni a Windows Azure Guest Agent szolgáltatást](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows). Ha a vendég ügynök telepítése nem megfelelő, a virtuálisgép- [ügynök offline állapotba](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)helyezhető.

Ha a szolgáltatások megjelennek és futnak, indítsa újra a szolgáltatást, és ellenőrizze, hogy megoldódott-e a probléma. Ha a szolgáltatások leállnak, indítsa el őket, és várjon néhány percet. Ezután győződjön meg arról, hogy az **ügynök állapota** **készként**van-e bejelentve. Ha úgy találja, hogy ezek a szolgáltatások összeomlanak, előfordulhat, hogy egyes harmadik féltől származó folyamatok összeomlást okoznak. A problémák további hibaelhárításához forduljon [Microsoft ügyfélszolgálatahoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-working"></a>2. lépés: annak megállapítása, hogy működik-e az automatikus frissítés

A Windows Azure vendég ügynökének automatikus frissítési funkciója van. Automatikusan megkeresi az új frissítéseket, és telepíti őket. Ha az automatikus frissítés funkció nem működik megfelelően, próbálja meg eltávolítani és telepíteni a Windows Azure Guest Agent ügynököt a következő lépések végrehajtásával:

1. Ha a Windows Azure Guest Agent ügynök megjelenik a **programok és szolgáltatások szolgáltatásban**, távolítsa el a Windows Azure Guest Agent ügynököt.

2. Nyisson meg egy parancssori ablakot, amely rendszergazdai jogosultságokkal rendelkezik.

3. Állítsa le a vendég ügynök szolgáltatásait. Ha a szolgáltatások nem állnak le, be kell állítania a szolgáltatásokat **kézi indításra** , majd újra kell indítania a virtuális gépet.
    ```
    net stop rdagent
    net stop WindowsAzureGuestAgent
    net stop WindowsAzureTelemetryService
    ```
1. A vendég ügynök szolgáltatásainak törlése:
    ```
    sc delete rdagent
    sc delete WindowsAzureGuestAgent
    sc delete WindowsAzureTelemetryService
    ```
1. `C:\WindowsAzure`A régi nevű mappa létrehozása területen.

1. Helyezze át a Packages vagy Vendégügynök nevű mappákat a régi mappába.

1. Töltse le és telepítse az ügynök-telepítési csomag legújabb [verzióját innen.](https://go.microsoft.com/fwlink/?linkid=394789&clcid=0x409) A telepítés befejezéséhez rendszergazdai jogosultságokkal kell rendelkeznie.

1. Telepítse a Guest Agent ügynököt a következő parancs használatával:

    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log
    ```
    Ezután győződjön meg arról, hogy a vendég ügynök szolgáltatásai megfelelően indulnak-e el.
 
    Ritka esetekben, amikor a vendég ügynök nem megfelelően telepíti [a virtuális gépet, a virtuálisgép-ügynököt offline állapotba is telepítheti](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>3. lépés azt vizsgálja, hogy a virtuális gép tud-e csatlakozni a háló vezérlőhöz

A PsPing eszközzel ellenőrizheti, hogy a virtuális gép tud-e csatlakozni a 168.63.129.16-hez a 80, 32526 és 443 portokon. Ha a virtuális gép nem a várt módon kapcsolódik, ellenőrizze, hogy az 80-es, 443-as és 32526-as porton keresztüli kimenő kommunikáció nyitva van-e a virtuális gépen a helyi tűzfalon. Ha ez az IP-cím le van tiltva, a virtuálisgép-ügynök nem várt viselkedést jeleníthet meg különböző helyzetekben.

## <a name="advanced-troubleshooting"></a>Speciális hibaelhárítás

A Windows Azure Guest Agent hibaelhárításával kapcsolatos események a következő naplófájlokban vannak rögzítve:

- C:\WindowsAzure\Logs\WaAppAgent.log
- C:\WindowsAzure\Logs\TransparentInstaller.log

Az alábbiakban néhány gyakori forgatókönyvet láthat, amelyekben a Windows Azure Guest Agent **nem üzemkész** állapotba kerülhet, vagy a várt módon leáll.

### <a name="agent-stuck-on-starting"></a>Ügynök beragadt a "kezdő"

A WaAppAgent-naplóban láthatja, hogy az ügynök beragadt a kezdeti folyamat során, és nem indítható el.

**Napló adatai**
 
[00000007] [05/28/2019 12:58:50.90] [INFO] a WindowsAzureGuestAgent indítása. 2.7.41491.901 verziója

**Elemzés**
 
A virtuális gép továbbra is a Windows Azure Guest Agent korábbi verzióját futtatja. Előfordulhat, hogy a C:\WindowsAzure mappában több Windows Azure Guest Agent-példány van telepítve, beleértve a több azonos verziót is. Mivel a rendszer több ügynököt is telepít, a virtuális gép nem indítja el a Windows Azure Guest Agent legújabb verzióját.

**Megoldás**

Távolítsa el manuálisan a Windows Azure Guest Agent ügynököt, majd telepítse újra a következő lépések végrehajtásával:

1. Nyissa meg a Vezérlőpultot > programok és szolgáltatások területen, és távolítsa el a Windows Azure Guest Agent ügynököt.
1. Nyissa meg a Feladatkezelő eszközt, és állítsa le a következő szolgáltatásokat: Windows Azure vendég ügynök szolgáltatás, RDAgent szolgáltatás, Windows Azure telemetria szolgáltatás és Windows Azure hálózati ügynök szolgáltatás.
1. A C:\WindowsAzure területen hozzon létre egy régi nevű mappát.
1. Helyezze át a Packages vagy Vendégügynök nevű mappákat a régi mappába. Helyezze át a C:\WindowsAzure\logs egyik Vendégügynök-mappáját is, amelyek GuestAgent_x. x. xxxxx néven kezdődnek a régi mappába.
1. Töltse le és telepítse az MSI-ügynök legújabb verzióját. A telepítés befejezéséhez rendszergazdai jogosultságokkal kell rendelkeznie.
1. Telepítse a Guest Agent ügynököt a következő MSI-parancs használatával:
    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log`
    ```
1. Ellenőrizze, hogy a RDAgent, a Windows Azure Guest Agent és a Windows Azure telemetria Services fut-e.
 
1. Ellenőrizze a WaAppAgent. log naplófájlt, és győződjön meg arról, hogy a Windows Azure Guest Agent legújabb verziója fut.
 
1. A régi mappa törlése a C:\WindowsAzure. alatt
  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Nem lehet csatlakozni a WireServer IP-címhez (gazdagép IP-címe) 

A WaAppAgent. log és a telemetria. log naplófájlban a következő hibaüzeneteket láthatja:

**Napló adatai**

```Log sample
[ERROR] GetVersions() failed with exception: Microsoft.ServiceModel.Web.WebProtocolException: Server Error: Service Unavailable (ServiceUnavailable) ---> 
System.Net.WebException: The remote server returned an error: (503) Server Unavailable.
```
```Log sample
[00000011] [12/11/2018 06:27:55.66] [WARN]  (Ignoring) Exception while fetching supported versions from HostGAPlugin: System.Net.WebException: Unable to connect to the remote server 
---> System.Net.Sockets.SocketException: An attempt was made to access a socket in a way forbidden by its access permissions 168.63.129.16:32526
at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
at System.Net.ServicePoint.ConnectSocketInternal(Boolean connectFailure, Socket s4, Socket s6, Socket& socket, IPAddress& address, ConnectSocketState status, IAsyncResult asyncResult, Exception& exception)
--- End of inner exception stack trace ---
at System.Net.WebClient.DownloadDataInternal(Uri address, WebRequest& request)
at System.Net.WebClient.DownloadString(Uri address)
at Microsoft.GuestAgentHostPlugin.Client.GuestInformationServiceClient.GetVersions()
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.HostGAPluginUtility.UpdateHostGAPluginAvailability()`
```
**Elemzés**

A virtuális gép nem tudja elérni a wireserver gazdagép-kiszolgálót.

**Megoldás**

1. Mivel a wireserver nem érhető el, a Távoli asztal használatával csatlakozhat a virtuális géphez, majd megpróbálhatja elérni a következő URL-címet egy böngészőből: http://168.63.129.16/?comp=versions 
1. Ha nem tudja elérni az 1. lépésben szereplő URL-címet, ellenőrizze a hálózati adaptert annak megállapításához, hogy DHCP-ként van-e beállítva, és DNS-sel rendelkezik-e. A hálózati adapter DHCP-állapotának megtekintéséhez futtassa a következő parancsot:  `netsh interface ip show config` .
1. Ha a DHCP le van tiltva, futtassa a következőt, és győződjön meg róla, hogy a sárga értékre módosítja a felület nevét: `netsh interface ip set address name="Name of the interface" source=dhcp` .
1. Az IP-168.63.129.16 való hozzáférést blokkoló tűzfal, proxy vagy más forrás által okozott esetleges problémák keresése.
1. Győződjön meg arról, hogy a Windows tűzfal vagy egy külső gyártótól származó tűzfal blokkolja-e a 80, 443 és 32526 portokhoz való hozzáférést. További információ arról, hogy a cím miért nem tiltható le: [Mi az IP-168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

### <a name="guest-agent-is-stuck-stopping"></a>A vendég ügynök beragadt "Leállítás"  

A WaAppAgent. log naplófájlban a következő hibaüzeneteket láthatja:

**Napló adatai** 

```Log sample
[00000007] [07/18/2019 14:46:28.87] [WARN] WindowsAzureGuestAgent stopping.
[00000007] [07/18/2019 14:46:28.89] [INFO] Uninitializing StateExecutor with WaitForTerminalStateReachedOnEnd : True
[00000004] [07/18/2019 14:46:28.89] [WARN] WindowsAzureGuestAgent could not be stopped. Exception: System.NullReferenceException: Object reference not set to an instance of an object.
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.WaitForExtensionWorkflowComplete(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorForCloud.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentCore.Stop(Boolean waitForTerminalState)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.DoStopService()
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.<>c__DisplayClass2.<OnStopProcessing>b__1()
```
**Elemzés**

A Windows Azure Guest Agent beragadt a leállítási folyamat során.

**Megoldás**

1. Győződjön meg arról, hogy a WaAppAgent.exe fut a virtuális gépen. Ha nem fut, indítsa újra a rdgagent szolgáltatást, és várjon öt percet. Ha WaAppAgent.exe fut, fejezze be a WindowsAzureGuest.exe folyamatot.
2. Ha az 1. lépés nem oldja meg a problémát, távolítsa el a jelenleg telepített verziót, és telepítse manuálisan az ügynök legújabb verzióját.

### <a name="npcap-loopback-adapter"></a>Npcap visszacsatolási adapter 

A WaAppAgent. log naplófájlban a következő hibaüzeneteket láthatja:
 
```Log sample
[00000006] [06/20/2019 07:44:28.93] [INFO]  Attempting to discover fabric address on interface Npcap Loopback Adapter.
[00000024] [06/20/2019 07:44:28.93] [WARN]  Empty DHCP option data returned
[00000006] [06/20/2019 07:44:28.93] [ERROR] Did not discover fabric address on interface Npcap Loopback Adapter
```
**Elemzés**

A Npcap visszacsatolási adapter a virtuális gépen a Wireshark használatával van telepítve. A Wireshark egy nyílt forráskódú eszköz a hálózati forgalom profilkészítéséhez és a csomagok elemzéséhez. Ezt az eszközt gyakran hálózati elemzőnek, hálózati protokoll-elemzőnek vagy Szippantó-nek nevezzük.

**Megoldás**

A WireShark valószínűleg telepíti a Npcap visszacsatolási adaptert. Próbálja meg letiltani, majd győződjön meg arról, hogy a probléma megoldódott-e.

## <a name="next-steps"></a>További lépések

A "Windows Azure Guest Agent ügynök nem működik" probléma megoldásához forduljon a [Microsoft ügyfélszolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
