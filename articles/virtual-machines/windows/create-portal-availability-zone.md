---
title: Zónás Windows virtuális gép létrehozása az Azure Portalon
description: Windows virtuális gép létrehozása rendelkezésre állási zónában az Azure Portalsegítségével
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: c6f843693ccfa16f9b31027ba370242e0462b138
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033896"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Windows virtuális gép létrehozása rendelkezésre állási zónában az Azure Portalsegítségével

Ez a cikk az Azure Portal használatával egy virtuális gép létrehozása egy Azure rendelkezésre állási zónában. A [rendelkezésre állási zónák](../../availability-zones/az-overview.md) egy Azure-régió fizikailag elkülönített zónáit jelentik. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére.

Rendelkezésre állási zóna használatához egy [támogatott Azure-régióban](../../availability-zones/az-overview.md#services-support-by-region) hozza létre a virtuális gépet.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 

3. Adja meg a virtuális gép adatait. Az itt megadott felhasználónévvel és jelszóval bejelentkezhet a virtuális gépbe. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](faq.md#what-are-the-password-requirements-when-creating-a-vm). Válasszon olyan helyet, például USA keleti része 2, amely támogatja a rendelkezésre állási zónákat. Amikor végzett, kattintson az **OK** gombra.

    ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Válasszon méretet a virtuális gép számára. Válassza ki az ajánlott méretet, vagy szűrjön a funkciók alapján. Ellenőrizze, hogy a méret elérhető-e a használni kívánt zónában.

    ![Virtuális gép méretének kiválasztása](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. A **Beállítások** > **magas elérhetősége**csoportban válassza ki a számozott zónák egyikét a **Rendelkezésre állási zóna** legördülő menüből, tartsa meg a fennmaradó alapértelmezett értékeket, majd kattintson az **OK**gombra.

    ![Elérhetőségi zóna kiválasztása](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Az összesítő lapon kattintson a **Létrehozás** gombra a virtuális gép telepítésének elindításához.

7. A virtuális gép rögzítve lesz az Azure Portal irányítópultján. Az üzembe helyezés befejeztével a virtuális gép összefoglalás panelje automatikusan megnyílik.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>A felügyelt lemez és IP-cím zónájának megerősítése

Amikor a virtuális gép egy rendelkezésre állási zónában van telepítve, a virtuális gép felügyelt lemeze ugyanabban a rendelkezésre állási zónában jön létre. Alapértelmezés szerint ebben a zónában nyilvános IP-cím is létrejön.

Ezeknek az erőforrásoknak a zónabeállításait a portálon ellenőrizheti.  

1. Kattintson **az Erőforráscsoportok** elemre, majd a virtuális gép erőforráscsoportjának nevét, például a *myResourceGroup csoportra.*

2. Kattintson a Lemez erőforrás nevére. Az **Áttekintés** lap az erőforrás helyével és rendelkezésre állási zónájával kapcsolatos részleteket tartalmazza.

    ![A felügyelt lemez rendelkezésre állási zónája](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Kattintson a Nyilvános IP-cím erőforrás nevére. Az **Áttekintés** lap az erőforrás helyével és rendelkezésre állási zónájával kapcsolatos részleteket tartalmazza.

    ![Az IP-cím elérhetőségi zónája](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan hozható létre virtuális gép egy rendelkezésre állási zónában. További információ az Azure-beli virtuális gépek [elérhetőségéről.](availability.md)
