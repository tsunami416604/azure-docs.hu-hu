---
title: Karbantartási értesítések az Azure-beli virtuálisgép-méretezési csoportokhoz
description: Tekintse meg a karbantartási értesítéseket, és indítsa el az önkiszolgáló karbantartást a virtuálisgép-méretezési készletekhez az Azure-ban.
author: shants123
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: shants
ms.openlocfilehash: 53ebb7c4710c5455ef90701dc7e94f1b846a874a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062705"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok – tervezett karbantartás értesítései


Az Azure rendszeres időközönként frissítéseket hajt végre a virtuális gépek gazdainfrastruktúrájának megbízhatóságának, teljesítményének és biztonságának javítása érdekében. A frissítések közé tartozhat az üzemeltetési környezet javítása, illetve a hardver frissítése és leszerelése. A legtöbb frissítés nincs hatással a tárolt virtuális gépek. A frissítések azonban a következő esetekben érintik a virtuális gépeket:

- Ha a karbantartás nem igényel újraindítást, az Azure a virtuális gép szüneteltetéséhez a virtuális gép, amíg az állomás frissül. Az újraindítást nem igénylő karbantartási műveleteket a tartalék tartomány tartalék tartományonként alkalmazza. A folyamat leáll, ha bármilyen figyelmeztető állapotjelzés érkezik.

- Ha a karbantartás újraindítást igényel, értesítést kap akarbantartás tervezett időpontjáról. Ezekben az esetekben kap egy időablakot, amely általában 35 nap, ahol el indíthatja a karbantartást saját maga, amikor működik az Ön számára.


Az újraindítást igénylő tervezett karbantartás hullámokban van ütemezve. Minden hullám nak különböző hatálya van (régiók):

- A hullám az ügyfelek értesítésével kezdődik. Alapértelmezés szerint értesítést küld az előfizetés tulajdonosa és társtulajdonosai. Az Azure [Activity Log-riasztások](../azure-monitor/platform/platform-logs-overview.md)használatával hozzáadhat címzetteket és üzenetküldési beállításokat, például e-maileket, SMS-eket és webhookokat az értesítésekhez.  
- Értesítéssel *egy önkiszolgáló ablak* is elérhetővé válik. Ebben az ablakban, amely általában 35 nap, megtudhatja, hogy melyik a virtuális gépek szerepelnek a hullám. Proaktív módon megkezdheti a karbantartást a saját ütemezési igényeinek megfelelően.
- Az önkiszolgáló ablak után megkezdődik az *ütemezett karbantartási időszak.* Egy bizonyos ponton ebben az ablakban az Azure ütemezi, és alkalmazza a szükséges karbantartást a virtuális gépre. 

A cél a két ablak, hogy elegendő időt a karbantartás elindításához és újraindításához a virtuális gép, miközben tudja, mikor az Azure automatikusan elindítja a karbantartást.

Az Azure Portalon, a PowerShell, a REST API-t és az Azure CLI-t használhatja a virtuális gép méretezési csoportba állított virtuális gépek karbantartási időszakai lekérdezéséhez, valamint az önkiszolgáló karbantartás elindításához.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Az önkiszolgáló ablakban kezdje el a karbantartást?  

Az alábbi irányelvek segítségével eldöntheti, hogy a karbantartást a kiválasztott időpontban kezdi-e el.

> [!NOTE] 
> Előfordulhat, hogy az önkiszolgáló karbantartás nem érhető el az összes virtuális géphez. Annak megállapításához, hogy a proaktív újratelepítés elérhető-e a virtuális géphez, keresse meg a **Start now-t** a karbantartási állapotban. Jelenleg az önkiszolgáló karbantartás nem érhető el az Azure Cloud Services (web/feldolgozó szerepkör) és az Azure Service Fabric esetében.


Az önkiszolgáló karbantartás nem ajánlott a *rendelkezésre állási csoportokat*használó központi telepítésekhez. A rendelkezésre állási készletek magas rendelkezésre állású beállítások, amelyekben egyszerre csak egy frissítési tartomány érintett. Rendelkezésre állási csoportok esetén:

