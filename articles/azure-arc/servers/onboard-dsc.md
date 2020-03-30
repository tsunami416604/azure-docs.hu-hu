---
title: Csatlakoztatott számítógép-ügynök telepítése a Windows PowerShell DSC használatával
description: Ebben a cikkben megtudhatja, hogyan csatlakoztathat gépeket az Azure-hoz az Azure-beli Arc kiszolgálókhoz (előzetes verzió) használatával a Windows PowerShell DSC használatával.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164682"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>A csatlakoztatott számítógép ügynöktelepítése a Windows PowerShell DSC használatával

A [Windows PowerShell kívánt állapotkonfigurációja](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) (DSC) használatával automatizálhatja a Windows-számítógépek szoftvertelepítését és -konfigurációját. Ez a cikk ismerteti, hogyan dsc használatával telepítheti az Azure Arc kiszolgálók hoz csatlakoztatott gép ügynök hibrid Windows-gépeken.

## <a name="requirements"></a>Követelmények

- A Windows PowerShell 4.0-s vagy újabb verziója

- Az [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC modul

- A gépek nem interaktív módon az Azure Archoz való csatlakoztatásához egyszerű szolgáltatás. Kövesse az Egyszerű [szolgáltatás létrehozása nagy méretekben való bevezetéshez](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) szakaszlépéseit, ha még nem hozott létre egyszerű szolgáltatást az Arc számára a kiszolgálókhoz.

## <a name="install-the-connectedmachine-dsc-module"></a>A ConnectedMachine DSC modul telepítése

1. A modul manuális telepítéséhez töltse le a forráskódot, és `$env:ProgramFiles\WindowsPowerShell\Modules folder`csomagolja ki a projektkönyvtár tartalmát a számára. Vagy futtassa a következő parancsot a PowerShell-galériából a PowerShellGet használatával történő telepítéshez (a PowerShell 5.0-s részében):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. A telepítés megerősítéséhez futtassa a következő parancsot, és győződjön meg arról, hogy az Azure Connected Machine DSC rendelkezésre álló erőforrásokat.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   A kimenetben a következőhöz hasonló tetszőséget kell látnia:

   ![Példa a csatlakoztatott gép DSC modul telepítésének megerősítésére](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Telepítse az ügynököt, és csatlakozzon az Azure-hoz

A modul ban található erőforrások az Azure Connected Machine Agent konfigurációjának kezelésére szolgálnak. Is szerepel egy PowerShell-parancsfájl `AzureConnectedMachineAgent.ps1` `AzureConnectedMachineDsc\examples` , megtalálható a mappában. Közösségi erőforrásokat használ a letöltés és a telepítés automatizálásához, és kapcsolatot létesít az Azure Arc. Ez a parancsfájl hasonló lépéseket hajt végre a [Connect hibrid gépek az Azure-hoz az Azure Portal cikkből.](onboard-portal.md)

Ha a számítógépnek proxykiszolgálón keresztül kell kommunikálnia a szolgáltatással, az ügynök telepítése után az [itt](onboard-portal.md#configure-the-agent-proxy-setting)ismertetett parancsot kell futtatnia. Ezzel beállítja a `https_proxy`proxykiszolgáló rendszerkörnyezeti változóját. Ahelyett, hogy a parancsot manuálisan futtatna, ezt a lépést a DSC-vel hajthatja végre a [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0) modul használatával.

>[!NOTE]
>A DSC futtatásának engedélyezéséhez a Windows rendszert úgy kell konfigurálni, hogy akkor is megkapja a PowerShell távoli parancsait, ha localhost-konfigurációt futtat. A környezet megfelelő konfigurálásához csak `Set-WsManQuickConfig -Force` futtassa egy emelt szintű PowerShell-terminálon.
>

A konfigurációs dokumentumok (MOF-fájlok) a `Start-DscConfiguration` parancsmag használatával alkalmazhatók a gépre.

Az alábbiakban azokat a paramétereket, amelyeket átad a PowerShell-parancsfájlnak.

- `TenantId`: Az egyedi azonosító (GUID), amely az Azure AD dedikált példányát jelöli.

- `SubscriptionId`: Az Azure-előfizetés előfizetésazonosítója (GUID), amelyben a gépeket szeretné.

- `ResourceGroup`: Az erőforráscsoport neve, amelyhez a csatlakoztatott gépeket szeretné tartozni.

- `Location`: Lásd a [támogatott Azure-régiókat](overview.md#supported-regions). Ez a hely lehet ugyanaz vagy eltérő, mint az erőforráscsoport helye.

- `Tags`: A csatlakoztatott gép erőforrására alkalmazandó címkék karakterlánctömbje.

- `Credential`: PowerShell-hitelesítő adatok objektuma az **ApplicationId** azonosítóval és **jelszóval,** amely a gépek nagy méretekben történő regisztrálására [szolgál egyszerű szolgáltatás](onboard-service-principal.md)használatával. 

1. Egy PowerShell-konzolon keresse meg azt `.ps1` a mappát, amelyben a fájlt mentette.

2. Futtassa a következő PowerShell-parancsokat a MOF-dokumentum fordításához (a DSC-konfigurációk összeállításáról a DSC-konfigurációk című témakörben talál információt: [DSC-konfigurációk:](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7)

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Ezzel létrehoz `localhost.mof file` egy új mappát, amelynek neve `C:\dsc`.

Miután telepítette az ügynököt, és konfigurálja, hogy csatlakozzon az Azure Arc kiszolgálókhoz (előzetes verzió), nyissa meg az Azure Portalon, hogy ellenőrizze, hogy a kiszolgáló sikeresen csatlakozott-e. Tekintse meg gépeit az [Azure Portalon.](https://aka.ms/hybridmachineportal)

## <a name="adding-to-existing-configurations"></a>Hozzáadás meglévő konfigurációkhoz

Ez az erőforrás hozzáadható a meglévő DSC-konfigurációkhoz, hogy egy számítógép végpontok között lévő konfigurációt képviseljen. Előfordulhat például, hogy hozzá szeretné adni ezt az erőforrást egy olyan konfigurációhoz, amely beállítja a biztonságos operációs rendszer beállításait.

A [PowerShell-katalógus CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) modul segítségével létrehozhat egy [összetett erőforrást](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) a példa konfiguráció, a konfigurációk kombinálásának további egyszerűsítése érdekében.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan kezelheti a gépet az [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendégkonfigurációja,](../../governance/policy/concepts/guest-configuration.md)ellenőrizze, hogy a gép a várt Log Analytics-munkaterületnek jelent-e, lehetővé teszi a figyelést az [Azure Monitor virtuális gépekkel](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)és még sok más használatával.

- További információ a [Log Analytics-ügynökről.](../../azure-monitor/platform/log-analytics-agent.md) A Windows és Linux log analytics-ügynöke akkor szükséges, ha proaktív módon szeretné figyelni a számítógépen futó operációs rendszert és számítási feladatokat, kezelni szeretné az Automation runbookok vagy megoldások, például az Update Management használatával, vagy más Azure-szolgáltatásokat, például az [Azure Security Centert.](../../security-center/security-center-intro.md)