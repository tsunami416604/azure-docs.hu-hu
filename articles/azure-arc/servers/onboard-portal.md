---
title: Hibrid gépek összekötése az Azure-ba a Azure Portal
description: Ebből a cikkből megtudhatja, hogyan telepítheti az ügynököt, és hogyan csatlakozhat a gépekhez az Azure-ba az Azure arc for Servers (előzetes verzió) használatával a Azure Portal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: bc9bc034abce789046803bbcad5b750984c905cb
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809527"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Hibrid gépek összekötése az Azure-ba a Azure Portal

Az Azure arc for Servers (előzetes verzió) lehetővé teszi, hogy a környezetében egy vagy több Windows-vagy Linux-gép számára engedélyezze a lépéseket manuálisan. Vagy használhat automatizált metódust egy általunk biztosított sablon-parancsfájl futtatásával. Ez a szkript automatizálja mindkét ügynök letöltését és telepítését.

Ehhez a módszerhez rendszergazdai jogosultságokkal kell rendelkeznie a gépen az ügynök telepítéséhez és konfigurálásához. Linux rendszeren a legfelső szintű fiók, a Windows rendszeren pedig a helyi Rendszergazdák csoport tagja.

Mielőtt elkezdené, tekintse át az [előfeltételeket](agent-overview.md#prerequisites) , és győződjön meg arról, hogy az előfizetés és az erőforrások megfelelnek a követelményeknek.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>A telepítési parancsfájl létrehozása a Azure Portal

A letöltés és telepítés automatizálására szolgáló parancsfájl, valamint az Azure arc-kapcsolat létesítése a Azure Portal érhető el. A folyamat befejezéséhez tegye a következőket:

1. A böngészőben nyissa meg a [Azure Portal](https://aka.ms/hybridmachineportal).

1. A **gépek – Azure arc** lapon válassza a **Hozzáadás**, a bal felső sarokban vagy a **számítógép létrehozása – Azure arc** lehetőséget a középső ablaktábla alján.

1. A **módszer kiválasztása** lapon jelölje be a **számítógépek hozzáadása interaktív parancsfájl** -csempével lehetőséget, majd válassza a **parancsfájl létrehozása**lehetőséget.

1. A **parancsfájl létrehozása** lapon válassza ki azt az előfizetést és erőforráscsoportot, ahol a gépet az Azure-ban szeretné felügyelni. Válasszon ki egy Azure-helyet, ahol a rendszer a számítógép metaadatait tárolja.

    >[!NOTE]
    >Az Azure arc for Servers (előzetes verzió) csak a következő régiókat támogatja:
    >- EastUS
    >- WestUS2
    >- WestEurope
    >- SoutheastAsia
    >
    >Tekintse át a további szempontokat, amikor [itt](overview.md#supported-regions) választ egy régiót az áttekintő cikkben.

1. A **parancsfájl létrehozása** lap **operációs rendszer** legördülő listájában válassza ki azt az operációs rendszert, amelyen a parancsfájl futni fog.

1. Ha a gép proxykiszolgálón keresztül kommunikál az internethez, válassza a **Tovább: proxykiszolgáló**lehetőséget.
1. A **proxykiszolgáló** lapon adja meg a proxykiszolgáló IP-címét, vagy azt a nevet és portszámot, amelyet a gép a proxykiszolgálóhoz való kommunikációhoz használni fog. Adja meg az értéket a formátumban `http://<proxyURL>:<proxyport>` .
1. Válassza a **felülvizsgálat + előállítás**lehetőséget.

1. A **felülvizsgálat + előállítás** lapon tekintse át az összegző információkat, majd kattintson a **Letöltés**gombra. Ha továbbra is módosításokat kell végeznie, válassza az **előző**lehetőséget.

## <a name="install-and-validate-the-agent-on-windows"></a>Az ügynök telepítése és ellenőrzése Windows rendszeren

### <a name="install-manually"></a>Manuális telepítés

A csatlakoztatott gépi ügynököt manuálisan is telepítheti a Windows Installer csomag *AzureConnectedMachineAgent.msi*futtatásával. A [Windows agent Windows Installer csomag](https://aka.ms/AzureConnectedMachineAgent) legújabb verzióját a Microsoft letöltőközpontból töltheti le.

>[!NOTE]
>* Az ügynök telepítéséhez vagy eltávolításához *rendszergazdai* jogosultságokkal kell rendelkeznie.
>* Először le kell töltenie és át kell másolnia a telepítőcsomagot egy mappába a célkiszolgálón vagy egy megosztott hálózati mappából. Ha bármilyen lehetőség nélkül futtatja a telepítőcsomagot, elindul egy telepítővarázsló, amelyet követve interaktív módon telepítheti az ügynököt.

Ha a gépnek egy proxykiszolgálón keresztül kell kommunikálnia a szolgáltatással, az ügynök telepítése után futtatnia kell egy, a cikk későbbi részében ismertetett parancsot. Ezzel beállítja a proxykiszolgáló rendszerkörnyezeti változóját `https_proxy` .

Ha nem ismeri a Windows Installer csomagok parancssori kapcsolóit, tekintse át az [msiexec standard parancssori kapcsolóit](/windows/win32/msi/standard-installer-command-line-options) és az [msiexec parancssori kapcsolókat](/windows/win32/msi/command-line-options).

Például futtassa a telepítőprogramot a `/?` paraméterrel, és tekintse át a Súgó és a gyors hivatkozás lehetőséget.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

Ha csendes módban szeretné telepíteni az ügynököt, és létre kell hoznia egy telepítési naplófájlt a `C:\Support\Logs` létező mappában, futtassa a következő parancsot.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

Ha a telepítés befejezése után az ügynök nem indul el, ellenőrizze a naplókat a részletes hibaüzenetek megtekintéséhez. A *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*a naplózási könyvtár.

### <a name="install-with-the-scripted-method"></a>Telepítés parancsfájl-metódussal

1. Jelentkezzen be a kiszolgálóra.

1. Nyisson meg egy emelt szintű PowerShell-parancssort.

    >[!NOTE]
    >A parancsfájl csak a Windows PowerShell 64 bites verziójáról támogatja a futtatást.
    >

1. Váltson arra a mappára vagy megosztásra, amelyre a parancsfájlt másolta, majd futtassa azt a kiszolgálón a parancsfájl futtatásával `./OnboardingScript.ps1` .

Ha a telepítés befejezése után az ügynök nem indul el, ellenőrizze a naplókat a részletes hibaüzenetek megtekintéséhez. A *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*a naplózási könyvtár.

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

`"%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"`

## <a name="install-and-validate-the-agent-on-linux"></a>Az ügynök telepítése és ellenőrzése Linuxon

A Linux rendszerhez csatlakoztatott számítógép-ügynök a terjesztés előnyben részesített csomag formátumban van megadva (. RPM vagy. DEB), amely a Microsoft [Package adattárában](https://packages.microsoft.com/)található. A [rendszerhéj-parancsfájl `Install_linux_azcmagent.sh` köteg](https://aka.ms/azcmagent) a következő műveleteket hajtja végre:

- Úgy konfigurálja a gazdagépet, hogy letöltse az ügynök csomagját a packages.microsoft.com.
- Telepíti a hibrid erőforrás-szolgáltatói csomagot.

Igény szerint az ügynököt a (z) paraméterrel is konfigurálhatja a proxy adataival `--proxy "{proxy-url}:{proxy-port}"` .

A parancsfájl a támogatott és nem támogatott disztribúciók azonosítására szolgáló logikát is tartalmaz, és ellenőrzi a telepítés végrehajtásához szükséges engedélyeket.

A következő példa letölti az ügynököt, és telepíti azt:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent.
bash ~/Install_linux_azcmagent.sh
```

A következő parancsok futtatásával töltheti le és telepítheti az ügynököt, beleértve azt a paramétert is, hogy `--proxy` az ügynököt a proxykiszolgálón keresztüli kommunikációra konfigurálja.

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Az ügynök kommunikációjának konfigurálása

Az ügynök telepítése után konfigurálja úgy, hogy az alábbi parancs futtatásával kommunikáljon az Azure arc szolgáltatással:

`azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"`

>[!NOTE]
>A **azcmagent**futtatásához *rendszergazdai* jogosultságokkal kell rendelkeznie a Linux rendszerű gépeken.

## <a name="verify-the-connection-with-azure-arc"></a>Az Azure Arc csatlakozásának ellenőrzése

Miután telepítette az ügynököt, és úgy konfigurálja, hogy az Azure arc for Servers (előzetes verzió) szolgáltatáshoz kapcsolódjon, lépjen a Azure Portal, és ellenőrizze, hogy a kiszolgáló sikeresen csatlakoztatva van-e. A gépet megtekintheti az [Azure Portalon](https://aka.ms/hybridmachineportal).

![Sikeres kiszolgálói kapcsolatok](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan kezelheti a gépet [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, lehetővé teszi a figyelést a virtuális [gépekkel Azure monitor](../../azure-monitor/insights/vminsights-enable-policy.md)és sok más további műveletet.

- További információ a [log Analytics-ügynökről](../../azure-monitor/platform/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics-ügynökre akkor van szükség, ha proaktívan szeretné figyelni a gépen futó operációs rendszert és munkaterheléseket, felügyelheti azt automatizálási runbookok vagy olyan megoldások használatával, mint például a Update Management, vagy más Azure-szolgáltatásokat is használhat, mint például a [Azure Security Center](../../security-center/security-center-intro.md).