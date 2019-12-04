---
title: Gyors útmutató – számítógépek összekötése az Azure-ba az Azure arc for Servers használatával – PowerShell
description: Ebből a rövid útmutatóból megtudhatja, hogyan csatlakoztathatók a gépek az Azure-hoz az Azure arc használatával a kiszolgálókon a PowerShell használatával
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, PowerShell, a kívánt állapot konfigurálása, frissítés kezelése, változások követése, leltár, runbookok, Python, grafikus, hibrid, előkészítés
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 7fb24d53876ab8c06fca4fbfe929c06a889335f3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786350"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---powershell"></a>Gyors útmutató: számítógépek összekötése az Azure-hoz az Azure arc for Servers használatával – PowerShell

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Tekintse át a támogatott ügyfeleket és a szükséges hálózati konfigurációt az [Azure arc for Servers áttekintése című témakörben](overview.md).

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Egyszerű szolgáltatás létrehozása a méretezéshez

Az egyszerű szolgáltatásnév egy speciális korlátozott felügyeleti identitás, amely csak a gépek Azure-hoz való összekapcsolásához szükséges minimális engedélyeket kapja meg. Ez biztonságosabb, mint egy nagyobb teljesítményű fiók használata, mint a Bérlői rendszergazda. Az egyszerű szolgáltatásnév csak a bevezetéskor használatos. A szolgáltatásnév biztonságos törlését a kívánt kiszolgálók összekapcsolását követően végezheti el.

> [!NOTE]
> Ez a lépés ajánlott, de nem kötelező.

### <a name="steps-to-create-the-service-principal"></a>Az egyszerű szolgáltatás létrehozásának lépései

Ebben a példában a [Azure PowerShellt](/powershell/azure/install-az-ps) használjuk egy egyszerű szolgáltatásnév (SPN) létrehozásához. Másik lehetőségként követheti az [egyszerű szolgáltatásnév létrehozása](../../active-directory/develop/howto-create-service-principal-portal.md) című témakörben felsorolt lépéseket a feladat Azure Portal használatával.

A `Azure Connected Machine Onboarding` szerepkör csak a bevezetéshez szükséges engedélyeket tartalmazza. Megadhatja egy egyszerű szolgáltatásnév engedélyét, amely lehetővé teszi annak hatókörét egy erőforráscsoport vagy előfizetés lefedéséhez.

Az [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) -parancsmag kimenetét kell tárolnia, vagy nem fogja tudni lekérni a jelszót egy későbbi lépésben való használatra.

```azurepowershell-interactive
$sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
$sp
```

```output
Secret                : System.Security.SecureString
ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
ObjectType            : ServicePrincipal
DisplayName           : Hybrid-RP
Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
Type                  :
```

Most kérje le a jelszót a PowerShell használatával.

```azurepowershell-interactive
$credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
$credential.GetNetworkCredential().password
```

A kimenetből másolja a **jelszót** és a **ApplicationId** (az előző lépésből), és tárolja azokat később egy biztonságos helyen, például a kiszolgáló konfigurációs eszközének titkos tárolójában. Ha elfelejti vagy elveszítette az egyszerű szolgáltatásnév jelszavát, a [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) parancsmaggal állíthatja vissza.

Az ügynök telepítése bevezetési parancsfájlban:

* A **ApplicationId** tulajdonság a telepítési ügynökben használt `--service-principal-id` paraméterhez használatos.
* A **Password** tulajdonság a telepítési ügynök `--service-principal-secret` paramétereként használatos.

## <a name="manually-install-the-agent-and-connect-to-azure"></a>Az ügynök manuális telepítése és az Azure-hoz való kapcsolódás

A következő útmutató lehetővé teszi egy gép összekapcsolását az Azure-ba a számítógépbe való bejelentkezéssel és a lépések végrehajtásával. [A portálról](quickstart-onboard-portal.md)is csatlakoztathatók a gépek az Azure-hoz.

