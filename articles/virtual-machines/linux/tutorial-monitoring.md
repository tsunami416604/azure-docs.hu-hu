---
title: "Figyelheti és frissítheti a Linux virtuális gépek Azure-ban |} Microsoft Docs"
description: "Útmutató a rendszerindítási diagnosztika és a metrikák felügyeletéhez és kezeléséhez a csomag frissítéseinek egy Linux virtuális gép az Azure-ban"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: cde484dd59ec6e2821678766726c02362222d496
ms.sourcegitcommit: 7d4b3cf1fc9883c945a63270d3af1f86e3bfb22a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="how-to-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Hogyan figyelheti és frissítheti a Linux virtuális gép az Azure-ban

Annak érdekében, hogy megfelelően fut a virtuális gépek (VM) az Azure-ban, tekintse át a rendszerindítási diagnosztika, metrikák és a csomag frissítések kezelése. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendszerindítási diagnosztika a virtuális Gépre engedélyezése
> * Rendszerindítási diagnosztika megtekintése
> * Gazdagép-metrikák megtekintése
> * A virtuális Gépre diagnosztika bővítmény engedélyezése
> * Nézet VM metrikák
> * A diagnosztikai mérőszámok alapján figyelmeztetések létrehozása
> * Csomag frissítések kezelése
> * Speciális figyelés beállítása


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-vm"></a>Virtuális gép létrehozása

Diagnosztikai és a művelet metrikák megtekintéséhez szüksége van egy virtuális Gépet. Először hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#create). Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupMonitor* a a *eastus* helyét.

```azurecli-interactive 
az group create --name myResourceGroupMonitor --location eastus
```

