Könnyen [automatikus méretezése](../articles/monitoring-and-diagnostics/insights-autoscale-best-practices.md) a [virtuális gépeken (VM)](../articles/virtual-machines/windows/overview.md) használatakor [a virtual machine scale sets](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) és a [az Azure automatikus skálázási funkcióval A figyelő](../articles/monitoring-and-diagnostics/monitoring-overview-autoscale.md). A virtuális gépek kell lennie egy méretezési csoport automatikus skálázását tagjai. Ez a cikk nyújt információt, amely lehetővé teszi, hogy jobban megértheti a virtuális gépek vertikális és horizontális skálázást végezhet módszerekkel automatikus és manuális méretezése.

## <a name="horizontal-or-vertical-scaling"></a>Vízszintes vagy függőleges skálázás

Az Azure monitor automatikus méretezési funkciója csak úgy méretezi vízszintesen, amely ("ki") növekedése vagy csökkenése ("") a virtuális gépek számát. Horizontális skálázásra rugalmas felhőalapú helyzetben, mert Ön futtatásához potenciálisan több ezer virtuális gép terhelés kezeléséhez. Vízszintes méretezése automatikus vagy manuális módosításával, a kapacitás (vagy a példányok száma) a méretezési történik. 

Vertikális skálázás azonos számú virtuális gépek megtartja, de lehetővé teszi a virtuális gépek ("be") több vagy kevesebb, ("üzemen kívül") hatékony. Az attribútumok, például a memória, a CPU-sebesség és a lemezterület mérik. Vertikális skálázás szolgáltatás nagyobb hardverekhez, ami gyors találatok száma a felső korlát és régiónként rendelkezésre állásának függ. Vertikális skálázás is általában egy virtuális gép leállítása és újraindítása van szükség. Új méret beállításával a konfigurációban a méretezési csoportban lévő virtuális gépek függőleges méretezése.

A runbookok használatával [Azure Automation](../articles/automation/automation-intro.md), egyszerűen [egy méretezési csoportban lévő virtuális gépek méretezése](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md) felfelé vagy lefelé.

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

A méretezési csoportok megkönnyítik az üzembe helyezés és kezelés az azonos virtuális gépek csoportként. Hozhat létre Linux vagy Windows-méretezési csoportok használata a [az Azure portal](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), [Azure PowerShell-lel](../articles/virtual-machines/windows/tutorial-create-vmss.md), vagy a [Azure CLI-vel](../articles/virtual-machines/linux/tutorial-create-vmss.md). Is hozzon létre és kezelhet, mint az SDK-k a méretezési csoportok [Python](https://azure.microsoft.com/develop/python/) vagy [Node.js](/nodejs/azure), vagy közvetlenül a [REST API-k](/rest/api/compute/virtualmachinescalesets). Virtuális gépek automatikus méretezése metrikák és a szabályok alkalmazása a méretezési csoporthoz történik.

## <a name="configure-autoscale-for-a-scale-set"></a>Méretezési csoportok automatikus skálázás konfigurálása

Automatikus skálázást nyújt a megfelelő számú virtuális gépek, az alkalmazás terhelés kezelésére. Lehetővé teszi, hogy a terhelés növekedését kezelni és pénzt takaríthat meg, amely ül, tétlen virtuális gépek eltávolítása, virtuális gépek hozzáadásához. Megadhatja a virtuális gépek, szabályai alapján futtathatók minimális és maximális száma. Egy minimális teszi meg arról, hogy az alkalmazás mindig fut még nincs terhelés alatt. A teljes lehetséges óránkénti költség-értékkel korlátozza.

Engedélyezheti az automatikus skálázás segítségével a méretezési létrehozásakor [Azure PowerShell-lel](../articles/monitoring-and-diagnostics/insights-powershell-samples.md#create-and-manage-autoscale-settings) vagy [Azure CLI-vel](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings). Is engedélyezheti azt követően a méretezési csoportot hozott létre. Hozzon létre egy méretezési csoportot, a bővítmény telepítése és konfigurálása az automatikus méretezés használatával egy [Azure Resource Manager-sablon](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md). Az Azure Portalon az Azure Monitor automatikus méretezés engedélyezése, vagy a méretezési csoport beállításai az automatikus skálázás engedélyezése.

![Automatikus skálázás engedélyezése](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Mérőszámok

Az Azure monitor automatikus méretezési funkciója lehetővé teszi futó virtuális gépek számának méretezését, illetve lefelé alapján [metrikák](../articles/monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Alapértelmezés szerint a virtuális gépei lemez, hálózati és CPU-használat alapszintű állomásszintű metrikákat. A diagnosztikai bővítmény használata a diagnosztikai adatok gyűjtését konfigurálásakor a további vendég operációs rendszer teljesítményszámlálók lemez, a CPU és memória elérhetővé válnak.

![Metrikai feltétel](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Ha az alkalmazás kell skálázni alapján mérőszámok, amelyek nem érhetők el a gazdagépen keresztül, akkor rendelkeznie kell a méretezési csoportban lévő virtuális gépek a [Linux diagnosztikai bővítmény](../articles/virtual-machines/linux/diagnostic-extension.md) vagy [Windows a diagnosztikai bővítmény](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)telepítve. Ha egy méretezési csoportot az Azure portal használatával hoz létre, szüksége Azure PowerShell vagy az Azure CLI-vel is használható a bővítmény telepítéséhez szükséges diagnosztikai konfiguráció.
 
### <a name="rules"></a>Szabályok

[Szabályok](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) metrika kombinálva a végrehajtandó műveletet. Ha a szabály feltételei teljesülnek, egy vagy több automatikus skálázási műveletek aktiválódnak. Előfordulhat például, hogy meghatározott szabály, amely növeli a virtuális gépek száma 1, ha az átlagos CPU-használat túllépik a 85 %-os.

![Automatikus skálázási műveletek](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Értesítések

Is [eseményindítók beállítása](../articles/monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md) , hogy meghatározott webes URL-címek nevezzük, vagy e-maileket is küldi el a rendszer az Ön által létrehozott automatikus méretezési szabályokat. Webhookok lehetővé teszik az Azure-riasztási értesítések átirányítása utófeldolgozási vagy egyéni értesítések más rendszerekre.

## <a name="manually-scale-vms-in-a-scale-set"></a>Manuális méretezése méretezési csoportban lévő virtuális gépek

### <a name="horizontal"></a>Vízszintes

Adja hozzá, vagy távolítsa el a virtuális gépeket a méretezési csoport kapacitásának módosításával. Az Azure Portalon, csökkentse vagy növelje a virtuális gépek száma (így jelölve: **példányszám**) a méretezési csoportban a késleltetett a felülbírálás feltétel sáv bal vagy jobb oldali méretezés képernyőjén.

Azure PowerShell-lel, le szeretné kérni a méretezési készlet objektum használatával [a Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss). Majd állítsa be a **sku.capacity** a virtuális gépek számát, amelyeket szeretne és frissítési tulajdonságot a méretezési csoport a [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss). Azure CLI-vel, hogy az a kapacitás módosításához a **– új kapacitás** paramétere a [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) parancsot.

### <a name="vertical"></a>Függőleges

Manuálisan módosíthatja a méretét képernyőn a méretezési csoport esetében az Azure Portalon a virtuális gépek méretét. Azure PowerShell használata a Get-AzureRmVmss, a referencia-lemezkép termékváltozat tulajdonság beállításával, és ezután használatával [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) és [Update-AzureRmVmssInstance](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

## <a name="next-steps"></a>További lépések

- További információ a méretezési csoportok [kialakítási szempontok a méretezési csoportok](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).

