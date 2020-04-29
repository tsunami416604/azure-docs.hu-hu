---
title: Hibrid gépek összekötése az Azure-on nagy méretekben
description: Ebből a cikkből megtudhatja, hogyan csatlakoztathatók a gépek az Azure-hoz az Azure arc for Servers (előzetes verzió) használatával az egyszerű szolgáltatásnév használatával.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77192271"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Hibrid gépek összekötése az Azure-on nagy méretekben

Az Azure arc for Servers (előzetes verzió) szolgáltatást engedélyezheti több Windows-vagy Linux-gépen a környezetben, és a követelményektől függően számos rugalmas lehetőség közül választhat. Az általunk biztosított sablon-parancsfájl segítségével automatizálhatja a telepítés minden lépését, beleértve az Azure arc-hoz való kapcsolódás létrehozását is. Azonban a parancsfájlt interaktív módon kell végrehajtani egy olyan fiókkal, amely emelt szintű engedélyekkel rendelkezik a célszámítógépen és az Azure-ban. Ahhoz, hogy a gépeket az Azure arc kiszolgálókhoz lehessen kapcsolni a kiszolgálókon, használhat egy Azure Active Directory [szolgáltatásnevet](../../active-directory/develop/app-objects-and-service-principals.md) ahelyett, hogy a Kiemelt identitást használja a [számítógép interaktív összekapcsolásához](onboard-portal.md). Az egyszerű szolgáltatásnév egy speciális, korlátozott felügyeleti identitás, amely csak a gépek az Azure-hoz való összekapcsolásához szükséges minimális `azcmagent` engedélyeket kapja meg a parancs használatával. Ez biztonságosabb, mint egy magasabb jogosultsági szintű fiók használata, mint például egy Bérlői rendszergazda, és a hozzáférés-vezérléssel kapcsolatos ajánlott biztonsági eljárásokat követi. Az egyszerű szolgáltatás csak a bevezetéskor használatos, ezért semmilyen más célra nem használható.  

A csatlakoztatott számítógép-ügynök telepítéséhez és konfigurálásához szükséges telepítési módszerek megkövetelik, hogy a használt automatizált módszer rendszergazdai engedélyekkel rendelkezik a gépeken. Linux rendszeren a rendszergazdai fiók és a Windows rendszeren a helyi Rendszergazdák csoport tagjaként.

