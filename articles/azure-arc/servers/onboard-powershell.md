---
title: Hibrid gépek összekötése az Azure-ba a PowerShell használatával
description: Ebből a cikkből megtudhatja, hogyan telepítheti az ügynököt, és hogyan csatlakoztatható egy gép az Azure-hoz az Azure arc-kompatibilis kiszolgálók használatával a PowerShell használatával.
ms.date: 10/21/2020
ms.topic: conceptual
ms.openlocfilehash: d36fd174606b49b28b1d8343bff6ccc1f62e5194
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375272"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Hibrid gépek összekötése az Azure-ba a PowerShell használatával

Az Azure arc-kompatibilis kiszolgálókat egy vagy több Windows-vagy Linux-gépen is engedélyezheti a környezetben a lépések manuális elvégzésével. Vagy használhatja a [AzConnectedMachine PowerShell-](/powershell/module/az.connectedmachine/remove-azconnectedmachine)parancsmagot is. Ez a parancsmag a következő műveleteket hajtja végre:

- Úgy konfigurálja a gazdagépet, hogy letöltse a Windows-ügynököt a Microsoft letöltőközpontból, valamint a Linux-ügynök csomagját a packages.microsoft.com-ből.
- Telepíti a csatlakoztatott gépi ügynököt.
- A gép regisztrálása az Azure Arcmal

Ehhez a módszerhez rendszergazdai jogosultságokkal kell rendelkeznie a gépen az ügynök telepítéséhez és konfigurálásához. Linux rendszeren a legfelső szintű fiók, a Windows rendszeren pedig a helyi Rendszergazdák csoport tagja.

Mielőtt elkezdené, tekintse át az [előfeltételeket](agent-overview.md#prerequisites) , és győződjön meg arról, hogy az előfizetés és az erőforrások megfelelnek a követelményeknek. A támogatott régiókkal és az egyéb kapcsolódó megfontolásokkal kapcsolatos információkért lásd: [támogatott Azure-régiók](overview.md#supported-regions).

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

- Azure PowerShell-t futtató számítógép. Útmutatásért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/).

Mielőtt a Azure PowerShell használatával kezelhesse a virtuálisgép-bővítményeket az arc-kompatibilis kiszolgálók által felügyelt hibrid kiszolgálón, telepítenie kell a `Az.ConnectedMachine` modult. Futtassa a következő parancsot az ív használatára képes kiszolgálón:

```powershell
Install-Module -Name Az.ConnectedMachine
```

A telepítés befejeztével a következő üzenet jelenik meg:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-and-validate-the-agent-on-windows"></a>Az ügynök telepítése és ellenőrzése Windows rendszeren

1. Nyisson meg egy PowerShell-konzolt rendszergazdaként.

2. Jelentkezzen be az Azure-ba a parancs futtatásával `Connect-AzAccount` .

3. A csatlakoztatott számítógép ügynökének telepítéséhez használja `Connect-AzConnectedMachine` a, a `-Name` `-ResourceGroupName` és a `-Location` paramétereket. A `-SubscriptionId` paraméter használatával felülbírálhatja az alapértelmezett előfizetést a bejelentkezés után létrehozott Azure-környezet eredményeképpen.

    A következő parancs futtatásával telepítheti a csatlakoztatott gépi ügynököt a célszámítógépen, amely közvetlenül tud kommunikálni az Azure-ban::

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    Ha a célszámítógép egy proxykiszolgálón keresztül kommunikál, futtassa a következő parancsot:
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

    Ha a telepítés befejezése után az ügynök nem indul el, ellenőrizze a naplókat a részletes hibaüzenetek megtekintéséhez. Windows on *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*és Linux rendszeren a */var/opt/azcmagent/log/himds.log*címen.

## <a name="verify-the-connection-with-azure-arc"></a>Az Azure Arc csatlakozásának ellenőrzése

Miután telepítette az ügynököt, és konfigurálta az Azure arc-kompatibilis kiszolgálókhoz való csatlakozáshoz, lépjen a Azure Portal, és ellenőrizze, hogy a kiszolgáló sikeresen csatlakozott-e. A gépet megtekintheti az [Azure Portalon](https://portal.azure.com).

![Sikeres kiszolgálói kapcsolatok](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Következő lépések

* A hibaelhárítási információ a [csatlakoztatott gép ügynökének hibaelhárítása című útmutatóban](troubleshoot-agent-onboard.md)található.

* Megtudhatja, hogyan kezelheti a gépet [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, lehetővé teszi a figyelést a virtuális [gépekkel Azure monitor](../../azure-monitor/insights/vminsights-enable-policy.md)és sok más további műveletet.

* További információ a [log Analytics-ügynökről](../../azure-monitor/platform/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics ügynökre akkor van szükség, ha az operációs rendszer és a számítási feladatok figyelésére szolgáló adatokat szeretné összegyűjteni, az Automation-runbookok vagy-szolgáltatásokkal, például a Update Managementekkel, vagy más Azure-szolgáltatások, például a [Azure Security Center](../../security-center/security-center-introduction.md)használatával