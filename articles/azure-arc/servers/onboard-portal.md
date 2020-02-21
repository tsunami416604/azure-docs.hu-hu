---
title: Hibrid gépek összekötése az Azure-ba a Azure Portal
description: Ebből a cikkből megtudhatja, hogyan telepítheti az ügynököt, és hogyan csatlakozhat a gépekhez az Azure-ba az Azure arc for Servers (előzetes verzió) használatával a Azure Portal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/12/2020
ms.topic: conceptual
ms.openlocfilehash: 3f4954b3a33cdd73c1e004ad1e643f9e24abf1e4
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485233"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Hibrid gépek összekötése az Azure-ba a Azure Portal

Az Azure arc for Servers (előzetes verzió) lehetővé teszi, hogy a környezetében egy vagy több Windows-vagy Linux-gép számára engedélyezze a lépéseket manuálisan. Vagy használhat automatizált metódust egy általunk biztosított sablon-parancsfájl futtatásával. Ez a szkript automatizálja mindkét ügynök letöltését és telepítését.

Ehhez a módszerhez rendszergazdai jogosultságokkal kell rendelkeznie a gépen az ügynök telepítéséhez és konfigurálásához. Linux rendszeren a legfelső szintű fiók, a Windows rendszeren pedig a helyi Rendszergazdák csoport tagja.

