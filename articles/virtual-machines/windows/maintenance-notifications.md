---
title: Windows virtuális gépek az Azure-ban karbantartásával kapcsolatos értesítések kezelése |} A Microsoft Docs
description: Az Azure-ban futó Windows virtuális gépek karbantartásával kapcsolatos értesítések megtekintése, és indítsa el az önkiszolgáló karbantartás.
services: virtual-machines-windows
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/02/2018
ms.author: shants
ms.openlocfilehash: f8cac174844d7f87687d08975b6fbf17ed47b03e
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543291"
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Tervezett karbantartásával kapcsolatos értesítések Windows virtuális gépek kezelése

Az Azure rendszeresen végez frissítéseket a virtuális gépeket futtató infrastruktúra megbízhatóságának, teljesítményének és biztonságának javítása érdekében. A frissítések olyan módosítások, például javítása, a üzemeltetési környezet vagy a frissítés és a hardver leszerelése. Ezek a frissítések többségét annak hatását a futtatott virtuális gépek működésére. Előfordulhatnak azonban olyan esetekben, amikor frissítések hatással:

- A karbantartás nem igényel újraindítást, ha az Azure a virtuális gép felfüggesztése, amíg frissül a gazdagép helyi áttelepítést használ. Ezek nem rebootful karbantartási műveleteket tartalék tartomány által alkalmazott tartalék tartományt, és folyamatban le van állítva, ha bármilyen figyelmeztetés állapot jelek. 

- Ha karbantartási újraindítás szükséges, egy értesítés, amikor a karbantartás tervezett kap. Ezekben az esetekben, kapnak egy olyan időkeretet, ahol elkezdheti a karbantartás saját magának, mikor működik az Ön számára.


Tervezett karbantartás, amely újraindítást igényel, a regisztrálást megelőző szakaszban tanácsos van ütemezve. Egyes szakaszokban eltérő hatókörbe (régió) rendelkezik.

- A szakasz egy értesítés azoknak az ügyfeleknek kezdődik. Alapértelmezés szerint az előfizetés tulajdonosa és a társtulajdonosok értesítést küld. Hozzáadhat további címzetteket, és e-mailben, SMS és Webhookok, például üzenetküldési lehetőségeket, az értesítéseket az Azure- [tevékenységnapló-riasztások](../../azure-monitor/platform/activity-logs-overview.md).  
- Az értesítés időpontban egy *önkiszolgáló ablak* szeretné elérhetővé tenni. Ezen időszak alatt található, amely a virtuális gépek szerepelnek a wave, és proaktív módon indítsa el a karbantartási ütemezés igény szerint.
- Az önkiszolgáló ablak után egy *ütemezett karbantartási időszaknál* kezdődik. Ebben az ablakban egy pontján az Azure ütemezi, és a szükséges karbantartás vonatkozik a virtuális gép. 

A két windows kellene célja, hogy indítsa el a karbantartást, és a virtuális gép újraindítása közben, hogy ha az Azure automatikusan elindul, karbantartási elegendő időt biztosítanak.


Az Azure portal, PowerShell, REST API-t és parancssori felület segítségével lekérdezheti a karbantartási időszakok a virtuális gépek számára, és indítsa el az önkiszolgáló karbantartás.

  
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
- Ha az ütemezett események, amelyekkel proaktív feladatátvételi vagy a számítási feladatok leállításának karbantartási leállítás kezdete előtt 15 perccel elfogadott

**Használat** az önkiszolgáló karbantartás, ha tervez futtatni a virtuális gép zavartalan az ütemezett karbantartás során, és a fent említett másik jelzések sem alkalmazható. 

Célszerű használni az önkiszolgáló karbantartás a következő esetekben:

- Szeretne kommunikálni a felügyeleti vagy végfelhasználói egy pontos karbantartási időszak. 
- Egy adott időpontig a karbantartás végrehajtásához szükséges. 
- Kell sorrendjét szabályozhatja a karbantartás, például több rétegből álló alkalmazás biztonságos helyreállítás biztosítása érdekében.
- Két frissítési tartomány (frissítési) között a virtuális gép helyreállítási idő több mint 30 percnyi van szüksége. A frissítési tartományok között eltelt idő szabályozásához indít el a kell karbantartási a virtuális gépek egy frissítési tartomány (UD) egyszerre.

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>PowerShell-lel karbantartási állapot ellenőrzése

