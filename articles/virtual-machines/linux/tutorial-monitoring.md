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
ms.openlocfilehash: 9ffd36da535a2e5ac4a355f429394dc4209348b7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Linux rendszerű virtuális gépek monitorozása és frissítése az Azure-ban

Annak érdekében, hogy az Azure-beli virtuális gépek megfelelően működjenek, áttekintheti a rendszerindítási diagnosztikát és a teljesítménymetrikákat, és felügyelheti a csomagfrissítéseket. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendszerindítási diagnosztika engedélyezése a virtuális gépen
> * Rendszerindítási diagnosztika megtekintése
> * Gazdagép metrikáinak megtekintése
> * Diagnosztikai bővítmény engedélyezése a virtuális gépen
> * Virtuálisgép-metrikák megtekintése
> * Riasztások létrehozása diagnosztikai metrikák alapján
> * Csomagfrissítések felügyelete
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
4. Adjon meg egy **nevet** a riasztás számára, például *myAlertRule*
5. Ha szeretne riasztást aktiválni, amikor a processzorhasználat 5 percig meghaladja az 1,0 értéket, hagyja változatlanul az összes többi alapértelmezett beállítást.
6. E-mail-értesítés küldéséhez jelölje be az *E-mail küldése a tulajdonosoknak, közreműködőknek és olvasóknak* jelölőnégyzetet. Az alapértelmezett művelet az értesítés megjelenítése a portálon.
7. Kattintson az **OK** gombra.

## <a name="manage-package-updates"></a>Csomagfrissítések felügyelete

Az Update management megoldással felügyelheti az Azure-beli Linux rendszerű virtuális gépek csomagfrissítéseit és javításait. A virtuális gépről gyorsan felmérheti az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, és áttekintheti a telepítési eredményeket, hogy ellenőrizze, sikeres volt-e a frissítések telepítése a virtuális gépen.

