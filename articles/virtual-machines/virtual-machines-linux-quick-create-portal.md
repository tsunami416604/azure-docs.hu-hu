---
title: "Linux virtuális gép létrehozása az Azure Portal használatával | Microsoft Docs"
description: "Létrehozhat Linux virtuális gépet az Azure Portal használatával."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 1/17/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: beff4fb41ed46b016088734054e7a7897fed1a30
ms.openlocfilehash: 7287b87b1e50e28de06a5363a1f35bd7ac34d51c


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Linux virtuális gép létrehozása az Azure-ban a portál használatával
Ez a cikk bemutatja, hogyan használhatja az [Azure Portalt](https://portal.azure.com/) Linux virtuális gépek létrehozására.

Követelmények:

* [egy Azure-fiók](https://azure.microsoft.com/pricing/free-trial/)
* [SSH nyilvános- és titkoskulcs-fájlok](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="sign-in"></a>Bejelentkezés
Jelentkezzen be az Azure Portalra az Azure-fiókjával. Kattintson a bal felső sarokban található **+ Új** elemre:

![Azure-erőforrás létrehozása](./media/virtual-machines-linux-quick-create-portal/create_new_resource.png)

## <a name="choose-vm"></a>Válassza ki a virtuális gépet
Kattintson a **Számítás** elemre a **Marketplace** területen, majd válassza az **Ubuntu Server 16.04 LTS** elemet a **Kiemelt alkalmazások** rendszerképlistájából.  Ellenőrizze a lap alján, hogy a telepítési modell `Resource Manager` legyen, majd kattintson a **Létrehozás** parancsra.

![Virtuálisgép-rendszerkép kiválasztása az Azure Marketplace-ről](./media/virtual-machines-linux-quick-create-portal/create_new_vm.png)

## <a name="enter-vm-options"></a>Adja meg a virtuális gép beállításait
Az **Alapvető beállítások** lapon adja meg az alábbiakat:

* a virtuális gép nevét,
* a virtuális gép lemeztípusát: SSD (alapértelmezés szerint) vagy HDD,
* a rendszergazdai felhasználó felhasználónevét,
* a **Hitelesítési típust** állítsa **Nyilvános SSH-kulcs** értékre,
* a nyilvános SSH-kulcsot karakterláncként (a `~/.ssh/` könyvtárból),
* az erőforráscsoport nevét, vagy válasszon egy meglévő erőforráscsoportot,

és kattintson az **OK** gombra a folytatáshoz. A panel a következő képernyőképhez hasonlóan fog kinézni:

![Az Azure-beli virtuális gép alapszintű beállításainak megadása](./media/virtual-machines-linux-quick-create-portal/enter_basic_vm_details.png)

## <a name="choose-vm-size"></a>Válassza ki a virtuális gép méretét
Válassza ki a virtuális gép méretét. Az alábbi példákban a **DS1_V2 Standard** van kiválasztva, amely prémium SSD-re telepíti az Ubuntut. A virtuális gép méreténél szereplő **S** az SSD-támogatást jelöli. Kattintson a **Kijelölés** elemre a beállítások konfigurálásához.

![Az Azure-beli virtuális gép méretének kiválasztása](./media/virtual-machines-linux-quick-create-portal/select_vm_size.png)

## <a name="storage-and-network"></a>Tárolás és hálózat
A **Beállítások** panelen beállíthatja, hogy az Azure Managed Disks felügyelje a virtuális gépet. Az alapértelmezett beállítás szerint a rendszer nem felügyelt lemezeket használ. Az Azure Managed Diskset az Azure platform kezeli, így nincs szükség előkészítésre és tárolási helyre. További információ az Azure Managed Disksről: [Azure Managed Disks – áttekintés](../storage/storage-managed-disks-overview.md). Nem felügyelt lemezek esetén létre kell hoznia vagy ki kell választania egy tárfiókot a virtuális merevlemezek számára:

![Tárfiók kiválasztása nem felügyelt lemezekhez](./media/virtual-machines-linux-quick-create-portal/configure_non_managed_disks.png)

Ha igénybe veszi az Azure Managed Diskset, akkor nem szükséges további tárolási beállításokat konfigurálnia. Lásd a következő példát:

![Az Azure Managed Disks beállítás kiválasztása a Portalon](./media/virtual-machines-linux-quick-create-portal/select_managed_disks.png)

A többi hálózati beállításnál ne módosítsa az alapértelmezett értéket.

## <a name="confirm-vm-settings-and-launch"></a>Erősítse meg a virtuális gép beállításait, majd indítsa el
Ellenőrizze az új Ubuntu virtuális gép beállításait, majd kattintson az **OK** gombra.

![Az Azure-beli virtuális gép beállításainak ellenőrzése és a virtuális gép létrehozása](./media/virtual-machines-linux-quick-create-portal/review_final_vm_settings.png)

## <a name="select-the-vm-resource"></a>A virtuális gép erőforrásának kiválasztása
Nyissa meg a portál kezdőlapját, majd a bal felső sarokban található menüben válassza ki az **Erőforráscsoportok** lehetőséget. Ha szükséges, kattintson a menü tetején található három sávra a lista kibontásához a következő módon:

![Erőforráscsoportok listájának megnyitása](./media/virtual-machines-linux-quick-create-portal/select_resource_group.png)

Jelölje ki az erőforráscsoportot, majd kattintson az új virtuális gépre:

![Az Azure-beli virtuális gép hálózatikártya-beállításainak megkeresése](./media/virtual-machines-linux-quick-create-portal/select_vm_resource.png)

## <a name="find-the-public-ip"></a>A nyilvános IP-cím megkeresése
Tekintse meg a virtuális géphez rendelt **nyilvános IP-címet**:

![Az Azure-beli virtuális gép nyilvános IP-címének beszerzése](./media/virtual-machines-linux-quick-create-portal/view_public_ip_address.png)

## <a name="ssh-to-the-vm"></a>SSH–kapcsolat létrehozása a virtuális géppel
Csatlakozzon az SSH-n keresztül a nyilvános IP-címhez a nyilvános SSH-kulcs használatával.  Mac vagy Linux munkaállomáson közvetlenül a terminálról hozhatja létre az SSH-kapcsolatot. Windows munkaállomásról a PuTTY, a MobaXTerm vagy a Cygwin használatával indíthat SSH-kapcsolatot Linuxra.  Ha még nem történt meg, ezzel a dokumentummal Windows-munkaállomása felkészíthető az SSH-kapcsolatra a Linux rendszerrel.

[Az SSH-kulcsok és a Windows együttes használata az Azure-ban](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```
ssh -i ~/.ssh/azure_id_rsa ops@40.112.255.214
```

## <a name="next-steps"></a>Következő lépések
Gyorsan létrehozott egy Linux virtuális gépet tesztelési és bemutatási célokra. Az infrastruktúrának megfelelően beállított Linux virtuális gép létrehozásához kattintson az alábbi cikkek valamelyikére.

* [Linux virtuális gép létrehozása az Azure-ban sablonok alapján](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SSH-védelemmel rendelkező Linux virtuális gép létrehozása az Azure-ban sablonok használatával](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux virtuális gép létrehozása az Azure parancssori felülettel](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Feb17_HO3-->


