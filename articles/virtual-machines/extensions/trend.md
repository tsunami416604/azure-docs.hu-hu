---
title: A virtuális gép telepítése a Trend Micro részletes biztonsági |} Microsoft Docs
description: A cikkből megtudhatja, hogyan kell telepíteni, és a Trend Micro biztonságának konfigurálása a klasszikus telepítési modellt az Azure-ban létrehozott egy virtuális gépen.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
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
ms.author: danis
ms.openlocfilehash: 6098d310bcc6fe5df2378688b78277fc7e4b16bc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942675"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>A Trend Micro Deep Security szolgáltatásként való telepítése és konfigurálása windowsos virtuális gépen
[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
Ez a cikk bemutatja, hogyan telepítse és konfigurálja a Trend Micro mély biztonsági egy új vagy meglévő virtuális gépen (VM) Windows Server rendszert futtató szolgáltatásként. Szolgáltatásként mély biztonsági magában foglalja a kártevők elleni védelem, a tűzfal, egy behatolás elleni védelmi rendszerének és integritásának ellenőrzésére.

Az ügyfél biztonsági bővítményként keresztül a Virtuálisgép-ügynök telepítve van. Új virtuális gépen, az mély biztonsági ügynök telepítése, mert a Virtuálisgép-ügynök automatikusan hozza létre az Azure-portálon.

Lehet, hogy egy meglévő virtuális gép létrehozása az Azure-portált használja, az Azure parancssori felület vagy a PowerShell nincs Virtuálisgép-ügynök. Egy meglévő virtuális gépen, amelyen a Virtuálisgép-ügynök nem rendelkezik akkor kell letöltheti és telepítheti azt. Ez a cikk mindkét eseteire vonatkozik.

Ha egy helyszíni megoldás a Trend Micro aktuális előfizetést, használhatja az Azure virtuális gépek védelme érdekében. Ha nem az ügyfél még, regisztrálhat egy próba-előfizetésre. Információ a megoldásról további információkért lásd a következő Trend Micro blogbejegyzésben [Microsoft Azure virtuális gép ügynök bővítmény a mély biztonsági](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Az átfogó biztonsági ügynök telepítése egy új virtuális gép

A [Azure-portálon](http://portal.azure.com) lehetővé teszi, hogy a Trend Micro biztonsági bővítményének telepítése, a lemezkép használatakor az **piactér** a virtuális gép létrehozásához. Ha egyetlen virtuális gép hoz létre, a portál használatával egyszerű módja védelem hozzáadása a Trend Micro.

Az egyik bejegyzésének használatával a **piactér** megnyit egy varázslót, amely segítséget nyújt a virtuális gép beállítása. Használja a **beállítások** panelen, a harmadik panelen, a varázsló a Trend Micro biztonsági bővítmény telepítésére.  Általános útmutatás: [hozzon létre egy olyan virtuális géphez a Windows Azure-portálon](../windows/classic/tutorial.md).

Amikor elér a **beállítások** varázsló panelen tegye a következőket:

1. Kattintson a **bővítmények**, majd kattintson a **felvenni a bővítményt** a következő oldalon.

   ![A bővítmény felvételének megkezdése][1]

2. Válassza ki **mély biztonsági ügynök** a a **új erőforrás** ablaktáblán. A részletes biztonsági ügynök ablaktábláján kattintson **létrehozása**.

   ![A részletes biztonsági megbízott azonosítása][2]

3. Adja meg a **bérlői azonosító** és **bérlői aktiválási jelszó** a bővítmény. Másik lehetőségként megadhat egy **biztonsági házirend-azonosító**. Kattintson a **OK** ügyfél.

   ![Adja meg a bővítmény részletei][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>A részletes biztonsági ügynök telepíthető egy meglévő virtuális Gépen
Az ügynök telepítése egy meglévő virtuális Gépre, az alábbi elemek szükségesek:

* Az Azure PowerShell modul, a 0.8.2 verzió vagy újabb, a helyi számítógépen. Ellenőrizheti, hogy az Azure PowerShell használatával telepített verzióját az **Get-Module azure |} táblázat formázása verzió** parancsot. Útmutatás és egy hivatkozást a legújabb verzióra: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview). Jelentkezzen be az Azure-előfizetés használata `Add-AzureAccount`.
* A Virtuálisgép-ügynök telepítve legyen a cél virtuális gépen.

Először is győződjön meg arról, hogy a Virtuálisgép-ügynök telepítve van. Adja meg a felhőszolgáltatás neve és a virtuális gép nevét, és futtassa a következő parancsokat egy rendszergazda szintű Azure PowerShell parancssorban. Cserélje le a mindent, ami az ajánlatokat, beleértve a < és > karakter.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Ha nem ismeri a felhőszolgáltatás és a virtuális gép nevét, futtassa **Get-AzureVM** megjelenítheti, hogy az összes virtuális gép az aktuális előfizetésben.

Ha a **write-host** parancs beolvasása **igaz**, a Virtuálisgép-ügynök telepítve van. Ha a visszaadott érték **hamis**, tekintse meg az utasításokat és az Azure blogbejegyzésben letöltési mutató hivatkozást [ügynök és Virtuálisgép-bővítmények – 2. rész](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Ha a Virtuálisgép-ügynök telepítve van, futtassa az alábbi parancsokat.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>További lépések
Az ügynök arra, hogy elindítsa a telepítés néhány percet vesz igénybe. Ezt követően részletes biztonsági aktiválása a virtuális gépen, így kezelhető egy átfogó biztonsági Manager kell. Tekintse meg a további utasításokat a következő cikkeket:

* Információ a megoldásról a trend a cikk [Instant-On Cloud Security a Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
* A [Windows PowerShell-mintaparancsfájl](http://go.microsoft.com/fwlink/?LinkId=404100) a virtuális gép konfigurálásához
* [Útmutatás](http://go.microsoft.com/fwlink/?LinkId=404099) a minta

## <a name="additional-resources"></a>További források
[Bejelentkezés a Windows Server rendszerű virtuális géphez]

[Az Azure Virtuálisgép-bővítmények és szolgáltatások]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Bejelentkezés a Windows Server rendszerű virtuális géphez]:../windows/classic/connect-logon.md
[Az Azure Virtuálisgép-bővítmények és szolgáltatások]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
