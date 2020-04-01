---
title: A nemmegfelelőség okainak meghatározása
description: Ha egy erőforrás nem megfelelő, számos oka lehet. Ismerje meg, hogy mi okozta a meg nem felelést.
ms.date: 04/26/2019
ms.topic: how-to
ms.openlocfilehash: c931831ddf3cc727b9861e75969eac3bf00c9e45
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "79264634"
---
# <a name="determine-causes-of-non-compliance"></a>A nemmegfelelőség okainak meghatározása

Ha egy Azure-erőforrás úgy határozott, hogy nem felel meg egy szabályzati szabálynak, hasznos tudni, hogy a szabály melyik része az erőforrás nem felel meg. Azt is hasznos tudni, hogy milyen változás módosította a korábban megfelelő erőforrást, hogy nem megfelelő legyen. Ezeket az információkat kétféleképpen találjuk meg:

> [!div class="checklist"]
> - [A megfelelőség részletei](#compliance-details)
> - [Módosítási előzmények (előzetes verzió)](#change-history)

## <a name="compliance-details"></a>A megfelelőség részletei

Ha egy erőforrás nem megfelelő, az adott erőforrás megfelelőségi részletei a **Szabályzat megfelelősége** lapon érhetők el. A megfelelőségi részletek ablaktábla a következő információkat tartalmazza:

- Erőforrás adatai, például név, típus, hely és erőforrásazonosító
- Az aktuális házirend-hozzárendelés utolsó kiértékelésének megfelelőségi állapota és időbélyege
- Az erőforrás meg nem felelése _okainak_ listája

> [!IMPORTANT]
> Mivel egy nem _megfelelő_ erőforrás megfelelőségi részletei az adott erőforrás tulajdonságainak aktuális értékét mutatják, a felhasználónak **olvasási** művelettel kell rendelkeznie az erőforrás **típusához.** Ha például a _nem megfelelő_ erőforrás **microsoft.compute/virtualMachines,** akkor a felhasználónak rendelkeznie kell a **Microsoft.Compute/virtualMachines/read** művelettel. Ha a felhasználó nem rendelkezik a szükséges művelettel, egy hozzáférési hiba jelenik meg.

A megfelelőségi részletek megtekintéséhez kövesse az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg, és válassza ki a **Szabályzat** elemet.

1. Az **Áttekintés** vagy **a Megfelelőség** lapon válasszon ki egy nem megfelelő **megfelelőségi állapotú** _szabályzatot._

1. A **Házirend-megfelelőség** lap **Erőforrás-megfelelőség** lapján kattintson a jobb gombbal, vagy válassza ki egy erőforrás három pontot egy _nem megfelelő_ **megfelelőségi állapotban.** Ezután válassza **a Megfelelőségi részletek megtekintése lehetőséget.**

   ![Megfelelőségi részletek megtekintése beállítás](../media/determine-non-compliance/view-compliance-details.png)

1. A **Megfelelőség részletei** ablaktábla az erőforrás legutóbbi kiértékelésétől az aktuális házirend-hozzárendelésig származó információkat jeleníti meg. Ebben a példában a **Microsoft.Sql/servers/version** mező _12.0,_ míg a házirend-definíció _14.0-ra_várt . Ha az erőforrás több okból sem megfelelő, mindegyik ezen az ablaktáblán jelenik meg.

   ![A megfelelőség részleteinek ablaktáblája és a meg nem felelés okai](../media/determine-non-compliance/compliance-details-pane.png)

   Egy **auditIfNotExists** vagy **deployIfNotExists** házirend-definíció esetén a részletek tartalmazzák a **details.type** tulajdonságot és a választható tulajdonságokat. A lista megtekintéséhez [lásd: auditIfNotExists tulajdonságok](../concepts/effects.md#auditifnotexists-properties) és [deployIfNotExists tulajdonságokat.](../concepts/effects.md#deployifnotexists-properties) **Az utolsó kiértékelt erőforrás** a definíció **részletek** szakaszából származó kapcsolódó erőforrás.

   Példa részleges **deployIfNotExists** definícióra:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![Megfelelőségi részletek ablaktábla - *ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Az adatok védelme _érdekében,_ ha egy tulajdonságérték titkos, az aktuális érték csillagokat jelenít meg.

Ezek a részletek azt magyarázzák, hogy egy erőforrás miért nem megfelelő, de nem jelenik meg, ha a módosítás történt az erőforráson, amely miatt nem megfelelővé vált. Erről lásd alább [a Módosítási előzmények (Előzetes verzió)](#change-history) című témakört.

### <a name="compliance-reasons"></a>Megfelelési okok

A következő mátrix a házirend-definícióban minden lehetséges _okot_ a felelős [feltételhez](../concepts/definition-structure.md#conditions) rendel:

|Ok | Állapot |
|-|-|
|Az aktuális értéknek kulcsként kell tartalmaznia a célértéket. |containsKey vagy **notContainsKey** |
|Az aktuális értéknek tartalmaznia kell a célértéket. |tartalmaz vagy **nemtartalmaz** |
|Az aktuális értéknek egyenlőnek kell lennie a célértékkel. |egyenlő vagy **nemEgyenlő** |
|Az aktuális értéknek kisebbnek kell lennie a célértéknél. |kevesebb vagy **nem** nagyobbOrEquals |
|Az aktuális értéknek nagyobbnak vagy egyenlőnek kell lennie a célértékkel. |greaterOrEquals vagy **nem** kevesebb |
|Az aktuális értéknek nagyobbnak kell lennie a célértéknél. |nagyobb vagy **nem** kevesebbOrEquals |
|Az aktuális értéknek kisebbnek vagy egyenlőnek kell lennie a célértékkel. |lessOrEquals vagy **nem** nagyobb |
|Az aktuális értéknek léteznie kell. |Létezik |
|Az aktuális értéknek a célértékben kell lennie. |vagy **nemIn** |
|Az aktuális értéknek hasonlónak kell lennie a célértékhez. |tetszik vagy **nemTetszik** |
|Az aktuális értéknek meg kell egyeznie a kis- és nagybetűk megkülönböztetésével. |egyezés vagy **nemMatch** |
|Az aktuális értéknek meg kell egyeznie a kis- és nagybetűk kel. |matchÉrzéketlenül vagy **not** nemMatchInsensitively |
|Az aktuális érték nem tartalmazhatja a célértéket kulcsként. |notContainsKey vagy **nem** tartalmazzaKey|
|Az aktuális érték nem tartalmazhatja a célértéket. |notTartalmaz vagy **nem** tartalmaz |
|Az aktuális érték nem lehet egyenlő a célértékkel. |notEquals vagy **nem** egyenlő |
|Az aktuális érték nem létezhet. |**nem** létezik  |
|Az aktuális érték nem lehet a célértékben. |notIn vagy **nem** |
|Az aktuális érték nem lehet olyan, mint a célérték. |notLike vagy **nem tetszik** |
|Az aktuális érték nem egyezheti meg a kis- és nagybetűk megkülönböztetését a célértékkel. |notMatch vagy **nem** egyezik |
|Az aktuális érték nem egyezheti meg a kis- és nagybetűk megkülönböztetését a célértékkel. |notMatchÉrzéketlenül vagy **nem** egyezésÉrzéketlenül |
|Nincs kapcsolódó erőforrások megfelelnek a hatás részleteit a szabályzat definíciójában. |Az **akkor.details.type** nyelven definiált típusú erőforrás, amely a házirendszabály **ha** részében definiált erőforráshoz kapcsolódik. |

## <a name="compliance-details-for-guest-configuration"></a>A vendégkonfiguráció megfelelőségi adatai

A _vendégkonfiguráció_ kategóriában az _auditIfNotExists_ házirendek esetében több beállítás is lehet a virtuális gépen belül, és meg kell tekintenie a beállítási részleteket. Ha például a jelszóházirendek listáját naplózza, és csak egyikük rendelkezik _Nem megfelelő_állapottal, tudnia kell, hogy mely jelszóházirendek nem felelnek meg a követelményeknek, és miért.

Előfordulhat, hogy nem rendelkezik hozzáféréssel közvetlenül a virtuális gépbe való bejelentkezéshez, de jelentést kell jelentenie arról, hogy miért _nem megfelelő_a virtuális gép.

### <a name="azure-portal"></a>Azure Portal

Kezdje a fenti szakaszban a szabályzatmegfelelőségi részletek megtekintéséhez szükséges lépések végrehajtásával.

A **Megfelelőség részletei** ablaktábla nézetben kattintson az **Utolsó értékelt erőforrás**hivatkozásra.

   ![Az auditIfNotExists definíció részleteinek megtekintése](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

A **Vendég-hozzárendelés** lap megjeleníti az összes rendelkezésre álló megfelelőségi részletet. A nézet minden sora a számítógépen elvégzett kiértékelést jelöli. Az **Ok** oszlopban megjelenik egy kifejezés, amely leírja, hogy miért _nem megfelelő_ a Vendég-hozzárendelés. Ha például jelszóházirendeket naplóz, az **Ok** oszlop szöveget jelenít meg, beleértve az egyes beállítások aktuális értékét is.

![Megfelelőségi részletek megtekintése](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

A megfelelőségi részleteket az Azure PowerShellből is megtekintheti. Először győződjön meg arról, hogy a Vendég konfiguráció modul telepítve van.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

A virtuális gépek összes vendég-hozzárendelésének aktuális állapotát a következő paranccsal tekintheti meg:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Ha csak az _ok_ kifejezés, amely leírja, hogy miért a virtuális gép _nem megfelelő,_ csak a Reason child tulajdonság.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

A vendég-hozzárendelések megfelelőségi előzményeit is kiadhatja a gép hatókörében. A kimenet ebből a parancsból tartalmazza a virtuális gép minden egyes jelentés részleteit.

> [!NOTE]
> A kimenet nagy mennyiségű adatot adhat vissza. Javasoljuk, hogy a kimenetet egy változóban tárolja.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

A nézet egyszerűsítéséhez használja a **ShowChanged paramétert.** A parancs kimenete csak azon jelentéseket tartalmazza, amelyek a megfelelőségi állapot megváltozását követték.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="change-history-preview"></a><a name="change-history"/>Módosítási előzmények (előzetes verzió)

Az új **nyilvános előzetes verzió**részeként a módosítási előzmények utolsó 14 napja elérhető minden olyan Azure-erőforráshoz, amely támogatja a teljes [módú törlést](../../../azure-resource-manager/templates/complete-mode-deletion.md). A módosítási előzmények részletesen ismertetik, hogy mikor észleltek módosítást, és _vizuális diff-et_ az egyes változásokhoz. A változásészlelés az Erőforrás-kezelő tulajdonságainak hozzáadásakor, eltávolításakor vagy módosításakor aktiválódik.

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg, és válassza ki a **Szabályzat** elemet.

1. Az **Áttekintés** vagy a **Megfelelőség** lapon válasszon ki egy szabályzatot bármely **megfelelőségi állapotban.**

1. A **Házirend-megfelelőség** lap **Erőforrás-megfelelőség** lapján válasszon ki egy erőforrást.

1. Válassza a **Módosítási előzmények (előnézet)** lapot az **Erőforrás-megfelelőség** lapon. Megjelenik az észlelt módosítások listája, ha vannak ilyenek.

   ![Az Azure Policy Change History lap az Erőforrás-megfelelőség lapon](../media/determine-non-compliance/change-history-tab.png)

1. Válasszon egyet az észlelt módosítások közül. Az erőforrás _vizuális diff-je_ a **Módosítási előzmények** lapon jelenik meg.

   ![Az Azure Policy Change History Vizuális diff a Változáselőzmények lapon](../media/determine-non-compliance/change-history-visual-diff.png)

Az erőforrás változásainak azonosításában a _vizuális diff_ aides. Előfordulhat, hogy az észlelt módosítások nem kapcsolódnak az erőforrás aktuális megfelelőségi állapotához.

A változási előzmények adatait az [Azure Resource Graph](../../resource-graph/overview.md)szolgáltatja. Ha ezt az információt az Azure Portalon kívül szeretné lekérdezni, [olvassa el az Erőforrás-módosítások beszereznie című témakört.](../../resource-graph/how-to/get-resource-changes.md)

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat az [Azure Policy-mintákban.](../samples/index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](programmatically-create.md)
- További információ a [megfelelőségi adatok beszedéséről.](get-compliance-data.md)
- További információ a [nem megfelelő erőforrások kiújulásáról.](remediate-resources.md)
- Tekintse át, hogy mi a felügyeleti csoport az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal.](../../management-groups/overview.md)
