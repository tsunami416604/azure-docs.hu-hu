---
title: Hibrid gépek csatlakoztatása az Azure-hoz nagy méretekben
description: Ebben a cikkben megtudhatja, hogyan csatlakoztathat gépeket az Azure-hoz az Azure-beli kiszolgálók (előzetes verzió) használatával egyszerű szolgáltatás használatával.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192271"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Hibrid gépek csatlakoztatása az Azure-hoz nagy méretekben

A környezetében lévő több Windows- vagy Linux-géphez is engedélyezheti az Azure Arc kiszolgálókhoz (előzetes verzió) a követelményektől függően számos rugalmas beállítással. Az általunk biztosított sablonparancsfájl használatával automatizálhatja a telepítés minden lépését, beleértve az Azure Archoz való csatlakozás létrehozását is. Azonban szükség van, hogy interaktívan hajtsa végre ezt a parancsfájlt egy fiókkal, amely emelt szintű engedélyekkel rendelkezik a célgépen és az Azure-ban. A gépek csatlakoztatásához az Azure Arc a kiszolgálók, használhatja az Azure Active Directory [szolgáltatás névjegyzékhasználata](../../active-directory/develop/app-objects-and-service-principals.md) helyett a kiemelt identitás [interaktívan csatlakoztassa a gépet.](onboard-portal.md) Az egyszerű szolgáltatás egy speciális korlátozott felügyeleti identitás, amely csak a minimális `azcmagent` engedélyt kap a gépek csatlakoztatásához szükséges az Azure-hoz a parancs használatával. Ez biztonságosabb, mint egy magasabb jogosultságú fiók, például egy bérlői rendszergazda használata, és követi a hozzáférés-vezérlés biztonsági gyakorlati tanácsok. A szolgáltatásnév csak a bevezetés során használatos, más célra nem használható.  

A csatlakoztatott számítógép ügynökének telepítéséhez és konfigurálásához szükséges telepítési módszerekhez a használt automatikus módszernek rendszergazdai engedélyekkel kell rendelkeznie a gépekhez. Linuxon, a gyökérfiók és a Windows használatával, a Helyi rendszergazdák csoport tagjaként.

