---
title: A Trend Micro Deep Security telepítése virtuális gépen
description: Ez a cikk bemutatja, hogyan telepítheti és konfigurálhatja a Trend Micro Security szolgáltatást a klasszikus üzembe helyezési modellel létrehozott virtuális gépeken az Azure-ban.
author: axayjo
tags: azure-service-management
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/20/2018
ms.author: akjosh
ms.openlocfilehash: bed1d567aa48a11c01ae952e6a4a2028e260e6e0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288523"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>A Trend Micro Deep Security telepítése és konfigurálása Windows rendszerű virtuális gépen

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
Ez a cikk bemutatja, hogyan telepítheti és konfigurálhatja a Trend Micro Deep Security szolgáltatást a Windows Servert futtató új vagy meglévő virtuális gépen (VM). A fokozott biztonság, mint a szolgáltatás kártevők elleni védelmet, tűzfalat, behatolás-megelőzési rendszereket és integritás-figyelést tartalmaz.

Az ügyfél biztonsági bővítményként települ a virtuálisgép-ügynök használatával. Egy új virtuális gépen telepítse a Deep Security ügynököt, mivel a Azure Portal automatikusan létrehozza a virtuálisgép-ügynököt.

Előfordulhat, hogy a Azure Portal, az Azure CLI vagy a PowerShell használatával létrehozott meglévő virtuális gép nem rendelkezik virtuálisgép-ügynökkel. Egy meglévő virtuális gép esetében, amely nem rendelkezik virtuálisgép-ügynökkel, először le kell töltenie és telepítenie kell azt. Ez a cikk mindkét helyzetet magában foglalja.

Ha a Trend Micro egy aktuális előfizetéssel rendelkezik egy helyszíni megoldáshoz, használhatja az Azure-beli virtuális gépek védelméhez. Ha még nem ügyfél, regisztrálhat egy próbaverziós előfizetésre. A megoldással kapcsolatos további információkért tekintse meg a Trend Micro blog post Microsoft Azure virtuálisgép- [ügynök bővítményt a Deep Security](https://go.microsoft.com/fwlink/p/?LinkId=403945)szolgáltatáshoz.

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>A mély biztonsági ügynök telepítése új virtuális gépre

A [Azure Portal](https://portal.azure.com) lehetővé teszi a Trend Micro biztonsági bővítmény telepítését, ha a **piactéren** rendszerkép használatával hozza létre a virtuális gépet. Ha egyetlen virtuális gépet hoz létre, a portál használatával egyszerűen adhat hozzá védelmet a Trend Micro-ből.

A **piactéren** egy bejegyzés használatával megnyílik egy varázsló, amely segít a virtuális gép beállításában. A Trend Micro biztonsági bővítmény telepítéséhez a varázsló harmadik paneljén a **Beállítások** panelt kell használni.  Általános útmutatásért lásd: [Windows rendszerű virtuális gép létrehozása a Azure Portalban](../windows/quick-create-portal.md).

A varázsló **Beállítások** paneljén hajtsa végre a következő lépéseket:

1. Kattintson a **bővítmények**, majd a bővítmény **hozzáadása** elemre a következő ablaktáblán.

   ![A bővítmény hozzáadásának megkezdése][1]

2. Az **új erőforrás** ablaktáblán válassza a **Deep Security Agent** lehetőséget. A Deep Security Agent ablaktáblán kattintson a **Létrehozás**elemre.

   ![Mélyreható biztonsági ügynök azonosítása][2]

3. Adja meg a **bérlő azonosítóját** és a **bérlő aktiválási jelszavát** a kiterjesztéshez. Szükség esetén megadhat egy **biztonsági szabályzat azonosítóját**. Ezután kattintson az **OK** gombra az ügyfél hozzáadásához.

   ![Adja meg a bővítmény részleteit][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>A mély biztonsági ügynök telepítése meglévő virtuális gépre
Az ügynök meglévő virtuális gépen való telepítéséhez a következő elemek szükségesek:

* A Azure PowerShell modul, a 0.8.2 vagy újabb verziója telepítve van a helyi számítógépen. A telepített Azure PowerShell verziószámát a **Get-Module Azure | Format-Table Version** paranccsal tudja megtekinteni. Útmutatást és a legújabb verzióra mutató hivatkozást a [Azure PowerShell telepítése és konfigurálása](/powershell/azure/)című témakörben talál. Jelentkezzen be az Azure-előfizetésbe a használatával `Add-AzureAccount` .
* A virtuális gépre telepített virtuálisgép-ügynök.

Először ellenőrizze, hogy a virtuálisgép-ügynök már telepítve van-e. Adja meg a felhőalapú szolgáltatás nevét és a virtuális gép nevét, majd futtassa a következő parancsokat egy rendszergazda szintű Azure PowerShell parancssorban. Cserélje le az idézőjelek közé eső összes karaktert, beleértve a < és a > karaktereket is.

```azurepowershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Ha nem ismeri a Cloud Service-t és a virtuális gépet, futtassa a **Get-AzureVM** parancsot a jelenlegi előfizetésben lévő összes virtuális gép adatainak megjelenítéséhez.

Ha a **Write-Host** parancs **igaz**értéket ad vissza, a rendszer telepíti a virtuálisgép-ügynököt. Ha **hamis**értéket ad vissza, tekintse meg az utasításokat és a letöltésre mutató hivatkozást az Azure [-beli VM-ügynök és-bővítmények – 2. rész](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Ha a virtuálisgép-ügynök telepítve van, futtassa ezeket a parancsokat.

```azurepowershell
$Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM
```

## <a name="next-steps"></a>További lépések
Néhány percet vesz igénybe, amíg az ügynök elindul a telepítéskor. Ezt követően aktiválnia kell a mély biztonságot a virtuális gépen, hogy azt egy Deep Security Manager kezelje. További útmutatásért tekintse meg a következő cikkeket:

* A trendről szóló cikk a megoldásról, [az azonnali Felhőbeli biztonságról Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* Egy [minta Windows PowerShell-parancsfájl](https://go.microsoft.com/fwlink/?LinkId=404100) a virtuális gép konfigurálásához
* A minta [utasításai](https://go.microsoft.com/fwlink/?LinkId=404099)

## <a name="additional-resources"></a>További források
[Bejelentkezés Windows Servert futtató virtuális gépre]

[Azure-beli virtuálisgép-bővítmények és-funkciók]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Bejelentkezés Windows Servert futtató virtuális gépre]:../windows/classic/connect-logon.md
[Azure-beli virtuálisgép-bővítmények és-funkciók]: features-windows.md
