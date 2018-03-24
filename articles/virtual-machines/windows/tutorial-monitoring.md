---
title: Az Azure figyelés és frissítése és a Windows virtuális gépek |} Microsoft Docs
description: Az oktatóanyag - figyelésére és frissítésére a Windows rendszerű virtuális gép az Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 94151008f0aba6020786e65c60cec66285f310c4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-and-update-a-windows-virtual-machine-with-azure-powershell"></a>Figyelheti és frissítheti a Windows virtuális gépként az Azure PowerShell

Ügynökök Azure figyelés használ a rendszerindító és teljesítményadatokat gyűjteni Azure virtuális gépeken, ezek az adatok tárolása az Azure storage, és lehetővé teszi a portál, az Azure PowerShell modul és az Azure parancssori felület keresztül érhető el. Frissítéskezelés lehetővé teszi a frissítések és javítások kezelheti a Windows Azure virtuális gépeken.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A virtuális gép rendszerindítási diagnosztika engedélyezése
> * Rendszerindítási diagnosztika megtekintése
> * Virtuális gép gazdagép-metrikák megtekintése
> * A diagnosztika-kiterjesztés telepítése
> * Virtuálisgép-metrikák megtekintése
> * Riasztás létrehozása
> * Windows-frissítések kezelése
> * Speciális monitorozás beállítása

Az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre. Amennyiben szükséges, [ezzel a mintaszkripttel](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) létrehozhat egyet. Az oktatóanyag lépéseinek használatakor, cserélje ki az erőforráscsoportot, a virtuális gép nevét és a helyet, ha szükséges.

## <a name="view-boot-diagnostics"></a>Rendszerindítási diagnosztika megtekintése

Indítsa el a Windows virtuális gépek, a rendszerindítási diagnosztikai ügynök rögzíti a képernyő kimeneti hibaelhárítási céllal használható. Ez a funkció alapértelmezés szerint engedélyezve van. A rögzített képernyőképek tárolódnak az Azure storage-fiók, amely alapértelmezés szerint is létrejön. 

A rendszerindítási diagnosztikai adatok kaphat a [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata) parancsot. A következő példában a rendszerindítási diagnosztika gyökerébe letöltődnek a * c:\* meghajtó. 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Gazdagép metrikáinak megtekintése

