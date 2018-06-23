Egyszerűen [automatikus méretezése](../articles/monitoring-and-diagnostics/insights-autoscale-best-practices.md) a [virtuális gépek (VM)](../articles/virtual-machines/windows/overview.md) használatakor [virtuálisgép-méretezési csoportok](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) és a [Azure automatikus skálázás szolgáltatása A figyelő](../articles/monitoring-and-diagnostics/monitoring-overview-autoscale.md). A virtuális gépek kell beállítása automatikusan átméretezi a skála tagjai lesznek. Ez a cikk nyújt információt, amely lehetővé teszi a virtuális gépek függőleges és vízszintes módszerekkel automatikus és manuális méretezése jobb megértése.

## <a name="horizontal-or-vertical-scaling"></a>Vízszintes vagy függőleges skálázás

Az automatikus skálázási funkciót Azure figyelő csak arányosan vízszintesen, ez ("out") növelését vagy csökkenését ("") a virtuális gépek számát. Horizontális skálázás rugalmasabb felhő helyzetben, lehetővé teszi több ezer virtuális gépek terhelés kezelése érdekében potenciálisan futtatásához. Vízszintes méretezni automatikusan vagy manuálisan módosítja a méretezési kapacitás (vagy a példányok száma). 

Függőleges skálázás tartja a virtuális gépek azonos számú, de lehetővé teszi a virtuális gépek ("Mentés") több vagy kevesebb ("le") hatékony. Az attribútumok például memória, Processzor sebessége vagy lemezterület mérik. Függőleges méretezés akkor elérhetőségétől függ nagyobb hardver, amely gyorsan találatok száma a felső korlátja, és régiónként eltérőek lehetnek. Általában is függőleges skálázáshoz kell a virtuális gépek leállítása és újraindítása. A konfigurációban a méretezési csoportban lévő virtuális gépek új beállításával függőleges méretezni.

A runbookok használata [Azure Automation](../articles/automation/automation-intro.md), egyszerűen [méretezési csoportban lévő virtuális gépek méretezési](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md) felfelé vagy lefelé.

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Méretezési csoportok megkönnyítik telepítéséhez és kezeléséhez az azonos virtuális gépek készletként. Létrehozhat Linux vagy a Windows-skálázási készletekben használatával a [Azure-portálon](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), [Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md), vagy a [Azure CLI](../articles/virtual-machines/linux/tutorial-create-vmss.md). Is hozhat létre, és kezelhet, mint az SDK-k méretezési csoportok [Python](/develop/python) vagy [Node.js](/nodejs/azure), vagy közvetlenül a [REST API-k](/rest/api/compute/virtualmachinescalesets). Virtuális gépek automatikus skálázás érhető el, a méretezési metrikák és a szabályok alkalmazásával.

## <a name="configure-autoscale-for-a-scale-set"></a>A skála beállított automatikus skálázás konfigurálása

Automatikus skálázás számos a megfelelő virtuális gépek a az alkalmazás terhelés kezelésére. Lehetővé teszi a terhelés növekszik, és pénzt takaríthat meg üresjárati ül, virtuális gépek eltávolítása virtuális gépeket. Megadja a virtuális gépek meghatározott szabályok alapján futtathatnak minimális és maximális számát. A minimális elérhetővé válnak meg arról, hogy az alkalmazás mindig fut még nincs terhelés alatt. A teljes lehetséges óránkénti költség-értékkel korlátozza.

Automatikus skálázás engedélyezheti a méretezési készletben használatával létrehozásakor [Azure PowerShell](../articles/monitoring-and-diagnostics/insights-powershell-samples.md#create-and-manage-autoscale-settings) vagy [Azure CLI](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings). Engedélyezheti azt a méretezési készlet létrehozása után. Hozzon létre egy méretezési készletet, a bővítmény telepítéséhez és beállításához az automatikus skálázás használatával egy [Azure Resource Manager sablon](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md). Az Azure-portálon Azure figyelő automatikus skálázás engedélyezze, vagy a skálázási beállításai az automatikus skálázás engedélyezéséhez.

![Automatikus skálázás engedélyezése](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Mérőszámok

Az automatikus skálázási funkciót Azure figyelő lefelé alapján, vagy lehetővé teszi futó virtuális gépek számának [metrikák](../articles/monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Alapértelmezés szerint virtuális gépek gazdaszintű alapvető metrikák lemez, hálózati és CPU-használat adja meg. A diagnosztikai kiterjesztéssel diagnosztikai adatok gyűjtésére konfigurálásakor további vendég operációs rendszer teljesítményszámlálók rendelkezésre állásúvá válik a lemezen, a Processzor és memória.

![Metrika feltételek](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Ha az alkalmazást kell méretezni alapján metrikákat, amelyek nem érhetők el a gazdagépen keresztül, akkor rendelkeznie kell a virtuális gépeket, a méretezési csoportban lévő a [Linux diagnosztikai bővítmény](../articles/virtual-machines/linux/diagnostic-extension.md) vagy [Windows diagnosztika bővítmény](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)telepítve. A méretezési készletben, az Azure portál használatával hoz létre, ha szüksége telepítéséhez is használható Azure PowerShell vagy az Azure parancssori felület a bővítmény diagnosztikai konfiguráció szerint van szüksége.
 
### <a name="rules"></a>Szabályok

[Szabályok](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) metrika egyesíthető egy művelet elvégzését. Ha a szabály feltételek teljesülnek, egy vagy több automatikus skálázási művelet aktiválódnak. Például lehetséges, hogy meghatározott szabály, amely növeli a virtuális gépek száma 1, ha az átlagos processzorhasználat hibernálva 85 % felett.

![Automatikus skálázási műveletek](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Értesítések

Is [eseményindítók beállítása](../articles/monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md) , hogy az URL-címek meghatározott webes nevezik, és e-maileket a létrehozott automatikus skálázási szabályok alapján küldi. Webhook lehetővé teszik a más rendszerekkel utófeldolgozási vagy egyéni értesítések az Azure riasztási értesítések továbbításához.

## <a name="manually-scale-vms-in-a-scale-set"></a>Manuálisan méretezhető méretezési csoportban lévő virtuális gépek

### <a name="horizontal"></a>Vízszintes

Adja hozzá, vagy távolítsa el a virtuális gépek a kapacitás, a méretezési módosításával. Az Azure portálon, csökkentse vagy növelje a virtuális gépek számát (megjelennek az helyeként **példány száma**) a rendszer állítja be a felülbírálás feltétel sáv késleltetett a bal és jobb oldali méretezés képernyőn.

Az Azure PowerShell használatával le kell töltenie a méretezési készlet objektum használatával [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss). Ezután a **sku.capacity** tulajdonság a használni kívánt virtuális gépek és a frissítés számára a méretezési rendelkező [frissítés-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss). Azure parancssori felület használatával, a kapacitás módosítja a **– új kapacitás** paramétere a [az vmss méretezési](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) parancsot.

### <a name="vertical"></a>Függőleges

A virtuális gépeket az Azure portálon, a méretezési készlet mérete képernyőn mérete manuálisan módosíthatja. Get-AzureRmVmss, a lemezkép referencia sku tulajdonságban beállítás, és használata az Azure PowerShell használatával [frissítés-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) és [frissítés-AzureRmVmssInstance](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

## <a name="next-steps"></a>További lépések

- További információ a méretezési készlet [skálázási készletekben kialakítási szempontjai](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).

