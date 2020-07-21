---
title: Azure Monitor üzembe helyezése
description: A Azure Policy használatával méretezhető Azure Monitor szolgáltatások.
ms.subservice: ''
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: fbfc0cafe83f53bd7cab2b93899e9c2cb02d52e3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505210"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>Azure Monitor üzembe helyezése méretezéssel Azure Policy használatával
Néhány Azure Monitor funkció egyszer vagy korlátozott számú alkalommal van konfigurálva, másokat meg kell ismételni minden figyelni kívánt erőforrásnál. Ez a cikk azokat a módszereket ismerteti, amelyekkel a Azure Policy használatával méretezhetők a Azure Monitorek, így biztosítható, hogy az összes Azure-erőforrás monitorozása következetes legyen és pontosan legyen konfigurálva.

Például létre kell hoznia egy diagnosztikai beállítást az összes meglévő Azure-erőforráshoz és minden létrehozott új erőforráshoz. A virtuális gépek létrehozásakor is telepítenie és konfigurálnia kell egy ügynököt. A műveletek végrehajtásához használhatja a PowerShell vagy a CLI metódust is, mivel ezek a módszerek a Azure Monitor összes szolgáltatásához elérhetők. A Azure Policy használatával olyan logikával rendelkezhet, amely automatikusan végrehajtja a megfelelő konfigurációt minden alkalommal, amikor létrehoz vagy módosít egy erőforrást.


## <a name="azure-policy"></a>Azure Policy
Ez a szakasz rövid bevezetést nyújt a [Azure Policy](../../governance/policy/overview.md) , amely lehetővé teszi a teljes Azure-előfizetés vagy-felügyeleti csoport szervezeti szabványainak értékelését és betartatását minimális erőfeszítéssel. A részletekért tekintse meg a [Azure Policy dokumentációját](../../governance/policy/overview.md) .

A Azure Policy segítségével megadhatja a létrehozott erőforrások konfigurációs követelményeit, és vagy azonosíthatja a nem megfelelő erőforrásokat, megakadályozhatja az erőforrások létrehozását, vagy hozzáadhatja a szükséges konfigurációt. Egy új erőforrás létrehozására vagy egy meglévő erőforrás módosítására irányuló hívások elfogásával működik. Az ilyen hatással lehet a kérés elutasítása, ha nem egyezik meg a házirend-definícióban várt tulajdonságokkal, a nem megfelelőség megjelölésével vagy egy kapcsolódó erőforrás üzembe helyezésével. A meglévő erőforrásokat a **deployIfNotExists** vagy a házirend-definíció **módosításával** javíthatja.

