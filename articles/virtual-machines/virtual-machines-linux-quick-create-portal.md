---
title: Linux virtuális gép létrehozása az Azure Portal használatával | Microsoft Docs
description: Létrehozhat Linux virtuális gépet az Azure Portal használatával.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 08/18/2016
ms.author: v-livech

---
# Linux virtuális gép létrehozása az Azure-ban a portál használatával
Ez a cikk bemutatja, hogyan használhatja az [Azure Portalt](https://portal.azure.com/) a Linux virtuális gépek gyors létrehozásához. Ehhez mindössze [egy Azure-fiókra](https://azure.microsoft.com/pricing/free-trial/) és [nyilvános és titkos SSH-kulcsfájlokra](virtual-machines-linux-mac-create-ssh-keys.md) van szükség.

1. Miután bejelentkezett az Azure Portalra Azure-fiókjának hitelesítő adataival, kattintson a bal felső sarokban található **+ Új** elemre:
   
    ![képernyő1](../media/virtual-machines-linux-quick-create-portal/screen1.png)
2. Kattintson a **Virtuális gépek** elemre a **Piactér** területen, majd válassza az **Ubuntu Server 14.04 LTS** elemet a **Kiemelt alkalmazások** rendszerképlistájában.  Ellenőrizze a lap alján, hogy a telepítési modell `Resource Manager` legyen, majd kattintson a **Létrehozás** parancsra.
   
    ![képernyő2](../media/virtual-machines-linux-quick-create-portal/screen2.png)
3. Az **Alapvető beállítások** lapon adja meg az alábbiakat:
   
   * a virtuális gép nevét,
   * a rendszergazdai felhasználó felhasználónevét,
   * a Hitelesítési típus beállítását **Nyilvános SSH-kulcs** értékűként,
   * a nyilvános SSH-kulcsot karakterláncként (a `~/.ssh/` könyvtárból),
   * az erőforráscsoport nevét, vagy válasszon egy meglévő csoportot,
     
     majd kattintson az **OK** gombra a folytatáshoz, és válassza ki a virtuális gép méretét, az alábbiakhoz hasonlóan:
     
     ![képernyő3](../media/virtual-machines-linux-quick-create-portal/screen3.png)
4. Válassza a **DS1** méretet, amely prémium SSD-re telepíti az Ubuntu rendszert, majd kattintson a **Kijelölés** elemre a beállítások konfigurálásához.
   
    ![képernyő4](../media/virtual-machines-linux-quick-create-portal/screen4.png)
5. A **Beállítások** részen hagyja meg a Tároló és a Hálózat alapértelmezett értékeit, majd kattintson az **OK** gombra az összegzés megtekintéséhez.  Láthatja, hogy a lemez típusa Premium SSD lett a DS1 kiválasztásával, az **S** az SSD-t jelzi.
   
    ![képernyő5](../media/virtual-machines-linux-quick-create-portal/screen5.png)
6. Ellenőrizze az új Ubuntu virtuális gép beállításait, majd kattintson az **OK** gombra.
   
    ![képernyő6](../media/virtual-machines-linux-quick-create-portal/screen6.png)
7. Nyissa meg a portál irányítópultját, és a **Hálózati adapterek** részen válassza ki a hálózati kártyát.
   
    ![képernyő7](../media/virtual-machines-linux-quick-create-portal/screen7.png)
8. Nyissa meg a Nyilvános IP-címek menüt a hálózati kártya beállításai között.
   
    ![képernyő8](../media/virtual-machines-linux-quick-create-portal/screen8.png)
9. Csatlakozzon az SSH-n keresztül a nyilvános IP-címhez a nyilvános SSH-kulcs használatával.

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## Következő lépések
Gyorsan létrehozott egy Linux virtuális gépet tesztelési és bemutatási célokra. Az infrastruktúrának megfelelően beállított Linux virtuális gép létrehozásához kattintson az alábbi cikkek valamelyikére.

* [Linux virtuális gép létrehozása az Azure-ban sablonok alapján](virtual-machines-linux-cli-deploy-templates.md)
* [SSH-védelemmel rendelkező Linux virtuális gép létrehozása az Azure-ban sablonok használatával](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
* [Linux virtuális gép létrehozása az Azure parancssori felülettel](virtual-machines-linux-create-cli-complete.md)

<!--HONumber=Sep16_HO4-->