Azure Powershell használatával látják, amikor a karbantartásra ütemezett virtuális gépek. Tervezett karbantartás információ érhető el a [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) parancsmag használatakor a `-status` paraméter.
 
Tervezett karbantartás esetén csak karbantartási információk jelennek-e. Ha nincs karbantartás ütemezve van, amely hatással van a virtuális Gépet, a parancsmag nem ad vissza semmilyen karbantartási információt. 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
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



Akkor is kaphat a karbantartási állapot egy erőforráscsoportba tartozó összes virtuális gép használatával [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) , és nem adja meg a virtuális gép.
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName -Status
```

A következő PowerShell-függvény vesz igénybe az előfizetés-Azonosítóját, és kiírja a karbantartásra ütemezett virtuális gépek listáját.

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Karbantartás indítása a virtuális gép PowerShell-lel

A függvény információk alapján az előző szakaszban a következő indítása karbantartása a virtuális gép Ha **IsCustomerInitiatedMaintenanceAllowed** értéke igaz.

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Klasszikus üzembe helyezések

Ha még régebbi virtuális gépek, amelyek korábban a klasszikus üzemi modellel üzembe helyezett is lekérdezés-beli virtuális gépek PowerShell használatával és kezdeményezheti a karbantartást.

A virtuális gépek karbantartási állapot lekéréséhez írja be:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

A klasszikus virtuális gép karbantartási indításához írja be:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>GYIK


**KÉRDÉS: Miért van szüksége, a virtual machines Újraindítás most?**

**VÁLASZ:** A frissítéseket és az Azure platform többsége nem érinti a virtuális gép rendelkezésre állási, amíg vannak esetek, hogy nem azzal elkerülheti az Azure-ban üzemeltetett virtuális gépek. A Microsoft rendelkezik halmozott számos, szükség lehet, hogy a virtuális gépek újraindításával jár újra módosítást.

**KÉRDÉS: Ha a rendelkezésre állási csoport I követi a magas rendelkezésre állás érdekében a javaslatok, vagyok a biztonságos?**

**VÁLASZ:** A rendelkezésre állási csoportban vagy virtuálisgép-méretezési csoportban üzembe helyezett virtuális gépek frissítési tartományokhoz (Update Domains, UD) tartoznak. A karbantartás során az Azure figyelembe veszi az UD-kényszert, és nem fogja újraindítani a virtuális gépek más frissítési tartományhoz tartozó (belül az azonos rendelkezésre állási csoport).  Az Azure is megvárja, amíg legalább 30 perc a következő csoporthoz a virtuális gépek áthelyezése előtt. 

Magas rendelkezésre állású kapcsolatos további információkért lásd: [régiók és rendelkezésre állás az Azure-beli virtuális gépek](regions-and-availability.MD).

**KÉRDÉS: Hogyan tegye szeretnék értesítést kaphat a tervezett karbantartásról?**

**VÁLASZ:** Tervezett karbantartási műveletek úgy, hogy egy vagy több Azure-régiók ütemezés szerint kezdődik. Hamarosan, e-mail-értesítés elküldése után az előfizetés tulajdonosainak (előfizetésenként egy e-mail címet). További csatornák és az értesítés címzettjeit sikerült konfigurálni a tevékenységnapló-riasztások segítségével. Abban az esetben, ha telepít egy virtuális gépet egy régióban, ahol már ütemezve van a tervezett karbantartás, nem kapják meg az értesítést, de nem kell a virtuális gép a karbantartás állapotának ellenőrzése.

**KÉRDÉS: Bármely a portal, Powershell vagy parancssori felület a tervezett karbantartásra utaló jel nem látható. mi a baj?**

**VÁLASZ:** Tervezett karbantartással kapcsolatos adatokat csak a virtuális gépek, amelyek hatással lehet, hogy a tervezett karbantartási műveletek során érhető el. Más szóval ha adatokat nem jelenik meg, annak oka az lehet, hogy a karbantartás már befejeződött (vagy nem kezdődött el), illetve, hogy a virtuális gép már üzemel frissített kiszolgálón.

**KÉRDÉS: Van mód, pontosan akkor, ha a virtuális gép érint?**

**VÁLASZ:** Az ütemezés beállítása, ha egy olyan időkeretet, több napig meghatározzuk. Azonban ezen időszakon belül kiszolgálók (és virtuális gépek) pontos karbantartási ütemezése nem ismert. Pontosan tudni, hogy a virtuális gépek kívánó ügyfelek használhatják a [ütemezett események](scheduled-events.md) és kérdezheti le a virtuális gép és a egy 15 perces értesítés a virtuális gép újraindítása előtt.

**KÉRDÉS: Mennyi ideig tart a virtuális gép újraindítását?**

**VÁLASZ:**  A virtuális gép méretétől függően az újraindítás a önkiszolgáló karbantartási időszak alatt több percig is eltarthat. Során az Azure által kezdeményezett újraindítása az ütemezett karbantartási időszakban, az újraindítás általában percet vesz igénybe körülbelül 25. Vegye figyelembe, hogy abban az esetben a Cloud Services (webes/feldolgozói szerepkört), a virtuálisgép-méretezési csoportok vagy a rendelkezésre állási csoportok, egyúttal 30 perc közötti minden csoport a virtuális gépek Garantáltnak az ütemezett karbantartási időszak alatt. 

**KÉRDÉS: Mi az a Virtual Machine Scale Sets karbantartása?**

**VÁLASZ:** Tervezett karbantartás már elérhető a Virtual Machine Scale Sets. Önkiszolgáló karbantartást kezdeményez útmutatást lásd [tervezett karbantartás az vmss-hez](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) dokumentumot.

**KÉRDÉS: Mi a Cloud Services (webes/feldolgozói szerepkör) és a Service Fabric karbantartása?**

**VÁLASZ:** Bár a tervezett karbantartás érinti ezeket a platformokat, az ezeket használó ügyfelek biztonsága garantáltnak tekinthető, mivel egy adott időpontban kizárólag egyetlen frissítési tartomány virtuális gépeit érinti a frissítés. Az önkiszolgáló karbantartás jelenleg nem érhető el a Cloud Services (webes/feldolgozói szerepkör) és a Service Fabric.

**KÉRDÉS: A virtuális gépek nem látható semmilyen karbantartási információt. Mi történt?**

**VÁLASZ:** Miért nem lát minden karbantartási információt a virtuális gépeken több oka is van:
1.  Belső Microsoft megjelölve előfizetést használ.
2.  A virtuális gépek nem ütemezett karbantartás céljából. Annak oka az lehet, hogy a karbantartás befejeződött, megszakított vagy módosítható úgy, hogy a virtuális gépek már nem érinti azt.
3.  Nem rendelkezik a **karbantartási** a virtuális gép listanézetre felvett oszloppal. Ezt az oszlopot hozzáadtuk az alapértelmezett nézethez, miközben az ügyfelek, akik a nem alapértelmezett oszlopok megjelenítéséhez manuálisan kell hozzáadni a **karbantartási** oszlop a virtuális gépek listanézetéhez.

**KÉRDÉS: A virtuális gépem második karbantartásra van ütemezve. Hogy miért?**

**VÁLASZ:** Több felhasználási eset van, amikor a virtuális gép a karbantartás utáni ismételt üzembe helyezés után is karbantartásra ütemezettként jelenik meg:
1.  Hogy megszakadt a karbantartás és újraindul, egy másik hasznos adattal. Annak oka az, hogy hibás hasznos észleltünk, és egyszerűen kell telepíteni egy további hasznos lehet.
2.  A virtuális gép volt *kezelte a szolgáltatás* hardverhiba miatt egy másik csomópontra
3.  Választotta, állítsa le (szabadítsa fel), majd indítsa újra a virtuális gép
4.  Rendelkezik **automatikus leállítás** engedélyezve van a virtuális gép


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan regisztrálhat a karbantartási események belül a virtuális Gépet az [ütemezett események](scheduled-events.md).
