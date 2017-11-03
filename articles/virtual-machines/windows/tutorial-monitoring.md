---
title: "Az Azure figyelés és frissítése és a Windows virtuális gépek |} Microsoft Docs"
description: "Az oktatóanyag - figyelésére és frissítésére a Windows rendszerű virtuális gép az Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: a37aed8b3321d3518ffd73e09f5bb21266a7e577
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-update-a-windows-virtual-machine-with-azure-powershell"></a>Figyelheti és frissítheti a Windows virtuális gépként az Azure PowerShell

Ügynökök Azure figyelés használ a rendszerindító és teljesítményadatokat gyűjteni Azure virtuális gépeken, ezek az adatok tárolása az Azure storage, és lehetővé teszi a portál, az Azure PowerShell modul és az Azure parancssori felület keresztül érhető el. Frissítéskezelés lehetővé teszi a frissítések és javítások kezelheti a Windows Azure virtuális gépeken.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A virtuális gép rendszerindítási diagnosztika engedélyezése
> * Rendszerindítási diagnosztika megtekintése
> * Virtuális gép gazdagép-metrikák megtekintése
> * A diagnosztika-kiterjesztés telepítése
> * Nézet VM metrikák
> * Riasztás létrehozása
> * Windows-frissítések kezelése
> * Speciális figyelés beállítása

Az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

A példa az oktatóanyag elvégzéséhez rendelkeznie kell egy meglévő virtuális gépet. Ha szükséges, ez [parancsfájl minta](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) hozhat létre egyet. Az oktatóanyag lépéseinek használatakor, cserélje ki az erőforráscsoportot, a virtuális gép nevét és a helyet, ha szükséges.

## <a name="view-boot-diagnostics"></a>Rendszerindítási diagnosztika megtekintése

Indítsa el a Windows virtuális gépek, a rendszerindítási diagnosztikai ügynök rögzíti a képernyő kimeneti hibaelhárítási céllal használható. Ez a funkció alapértelmezés szerint engedélyezve van. A rögzített képernyőképek tárolódnak az Azure storage-fiók, amely alapértelmezés szerint is létrejön. 

A rendszerindítási diagnosztikai adatok kaphat a [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata) parancsot. A következő példában a rendszerindítási diagnosztika gyökerébe letöltődnek a * c:\* meghajtó. 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Gazdagép-metrikák megtekintése

Egy Windows virtuális gép a gazdagép dedikált virtuális gépek rendelkezik, amely hatással van az Azure-ban. Metrikák automatikusan összegyűjtött ahhoz, hogy a gazdagép és az Azure portálon is megtekinthetők.

