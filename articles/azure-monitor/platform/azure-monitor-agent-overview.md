---
title: Azure Monitor-ügynök áttekintése
description: A Azure Monitor ügynök (AMA) áttekintése, amely a virtuális gépek vendég operációs rendszerének figyelési adatait gyűjti.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: e38d59ff1eb31dd5fc3ecf6b7df6b12504141d5e
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88083051"
---
# <a name="azure-monitor-agent-overview-preview"></a>Azure Monitor-ügynök áttekintése (előzetes verzió)
A Azure Monitor ügynök (AMA) figyeli a virtuális gépek vendég operációs rendszerének figyelési adatait, és a Azure Monitor számára biztosítja azt. Ez a cikk áttekintést nyújt a Azure Monitor-ügynökről, beleértve a telepítésének módját és az adatgyűjtés konfigurálását.

## <a name="relationship-to-other-agents"></a>Kapcsolat más ügynökökkel
A Azure Monitor ügynök a következő, jelenleg a Azure Monitor által használt ügynököket váltja fel, hogy a vendég adatait a virtuális gépekről gyűjtsön:

- [Log Analytics ügynök](log-analytics-agent.md) – adatokat küld log Analytics munkaterületre, és támogatja Azure monitor for VMS és figyelési megoldásokat.
- [Diagnosztikai bővítmény](diagnostics-extension-overview.md) – adatokat küld Azure monitor metrikák (csak Windows), az Azure Event Hubs és az Azure Storage szolgáltatásba.
- A [Grafi ügynök](collect-custom-metrics-linux-telegraf.md) – adatokat küld Azure monitor metrikáknak (csak Linux).

A funkció egyetlen ügynökbe való konszolidálásán kívül a Azure Monitor ügynök a következő előnyöket biztosítja a meglévő ügynököknél:

- A figyelés hatóköre. Központilag konfigurálhatja a gyűjteményt különböző típusú virtuális gépek különböző csoportjaihoz.  
- Linux rendszerű többszörös vezérlés: a linuxos virtuális gépekről több munkaterületre küldheti az adatait.
- Windows-események szűrése: az XPATH-lekérdezésekkel szűrheti, hogy mely Windows-események legyenek gyűjtve.
- Továbbfejlesztett bővítmények kezelése: Azure Monitor-ügynök egy új módszert használ a bővíthetőség kezelésére, amely átláthatóbbá és ellenőrizhető, mint a felügyeleti csomagok és a Linux beépülő modulok az aktuális Log Analytics-ügynökökben.

### <a name="changes-in-data-collection"></a>Az adatgyűjtés változásai
A meglévő ügynökök adatgyűjtésének meghatározására szolgáló módszerek különböznek egymástól, és mindegyiknek van olyan kihívása, amely Azure Monitor ügynökkel foglalkozik.

- Log Analytics ügynök Log Analytics munkaterületről kapja meg a konfigurációt. Ez könnyen központilag konfigurálható, de nehéz meghatározni a különböző virtuális gépek független definícióit. Csak Log Analytics munkaterületre tud küldeni.

- A diagnosztikai bővítmény minden virtuális géphez rendelkezik konfigurációval. Ez egyszerűen meghatározható a különböző virtuális gépek független definíciói, de a központilag könnyen kezelhető. Az adatok csak Azure Monitor metrikák, Azure Event Hubs vagy Azure Storage szolgáltatásba küldhetők. A Linux-ügynökök esetében a nyílt forráskódú, a Azure Monitor metrikák számára szükséges, hogy az adatok elküldjenek adatokat.

Azure Monitor ügynök az [adatgyűjtési szabályok (DCR)](data-collection-rule-overview.md) használatával konfigurálja az egyes ügynököktől gyűjtött adatokat. Az adatgyűjtési szabályok lehetővé teszik a gyűjtési beállítások kezelhetőségét, miközben továbbra is engedélyezik az egyedi, hatókörön belüli konfigurációkat a számítógépek részhalmaza számára. Függetlenek a munkaterülettől és függetlenek a virtuális gépről, amely lehetővé teszi, hogy egyszer legyenek meghatározva és újra felhasználhatók legyenek a gépek és a környezetek között. Lásd: [az adatgyűjtés konfigurálása a Azure monitor-ügynökhöz (előzetes verzió)](data-collection-rule-azure-monitor-agent.md).



## <a name="current-limitations"></a>Aktuális korlátozások
A Azure Monitor ügynök nyilvános előzetes verziója a következő korlátozásokat alkalmazza:

- A Azure Monitor ügynök nem támogatja a megoldásokat és az adatAzure Monitor for VMsokat, például a és a Azure Security Center. Az egyetlen jelenleg támogatott forgatókönyv az adatok gyűjtése a konfigurált adatgyűjtési szabályok használatával. 
- Az adatgyűjtési szabályokat ugyanabban a régióban kell létrehozni, mint a célhelyként használt Log Analytics munkaterület.
- Jelenleg csak az Azure-beli virtuális gépek támogatottak. A helyszíni virtuális gépek, a virtuálisgép-méretezési csoportok, az arc a kiszolgálók, az Azure Kubernetes szolgáltatás és más számítási erőforrástípusok jelenleg nem támogatottak.
- A virtuális gépnek hozzá kell férnie a következő HTTPS-végpontokhoz:
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>Együttélés más ügynökökkel
A Azure Monitor ügynök együtt létezhet a meglévő ügynökökkel, így az értékelés vagy az áttelepítés során továbbra is használhatja meglévő funkcióit. Ez különösen fontos a meglévő megoldások támogatásának nyilvános előzetes verziójában. Ügyeljen arra, hogy a duplikált adatok összegyűjtése óta ez a lekérdezési eredmények eldöntése, valamint az adatfeldolgozás és-megőrzés további díját eredményezheti.

