---
title: Az állomásnév beállítását a Linux virtuális gépek Azure-felhő inicializálás használja |} Microsoft Docs
description: Felhő inicializálás használata a Linux virtuális gép testreszabása az Azure CLI 2.0 létrehozása során
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: a858a12ec81db7ae1c0a7b7cfea06fa2abdcdcc6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29124024"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Felhő inicializálás használja az állomásnév beállítását egy Linux virtuális Gépet az Azure-ban
Ez a cikk bemutatja, hogyan használható [felhő inicializálás](https://cloudinit.readthedocs.io) konfigurálása egy adott állomásnév egy virtuális gép (VM) vagy a virtuális gép méretezési beállítja (VMSS) kiépítési idő az Azure-ban. Ezen felhő inicializálás parancsfájlok futtatása az első betöltés után az erőforrásokat az Azure-ban kiépített. Felhő inicializálás működése natív Azure-ban és a Linux támogatott disztribúciókkal kapcsolatos további információkért lásd: [felhő inicializálás áttekintése](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Állítsa be a felhő inicializálás állomásnév
Alapértelmezés szerint az állomásnév esetén ugyanaz, mint a virtuális gép nevét egy új virtuális gép létrehozása az Azure-ban.  Ez az alapértelmezett állomásnév módosítani, ha a virtuális gép létrehozása az Azure-felhő inicializálás parancsfájl futtatása [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create), adja meg a felhő inicializálás fájlt a `--custom-data` váltani.  

Tekintse meg a frissítési folyamat működés közben, hozzon létre egy fájlt az aktuális rendszerhéjban nevű *cloud_init_hostname.txt* , majd illessze be a következő konfigurációt. Hozzon létre a fájl ebben a példában a felhő rendszerhéj nem a helyi számítógépen. A szerkesztő kívánja használata. Írja be a `sensible-editor cloud_init_hostname.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki a használandó #1 a **nano** szerkesztő. Győződjön meg arról, hogy az egész felhő inicializálás fájl megfelelően lett lemásolva különösen az első sor.  

```yaml
#cloud-config
hostname: myhostname
```

Ez a rendszerkép telepítés megkezdése előtt hozzon létre egy erőforráscsoportot a kell a [az csoport létrehozása](/cli/azure/group#az_group_create) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Ezután hozzon létre a virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) , és adja meg a felhő inicializálás fájl `--custom-data cloud_init_hostname.txt` az alábbiak szerint:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Létrehozása után az Azure parancssori felület információval is szolgál a virtuális Gépet. Használja a `publicIpAddress` az SSH-kapcsolatot a virtuális Gépet. Adja meg a saját címet a következőképpen:

```bash
ssh <publicIpAddress>
```

A virtuális gép nevét megjelenítéséhez használja a `hostname` parancsot a következőképpen:

```bash
hostname
```

A virtuális Gépet, hogy állítsa a felhő inicializálás fájlban, ahogy az az alábbi példa kimenetben értékként jelenteniük kell az állomásnév:

```bash
myhostname
```

## <a name="next-steps"></a>További lépések
Konfigurációs módosítások további felhőalapú inicializálás példákért lásd a következő:
 
- [Egy további Linux-felhasználó hozzáadása a virtuális gépek](cloudinit-add-user.md)
- [Futtassa a Csomagkezelő első indításakor meglévő csomagok frissítése](cloudinit-update-vm.md)
- [Módosítsa a virtuális gép helyi állomásnév](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítését, a konfigurációs fájlokat és a kulcsok beszúrása](tutorial-automate-vm-deployment.md)