Most létrehozza a virtuális gép és [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm#az_vm_create). Az alábbi példakód létrehozza a virtuális gépek nevű *myVM*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Rendszerindítási diagnosztika engedélyezése

Linux virtuális gépek a rendszerindítás, a rendszerindítási diagnosztikai bővítmény rendszerindító kimeneti rögzíti és azt az Azure storage tárolja. Ezeket az adatokat a virtuális gép rendszerindító probléma megoldásához használható. Rendszerindítási diagnosztika nem automatikusan engedélyezve vannak az Azure parancssori felület használatával Linux virtuális gép létrehozásakor.

Ahhoz, hogy a rendszerindítási diagnosztika, egy tárfiókot kell létrehozni rendszerindító naplók tárolásához. Storage-fiókok 3 és 24 karakter között lehet globálisan egyedi névvel kell rendelkeznie, és csak számokat és kisbetűket tartalmazhat. A storage-fiók létrehozása a [az storage-fiók létrehozása](/cli/azure/storage/account#create) parancsot. Ebben a példában a véletlenszerű karakterlánc egyedi tárfióknév létrehozására szolgál. 

```azurecli-interactive 
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Ha engedélyezve van a rendszerindítási diagnosztika, az URI-t a blob storage tárolóban van szükség. A következő parancsot a tárfiók ezt az URI vissza lekérdezi. Az URI azonosítóját tárolja a változók nevében *bloburi*, amellyel a következő lépésben.

```azurecli-interactive 
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Most már engedélyezheti a rendszerindítási diagnosztika a [az virtuális gép rendszerindítási-diagnosztika engedélyezése](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). A `--storage` értéke a blob URI gyűjti az előző lépésben.

```azurecli-interactive 
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```


## <a name="view-boot-diagnostics"></a>Rendszerindítási diagnosztika megtekintése

Rendszerindítási diagnosztika, ha engedélyezve vannak minden alkalommal, állítsa le és indítsa el a virtuális Gépet, a rendszerindítási folyamat információ íródik naplófájlba. Ehhez a példához, először a virtuális Géphez a felszabadítani a [az virtuális gép felszabadítása](/cli/azure/vm#deallocate) parancsot a következőképpen:

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Most indítsa el a virtuális Géphez a a [az vm indítása]( /cli/azure/vm#stop) parancsot a következőképpen:

```azurecli-interactive 
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Kaphat a rendszerindítási diagnosztikai adatok *myVM* rendelkező a [az virtuális gép rendszerindítási-diagnosztika get-rendszerindítási-naplófájl](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log) parancsot a következőképpen:

```azurecli-interactive 
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```


## <a name="view-host-metrics"></a>Gazdagép-metrikák megtekintése

A Linux virtuális gépek dedikált-állomással rendelkezik, amely hatással van az Azure-ban. Metrikák automatikusan összegyűjtött ahhoz, hogy a gazdagép és tekintheti meg az Azure-portálon az alábbiak szerint:

1. Az Azure portálon kattintson **erőforráscsoportok**, jelölje be **myResourceGroupMonitor**, majd válassza ki **myVM** erőforrás listájában.
1. Hogyan működik-e a gazdagép VM megtekintéséhez kattintson **metrikák** a virtuális gép panelen, majd válassza ki valamelyik a *[állomás]* mérőszámok alapján **elérhető**.

    ![Gazdagép-metrikák megtekintése](./media/tutorial-monitoring/monitor-host-metrics.png)


## <a name="install-diagnostics-extension"></a>Diagnosztika-kiterjesztés telepítése

> [!IMPORTANT]
> Ez a dokumentum ismerteti a Linux diagnosztikai bővítményt, amely elavult 2.3 verzióját. 2.3 verziója lesz támogatott 2018. június 30-ig.
>
> A Linux diagnosztikai bővítmény 3.0-s verziója a helyette engedélyezhető. További információkért lásd: [dokumentációjában](./diagnostic-extension.md).

Az alapvető gazdagép metrikák érhetők el, de a részletes és a Virtuálisgép-specifikus metrikák megtekintéséhez telepítenie kell az Azure diagnostics bővítményt a virtuális Gépen. Az Azure diagnostics bővítmény lehetővé teszi, hogy további figyelési és diagnosztikai adatokat beolvasni a virtuális gépről. Megtekintheti a metrikák és riasztások alapján hogyan hajtja végre a virtuális gép létrehozása. A diagnosztikai bővítmény telepítve van az Azure portálon keresztül az alábbiak szerint:

1. Az Azure portálon kattintson **erőforráscsoportok**, jelölje be **myResourceGroup**, majd válassza ki **myVM** erőforrás listájában.
1. Kattintson a **diagnosztikai beállítások**. A lista azt mutatja, hogy *rendszerindítási diagnosztika* már engedélyezve van az előző szakaszából. Jelölje be a jelölőnégyzetet a *alapvető metrikák*.
1. Az a *tárfiók* szakaszban, keresse meg és jelölje ki a *mydiagdata [1234]* az előző szakaszban létrehozott fiók.
1. Kattintson a **Mentés** gombra.

    ![Nézet diagnosztikai metrikák](./media/tutorial-monitoring/enable-diagnostics-extension.png)


## <a name="view-vm-metrics"></a>Nézet VM metrikák

A virtuális gép mérni, hogy megtekinthetők-e a gazdagép VM metrikák azonos módon tekintheti meg:

1. Az Azure portálon kattintson **erőforráscsoportok**, jelölje be **myResourceGroup**, majd válassza ki **myVM** erőforrás listájában.
1. Hogyan működik-e a virtuális gép megtekintéséhez kattintson **metrikák** a virtuális gép panelen, majd válassza ki a diagnosztika mérőszámok alapján bármelyikét **elérhető**.

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

## <a name="manage-package-updates"></a>Csomag frissítések kezelése

Frissítéskezelés használatával kezelheti csomag frissítések és javítások a Azure Linux virtuális gépekhez. Közvetlenül a virtuális gépről, gyorsan mérje fel a rendelkezésre álló frissítések állapotát, kötelező frissítések telepítésének ütemezése, és tekintse át a központi telepítési eredmények ellenőrzése a frissítések alkalmazása sikeresen megtörtént a virtuális géphez.

Díjszabási információkért tekintse meg az [Automation Update Management-díjszabását](https://azure.microsoft.com/pricing/details/automation/) ismertető cikket.

### <a name="enable-update-management-preview"></a>Engedélyezze frissítéskezelésről (előzetes verzió)

A virtuális gép frissítéskezelés engedélyezése

1. A képernyő bal oldalán kattintson **virtuális gépek**.
1. A listában jelölje ki a virtuális gépek.
1. A virtuális gép képernyőn a a **műveletek** kattintson **frissítéskezelés**. A **engedélyezze frissítéskezelésről** képernyőn megnyílik.

A rendszer ellenőrzi, hogy az Update Management engedélyezve van-e a virtuális gépen. A rendszer eközben azt is ellenőrzi, hogy létezik-e Log Analytics-munkaterület és egy csatlakoztatott Automation-fiók, valamint hogy a megoldás már jelen van-e a munkaterületen.

A Naplóelemzési munkaterület funkciókat és szolgáltatásokat, például a frissítéskezelés által generált adatok összegyűjtésére szolgál. A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez. A virtuális gépeken, amelyek a frissítés szükséges további művelet végrehajtásához Azure Automation futtatását teszi-szkriptek használatát a virtuális gépek, többek között letöltéséhez, és alkalmazza a frissítéseket.

Az ellenőrzési folyamat arra is kiterjed, hogy a virtuális gépen működik-e a Microsoft Monitoring Agent (MMA) és egy hibrid feldolgozó. Ez az ügynök kommunikál a virtuális géppel, továbbá begyűjti a frissítési állapottal kapcsolatos információkat. 

Ha az előfeltételek nem teljesülnek, a megoldás lehetővé teszi lehetővé teszi egy fejléc jelenik meg.

![Az Update Management felvételének konfigurációs szalagcíme](./media/tutorial-monitoring/manage-updates-onboard-solution-banner.png)

A megoldás engedélyezéséhez kattintson a szalagcímre. Ha az ellenőrzés után nem található a következő előfeltételek bármelyike található, akkor automatikusan megkapja:

* [Log Analytics](../../log-analytics/log-analytics-overview.md)-munkaterület
* [Automatizálás](../../automation/automation-offering-get-started.md)
* Engedélyezett [hibrid runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md) a virtuális gépen

A **engedélyezze Frissítéskezelésről** képernyőn megnyílik. Adja meg a beállításokat, és kattintson a **engedélyezése**.

![Frissítés felügyeleti megoldás engedélyezése](./media/tutorial-monitoring/manage-updates-update-enable.png)

A megoldás engedélyezése akár 15 percig is eltarthat, és ebben az időszakban, akkor nem zárja be a böngészőablakot. A megoldás engedélyezése után a virtuális Gépen a Csomagkezelő a hiányzó frissítésekkel kapcsolatos információk szolgáltatáshoz zajlik.
Az adatok legalább 30 perc és legfeljebb 6 óra múlva állnak készen az elemzésre.

### <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

Miután a **frissítéskezelés** megoldás engedélyezve van, a **frissítéskezelés** képernyő jelenik meg. A **Hiányzó frissítések** lapon a hiányzó frissítések listája látható.

![Frissítés állapotának megtekintése](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

Frissítések telepítéséhez a központi telepítés utáni a kiadási ütemezés és a karbantartási időszak ütemezése.

A virtuális Gépet egy új központi telepítésének ütemezése kattintva **ütemezés központi telepítésének** tetején a **frissítéskezelés** képernyő. Az **Új frissítéstelepítés** képernyőn adja meg a következő információkat:

* **Név** – Adjon meg egy egyedi nevet a frissítéstelepítés azonosításához.
* **Frissítések kizárandó** – válassza ezt a csomagot kell zárni a frissítés adja meg.
* **Ütemezési beállítások** – Elfogadhatja az alapértelmezett időpontot, amely a 30 perccel az aktuális idő utáni időpont, vagy megadhat egy másik időpontot. Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődjön. Ha ismétlődő ütemezést szeretne beállítani, az Ismétlődés alatt kattintson az Ismétlődő lehetőségre.

  ![A frissítés ütemezés beállításai képernyője](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Karbantartási időszak (perc)** – Adja meg azt az időtartamot, amelyen belül szeretné, hogy a frissítés telepítése megtörténjen.  Ez segít a módosítások a megadott karbantartási időszakon belül el kell végezni. 

Ha befejezte az ütemezés konfigurálását, kattintson a **Létrehozás** gombra. Ezután visszalép az állapot-irányítópultra.
Figyelje meg, hogy a **ütemezett** táblázat mutatja a központi telepítési ütemezés létrehozott.

> [!WARNING]
> A virtuális gép automatikusan újraindul frissítések települnek, ha a karbantartási időszakban elegendő idő után.

Frissítéskezelés a virtuális Gépet a meglévő Csomagkezelő csomagok használja.

### <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, a **Frissítéskezelés** képernyő **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota.
Ha éppen fut, az állapota **Folyamatban**. Ha sikeresen befejeződik, **Sikeres** állapotúra változik.
Ha hiba történik a központi telepítésben lévő egy vagy több frissítésekkel, az állapot értéke **sikertelen**.
Ha rákattint a befejezett frissítéstelepítésre, megjelenik az adott frissítéstelepítés irányítópultja.

![Adott telepítés frissítéstelepítési állapot-irányítópultja](./media/tutorial-monitoring/manage-updates-view-results.png)

A **frissítésének elmulasztása az** csempe a frissítések és a virtuális gép telepítési eredmények száma összegzését.
A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza, amely a következők egyike lehet:

* **Nem lett megkísérelve** – a frissítés nem lett telepítve, mert a megadott karbantartási időszak alapján nem lett volna rá elég idő.
* **Sikeres** – a frissítés sikeresen letöltötte és telepítette a virtuális Gépen
* **Nem sikerült** – a frissítés letöltése vagy telepítése a virtuális gépen nem sikerült.

Kattintson a **Minden napló** csempére a telepítés által létrehozott összes naplóbejegyzés megtekintéséhez.

Kattintson a **Kimenet** csempére azon runbook feladatstreamjének megtekintéséhez, amely a frissítések telepítését kezeli a cél virtuális gépen.

Kattintson a **Hibák** csempére a telepítés közben felmerülő hibák részletes információinak megtekintéséhez.

## <a name="advanced-monitoring"></a>Speciális figyelés 

Fejlettebb, figyelés, a virtuális gép segítségével teheti [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Ha még nem tette meg, akkor regisztrálhatnak az egy [ingyenes próbaverzió](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) az Operations Management Suite szolgáltatásban.

Ha rendelkezik az OMS-portállal, találja a kulcsát és a munkaterület azonosítóját a beállítások panelen. A név felülírandó < munkaterület-kulcs > és < munkaterület-azonosítója > az OMS Szolgáltatáshoz tartozó értékeket a munkaterületet, és ezután használhatja **az virtuálisgép-bővítmény készlet** az OMS-bővítmény hozzáadása a virtuális Gépet:

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

Az OMS-portálon naplófájl-keresési paneljén láthatja *myVM* például az alábbi ábrán is látható:

![OMS panel](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban konfigurálhatók, tekintse át, és a virtuális gépek frissítések kezelhetők. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Rendszerindítási diagnosztika a virtuális Gépre engedélyezése
> * Rendszerindítási diagnosztika megtekintése
> * Gazdagép-metrikák megtekintése
> * A virtuális Gépre diagnosztika bővítmény engedélyezése
> * Nézet VM metrikák
> * A diagnosztikai mérőszámok alapján figyelmeztetések létrehozása
> * Csomag frissítések kezelése
> * Speciális figyelés beállítása

A következő oktatóanyag az Azure Security Centerrel kapcsolatos további továbblépés.

> [!div class="nextstepaction"]
> [A virtuális gépek biztonságának kezelése](./tutorial-azure-security.md)
