---
title: Oktatóanyag – Linux rendszerű virtuális gépek monitorozása az Azure-ban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan figyelheti a linuxos virtuális gépeken futó teljesítmény-és észlelt alkalmazás-összetevőket.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 9c6458eea2b1352e7d13ea6691eac4498182ecd3
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "71680077"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>Oktatóanyag: Linux rendszerű virtuális gép figyelése az Azure-ban

Az Azure monitoring ügynökök használatával gyűjti az Azure-beli virtuális gépekről származó rendszerindítási és teljesítményadatokat, tárolja ezeket az adatokat az Azure Storage-ban, és elérhetővé teszi a portálon, a Azure PowerShell modulon és az Azure CLI-n keresztül A speciális figyelés Azure Monitor for VMs a teljesítmény-metrikák gyűjtésével, a virtuális gépre telepített alkalmazás-összetevők felderítésével, valamint a teljesítmény-és a függőségi térképekkel együtt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendszerindítási diagnosztika engedélyezése a virtuális gépen
> * Rendszerindítási diagnosztika megtekintése
> * Virtuálisgép-gazda metrikáinak megtekintése
> * Azure Monitor for VMs engedélyezése
> * VIRTUÁLIS gépek teljesítmény-metrikáinak megtekintése
> * Riasztás létrehozása

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli) ismertető cikket.

## <a name="create-vm"></a>Virtuális gép létrehozása

