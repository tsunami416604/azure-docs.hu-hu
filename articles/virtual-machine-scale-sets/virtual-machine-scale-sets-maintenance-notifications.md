---
title: Az Azure-ban a Virtual Machine Scale Sets karbantartásával kapcsolatos értesítések kezelése |} A Microsoft Docs
description: Virtual Machine Scale Sets karbantartásával kapcsolatos értesítések megtekintése az Azure-ban, és indítsa el az önkiszolgáló karbantartás.
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
ms.openlocfilehash: 4ce984686c2bb320d5d32771d31b81cdec1153af
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38314405"
---
# <a name="handling-planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Tervezett Virtual Machine Scale Sets karbantartásával kapcsolatos értesítések kezelése

Az Azure rendszeresen végez frissítéseket a virtuális gépeket futtató infrastruktúra megbízhatóságának, teljesítményének és biztonságának javítása érdekében. A frissítések olyan módosítások, például javítása, a üzemeltetési környezet vagy a frissítés és a hardver leszerelése. Ezek a frissítések többségét annak hatását a futtatott virtuális gépek működésére. Előfordulhatnak azonban olyan esetekben, amikor frissítések hatással:

- A karbantartás nem igényel újraindítást, ha az Azure a virtuális gép felfüggesztése, amíg frissül a gazdagép helyi áttelepítést használ. Ezek nem rebootful karbantartási műveleteket tartalék tartomány által alkalmazott tartalék tartományt, és folyamatban le van állítva, ha bármilyen figyelmeztetés állapot jelek.

- Ha karbantartási újraindítás szükséges, egy értesítés, amikor a karbantartás tervezett kap. Ezekben az esetekben, kapnak egy olyan időkeretet, ahol elkezdheti a karbantartás saját magának, mikor működik az Ön számára.


Tervezett karbantartás, amely újraindítást igényel, a regisztrálást megelőző szakaszban tanácsos van ütemezve. Egyes szakaszokban eltérő hatókörbe (régió) rendelkezik.

- A szakasz egy értesítés azoknak az ügyfeleknek kezdődik. Alapértelmezés szerint az előfizetés tulajdonosa és a társtulajdonosok értesítést küld. Hozzáadhat további címzetteket, és e-mailben, SMS és Webhookok, például üzenetküldési lehetőségeket, az értesítéseket az Azure- [tevékenységnapló-riasztások](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).  
- Az értesítés időpontban egy *önkiszolgáló ablak* szeretné elérhetővé tenni. Ezen időszak alatt található, amely a virtuális gépek szerepelnek a wave, és proaktív módon indítsa el a karbantartási ütemezés igény szerint.
- Az önkiszolgáló ablak után egy *ütemezett karbantartási időszaknál* kezdődik. Ebben az ablakban egy pontján az Azure ütemezi, és a szükséges karbantartás vonatkozik a virtuális gép. 

A két windows kellene célja, hogy indítsa el a karbantartást, és a virtuális gép újraindítása közben, hogy ha az Azure automatikusan elindul, karbantartási elegendő időt biztosítanak.


Az Azure portal, PowerShell, REST API-t is használhatja, és a CLI lekérdezéséhez a karbantartási időszak a virtuálisgép-méretezési csoport virtuális gépei, és indítsa el az önkiszolgáló karbantartás.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>El kell indulnia karbantartási önkiszolgáló időszakban?  

Az alábbi irányelvek segítenek használhatják ezt a funkciót, és a saját időpontban karbantartási el kell-e.

> [!NOTE] 
> Az önkiszolgáló karbantartás nem érhető el az összes, a virtuális gépeket. Annak megállapításához, hogy proaktív ismételt üzembe helyezése a virtuális Géphez elérhető, keresse meg a **most** a karbantartási állapota. Az önkiszolgáló karbantartás jelenleg nem érhető el a Cloud Services (webes/feldolgozói szerepkör) és a Service Fabric.


Az önkiszolgáló karbantartás nem ajánlott használó **rendelkezésre állási csoportok** mivel ezek a magas rendelkezésre állású beállításnál, ha csak egyetlen frissítési tartományt kihatással van a egy adott időpontban. 

