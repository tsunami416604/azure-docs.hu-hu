---
title: Virtuálisgép-méretezési csoport karbantartásával kapcsolatos értesítések beállítása az Azure-ban |} A Microsoft Docs
description: Karbantartásával kapcsolatos értesítések megtekintése, és indítsa el az önkiszolgáló karbantartás virtuálisgép-méretezési csoportokhoz az Azure-ban.
services: virtual-machine-scale-sets
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: shants
ms.openlocfilehash: 82a3ce9f899e94a1cc737f2ca2dc1dc79688a224
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42054079"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>A virtual machine scale sets tervezett karbantartásával kapcsolatos értesítések

Az Azure rendszeresen végez javítása, a megbízhatóság, teljesítmény és a gazda-infrastruktúrát, a virtuális gépek (VM) biztonsági frissítéseket. Frissítések tartalmazhatnak az üzemeltetési környezet javítások vagy frissítése és hardver leszerelése. A legtöbb frissítések nem befolyásolják a üzemeltetett virtuális gépeket. Frissítések azonban ezekben a forgatókönyvekben befolyásolja az virtuális gépek:

- A karbantartás nem igényel újraindítást, ha az Azure a virtuális gép felfüggesztése, amíg frissül a gazdagép helyi áttelepítést használ. Karbantartási műveleteket, amelyek nem igényelnek újraindítást tartalék tartomány által alkalmazott tartalék tartományban. Folyamat leáll, ha bármilyen figyelmeztetés állapot jelek.

- Ha karbantartási újraindítás szükséges, bemutató, amikor a karbantartás tervezett értesítést kap. Ezekben az esetekben, kapnak egy olyan időkeretet, amelyben is elindítható a karbantartás saját kezűleg Ha működik a legjobban az Ön számára.


Tervezett karbantartás, hogy újra kell indítani a regisztrálást megelőző szakaszban tanácsos van ütemezve. Egyes szakaszokban eltérő hatókörbe (régió) rendelkezik:

- A szakasz egy értesítés azoknak az ügyfeleknek kezdődik. Alapértelmezés szerint az előfizetés tulajdonosa és a társtulajdonosok értesítést küld. Hozzáadhat címzetteket és üzenetküldési lehetőségeket, például e-mail, SMS és webhookok az értesítéseket az Azure [tevékenységnapló-riasztásokba](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).  
- Az értesítés egy *önkiszolgáló ablak* szeretné elérhetővé tenni. Ezen időszak alatt találja, amely a virtuális gépek szerepelnek a wave. Proaktív módon elindítható a karbantartás ütemezési igény szerint.
- Az önkiszolgáló ablak után egy *ütemezett karbantartási időszaknál* kezdődik. Ezen időszak alatt bármikor Azure ütemezi, és a szükséges karbantartás a virtuális Gépre vonatkozik. 

A két windows kellene célja, hogy indítsa el a karbantartást, és a virtuális gép újraindítása közben, hogy ha az Azure automatikusan elindul, karbantartási elegendő időt biztosítanak.


Használhatja az Azure portal, PowerShell, REST API és az Azure CLI lekérdezni a karbantartási időszakokat, a virtuális gép méretezési virtuális gépeket, és indítsa el az önkiszolgáló karbantartás.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>El kell indulnia karbantartási önkiszolgáló időszakban?  

Az alábbi irányelvek segítségével indítsa el a karbantartási Ön által választott egyszerre kell-e.

> [!NOTE] 
> Az önkiszolgáló karbantartás nem érhető el az összes, a virtuális gépeket. Annak megállapításához, hogy proaktív ismételt üzembe helyezés elérhető-e a virtuális géphez, keressen **most** a karbantartási állapota. Jelenleg az önkiszolgáló karbantartás nem érhető el az Azure Cloud Services (webes/feldolgozói szerepkör) és az Azure Service Fabric.


