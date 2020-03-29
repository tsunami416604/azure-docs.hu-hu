---
title: A Symantec Endpoint Protection telepítése Windows virtuális gépre az Azure-ban
description: Megtudhatja, hogyan telepítheti és konfigurálhatja a Symantec Endpoint Protection biztonsági bővítményt egy új vagy meglévő Azure virtuális gépen, amelyet a klasszikus üzembe helyezési modellel hoztak létre.
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
ms.author: akjosh
ms.openlocfilehash: 63f9441d4df9551405c2ab2bf8c0c67d7de5753c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919906"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>A Symantec Endpoint Protection telepítése és konfigurálása Windows virtuális gépen
[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Az Azure két különböző üzembe helyezési modellt rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Erőforrás-kezelő és a Klasszikus.](../../azure-resource-manager/management/deployment-models.md) Ez a cikk a klasszikus üzembe helyezési modell használatával ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ez a cikk bemutatja, hogyan telepítheti és konfigurálhatja a Symantec végpontvédelmi ügyfelet egy Windows Server rendszert futtató meglévő virtuális gépen.This article this cikk you you to install and configure the Symantec Endpoint Protection client on an existing virtual machine (VM) running Windows Server. Ez a teljes ügyfél olyan szolgáltatásokat tartalmaz, mint a vírus- és kémprogram-védelem, a tűzfal és a behatolás-megelőzés. Az ügyfél a virtuális gép ügynöke által telepített biztonsági bővítményként.

Ha rendelkezik egy meglévő előfizetéssel a Symantectől egy helyszíni megoldáshoz, használhatja az Azure virtuális gépeinek védelmére. Ha még nem ügyfél, regisztrálhat egy próba-előfizetésre. A megoldásról további információt a [Symantec Endpoint Protection (Symantec Végpontvédelem a Microsoft Azure platformján) című témakörben][Symantec]talál. Ez az oldal hivatkozásokat tartalmaz a licencelési információkra és az ügyfél telepítésére vonatkozó utasításokra is, ha Ön már Symantec ügyfél.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>A Symantec Végpontvédelem telepítése meglévő virtuális gépre
Mielőtt elkezdené, a következőkre van szüksége:

* Az Azure PowerShell-modul, 0.8.2-es vagy újabb verzió, a munkahelyi számítógépen. Ellenőrizheti az Azure PowerShell telepített verzióját a **Get-Module azure | format-table version** paranccsal. Az azure [PowerShell telepítése és konfigurálása][PS]című témakörben talál utasításokat és a legújabb verzióra mutató hivatkozást. Jelentkezzen be Azure-előfizetéséhez a használatával. `Add-AzureAccount`
* Az Azure virtuális gépen futó virtuális gép.

Először ellenőrizze, hogy a virtuális gép ügynöke már telepítve van-e a virtuális gépen. Töltse ki a felhőszolgáltatás nevét és a virtuális gép nevét, majd futtassa a következő parancsokat egy rendszergazdai szintű Azure PowerShell parancssorban. Cseréljeki mindent az idézőjelek között, beleértve a < és > karaktereket is.

> [!TIP]
> Ha nem ismeri a felhőszolgáltatás és a virtuális gép nevét, futtassa a **Get-AzureVM-et** az aktuális előfizetésben szereplő összes virtuális gép nevének listázásához.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Ha a **write-host** parancs **true**parancsot jeleníti meg, a virtuálisgép-ügynök telepítve van. Ha **hamis**értéket jelenít meg, tekintse meg az utasításokat és a letöltésre mutató hivatkozást az Azure [blogbejegyzésvirtuálisgép-ügynök és -bővítmények – 2.][Agent]

Ha a virtuálisgép-ügynök telepítve van, futtassa ezeket a parancsokat a Symantec Endpoint Protection ügynök telepítéséhez.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Annak ellenőrzése, hogy a Symantec biztonsági bővítmény telepítve van-e, és naprakész-e:

1. Jelentkezzen be a virtuális gépre. További információt a [Bejelentkezés Windows Server rendszert futtató virtuális gépre][Logon].
2. Windows Server 2008 R2 esetén kattintson **a Symantec endpoint protection > indítása parancsra.** Windows Server 2012 vagy Windows Server 2012 R2 esetén a kezdőképernyőn írja be a **Symantec**, majd a **Symantec végpontvédelem (Symantec Endpoint Protection ) ( Symantec Endpoint Protection ) ( Symantec Végpontvédelem ) című szöveget.**
3. A **Status-Symantec Endpoint Protection** ablak **Állapot** lapján alkalmazza a frissítéseket, vagy szükség esetén indítsa újra az számítógépet.

## <a name="additional-resources"></a>További források
[Bejelentkezés Windows Server rendszert futtató virtuális gépre][Logon]

[Az Azure VM-bővítmények és -szolgáltatások][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
