---
title: "Linux virtuális gép létrehozása az Azure Portal használatával | Microsoft Docs"
description: "Létrehozhat Linux virtuális gépet az Azure Portal használatával."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/28/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 3ad64861bc4c3b0a938c75990fc516ef634943ef


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Linux virtuális gép létrehozása az Azure-ban a portál használatával
Ez a cikk bemutatja, hogyan használhatja az [Azure Portalt](https://portal.azure.com/) Linux virtuális gépek létrehozására.

Követelmények:

* [egy Azure-fiók](https://azure.microsoft.com/pricing/free-trial/)
* [SSH nyilvános- és titkoskulcs-fájlok](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="sign-in"></a>Bejelentkezés
Miután bejelentkezett az Azure Portalra Azure-fiókjának hitelesítő adataival, kattintson a bal felső sarokban található **+ Új** elemre:

![képernyő1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

## <a name="choose-vm"></a>Válassza ki a virtuális gépet
Kattintson a **Virtuális gépek** elemre a **Piactér** területen, majd válassza az **Ubuntu Server 14.04 LTS** elemet a **Kiemelt alkalmazások** rendszerképlistájában.  Ellenőrizze a lap alján, hogy a telepítési modell `Resource Manager` legyen, majd kattintson a **Létrehozás** parancsra.

![képernyő2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

## <a name="enter-vm-options"></a>Adja meg a virtuális gép beállításait
Az **Alapvető beállítások** lapon adja meg az alábbiakat:

* a virtuális gép nevét,
* a rendszergazdai felhasználó felhasználónevét,
* a Hitelesítési típus beállítását **Nyilvános SSH-kulcs** értékűként,
* a nyilvános SSH-kulcsot karakterláncként (a `~/.ssh/` könyvtárból),
* az erőforráscsoport nevét, vagy válasszon egy meglévő csoportot,

majd kattintson az **OK** gombra a folytatáshoz, és válassza ki a virtuális gép méretét, mely az alábbi képernyőképhez hasonló módon jelenik meg:

![képernyő3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

## <a name="choose-vm-size"></a>Válassza ki a virtuális gép méretét
Válassza a **DS1** méretet, amely prémium SSD-re telepíti az Ubuntu rendszert, majd kattintson a **Kijelölés** elemre a beállítások konfigurálásához.

![képernyő4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

## <a name="storage-and-network"></a>Tárolás és hálózat
A **Beállítások** részen hagyja meg a Tároló és a Hálózat alapértelmezett értékeit, majd kattintson az **OK** gombra az összegzés megtekintéséhez.  Láthatja, hogy a lemez típusa Premium SSD lett a DS1 kiválasztásával, az **S** az SSD-t jelzi.

![képernyő5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

## <a name="confirm-vm-settings-and-launch"></a>Erősítse meg a virtuális gép beállításait, majd indítsa el
Ellenőrizze az új Ubuntu virtuális gép beállításait, majd kattintson az **OK** gombra.

![képernyő6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

## <a name="find-the-vm-nic"></a>A virtuális gép hálózati adapterének megkeresése
Nyissa meg a portál irányítópultját, és a **Hálózati adapterek** részen válassza ki a hálózati kártyát.

![képernyő7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

## <a name="find-the-public-ip"></a>A nyilvános IP-cím megkeresése
Nyissa meg a Nyilvános IP-címek menüt a hálózati kártya beállításai között.

![képernyő8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

## <a name="ssh-to-the-vm"></a>SSH–kapcsolat létrehozása a virtuális géppel
Csatlakozzon az SSH-n keresztül a nyilvános IP-címhez a nyilvános SSH-kulcs használatával.  Mac vagy Linux munkaállomáson közvetlenül a terminálról hozhatja létre az SSH-kapcsolatot. Windows munkaállomásról a PuTTY, a MobaXTerm vagy a Cygwin használatával indíthat SSH-kapcsolatot Linuxra.  Ha még nem történt meg, ezzel a dokumentummal Windows-munkaállomása felkészíthető az SSH-kapcsolatra a Linux rendszerrel.

[Az SSH-kulcsok és a Windows együttes használata az Azure-ban](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>Következő lépések
Gyorsan létrehozott egy Linux virtuális gépet tesztelési és bemutatási célokra. Az infrastruktúrának megfelelően beállított Linux virtuális gép létrehozásához kattintson az alábbi cikkek valamelyikére.

* [Linux virtuális gép létrehozása az Azure-ban sablonok alapján](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SSH-védelemmel rendelkező Linux virtuális gép létrehozása az Azure-ban sablonok használatával](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux virtuális gép létrehozása az Azure parancssori felülettel](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Nov16_HO2-->