Mielőtt elkezdené, tekintse át az [előfeltételeket](overview.md#prerequisites) , és győződjön meg arról, hogy az előfizetés és az erőforrások megfelelnek a követelményeknek.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>A telepítési parancsfájl létrehozása a Azure Portal

A letöltés és telepítés automatizálására szolgáló parancsfájl, valamint az Azure arc-kapcsolat létesítése a Azure Portal érhető el. A folyamat befejezéséhez tegye a következőket:

1. A böngészőben nyissa meg a [Azure Portal](https://aka.ms/hybridmachineportal).

1. A **gépek – Azure arc** lapon válassza a **Hozzáadás**, a bal felső sarokban vagy a **számítógép létrehozása – Azure arc** lehetőséget a középső ablaktábla alján. 

1. A **módszer kiválasztása** lapon jelölje be a **számítógépek hozzáadása interaktív parancsfájl** -csempével lehetőséget, majd válassza a **parancsfájl létrehozása**lehetőséget.

1. A **parancsfájl létrehozása** lapon válassza ki azt az előfizetést és erőforráscsoportot, ahol a gépet az Azure-ban szeretné felügyelni. Válasszon ki egy Azure-helyet, ahol a rendszer a számítógép metaadatait tárolja.

    >[!NOTE]
    >Az Azure arc for Servers (előzetes verzió) csak a következő régiókat támogatja:
    >- WestUS2
    >- WestEurope
    >- WestAsia
    >
    >Tekintse át a további szempontokat, amikor [itt](overview.md#supported-regions) választ egy régiót az áttekintő cikkben.

1. A **parancsfájl létrehozása** lap **operációs rendszer** legördülő listájában válassza ki azt az operációs rendszert, amelyen a parancsfájl futni fog.

1. Ha a gép proxykiszolgálón keresztül kommunikál az internethez, válassza a **Tovább: proxykiszolgáló**lehetőséget. 
1. A **proxykiszolgáló** lapon adja meg a proxykiszolgáló IP-címét, vagy azt a nevet és portszámot, amelyet a gép a proxykiszolgálóhoz való kommunikációhoz használni fog. Adja meg az értéket a `http://<proxyURL>:<proxyport>`formátumban. 
1. Válassza a **felülvizsgálat + előállítás**lehetőséget.

1. A **felülvizsgálat + előállítás** lapon tekintse át az összegző információkat, majd kattintson a **Letöltés**gombra. Ha továbbra is módosításokat kell végeznie, válassza az **előző**lehetőséget.

## <a name="install-and-validate-the-agent-on-windows"></a>Az ügynök telepítése és ellenőrzése Windows rendszeren

### <a name="install-manually"></a>Manuális telepítés
A csatlakoztatott gépi ügynököt manuálisan is telepítheti a *AzureConnectedMachineAgent. msi*Windows Installer csomag futtatásával. 

> [!NOTE]
> * Az ügynök telepítéséhez vagy eltávolításához *rendszergazdai* jogosultságokkal kell rendelkeznie.
> * Először le kell töltenie és át kell másolnia a telepítőcsomagot egy mappába a célkiszolgálón vagy egy megosztott hálózati mappából. Ha bármilyen lehetőség nélkül futtatja a telepítőcsomagot, elindul egy telepítővarázsló, amelyet követve interaktív módon telepítheti az ügynököt.

Ha a gépnek egy proxykiszolgálón keresztül kell kommunikálnia a szolgáltatással, az ügynök telepítése után futtatnia kell egy, a cikk későbbi részében ismertetett parancsot. Ezzel beállítja a proxykiszolgáló rendszerkörnyezeti változóját `https_proxy`.

Az alábbi táblázat a paramétereket, az ügynök a parancssorból a telepítő által támogatott emeli ki.

| Paraméter | Leírás |
|:--|:--|
| /? | A parancssori kapcsolók listáját adja vissza. |
| /S | Felhasználói beavatkozás nélkül csendes telepítést hajt végre. |

A telepítőprogramnak a `/?` paraméterrel való futtatásához például írja be a `msiexec.exe /i AzureConnectedMachineAgent.msi /?`értéket.

A csatlakoztatott gépi ügynök fájljai alapértelmezés szerint a *C:\Program Files\AzureConnectedMachineAgent* -ben települnek. Ha a telepítés befejezése után az ügynök nem indul el, ellenőrizze a naplókat a részletes hibaüzenetek megtekintéséhez. A *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*a naplózási könyvtár.

### <a name="install-with-the-scripted-method"></a>Telepítés parancsfájl-metódussal

1. Jelentkezzen be a kiszolgálóra.

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort.

1. Váltson arra a mappára vagy megosztásra, amelyre a parancsfájlt másolta, majd futtassa azt a kiszolgálón a `./OnboardingScript.ps1` parancsfájl futtatásával.

### <a name="configure-the-agent-proxy-setting"></a>Az ügynök proxy beállításának konfigurálása

A proxykiszolgáló környezeti változójának beállításához futtassa a következő parancsot:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>Az ügynök nem támogatja a proxy hitelesítés beállítását ebben az előzetes verzióban.
>

### <a name="configure-agent-communication"></a>Az ügynök kommunikációjának konfigurálása

Az ügynök telepítése után az ügynököt úgy kell konfigurálnia, hogy az alábbi parancs futtatásával kommunikáljon az Azure arc szolgáltatással:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Az ügynök telepítése és ellenőrzése Linuxon

A Linux rendszerhez csatlakoztatott számítógép-ügynök a terjesztés előnyben részesített csomag formátumban van megadva (. RPM vagy. DEB), amely a Microsoft [Package adattárában](https://packages.microsoft.com/)található. A [rendszerhéj parancsfájl](https://aka.ms/azcmagent) -csomagja `Install_linux_azcmagent.sh`a következő műveleteket hajtja végre:

- Úgy konfigurálja a gazdagépet, hogy letöltse az ügynök csomagját a packages.microsoft.com.
- Telepíti a hibrid erőforrás-szolgáltatói csomagot.

Igény szerint az ügynököt a proxy adataival is konfigurálhatja a `--proxy "{proxy-url}:{proxy-port}"` paraméterrel együtt.

A parancsfájl a támogatott és nem támogatott disztribúciók azonosítására szolgáló logikát is tartalmaz, és ellenőrzi a telepítés végrehajtásához szükséges engedélyeket. 

A következő példa letölti az ügynököt, és telepíti azt:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

A következő parancsok futtatásával töltheti le és telepítheti az ügynököt, beleértve a `--proxy` paramétert az ügynök konfigurálásához a proxykiszolgálón keresztül történő kommunikációhoz:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Az ügynök kommunikációjának konfigurálása

Az ügynök telepítése után konfigurálja úgy, hogy az alábbi parancs futtatásával kommunikáljon az Azure arc szolgáltatással:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Az Azure arc-beli kapcsolatok ellenőrzése

Miután telepítette az ügynököt, és úgy konfigurálja, hogy az Azure arc for Servers (előzetes verzió) szolgáltatáshoz kapcsolódjon, lépjen a Azure Portal, és ellenőrizze, hogy a kiszolgáló sikeresen csatlakoztatva van-e. Megtekintheti a gépeket a [Azure Portalban](https://aka.ms/hybridmachineportal).

![Sikeres kiszolgálói kapcsolatok](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha le szeretne bontani egy gépet az Azure arc for Servers szolgáltatásban (előzetes verzió), tegye a következőket:

1. Nyissa meg az Azure arc for Servers (előzetes verzió) lehetőséget a [Azure Portal](https://aka.ms/hybridmachineportal).

1. Válassza ki a gépet a listából, válassza a három pontot ( **..** .), majd válassza a **Törlés**lehetőséget.

1. A Windows-ügynök számítógépről való eltávolításához tegye a következőket:

    a. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.  
    b. A **Vezérlőpulton**válassza a **programok és szolgáltatások**lehetőséget.  
    c. A **programok és szolgáltatások**területen válassza **Az Azure Connected Machine Agent ügynököt**, válassza az **Eltávolítás**lehetőséget, majd válassza az **Igen**lehetőséget.  

    >[!NOTE]
    > Az ügynök telepítővarázslójának futtatásához kattintson duplán a **AzureConnectedMachineAgent. msi** telepítőcsomag-csomagra.

    Ha parancsfájlt szeretne eltávolítani az ügynök eltávolításához, használja a következő példát, amely lekéri a termékkód lekérését, és az msiexec. exe parancssori `msiexec /x {Product Code}`használatával eltávolítja az ügynököt. Ehhez tegye a következőket:  
    
    a. Nyissa meg a Beállításszerkesztőt.  
    b. A beállításkulcs `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`területen keresse meg és másolja a termékkód GUID azonosítóját.  
    c. Ezután eltávolíthatja az ügynököt az msiexec használatával.

    Az alábbi példa bemutatja, hogyan távolíthatja el az ügynököt:

    ```powershell
    Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
    Get-ItemProperty | `
    Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
    ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
    ```

1. A Linux-ügynök eltávolításához futtassa a következő parancsot:

      ```bash
      sudo apt purge azcmagent
      ```

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan kezelheti a gépet [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, lehetővé teszi a figyelést a virtuális [gépekkel Azure monitor](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)és sok más további műveletet.

- További információ a [log Analytics-ügynökről](../../azure-monitor/platform/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics-ügynökre akkor van szükség, ha proaktívan szeretné figyelni a gépen futó operációs rendszert és munkaterheléseket, felügyelheti azt automatizálási runbookok vagy olyan megoldások használatával, mint például a Update Management, vagy más Azure-szolgáltatásokat is használhat, mint például a [Azure Security Center](../../security-center/security-center-intro.md).