A Azure Monitor for VMs például a Log Analytics ügynök használatával küld teljesítményadatokat egy Log Analytics munkaterületre. A munkaterületet úgy is beállíthatja, hogy a Windows-eseményeket és a syslog-eseményeket összegyűjtse az ügynököktől. Ha telepíti a Azure Monitor-ügynököt, és létrehoz egy adatgyűjtési szabályt ugyanezen események és teljesítményadatok esetében, a rendszer duplikált adatértéket eredményez.


## <a name="costs"></a>Költségek
Azure Monitor ügynöknek nincs díja, de a betöltött adatokért díjat számítunk fel. Az adatgyűjtéssel és-megőrzéssel, valamint az ügyfél-metrikákkal kapcsolatos részletekért tekintse Log Analytics meg [Azure monitor díjszabását](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="data-sources-and-destinations"></a>Adatforrások és célhelyek
A következő táblázat felsorolja, hogy milyen típusú adatok gyűjthetők össze az Azure Monitor ügynökkel az adatgyűjtési szabályok segítségével, és hogy hol küldhetik el ezeket az adatokat. Tekintse meg a [Azure monitor által figyelt adatokat?](../monitor-reference.md) az elemzések, megoldások és egyéb olyan megoldások listáját, amelyek a Azure monitor-ügynököt használják más típusú adatok gyűjtésére.


A Azure Monitor ügynök adatokat küld Azure Monitor metrikák vagy egy Log Analytics munkaterületre, amely támogatja Azure Monitor naplókat.

| Adatforrás | Célhelyek | Leírás |
|:---|:---|:---|
| Teljesítmény        | Azure Monitor metrikák<br>Log Analytics-munkaterület | Az operációs rendszer és a számítási feladatok különböző szempontjainak teljesítményét mérő numerikus értékek. |
| Windows-eseménynaplók | Log Analytics-munkaterület | A Windows eseménynaplózási rendszernek eljuttatott információk. |
| Rendszernapló             | Log Analytics-munkaterület | A Linux-eseménynaplózási rendszernek eljuttatott információk. |


## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
A Azure Monitor ügynök jelenleg a következő operációs rendszereket támogatja.

### <a name="windows"></a>Windows 
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012
  - Windows Server 2012 R2

### <a name="linux"></a>Linux
  - CentOS 6<sup>1</sup>, 7
  - Debian 9, 10
  - Oracle Linux 6<sup>1</sup>, 7
  - RHEL 6<sup>1</sup>, 7, 8
  - SLES 11, 12, 15
  - Ubuntu 14,04 LTS, 16,04 LTS, 18,04 LTS

> [!IMPORTANT]
> <sup>1</sup> A syslog-adatküldéshez a rsyslog el kell távolítania, és telepítenie kell a syslog-ng-t.


## <a name="security"></a>Biztonság
A Azure Monitor ügynöknek nincs szüksége kulcsra, de a [rendszerhez rendelt felügyelt identitásra](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)van szükség. Az ügynök telepítése előtt minden virtuális gépen engedélyezve kell lennie egy rendszerhez rendelt felügyelt identitásnak.


## <a name="install-the-azure-monitor-agent"></a>A Azure Monitor ügynök telepítése
A Azure Monitor ügynök Azure-beli virtuálisgép- [bővítményként](../../virtual-machines/extensions/overview.md) van megvalósítva, és a következő táblázatban található adatokat tartalmazza. 

| Tulajdonság | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft. Azure. monitor  | Microsoft. Azure. monitor |
| Típus      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1,0 | 0.9 |

Telepítse a Azure Monitor-ügynököt a virtuálisgép-ügynökök telepítésének bármely módszerével, beleértve az alábbiakat a PowerShell vagy a parancssori felület használatával. Azt is megteheti, hogy telepítheti az ügynököt, és az Azure-előfizetésben lévő virtuális gépeken is konfigurálhatja az adatgyűjtést a portálon az [adatgyűjtés konfigurálása a Azure monitor ügynökhöz (előzetes verzió)](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal)című szakaszban ismertetett eljárással.

### <a name="windows"></a>Windows

# <a name="cli"></a>[Parancssori felület](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --version 1.0 --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -Version 1.0 -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[Parancssori felület](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --version 0.9 --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -Version 0.9 -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---

## <a name="next-steps"></a>További lépések

- [Hozzon létre egy adatgyűjtési szabályt](data-collection-rule-azure-monitor-agent.md) , amely adatokat gyűjt az ügynöktől, és elküldi azt a Azure monitornak.
