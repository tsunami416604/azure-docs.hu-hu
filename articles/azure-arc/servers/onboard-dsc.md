---
title: Csatlakoztatott számítógép ügynökének telepítése a Windows PowerShell DSC használatával
description: Ebből a cikkből megtudhatja, hogyan csatlakoztathatók a gépek az Azure-hoz az Azure arc for Servers (előzetes verzió) használatával a Windows PowerShell DSC használatával.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79164682"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>A csatlakoztatott gép ügynökének telepítése a Windows PowerShell DSC használatával

A [Windows PowerShell desired State Configuration](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) (DSC) használatával automatizálhatja a szoftverek telepítését és konfigurációját a Windows rendszerű számítógépeken. Ez a cikk azt ismerteti, hogyan telepítheti a DSC-t az Azure arc for Servers Connected Machine Agent használatára a hibrid Windows rendszerű gépeken.

## <a name="requirements"></a>Követelmények

- Windows PowerShell 4,0 vagy újabb verzió

- A [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC modul

- Egy egyszerű szolgáltatásnév, amely nem interaktív módon csatlakozik a gépekhez az Azure-ív számára. Ha még nem hozott létre a kiszolgálókhoz arc for Servers szolgáltatást, kövesse az [egyszerű szolgáltatás létrehozása](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) a bevezetéshez című szakasz lépéseit.

## <a name="install-the-connectedmachine-dsc-module"></a>A ConnectedMachine DSC moduljának telepítése

1. A modul manuális telepítéséhez töltse le a forráskódot, és bontsa ki a projekt könyvtárának tartalmát a `$env:ProgramFiles\WindowsPowerShell\Modules folder`. Vagy futtassa a következő parancsot a PowerShell-galériából a PowerShellGet használatával történő telepítéshez (a PowerShell 5,0-ben):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. A telepítés megerősítéséhez futtassa a következő parancsot, és győződjön meg arról, hogy az Azure Connected Machine DSC-erőforrásai elérhetők.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   A kimenetben a következőhöz hasonlónak kell megjelennie:

   ![A csatlakoztatott Machine DSC-modul telepítési példájának megerősítése](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Az ügynök telepítése és az Azure-hoz való kapcsolódás

Az ebben a modulban található erőforrások úgy vannak kialakítva, hogy kezelhesse az Azure-beli csatlakoztatott gép ügynökének konfigurációját. Az is tartalmaz egy PowerShell-parancsfájlt `AzureConnectedMachineAgent.ps1`, amely a `AzureConnectedMachineDsc\examples` mappában található. Közösségi erőforrásokat használ a letöltés és a telepítés automatizálásához, és kapcsolatot létesíteni az Azure arc használatával. Ez a szkript a [hibrid gépek az Azure-ba való összekapcsolásához a Azure Portal](onboard-portal.md) cikkben ismertetett hasonló lépéseket hajtja végre.

Ha a gépnek egy proxykiszolgálón keresztül kell kommunikálnia a szolgáltatással, az ügynök telepítése után futtatnia kell egy, az [itt](onboard-portal.md#configure-the-agent-proxy-setting)ismertetett parancsot. Ezzel beállítja a proxykiszolgáló rendszerkörnyezeti változóját `https_proxy`. A parancs manuális futtatása helyett a [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0) modul használatával elvégezheti ezt a lépést a DSC-vel.

>[!NOTE]
>A DSC futtatásának engedélyezéséhez a Windows rendszert úgy kell konfigurálni, hogy a localhost konfigurációjának futtatásakor is megkapja a PowerShell távoli parancsait. A környezet megfelelő konfigurálásához egyszerűen futtasson `Set-WsManQuickConfig -Force` egy emelt szintű PowerShell-terminálon.
>

A konfigurációs dokumentumok (MOF-fájlok) a `Start-DscConfiguration` parancsmag használatával alkalmazhatók a gépre.

A következő paraméterek a használni kívánt PowerShell-szkripthez adhatók meg.

- `TenantId`: az egyedi azonosító (GUID), amely az Azure AD dedikált példányát jelöli.

- `SubscriptionId`: annak az Azure-előfizetésnek az előfizetés-azonosítója (GUID), amelyhez a gépeket szeretné használni.

- `ResourceGroup`: az erőforráscsoport neve, ahová a csatlakoztatott gépek tartoznak.

- `Location`: Tekintse meg a [támogatott Azure-régiókat](overview.md#supported-regions). Ez a hely lehet azonos vagy eltérő, mint az erőforráscsoport helye.

- `Tags`: a csatlakoztatott gépi erőforrásra alkalmazni kívánt címkék karakterlánc-tömbje.

- `Credential`: a **ApplicationId** és- **jelszóval** ellátott PowerShell hitelesítőadat-objektum, amellyel a gépeket egy [egyszerű szolgáltatásnév](onboard-service-principal.md)használatával lehet regisztrálni. 

1. A PowerShell-konzolon Navigáljon arra a mappára, ahová a `.ps1` fájlt mentette.

2. Futtassa a következő PowerShell-parancsokat a MOF-dokumentum fordításához (a DSC-konfigurációk fordításával kapcsolatos információkért lásd: [DSC-konfigurációk](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Ezzel egy `localhost.mof file` fog létrehozni egy `C:\dsc`nevű új mappában.

Miután telepítette az ügynököt, és úgy konfigurálja, hogy az Azure arc for Servers (előzetes verzió) szolgáltatáshoz kapcsolódjon, lépjen a Azure Portal, és ellenőrizze, hogy a kiszolgáló sikeresen csatlakoztatva van-e. Megtekintheti a gépeket a [Azure Portalban](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Hozzáadás meglévő konfigurációkhoz

Ezt az erőforrást hozzá lehet adni a meglévő DSC-konfigurációkhoz a gép végpontok közötti konfigurációjának ábrázolásához. Előfordulhat például, hogy hozzá szeretné adni ezt az erőforrást egy olyan konfigurációhoz, amely az operációs rendszer biztonságos beállításait állítja be.

A PowerShell-galéria [CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) modulja a konfiguráció [összetett erőforrásának](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) létrehozásához használható a konfigurációk összekapcsolásának további egyszerűsítése érdekében.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan kezelheti a gépet [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, lehetővé teszi a figyelést a virtuális [gépekkel Azure monitor](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)és sok más további műveletet.

- További információ a [log Analytics-ügynökről](../../azure-monitor/platform/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics-ügynökre akkor van szükség, ha proaktívan szeretné figyelni a gépen futó operációs rendszert és munkaterheléseket, felügyelheti azt automatizálási runbookok vagy olyan megoldások használatával, mint például a Update Management, vagy más Azure-szolgáltatásokat is használhat, mint például a [Azure Security Center](../../security-center/security-center-intro.md).