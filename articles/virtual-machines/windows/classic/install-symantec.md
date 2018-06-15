---
title: Symantec Endpoint Protection telepítése a Windows Azure-ban |} Microsoft Docs
description: Megtudhatja, hogyan telepítse és konfigurálja a Symantec Endpoint Protection biztonsági bővítményt a klasszikus telepítési modellel létrehozott egy új vagy meglévő Azure virtuális gépen.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: iainfou
ms.openlocfilehash: bd96cb975bfc30b2561a98a950a9dd3fc060fa54
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30913341"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>A Symantec Endpoint Protection telepítése és konfigurálása windowsos virtuális gépen
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ez a cikk bemutatja, hogyan telepítse és konfigurálja a Symantec Endpoint Protection-ügyfél egy meglévő virtuális gép (VM) Windows Server rendszerű. A teljes ügyfél szolgáltatásokat, például vírus- és kémprogram-védelmi-, tűzfal, és behatolás megelőzési tartalmazza. Az ügyfél biztonsági bővítményként segítségével a Virtuálisgép-ügynök telepítve van.

Ha egy helyszíni megoldás a Symantec egy meglévő előfizetéshez, használhatja az Azure virtuális gépek védelmét. Ha nem az ügyfél még, regisztrálhat egy próba-előfizetésre. Információ a megoldásról további információkért lásd: [Symantec Endpoint Protection a Microsoft Azure platformon][Symantec]. Ezen a lapon olyan licencelési információkat és útmutatást az ügyfél telepítéséhez, ha Ön már egy Symantec-ügyfél mutató hivatkozásokat is tartalmaz.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Symantec Endpoint Protection telepítése egy meglévő virtuális gépen
Mielőtt elkezdené, a következők szükségesek:

* Az Azure PowerShell modul, a 0.8.2 verzió vagy újabb verzióját, a munkahelyi számítógép. Ellenőrizheti, hogy az Azure PowerShell, a telepített verzióját az **Get-Module azure |} táblázat formázása verzió** parancsot. Útmutatás és egy hivatkozást a legújabb verzióra: [telepítése és konfigurálása az Azure PowerShell][PS]. Jelentkezzen be az Azure-előfizetés használata `Add-AzureAccount`.
* A Virtuálisgép-ügynök az Azure virtuális gépet futtat.

Először is győződjön meg arról, hogy a Virtuálisgép-ügynök már telepítve van a virtuális gépen. Adja meg a felhőszolgáltatás neve és a virtuális gép nevét, és futtassa a következő parancsokat egy rendszergazda szintű Azure PowerShell parancssorban. Cserélje le a mindent, ami az ajánlatokat, beleértve a < és > karakter.

> [!TIP]
> Ha nem ismeri a felhőszolgáltatás és a virtuális gép nevét, futtassa **Get-AzureVM** listát az összes virtuális gépet a jelenlegi előfizetés nevét.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Ha a **write-host** parancs megjeleníti **igaz**, a Virtuálisgép-ügynök telepítve van. Ha megjeleníti **hamis**, tekintse meg az utasításokat és az Azure blogbejegyzésben letöltési mutató hivatkozást [ügynök és Virtuálisgép-bővítmények – 2. rész][Agent].

Ha a Virtuálisgép-ügynök telepítve van, futtassa az alábbi parancsokat a Symantec Endpoint Protection-ügynök telepítéséhez.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Ellenőrizheti, hogy a Symantec biztonsági bővítménye telepítve van-e, és naprakész:

1. Jelentkezzen be a virtuális gép. Útmutatásért lásd: [jelentkezzen be a virtuális gép futó Windows Server hogyan][Logon].
2. A Windows Server 2008 R2, kattintson a **Start > Symantec Endpoint Protection**. Írja be a Windows Server 2012 vagy Windows Server 2012 R2-ben a kezdőképernyőről **Symantec**, és kattintson a **Symantec Endpoint Protection**.
3. Az a **állapot** lapján a **állapot-Symantec Endpoint Protection** ablak, alkalmazza a frissítéseket, vagy szükség esetén indítsa újra.

## <a name="additional-resources"></a>További források
[Jelentkezzen be a Windows Server rendszerű virtuális gép hogyan][Logon]

[Az Azure Virtuálisgép-bővítmények és szolgáltatások][Ext]

<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493