- Bízza az Azure-trigger a karbantartás. Újraindítás szükséges karbantartás vegye figyelembe, hogy a karbantartás történik, hogy a frissítési tartományok nem feltétlenül kap a karbantartás egymás után, frissítési tartományonként frissítési tartományt, és hogy van-e a 30 perces szüneteltetése frissítési tartományok között.
- Ha átmenetileg megszakad a kapacitást (1/frissítési tartományok száma) néhány szempont, azt is könnyen lehet kompenzálni további példányokat lefoglalása a karbantartási időszak alatt.
- Nem igénylő karbantartási újraindítás, a frissítések alkalmazása a tartalék tartomány szintjén. 
    
**Nem** önkiszolgáló karbantartási használja a következő esetekben: 

- Ha leállítja a virtuális gépek gyakran, akár manuálisan, DevTest Labs használatával automatikus leállítási, vagy ütemezés szerint, a következő használatával, képes a karbantartási állapot visszaállítása, és ezért a további üzemkimaradást eredményezhet.
- A rövid élettartamú virtuális gépeken arról, hogy törli a karbantartás lejárta előtt. 
- A számítási feladatok nagy állapotú tárolt helyi (ideiglenes) lemezre, amely megmaradjon a frissítés után van szükség. 
- Olyan esetekben, ahol a virtuális gép átméretezésekor, gyakran, ahogy sikerült a karbantartási állapot visszaállítása. 
- Ha az ütemezett események, amelyekkel proaktív feladatátvételi vagy a számítási feladatok leállításának karbantartási leállítás kezdete előtt 15 perccel fogadtak.

**Használat** az önkiszolgáló karbantartás, ha tervez futtatni a virtuális gép zavartalan az ütemezett karbantartás során, és a fent említett másik jelzések sem alkalmazható. 

Célszerű használni az önkiszolgáló karbantartás a következő esetekben:

- Szeretne kommunikálni a felügyeleti vagy végfelhasználói egy pontos karbantartási időszak. 
- Egy adott időpontig a karbantartás végrehajtásához szükséges. 
- Kell sorrendjét szabályozhatja a karbantartás, például több rétegből álló alkalmazás biztonságos helyreállítás biztosítása érdekében.
- Két frissítési tartomány (frissítési) között a virtuális gép helyreállítási idő több mint 30 percnyi van szüksége. A frissítési tartományok között eltelt idő szabályozásához indít el a kell karbantartási a virtuális gépek egy frissítési tartomány (UD) egyszerre.

 
## <a name="view-virtual-machine-scale-sets-impacted-by-maintenance-in-the-portal"></a>A portálon karbantartás által érintett virtuális gépek méretezési megtekintése

Ha egy tervezett karbantartás ütemezve van, megtekintheti az Azure portal használatával a következő karbantartás által befolyásolt virtuális virtuálisgép-méretezési csoportok listája. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** a bal oldali navigációs válassza **a Virtual machine scale sets**.
3. Az a **a Virtual machine scale sets** lappján válassza a **Upravit sloupce** lehetőséget az oldal tetején elérhető oszlopok listájának megnyitásához.
4. Az a **elérhető oszlopok szakasz**, jelölje be a **önkiszolgáló karbantartási** elemet, és helyezze be a nyílgombokkal a **kijelölt oszlopok** listája. Válthat a legördülő menüt a **elérhető oszlopok** szakasz a **összes** való **tulajdonságok** győződjön meg arról, hogy a **önkiszolgáló karbantartási** elem egyszerűbb megkereséséhez. Miután a **önkiszolgáló karbantartási** elemnek, a **kijelölt oszlopok** szakaszban jelölje be a **alkalmaz** gombra a lap alján. 

A fenti lépések végrehajtása után a **önkiszolgáló karbantartási** oszlop jelenik meg a virtuálisgép-méretezési csoportok listája. Minden egyes virtuálisgép-méretezési csoportot az önkiszolgáló karbantartás oszlop a következő értékek egyike lehet:

