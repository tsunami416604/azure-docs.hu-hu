---
title: A Trend Micro Deep Security telepítése virtuális gépre
description: Ez a cikk ismerteti, hogyan telepítheti és konfigurálhatja a Trend Micro biztonsági egy virtuális gép az Azure-ban a klasszikus üzembe helyezési modell használatával létrehozott.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 04/20/2018
ms.author: akjosh
ms.openlocfilehash: cffd2eab3a616b4d16d847d0f2e1a26655f40459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919923"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>A Trend Micro Deep Security szolgáltatásként való telepítése és konfigurálása Windows virtuális gépen

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
Ez a cikk bemutatja, hogyan telepítheti és konfigurálhatja a Trend Micro Deep Security szolgáltatást egy Windows Server rendszert futtató új vagy meglévő virtuális gépen (VM). A Deep Security as a Service kártevőirtó védelmet, tűzfalat, behatolásmegelőző rendszert és integritásfigyelést tartalmaz.

Az ügyfél a virtuális gép ügynökén keresztül biztonsági bővítményként van telepítve. Egy új virtuális gépen telepíti a Deep Security Agent, a virtuális gép ügynök e virtuális gép automatikusan létrehozza az Azure Portalon.

Az Azure Portal, az Azure CLI vagy a PowerShell használatával létrehozott meglévő virtuális gép előfordulhat, hogy nem rendelkezik virtuálisgép-ügynökkel. Egy meglévő virtuális gép, amely nem rendelkezik a virtuálisgép-ügynök, először le kell töltenie és telepítenie kell. Ez a cikk mindkét helyzetet ismerteti.

Ha rendelkezik egy aktuális előfizetéstrend Micro egy helyszíni megoldás, használhatja az Azure virtuális gépek védelme. Ha még nem ügyfél, regisztrálhat egy próba-előfizetésre. A megoldásról további információt a Trend Micro blogbejegyzésében, a [Microsoft Azure VM Agent Extension for Deep Security című](https://go.microsoft.com/fwlink/p/?LinkId=403945)témakörben talál.

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>A deep security ügynök telepítése új virtuális gépre

Az [Azure Portalon](https://portal.azure.com) telepítheti a Trend Micro biztonsági bővítményt, ha a **Piactérről** egy lemezképet használ a virtuális gép létrehozásához. Ha egyetlen virtuális gépet hoz létre, a portál használatával egyszerűen adhat védelmet a Trend Micro-tól.

A **Piactér** egy bejegyzésének használatával megnyílik egy varázsló, amely segít a virtuális gép beállításában. A **Beállítások** panelen, a varázsló harmadik paneljén telepítheti a Trend Micro biztonsági bővítményt.  Általános tudnivalók: [Hozzon létre egy Windows rendszert futtató virtuális gépet az Azure Portalon.](../windows/classic/tutorial.md)

Amikor a varázsló **Beállítások** paneljéhez ért, tegye a következőket:

1. Kattintson **a Bővítmények**menü szomszédos ablaktáblájára, majd a Bővítmény **hozzáadása** parancsra.

   ![A bővítmény hozzáadásának megkezdése][1]

2. Az **Új erőforrás** ablaktáblán válassza a Mély **biztonsági ügynök** lehetőséget. A Deep Security Agent ablaktáblán kattintson a **Létrehozás gombra.**

   ![Deep Security Agent azonosítása][2]

3. Adja meg a **bővítmény bérlői azonosítóját** és **bérlői aktiválási jelszavát.** Tetszés szerint megadhat **biztonsági házirend-azonosítót.** Ezután kattintson az **OK** gombra az ügyfél hozzáadásához.

   ![A bővítmény részleteinek megadása][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>A deep security ügynök telepítése meglévő virtuális gépre
Az ügynök meglévő virtuális gépre való telepítéséhez a következő elemekre van szükség:

* Az Azure PowerShell-modul, 0.8.2-es vagy újabb verziójú, telepítve a helyi számítógépen. Az Azure PowerShell telepített verziójának ellenőrzése a **Get-Module azure | format-table version** paranccsal. Az azure [PowerShell telepítése és konfigurálása](/powershell/azure/overview)című témakörben talál utasításokat és a legújabb verzióra mutató hivatkozást. Jelentkezzen be Azure-előfizetéséhez a használatával. `Add-AzureAccount`
* A virtuális gép a célvirtuális gépen telepített virtuálisgép-ügynök.

Először ellenőrizze, hogy a virtuális gép ügynöke már telepítve van.First, verify that the VM Agent is already installed. Töltse ki a felhőszolgáltatás nevét és a virtuális gép nevét, majd futtassa a következő parancsokat egy rendszergazdai szintű Azure PowerShell parancssorban. Cseréljeki mindent az idézőjelek között, beleértve a < és > karaktereket is.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Ha nem ismeri a felhőszolgáltatás és a virtuális gép nevét, futtassa a **Get-AzureVM-et,** és jelenítse meg ezeket az információkat az aktuális előfizetésében szereplő összes virtuális géphez.

Ha a **write-host** parancs **igaz**értéket ad vissza, a virtuálisgép-ügynök telepítve van. Ha **hamis**értéket ad vissza, tekintse meg az utasításokat és a letöltésre mutató hivatkozást az Azure blogbejegyzésben, a [VM Agent and Extensions – Part 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Ha a virtuálisgép-ügynök telepítve van, futtassa ezeket a parancsokat.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>További lépések
Az ügynök telepítésekor néhány percet vesz igénybe. Ezt követően aktiválnia kell a Deep Security-t a virtuális gépen, hogy a Deep Security Manager kezelni tudja. További utasításokat az alábbi cikkekben talál:

* A Trend cikke erről a megoldásról, [a Microsoft Azure azonnali felhőalapú biztonsága](https://go.microsoft.com/fwlink/?LinkId=404101)
* Minta [Windows PowerShell-parancsfájl](https://go.microsoft.com/fwlink/?LinkId=404100) a virtuális gép konfigurálásához
* [A](https://go.microsoft.com/fwlink/?LinkId=404099) mintára vonatkozó utasítások

## <a name="additional-resources"></a>További források
[Bejelentkezés Windows Server rendszert futtató virtuális gépre]

[Az Azure VM-bővítmények és -szolgáltatások]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Bejelentkezés Windows Server rendszert futtató virtuális gépre]:../windows/classic/connect-logon.md
[Az Azure VM-bővítmények és -szolgáltatások]: https://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