A diagnosztika és a metrikák működés közbeni megtekintéséhez egy virtuális gépre van szükség. Először hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal. A következő példában létrehozunk egy *myResourceGroupMonitor* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Most hozzon létre egy virtuális gépet az [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) paranccsal. Az alábbi példa egy *myVM* nevű virtuális gépet és SSH-kulcsokat hoz létre, ha azok még nem léteznek a *~/.ssh/* mappában:

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

Ahhoz, hogy a rendszerindítási diagnosztikát engedélyezni lehessen, tárfiókot kell létrehozni a rendszerindítási naplók tárolásához. A tárfiókoknak globálisan egyedi névvel kell rendelkezniük, amelynek hossza 3–24 karakter lehet, továbbá csak számokat és kisbetűket tartalmazhat. Az [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) paranccsal hozzon létre egy tárfiókot. Ebben a példában az egyedi tárfióknév létrehozásához véletlenszerű sztringet használunk.

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

Most már engedélyezheti a rendszerindítási diagnosztikát az [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable) paranccsal. A `--storage` érték az előző lépésben lekért blob URI.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Rendszerindítási diagnosztika megtekintése

Ha a rendszerindítási diagnosztika engedélyezve van, a rendszer a virtuális gép minden egyes elindításakor és leállításakor egy naplófájlba írja a rendszerindítási folyamat adatait. Ebben a példában először szabadítsa fel a virtuális gépet az [az vm deallocate](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate) paranccsal a következőképpen:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Most indítsa el a virtuális gépet az [az vm start](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-start) paranccsal a következő módon:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

A *myVM* virtuális gép rendszerindítási diagnosztikai adatait az [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) paranccsal kérheti le a következő módon:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Gazdagép metrikáinak megtekintése

A Linux rendszerű virtuális gépek egy dedikált gazdagéppel interaktálnak az Azure-ban. A rendszer automatikusan gyűjti a gazdagép metrikáit, amelyek a következőképpen tekinthetők meg az Azure Portalon:

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget, és válassza ki a **myResourceGroupMonitor**, majd a **myVM** elemet az erőforrások listájából.
1. A virtuális gazdagép teljesítményének megtekintéséhez válassza a **Metrikák** elemet a virtuális gép ablakában, majd válassza ki valamelyik *[Gazdagép]* metrikát a **Rendelkezésre álló metrikák** területen.

    ![Gazdagép metrikáinak megtekintése](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Speciális figyelés engedélyezése

Az Azure-beli virtuális gép monitorozásának engedélyezése Azure Monitor for VMs használatával:

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroupMonitor**, majd a **myVM** elemet az erőforrások listájából.

2. A virtuális gép lap **figyelés** területén válassza az eredmények **(előzetes verzió)** lehetőséget.

3. Az **áttekintések (előzetes verzió)** lapon válassza a **kipróbálás most**lehetőséget.

    ![Virtuális gép Azure Monitor for VMsának engedélyezése](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

4. Ha egy meglévő Log Analytics munkaterülettel rendelkezik ugyanabban az előfizetésben, akkor a **Azure monitor** megállapítások bevezetése lapon válassza ki azt a legördülő listából.  

    A lista előjelöli az alapértelmezett munkaterületet és helyet, ahol a virtuális gép üzembe lett helyezve az előfizetésben. 

    >[!NOTE]
    >Ha új Log Analytics munkaterületet szeretne létrehozni a figyelési adatok virtuális gépről való tárolásához, tekintse meg a [log Analytics munkaterület létrehozása](../../azure-monitor/learn/quick-create-workspace.md)című témakört. A Log Analytics-munkaterületnek a [támogatott régiók](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics)egyikéhez kell tartoznia.

Ha engedélyezte a figyelést, előfordulhat, hogy néhány percet várnia kell, amíg meg nem tekinti a virtuális gép teljesítmény-metrikáit.

![Azure Monitor for VMs figyelés telepítésének engedélyezése](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>VIRTUÁLIS gépek teljesítmény-metrikáinak megtekintése

Azure Monitor for VMs olyan teljesítménymutatókat tartalmaz, amelyek több fő teljesítménymutatót (KPI-ket) céloznak meg, amelyek segítségével meghatározhatja, hogy a virtuális gép milyen jól van végrehajtva. A virtuális gépről való hozzáféréshez hajtsa végre az alábbi lépéseket.

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroupMonitor**, majd a **myVM** elemet az erőforrások listájából.

2. A virtuális gép lap **figyelés** területén válassza az eredmények **(előzetes verzió)** lehetőséget.

3. Válassza a **teljesítmény** fület.

Ez a lap nem csak a teljesítmény-kihasználtsági diagramokat tartalmazza, hanem egy táblázatot is, amely minden felderített logikai lemezhez, annak kapacitásához, kihasználtságához és teljes átlagához tartozik.

## <a name="create-alerts"></a>Riasztások létrehozása

Létrehozhat megadott teljesítménymetrikákon alapuló riasztásokat. A riasztások segítségével értesülhet például arról, ha az átlagos processzorhasználat meghalad egy bizonyos küszöbértéket vagy a rendelkezésre álló szabad lemezterület egy adott érték alá csökken. A riasztások megjeleníthetők az Azure Portalon vagy elküldhetők e-mailben. A létrehozott riasztásokra adott válaszként aktiválhatók Azure Automation-runbookok vagy Azure Logic Apps-alkalmazások.

A következő példában az átlagos processzorhasználat alapján hozunk létre riasztást.

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroupMonitor**, majd a **myVM** elemet az erőforrások listájából.

2. Kattintson a **Riasztási szabályok** elemre a virtuális gép paneljén, majd a **Metrikariasztás hozzáadása** lehetőségre a riasztási panel felső részén.

3. Adjon meg egy **nevet** a riasztás számára, például *myAlertRule*

4. Ha szeretne riasztást aktiválni, amikor a processzorhasználat 5 percig meghaladja az 1,0 értéket, hagyja változatlanul az összes többi alapértelmezett beállítást.

5. E-mail-értesítés küldéséhez jelölje be az *E-mail küldése a tulajdonosoknak, közreműködőknek és olvasóknak* jelölőnégyzetet. Az alapértelmezett művelet az értesítés megjelenítése a portálon.

6. Kattintson az **OK** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban konfigurálta és megtekintette a virtuális gép teljesítményét. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Erőforráscsoport és virtuális gép létrehozása
> * Rendszerindítási diagnosztika engedélyezése a virtuális gépen
> * Rendszerindítási diagnosztika megtekintése
> * Gazdagép metrikáinak megtekintése
> * Azure Monitor for VMs engedélyezése
> * Virtuálisgép-metrikák megtekintése
> * Riasztás létrehozása

Folytassa a következő oktatóanyaggal, amely az Azure Security Center használatát ismerteti.

> [!div class="nextstepaction"]
> [A virtuális gépek biztonságának kezelése](../../security/fundamentals/overview.md)
