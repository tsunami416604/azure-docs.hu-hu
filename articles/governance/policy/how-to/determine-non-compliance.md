---
title: A nemmegfelelőség okainak meghatározása
description: Amikor az erőforrás nem megfelelő, nincsenek számos oka lehet. Ismerje meg, mi okozta a meg nem felelés megtanulhatja, hogyan.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 6e3e01ca9bd459aa6c6aca8dfaacb98b1267fada
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979351"
---
# <a name="determine-causes-of-non-compliance"></a>A nemmegfelelőség okainak meghatározása

Ha egy Azure-erőforrás nem kompatibilis a szabály a házirend, hasznos lehet tudni, mely az erőforrás nem felelnek meg a szabály részét. Emellett hasznos megérteni, milyen módosítása módosítani egy korábban megfelelő erőforrással, hogy nem megfelelő. Ezen adatok két módja van:

> [!div class="checklist"]
> - [Megfelelőségi részletei](#compliance-details)
> - [Változások nyomon követése (előzetes verzió)](#change-history-preview)

## <a name="compliance-details"></a>Megfelelőségi részletek

Amikor az erőforrás nem megfelelő, az adott erőforráshoz a megfelelőségi információk érhetők el a **szabályzatoknak való megfelelés** lapot. A megfelelőségi részletek panelen a következő információkat tartalmazza:

- Erőforrás részleteit, például a név, típus, hely és erőforrás-azonosító
- Megfelelőségi állapotok és az utolsó értékelés a jelenlegi szabályzat-hozzárendeléshez tartozó időbélyeg
- Listáját _okokból_ az erőforrás meg nem felelés esetén

> [!IMPORTANT]
> A megfelelőségi adatai számára, egy _nem megfelelő_ erőforrás látható tulajdonságok aktuális értékét az erőforráson, a felhasználónak rendelkeznie kell **olvasási** művelet a **típus** , az erőforrás. Például ha a _nem megfelelő_ erőforrás **Microsoft.Compute/virtualMachines** akkor a felhasználónak rendelkeznie kell a **Microsoft.Compute/virtualMachines/read** a művelet. Ha a felhasználó nem rendelkezik a szükséges művelet, hozzáférési hiba jelenik meg.

A megfelelőségi részletek megjelenítéséhez kövesse az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg és válassza ki a **Szabályzat** elemet.

1. Az a **áttekintése** vagy **megfelelőségi** lapra, jelölje be a házirend egy **megfelelőségi állapot** , amely _nem megfelelő_.

1. Alatt a **erőforrás megfelelőségi** lapján a **szabályzatoknak való megfelelés** lapon kattintson a jobb gombbal, vagy kattintson a három pontra az erőforrások egy **megfelelőségi állapot** , amely  _Nem megfelelő_. Válassza ki **megfelelőségi részletek megtekintéséhez**.

   ![Nézet megfelelőségi részletek lehetőség](../media/determine-non-compliance/view-compliance-details.png)

1. A **megfelelőségi részletek** ablaktáblán jelennek meg a jelenlegi szabályzat-hozzárendelés a legújabb kiértékelés az erőforrás adatait. Ebben a példában a mező **Microsoft.Sql/servers/version** bizonyul _12.0-s_ során a várt szabályzatdefiníció _14.0_. Ha több okból az erőforrás nem megfelelő, mindegyik megjelenik ezen a panelen.

   ![Megfelelőségi részletek ablaktábla és a meg nem felelés oka](../media/determine-non-compliance/compliance-details-pane.png)

   Az egy **auditIfNotExists** vagy **deployIfNotExists** szabályzatdefiníciót, szerepel a **details.type** tulajdonság és a választható tulajdonságok. Egy listát lásd: [auditIfNotExists tulajdonságok](../concepts/effects.md#auditifnotexists-properties) és [deployIfNotExists tulajdonságok](../concepts/effects.md#deployifnotexists-properties). **Legutóbbi értékelés erőforrás** a kapcsolódó erőforrás a **részletek** a definíció szakasza.

   Példa részleges **deployIfNotExists** definíciója:

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

   ![Megfelelőségi részletek ablaktábla - * ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Az adatok védelme, amikor egy tulajdonság értéke egy _titkos_ a jelenlegi érték csillagokat.

Ezeket a részleteket ismertetik, hogy miért erőforrás jelenleg nem megfelelő, de ne jelenjen meg, amikor az erőforrás, amely miatt nem nem megfelelővé válik a módosítást hajtottak végre. Ez az információ, lásd: [módosítási előzmények (előzetes verzió)](#change-history-preview) alatt.

### <a name="compliance-reasons"></a>Megfelelőségi okokból

A következő mátrix térképek minden lehetséges _OK_ a a felelős [feltétel](../concepts/definition-structure.md#conditions) szabályzatdefinícióban:

|Reason | Feltétel |
|-|-|
|Az aktuális értéknek tartalmaznia kell a célértéket kulcsként. |containsKey vagy **nem** notContainsKey |
|Az aktuális értéknek tartalmaznia kell a célértéket. |tartalmaz vagy **nem** notContains |
|Az aktuális értéknek egyenlőnek kell lennie a célértékkel. |egyenlő vagy **nem** notEquals |
|Az aktuális értéknek léteznie kell. |Létezik |
|Az aktuális értéknek a célértéken belülinek kell lennie. |a vagy **nem** notIn |
|Az aktuális értéknek a célértékhez hasonlónak kell lennie. |például vagy **nem** notLike |
|Az aktuális értéknek a ki- és nagybetűk közötti különbség figyelembevételével meg kell felelnie a célértéknek. |megfelelő vagy **nem** notMatch |
|Az aktuális értéknek a kis- és nagybetűk közötti különbség figyelmen kívül hagyásával meg kell felelnie a célértéknek. |matchInsensitively vagy **nem** notMatchInsensitively |
|Az aktuális értéknek nem tartalmazhatja a célértéket kulcsként. |notContainsKey vagy **nem** containsKey|
|Az aktuális értéknek nem tartalmazhatja a célértéket. |notContains vagy **nem** tartalmaz |
|Az aktuális érték nem lehet egyenlő a célértékkel. |notEquals vagy **nem** egyenlő |
|Az aktuális érték nem létezhet. |**nem** létezik  |
|Az aktuális érték nem eshet a célértékbe. |notIn vagy **nem** a |
|Az aktuális érték nem lehet a célértékhez hasonló. |notLike vagy **nem** például |
|Az aktuális érték a kis- és nagybetűk közötti különbség figyelembevételével nem felelhet meg a célértéknek. |notMatch vagy **nem** felel meg |
|Az aktuális érték a kis- és nagybetűk közötti különbség figyelmen kívül hagyásával nem felelhet meg a célértéknek. |notMatchInsensitively vagy **nem** matchInsensitively |
|A szabályzat definíciójában nem felel meg kapcsolódó erőforrás a hatás részleteinek. |A megadott típusú erőforrás **then.details.type** és a meghatározott erőforráshoz kapcsolódó a **Ha** része a szabály nem létezik. |

## <a name="compliance-details-for-guest-configuration"></a>Megfelelőségi adatai számára a Vendég-konfiguráció

A _naplózási_ szabályzatokat a _Vendég konfigurációs_ kategória, lehetséges, hogy értékeli ki a virtuális gép több beállítást, és kell beállításonkénti részletek megtekintéséhez. Például ha, már telepített alkalmazásokat és a hozzárendelés állapotának listáját naplózás, _nem megfelelő_, meg kell tudni, hogy mely adott alkalmazásokat hiányoznak.

Akkor is előfordulhat, hogy nincs hozzáférése a közvetlenül bejelentkezni a virtuális gép, de miért nem a virtuális gép jelentést kell _nem megfelelő_. Például előfordulhat, hogy naplózza, hogy a virtuális gépek a megfelelő tartományhoz csatlakozó és az aktuális tartományi tagság tartalmazzák a jelentéskészítési részletei között.

### <a name="azure-portal"></a>Azure Portal

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg és válassza ki a **Szabályzat** elemet.

1. Az a **áttekintése** vagy **megfelelőségi** lapon, válassza ki a szabályzat-hozzárendelés minden kezdeményezés, amely tartalmazza a Vendég-konfigurációs szabályzat-definíció hogy _nem megfelelő_.

1. Válassza ki egy _naplózási_ kezdeményezésére szabályzat hogy _nem megfelelő_.

   ![Naplózási definíció részleteinek megtekintése](../media/determine-non-compliance/guestconfig-audit-compliance.png)

1. Az a **erőforrás megfelelőségi** lapon a következő információkat biztosítja:

   - **Név** – a Vendég konfigurációs hozzárendelések nevét.
   - **Szülőerőforrás** – a virtuális gép egy _nem megfelelő_ állapot, a kiválasztott Vendég konfigurációs hozzárendelés.
   - **Erőforrástípus** – a _guestConfigurationAssignments_ teljes neve.
   - **Legutóbbi értékelés** – az utolsó időpont, a Vendég konfigurációs szolgáltatás értesítést kap az Azure Policy a cél virtuális gép állapotát.

   ![Tekintse át a megfelelőségi részleteket.](../media/determine-non-compliance/guestconfig-assignment-view.png)

1. Válassza ki a Vendég konfigurációs hozzárendelés neve a **neve** nyissa meg az oszlop a **erőforrás megfelelőségi** lapot.

1. Válassza ki a **erőforrás megtekintése** gomb nyissa meg a lap tetején a **Vendég hozzárendelés** lapot.

A **Vendég hozzárendelés** az összes elérhető megfelelőségi adatait jeleníti meg. Minden egyes sorára a nézet a virtuális gépen végrehajtott értékelést jelöli. Az a **OK** oszlop, miért a Vendég-hozzárendelés nem leíró kifejezés _nem megfelelő_ jelenik meg. Ha például Ön naplózás, hogy a virtuális gépek egy tartományhoz kell csatlakoztatni a **OK** oszlop jeleníti meg, beleértve az aktuális tartományi tagság szöveg.

![Tekintse át a megfelelőségi részleteket.](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Megfelelőségi adatai az Azure PowerShell-lel is megtekintheti. Győződjön meg róla, hogy telepítette a Vendég konfigurációs modul.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Az összes Vendég-hozzárendelés az aktuális állapotát egy virtuális gép a következő paranccsal tekintheti meg:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Csak megtekintéséhez a _OK_ kifejezés, amely azt ismerteti, miért nem a virtuális gép _nem megfelelő_, csak a OK gyermek tulajdonságát.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Megfelelőségi előzményei terjed ki a virtuális gép vendég hozzárendelések is készíthető. Ez a parancs kimenete az egyes jelentések részletezi a virtuális gép számára.

> [!NOTE]
> A kimeneti adatok nagy mennyiségű adhatnak vissza. Azt javasoljuk, hogy a kimenet tárolható egy változóban.

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

Ez a nézet leegyszerűsítése használja a **ShowChanged** paraméter. Ez a parancs kimenete csak a jelentéseket tartalmazza, majd a megfelelőségi állapot módosítása.

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

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>Változások nyomon követése (előzetes verzió)

Egy új részeként **nyilvános előzetes verzióban**, a változások nyomon követése az elmúlt 14 napban érhetők el az összes Azure-erőforrások, amelyek támogatják a [mód törlésének befejezéséhez](../../../azure-resource-manager/complete-mode-deletion.md). Változások nyomon követése kapcsolatos tudnivalókat tartalmaz, amikor a rendszer észlelte a változást, és a egy _diff vizuális_ minden módosításakor. Egy címváltozásának felderítését akkor aktiválódik, ha a Resource Manager-tulajdonságok hozzáadott, eltávolított vagy módosítani.

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg és válassza ki a **Szabályzat** elemet.

1. Az a **áttekintése** vagy **megfelelőségi** lapon, válassza ki a szabályzat minden **megfelelőségi állapot**.

1. Alatt a **erőforrás megfelelőségi** lapján a **szabályzatoknak való megfelelés** lap, válasszon ki egy erőforrást.

1. Válassza ki a **változások nyomon követése (előzetes verzió)** lapján a **erőforrás megfelelőségi** lapot. Listáját módosítások észlelése, ha a bármely exist jelennek meg.

   ![Erőforrás megfelelősége oldalon Azure házirend változások nyomon követése lap](../media/determine-non-compliance/change-history-tab.png)

1. Válassza ki az egyik észlelt változás. A _diff vizuális_ esetében az erőforrás egyike jelenik meg a **módosítási előzmények** lapot.

   ![Az Azure szabályzat módosítási előzmények Visual Diff módosítási előzmények lapon](../media/determine-non-compliance/change-history-visual-diff.png)

A _diff vizuális_ segédeszközökkel erőforrásai azonosításában. A módosítások észlelhetők nem kapcsolódik az erőforrás aktuális megfelelőségi állapotát.

Módosítási előzmények adatok által biztosított [Azure Erőforrás-grafikon](../../resource-graph/overview.md). Ezt az információt az Azure Portalon kívül lekérdezéséhez, lásd: [beolvasása az erőforrás-módosítások](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>További lépések

- Tekintse át a következő példák [Azure Policy minták](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Megismerheti, hogyan [szabályzatok létrehozása programozott módon](programmatically-create.md).
- Ismerje meg, hogyan [megfelelőségi adatok](getting-compliance-data.md).
- Ismerje meg, hogyan [javítani a nem megfelelő erőforrások](remediate-resources.md).
- Tekintse át a felügyeleti csoport van [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](../../management-groups/overview.md).