---
title: Hibrid gépek csatlakoztatása az Azure-hoz az Azure portalról
description: Ebben a cikkben megtudhatja, hogyan telepítheti az ügynököt, és hogyan csatlakoztathat gépeket az Azure Arc kiszolgálókhoz (előzetes verzió) használatával az Azure Portalon.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 40885e1de4ff4c16d2a50399c654d8596396ab53
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366371"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Hibrid gépek csatlakoztatása az Azure-hoz az Azure portalról

Engedélyezheti az Azure Arc kiszolgálókhoz (előzetes verzió) egy vagy kis számú Windows vagy Linux gépek a környezetben manuálisan végzett lépések végrehajtásával. Vagy használhat egy automatikus módszert az általunk biztosított sablonparancsfájl futtatásával. Ez a parancsfájl automatizálja mindkét ügyintéző letöltését és telepítését.

Ehhez a módszerhez rendszergazdai engedélyekszükségesek a számítógépen az ügynök telepítéséhez és konfigurálásához. Linuxon, a gyökérfiók használatával és a Windows rendszeren a Helyi rendszergazdák csoport tagja.

Mielőtt elkezdenéd, tekintse át az [előfeltételeket,](overview.md#prerequisites) és ellenőrizze, hogy az előfizetés és az erőforrások megfelelnek-e a követelményeknek.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>A telepítési parancsfájl létrehozása az Azure Portalról

A letöltés és a telepítés automatizálásához, valamint az Azure Archoz való kapcsolat létrehozásához szükséges parancsfájl elérhető az Azure Portalon. A folyamat befejezéséhez tegye a következőket:

1. A böngészőből nyissa meg az [Azure Portalt.](https://aka.ms/hybridmachineportal)

1. A **Gépek – Azure Arc** lapon válassza a **Hozzáadás**lehetőséget a bal felső sarokban, vagy a **Gép létrehozása - Azure Arc** beállítást a középső ablaktábla alján. 

1. A **Módszer kiválasztása** lapon jelölje ki a **Gépek hozzáadása interaktív parancsfájllal** csempét, majd válassza **a Parancsfájl létrehozása lehetőséget.**

1. A **Parancsfájl létrehozása** lapon válassza ki azt az előfizetési és erőforráscsoportot, amelyben a gépet az Azure-on belül szeretné kezelni. Válasszon ki egy Azure-helyet, ahol a gép metaadatait tárolja.

    >[!NOTE]
    >Az Azure Arc kiszolgálókhoz (előzetes verzió) csak a következő régiókat támogatja:
    >- Nyugat-us2
    >- WestEurope
    >- Nyugat-Ázsia
    >
    >Tekintse át a régió kiválasztásával kapcsolatos további szempontokat [az](overview.md#supported-regions) Áttekintés című cikkben.

1. A **Létrehozás parancsfájl** lapon az **Operációs rendszer** legördülő listájában válassza ki azt az operációs rendszert, amelyen a parancsfájl futni fog.

1. Ha a számítógép proxykiszolgálón keresztül kommunikál az internethez való csatlakozáshoz, válassza a **Tovább: Proxykiszolgáló**lehetőséget. 
1. A **Proxykiszolgáló** lapon adja meg a proxykiszolgáló IP-címét, illetve azt a nevet és portszámot, amelyet a számítógép a proxykiszolgálóval való kommunikációhoz használ. Adja meg az `http://<proxyURL>:<proxyport>`értéket a formátumban. 
1. Válassza **a Véleményezés + generate lehetőséget.**

1. A **Véleményezés + létrehoz** lapon tekintse át az összesítő információkat, majd kattintson a **Letöltés gombra.** Ha továbbra is módosítania kell, válassza **az Előző**lehetőséget.

## <a name="install-and-validate-the-agent-on-windows"></a>Az ügynök telepítése és érvényesítése Windows rendszeren

### <a name="install-manually"></a>Manuális telepítés

A Csatlakoztatott gép ügynökét manuálisan is telepítheti a Windows Installer csomag, az *AzureConnectedMachineAgent.msi*futtatásával. 

> [!NOTE]
> * Az ügynök telepítéséhez vagy eltávolításához rendszergazdai engedélyekkel kell *rendelkeznie.*
> * Először le kell töltenie és át kell másolnia a Telepítő csomagot a célkiszolgálón lévő mappába vagy egy megosztott hálózati mappából. Ha a Telepítő csomagot beállítások nélkül futtatja, akkor egy telepítővarázslót indít el, amelyet követhet az ügynök interaktív telepítéséhez.

Ha a számítógépnek proxykiszolgálón keresztül kell kommunikálnia a szolgáltatással, az ügynök telepítése után a cikk későbbi részében ismertetett parancsot kell futtatnia. Ezzel beállítja a `https_proxy`proxykiszolgáló rendszerkörnyezeti változóját.

Ha nem ismeri a Windows Installer csomagok parancssori beállításait, tekintse át az [Msiexec szabványos parancssori beállításait](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) és az [Msiexec parancssori kapcsolókat.](https://docs.microsoft.com/windows/win32/msi/command-line-options)

Futtassa például a `/?` telepítőprogramot a paraméterrel a súgó és a gyorshivatkozás lehetőség áttekintéséhez. 

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

Ha csendben szeretné telepíteni az ügynököt, `C:\Support\Logs` és létre szeretne hozni egy telepítőnaplófájlt a mappában, futtassa a következő parancsot.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

A Csatlakoztatott számítógép ügynökéhez tartozó fájlok alapértelmezés szerint a *C:\Program Files\AzureConnectedMachineAgent*mappába települnek. Ha az ügynök a telepítés befejezése után nem indul el, a részletes hibainformációkért ellenőrizze a naplókat. A naplókönyvtár *a következő: %Programfiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Telepítés parancsfájlalapú módszerrel

1. Jelentkezzen be a kiszolgálóra.

1. Nyisson meg egy emelt szintű PowerShell-parancssort.

1. Váltson arra a mappára vagy megosztásra, amelybe a parancsfájlt másolta, és hajtsa végre a kiszolgálón a `./OnboardingScript.ps1` parancsfájl futtatásával.

### <a name="configure-the-agent-proxy-setting"></a>Az ügynökproxy-beállítás konfigurálása

A proxykiszolgáló környezeti változójának beállításához futtassa a következő parancsot:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>Az ügynök nem támogatja a proxyhitelesítés beállítását ebben az előzetes verzióban.
>

### <a name="configure-agent-communication"></a>Ügynökkommunikáció konfigurálása

Az ügynök telepítése után konfigurálnia kell az ügynököt az Azure Arc szolgáltatással való kommunikációra a következő parancs futtatásával:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Az ügynök telepítése és érvényesítése Linuxon

A Csatlakoztatott gép linuxos ügynöke a terjesztési (. RPM vagy . DEB), amely a Microsoft [csomagtárában](https://packages.microsoft.com/)található. A [rendszerhéj-parancsfájlköteg `Install_linux_azcmagent.sh` ](https://aka.ms/azcmagent) a következő műveleteket hajtja végre:

- Úgy állítja be a gazdagépet, hogy töltse le az ügynökcsomagot a packages.microsoft.com.
- A hibrid erőforrás-szolgáltató csomag telepítése.

Szükség esetén az ügynököt a proxyadatokkal `--proxy "{proxy-url}:{proxy-port}"` konfigurálhatja a paraméter befoglalásával.

A parancsfájl logikát is tartalmaz a támogatott és a nem támogatott disztribúciók azonosításához, és ellenőrzi a telepítés végrehajtásához szükséges engedélyeket. 

A következő példa letölti és telepíti az ügynököt:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Az ügynök letöltéséhez és `--proxy` telepítéséhez, beleértve az ügynök proxykiszolgálón keresztüli kommunikációra való konfigurálásának paraméterét, futtassa a következő parancsokat:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Az ügynök kommunikációjának konfigurálása

Az ügynök telepítése után konfigurálja úgy, hogy kommunikáljon az Azure Arc szolgáltatással a következő parancs futtatásával:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Az Azure Arctal létesített kapcsolat ellenőrzése

Miután telepítette az ügynököt, és konfigurálja, hogy csatlakozzon az Azure Arc kiszolgálókhoz (előzetes verzió), nyissa meg az Azure Portalon, hogy ellenőrizze, hogy a kiszolgáló sikeresen csatlakozott-e. Tekintse meg gépeit az [Azure Portalon.](https://aka.ms/hybridmachineportal)

![Sikeres kiszolgálói kapcsolat](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan kezelheti a gépet az [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendégkonfigurációja,](../../governance/policy/concepts/guest-configuration.md)ellenőrizze, hogy a gép a várt Log Analytics-munkaterületnek jelent-e, lehetővé teszi a figyelést az [Azure Monitor virtuális gépekkel](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)és még sok más használatával.

- További információ a [Log Analytics-ügynökről.](../../azure-monitor/platform/log-analytics-agent.md) A Windows és Linux log analytics-ügynöke akkor szükséges, ha proaktív módon szeretné figyelni a számítógépen futó operációs rendszert és számítási feladatokat, kezelni szeretné az Automation runbookok vagy megoldások, például az Update Management használatával, vagy más Azure-szolgáltatásokat, például az [Azure Security Centert.](../../security-center/security-center-intro.md)
