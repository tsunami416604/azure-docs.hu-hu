---
title: Windows rendszerű virtuális gép javítása az Azure-beli virtuálisgép-javítási parancsok használatával | Microsoft Docs
description: Ez a cikk részletesen ismerteti, hogyan használható az Azure-beli virtuális gépek javítási parancsai a lemez egy másik Windowsos virtuális géphez való összekapcsolásához a hibák elhárításához, majd az eredeti virtuális gép újraépítéséhez.
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
ms.openlocfilehash: 82bebcbda3110d51ae72df1fb4b18fedaa6c2f4e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597698"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Windows rendszerű virtuális gép javítása az Azure-beli virtuális gép javítási parancsaival

Ha az Azure-beli Windows rendszerű virtuális gép (VM) rendszerindítási vagy lemezhiba miatt fordul elő, előfordulhat, hogy maga a lemezen is el kell végeznie a csökkentést. Gyakori példa egy sikertelen alkalmazás frissítése, amely megakadályozza, hogy a virtuális gép sikeresen elinduljon. Ez a cikk részletesen ismerteti, hogyan használható az Azure-beli virtuális gépek javítási parancsai a lemez egy másik Windowsos virtuális géphez való összekapcsolásához a hibák elhárításához, majd az eredeti virtuális gép újraépítéséhez.

> [!IMPORTANT]
> * A cikkben szereplő parancsfájlok csak a [Azure Resource Managert](../../azure-resource-manager/management/overview.md)használó virtuális gépekre vonatkoznak.
> * A parancsfájl futtatásához kimenő kapcsolat szükséges a virtuális gépről (443-es port).
> * Egyszerre csak egy parancsfájl futhat.
> * Egy futó parancsfájlt nem lehet megszakítani.
> * A parancsfájlok futtatásának maximális ideje 90 perc, ami után időtúllépés történik.
> * A Azure Disk Encryptiont használó virtuális gépek esetében csak az egymenetes titkosítással titkosított felügyelt lemezek támogatottak (KEK-vel vagy anélkül).


## <a name="repair-process-overview"></a>Javítási folyamat áttekintése

Mostantól használhatja az Azure-beli virtuális gépek javítási parancsait egy virtuális gép operációsrendszer-lemezének módosításához, és már nem kell törölnie és újból létrehoznia a virtuális gépet.

A virtuálisgép-probléma megoldásához kövesse az alábbi lépéseket:

1. Az Azure Cloud Shell elindítása
2. Futtatás az Extension Add/Update.
3. Futtatás az VM Repair Create.
4. Futtassa az az VM Repair Run parancsot, vagy végezzen kockázatcsökkentő lépéseket.
5. Futtatás az VM Repair Restore.

További dokumentációt és útmutatást az [az VM Repair](/cli/azure/ext/vm-repair/vm/repair)című témakörben talál.

## <a name="repair-process-example"></a>Javítási folyamat – példa

1. Az Azure Cloud Shell elindítása

   Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. Az előtelepített és a fiókkal való használatra konfigurált általános Azure-eszközöket tartalmaz.

   A Cloud Shell megnyitásához válassza a **kipróbálás** lehetőséget a kódrészlet jobb felső sarkában. Emellett megnyithatja a Cloud Shellt egy külön böngészőablakban [https://shell.azure.com](https://shell.azure.com) .

   Válassza a **Másolás** elemet a kód blokkjának másolásához, majd illessze be a kódot a Cloud Shellba, majd a futtatásához válassza az **ENTER billentyűt** .

   Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.30-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ``az --version``. Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg az [Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.
   
   Ha egy másik fiókkal kell bejelentkeznie Cloud Shellba, mint amit jelenleg a Azure Portalba bejelentkezett, akkor használhatja az az ``az login`` [login Reference](/cli/azure/reference-index?view=azure-cli-latest#az-login&preserve-view=true)(az).  A fiókjához társított előfizetések közötti váltáshoz használhatja ``az account set --subscription`` [az az Account set Reference](/cli/azure/account?view=azure-cli-latest#az-account-set&preserve-view=true)lehetőséget.

2. Ha első alkalommal használja a `az vm repair` parancsokat, adja hozzá a virtuális gép javítási CLI-bővítményét.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Ha korábban már használta a `az vm repair` parancsokat, alkalmazza a frissítéseket a virtuálisgép-javító bővítményre.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. A `az vm repair create` parancs futtatása. Ezzel a paranccsal létrejön a nem működőképes virtuális géphez tartozó operációsrendszer-lemez másolata, létrehozhat egy új erőforráscsoport-javító virtuális gépet, és csatolhatja az operációsrendszer-lemez másolatát.  A javítási virtuális gép mérete és régiója megegyezik a megadott nem funkcionális virtuális géppel. Az erőforráscsoport és a virtuális gép neve az összes lépésben a nem funkcionális virtuális géphez lesz használva. Ha a virtuális gép Azure Disk Encryption használja, a parancs megkísérli a titkosított lemez zárolását, hogy az elérhető legyen a javítási virtuális géphez való csatlakozáskor.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password 'password!234' --verbose
   ```

4. A `az vm repair run` parancs futtatása. Ez a parancs a megadott javítási parancsfájlt futtatja a csatlakoztatott lemezen a javítási virtuális gépen keresztül. Ha a hibaelhárítási útmutató megadott Run-ID-t használ, használja ezt itt, ellenkező esetben az `az vm repair list-scripts` elérhető javítási parancsfájlok megjelenítéséhez használható. Az itt használt erőforráscsoport és virtuális gép neve a 3. lépésben használt nem funkcionális virtuális gép. A javítási parancsfájlokkal kapcsolatos további információkért tekintse meg a [javítási parancsfájl-függvénytárat](https://github.com/Azure/repair-script-library).

   ```azurecli-interactive
   az vm repair run -g MyResourceGroup -n MyVM --run-on-repair --run-id win-hello-world --verbose
   ```
   
   Opcionálisan a virtuális gép kijavításával, az 5. lépéssel elvégezheti a szükséges manuális kockázatcsökkentő lépéseket is.

5. A `az vm repair restore` parancs futtatása. Ez a parancs felcseréli a javított operációsrendszer-lemezt a virtuális gép eredeti operációsrendszer-lemezére. Az itt használt erőforráscsoport és virtuális gép neve a 3. lépésben használt nem funkcionális virtuális gép.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Rendszerindítási diagnosztika ellenőrzése és engedélyezése

A következő példa engedélyezi a diagnosztikai bővítményt a ``myVMDeployed`` nevű erőforráscsoport-beli virtuális gépen ``myResourceGroup`` :

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Következő lépések

* Ha problémába ütközik a virtuális géphez való csatlakozással kapcsolatban, tekintse meg [az RDP-kapcsolatok hibaelhárítása Azure-beli virtuális géppel](./troubleshoot-rdp-connection.md)című témakört.
* A virtuális GÉPEN futó alkalmazások elérésével kapcsolatos problémák: az [alkalmazások kapcsolódási problémáinak elhárítása az Azure-beli virtuális gépeken](./troubleshoot-app-connection.md).
* További információ a Resource Manager használatáról: [Azure Resource Manager Overview (áttekintés](../../azure-resource-manager/management/overview.md)).