| Érték | Leírás |
|-------|-------------|
| Igen | A virtuálisgép-méretezési csoportban lévő legalább egy virtuális gép van egy önkiszolgáló ablakban. Az önkiszolgáló időszak alatt bármikor elindítható a karbantartás. | 
| Nem | Nincsenek nincsenek olyan virtuális gépek, amelyek az érintett virtuálisgép-méretezési csoport egy önkiszolgáló ablakban beállítva. | 
| - | A virtuális gépek méretezési csoportjai nem részei egy tervezett karbantartási műveletek.| 

## <a name="notification-and-alerts-in-the-portal"></a>Értesítések és riasztások a portálon

Az Azure tervezett karbantartási ütemezés egy e-mailt küld az előfizetés tulajdonosa és a társtulajdonosok csoport kommunikál. Adhat hozzá további címzettek és csatornák a kommunikáció Azure-tevékenységi riasztások létrehozásával. További információkért lásd: [előfizetési tevékenység figyelése az Azure-tevékenységnapló] (.. / monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza **figyelő**. 
3. Az a **figyelő – riasztások (klasszikus)** ablaktáblán kattintson a **+ tevékenységnapló-riasztás hozzáadása**.
4. Töltse a **tevékenységnapló-riasztás hozzáadása** lapon és ellenőrizze, hogy állítsa be a következő **feltételek**:
   - **Eseménykategória**: szolgáltatásának állapota
   - **Szolgáltatások**: Virtual Machine Scale Sets és a virtuális gépek
   - **Típus**: tervezett karbantartás 
    
A tevékenységnapló-riasztások konfigurálásának további tudnivalókért lásd: [tevékenységnapló-riasztások létrehozása](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Karbantartás indítása a portálról a virtuális gép méretezési

A virtual machine scale sets áttekintése megtekintése közben további karbantartási látni fogja kapcsolatos részleteket. Ha a virtuálisgép-méretezési csoportban lévő legalább egy virtuális gép a tervezett karbantartás szerepel, egy új értesítési menüszalag bekerül a lap tetején. Nyissa meg az értesítési lehetőségre is kattinthat a **karbantartási** oldalt, hogy melyik virtuálisgép-példány a tervezett karbantartás hatással van. 

Itt a mezőbe az érintett virtuális gépnek megfelelő, és kattintson a karbantartási indul el fogja **indítsa el a karbantartási** lehetőséget.

Amikor hozzákezd a karbantartás, az érintett virtuális gépek a virtuális gép méretezési karbantartás, és átmenetileg nem érhető el. Ha lemaradt az önkiszolgáló ablak, akkor továbbra is képesek lesznek az ablakban megtekintheti, ha a virtuális gép méretezési továbbra is ugyanúgy működik, az Azure-ban.
 
## <a name="check-maintenance-status-using-powershell"></a>PowerShell-lel karbantartási állapot ellenőrzése

Azure Powershell használatával tekintse meg, ha a virtuálisgép-méretezési csoportok virtuális gépeinek ütemezett karbantartás. Tervezett karbantartás információ érhető el a [a Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) parancsmag használatakor a `-InstanceView` paraméter.
 
Tervezett karbantartás esetén csak karbantartási információk jelennek-e. Nincs karbantartás ütemezve van, amely hatással van a Virtuálisgép-példány, ha a parancsmag nem ad vissza semmilyen karbantartási információt. 

```powershell
Get-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

A MaintenanceRedeployStatus visszaadott a következő tulajdonságokkal: 
| Érték | Leírás   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy elindítható a karbantartás a virtuális gép jelenleg ||
| PreMaintenanceWindowStartTime         | A karbantartási önkiszolgáló időszak elején amikor karbantartási is kezdeményezhet a virtuális Gépen ||
| PreMaintenanceWindowEndTime           | Önkiszolgáló időszak során a virtuális Gépen kezdeményezheti a karbantartást vége ||
| MaintenanceWindowStartTime            | Az ütemezett karbantartási elején, amelyben Azure kezdeményez a virtuális gép karbantartását ||
| MaintenanceWindowEndTime              | Az ütemezett karbantartási időszak, amelyben Azure kezdeményez a virtuális gép karbantartását vége ||
| LastOperationResultCode               | Kezdeményezheti a karbantartást, a virtuális gép a legutóbbi kísérlet eredménye ||



### <a name="start-maintenance-on-your-vm-instance-using-powershell"></a>Karbantartás indítása a Virtuálisgép-példány PowerShell-lel

Karbantartási indítsa el a virtuális gép Ha **IsCustomerInitiatedMaintenanceAllowed** értéke igaz használatával [Set-azurermvmss parancsmagban](/powershell/module/azurerm.compute/set-azurermvmss) parancsmagot `-PerformMaintenance` paraméter.

```powershell
Set-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-using-cli"></a>Karbantartási állapot ellenőrzése parancssori felület használatával

Tervezett karbantartás információk elemének segítségével [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Tervezett karbantartás esetén csak karbantartási információk jelennek-e. Ha nincs karbantartás ütemezve van, amely hatással van a Virtuálisgép-példány, a parancs nem ad vissza semmilyen karbantartási információt. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Az alábbi tulajdonságokat adja vissza csoportban MaintenanceRedeployStatus minden egyes Virtuálisgép-példány: 
| Érték | Leírás   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy elindítható a karbantartás a virtuális gép jelenleg ||
| PreMaintenanceWindowStartTime         | A karbantartási önkiszolgáló időszak elején amikor karbantartási is kezdeményezhet a virtuális Gépen ||
| PreMaintenanceWindowEndTime           | Önkiszolgáló időszak során a virtuális Gépen kezdeményezheti a karbantartást vége ||
| MaintenanceWindowStartTime            | Az ütemezett karbantartási elején, amelyben Azure kezdeményez a virtuális gép karbantartását ||
| MaintenanceWindowEndTime              | Az ütemezett karbantartási időszak, amelyben Azure kezdeményez a virtuális gép karbantartását vége ||
| LastOperationResultCode               | Kezdeményezheti a karbantartást, a virtuális gép a legutóbbi kísérlet eredménye ||


### <a name="start-maintenance-on-your-vm-instance-using-cli"></a>Karbantartás indítása a Virtuálisgép-példány parancssori felület használatával

A következő hívást kezdeményez a karbantartási egy Virtuálisgép-példány, ha `IsCustomerInitiatedMaintenanceAllowed` értéke igaz.

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>GYIK


**K: Miért van szüksége, a virtual machines Újraindítás most?**

**V:** közben a frissítéseket és az Azure platform többsége nem érinti a virtuális gép rendelkezésre állási, vannak esetek, hogy nem azzal elkerülheti az Azure-ban üzemeltetett virtuális gépek. A Microsoft rendelkezik halmozott számos, szükség lehet, hogy a virtuális gépek újraindításával jár újra módosítást.

**K: Ha a rendelkezésre állási csoport használatával I kövesse a magas rendelkezésre állás érdekében a javaslatok, vagyok a biztonságos?**

**V:** állítsa be a rendelkezésre állási üzembe helyezett virtuális gépek vagy virtuálisgép-méretezési csoportok rendelkezik az a legvonzóbb Garantáltnak frissítési tartományok. A karbantartás során az Azure figyelembe veszi az UD-kényszert, és nem fogja újraindítani a virtuális gépek más frissítési tartományhoz tartozó (belül az azonos rendelkezésre állási csoport).  Az Azure is megvárja, amíg legalább 30 perc a következő csoporthoz a virtuális gépek áthelyezése előtt. 

Magas rendelkezésre állású kapcsolatos további információkért lásd: [régiók és rendelkezésre állás az Azure-beli virtuális gépek](../virtual-machines/windows/regions-and-availability.md).

**K: hogyan tegye szeretnék értesítést kaphat a tervezett karbantartásról?**

**V:** tervezett karbantartási műveletek úgy, hogy egy vagy több Azure-régiók ütemezés szerint kezdődik. Hamarosan, e-mail-értesítés elküldése után az előfizetés tulajdonosainak (előfizetésenként egy e-mail címet). További csatornák és az értesítés címzettjeit sikerült konfigurálni a tevékenységnapló-riasztások segítségével. Abban az esetben, ha telepít egy virtuális gépet egy régióban, ahol már ütemezve van a tervezett karbantartás, nem kapják meg az értesítést, de nem kell a virtuális gép a karbantartás állapotának ellenőrzése.

**K: nem látom a végrehajtás a tervezett karbantartás a portálon, a Powershell vagy parancssori felület. mi a baj?**

**V:** tervezett karbantartáshoz kapcsolódó információk érhetők el a virtuális gépek, amelyek hatással lehet, csak a tervezett karbantartási műveletek során. Más szóval ha adatokat nem jelenik meg, annak oka az lehet, hogy a karbantartás már befejeződött (vagy nem kezdődött el), illetve, hogy a virtuális gép már üzemel frissített kiszolgálón.

**K: van mód, pontosan akkor, ha a virtuális gép érint?**

**V:** meghatározzuk, ha az ütemezés beállítása, egy olyan időkeretet, több napig. Azonban ezen időszakon belül kiszolgálók (és virtuális gépek) pontos karbantartási ütemezése nem ismert. Pontosan tudni, hogy a virtuális gépek kívánó ügyfelek használhatják a [ütemezett események](../virtual-machines/windows/scheduled-events.md) és kérdezheti le a virtuális gép és a egy 15 perces értesítés a virtuális gép újraindítása előtt.

**K: mennyi ideig tart a virtuális gép újraindítását?**

**V:** a virtuális gép méretétől függően újraindítás is igénybe vehet néhány percig, az önkiszolgáló karbantartási időszak alatt. Során az Azure által kezdeményezett újraindítása az ütemezett karbantartási időszakban, az újraindítás általában percet vesz igénybe körülbelül 25. Vegye figyelembe, hogy abban az esetben a Cloud Services (webes/feldolgozói szerepkört), a virtuálisgép-méretezési csoportok vagy a rendelkezésre állási csoportok, egyúttal 30 perc közötti minden csoport a virtuális gépek Garantáltnak az ütemezett karbantartási időszak alatt. 

**K: nem látom minden karbantartási információt saját virtuális gépeken. Mi történt?**

**V:** számos oka lehet, miért nem lát minden karbantartási információt a virtuális gépeken:
   - Belső Microsoft megjelölve előfizetést használ.
   - A virtuális gépek nem ütemezett karbantartás céljából. Annak oka az lehet, hogy a karbantartás befejeződött, megszakított vagy módosítható úgy, hogy a virtuális gépek már nem érinti azt.
   - Nem rendelkezik a **karbantartási** a virtuális gép listanézetre felvett oszloppal. Ezt az oszlopot hozzáadtuk az alapértelmezett nézethez, miközben az ügyfelek, akik a nem alapértelmezett oszlopok megjelenítéséhez manuálisan kell hozzáadni a **karbantartási** oszlop a virtuális gépek listanézetéhez.

**K: a virtuális gép karbantartásra van ütemezve a második alkalommal. Hogy miért?**

**V:** nincsenek több felhasználási eset, ahol láthatja a virtuális gép a karbantartás után már végrehajtotta a karbantartással és ismételt üzembe:
   - Hogy megszakadt a karbantartás és újraindul, egy másik hasznos adattal. Annak oka az, hogy hibás hasznos észleltünk, és egyszerűen kell telepíteni egy további hasznos lehet.
   - A virtuális gép volt *kezelte a szolgáltatás* hardverhiba miatt egy másik csomópontra.
   - Választotta, állítsa le (szabadítsa fel), majd indítsa újra a virtuális Gépet.
   - Rendelkezik **automatikus leállítás** engedélyezve van a virtuális gép.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan regisztrálhat a karbantartási események belül a virtuális Gépet az [ütemezett események](../virtual-machines/windows/scheduled-events.md).