1. Az Azure portálon kattintson **erőforráscsoportok**, jelölje be **myResourceGroup**, majd válassza ki **myVM** erőforrás listájában.
2. Kattintson a **metrikák** a virtuális gép panelen, majd válassza ki a gazdagép-metrikák bármelyikét **elérhető** tekintheti meg, hogyan működik-e a gazdagép virtuális.

    ![Gazdagép-metrikák megtekintése](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Diagnosztika-kiterjesztés telepítése

Az alapvető állomás adatok gyűjtése le elérhető, de a részletesebb és Virtuálisgép-specifikus metrika, meg kell telepítenie az Azure diagnostics bővítményt a virtuális Gépen. Az Azure diagnostics bővítmény lehetővé teszi, hogy további figyelési és diagnosztikai adatokat beolvasni a virtuális gépről. Megtekintheti a metrikák és riasztások alapján hogyan hajtja végre a virtuális gép létrehozása. A diagnosztikai bővítmény telepítve van az Azure portálon keresztül az alábbiak szerint:

1. Az Azure portálon kattintson **erőforráscsoportok**, jelölje be **myResourceGroup**, majd válassza ki **myVM** erőforrás listájában.
2. Kattintson a **diagnosztikai beállítások**. A lista azt mutatja, hogy *rendszerindítási diagnosztika* már engedélyezve van az előző szakaszából. Jelölje be a jelölőnégyzetet a *alapvető metrikák*.
3. Kattintson a **vendégszintű a figyelés bekapcsolható** gombra.

    ![Nézet diagnosztikai metrikák](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Nézet VM metrikák

A virtuális gép mérni, hogy megtekinthetők-e a gazdagép VM metrikák azonos módon tekintheti meg:

1. Az Azure portálon kattintson **erőforráscsoportok**, jelölje be **myResourceGroup**, majd válassza ki **myVM** erőforrás listájában.
2. Hogyan működik-e a virtuális gép megtekintéséhez kattintson **metrikák** a virtuális gép panelen, majd válassza ki a diagnosztika mérőszámok alapján bármelyikét **elérhető**.

    ![Nézet VM metrikák](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Riasztások létrehozása

Riasztások adott mérőszámok alapján hozhat létre. Riasztások értesíti, amikor az átlagos CPU-használat meghaladja az egy bizonyos küszöb vagy a rendelkezésre álló szabad lemezterületet alá csökken egy adott értékre, például használható. Riasztások jelennek meg az Azure portálon, vagy e-mailben küldhetők el. Riasztás generálása Azure Automation-forgatókönyveket vagy Azure Logic Apps is elindítható.

A következő példa az átlagos processzorhasználat riasztást hoz létre.

1. Az Azure portálon kattintson **erőforráscsoportok**, jelölje be **myResourceGroup**, majd válassza ki **myVM** erőforrás listájában.
2. Kattintson a **riasztási szabályok** virtuális gép paneljén kattintson a **metrika riasztás hozzáadása** a riasztások panel tetején.
4. Adjon meg egy **neve** a riasztás például *myAlertRule*
5. Riasztást vált ki, ha processzor 1.0 meghaladja 5 percig, hagyja a többi alapértelmezett kiválasztva.
6. Szükség esetén jelölje be a *E-mail-tulajdonosok, közreműködőknek és olvasóknak* e-mail értesítést küldeni. Az alapértelmezett művelet is értesítést megjeleníteni a portálon.
7. Kattintson az **OK** gombra.

## <a name="manage-windows-updates"></a>Windows-frissítések kezelése

Frissítéskezelés lehetővé teszi a frissítések és javítások kezelheti a Windows Azure virtuális gépeken.
Közvetlenül a virtuális gépről, gyorsan mérje fel a rendelkezésre álló frissítések állapotát, kötelező frissítések telepítésének ütemezése, és tekintse át a központi telepítési eredmények ellenőrzése a frissítések alkalmazása sikeresen megtörtént a virtuális géphez.

Díjszabási információkért lásd: [Automation frissítéskezelés az díjszabása](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Frissítéskezelés engedélyezése

A virtuális gép frissítéskezelés engedélyezése:
 
1. A képernyő bal oldalán kattintson **virtuális gépek**.
2. A listában jelölje ki a virtuális gépek.
3. A virtuális gép képernyőn a a **műveletek** kattintson **frissítéskezelés**. A **engedélyezze Frissítéskezelésről** képernyőn megnyílik.

Az érvényesség annak meghatározásához, hogy engedélyezve van-e frissítéskezelésről a virtuális gép. Az érvényesítési tartalmaz ellenőrzi a Naplóelemzési munkaterület és a csatolt Automation-fiók, és ha a megoldás a munkaterületen.

A Naplóelemzési munkaterület funkciókat és szolgáltatásokat, például a frissítéskezelés által generált adatok összegyűjtésére szolgál. A munkaterületen áttekintheti, és a különböző forrásokból származó adatok elemzése egyetlen helyen. A virtuális gépeken, amelyek a frissítés szükséges további művelet végrehajtásához Azure Automation futtatását teszi-szkriptek használatát a virtuális gépek, többek között letöltéséhez, és alkalmazza a frissítéseket.

Az érvényesítési folyamat is ellenőrzi, hogy ha a virtuális gép ki van építve a Microsoft Monitoring Agent (MMA) és a hibrid feldolgozó. Ez az ügynök segítségével kommunikálnak a virtuális gép és a frissítési állapot kapcsolatos információkhoz. 

Ha az előfeltételek nem teljesülnek, a megoldás lehetővé teszi lehetővé teszi egy fejléc jelenik meg.

![Felügyeleti előkészítésére konfigurációs szalagcím frissítése](./media/tutorial-monitoring/manageupdates-onboard-solution-banner.png)

A megoldás engedélyezéséhez kattintson a szalagcímre. Ha az ellenőrzés után nem található a következő előfeltételek bármelyike található, akkor automatikusan megkapja:

* [Naplófájl Analytics](../../log-analytics/log-analytics-overview.md) munkaterület
* [Automatizálás](../../automation/automation-offering-get-started.md)
* A [hibrid forgatókönyv-feldolgozó](../../automation/automation-hybrid-runbook-worker.md) engedélyezve van a virtuális Gépen

A **engedélyezze Frissítéskezelésről** képernyőn megnyílik. Adja meg a beállításokat, és kattintson a **engedélyezése**.

![Frissítés felügyeleti megoldás engedélyezése](./media/tutorial-monitoring/manageupdates-update-enable.png)

A megoldás engedélyezése akár 15 percig is eltarthat, és ebben az időszakban, akkor nem zárja be a böngészőablakot. A megoldás engedélyezése után információ a frissítések a virtuális Gépre szolgáltatáshoz zajlik.
30 perc és 6 óra elemzéshez használható az adatok között is igénybe vehet.

### <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

A **Frissítéskezelés** engedélyezése után megjelenik a **Frissítéskezelés** képernyő. A **Hiányzó frissítések** lapon a hiányzó frissítések listája látható.

 ![Frissítési állapotának megtekintése](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítések telepítéséhez ütemezzen egy olyan telepítést, amely megfelel a kiadási ütemtervnek és a szolgáltatási időkeretnek.
Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például megadhatja, hogy a kritikus vagy biztonsági frissítések és -kizárási kumulatív frissítését.

A virtuális Gépet egy új központi telepítésének ütemezése kattintva **ütemezés központi telepítésének** tetején a **frissítéskezelés** képernyő. Az a **új üzemelő példány frissítése** képernyőn, adja meg a következőket:

* **Név** – Adjon meg egy egyedi nevet a frissítéstelepítés azonosításához.
* **Frissítés besorolása** – a frissítés telepítése a központi telepítésben lévő szoftvertípusok kiválasztása. A választható besorolási típusok a következők:
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
Figyelje meg, hogy a **ütemezett** táblázat mutatja a központi telepítési ütemezés létrehozott.

> [!WARNING]
> A számítógép újraindítása szükséges frissítések a virtuális gép automatikusan újraindul.

### <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, a **Frissítéskezelés** képernyő **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota.
Ha éppen fut, az állapota **Folyamatban**. Ha sikeresen befejeződik, **Sikeres** állapotúra változik.
Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, az állapota **Részben sikertelen**.
Ha rákattint a befejezett frissítéstelepítésre, megjelenik az adott frissítéstelepítés irányítópultja.

   ![Frissítés telepítési állapotát irányítópult az adott központi telepítéshez](./media/tutorial-monitoring/manageupdates-view-results.png)

A **frissítésének elmulasztása az** csempe a frissítések és a virtuális gép telepítési eredmények száma összegzését.
A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza, amely a következők egyike lehet:

* **Nem történt kísérlet** – a frissítés nem lett telepítve, mert nincs elegendő rendelkezésre álló idő definiált karbantartási ablak időtartam alapján.
* **Sikeres** – a frissítés telepítése sikeres volt
* **Nem sikerült** – a frissítés nem sikerült

Kattintson a **Minden napló** csempére a telepítés által létrehozott összes naplóbejegyzés megtekintéséhez.

Kattintson a **kimeneti** csempe a runbook a frissítés telepítése a cél virtuális gép kezeléséért feladatstream megjelenítéséhez.

Kattintson a **Hibák** csempére a telepítés közben felmerülő hibák részletes információinak megtekintéséhez.

## <a name="advanced-monitoring"></a>Speciális figyelés 

Fejlettebb, figyelés, a virtuális gép segítségével teheti [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Ha még nem tette meg, akkor regisztrálhatnak az egy [ingyenes próbaverzió](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) az Operations Management Suite szolgáltatásban.

Ha rendelkezik az OMS-portállal, találja a kulcsát és a munkaterület azonosítóját a beállítások panelen. Használja a [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) parancs futtatásával adja hozzá az OMS-bővítményt a virtuális Gépet. Frissíti a változó értékét az alábbi minta megfelelően, OMS-munkaterület kulcs és a munkaterület azonosítóját.  

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

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban konfigurálva, és tekintse át a virtuális gépek az Azure Security Center. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása
> * Egy erőforráscsoport és a virtuális gép létrehozása 
> * Rendszerindítási diagnosztika a virtuális Gépre engedélyezése
> * Rendszerindítási diagnosztika megtekintése
> * Gazdagép-metrikák megtekintése
> * A diagnosztika-kiterjesztés telepítése
> * Nézet VM metrikák
> * Riasztás létrehozása
> * Windows-frissítések kezelése
> * Speciális figyelés beállítása

A következő oktatóanyag az Azure security Centerrel kapcsolatos további továbblépés.

> [!div class="nextstepaction"]
> [A virtuális gépek biztonságának kezelése](./tutorial-azure-security.md)