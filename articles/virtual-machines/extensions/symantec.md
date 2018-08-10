---
title: A Symantec Endpoint Protection telepítése a Windows Azure-beli virtuális gép |} A Microsoft Docs
description: Ismerje meg, hogyan telepítse és konfigurálja a Symantec Endpoint Protection biztonsági bővítményt a klasszikus üzemi modellel létrehozott egy új vagy meglévő Azure virtuális gépen.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
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
ms.author: roiyz
ms.openlocfilehash: 455ca0ed1de5df3a184d900aeae286dfd5233a60
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716508"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>A Symantec Endpoint Protection telepítése és konfigurálása windowsos virtuális gépen
> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ez a cikk bemutatja, hogyan telepítése és a Symantec Endpoint Protection-ügyfél konfigurálása a Windows Server operációs rendszert futtató meglévő virtuális gépen (VM). A teljes ügyfél például a víruskereső és kémprogram-védelmi, tűzfal és a behatolás-megelőzési olyan szolgáltatást tartalmaz. Az ügyfél a Virtuálisgép-ügynök segítségével telepítve van egy biztonsági bővítményként.

Ha rendelkezik egy meglévő előfizetéshez, a Symantec egy helyszíni megoldás esetében, használhatja az Azure-beli virtuális gépek védelme érdekében. Ha még nem vagyunk ügyfél, akkor regisztráljon egy próba-előfizetést. Ez a megoldás kapcsolatos további információkért lásd: [a Symantec Endpoint Protection a Microsoft Azure platformon][Symantec]. Ezen a lapon a licencelési információk és utasítások telepíti az ügyfelet, ha Ön már egy Symantec-ügyfél mutató hivatkozásokat is tartalmaz.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>A Symantec Endpoint Protection telepítése egy meglévő virtuális gépen
Mielőtt elkezdené, a következők szükségesek:

* Az Azure PowerShell-modul, a 0.8.2 verzió vagy újabb, a munkahelyi számítógépen. A verziószám, amelyen telepítve van az Azure PowerShell a **Get-Module azure |} táblázat formázása verzió** parancsot. Útmutatás és a egy hivatkozást a legújabb verzióra: [telepítése és konfigurálása az Azure PowerShell][PS]. Az Azure-előfizetés használatával jelentkezzen be `Add-AzureAccount`.
* Az Azure virtuális gépen futó Virtuálisgép-ügynök.

Először is győződjön meg arról, hogy a Virtuálisgép-ügynök már telepítve van a virtuális gépen. Töltse ki a felhőszolgáltatás neve és a virtuális gép nevét, és futtassa a következő parancsokat egy rendszergazda szintű Azure PowerShell parancssorban. Cserélje le a mindent, ami az ajánlatokat, többek között a < és > karakterek.

> [!TIP]
> Ha nem ismeri a felhőszolgáltatás és a virtuális gép nevét, futtassa **Get-AzureVM** , listázza a nevüket, minden virtuális gép az aktuális előfizetésben.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Ha a **write-host** parancs megjeleníti **igaz**, a Virtuálisgép-ügynök telepítve van. Ha megjeleníti **hamis**, tekintse meg az utasításokat, és az Azure ebben a blogbejegyzésben a letöltésére mutató hivatkozást [Virtuálisgép-ügynök és -bővítmények – 2. rész][Agent].

Ha a Virtuálisgép-ügynök van telepítve, futtassa az alábbi parancsokat a Symantec Endpoint Protection-ügynök telepítése.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Arról, hogy a Symantec biztonsági bővítmény telepítve van-e, és naprakész:

1. Jelentkezzen be a virtuális gépet. Útmutatásért lásd: [bejelentkezés egy virtuális gépen futó Windows Server][Logon].
2. Kattintson a Windows Server 2008 R2 **Start > a Symantec Endpoint Protection**. Windows Server 2012 vagy Windows Server 2012 R2, a kezdőképernyőn írja be a következőt **Symantec**, és kattintson a **a Symantec Endpoint Protection**.
3. Az a **állapot** lapján a **állapot – a Symantec Endpoint Protection** ablakban, alkalmazza a frissítéseket, vagy szükség esetén indítsa újra.

## <a name="additional-resources"></a>További források
[Bejelentkezés a Windows Server rendszerű virtuális gép][Logon]

[Az Azure Virtuálisgép-bővítmények és szolgáltatások][Ext]

<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493
