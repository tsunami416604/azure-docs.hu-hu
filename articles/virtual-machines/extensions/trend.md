---
title: Trend Micro Deep Security telepítése a virtuális gép |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan security telepítése és konfigurálása a Trend Micro Azure-beli klasszikus üzemi modellel létrehozott virtuális gépen.
services: virtual-machines-windows
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: roiyz
ms.openlocfilehash: 7cddbce56dc136b706bc55c19e3ad700ef13073f
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413765"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>A Trend Micro Deep Security szolgáltatásként való telepítése és konfigurálása windowsos virtuális gépen
[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
Ez a cikk bemutatja, hogyan telepítése és konfigurálása a Trend Micro Deep Security szolgáltatás egy új vagy meglévő virtuális gépen (VM) a Windows Server operációs rendszert futtató. Deep Security szolgáltatásként is magában foglalja a kártevők elleni védelem, tűzfal, olyan behatolásvédelmi rendszer és a fájlintegritási monitorozás.

Az ügyfél biztonsági bővítményeként keresztül a Virtuálisgép-ügynök telepítve van. Új virtuális gépet telepíti a Deep Security Agent, a Virtuálisgép-ügynök automatikusan hozza létre az Azure Portalon.

Egy meglévő virtuális gép létrehozása az Azure portal, az Azure CLI vagy a PowerShell használatával lehet, hogy rendelkezik egy Virtuálisgép-ügynök. Meglévő virtuális gép, amelyen a Virtuálisgép-ügynök nincs telepítve letöltése és telepítése, először szüksége. Ez a cikk ismerteti mind helyzetekben.

Ha egy helyszíni megoldás a Trend Micro érvényes előfizetéssel rendelkezik, használhatja az Azure-beli virtuális gépek védelme érdekében. Ha még nem vagyunk ügyfél, akkor regisztráljon egy próba-előfizetést. Ez a megoldás kapcsolatos további információkért lásd: a Trend Micro blogbejegyzést [a Microsoft Azure VM Agent bővítmény a Deep Security](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>A Deep Security Agent telepítése egy új virtuális Gépre

A [az Azure portal](http://portal.azure.com) lehetővé teszi a Trend Micro biztonsági bővítmény telepítését, a lemezkép használatakor a **Marketplace** a virtuális gép létrehozásához. Ha egy virtuális gépen hoz létre, könnyen adhat védelmet a Trend Micro a portál használatával.

Használatával egy bejegyzést a **Marketplace** megnyílik egy varázsló, amely segítséget nyújt a virtuális gép beállítása. Használja a **beállítások** panelen, a harmadik panelen, a varázsló, a Trend Micro biztonsági bővítmény telepítéséhez.  Általános útmutatás: [létrehozása az Azure Portalon Windows rendszerű virtuális gép](../windows/classic/tutorial.md).

Amikor juthat el a **beállítások** varázsló panelen tegye a következőket:

1. Kattintson a **bővítmények**, majd kattintson a **bővítmény hozzáadása** a következő oldalon.

   ![Indítsa el a bővítményt][1]

2. Válassza ki **Deep Security Agent** a a **új erőforrás** ablaktáblán. Kattintson a Deep Security Agent ablaktáblában **létrehozás**.

   ![A Deep Security Agent azonosítása][2]

3. Adja meg a **Bérlőazonosító** és **Bérlőaktivációs jelszó** a bővítmény. Igény szerint megadhat egy **biztonsági házirend-azonosító**. Kattintson a **OK** hozzáadni az ügyfél.

   ![Adja meg a bővítmény részletei][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>A meglévő virtuális gép a Deep Security Agent telepítése
Telepítse az ügynököt egy meglévő virtuális Gépet, a következő elemeket kell:

* Az Azure PowerShell-modul, a 0.8.2 verzió vagy újabb, telepített, a helyi számítógépen. Azure PowerShell használatával telepített verziójának ellenőrizheti a **Get-Module azure |} táblázat formázása verzió** parancsot. Útmutatás és a egy hivatkozást a legújabb verzióra: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview). Az Azure-előfizetés használatával jelentkezzen be `Add-AzureAccount`.
* A Virtuálisgép-ügynök telepítve van a cél virtuális gépen.

Először is győződjön meg arról, hogy a Virtuálisgép-ügynök már telepítve van. Töltse ki a felhőszolgáltatás neve és a virtuális gép nevét, és futtassa a következő parancsokat egy rendszergazda szintű Azure PowerShell parancssorban. Cserélje le a mindent, ami az ajánlatokat, többek között a < és > karakterek.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Ha nem ismeri a felhőszolgáltatás és a virtuális gép nevét, futtassa **Get-AzureVM** , hogy a virtuális gépek adatainak megjelenítése az aktuális előfizetésben.

Ha a **write-host** értéket ad vissza a parancs **igaz**, a Virtuálisgép-ügynök telepítve van. Ha a visszaadott érték **false (hamis)**, tekintse meg az utasításokat, és az Azure ebben a blogbejegyzésben a letöltésére mutató hivatkozást [Virtuálisgép-ügynök és -bővítmények – 2. rész](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Ha a Virtuálisgép-ügynök van telepítve, futtassa a következő parancsokat.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>További lépések
Az ügynök indítsa el a telepítés néhány percet vesz igénybe. Ezt követően kell aktiválnia a virtuális gépen Deep Security, így azt a Deep Security Manager kezelhetők. További útmutatást a következő cikkekben talál:

* Trend a cikk arról, hogy ez a megoldás [Instant-On Cloud Security for Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
* A [Windows PowerShell-mintaparancsfájl](http://go.microsoft.com/fwlink/?LinkId=404100) a virtuális gép konfigurálása
* [Utasítások](http://go.microsoft.com/fwlink/?LinkId=404099) a minta

## <a name="additional-resources"></a>További források
[Hogyan lehet bejelentkezni a Windows Server rendszerű virtuális gép]

[Az Azure Virtuálisgép-bővítmények és szolgáltatások]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Hogyan lehet bejelentkezni a Windows Server rendszerű virtuális gép]:../windows/classic/connect-logon.md
[Az Azure Virtuálisgép-bővítmények és szolgáltatások]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