Mielőtt elkezdenéd, tekintse át az [előfeltételeket,](overview.md#prerequisites) és ellenőrizze, hogy az előfizetés és az erőforrások megfelelnek-e a követelményeknek.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

A folyamat végén sikeresen csatlakoztatta a hibrid gépeket az Azure Arc kiszolgálókhoz.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Egyszerű szolgáltatás létrehozása nagy méretekben való bevezetéshez

Az [Azure PowerShell](/powershell/azure/install-az-ps) használatával hozzon létre egy egyszerű szolgáltatás a [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) parancsmaggal. Vagy kövesse az egyszerű [szolgáltatás létrehozása az Azure Portal használatával a](../../active-directory/develop/howto-create-service-principal-portal.md) feladat végrehajtásához című csoportban felsorolt lépéseket.

> [!NOTE]
> Amikor létrehoz egy egyszerű szolgáltatás, a fiók tulajdonosának vagy felhasználói hozzáférés-rendszergazdának kell lennie a bevezetéshez használni kívánt előfizetésben. Ha nem rendelkezik a szerepkör-hozzárendelések létrehozásához szükséges engedélyekkel, előfordulhat, hogy létrejön az egyszerű szolgáltatás, de nem tud gépeket bevonni.
>

A szolgáltatásnév a PowerShell használatával, hajtsa végre a következőket.

1. Futtassa a következő parancsot. A [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) parancsmag kimenetét változóban kell tárolnia, különben egy későbbi lépésben nem fogja tudni beolvasni a szükséges jelszót.

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

2. A `$sp` változóban tárolt jelszó beolvasásához futtassa a következő parancsot:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. A kimenetben keresse meg a jelszó értékét a mező **jelszó** alatt, és másolja azt. Is keresse meg az értéket a területen **ApplicationId** és másolja is. Tartogasd őket későbbre egy biztonságos helyre. Ha elfelejti vagy elveszíti a szolgáltatás egyszerű jelszavát, alaphelyzetbe állíthatja azt a [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) parancsmag használatával.

A következő tulajdonságok értékeit a következő paraméterekkel `azcmagent`használjuk:

* Az **ApplicationId** tulajdonság értéke a `--service-principal-id` paraméterértékhez használatos.
* A **jelszó** tulajdonság értéke az `--service-principal-secret` ügynök csatlakoztatásához használt paraméterhez használatos.

> [!NOTE]
> Győződjön meg arról, hogy a szolgáltatásnév **ApplicationId** tulajdonság, nem az **id** tulajdonság.
>

Az **Azure Connected Machine bevezetési** szerepkör csak a gép fedélzeti ellátásához szükséges engedélyeket tartalmazza. Az egyszerű szolgáltatásengedély hez hozzárendelheti, hogy hatóköre erőforráscsoportot vagy előfizetést tartalmazzon. Szerepkör-hozzárendelés hozzáadásához olvassa el a [Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure Portal használatával,](../../role-based-access-control/role-assignments-portal.md) illetve [szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure CLI használatával című témakört.](../../role-based-access-control/role-assignments-cli.md)

## <a name="install-the-agent-and-connect-to-azure"></a>Telepítse az ügynököt, és csatlakozzon az Azure-hoz

A következő lépések telepítése és konfigurálása a csatlakoztatott gép ügynök a hibrid gépeken a parancsfájlsablon használatával, amely végrehajtja a hasonló lépéseket a [Connect hibrid gépek az Azure-hoz az Azure Portalon](onboard-portal.md) cikket. A különbség az utolsó lépés, ahol létrehozza a `azcmagent` kapcsolatot az Azure Arc a parancs használatával a szolgáltatás névszerint. 

Az alábbiakban azalábbi beállításokat kell beállítania a `azcmagent` parancs a szolgáltatásnévhez való használatra.

* `tenant-id`: Az egyedi azonosító (GUID), amely az Azure AD dedikált példányát jelöli.
* `subscription-id`: Az Azure-előfizetés előfizetésazonosítója (GUID), amelyben a gépeket szeretné.
* `resource-group`: Az erőforráscsoport neve, amelyhez a csatlakoztatott gépeket szeretné tartozni.
* `location`: Lásd a [támogatott Azure-régiókat](overview.md#supported-regions). Ez a hely lehet ugyanaz vagy eltérő, mint az erőforráscsoport helye.
* `resource-name`: (*Nem kötelező*) a helyszíni gép Azure-erőforrás-ábrázolásához használható. Ha nem adja meg ezt az értéket, a gép állomásnevét használja a gép.

A parancssori `azcmagent` eszközről az [Azcmagent referencia](azcmagent-reference.md)című elem áttekintésével tudhat meg többet.

### <a name="windows-installation-script"></a>Windows telepítési parancsfájl

Az alábbiakban egy példa a Csatlakoztatott gép ügynök windows telepítési parancsfájl, amely úgy módosult, hogy a szolgáltatás egyszerű, hogy támogassa a teljesen automatizált, nem interaktív telepítés az ügynök.

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

### <a name="linux-installation-script"></a>Linux telepítő parancsfájl

Az alábbiakban egy példa a Csatlakoztatott gép ügynök Linux telepítési parancsfájl, amely úgy módosult, hogy a szolgáltatás névszerint támogatja az ügynök teljesen automatizált, nem interaktív telepítését.

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

Miután telepítette az ügynököt, és konfigurálja, hogy csatlakozzon az Azure Arc kiszolgálókhoz (előzetes verzió), nyissa meg az Azure Portalon, hogy ellenőrizze, hogy a kiszolgáló sikeresen csatlakozott-e. Tekintse meg gépeit az [Azure Portalon.](https://aka.ms/hybridmachineportal)

![Sikeres kiszolgálói kapcsolat](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan kezelheti a gépet az [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendégkonfigurációja,](../../governance/policy/concepts/guest-configuration.md)ellenőrizze, hogy a gép a várt Log Analytics-munkaterületnek jelent-e, lehetővé teszi a figyelést az [Azure Monitor virtuális gépekkel](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)és még sok más használatával.

- További információ a [Log Analytics-ügynökről.](../../azure-monitor/platform/log-analytics-agent.md) A Windows és Linux log analytics-ügynöke akkor szükséges, ha proaktív módon szeretné figyelni a számítógépen futó operációs rendszert és számítási feladatokat, kezelni szeretné az Automation runbookok vagy megoldások, például az Update Management használatával, vagy más Azure-szolgáltatásokat, például az [Azure Security Centert.](../../security-center/security-center-intro.md)
