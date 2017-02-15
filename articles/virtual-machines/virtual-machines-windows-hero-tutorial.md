---
title: "Az első Windows-rendszerű virtuális gép létrehozása | Microsoft Docs"
description: "Megtudhatja, hogyan hozhatja létre az első Windows rendszerű virtuális gépet az Azure Portal használatával."
keywords: "Windows virtuális gép,virtuális gép létrehozása,virtuális számítógép, virtuális gép beállítása"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 785e17eb-4a13-4f06-b70f-4bd496d0ec5d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: hero-article
ms.date: 01/03/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 425637599df40ac3be23984785e4f3292d72978d
ms.openlocfilehash: 7c7a300b620b1e7bd0cd1b816e575f2d57fee80a


---
# <a name="create-your-first-windows-virtual-machine-in-the-azure-portal"></a>Az első Windows rendszerű virtuális gép létrehozása az Azure Portalon
Ez az oktatóanyag bemutatja, milyen könnyű akár néhány perc alatt létrehozni egy Windows virtuális gépet (VM) az Azure Portal használatával.  

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="choose-the-vm-image-from-the-marketplace"></a>A virtuális gép rendszerképének kiválasztása a piactérről
Példaként egy Windows Server 2016 Datacenter rendszerképet használunk, de ez csak egyike az Azure által kínált számos rendszerképnek. Az elérhető rendszerképek az előfizetéstől függenek. Egyes asztali rendszerképek például az [MSDN-előfizetők](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) számára állnak rendelkezésre.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. A bal felső sarokban kattintson az **Új** > **Számítás** > **Windows Server 2016 Datacenter** elemre.
   
    ![Képernyőkép a portálon elérhető Azure virtuálisgép-rendszerképekről](./media/virtual-machines-windows-hero-tutorial/marketplace-new.png)
3. Ellenőrizze, hogy a **Windows Server 2016 Datacenter** panelen az **Üzembe helyezési modell kiválasztása** alatt az **Erőforrás-kezelő** van-e kiválasztva. Kattintson a **Létrehozás** gombra.
   
    ![Képernyőkép a virtuális géphez kiválasztandó üzembe helyezési modellről](./media/virtual-machines-windows-hero-tutorial/deployment-model.png)

## <a name="create-the-windows-virtual-machine"></a>A Windows rendszerű virtuális gép létrehozása
A rendszerkép kiválasztása után használhatja az alapértelmezett beállításokat, és gyorsan létrehozhatja a virtuális gépet.

1. Az **Alapvető beállítások** panelen adjon meg egy **nevet** a virtuális gép számára. Ebben a példában a virtuális gép neve: *HeroVM*. A névnek 1–15 karakter hosszúnak kell lennie, és nem tartalmazhat különleges karaktereket.
2. Adjon meg egy **felhasználónevet** és egy erős **jelszót**, amelyeket a helyi fióknak a virtuális gépen való létrehozásához használ a szolgáltatás. A helyi fiókkal jelentkezhet be a virtuális gépre és kezelheti azt. Ebben a példában a felhasználónév: *azureuser*.
   
    A jelszónak 8–123 karakter hosszúnak kell lennie, és meg kell felelnie a következő négy összetettségi feltétel közül háromnak: egy kisbetű, egy nagybetű, egy szám és egy különleges karakter. További információk a [felhasználónév- és jelszókövetelményekről](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).