A Azure Policy a következő táblázatban szereplő objektumokból áll. Az egyes elemek részletes ismertetését lásd: [Azure Policy objektumok](../../governance/policy/overview.md#azure-policy-objects) .

| Elem | Leírás |
|:---|:---|
| Szabályzatdefiníció | Ismerteti az erőforrás-megfelelőségi feltételeket, valamint azt, hogy egy adott feltétel teljesül-e. Ez lehet egy adott típus összes erőforrása, vagy csak bizonyos tulajdonságokkal egyező erőforrások. Ennek hatására egyszerűen megjelölheti az erőforrást a megfelelőség szempontjából, vagy egy kapcsolódó erőforrást is üzembe helyezhet. A szabályzat-definíciók a JSON használatával íródnak, [Azure Policy definíciós struktúrában](../../governance/policy/concepts/definition-structure.md)leírtak szerint. A hatások ismertetése a [Azure Policy effektusok ismertetése](../../governance/policy/concepts/effects.md)című témakörben található.
| Házirend kezdeményezése | Csoportházirend-definíciók egy csoportja, amelyeket együtt kell alkalmazni. Előfordulhat például, hogy egy házirend-definícióban erőforrás-naplókat küld egy Log Analytics munkaterületre, és egy másikat, hogy erőforrás-naplókat küldjön az Event huboknak. Hozzon létre egy olyan kezdeményezést, amely mindkét házirend-definíciót tartalmazza, és alkalmazza a kezdeményezést az erőforrásokra az egyes házirend-definíciók helyett. A kezdeményezések a JSON használatával íródnak [Azure Policy kezdeményezési struktúrában](../../governance/policy/concepts/initiative-definition-structure.md)leírt módon. |
| Hozzárendelés | Egy házirend-definíció vagy kezdeményezés csak akkor lép érvénybe, ha hozzá van rendelve egy hatókörhöz. Például hozzárendelhet egy szabályzatot egy erőforráscsoporthoz, hogy az az adott erőforrásban létrehozott összes erőforrásra alkalmazza azt, vagy alkalmazza azt egy előfizetésre, hogy alkalmazza az adott előfizetés összes erőforrására.  További részletekért tekintse meg [Azure Policy hozzárendelési struktúrát](../../governance/policy/concepts/assignment-structure.md). |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>A Azure Monitor beépített szabályzat-definíciói
Azure Policy tartalmaz több, Azure Monitorhoz kapcsolódó előre összeépített definíciót is. Ezeket a házirend-definíciókat hozzárendelheti meglévő előfizetéséhez, vagy felhasználhatja azokat a saját egyéni definícióinak létrehozásához. A **figyelés** kategóriájában található beépített szabályzat teljes listájáért lásd: [Azure Policy beépített házirend-definíciók a Azure monitorhoz](../samples/policy-samples.md).

A figyeléshez kapcsolódó beépített szabályzat-definíciók megtekintéséhez hajtsa végre a következőket:

1. Lépjen **Azure Policy** a Azure Portal.
2. Válassza a **definíciók**lehetőséget.
3. A **Típus mezőben**válassza a *beépített* lehetőséget, és a **Kategória**beállításnál válassza a *figyelés*lehetőséget.

  ![Beépített szabályzat-definíciók](media/deploy-scale/builtin-policies.png)


## <a name="diagnostic-settings"></a>Diagnosztikai beállítások
A [diagnosztikai beállítások](../platform/diagnostic-settings.md) erőforrás-naplókat és mérőszámokat gyűjtenek az Azure-erőforrásokból több helyre, jellemzően egy log Analytics munkaterületre, amely lehetővé teszi az adatok elemzését a [naplók lekérdezésével](../log-query/log-query-overview.md) és a [naplók riasztásával](alerts-log.md). A házirend használatával automatikusan létrehozhat egy diagnosztikai beállítást, valahányszor létrehoz egy erőforrást.

Minden egyes Azure-erőforrástípus olyan egyedi kategóriákat tartalmaz, amelyeknek szerepelniük kell a diagnosztikai beállításban. Emiatt minden erőforrástípus külön házirend-definíciót igényel. Egyes erőforrástípusok beépített szabályzat-definíciókkal rendelkeznek, amelyeket módosítás nélkül rendelhet hozzá. Más erőforrástípusok esetén létre kell hoznia egy egyéni definíciót.

### <a name="built-in-policy-definitions-for-azure-monitor"></a>A Azure Monitor beépített szabályzat-definíciói
Az egyes erőforrástípusok két beépített házirend-definícióval rendelkeznek, amelyek közül az egyik a Log Analytics munkaterületre, egy másik pedig az Event hub-ba küldhető. Ha csak egy helyet kell megadnia, akkor ezt a házirendet az erőforrástípus számára kell kiosztania. Ha mindkettőre van szüksége, rendeljen az erőforráshoz mindkét házirend-definíciót.

Az alábbi képen például az Data Lake Analytics beépített diagnosztikai beállítási szabályzatának definíciói láthatók.

  ![Beépített szabályzat-definíciók](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>Egyéni szabályzat-definíciók
A beépített házirenddel nem rendelkező erőforrástípusok esetén létre kell hoznia egy egyéni szabályzat-definíciót. Ezt manuálisan is megteheti a Azure Portal egy meglévő beépített szabályzat másolásával, majd az erőforrástípus módosításával. Ez hatékonyabb, ha a szabályzatot a PowerShell-galéria parancsfájl használatával hozza létre a programozott módon.

A [create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) parancsfájl a PowerShell vagy a parancssori felület használatával telepíthető adott erőforrástípus számára hoz létre házirend-fájlokat. A következő eljárással hozhat létre egyéni szabályzat-definíciót a diagnosztikai beállításokhoz.


1. Győződjön meg arról, hogy [Azure PowerShell](/powershell/azure/install-az-ps) telepítve van.
2. Telepítse a parancsfájlt a következő paranccsal:
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. Futtassa a szkriptet a paraméterek használatával a naplók küldési helyének megadásához. A rendszer kérni fogja az előfizetés és az erőforrás típusának megadását. Ha például olyan házirend-definíciót szeretne létrehozni, amely Log Analytics munkaterületre és az Event hub-ra küld, használja a következő parancsot.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. Másik lehetőségként megadhat egy előfizetést és egy erőforrástípust is a parancsban. Ha például olyan házirend-definíciót szeretne létrehozni, amely Log Analytics munkaterületre és az Azure SQL Server-adatbázisokhoz tartozó Event hub-ra küldi, használja a következő parancsot.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. A szkript külön mappákat hoz létre az egyes szabályzat-definíciók számára, amelyek mindegyike három, azurepolicy, JSON, azurepolicy.rules.json azurepolicy.parameters.jsnevű fájlt tartalmaz. Ha a házirendet manuálisan kívánja létrehozni a Azure Portalban, másolhatja és beillesztheti a azurepolicy.jstartalmát, mert az tartalmazza a teljes házirend-definíciót is. A másik két fájl PowerShell vagy CLI használatával hozza létre a házirend-definíciót a parancssorból.

    Az alábbi példák bemutatják, hogyan telepítheti a házirend-definíciót a PowerShell és a parancssori felületről. Minden olyan metaadatot tartalmaz **, amely a** beépített szabályzat-definíciókkal csoportosítja az új házirend-definíciót.

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>Kezdeményezés
Ahelyett, hogy minden egyes házirend-definícióhoz hozzon létre hozzárendelést, egy közös stratégia egy olyan kezdeményezés létrehozása, amely tartalmazza a szabályzat-definíciókat az egyes Azure-szolgáltatásokhoz tartozó diagnosztikai beállítások létrehozásához. Hozzon létre egy hozzárendelést a kezdeményezés és egy felügyeleti csoport, előfizetés vagy erőforráscsoport között attól függően, hogy hogyan kezeli a környezetet. Ez a stratégia a következő előnyöket kínálja:

- Hozzon létre egyetlen hozzárendelést a kezdeményezéshez több hozzárendelés helyett az egyes erőforrástípusok esetében. Ugyanazt a kezdeményezést használja több figyelési csoporthoz, előfizetéshez vagy erőforráscsoporthoz.
- Módosítsa a kezdeményezést, ha új erőforrástípust vagy célhelyet kell hozzáadnia. Előfordulhat például, hogy a kezdeti követelmények csak Log Analytics munkaterületre küldenek adatküldést, de később az Event hub-t szeretné hozzáadni. Új hozzárendelések létrehozása helyett módosítsa a kezdeményezést.

A kezdeményezés létrehozásával kapcsolatos részletekért tekintse meg a [kezdeményezési definíció létrehozása és társítása](../../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition) című témakört. Vegye figyelembe a következő ajánlásokat:

- Állítsa be a **kategóriát** a **figyeléshez** , hogy csoportosítsa a kapcsolódó beépített és egyéni szabályzat-definíciókkal.
- Ahelyett, hogy megadta a Log Analytics munkaterület részleteit és az Event hub-t a kezdeményezésben szereplő házirend-definícióhoz, használjon egy közös kezdeményezési paramétert. Így egyszerűen meghatározhatja az összes házirend-definíció közös értékét, és szükség esetén módosíthatja az adott értéket.

![Kezdeményezési definíció](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>Hozzárendelés 
Rendelje hozzá a kezdeményezést egy Azure felügyeleti csoporthoz, előfizetéshez vagy erőforráscsoporthoz a figyelni kívánt erőforrások körétől függően. A [felügyeleti csoport](../../governance/management-groups/overview.md) különösen akkor hasznos, ha a szervezet több előfizetéssel rendelkezik.

![Kezdeményezési hozzárendelés](media/deploy-scale/initiative-assignment.png)

A kezdeményezési paraméterek használatával megadhatja a munkaterületet vagy bármely más részletet a kezdeményezés összes házirend-definíciójában. 

![Kezdeményezési paraméterek](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>Szervizelés
A kezdeményezés a létrehozott virtuális gépekre lesz érvényes. Egy [szervizelési feladat](../../governance/policy/how-to/remediate-resources.md) központilag telepíti a házirend-definíciókat a meglévő erőforrások számára, így lehetővé teszi a már létrehozott erőforrások diagnosztikai beállításainak létrehozását. Amikor a Azure Portal használatával hozza létre a hozzárendelést, lehetősége van egy szervizelési feladat egyidejű létrehozására. A szervizeléssel kapcsolatos részletekért tekintse meg a [nem megfelelő erőforrások szervizelése Azure Policy](../../governance/policy/how-to/remediate-resources.md) című témakört.

![Kezdeményezés szervizelése](media/deploy-scale/initiative-remediation.png)


## <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez
A [Azure monitor for VMS](../insights/vminsights-overview.md) a virtuális gépek figyelésére szolgáló Azure monitor elsődleges eszköze. A Azure Monitor for VMs engedélyezése a Log Analytics ügynököt és a függőségi ügynököt is telepíti. Ahelyett, hogy ezeket a feladatokat manuálisan hajtja végre, a Azure Policy használatával győződjön meg arról, hogy az egyes virtuális gépeket a létrehozáskor konfigurálta.

A Azure Monitor for VMs két beépített kezdeményezést tartalmaz, amelyek az **Azure monitor for VMS engedélyezése** és **a Azure monitor engedélyezése Virtual Machine Scale sets**. Ezek a kezdeményezések olyan szabályzat-definíciókat tartalmaznak, amelyek szükségesek a Log Analytics-ügynök telepítéséhez, valamint a Azure Monitor for VMs engedélyezéséhez szükséges függőségi ügynökhöz. 

Ahelyett, hogy a Azure Policy felületen hozza létre a kezdeményezésekhez tartozó hozzárendeléseket, Azure Monitor for VMs tartalmaz egy olyan szolgáltatást, amely lehetővé teszi az egyes hatókörökben lévő virtuális gépek számának vizsgálatát annak megállapítására, hogy a kezdeményezés alkalmazása megtörtént-e. Ezután konfigurálhatja a munkaterületet, és létrehozhatja a szükséges hozzárendeléseket a csatoló használatával.

A folyamat részleteiért lásd: [Azure monitor for VMS engedélyezése Azure Policy használatával](../insights/vminsights-enable-at-scale-policy.md).

![Azure Monitor for VMs házirend](../platform/media/deploy-scale/vminsights-policy.png)


## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Policyról](../../governance/policy/overview.md).
- További információ a [diagnosztikai beállításokról](diagnostic-settings.md).