Az önkiszolgáló karbantartás használó központi telepítések esetében nem ajánlott *rendelkezésre állási csoportok*. A rendelkezésre állási csoportok a melyik csak egyetlen frissítési tartomány érintett bármikor magas rendelkezésre állású beállításokat is. A rendelkezésre állási csoportok:

- Bízza az Azure-trigger a karbantartás. Karbantartási újraindítását igénylő karbantartás, frissítési tartomány frissítési tartományonként történik. Frissítési tartományok nem feltétlenül kapja meg a karbantartási egymás után. Egy 30 perces szüneteltetése frissítési tartományok között van.
- Ha átmenetileg megszakad a kapacitást (1/frissítési tartományok száma) néhány szempont, akkor is könnyen kompenzálja a foglalhatja le a további példányokat a karbantartási időszak alatt.
- Frissítések, amelyek nem igényelnek újraindítást karbantartás, a tartalék tartomány szintje vonatkoznak. 
    
**Nem** önkiszolgáló karbantartási használja a következő esetekben: 

- Ha leállítja a virtuális gépek gyakran manuálisan, a DevTest Labs használatával, az automatikus leállítási vagy a következő ütemezés szerint. Az önkiszolgáló karbantartás ezekben az esetekben előfordulhat, hogy a karbantartási állapot visszaállítása, és további üzemkimaradást eredményezhet.
- A rövid élettartamú virtuális gépeken arról, hogy törli a karbantartás lejárta előtt. 
- A számítási feladatok nagy állapotú tárolt helyi (ideiglenes) lemezre, amely meg szeretné tartani a frissítés után. 
- Ha a virtuális gép gyakran méretezze át. Ebben a forgatókönyvben előfordulhat, hogy visszaállítja a karbantartás állapotával. 
- Ha az ütemezett események, amelyek proaktív feladatátvételi vagy az alkalmazások és szolgáltatások biztonságos leállításának karbantartási leállítás kezdete előtt 15 perc fogadtak.

**Tegye** önkiszolgáló karbantartási használja, ha azt tervezi, hogy megszakítás nélkül az ütemezett karbantartás során a virtuális gép futtatásához, és egyetlen, sem az előző counterindications a alkalmazni. 

Célszerű használni az önkiszolgáló karbantartás a következő esetekben:

- Szeretne kommunikálni egy felügyeleti vagy az ügyfél pontos karbantartási időszakot. 
- Egy adott dátum szerint a karbantartás végrehajtásához szükséges. 
- Kell sorrendjét szabályozhatja a karbantartás, például egy többrétegű alkalmazás esetében biztonságos helyreállítás biztosítása érdekében.
- Két frissítési tartományok között a virtuális gép helyreállítási idő több mint 30 percnyi van szüksége. A frissítési tartományok között eltelt idő szabályozásához indít el a kell karbantartási a virtuális gépek egy frissítési tartomány egyszerre.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Nézet virtuális gépek méretezési karbantartási a portál által érintett

Ha egy tervezett karbantartás ütemezve van, megtekintheti az Azure portal használatával a következő karbantartás által érintett virtuális gép méretezési csoportok listája. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza ki a **minden szolgáltatás**, majd válassza ki **a Virtual machine scale sets**.
3. A **a Virtual machine scale sets**válassza **oszlopok szerkesztése** az elérhető oszlopok listájának megnyitásához.
4. Az a **elérhető oszlopok** szakaszban jelölje be **önkiszolgáló karbantartási**, és áthelyezi az, hogy a **kijelölt oszlopok** listája. Kattintson az **Alkalmaz** gombra.  

    Győződjön meg arról, hogy a **önkiszolgáló karbantartási** elem egyszerűbb megkereséséhez, módosíthatja a legördülő lehetőség a **elérhető oszlopok** című **összes** való  **Tulajdonságok**.

A **önkiszolgáló karbantartási** oszlop most már a virtual machine scale sets listájában jelenik meg. Minden egyes virtuálisgép-méretezési csoportot az önkiszolgáló karbantartás oszlop a következő értékek egyike lehet:

