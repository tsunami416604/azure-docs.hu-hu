---
title: Virtuálisgép-bővítmények kezelése az Azure arc-kompatibilis kiszolgálókkal
description: Az Azure arc-kompatibilis kiszolgálók kezelhetik azokat a virtuálisgép-bővítmények központi telepítését, amelyek a telepítés utáni konfigurálást és az automatizálási feladatokat nem Azure-beli virtuális gépekkel is rendelkeznek.
ms.date: 12/14/2020
ms.topic: conceptual
ms.openlocfilehash: 55e21f9c6bcd2dfe5f995093034773f2a87d9b03
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504508"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Virtuálisgép-bővítmények kezelése az Azure arc használatára képes kiszolgálókon

A virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Ha például egy virtuális gépnek szoftver telepítésére, vírusvédelmi védelemre vagy parancsfájl futtatására van szüksége, a rendszer egy virtuálisgép-bővítményt is használhat.

Az Azure arc-kompatibilis kiszolgálók lehetővé teszik az Azure-beli virtuálisgép-bővítmények üzembe helyezését a nem Azure-beli Windows-és Linux-alapú virtuális gépeken, így egyszerűbbé válik a hibrid gép felügyelete életciklusuk során. A virtuálisgép-bővítmények a következő módszerekkel kezelhetők a hibrid gépeken vagy az arc-kompatibilis kiszolgálók által felügyelt kiszolgálókon:

- A [Azure Portal](manage-vm-extensions-portal.md)
- Az [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager-sablonok](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Főbb előnyök

Az Azure arc-kompatibilis kiszolgálók virtuálisgép-bővítmények támogatása a következő főbb előnyöket nyújtja:

- [Azure Automation állapot konfigurációjának](../../automation/automation-dsc-overview.md) használatával központilag tárolhatja a konfigurációkat, és megtarthatja a DSC virtuálisgép-bővítményen keresztül engedélyezett hibrid csatlakoztatott gépek kívánt állapotát.

- Begyűjti a naplózási adatokat az elemzéshez [Azure monitor a naplókat](../../azure-monitor/platform/data-platform-logs.md) az log Analytics ügynök virtuálisgép-bővítményének használatával. Ez hasznos lehet a különböző típusú forrásokból származó adatok összetett elemzéséhez.

- A [Azure monitor for VMS](../../azure-monitor/insights/vminsights-overview.md)a elemzi a Windows-és Linux-alapú virtuális gépek teljesítményét, és figyeli a folyamatokat és a függőségeket más erőforrásokra és külső folyamatokra. Ez a Log Analytics ügynök és a függőségi ügynök virtuálisgép-bővítményeinek engedélyezésével érhető el.

- Parancsfájlokat tölthet le és futtathat a hibrid csatlakoztatott gépeken az egyéni szkriptek bővítmény használatával. Ez a bővítmény az üzembe helyezési konfiguráció, a Szoftvertelepítés vagy bármely egyéb konfigurációs vagy felügyeleti feladat után hasznos.

- [Azure Key Vault](../../key-vault/general/overview.md)tárolt tanúsítványok automatikus frissítése.

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
|Key Vault | Windows | Microsoft.Compute | [A Windows rendszerhez készült virtuálisgép-bővítmény Key Vault](../../virtual-machines/extensions/key-vault-windows.md) |
|CustomScript|Linux |Microsoft. Azure. Extension |[Linux Custom script Extension 2. verzió](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft. OSTCExtensions |[PowerShell DSC-bővítmény Linux rendszerhez](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics-ügynök |Linux |Microsoft. EnterpriseCloud. monitoring |[A Linux rendszerhez készült virtuálisgép-bővítmény Log Analytics](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft függőségi ügynök | Linux |Microsoft.Compute | [Függőségi ügynök linuxos virtuálisgép-bővítménye](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Key Vault | Linux | Microsoft.Compute | [A Linux rendszerhez készült virtuálisgép-bővítmény Key Vault](../../virtual-machines/extensions/key-vault-linux.md) |

Ha többet szeretne megtudni az Azure-beli csatlakoztatott gépi ügynök csomagról és a bővítmény ügynök összetevőjéről, tekintse meg az [ügynök áttekintése](agent-overview.md#agent-component-details)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció az előfizetés alábbi Azure-erőforrás-szolgáltatói függ:

- **Microsoft. HybridCompute**
- **Microsoft. GuestConfiguration**

Ha még nincsenek regisztrálva, kövesse az [Azure erőforrás-szolgáltatók regisztrálása](agent-overview.md#register-azure-resource-providers)című témakör lépéseit.

Ügyeljen arra, hogy az előző táblázatban hivatkozott minden virtuálisgép-bővítmény dokumentációjában ellenőrizze, hogy rendelkezik-e hálózati vagy rendszerkövetelményekkel. Ez segít elkerülni az Azure-szolgáltatásokkal vagy-szolgáltatásokkal kapcsolatos kapcsolódási problémák elkerülését, amely az adott virtuálisgép-bővítményre támaszkodik.

### <a name="log-analytics-vm-extension"></a>Log Analytics virtuálisgép-bővítmény

A Linux rendszerhez készült Log Analytics Agent virtuálisgép-bővítményhez a Python 2. x verziója szükséges a célszámítógépen. 

### <a name="azure-key-vault-vm-extension-preview"></a>Azure Key Vault VM-bővítmény (előzetes verzió)

A Key Vault VM-bővítmény (előzetes verzió) nem támogatja a következő Linux operációs rendszereket:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

A Key Vault virtuálisgép-bővítmény (előzetes verzió) üzembe helyezése csak a következő használatával támogatott:

- Azure CLI
- A Azure PowerShell
- Azure Resource Manager-sablon

A bővítmény telepítése előtt a következőket kell elvégeznie:

1. [Hozzon létre egy tárolót és egy tanúsítványt](../../key-vault/certificates/quick-create-portal.md) (önaláírt vagy importált).

2. Adja meg az Azure arc számára engedélyezett kiszolgáló hozzáférését a tanúsítvány titkos kódjához. Ha a [RBAC előzetes](../../key-vault/general/rbac-guide.md)verzióját használja, keresse meg az Azure arc-erőforrás nevét, és rendelje hozzá a **Key Vault Secrets User (előzetes verzió)** szerepkört. Ha [Key Vault hozzáférési szabályzatot](../../key-vault/general/assign-access-policy-portal.md)használ, rendeljen titkos **Get** engedélyeket az Azure arc-erőforrás rendszerhez rendelt identitásához.

### <a name="connected-machine-agent"></a>Csatlakoztatott számítógép ügynöke

Ellenőrizze, hogy a gép megfelel-e a Windows és Linux operációs rendszer [támogatott verzióival](agent-overview.md#supported-operating-systems) az Azure Connected Machine Agent ügynökhöz.

A Windows és Linux rendszeren a szolgáltatással támogatott csatlakoztatott gépi ügynök minimális verziója a 1,0-es kiadás.

Ha a gépet a szükséges ügynök verziójára szeretné frissíteni, tekintse meg az [ügynök frissítése](manage-agent.md#upgrading-agent)című témakört.

## <a name="next-steps"></a>Következő lépések

Virtuálisgép-bővítményeket telepíthet, kezelhet és eltávolíthat az [Azure CLI](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md), [Azure Portal](manage-vm-extensions-portal.md)vagy [Azure Resource Manager sablonok](manage-vm-extensions-template.md)használatával.