### <a name="download-and-install-the-agent"></a>Az ügynök letöltése és telepítése

Az ügynök csomagjának telepítése a célkiszolgálón rendszergazdai vagy helyi rendszergazdai hozzáférésre van szükség, de nincs Azure-hozzáférése.

#### <a name="linux"></a>Linux

A **Linux** -kiszolgálók esetében az ügynököt a [Microsoft csomag-tárházán](https://packages.microsoft.com) keresztül terjeszti a rendszer a terjesztés előnyben részesített csomag formátumával (. RPM vagy. DEB).

> [!NOTE]
> A nyilvános előzetes verzióban csak egy csomag jelenik meg, amely Ubuntu 16,04 vagy 18,04 esetén megfelelő.

<!-- What about this aks? -->
A legegyszerűbb lehetőség a Package adattár regisztrálása, majd a csomag telepítése a Distribution Package Manager használatával.
A [https://aka.ms/azcmagent](https://aka.ms/azcmagent) található bash-parancsfájl a következő műveleteket hajtja végre:

1. Úgy konfigurálja a gazdagépet, hogy letöltse a `packages.microsoft.com`.
2. Telepíti a hibrid erőforrás-szolgáltatói csomagot.
3. Szükség esetén a proxy művelethez is konfigurálhatja az ügynököt, ha a `--proxy`t adja meg.

A parancsfájl a támogatott és nem támogatott disztribúciók ellenőrzéseit is tartalmazza, valamint a telepítéshez szükséges engedélyek észlelését.

Az alábbi példa letölti az ügynököt, és telepíti azt a feltételes ellenőrzések nélkül.

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. Omit the '--proxy "{proxy-url}"' parameters if proxy is not needed
bash ~/Install_linux_azcmagent.sh--proxy "{proxy-url}"
```

> [!NOTE]
> Ha inkább nem a Microsoft csomagkezelő-tárházára hivatkozik, akkor a csomagot innen is másolhatja a belső tárházba.

#### <a name="windows"></a>Windows

A **Windows**esetében az ügynök egy Windows Installer (`.MSI`) fájlba van csomagolva, és a [https://download.microsoft.com](https://download.microsoft.com)üzemeltetett [https://aka.ms/AzureConnectedMachineAgent ból ](https://aka.ms/AzureConnectedMachineAgent)tölthető le.

```powershell
# Download the package
Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String
```

> [!NOTE]
> Linux rendszeren a telepítési parancsfájl ismételt futtatása automatikusan a legújabb verzióra fog frissülni. Windows rendszeren el kell távolítania az "Azure Connected Machine Agent" ügynököt, mielőtt a telepítőt újra futtatná a frissítéshez.

### <a name="connecting-to-azure"></a>Csatlakozás az Azure-hoz

A telepítés után az ügynököt egy `azcmagent.exe`nevű parancssori eszköz használatával kezelheti és konfigurálhatja. Az ügynök a Linux és a Windows `$env:programfiles\AzureConnectedMachineAgent` `/opt/azcmagent/bin` alatt található.

Windows rendszeren nyissa meg a PowerShellt rendszergazdaként a cél csomóponton, és futtassa a következőt:

```powershell
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{your-spn-appid}" `
  --service-principal-secret "{your-spn-password}" `
  --resource-group "{your-resource-group-name}" `
  --tenant-id "{your-tenant-id}" `
  --location "{location-of-your-resource-group}" `
  --subscription-id "{your-subscription-id}"
```

Linux rendszeren nyisson meg egy rendszerhéjat, és futtassa a parancsot.

<!-- Same command for linux?-->
```bash
azcmagent connect \
  --service-principal-id "{your-spn-appid}" \
  --service-principal-secret "{your-spn-password}" \
  --resource-group "{your-resource-group-name}" \
  --tenant-id "{your-tenant-id}" \
  --location "{location-of-your-resource-group}" \
  --subscription-id "{your-subscription-id}"
```

Paraméterek:

* `tenant-id`: a bérlő GUID-azonosítója. Azure Portal az **Azure Active directory** -> **Tulajdonságok** -> **könyvtár-azonosító**lehetőség kiválasztásával találhatja meg.
* `subscription-id`: annak az előfizetésnek a GUID azonosítója, amely az Azure-ban csatlakozik a számítógéphez.
* `resource-group`: az erőforráscsoport, amelyhez a gépet csatlakoztatni szeretné.
* `location`: [Az Azure-régiók és-telephelyek](https://azure.microsoft.com/global-infrastructure/regions/). Ez a hely lehet azonos vagy más, mint az erőforráscsoport helye. A nyilvános előzetes verzióban a szolgáltatás a **WestUS2** és **Nyugat-Európában**is támogatott.
* `resource-name`: (nem*kötelező*) a helyszíni gép Azure-beli erőforrás-ábrázolásához használatos. Ha nem megadja ezt az értéket, a rendszer a gép állomásnevét fogja használni.

A "azcmagent" eszközzel kapcsolatos további információkat a [azcmagent-dokumentációban](azcmagent-reference.md)talál.
<!-- Isn't this still needed to view machines? -->

A sikeres befejezést követően a gép csatlakoztatva van az Azure-hoz. A Azure Portal a [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal)meglátogatásával tekintheti meg a számítógépet.

![Sikeres előkészítés](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

### <a name="proxy-server-configuration"></a>Proxykiszolgáló konfigurálása

#### <a name="linux"></a>Linux

<!-- New proxy name? -->

**Linux**esetén, ha a kiszolgálónak proxykiszolgálót kell használnia, az alábbiakat teheti:

* Futtassa a `install_linux_hybrid_agent.sh` szkriptet az [ügynök telepítése](#download-and-install-the-agent) című szakaszban, a következővel: `--proxy`.
* Ha már telepítette az ügynököt, hajtsa végre a `/opt/azcmagent/bin/hybridrp_proxy add http://{proxy-url}:{proxy-port}`parancsot, amely konfigurálja a proxyt, és újraindítja az ügynököt.

#### <a name="windows"></a>Windows

**Windows**esetén, ha a kiszolgálónak proxykiszolgáló szükséges az internetes erőforrásokhoz való hozzáféréshez, az alábbi parancs futtatásával állíthatja be a proxykiszolgáló környezeti változóját. Ez lehetővé teszi, hogy az ügynök proxykiszolgálót használjon az internet-hozzáféréshez.

```powershell
# If a proxy server is needed, execute these commands with actual proxy URL
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# The agent service needs to be restarted after the proxy environment variable is set in order for the changes to take effect.
Restart-Service -Name himds
```

> [!NOTE]
> Nyilvános előzetes verzió esetén a hitelesített proxyk nem támogatottak.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A gépek Azure arc-kiszolgálókról történő leválasztásához két lépést kell végrehajtania.

1. Válassza ki a gépet a [portálon](https://aka.ms/hybridmachineportal), kattintson a három pontra (`...`), majd válassza a **Törlés**lehetőséget.
1. Távolítsa el az ügynököt a gépről.

   Windows rendszeren az "alkalmazások & szolgáltatások" Vezérlőpult használatával távolíthatja el az ügynököt.
  
  ![Alkalmazások & funkciók](./media/quickstart-onboard/apps-and-features.png)

   Ha parancsfájlt szeretne használni az eltávolításhoz, használja a következő példát, amely lekéri a **PackageId** , és eltávolítja az ügynököt `msiexec /X`használatával.

   Keresse meg a `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` beállításkulcsot, és keresse meg a **PackageId**. Ezután eltávolíthatja az ügynököt `msiexec`használatával.

   Az alábbi példa az ügynök eltávolítását mutatja be.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   Linux rendszeren futtassa a következő parancsot az ügynök eltávolításához.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Szabályzat társítása csatlakoztatott gépekhez](../../governance/policy/assign-policy-portal.md)