| Érték | Leírás |
|-------|-------------|
| Igen | A virtuálisgép-méretezési csoportban lévő legalább egy virtuális gép van egy önkiszolgáló ablakban. Az önkiszolgáló időszak alatt bármikor elindítható a karbantartás. | 
| Nem | Nincsenek olyan virtuális gépek vannak az érintett virtuálisgép-méretezési csoport egy önkiszolgáló ablakban állítsa be. | 
| - | A virtuális gépek méretezési csoportok nem tervezett karbantartási műveletek részét.| 

## <a name="notification-and-alerts-in-the-portal"></a>Értesítések és riasztások a portálon

Az Azure tervezett karbantartási ütemezés egy e-mailt küld az előfizetés tulajdonosa és a társtulajdonosok csoport kommunikál. Adhat hozzá címzetteket és csatornák a kommunikáció tevékenységnapló-riasztások létrehozásával. További információkért lásd: [figyelése az Azure-tevékenységnapló előfizetés](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza ki a **figyelő**. 
3. Az a **figyelő – riasztások (klasszikus)** ablaktáblán válassza előbb **+ tevékenységnapló-riasztás hozzáadása**.
4. Az a **tevékenységnapló-riasztás hozzáadása** oldalon válassza ki vagy adja meg a kért adatokat. A **feltételek**, győződjön meg arról, hogy állítsa a következő értékeket:
   - **Eseménykategória**: válasszon **Service Health**.
   - **Szolgáltatások**: válasszon **Virtual Machine Scale Sets és a virtuális gépek**.
   - **Típus**: válasszon **tervezett karbantartás**. 
    
Tevékenységnapló-riasztásokba konfigurálásával kapcsolatos további tudnivalókért lásd: [létrehozása tevékenységnapló-riasztások](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Karbantartás indítása a portálról a virtuális gép méretezési

Láthatja, hogy a virtual machine scale sets áttekintése karbantartási kapcsolatos további részleteket. Ha a virtuálisgép-méretezési csoportban lévő legalább egy virtuális gép a tervezett karbantartás szerepel, egy új értesítési menüszalag adnak az oldal tetején. Válassza ki az értesítési menüszalagjának nyissa meg a **karbantartási** lapot. 

Az a **karbantartási** lapon láthatja, melyik Virtuálisgép-példány a tervezett karbantartás hatással van. Karbantartás indítása, jelölje be a jelölőnégyzetet, az érintett virtuális gép megfelelő. Ezután válassza ki **indítsa el a karbantartási**.

Elindítja a karbantartási, miután az érintett virtuális gépek a virtuális gép méretezési karbantartás, és átmenetileg nem érhetők el. Ha lemaradt az önkiszolgáló ablak, továbbra is láthatja az időtartomány, ha a virtuális gép méretezési továbbra is ugyanúgy működik, az Azure-ban.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Karbantartási állapot ellenőrzése a PowerShell használatával

Azure PowerShell használatával tekintse meg, ha a virtuálisgép-méretezési csoportok virtuális gépeinek ütemezett karbantartás. Tervezett karbantartás érhető el információ segítségével a [a Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) parancsmag használatakor a `-InstanceView` paraméter.
 
Csak akkor, ha a karbantartási tervezett karbantartási információk jelennek-e. Nincs karbantartás ütemezve van, amely hatással van a Virtuálisgép-példány, ha a parancsmag nem ad vissza semmilyen karbantartási információt. 

```powershell
Get-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Az alábbi tulajdonságokat adja vissza csoportban **MaintenanceRedeployStatus**: 
| Érték | Leírás   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy elindítható a karbantartás a virtuális gép jelenleg. ||
| PreMaintenanceWindowStartTime         | A karbantartási önkiszolgáló időszak elején, ha a virtuális Gépen kezdeményezheti a karbantartást. ||
| PreMaintenanceWindowEndTime           | Önkiszolgáló időszak során a virtuális Gépen kezdeményezheti a karbantartást végén. ||
| MaintenanceWindowStartTime            | Az elején, az ütemezett karbantartási, amelyben Azure kezdeményez a virtuális gép karbantartását. ||
| MaintenanceWindowEndTime              | Az ütemezett karbantartási időszak, amelyben Azure kezdeményez a virtuális gép karbantartását végén. ||
| LastOperationResultCode               | Kezdeményezheti a karbantartást, a virtuális gép a legutóbbi kísérlet eredménye. ||



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Karbantartási indítása a Virtuálisgép-példány a PowerShell használatával

Karbantartási indítsa el a virtuális gép Ha **IsCustomerInitiatedMaintenanceAllowed** értékre van állítva **igaz**. Használja a [Set-azurermvmss parancsmagban](/powershell/module/azurerm.compute/set-azurermvmss) parancsmagot `-PerformMaintenance` paraméter.

```powershell
Set-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Karbantartási állapot ellenőrzése a CLI-vel

Tervezett karbantartás információ segítségével tekintheti [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Csak akkor, ha a karbantartási tervezett karbantartási információk jelennek-e. Nincs karbantartás, amely hatással van a Virtuálisgép-példány van ütemezve, akkor a parancs nem ad vissza karbantartási információt. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Az alábbi tulajdonságokat adja vissza csoportban **MaintenanceRedeployStatus** az egyes Virtuálisgép-példányok: 
| Érték | Leírás   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy elindítható a karbantartás a virtuális gép jelenleg. ||
| PreMaintenanceWindowStartTime         | A karbantartási önkiszolgáló időszak elején, ha a virtuális Gépen kezdeményezheti a karbantartást. ||
| PreMaintenanceWindowEndTime           | Önkiszolgáló időszak során a virtuális Gépen kezdeményezheti a karbantartást végén. ||
| MaintenanceWindowStartTime            | Az elején, az ütemezett karbantartási, amelyben Azure kezdeményez a virtuális gép karbantartását. ||
| MaintenanceWindowEndTime              | Az ütemezett karbantartási időszak, amelyben Azure kezdeményez a virtuális gép karbantartását végén. ||
| LastOperationResultCode               | Kezdeményezheti a karbantartást, a virtuális gép a legutóbbi kísérlet eredménye. ||


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Karbantartás indítása a Virtuálisgép-példány a parancssori felület használatával

A következő hívást kezdeményez a karbantartási egy Virtuálisgép-példány, ha `IsCustomerInitiatedMaintenanceAllowed` értékre van állítva **igaz**:

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>GYIK

**K: Miért van szüksége a virtuális gépek Újraindítás most?**

**V:** Bár a legtöbb frissítéseket és az Azure platform nem befolyásolják a virtuális gépek rendelkezésre állása, néhány esetben azt nem kerülheti el az Azure-ban üzemeltetett virtuális gépek újraindítása. A Microsoft rendelkezik halmozott számos, szükség lehet, hogy a virtuális gép újraindítása eredményez újra módosítást.

**K: Ha szeretnék kövesse a magas rendelkezésre állás a rendelkezésre állási javaslatok beállítása, vagyok a biztonságos?**

**V:** egy rendelkezésre állási üzembe helyezett virtuális gépek vagy virtuálisgép-méretezési használhatja a frissítési tartományok. A karbantartás során az Azure figyelembe veszi a frissítési tartomány korlátozás, és nem indítsa újra a virtuális gépeket egy másik frissítési tartományon (belül az azonos rendelkezésre állási csoport). Azure virtuális gépek a következő csoporthoz áthelyezése előtt legalább 30 percig is vár. 

Magas rendelkezésre állású kapcsolatos további információkért lásd: [régiók és rendelkezésre állás az Azure-beli virtuális gépek](../virtual-machines/windows/regions-and-availability.md).

**K: Hogyan kaphatok értesítést a tervezett karbantartásról?**

**V:** tervezett karbantartási műveletek úgy, hogy egy vagy több Azure-régiók ütemezés szerint kezdődik. Hamarosan, e-mail-értesítés elküldése után az előfizetés tulajdonosainak (előfizetésenként egy e-mail címet). Tevékenységnapló-riasztások használatával adhat hozzá a csatornákat és az értesítés címzettjeit. Ha egy virtuális Gépet telepít egy régiót, amelyben a tervezett karbantartás már ütemezve van, nem kapja meg az értesítést. Ehelyett ellenőrizze a virtuális gép karbantartási állapotát.

**K: nem látom minden a portal, PowerShell vagy a parancssori felület a tervezett karbantartásra utaló jel. mi a baj?**

**V:** tervezett karbantartáshoz kapcsolódó információk érhetők el a a tervezett karbantartás által érintett virtuális gépek csak a tervezett karbantartási műveletek során. Ha nem lát adatokat, a karbantartás már valószínűleg befejeződött (vagy nem kezdődött el), vagy előfordulhat, hogy a virtuális gép már eleve egy frissített kiszolgálón.

**K: van mód a tudni, hogy pontosan mikor virtuális gépem érinti?**

**V:** meghatározzuk, ha az ütemezés beállítása, hogy egy olyan időkeretet, több napig. Ezen időszakon belül a kiszolgálók (és a virtuális gépek) pontos karbantartási ütemezése nem ismert. Ha szeretné tudni, hogy a virtuális gépek frissülni fog a pontos idő, használhatja [ütemezett események](../virtual-machines/windows/scheduled-events.md). Az ütemezett események használatakor kérdezheti le a virtuális gép, és egy 15 perces értesítés a virtuális gép újraindítása előtt.

**K: mennyi ideig tart, hogy újraindítja a virtuális Gépet?**

**V:** a virtuális gép méretétől függően újraindítás is igénybe vehet néhány percig, az önkiszolgáló karbantartási időszak alatt. Az ütemezett karbantartási időszaknál Azure által kezdeményezett újraindítások során az újraindítás általában körülbelül 25 percig tart. Ha Cloud Services (webes/feldolgozói szerepkört), a virtual machine scale sets, használhatja, vagy rendelkezésre állási csoportok, akkor az ütemezett karbantartási időszak alatt 30 perc (frissítési tartomány) virtuális gépek csoportjai között kapnak. 

**K: nem látom minden karbantartási információt saját virtuális gépeken. Mi történt?**

**V:** miért akkor lehet, hogy nem jelenik meg semmilyen karbantartási információt a virtuális gépek több oka is van:
   - -Előfizetését egy megjelölve *Microsoft Internal*.
   - A virtuális gépek nem tervezett karbantartásra. Lehet, hogy a karbantartás véget ért, megszakadt vagy módosult, hogy a virtuális gépek van már nincs hatással.
   - Nem rendelkezik a **karbantartási** a virtuális gép listanézetre felvett oszloppal. Bár ebben az oszlopban az alapértelmezett nézethez hozzáadtunk, ha a nézet nem alapértelmezett oszlopok, manuálisan kell hozzáadni a **karbantartási** oszlop a virtuális gép lista nézetre.

**K: a virtuális gép karbantartásra van ütemezve a második alkalommal. Hogy miért?**

**V:** több használati esetek, a virtuális gép ütemezett karbantartás már befejeződött a karbantartás és újratelepítése után:
   - Hogy megszakadt a karbantartás és újraindul, egy másik hasznos adattal. Lehet, hogy olyan hibás adattartalmat észleltünk, és egyszerűen kell telepíteni egy további hasznos.
   - A virtuális gép volt *kezelte a szolgáltatás* hardverhiba miatt egy másik csomópontra.
   - Választotta, állítsa le (szabadítsa fel), majd indítsa újra a virtuális Gépet.
   - Rendelkezik **automatikus leállítás** engedélyezve van a virtuális gép.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan kell regisztrálni a virtuális gépen a karbantartási események használatával [ütemezett események](../virtual-machines/windows/scheduled-events.md).
