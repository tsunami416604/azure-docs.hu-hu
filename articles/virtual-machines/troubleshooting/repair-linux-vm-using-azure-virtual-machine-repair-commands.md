---
title: Linux rendszerű virtuális gép javítása az Azure-beli virtuálisgép-javítási parancsok használatával | Microsoft Docs
description: Ez a cikk részletesen ismerteti, hogyan használható az Azure-beli virtuális gépek javítási parancsai a lemez egy másik linuxos virtuális géphez való összekapcsolásához a hibák kijavításához, majd az eredeti virtuális gép újraépítéséhez.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 56e420f9641638bfa79ff077be73132b00b934ab
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "71132090"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Linux rendszerű virtuális gép javítása az Azure-beli virtuális gép javítási parancsaival

Ha az Azure-beli linuxos virtuális gép (VM) rendszerindítási vagy lemezhiba miatt fordul elő, előfordulhat, hogy maga a lemezen is el kell végeznie a mérséklést. Gyakori példa egy sikertelen alkalmazás frissítése, amely megakadályozza, hogy a virtuális gép sikeresen elinduljon. Ez a cikk részletesen ismerteti, hogyan használható az Azure-beli virtuális gépek javítási parancsai a lemez egy másik linuxos virtuális géphez való összekapcsolásához a hibák kijavításához, majd az eredeti virtuális gép újraépítéséhez.

> [!IMPORTANT]
> A cikkben szereplő parancsfájlok csak a [Azure Resource Managert](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)használó virtuális gépekre vonatkoznak.

## <a name="repair-process-overview"></a>Javítási folyamat áttekintése

Mostantól használhatja az Azure-beli virtuális gépek javítási parancsait egy virtuális gép operációsrendszer-lemezének módosításához, és már nem kell törölnie és újból létrehoznia a virtuális GÉPET.

A virtuálisgép-probléma megoldásához kövesse az alábbi lépéseket:

1. Az Azure Cloud Shell indítása
2. Futtatás az Extension Add/Update
3. Futtatás az VM Repair Create
4. Enyhítő lépések végrehajtása
5. Futtatás az VM Repair Restore

További dokumentációt és útmutatást az [az VM Repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair)című témakörben talál.

## <a name="repair-process-example"></a>Javítási folyamat – példa

1. Az Azure Cloud Shell indítása

   Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. Az előtelepített és a fiókkal való használatra konfigurált általános Azure-eszközöket tartalmaz.

   A Cloud Shell megnyitásához válassza a **kipróbálás** lehetőséget a kódrészlet jobb felső sarkában. A Cloud Shell egy külön böngésző lapon is megnyithatja, ha az [https://shell.azure.com](https://shell.azure.com).

   Válassza a **Másolás** elemet a kód blokkjának másolásához, majd illessze be a kódot a Cloud Shellba, majd a futtatásához válassza az **ENTER billentyűt** .

   Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.30-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ``az --version``. Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg az [Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli)ismertető témakört.

2. Ha első alkalommal használja a `az vm repair` parancsokat, adja hozzá a virtuális gép javítási CLI-bővítményét.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Ha korábban már használta a `az vm repair` parancsokat, alkalmazza a virtuális gép javítási bővítményének frissítéseit.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Futtassa az `az vm repair create` parancsot. Ez a parancs létrehozza az operációsrendszer-lemez másolatát a nem működőképes virtuális géphez, létrehoz egy javítási virtuális gépet, és csatlakoztatja a lemezt.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Hajtsa végre a szükséges kockázatcsökkentő lépéseket a létrehozott javítási virtuális gépen, majd folytassa az 5. lépéssel.

5. Futtassa az `az vm repair restore` parancsot. Ez a parancs felcseréli a javított operációsrendszer-lemezt a virtuális gép eredeti operációsrendszer-lemezére.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Rendszerindítási diagnosztika ellenőrzése és engedélyezése

A következő példa engedélyezi a diagnosztikai bővítményt a ``myVMDeployed`` nevű virtuális gépen az ``myResourceGroup`` nevű erőforráscsoporthoz:

Azure parancssori felület (CLI)

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Következő lépések

* Ha problémába ütközik a virtuális géphez való csatlakozással kapcsolatban, tekintse meg [Az Azure-beli virtuális gép RDP-kapcsolatainak hibaelhárítását](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection)ismertető témakört.
* A virtuális GÉPEN futó alkalmazások elérésével kapcsolatos problémák: az [alkalmazások kapcsolódási problémáinak elhárítása az Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* További információ a Resource Manager használatáról: [Azure Resource Manager Overview (áttekintés](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)).