- Hagyja, hogy az Azure indítsa el a karbantartást. Az újraindítást igénylő karbantartás esetén a karbantartás frissítési tartományonként történik. A frissítési tartományok nem feltétlenül kapják meg egymás után a karbantartást. A frissítési tartományok között 30 perces szünet van.
- Ha a kapacitás egy részének (1/frissítési tartományszám) ideiglenes elvesztése aggodalomra ad okot, könnyen kompenzálhatja a veszteséget további példányok hozzárendelésével a tartalékolási időszak alatt.
- A karbantartás, amely nem igényel újraindítást, frissítések a tartalék tartomány szintjén. 
    
**Ne** használjon önkiszolgáló karbantartást a következő esetekben: 

- Ha gyakran leállítja a virtuális gépeket, manuálisan, a DevTest Labs használatával, az automatikus leállítás használatával vagy az ütemezés követésével. Önkiszolgáló karbantartás ezekben a forgatókönyvekben előfordulhat, hogy visszaállítja a karbantartási állapotot, és további állásidőt okozhat.
- A rövid életű virtuális gépek, amelyekről tudja, törlődnek a karbantartási hullám vége előtt. 
- A helyi (rövid élettartamú) lemezen tárolt nagy állapotú munkaterhelések esetén, amelyeket a frissítés után szeretne karbantartani. 
- Ha gyakran átméretezi a virtuális gép. Ez a forgatókönyv visszaállíthatja a karbantartási állapotot. 
- Ha olyan ütemezett eseményeket fogadott el, amelyek lehetővé teszik a proaktív feladatátvételt vagy a munkaterhelés kecses leállítását 15 perccel a karbantartás leállítása előtt.

**Használja** önkiszolgáló karbantartást, ha azt tervezi, hogy a virtuális gép megszakítás nélkül fut az ütemezett karbantartási fázisban, és az előző ellenjavallatok egyike sem vonatkozik. 

A legjobb, ha önkiszolgáló karbantartást használ a következő esetekben:

- Pontos karbantartási időszakot kell közölnie a vezetőséggel vagy az ügyféllel. 
- A karbantartást egy adott dátumig kell elvégeznie. 
- A biztonságos helyreállítás biztosítása érdekében szabályoznia kell a karbantartás sorrendjét, például egy többrétegű alkalmazásban.
- Több mint 30 perc virtuális gép helyreállítási idő két frissítési tartományok között. A frissítési tartományok közötti idő szabályozásához a virtuális gépek karbantartását egyszerre egy frissítési tartományban kell aktiválnia.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>A portálon a karbantartás által érintett virtuálisgép-méretezési készletek megtekintése

Ha egy tervezett karbantartási hullám van ütemezve, megtekintheti a virtuális gép méretezési készletek listáját, amelyek a közelgő karbantartási hullám által érintett az Azure Portalhasználatával. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A bal oldali menüben válassza a **Minden szolgáltatás**lehetőséget, majd válassza a **Virtuálisgép-méretezési csoportok lehetőséget.**
3. A **Virtuálisgép-méretezési készletek**csoportban jelölje be az **Oszlopok szerkesztése jelölőnégyzetet** az elérhető oszlopok listájának megnyitásához.
4. A **Rendelkezésre álló oszlopok** csoportban válassza az **Önkiszolgáló karbantartás**lehetőséget, majd helyezze át a Kijelölt **oszlopok** listára. Kattintson az **Alkalmaz** gombra.  

    Az **Önkiszolgáló karbantartási** cikk könnyebb megtalálásához módosítsa a Legördülő menü legördülő beállítását a **Mindenki** **és** **tulajdonságok**szakaszban.

Az **Önkiszolgáló karbantartás** oszlop most megjelenik a virtuálisgép-méretezési csoportok listájában. Minden virtuálisgép-méretezési csoport rendelkezhet az alábbi értékek egyikével az önkiszolgáló karbantartási oszlophoz:

| Érték | Leírás |
|-------|-------------|
| Igen | Legalább egy virtuális gép a virtuális gép méretezési készletében egy önkiszolgáló ablakban. Ebben az önkiszolgáló időszakban bármikor elkezdheti a karbantartást. | 
| Nem | Az érintett virtuálisgép-méretezési csoportban nincsenek virtuális gépek az önkiszolgáló ablakban. | 
| - | A virtuális gépek méretezési készletei nem részei egy tervezett karbantartási hullámnak.| 

## <a name="notification-and-alerts-in-the-portal"></a>Értesítések és riasztások a portálon

Az Azure közli a tervezett karbantartás ütemezését azáltal, hogy e-mailt küld az előfizetés tulajdonosának és társtulajdonosai csoportnak. A kommunikációhoz a tevékenységek naplójának riasztásai segítségével adhat hozzá címzetteket és csatornákat. További információ: [Előfizetési tevékenység figyelése az Azure-tevékenységnaplóval.](../azure-monitor/platform/platform-logs-overview.md)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A bal oldali menüben válassza a **Monitor**lehetőséget. 
3. A **Figyelő – Riasztások (klasszikus)** ablaktáblán válassza a **+Tevékenységnapló-riasztás hozzáadása**lehetőséget.
4. A **Tevékenységnapló riasztásának hozzáadása** lapon jelölje ki vagy adja meg a kért adatokat. A **Feltétel területen**győződjön meg arról, hogy a következő értékeket állította be:
   - **Esemény kategória**: Válassza a **Szolgáltatás állapota**lehetőséget.
   - **Szolgáltatások**: Válassza ki **a virtuális gép méretezhető készleteit és a virtuális gépeket.**
   - **Típus**: Válassza **a Tervezett karbantartás**lehetőséget. 
    