Díjszabási információkért tekintse meg az [Automation Update Management-díjszabását](https://azure.microsoft.com/pricing/details/automation/) ismertető cikket.

### <a name="enable-update-management-preview"></a>Az Update management (előzetes verzió) engedélyezése

Az Update management engedélyezése a virtuális géphez

1. A képernyő bal oldalán válassza a **Virtuális gépek** elemet.
1. Válasszon ki egy virtuális gépet a listából.
1. A virtuális gép képernyőjének **Műveletek** szakaszában kattintson a **Frissítéskezelés** elemre. Ekkor megnyílik **Az Update Management engedélyezése** képernyő.

A rendszer ellenőrzi, hogy az Update Management engedélyezve van-e a virtuális gépen. A rendszer eközben azt is ellenőrzi, hogy létezik-e Log Analytics-munkaterület és egy csatlakoztatott Automation-fiók, valamint hogy a megoldás már jelen van-e a munkaterületen.

A Log Analytics-munkaterület az Update Management, valamint a hasonló funkciók és szolgáltatások által létrehozott adatok gyűjtésére szolgál. A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez. A frissítést igénylő virtuális gépeken további műveletek elvégzése érdekében az Azure Automation-szkriptek futtatását is lehetővé teszi a virtuális gépeken (pl. letöltés és frissítések alkalmazása).

Az ellenőrzési folyamat arra is kiterjed, hogy a virtuális gépen működik-e a Microsoft Monitoring Agent (MMA) és egy hibrid feldolgozó. Ez az ügynök kommunikál a virtuális géppel, továbbá begyűjti a frissítési állapottal kapcsolatos információkat. 

Ha az előfeltételek nem teljesülnek, egy szalagcím jelenik meg, amelyen engedélyezheti a megoldást.

![Az Update Management felvételének konfigurációs szalagcíme](./media/tutorial-monitoring/manage-updates-onboard-solution-banner.png)

A megoldás engedélyezéséhez kattintson a szalagcímre. Ha az ellenőrzést követően az alábbi előfeltételek bármelyike hiányzik, a rendszer automatikusan hozzáadja azt:

* [Log Analytics](../../log-analytics/log-analytics-overview.md)-munkaterület
* [Automatizálás](../../automation/automation-offering-get-started.md)
* Engedélyezett [hibrid runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md) a virtuális gépen

Ekkor megnyílik **Az Update Management engedélyezése** képernyő. Adja meg a beállításokat, és kattintson az **Engedélyezés** gombra.

![Az Update Management megoldás engedélyezése](./media/tutorial-monitoring/manage-updates-update-enable.png)

A megoldás engedélyezése akár 15 percet is igénybe vehet. Ez idő alatt ne zárja be a böngészőablakot. A megoldás engedélyezését követően a virtuális gép hiányzó frissítéseivel kapcsolatos adatok elkezdenek beérkezni a csomagkezelőből a Log Analytics szolgáltatásba.
Az adatok legalább 30 perc és legfeljebb 6 óra múlva állnak készen az elemzésre.

### <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

Az **Update management** megoldás engedélyezését követően megjelenik a **Frissítéskezelés** képernyő. A **Hiányzó frissítések** lapon a hiányzó frissítések listája látható.

![Frissítés állapotának megtekintése](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítések telepítéséhez ütemezzen egy olyan telepítést, amely megfelel a kiadási ütemtervnek és a karbantartási időszaknak.

Ütemezzen egy új frissítéstelepítést a virtuális géphez. Ehhez kattintson a **Frissítéskezelés** képernyő felső részén található **Frissítések központi telepítésének ütemezése** elemre. Az **Új frissítéstelepítés** képernyőn adja meg a következő információkat:

* **Név** – Adjon meg egy egyedi nevet a frissítéstelepítés azonosításához.
* **Kihagyandó frissítések** – Válassza ezt a lehetőséget a frissítésből kizárni kívánt csomagok neveinek megadásához.
* **Ütemezési beállítások** – Elfogadhatja az alapértelmezett időpontot, amely a 30 perccel az aktuális idő utáni időpont, vagy megadhat egy másik időpontot. Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődjön. Ha ismétlődő ütemezést szeretne beállítani, az Ismétlődés alatt kattintson az Ismétlődő lehetőségre.

  ![A frissítés ütemezés beállításai képernyője](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Karbantartási időszak (perc)** – Adja meg azt az időtartamot, amelyen belül szeretné, hogy a frissítés telepítése megtörténjen.  Ez biztosítja, hogy a módosítások a megadott karbantartási időszakban menjenek végbe. 

Ha befejezte az ütemezés konfigurálását, kattintson a **Létrehozás** gombra. Ezután visszalép az állapot-irányítópultra.
Ekkor az **Ütemezett** táblázatban már látható az Ön által létrehozott telepítésütemezés.

> [!WARNING]
> A virtuális gép automatikusan újraindul frissítések telepítése után, ha a karbantartási időszakban még elegendő idő áll erre rendelkezésre.

Az Update management a virtuális gép meglévő csomagkezelőjét használja a csomagok telepítéséhez.

### <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, a **Frissítéskezelés** képernyő **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota.
Ha éppen fut, az állapota **Folyamatban**. Ha sikeresen befejeződik, **Sikeres** állapotúra változik.
Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, annak az állapota **Sikertelen**.
Ha rákattint a befejezett frissítéstelepítésre, megjelenik az adott frissítéstelepítés irányítópultja.

![Adott telepítés frissítéstelepítési állapot-irányítópultja](./media/tutorial-monitoring/manage-updates-view-results.png)

A **Frissítés eredményei** csempe összesíti a frissítések teljes számát és az adott virtuális gépre vonatkozó telepítési eredményeket.
A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza, amely a következők egyike lehet:

* **Nem lett megkísérelve** – a frissítés nem lett telepítve, mert a megadott karbantartási időszak alapján nem lett volna rá elég idő.
* **Sikeres** – a frissítés sikeresen le lett töltve és telepítve lett a virtuális gépen.
* **Sikertelen** – a frissítés virtuális gépre történő letöltése vagy telepítése nem sikerült.

Kattintson a **Minden napló** csempére a telepítés által létrehozott összes naplóbejegyzés megtekintéséhez.

Kattintson a **Kimenet** csempére azon runbook feladatstreamjének megtekintéséhez, amely a frissítések telepítését kezeli a cél virtuális gépen.

Kattintson a **Hibák** csempére a telepítés közben felmerülő hibák részletes információinak megtekintéséhez.

## <a name="advanced-monitoring"></a>Speciális figyelés 

Az [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) használatával fejlettebb virtuálisgép-monitorozási megoldásokat is alkalmazhat. Regisztráljon az Operations Management Suite [ingyenes próbaverziójára](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial), ha még nem tette meg.

Ha rendelkezik hozzáféréssel az OMS-portálhoz, a Beállítások panelen találja a munkaterület kulcsát és azonosítóját. Cserélje le a <workspace-key> és a <workspace-id> helyőrzőt az OMS-munkaterületről származó adatokra, majd az **az vm extension set** paranccsal adja hozzá az OMS-bővítményt a virtuális géphez:

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

Az OMS-portál naplókeresési paneljén ekkor meg kell jelennie a *myVM* elemnek, ahogyan az az alábbi képen is látható:

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
> * Speciális monitorozás beállítása

Folytassa a következő oktatóanyaggal, amely az Azure Security Center használatát ismerteti.

> [!div class="nextstepaction"]
> [A virtuális gépek biztonságának kezelése](./tutorial-azure-security.md)