3. Válasszon ki egy létező [Erőforráscsoportot](../azure-resource-manager/resource-group-overview.md#resource-groups), vagy adja meg egy új csoport nevét. Ebben a példában az erőforráscsoport neve: *HeroVMRG*.

4. Válassza ki egy Azure-adatközpont **helyét**. Ebben a példában a választott hely az *USA keleti régiója**. 

4. Ha végzett, kattintson az **OK** gombra a következő szakaszra való továbblépéshez. 
   
    ![Képernyőkép az **Alapvető beállítások** panel beállításairól az Azure virtuális gép konfigurálásához](./media/virtual-machines-windows-hero-tutorial/basics-blade.png)
5. Válassza ki a virtuális gép [méretét](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), majd kattintson a **Kijelölés** elemre a folytatáshoz. Ebben a példában a virtuális gép mérete *DS1_V2 Standard*.
   
    ![Képernyőkép a Méret panelen kiválasztható Azure virtuálisgép-méretekről](./media/virtual-machines-windows-hero-tutorial/size-blade.png)
6. A **Beállítások** panelen módosíthatja a tárolási és hálózati beállításokat. A jelen oktatóanyag esetében fogadja el az alapértelmezett értékeket. Ha megfelelő méretet választott a virtuális gépnek, kipróbálhatja az Azure Premium Storage-ot – ehhez válassza a **Prémium (SSD)** elemet a **Lemez típusa** részen. A módosítások végrehajtása után kattintson az **OK** gombra.
   
    ![Képernyőkép a Beállítások panelről, amelyen konfigurálhatja egy Azure virtuális gép választható funkcióit](./media/virtual-machines-windows-hero-tutorial/settings-blade.png)
7. A választott beállítások áttekintéséhez kattintson az **Összefoglalás** elemre. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, kattintson az **OK** gombra.
   
    ![Képernyőkép az Összefoglalás panelről, amely az Azure virtuális gép számára választott beállításokat mutatja](./media/virtual-machines-windows-hero-tutorial/summary-blade.png)
8. Amíg az Azure létrehozza a virtuális gépet, a bal oldali **Virtuális gépek** gombra kattintva nyomon követheti a folyamat állapotát. A virtuális gép létrehozása után az állapot **Fut** értékre módosul.

## <a name="connect-to-the-virtual-machine-and-sign-on"></a>Csatlakozás a virtuális géphez és bejelentkezés
1. A bal oldalon kattintson a **Virtuális gépek** lehetőségre.
2. Válassza ki a virtuális gépet a listából.
3. A virtuális gép paneljén kattintson a **Csatlakozás** elemre. Ez létrehoz és letölt egy RDP-fájlt, amely mintegy parancsikonként használható a géphez való csatlakozáshoz. Az egyszerű elérés érdekében érdemes ezt a fájlt az asztalra menteni. **Nyissa meg** ezt a fájlt a virtuális géphez való csatlakozáshoz.
   
    ![A virtuális géphez való csatlakozást ismertető képernyőkép az Azure Portalról](./media/virtual-machines-windows-hero-tutorial/connect.png)
4. Egy figyelmeztetés fog megjelenni arról, hogy az RDP-fájl közzétevője ismeretlen. Ez nem jelent rendellenes működést. A Távoli asztal ablakában kattintson a **Csatlakozás** gombra a folytatáshoz.
   
    ![Képernyőkép az ismeretlen közzétevőre vonatkozó figyelmeztetésről](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)
5. A Windows rendszerbiztonsági ablakban írja be a virtuális gép létrehozásakor létrehozott helyi fiók felhasználónevét és jelszavát. A felhasználónevet a következőképpen írja be: *vmname*&#92;*felhasználónév*, majd kattintson az **OK** gombra.
   
    ![Képernyőkép a virtuálisgép-név, a felhasználónév és a jelszó megadásáról](./media/virtual-machines-windows-hero-tutorial/credentials.png)
6. Egy figyelmeztetés jelenik meg arról, hogy a tanúsítványt nem lehetett ellenőrizni. Ez nem jelent rendellenes működést. Kattintson az **Igen** gombra a virtuális gép identitásának ellenőrzéséhez és a bejelentkezés befejezéséhez.
   
   ![Képernyőkép a virtuális gép identitásának ellenőrzésére vonatkozó üzenetről](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)

A csatlakozásra tett kísérlet során felmerülő hibákkal kapcsolatban tekintse meg a [Troubleshoot Remote Desktop connections to a Windows-based Azure Virtual Machine](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows-alapú Azure virtuális gépek távoli asztali kapcsolatainak hibaelhárítása) című témakört.

Mostantól ugyanúgy használhatja a virtuális gépet, mint bármilyen más kiszolgálót.

## <a name="optional-stop-the-vm"></a>Opcionális: A virtuális gép leállítása
Amikor nem használja a virtuális gépet, érdemes leállítani azt, a költségek csökkentése érdekében. Ehhez egyszerűen kattintson a **Leállítás** gombra, majd az **Igen** gombra.

![Képernyőkép a virtuális gép leállítására szolgáló gombról](./media/virtual-machines-windows-hero-tutorial/stop-vm.png)

Ha újra használni kívánja a virtuális gépet, az **Indítás** gombra kattintva újraindíthatja.

## <a name="next-steps"></a>Következő lépések
* Kísérletezhet az új virtuális gépen az [IIS telepítésével](virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ez az oktatóanyag azt is bemutatja, hogyan nyithatja meg a 80-as portot a bejövő webes forgalomnak egy hálózati biztonsági csoport (NSG) használatával. 
* [Létrehozhat Windows rendszerű virtuális gépet a PowerShell használatával](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), vagy [Linux rendszerű virtuális gépet](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) az Azure parancssori felület használatával.
* Ha érdekli az üzembe helyezés automatizálása, tekintse meg a [Windowsos virtuális gép Resource Manager-sablonnal történő létrehozását ](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ismertető cikket.




<!--HONumber=Jan17_HO2-->


