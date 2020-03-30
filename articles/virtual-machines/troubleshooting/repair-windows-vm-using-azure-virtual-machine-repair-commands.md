---
title: Windows virtuális gép javítása az Azure virtuálisgép-javítási parancsokkal | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan azure vm javítási parancsokat, hogy csatlakoztassa a lemezt egy másik Windows virtuális gép a hibák kijavítása, majd az eredeti virtuális gép újraépítése.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 2055558ef80a641084a7cf9d299281497d282936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060678"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Windows rendszerű virtuális gép javítása az Azure-beli virtuális gép javítási parancsaival

Ha a Windows virtuális gép (VM) az Azure-ban észlel egy rendszerindító vagy lemezhiba, előfordulhat, hogy végre kell hajtania a megoldás a lemezen is. Egy gyakori példa lenne egy sikertelen alkalmazásfrissítés, amely megakadályozza, hogy a virtuális gép sikeresen eltudja indítani. Ez a cikk ismerteti, hogyan azure vm javítási parancsokat, hogy csatlakoztassa a lemezt egy másik Windows virtuális gép a hibák kijavítása, majd az eredeti virtuális gép újraépítése.

> [!IMPORTANT]
> A cikkben szereplő parancsfájlok csak az [Azure Resource Managert](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)használó virtuális gépekre vonatkoznak.

## <a name="repair-process-overview"></a>Javítási folyamat – áttekintés

Most már használhatja az Azure VM javítási parancsokat a virtuális gép operációs rendszer lemezének módosításához, és már nem kell törölni, és újra a virtuális gép.

A virtuális gépekkel kapcsolatos probléma elhárításához kövesse az alábbi lépéseket:

1. Az Azure Cloud Shell indítása
2. Futtassa az bővítmény hozzáadása/frissítése.
3. Futtassa az vm javítás létrehozása.
4. Futtassa az vm javítási futtatást.
5. Futtassa az vm javítás-visszaállítást.

További dokumentációt és utasításokat lásd [az az vm javítás](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Példa a javítási folyamatra

> [!NOTE]
> * Kimenő kapcsolat a virtuális gépről (443-as port) szükséges a parancsfájl futtatásához.
> * Egyszerre csak egy parancsfájl futhat.
> * Futó parancsfájl nem szakítható meg.
> * A parancsfájl oka legfeljebb 90 perc, ami után időtúlodik.

1. Az Azure Cloud Shell indítása

   Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. Ez magában foglalja a közös Azure-eszközök előre telepített és a fiókjával való használatra konfigurálva.

   A Cloud Shell megnyitásához válassza a **Próba** a kódblokk jobb felső sarkából. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com](https://shell.azure.com)is megnyithatja a felkeresi.

   Válassza a **Másolás** lehetőséget a kódblokkok másolásához, majd illessze be a kódot a Felhőrendszerhéjba, és válassza az **Enter** lehetőséget a kód futtatásához.

   Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.30-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ``az --version``. Ha telepítenie vagy frissítenie kell az Azure CLI-t, olvassa el [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

2. Ha ez az első alkalom, `az vm repair` hogy használta a parancsokat, adja hozzá a vm-javítás CLI kiterjesztés.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Ha korábban már `az vm repair` használta a parancsokat, alkalmazza a frissítéseket a vm-javításbővítmény.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Futtassa az `az vm repair create` parancsot. Ez a parancs létrehozza a nem működő virtuális gép operációsrendszer-lemezének másolatát, létrehoz egy javítási virtuális gép, és csatolja a lemezt.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Futtassa az `az vm repair run` parancsot. Ez a parancs a csatlakoztatott lemezen a javítási virtuális gépen keresztül futtatja a megadott javítási parancsfájlt.

   ```azurecli-interactive
   az vm repair run  –g MyResourceGroup –n MyVM -–run-on-repair --run-id 2 --verbose
   ```

5. Futtassa az `az vm repair restore` parancsot. Ez a parancs felcseréli a javított operációsrendszer-lemezt a virtuális gép eredeti operációsrendszer-lemezével.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Rendszerindítási diagnosztika ellenőrzése és engedélyezése

A következő példa engedélyezi a diagnosztikai ``myVMDeployed`` bővítményt a ``myResourceGroup``névvel ellátott erőforráscsoportban megnevezett virtuális gépen:

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>További lépések

* Ha problémái vannak a virtuális géphez való csatlakozással, olvassa [el az RDP-kapcsolatok hibaelhárítása azure-beli virtuális géphez című témakört.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection)
* A virtuális gépen futó alkalmazások elérésével kapcsolatos problémákról az [Azure-beli virtuális gépeken az alkalmazáscsatlakozási problémák elhárítása című témakörben nyújt](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection)fel problémát.
* Az Erőforrás-kezelő használatáról az Azure Resource Manager – áttekintés című témakörben olvashat [bővebben.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