Mielőtt elkezdené, tekintse át az [előfeltételeket](overview.md#prerequisites) , és győződjön meg arról, hogy az előfizetés és az erőforrások megfelelnek a követelményeknek.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

A folyamat végén sikeresen csatlakoztatta a hibrid gépeket az Azure arc for Servers rendszerhez.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Egyszerű szolgáltatás létrehozása a méretezéshez

A [Azure PowerShell](/powershell/azure/install-az-ps) használatával létrehozhat egy egyszerű szolgáltatást a [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) parancsmaggal. Vagy követheti az egyszerű szolgáltatásnév létrehozása című témakörben felsorolt lépéseket a feladat végrehajtásához [a Azure Portal használatával](../../active-directory/develop/howto-create-service-principal-portal.md) .

> [!NOTE]
> Egyszerű szolgáltatásnév létrehozásakor a fióknak a bevezetéshez használni kívánt előfizetés tulajdonosi vagy felhasználói hozzáférési rendszergazdája kell lennie. Ha nem rendelkezik megfelelő engedélyekkel a szerepkör-hozzárendelések létrehozásához, előfordulhat, hogy az egyszerű szolgáltatásnév létrejött, de nem fogja tudni bevezetni a gépeket.
>

Az egyszerű szolgáltatásnév a PowerShell használatával történő létrehozásához hajtsa végre a következő műveleteket.

1. Futtassa a következő parancsot. A [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) parancsmag kimenetét egy változóban kell tárolnia, vagy egy későbbi lépésben nem fogja tudni lekérni a szükséges jelszót.

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

2. A `$sp` változóban tárolt jelszó lekéréséhez futtassa a következő parancsot:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. A kimenetben keresse meg a jelszó értéket a mező **jelszava** alatt, és másolja. Keresse meg a **ApplicationId** mező alatti értéket is, és másolja azt is. Mentse őket később egy biztonságos helyen. Ha elfelejti vagy elveszíti a szolgáltatás egyszerű jelszavát, a [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) parancsmag használatával visszaállíthatja.

A következő tulajdonságok értékeit használja a rendszer a paraméternek átadott `azcmagent`paraméterekkel:

* A **ApplicationId** tulajdonság értékét a rendszer a `--service-principal-id` paraméter értékéhez használja.
* A **Password (jelszó** ) tulajdonság értéke az ügynök összekapcsolásához használt `--service-principal-secret` paraméterhez használatos.

> [!NOTE]
> Ügyeljen arra, hogy az egyszerű szolgáltatásnév **ApplicationId** tulajdonságát használja, ne az **ID** tulajdonságot.
>

Az **Azure Connected Machine** bevezetési szerepkör csak a gép bevezetéséhez szükséges engedélyeket tartalmazza. Hozzárendelheti az egyszerű szolgáltatás engedélyét, hogy a hatóköre tartalmazzon egy erőforráscsoportot vagy egy előfizetést. A szerepkör-hozzárendelés hozzáadásával kapcsolatban lásd: [szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és a Azure Portal](../../role-based-access-control/role-assignments-portal.md) , illetve [szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure CLI használatával](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Az ügynök telepítése és az Azure-hoz való kapcsolódás

A következő lépésekkel telepítheti és konfigurálhatja a csatlakoztatott gépi ügynököt a hibrid gépeken a parancsfájl-sablon használatával, amely a [hibrid gépek csatlakoztatása az Azure-hoz](onboard-portal.md) című témakörben leírt hasonló lépéseket hajt végre a Azure Portal cikkben. A különbség az utolsó lépés, amelyben az Azure arc kapcsolatát az `azcmagent` paranccsal az egyszerű szolgáltatásnév használatával hozza létre. 

Az alábbi beállításokkal konfigurálhatja az `azcmagent` egyszerű szolgáltatáshoz használt parancsot.

* `tenant-id`: Az egyedi azonosító (GUID), amely az Azure AD dedikált példányát jelöli.
* `subscription-id`: Annak az Azure-előfizetésnek az előfizetés-azonosítója (GUID), amelyhez a gépeket szeretné használni.
* `resource-group`: Az erőforráscsoport neve, ahová a csatlakoztatott gépek tartoznak.
* `location`: Tekintse meg a [támogatott Azure-régiókat](overview.md#supported-regions). Ez a hely lehet azonos vagy eltérő, mint az erőforráscsoport helye.
* `resource-name`: (Nem*kötelező*) a helyszíni gép Azure-beli erőforrás-ábrázolásához használatos. Ha nem megadja ezt az értéket, a rendszer a gép állomásnevét használja.

A `azcmagent` parancssori eszközzel kapcsolatos további információkért tekintse meg a [Azcmagent-hivatkozás](azcmagent-reference.md)áttekintését.

### <a name="windows-installation-script"></a>Windows telepítési parancsfájl

A következőkben egy példa látható a Windows telepítési parancsfájlhoz készült csatlakoztatott Machine Agent ügynökre, amelyet úgy módosítottak, hogy az a szolgáltatásnév használatával támogassa az ügynök teljes mértékben automatizált, nem interaktív telepítését.

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

### <a name="linux-installation-script"></a>Linuxos telepítési parancsfájl

A következőkben egy példa látható a Linux telepítési parancsfájlhoz készült csatlakoztatott Machine Agent ügynökre, amelyet az egyszerű szolgáltatásnév használata az ügynök teljes mértékben automatizált, nem interaktív telepítésének támogatásához módosított.

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

Miután telepítette az ügynököt, és úgy konfigurálja, hogy az Azure arc for Servers (előzetes verzió) szolgáltatáshoz kapcsolódjon, lépjen a Azure Portal, és ellenőrizze, hogy a kiszolgáló sikeresen csatlakoztatva van-e. Megtekintheti a gépeket a [Azure Portalban](https://aka.ms/hybridmachineportal).

![Sikeres kiszolgálói kapcsolatok](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan kezelheti a gépet [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, lehetővé teszi a figyelést a virtuális [gépekkel Azure monitor](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)és sok más további műveletet.

- További információ a [log Analytics-ügynökről](../../azure-monitor/platform/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics-ügynökre akkor van szükség, ha proaktívan szeretné figyelni a gépen futó operációs rendszert és munkaterheléseket, felügyelheti azt automatizálási runbookok vagy olyan megoldások használatával, mint például a Update Management, vagy más Azure-szolgáltatásokat is használhat, mint például a [Azure Security Center](../../security-center/security-center-intro.md).