Egy Windows virtuális gép a gazdagép dedikált virtuális gépek rendelkezik, amely hatással van az Azure-ban. Metrikák automatikusan összegyűjtött ahhoz, hogy a gazdagép és az Azure portálon is megtekinthetők.

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroup**, majd a **myVM** elemet az erőforrások listájából.
2. Kattintson a **metrikák** a virtuális gép panelen, majd válassza ki a gazdagép-metrikák bármelyikét **elérhető** tekintheti meg, hogyan működik-e a gazdagép virtuális.

    ![Gazdagép metrikáinak megtekintése](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>A diagnosztikai bővítmény telepítése

Az alapvető állomás adatok gyűjtése le elérhető, de a részletesebb és Virtuálisgép-specifikus metrika, meg kell telepítenie az Azure diagnostics bővítményt a virtuális Gépen. Az Azure diagnosztikai bővítményének segítségével további monitorozási és diagnosztikai adatok kérdezhetők le a virtuális gépről. Megtekintheti ezeket a teljesítménymetrikákat, és a virtuális gép teljesítményétől függő riasztásokat hozhat létre. A diagnosztikai bővítmény telepítését az Azure Portalon végezheti el a következő módon:

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroup**, majd a **myVM** elemet az erőforrások listájából.
2. Kattintson a **Diagnosztikai beállítások** lehetőségre. A listából látható, hogy a *rendszerindítási diagnosztika* az előző szakaszban már engedélyezve lett. Jelölje be az *Alapmetrikák* jelölőnégyzetet.
3. Kattintson a **vendégszintű a figyelés bekapcsolható** gombra.

    ![Diagnosztikai metrikák megtekintése](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Virtuálisgép-metrikák megtekintése

A virtuális gép metrikái ugyanúgy tekinthetők meg, mint korábban a virtuális gazdagép metrikái:

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroup**, majd a **myVM** elemet az erőforrások listájából.
2. A virtuális gép teljesítményének nyomon követéséhez kattintson a **Metrikák** elemre a virtuális gép paneljén, majd válassza ki valamelyik diagnosztikai metrikát a **Rendelkezésre álló metrikák** területen.

    ![Virtuálisgép-metrikák megtekintése](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Riasztások létrehozása

Létrehozhat megadott teljesítménymetrikákon alapuló riasztásokat. A riasztások segítségével értesülhet például arról, ha az átlagos processzorhasználat meghalad egy bizonyos küszöbértéket vagy a rendelkezésre álló szabad lemezterület egy adott érték alá csökken. A riasztások megjeleníthetők az Azure Portalon vagy elküldhetők e-mailben. A létrehozott riasztásokra adott válaszként aktiválhatók Azure Automation-runbookok vagy Azure Logic Apps-alkalmazások.

A következő példában az átlagos processzorhasználat alapján hozunk létre riasztást.

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroup**, majd a **myVM** elemet az erőforrások listájából.
2. Kattintson a **Riasztási szabályok** elemre a virtuális gép paneljén, majd a **Metrikariasztás hozzáadása** lehetőségre a riasztási panel felső részén.
4. Adjon meg egy **nevet** a riasztás számára, például *myAlertRule*
5. Ha szeretne riasztást aktiválni, amikor a processzorhasználat 5 percig meghaladja az 1,0 értéket, hagyja változatlanul az összes többi alapértelmezett beállítást.
6. E-mail-értesítés küldéséhez jelölje be az *E-mail küldése a tulajdonosoknak, közreműködőknek és olvasóknak* jelölőnégyzetet. Az alapértelmezett művelet az értesítés megjelenítése a portálon.
7. Kattintson az **OK** gombra.

## <a name="manage-windows-updates"></a>Windows-frissítések kezelése

Frissítéskezelés lehetővé teszi a frissítések és javítások kezelheti a Windows Azure virtuális gépeken.
A virtuális gépről gyorsan felmérheti az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, és áttekintheti a telepítési eredményeket, hogy ellenőrizze, sikeres volt-e a frissítések telepítése a virtuális gépen.

Díjszabási információkért tekintse meg az [Automation Update Management-díjszabását](https://azure.microsoft.com/pricing/details/automation/) ismertető cikket.

### <a name="enable-update-management"></a>Az Update Management engedélyezése

A virtuális gép frissítéskezelés engedélyezése:
 
1. A képernyő bal oldalán válassza a **Virtuális gépek** elemet.
2. Válasszon ki egy virtuális gépet a listából.
3. A virtuális gép képernyőjének **Műveletek** szakaszában kattintson a **Frissítéskezelés** elemre. Ekkor megnyílik **Az Update Management engedélyezése** képernyő.

A rendszer ellenőrzi, hogy az Update Management engedélyezve van-e a virtuális gépen. A rendszer eközben azt is ellenőrzi, hogy létezik-e Log Analytics-munkaterület és egy csatlakoztatott Automation-fiók, valamint hogy a megoldás már jelen van-e a munkaterületen.

A Log Analytics-munkaterület az Update Management, valamint a hasonló funkciók és szolgáltatások által létrehozott adatok gyűjtésére szolgál. A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez. A frissítést igénylő virtuális gépeken további műveletek elvégzése érdekében az Azure Automation-szkriptek futtatását is lehetővé teszi a virtuális gépeken (pl. letöltés és frissítések alkalmazása).

Az ellenőrzési folyamat arra is kiterjed, hogy a virtuális gépen működik-e a Microsoft Monitoring Agent (MMA) és egy hibrid feldolgozó. Ez az ügynök kommunikál a virtuális géppel, továbbá begyűjti a frissítési állapottal kapcsolatos információkat. 

Ha az előfeltételek nem teljesülnek, egy szalagcím jelenik meg, amelyen engedélyezheti a megoldást.

![Az Update Management felvételének konfigurációs szalagcíme](./media/tutorial-monitoring/manageupdates-onboard-solution-banner.png)

A megoldás engedélyezéséhez kattintson a szalagcímre. Ha az ellenőrzést követően az alábbi előfeltételek bármelyike hiányzik, a rendszer automatikusan hozzáadja azt:

* [Log Analytics](../../log-analytics/log-analytics-overview.md)-munkaterület
* [Automatizálás](../../automation/automation-offering-get-started.md)
* Engedélyezett [hibrid runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md) a virtuális gépen

Ekkor megnyílik **Az Update Management engedélyezése** képernyő. Adja meg a beállításokat, és kattintson az **Engedélyezés** gombra.

![Az Update Management megoldás engedélyezése](./media/tutorial-monitoring/manageupdates-update-enable.png)

A megoldás engedélyezése akár 15 percet is igénybe vehet. Ez idő alatt ne zárja be a böngészőablakot. A megoldás engedélyezését követően a virtuális gép hiányzó frissítéseivel kapcsolatos adatok elkezdenek beérkezni a Log Analytics szolgáltatásba.
Az adatok legalább 30 perc és legfeljebb 6 óra múlva állnak készen az elemzésre.

### <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

A **Frissítéskezelés** engedélyezése után megjelenik a **Frissítéskezelés** képernyő. A **Hiányzó frissítések** lapon a hiányzó frissítések listája látható.

 ![Frissítés állapotának megtekintése](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítések telepítéséhez ütemezzen egy olyan telepítést, amely megfelel a kiadási ütemtervnek és a szolgáltatási időkeretnek.
Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

Ütemezzen egy új frissítéstelepítést a virtuális géphez. Ehhez kattintson a **Frissítéskezelés** képernyő felső részén található **Frissítések központi telepítésének ütemezése** elemre. Az **Új frissítéstelepítés** képernyőn adja meg a következő információkat:

* **Név** – Adjon meg egy egyedi nevet a frissítéstelepítés azonosításához.
* **Frissítési besorolás** – Válassza ki azokat a szoftvertípusokat, amelyeket a frissítéstelepítés belefoglal a telepítésbe. A választható besorolási típusok a következők:
  * Kritikus frissítések
  * Biztonsági frissítések
  * Kumulatív frissítések
  * Funkciócsomagok
  * Szervizcsomagok
  * Definíciófrissítések
  * Eszközök
  * Frissítések

* **Ütemezési beállítások** – Elfogadhatja az alapértelmezett időpontot, amely a 30 perccel az aktuális idő utáni időpont, vagy megadhat egy másik időpontot.
  Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődjön. Ha ismétlődő ütemezést szeretne beállítani, az Ismétlődés alatt kattintson az Ismétlődő lehetőségre.

  ![A frissítés ütemezés beállításai képernyője](./media/tutorial-monitoring/manageupdates-schedule-win.png)

* **Karbantartási időszak (perc)** – Adja meg azt az időtartamot, amelyen belül szeretné, hogy a frissítés telepítése megtörténjen.  Ez biztosítja, hogy a módosítások a megadott szolgáltatási időkereten belül menjenek végbe.

Ha befejezte az ütemezés konfigurálását, kattintson a **Létrehozás** gombra. Ezután visszalép az állapot-irányítópultra.
Ekkor az **Ütemezett** táblázatban már látható az Ön által létrehozott telepítésütemezés.

> [!WARNING]
> A számítógép újraindítása szükséges frissítések a virtuális gép automatikusan újraindul.

### <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, a **Frissítéskezelés** képernyő **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota.
Ha éppen fut, az állapota **Folyamatban**. Ha sikeresen befejeződik, **Sikeres** állapotúra változik.
Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, az állapota **Részben sikertelen**.
Ha rákattint a befejezett frissítéstelepítésre, megjelenik az adott frissítéstelepítés irányítópultja.

   ![Adott telepítés frissítéstelepítési állapot-irányítópultja](./media/tutorial-monitoring/manageupdates-view-results.png)

A **Frissítés eredményei** csempe összesíti a frissítések teljes számát és az adott virtuális gépre vonatkozó telepítési eredményeket.
A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza, amely a következők egyike lehet:

* **Nem lett megkísérelve** – a frissítés nem lett telepítve, mert a megadott karbantartási időszak alapján nem lett volna rá elég idő.
* **Sikeres** – a frissítés sikeres volt
* **Sikertelen** – a frissítés sikertelen volt

Kattintson a **Minden napló** csempére a telepítés által létrehozott összes naplóbejegyzés megtekintéséhez.

Kattintson a **Kimenet** csempére azon runbook feladatstreamjének megtekintéséhez, amely a frissítések telepítését kezeli a cél virtuális gépen.

Kattintson a **Hibák** csempére a telepítés közben felmerülő hibák részletes információinak megtekintéséhez.

## <a name="advanced-monitoring"></a>Speciális figyelés 

Az [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) használatával fejlettebb virtuálisgép-monitorozási megoldásokat is alkalmazhat. Regisztráljon az Operations Management Suite [ingyenes próbaverziójára](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial), ha még nem tette meg.

Ha rendelkezik hozzáféréssel az OMS-portálhoz, a Beállítások panelen találja a munkaterület kulcsát és azonosítóját. Használja a [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) parancs futtatásával adja hozzá az OMS-bővítményt a virtuális Gépet. Frissíti a változó értékét az alábbi minta megfelelően, OMS-munkaterület kulcs és a munkaterület azonosítóját.  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

Néhány perc múlva megtekintheti az új virtuális Gépet az OMS-munkaterület. 

![OMS panel](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban konfigurálva, és tekintse át a virtuális gépek az Azure Security Center. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása
> * Egy erőforráscsoport és a virtuális gép létrehozása 
> * Rendszerindítási diagnosztika engedélyezése a virtuális gépen
> * Rendszerindítási diagnosztika megtekintése
> * Gazdagép metrikáinak megtekintése
> * A diagnosztika-kiterjesztés telepítése
> * Virtuálisgép-metrikák megtekintése
> * Riasztás létrehozása
> * Windows-frissítések kezelése
> * Speciális monitorozás beállítása

A következő oktatóanyag az Azure security Centerrel kapcsolatos további továbblépés.

> [!div class="nextstepaction"]
> [A virtuális gépek biztonságának kezelése](./tutorial-azure-security.md)