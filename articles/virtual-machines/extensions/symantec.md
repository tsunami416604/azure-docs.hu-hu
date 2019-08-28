---
title: A Symantec Endpoint Protection telepítése Azure-beli Windows rendszerű virtuális gépen | Microsoft Docs
description: Megtudhatja, hogyan telepítheti és konfigurálhatja a Symantec Endpoint Protection biztonsági bővítményt egy olyan új vagy meglévő Azure virtuális gépen, amely a klasszikus üzemi modellel lett létrehozva.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 03/31/2017
ms.author: roiyz
ms.openlocfilehash: e6e81732c0e25bc46d28172cdf085f6c3f457ca8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084251"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>A Symantec Endpoint Protection telepítése és konfigurálása windowsos virtuális gépen
> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembe helyezési modell használatát ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ez a cikk bemutatja, hogyan telepítheti és konfigurálhatja a Symantec Endpoint Protection-ügyfelet egy Windows Server rendszert futtató meglévő virtuális gépen (VM). Ez a teljes ügyfél olyan szolgáltatásokat tartalmaz, mint például a vírus-és kémprogram-védelem, a tűzfal és a behatolás-megelőzés. A-ügyfél biztonsági bővítményként van telepítve a virtuálisgép-ügynök használatával.

Ha a Symantec meglévő előfizetése egy helyszíni megoldáshoz tartozik, használhatja az Azure-beli virtuális gépeket. Ha még nem ügyfél, regisztrálhat egy próbaverziós előfizetésre. További információ erről a megoldásról: [Symantec Endpoint Protection a Microsoft Azure platformon][Symantec]. Ezen a lapon a licencelési információkra mutató hivatkozások és a-ügyfél telepítésére vonatkozó utasítások is szerepelnek, ha Ön már Symantec-Ügyfél.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>A Symantec Endpoint Protection telepítése meglévő virtuális gépre
A Kezdés előtt a következőkre lesz szüksége:

* A Azure PowerShell modul, 0.8.2 vagy újabb verzió a munkahelyi számítógépen. A **Get-Module Azure | Format-Table Version** paranccsal megtekintheti Azure PowerShell telepített verzióját. Útmutatást és a legújabb verzióra mutató hivatkozást a [Azure PowerShell telepítése és konfigurálása][PS]című témakörben talál. Jelentkezzen be az Azure-előfizetésbe a használatával `Add-AzureAccount`.
* Az Azure-beli virtuális gépen futó virtuálisgép-ügynök.

Először ellenőrizze, hogy a virtuálisgép-ügynök már telepítve van-e a virtuális gépen. Adja meg a felhőalapú szolgáltatás nevét és a virtuális gép nevét, majd futtassa a következő parancsokat egy rendszergazda szintű Azure PowerShell parancssorban. Cserélje le az idézőjelek közé eső összes karaktert, beleértve a < és a > karaktereket is.

> [!TIP]
> Ha nem ismeri a Cloud Service-t és a virtuális gépek nevét, futtassa a **Get-AzureVM** parancsot a jelenlegi előfizetésében lévő összes virtuális gép nevének listázásához.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Ha a **Write-Host** parancs **igaz**értéket jelenít meg, a virtuálisgép-ügynök telepítve lesz. Ha a **Hamis értéket**jeleníti meg, tekintse meg az utasításokat, valamint a letöltésre mutató hivatkozást az Azure-blogbejegyzésben a [VM-ügynök és-bővítmények – 2. rész][Agent].

Ha a virtuálisgép-ügynök telepítve van, futtassa a következő parancsokat a Symantec Endpoint Protection-ügynök telepítéséhez.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Annak ellenőrzéséhez, hogy telepítve van-e a Symantec biztonsági bővítménye, és hogy naprakész-e:

1. Jelentkezzen be a virtuális gépre. Útmutatásért lásd: [Hogyan jelentkezhet be a Windows Servert futtató virtuális gépre][Logon].
2. Windows Server 2008 R2 esetén kattintson a **Start > Symantec Endpoint Protection**lehetőségre. A Windows Server 2012 vagy a Windows Server 2012 R2 esetében a kezdőképernyőn írja be a **Symantec**parancsot, majd kattintson a **Symantec Endpoint Protection**elemre.
3. Az **állapot – Symantec Endpoint Protection** ablak **állapot** lapján frissítse a frissítéseket, vagy szükség esetén indítsa újra a rendszert.

## <a name="additional-resources"></a>További források
[Bejelentkezés Windows Servert futtató virtuális gépre][Logon]

[Azure-beli virtuálisgép-bővítmények és-funkciók][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
