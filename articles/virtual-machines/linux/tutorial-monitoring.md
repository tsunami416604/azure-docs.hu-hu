---
title: Linux rendszerű virtuális gépek monitorozása és frissítése az Azure-ban | Microsoft Docs
description: Tudnivalók Linux rendszerű virtuális gépek Azure-beli rendszerindítási diagnosztikájának és teljesítménymetrikáinak monitorozásáról, valamint csomagfrissítéseinek felügyeletéről
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d5fb239ffd6a957cbb088bf4843819e2c886cee8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Linux rendszerű virtuális gépek monitorozása és frissítése az Azure-ban

Annak érdekében, hogy az Azure-beli virtuális gépek megfelelően működjenek, áttekintheti a rendszerindítási diagnosztikát és a teljesítménymetrikákat, és felügyelheti a csomagfrissítéseket. Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendszerindítási diagnosztika engedélyezése a virtuális gépen
> * Rendszerindítási diagnosztika megtekintése
> * Gazdagép metrikáinak megtekintése
> * Diagnosztikai bővítmény engedélyezése a virtuális gépen
> * Virtuálisgép-metrikák megtekintése
> * Riasztások létrehozása diagnosztikai metrikák alapján
> * Csomagfrissítések felügyelete
> * A módosítások és a leltár monitorozása
> * Speciális monitorozás beállítása

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Virtuális gép létrehozása

A diagnosztika és a metrikák működés közbeni megtekintéséhez egy virtuális gépre van szükség. Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroupMonitor* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Most hozzon létre egy virtuális gépet az [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) paranccsal. Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Rendszerindítási diagnosztika engedélyezése

A Linux rendszerű virtuális gépek rendszerindítójaként működő rendszerindítási diagnosztikai bővítmény rögzíti a rendszerindítás kimeneti adatait, és az Azure Storage-ben tárolja azokat. Ezek az adatok a virtuális gépek rendszerindítási problémáinak hibaelhárításához használhatóak. Ha a Linux rendszerű virtuális gépet az Azure CLI-vel hozza létre, a rendszerindítási diagnosztika nincs automatikusan engedélyezve.

