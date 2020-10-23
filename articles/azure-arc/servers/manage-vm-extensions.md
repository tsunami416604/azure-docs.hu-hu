---
title: Virtuálisgép-bővítmények kezelése az Azure arc-kompatibilis kiszolgálókkal
description: Az Azure arc-kompatibilis kiszolgálók kezelhetik azokat a virtuálisgép-bővítmények központi telepítését, amelyek a telepítés utáni konfigurálást és az automatizálási feladatokat nem Azure-beli virtuális gépekkel is rendelkeznek.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: e9865761fd3e5897ee3f01cd3d6ca620d5ea2f4b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460886"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Virtuálisgép-bővítmények kezelése az Azure arc használatára képes kiszolgálókon

A virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Ha egy virtuális gépen például szoftver telepítésére, vírusvédelemre vagy egy szkript futtatására van szükség, erre felhasználható egy virtuálisgép-bővítmény.

Az Azure arc-kompatibilis kiszolgálók lehetővé teszik az Azure-beli virtuálisgép-bővítmények üzembe helyezését nem Azure-beli Windows-és Linux-alapú virtuális gépeken, a helyszíni, peremhálózati és más felhőalapú környezetek felügyeletének egyszerűsítését életciklusuk alapján. A virtuálisgép-bővítmények a következő módszerekkel kezelhetők a hibrid gépeken vagy az arc-kompatibilis kiszolgálók által felügyelt kiszolgálókon:

- A [Azure Portal](manage-vm-extensions-portal.md)
- Az [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager-sablonok](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Főbb előnyök

Az Azure arc-kompatibilis kiszolgálók virtuálisgép-bővítmények támogatása a következő főbb előnyöket nyújtja:

- [Azure Automation állapot konfigurációjának](../../automation/automation-dsc-overview.md) használatával központilag tárolhatja a konfigurációkat, és megtarthatja a DSC virtuálisgép-bővítményen keresztül engedélyezett hibrid csatlakoztatott gépek kívánt állapotát.

- Begyűjti a naplózási adatokat az elemzéshez [Azure monitor a naplókat](../../azure-monitor/platform/data-platform-logs.md) az log Analytics ügynök virtuálisgép-bővítményének használatával. Ez hasznos lehet a különböző forrásokból származó adatok összetett elemzésének végrehajtásához.

- A [Azure monitor for VMS](../../azure-monitor/insights/vminsights-overview.md)a elemzi a Windows-és Linux-alapú virtuális gépek teljesítményét, és figyeli a folyamatokat és a függőségeket más erőforrásokra és külső folyamatokra. Ez a Log Analytics ügynök és a függőségi ügynök virtuálisgép-bővítményeinek engedélyezésével érhető el.

- Parancsfájlokat tölthet le és futtathat a hibrid csatlakoztatott gépeken az egyéni szkriptek bővítmény használatával. Ez a bővítmény az üzembe helyezési konfiguráció, a Szoftvertelepítés vagy bármely egyéb konfigurációs vagy felügyeleti feladat után hasznos.

## <a name="availability"></a>Rendelkezésre állás

A VM-bővítmény funkció csak a [támogatott régiók](overview.md#supported-regions)listájában érhető el. Győződjön meg arról, hogy a számítógépe ezen régiók egyikében található.

## <a name="extensions"></a>Bővítmények

Ebben a kiadásban a következő virtuálisgép-bővítményeket támogatjuk a Windows és Linux rendszerű gépeken.

|Kiterjesztés |Operációs rendszer |Publisher |További információ |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Egyéni Windows-szkriptek bővítménye](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Windows PowerShell DSC-bővítmény](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics-ügynök |Windows |Microsoft. EnterpriseCloud. monitoring |[Log Analytics virtuálisgép-bővítmény a Windowshoz](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft függőségi ügynök | Windows |Microsoft.Compute | [Függőségi ügynök virtuálisgép-bővítménye Windows rendszerhez](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure. Extension |[Linux Custom script Extension 2. verzió](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft. OSTCExtensions |[PowerShell DSC-bővítmény Linux rendszerhez](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics-ügynök |Linux |Microsoft. EnterpriseCloud. monitoring |[A Linux rendszerhez készült virtuálisgép-bővítmény Log Analytics](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft függőségi ügynök | Linux |Microsoft.Compute | [Függőségi ügynök linuxos virtuálisgép-bővítménye](../../virtual-machines/extensions/agent-dependency-linux.md) |

Ha többet szeretne megtudni az Azure-beli csatlakoztatott gépi ügynök csomagról és a bővítmény ügynök összetevőjéről, tekintse meg az [ügynök áttekintése](agent-overview.md#agent-component-details)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció az előfizetés alábbi Azure-erőforrás-szolgáltatói függ:

- **Microsoft. HybridCompute**
- **Microsoft. GuestConfiguration**

Ha még nincsenek regisztrálva, kövesse az [Azure Resource Providers regisztrálása](agent-overview.md#register-azure-resource-providers)című témakör lépéseit.

A Linux rendszerhez készült Log Analytics Agent virtuálisgép-bővítményhez a Python 2. x verziója szükséges a célszámítógépen.

### <a name="connected-machine-agent"></a>Csatlakoztatott számítógép ügynöke

Ellenőrizze, hogy a gép megfelel-e a Windows és Linux operációs rendszer [támogatott verzióival](agent-overview.md#supported-operating-systems) az Azure Connected Machine Agent ügynökhöz.

A Windows és Linux rendszeren a szolgáltatással támogatott csatlakoztatott gépi ügynök minimális verziója a 1,0-es kiadás.

Ha a gépet a szükséges ügynök verziójára szeretné frissíteni, tekintse meg az [ügynök frissítése](manage-agent.md#upgrading-agent)című témakört.

## <a name="next-steps"></a>További lépések

A virtuálisgép-bővítmények üzembe helyezése, kezelése és eltávolítása az [Azure CLI](manage-vm-extensions-cli.md), a [PowerShell](manage-vm-extensions-powershell.md), a [Azure Portal](manage-vm-extensions-portal.md)vagy [Azure Resource Manager sablonok](manage-vm-extensions-template.md)használatával végezhető el.