Ha többet szeretne tudni a tevékenységnapló-riasztások konfigurálásáról, olvassa el a [Tevékenységnapló-riasztások létrehozása című témakört.](../azure-monitor/platform/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>A virtuálisgép-méretezési csoport karbantartásának megkezdése a portálról

A karbantartással kapcsolatos további részleteket a virtuálisgép-méretezési csoportok áttekintésében tekintheti meg. Ha a virtuális gép méretezési készletében legalább egy virtuális gép szerepel a tervezett karbantartási hullámban, a lap tetején egy új értesítési menüszalag kerül hozzáadásra. Válassza ki az értesítési menüszalagot a **Karbantartás** lapra való ugráshoz. 

A Karbantartás lapon **láthatja,** hogy melyik virtuálisgép-példányt érinti a tervezett karbantartás. A karbantartás megkezdéséhez jelölje be az érintett virtuális gépnek megfelelő jelölőnégyzetet. Ezután válassza a **Karbantartás indítása lehetőséget.**

A karbantartás megkezdése után a virtuális gép méretezési készletében lévő érintett virtuális gépek karbantartáson mennek keresztül, és átmenetileg nem érhetők el. Ha kihagyta az önkiszolgáló ablakot, továbbra is láthatja az időablakot, amikor a virtuális gép méretezési készletét az Azure karbantartja.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Karbantartási állapot ellenőrzése a PowerShell használatával

Az Azure PowerShell segítségével megtekintheti, hogy a virtuális gép méretezési csoportjaiban lévő virtuális gépek karbantartásra vannak ütemezve. A tervezett karbantartási információk a [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) parancsmag használatával `-InstanceView` érhetők el a paraméter használatakor.
 
A karbantartási adatokat csak akkor adja vissza, ha a karbantartást tervezik. Ha nincs karbantartás ütemezve, amely hatással van a virtuális gép példányát, a parancsmag nem ad vissza karbantartási információkat. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

A **maintenanceredeploystatus**alatt a következő tulajdonságok kerülnek visszaadásra: 

| Érték | Leírás   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy a virtuális gép jelenleg elindíthatja-e a karbantartást. |
| Karbantartás előttiablak-kezdetideje         | A karbantartási önkiszolgáló ablak kezdete, amikor karbantartást kezdeményezhet a virtuális gépen. |
| Karbantartás előttiablakbefejezési idő           | A karbantartási önkiszolgáló ablak végén, amikor a virtuális gép karbantartást kezdeményezhet. |
| MaintenanceWindowStarttime karbantartás            | Az ütemezett karbantartás kezdete, amelyben az Azure karbantartást kezdeményez a virtuális gépen. |
| MaintenanceWindowEndTime              | A karbantartási ütemezett ablak vége, amelyben az Azure karbantartást kezdeményez a virtuális gépen. |
| LastOperationResultKód               | Az utolsó kísérlet a karbantartás kezdeményezésére a virtuális gépen. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Karbantartás indítása a virtuálisgép-példányon a PowerShell használatával

A karbantartást elindíthatja egy virtuális gépen, ha az **IsCustomerInitiatedMaintenanceAllowed** **értéke true.** Használja a [Set-AzVmss](/powershell/module/az.compute/set-azvmss) parancsmag paraméter. `-PerformMaintenance`

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>A karbantartási állapot ellenőrzése a CLI használatával

A tervezett karbantartási információkat [az az vmss list-példányok](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances)használatával tekintheti meg.
 
A karbantartási adatokat csak akkor adja vissza, ha a karbantartást tervezik. Ha nincs karbantartás, amely befolyásolja a virtuális gép példány van ütemezve, a parancs nem ad vissza karbantartási információkat. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

A következő tulajdonságok at ad vissza **maintenanceredeploystatus** minden virtuális gép példány: 

| Érték | Leírás   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy a virtuális gép jelenleg elindíthatja-e a karbantartást. |
| Karbantartás előttiablak-kezdetideje         | A karbantartási önkiszolgáló ablak kezdete, amikor karbantartást kezdeményezhet a virtuális gépen. |
| Karbantartás előttiablakbefejezési idő           | A karbantartási önkiszolgáló ablak végén, amikor a virtuális gép karbantartást kezdeményezhet. |
| MaintenanceWindowStarttime karbantartás            | Az ütemezett karbantartás kezdete, amelyben az Azure karbantartást kezdeményez a virtuális gépen. |
| MaintenanceWindowEndTime              | A karbantartási ütemezett ablak vége, amelyben az Azure karbantartást kezdeményez a virtuális gépen. |
| LastOperationResultKód               | Az utolsó kísérlet a karbantartás kezdeményezésére a virtuális gépen. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>A karbantartás indítása a virtuálisgép-példányon a CLI használatával

A következő hívás karbantartást kezdeményez egy `IsCustomerInitiatedMaintenanceAllowed` virtuálisgép-példányon, ha igaz **értékre**van állítva:

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>GYIK

**K: Miért kell most újraindítani a virtuális gépeimet?**

**A.** Bár az Azure platform legtöbb frissítése és frissítése nincs hatással a virtuális gépek rendelkezésre állására, bizonyos esetekben nem kerülhetjük el az Azure-ban üzemeltetett virtuális gépek újraindítását. Számos olyan módosítást gyűjtöttünk össze, amelyek megkövetelik, hogy újraindítsuk a kiszolgálóinkat, ami a virtuális gép újraindítását eredményezi.

**K: Ha egy rendelkezésre állási csoport használatával követem a magas rendelkezésre állásra vonatkozó javaslatait, biztonságban vagyok?**

**A.** Egy rendelkezésre állási csoportban vagy virtuálisgép-méretezési csoportokban üzembe helyezett virtuális gépek frissítési tartományokat használnak. Karbantartás végrehajtásakor az Azure tiszteletben tartja a frissítési tartomány megkötése, és nem indítja újra a virtuális gépeket egy másik frissítési tartományból (ugyanazon a rendelkezésre állási csoporton belül). Az Azure is vár legalább 30 percet, mielőtt a virtuális gépek következő csoportjába. 

A magas rendelkezésre állásról további információt a [Régiók és a virtuális gépek azure-beli elérhetősége című témakörben talál.](../virtual-machines/windows/availability.md)

**K: Hogyan kaphatok értesítést a tervezett karbantartásról?**

**A.** A tervezett karbantartási hullám egy vagy több Azure-régió ütemezését állítja be. Nem sokkal később e-mailben értesítést küld ünk az előfizetés tulajdonosainak (előfizetésenként egy e-mail). Az értesítéshez a tevékenységnapló-riasztások segítségével adhat hozzá csatornákat és címzetteket. Ha üzembe helyez egy virtuális gép egy olyan régióban, ahol a tervezett karbantartás már ütemezve van, nem kapja meg az értesítést. Ehelyett ellenőrizze a virtuális gép karbantartási állapotát.

**K: Nem látom a tervezett karbantartás jeleit a portálon, a PowerShellben vagy a CLI-ben. mi a baj?**

**A.** A tervezett karbantartással kapcsolatos információk csak a tervezett karbantartás által érintett virtuális gépek tervezett karbantartási időszakban érhetők el. Ha nem látja az adatokat, előfordulhat, hogy a karbantartási hullám már befejeződött (vagy nem indult el), vagy a virtuális gép már üzemeltetve egy frissített kiszolgálón.

**K: Van-e módja annak, hogy pontosan tudja, mikor a virtuális gép érinti?**

**A.** Amikor beállítjuk az ütemtervet, több napos időablakot határozunk meg. Ezen időszakon belül a kiszolgálók (és a virtuális gépek) pontos karbantartási ütemezése nem ismert. Ha tudni szeretné, hogy a virtuális gépek mikor frissülnek, használhatja az [ütemezett eseményeket.](../virtual-machines/windows/scheduled-events.md) Ütemezett események használata esetén lekérdezheti a virtuális gép, és kap egy 15 perces értesítést, mielőtt a virtuális gép újraindítása.

**K: Mennyi ideig tart a virtuális gép újraindítása?**

**A.**  A virtuális gép méretétől függően újraindítás akár néhány percet is igénybe vehet az önkiszolgáló karbantartási időszak alatt. Az Azure által kezdeményezett újraindítások az ütemezett karbantartási időszakban, az újraindítás általában körülbelül 25 percet vesz igénybe. Ha a Felhőszolgáltatások (web/feldolgozó szerepkör), a virtuálisgép-méretezési csoportok vagy a rendelkezésre állási csoportok használata, az ütemezett karbantartási időszak alatt 30 percet kap a virtuális gépek (frissítési tartomány) minden csoportja között. 

**K: Nem jelennek meg karbantartási információk a virtuális gépeken. Mi romlott el?**

**A.** Számos oka lehet annak, hogy nem jelennek meg karbantartási információk a virtuális gépeken:
   - *Microsoft Internal*néven megjelölt előfizetést használ.
   - A virtuális gépek nincsenek ütemezve karbantartásra. Előfordulhat, hogy a karbantartási hullám véget ért, megszakadt, vagy úgy módosult, hogy a virtuális gépek már nem érinti.
   - Nem adja hozzá a **Karbantartás** oszlopot a virtuális gép listájának nézetéhez. Bár ezt az oszlopot hozzáadtuk az alapértelmezett nézethez, ha úgy konfigurálja a nézetet, hogy nem alapértelmezett oszlopokat lásson, manuálisan hozzá kell **adnia** a Karbantartás oszlopot a virtuális gép listanézetéhez.

**K: A virtuális gép a tervek szerint a karbantartás a második alkalommal. miért?**

**A.** Több használati esetben a virtuális gép karbantartásra van ütemezve, miután már befejezte a karbantartást és újraüzembe helyezte:
   - Lefújtuk a karbantartási hullámot, és újraindítottuk egy másik hasznos teherrel. Lehet, hogy hibás hasznos terhet észleltünk, és egyszerűen csak egy további hasznos adatot kell üzembe helyeznünk.
   - A virtuális gép hardverhiba miatt egy másik csomópontra *lett javítva.*
   - Kiválasztotta a virtuális gép leállítását (felszabadítását), és újraindítását.
   - A virtuális **gép automatikus leállítása** be van kapcsolva.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan regisztrálhat karbantartási eseményekre a virtuális gépen az [ütemezett események](../virtual-machines/windows/scheduled-events.md)használatával.