Ahhoz, hogy a rendszerindítási diagnosztikát engedélyezni lehessen, tárfiókot kell létrehozni a rendszerindítási naplók tárolásához. A tárfiókoknak globálisan egyedi névvel kell rendelkezniük, amelynek hossza 3–24 karakter lehet, továbbá csak számokat és kisbetűket tartalmazhat. Az [az storage account create](/cli/azure/storage/account#az_storage_account_create) paranccsal hozzon létre egy tárfiókot. Ebben a példában az egyedi tárfióknév létrehozásához véletlenszerű karakterláncot használunk.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

A rendszerindítási diagnosztika engedélyezéséhez szükség van a Blob Storage-tároló URI-jére. A tárfiókok lekérdezésekor a következő lekérdezési parancsok adják vissza ezt az URI-t. Ezt az URI értéket a *bloburi* változóban tárolja a rendszer. A változóra a következő lépésben lesz szükség.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Most már engedélyezheti a rendszerindítási diagnosztikát az [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable) paranccsal. A `--storage` érték az előző lépésben lekért blob URI.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Rendszerindítási diagnosztika megtekintése

Ha a rendszerindítási diagnosztika engedélyezve van, a rendszer a virtuális gép minden egyes elindításakor és leállításakor egy naplófájlba írja a rendszerindítási folyamat adatait. Ebben a példában először szabadítsa fel a virtuális gépet az [az vm deallocate](/cli/azure/vm#az_vm_deallocate) paranccsal a következőképpen:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Most indítsa el a virtuális gépet az [az vm start]( /cli/azure/vm#az_vm_stop) paranccsal a következő módon:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

A *myVM* virtuális gép rendszerindítási diagnosztikai adatait az [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log) paranccsal kérheti le a következő módon:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Gazdagép metrikáinak megtekintése

A Linux rendszerű virtuális gépek egy dedikált gazdagéppel interaktálnak az Azure-ban. A rendszer automatikusan gyűjti a gazdagép metrikáit, amelyek a következőképpen tekinthetők meg az Azure Portalon:

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroupMonitor**, majd a **myVM** elemet az erőforrások listájából.
1. A virtuális gazdagép teljesítményének megtekintéséhez kattintson a **Metrikák** elemre a virtuális gép paneljén, majd válassza ki valamelyik *[Gazdagép]* metrikát a **Rendelkezésre álló metrikák** területen.

    ![Gazdagép metrikáinak megtekintése](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>A diagnosztikai bővítmény telepítése

> [!IMPORTANT]
> Ez a dokumentum a Linux diagnosztikai bővítmény 2.3-as verzióját ismerteti, amely már elavult. A 2.3-as verzió támogatása 2018. június 30-tól megszűnik.
>
> Ettől az időponttól kezdve a Linux diagnosztikai bővítmény 3.0-s verziója engedélyezhető a rendszeren. További információkat [a dokumentációban](./diagnostic-extension.md) talál.

Az alapvető gazdagépmetrikák elérhetőek, de a részletesebb és a virtuálisgép-specifikus metrikák megtekintéséhez telepíteni kell az Azure diagnosztikai bővítményét a virtuális gépen. Az Azure diagnosztikai bővítményének segítségével további monitorozási és diagnosztikai adatok kérdezhetők le a virtuális gépről. Megtekintheti ezeket a teljesítménymetrikákat, és a virtuális gép teljesítményétől függő riasztásokat hozhat létre. A diagnosztikai bővítmény telepítését az Azure Portalon végezheti el a következő módon:

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroup**, majd a **myVM** elemet az erőforrások listájából.
1. Kattintson a **Diagnosztikai beállítások** lehetőségre. A listából látható, hogy a *rendszerindítási diagnosztika* az előző szakaszban már engedélyezve lett. Jelölje be az *Alapmetrikák* jelölőnégyzetet.
1. A *Tárfiók* szakaszban keresse meg és jelölje ki az előző szakaszban létrehozott *mydiagdata[1234]* fiókot.
1. Kattintson a **Mentés** gombra.

    ![Diagnosztikai metrikák megtekintése](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Virtuálisgép-metrikák megtekintése

A virtuális gép metrikái ugyanúgy tekinthetők meg, mint korábban a virtuális gazdagép metrikái:

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroup**, majd a **myVM** elemet az erőforrások listájából.
1. A virtuális gép teljesítményének nyomon követéséhez kattintson a **Metrikák** elemre a virtuális gép paneljén, majd válassza ki valamelyik diagnosztikai metrikát a **Rendelkezésre álló metrikák** területen.

    ![Virtuálisgép-metrikák megtekintése](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Riasztások létrehozása

Létrehozhat megadott teljesítménymetrikákon alapuló riasztásokat. A riasztások segítségével értesülhet például arról, ha az átlagos processzorhasználat meghalad egy bizonyos küszöbértéket vagy a rendelkezésre álló szabad lemezterület egy adott érték alá csökken. A riasztások megjeleníthetők az Azure Portalon vagy elküldhetők e-mailben. A létrehozott riasztásokra adott válaszként aktiválhatók Azure Automation-runbookok vagy Azure Logic Apps-alkalmazások.

A következő példában az átlagos processzorhasználat alapján hozunk létre riasztást.

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroup**, majd a **myVM** elemet az erőforrások listájából.
2. Kattintson a **Riasztási szabályok** elemre a virtuális gép paneljén, majd a **Metrikariasztás hozzáadása** lehetőségre a riasztási panel felső részén.
3. Adjon meg egy **nevet** a riasztás számára, például *myAlertRule*
4. Ha szeretne riasztást aktiválni, amikor a processzorhasználat 5 percig meghaladja az 1,0 értéket, hagyja változatlanul az összes többi alapértelmezett beállítást.
5. E-mail-értesítés küldéséhez jelölje be az *E-mail küldése a tulajdonosoknak, közreműködőknek és olvasóknak* jelölőnégyzetet. Az alapértelmezett művelet az értesítés megjelenítése a portálon.
6. Kattintson az **OK** gombra.

## <a name="manage-package-updates"></a>Csomagfrissítések felügyelete

A frissítéskezelés segítségével kezelheti az Azure-beli linuxos virtuális gépek frissítéseit és javításait.
A virtuális gépről gyorsan felmérheti az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, és áttekintheti a telepítési eredményeket, hogy ellenőrizze, sikeres volt-e a frissítések telepítése a virtuális gépen.

Díjszabási információkért tekintse meg az [Automation Update Management-díjszabását](https://azure.microsoft.com/pricing/details/automation/) ismertető cikket.

### <a name="enable-update-management"></a>Az Update Management engedélyezése

Az Update management engedélyezése a virtuális géphez:

1. A képernyő bal oldalán válassza a **Virtuális gépek** elemet.
2. Válasszon ki egy virtuális gépet a listából.
3. A virtuális gép képernyőjének **Műveletek** szakaszában kattintson a **Frissítéskezelés** elemre. Ekkor megnyílik **Az Update Management engedélyezése** képernyő.

A rendszer ellenőrzi, hogy az Update Management engedélyezve van-e a virtuális gépen.
A rendszer eközben azt is ellenőrzi, hogy létezik-e Log Analytics-munkaterület és egy csatlakoztatott Automation-fiók, valamint hogy a megoldás már jelen van-e a munkaterületen.

A [Log Analytics](../../log-analytics/log-analytics-overview.md)-munkaterület az Update Management, valamint a hasonló funkciók és szolgáltatások által létrehozott adatok gyűjtésére szolgál.
A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez.
A frissítést igénylő virtuális gépeken további műveletek elvégzése érdekében az Azure Automation runbookok futtatását is lehetővé teszi a virtuális gépeken (pl. letöltés és frissítések alkalmazása).

Az ellenőrzési folyamat arra is kiterjed, hogy a virtuális gépen működik-e a Microsoft Monitoring Agent (MMA) és az Automation hibrid runbook-feldolgozó.
Ez az ügynök kommunikál a virtuális géppel, továbbá begyűjti a frissítési állapottal kapcsolatos információkat.

A megoldás engedélyezéséhez válassza ki a Log Analytics-munkaterületet és az Automation-fiókot, majd kattintson az **Engedélyezés** gombra. A megoldás engedélyezése akár 15 percet is igénybe vehet.

Ha az előkészítés közben az alábbi előfeltételek bármelyike hiányzik, a rendszer automatikusan hozzáadja azt:

* [Log Analytics](../../log-analytics/log-analytics-overview.md)-munkaterület
* [Automatizálás](../../automation/automation-offering-get-started.md)
* Engedélyezett [hibrid runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md) a virtuális gépen

Megnyílik az **Update Management** képernyő. Konfigurálja a használni kívánt helyet, Log Analytics-munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés** gombra. Ha a mezők szürkén jelennek meg, az azt jelenti, hogy egy másik automatizálási megoldás már engedélyezve van a virtuális gépen, ezért az ahhoz tartozó munkaterületet és Automation-fiókot kell használnia.

![Az Update Management megoldás engedélyezése](./media/tutorial-monitoring/manage-updates-update-enable.png)

A megoldás engedélyezése akár 15 percet is igénybe vehet. Ez idő alatt ne zárja be a böngészőablakot. A megoldás engedélyezését követően a virtuális gép hiányzó frissítéseivel kapcsolatos adatok elkezdenek beérkezni a Log Analytics szolgáltatásba. Az adatok legalább 30 perc és legfeljebb 6 óra múlva állnak készen az elemzésre.

### <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

A **Frissítéskezelés** engedélyezése után megjelenik a **Frissítéskezelés** képernyő. Miután elkészült a frissítések kiértékelése, a **Hiányzó frissítések** lapon megjelenik a hiányzó frissítések listája.

 ![Frissítés állapotának megtekintése](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítések telepítéséhez ütemezzen egy olyan telepítést, amely megfelel a kiadási ütemtervnek és a szolgáltatási időkeretnek. Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

Ütemezzen egy új frissítéstelepítést a virtuális géphez. Ehhez kattintson a **Frissítéskezelés** képernyő felső részén található **Frissítések központi telepítésének ütemezése** elemre. Az **Új frissítéstelepítés** képernyőn adja meg a következő információkat:

* **Név** – Adjon meg egy egyedi nevet a frissítéstelepítés azonosításához.
* **Frissítési besorolás** – Válassza ki azokat a szoftvertípusokat, amelyeket a frissítéstelepítés belefoglal a telepítésbe. A választható besorolási típusok a következők:
  * Kritikus vagy biztonsági frissítések
  * Egyéb frissítések
* **Kihagyandó frissítések** – Megadhatja, hogy a frissítések telepítése során a rendszer mely csomagneveket hagyja ki. A csomagnevek támogatják a helyettesítő karakterek használatát (például \*kernel\*).

  ![A frissítés ütemezés beállításai képernyője](./media/tutorial-monitoring/manage-updates-exclude-linux.png)

* **Ütemezési beállítások** – Elfogadhatja az alapértelmezett időpontot, amely a 30 perccel az aktuális idő utáni időpont, vagy megadhat egy másik időpontot.
  Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődjön. Ha ismétlődő ütemezést szeretne beállítani, az Ismétlődés alatt kattintson az Ismétlődő lehetőségre.

  ![A frissítés ütemezés beállításai képernyője](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Karbantartási időszak (perc)** – Adja meg azt az időtartamot, amelyen belül szeretné, hogy a frissítés telepítése megtörténjen. Ez biztosítja, hogy a módosítások a megadott szolgáltatási időkereten belül menjenek végbe.

Ha befejezte az ütemezés konfigurálását, kattintson a **Létrehozás** gombra. Ezután visszalép az állapot-irányítópultra.
Ekkor az **Ütemezett** táblázatban már látható az Ön által létrehozott telepítésütemezés.

> [!WARNING]
> Az újraindítást igénylő frissítések esetén a rendszer automatikusan újraindítja a virtuális gépet.

### <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, az **Update Management** képernyő **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota.
Ha éppen fut, az állapota **Folyamatban**. Ha sikeresen befejeződik, **Sikeres** állapotúra változik.
Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, az állapota **Részben sikertelen**.
Ha rákattint a befejezett frissítéstelepítésre, megjelenik az adott frissítéstelepítés irányítópultja.

![Adott telepítés frissítéstelepítési állapot-irányítópultja](./media/tutorial-monitoring/manage-updates-view-results.png)

A **Frissítés eredményei** csempe összesíti a frissítések teljes számát és az adott virtuális gépre vonatkozó telepítési eredményeket.
A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza, amely a következők egyike lehet:

* **Nem lett megkísérelve** – a frissítés nem lett telepítve, mert a megadott karbantartási időszak alapján nem lett volna rá elég idő.
* **Sikeres** – a frissítés sikeres volt
* **Sikertelen** – a frissítés sikertelen volt

Kattintson a **Minden napló** csempére a telepítés által létrehozott összes naplóbejegyzés megtekintéséhez.

Kattintson a **Kimenet** csempére azon runbook feladatstreamjének megtekintéséhez, amely a frissítések telepítését kezeli a cél virtuális gépen.

Kattintson a **Hibák** csempére a telepítés közben felmerülő hibák részletes információinak megtekintéséhez.

## <a name="monitor-changes-and-inventory"></a>A módosítások és a leltár monitorozása

Összegyűjtheti a számítógépeken található szoftverek, fájlok, Linux-démonok, Windows-szolgáltatások és Windows-beállításkulcsok listáját, és leltárt készíthet belőlük. A gépek konfigurációjának nyomon követésével megtalálhatja a környezetben felmerülő működési problémákat, és alaposabban megismerheti a gépek állapotát.

### <a name="enable-change-and-inventory-management"></a>A változás- és leltárkezelés engedélyezése

A változás- és leltárkezelés engedélyezése a virtuális géphez:

1. A képernyő bal oldalán válassza a **Virtuális gépek** elemet.
2. Válasszon ki egy virtuális gépet a listából.
3. A virtuális gép képernyőjének **Műveletek** szakaszában kattintson az **Inventory** vagy a **Change tracking** elemre. Megnyílik **A Change Tracking and Inventory engedélyezése** képernyő.

Konfigurálja a használni kívánt helyet, Log Analytics-munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés** gombra. Ha a mezők szürkén jelennek meg, az azt jelenti, hogy egy másik automatizálási megoldás már engedélyezve van a virtuális gépen, ezért az ahhoz tartozó munkaterületet és Automation-fiókot kell használnia. Bár a megoldások elkülönülve jelennek meg a menüben, ugyanarról a megoldásról van szó. Ha engedélyezi az egyiket, a virtuális gépen mindkét megoldás engedélyezve lesz.

![A változások és a leltár követésének engedélyezése](./media/tutorial-monitoring/manage-inventory-enable.png)

A megoldás engedélyezését követően eltarthat egy ideig, amíg a virtuális gépen elkészül a leltár és megjelennek az adatok.

### <a name="track-changes"></a>Változások követése

A virtuális gépen kattintson a **Change Tracking** elemre a **MŰVELETEK** területen. A megnyíló **Change Tracking** lapon kattintson a **Beállítások szerkesztése** elemre. Jelölje be a követni kívánt beállításokat, majd kattintson a **+ Hozzáadás** gombra. A Linux esetében elérhető lehetőség a **Linux-fájlok**.

A Change Tracking megoldásról további információt a [Virtuális gép módosításainak hibaelhárítása](../../automation/automation-tutorial-troubleshoot-changes.md) című cikkben talál.

### <a name="view-inventory"></a>Leltár megtekintése

A virtuális gépen kattintson az **Inventory** elemre a **MŰVELETEK** területen. A **Szoftver** lapon egy táblázat formátumú lista jeleníti meg a felderített szoftvereket. Az egyes szoftverrekordok részletes információi megtekinthetők a táblázatban. Ilyen információ például a szoftver neve, verziója, közzétevője és a legutóbbi frissítés ideje.

![Leltár megtekintése](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Tevékenységnaplók és változások monitorozása

A virtuális gép **Change Tracking** lapján válassza a **Tevékenységnapló-kapcsolat kezelése** lehetőséget. Ez a feladat megnyitja az **Azure-tevékenységnapló** lapot. A **Csatlakozás** gombra kattintva csatlakoztathatja a Change Tracking megoldást a virtuális gép Azure-tevékenységnaplójához.

A beállítás engedélyezése után lépjen a virtuális gép **Áttekintés** lapjára, majd kattintson a **Leállítás** gombra a virtuális gép leállításához. A megjelenő üzenetben kattintson az **Igen** gombra a virtuális gép leállításához. A felszabadított virtuális gépeket az **Indítás** gombra kattintva indíthatja újra.

A virtuális gép leállítása és elindítása egy-egy eseményt rögzít a tevékenységnaplóban. Lépjen vissza a **Change Tracking** oldalra. Az oldal alján válassza az **Események** lapot. Az események idővel megjelennek a diagramban és a táblázatban. Az egyes események kijelölésével megjelenítheti a hozzájuk tartozó részletes adatokat.

![Változások megtekintése a tevékenységnaplóban](./media/tutorial-monitoring/manage-activitylog-view-results.png)

A diagram az eltelt idő alatt bekövetkezett változásokat mutatja. A Tevékenységnapló-kapcsolat hozzáadása után a felül látható vonaldiagramon az Azure tevékenységnapló eseményei jelennek meg. A sávdiagramok minden egyes sora más-más követhető változástípust jelenít meg. Ezek a típusok lehetnek Linux-démonok, fájlok és szoftverek. A Változások lapon jelennek meg a vizualizációban látható változások részletei a változás bekövetkezte szerinti fordított időrendben (vagyis a legutóbbi van legelöl).

## <a name="advanced-monitoring"></a>Speciális figyelés

Az [Azure Automation](../../automation/automation-intro.md) által kínált Update Management és Change and Inventory használatával fejlettebb virtuálisgép-monitorozási megoldásokat is alkalmazhat.

Ha rendelkezik hozzáféréssel a Log Analytics-munkaterülethez, a munkaterület kulcsát és azonosítóját a **BEÁLLÍTÁSOK** terület **Speciális beállítások** elemére kattintva találja meg. Cserélje le a \<workspace-key\> és a \<workspace-id\> helyőrzőt a Log Analytics-munkaterületről származó adatokra, majd az **az vm extension set** paranccsal adja hozzá a bővítményt a virtuális géphez:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

Pár perccel később meg kell jelennie az új virtuális gépnek a Log Analytics-munkaterületen.

![OMS panel](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy virtuális gép konfigurálását, áttekintését és frissítéseinek felügyeletét végezte el. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Rendszerindítási diagnosztika engedélyezése a virtuális gépen
> * Rendszerindítási diagnosztika megtekintése
> * Gazdagép metrikáinak megtekintése
> * Diagnosztikai bővítmény engedélyezése a virtuális gépen
> * Virtuálisgép-metrikák megtekintése
> * Riasztások létrehozása diagnosztikai metrikák alapján
> * Csomagfrissítések felügyelete
> * A módosítások és a leltár monitorozása
> * Speciális monitorozás beállítása

Folytassa a következő oktatóanyaggal, amely az Azure Security Center használatát ismerteti.

> [!div class="nextstepaction"]
> [A virtuális gépek biztonságának kezelése](./tutorial-azure-security.md)
