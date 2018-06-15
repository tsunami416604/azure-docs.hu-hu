---
title: Zoned Windows virtuális gép létrehozása az Azure portállal |} Microsoft Docs
description: Windows virtuális gép létrehozása az Azure portállal egy rendelkezésre állási zónában
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 3d3561cf1ad760930821fabeef9839c25d55f2a9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30321988"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Hozzon létre egy Windows rendszerű virtuális gép egy rendelkezésre állási zónát az Azure-portálon

Ez a cikk lépéseit az Azure portál használatával egy virtuális gép létrehozása az Azure rendelkezésre állási zónában. A [rendelkezésre állási zónák](../../availability-zones/az-overview.md) egy Azure-régió fizikailag elkülönített zónáit jelentik. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére.

Egy rendelkezésre állási zóna használatához hozzon létre a virtuális gép egy [támogatott Azure-régiót](../../availability-zones/az-overview.md#regions-that-support-availability-zones).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 

3. Adja meg a virtuális gép adatait. Az itt megadott felhasználónévvel és jelszóval bejelentkezhet a virtuális gépbe. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](faq.md#what-are-the-password-requirements-when-creating-a-vm). USA keleti régiója 2. régiója, amely támogatja a rendelkezésre állási zónák például a hely kiválasztásához. Amikor végzett, kattintson az **OK** gombra.

    ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Válasszon méretet a virtuális gép számára. Válasszon ki egy ajánlott méretet, vagy szűrő szolgáltatások alapján. Győződjön meg róla, méretének érhető el a zóna szeretne használni.

    ![A Virtuálisgép-méret kiválasztása](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. A **beállítások** > **magas rendelkezésre állású**, válasszon egyet a számozott zónák a **rendelkezésre állási zóna** legördülő menüből, hagyja a további alapértelmezett beállításokat, és Kattintson a **OK**.

    ![Válassza ki egy rendelkezésre állási zóna](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Az összefoglalás lapon kattintson a **létrehozása** elindítani a virtuális gépek telepítése során.

7. A virtuális gép rögzítve lesz az Azure Portal irányítópultján. Az üzembe helyezés befejeztével a virtuális gép összefoglalás panelje automatikusan megnyílik.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Erősítse meg a zóna felügyelt lemezes és IP-címhez

Telepítésekor a virtuális gép egy rendelkezésre állási zónában, a kezelt lemez a virtuális gép létrehozása ugyanazon a rendelkezésre állási területen. Alapértelmezés szerint egy nyilvános IP-címet is létrejön, hogy a zónában.

Ellenőrizheti a zónabeállítások ezeket az erőforrásokat a portálon.  

1. Kattintson a **erőforráscsoportok** majd az erőforrás nevének csoportosítja a virtuális gép, például a *myResourceGroup*.

2. Kattintson a lemez erőforrás nevét. A **áttekintése** lap tartalmazza a hely és a rendelkezésre állási zóna az erőforrás részleteit.

    ![A felügyelt lemezes rendelkezésre állási zóna](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Kattintson a nyilvános IP-cím erőforrás nevét. A **áttekintése** lap tartalmazza a hely és a rendelkezésre állási zóna az erőforrás részleteit.

    ![Rendelkezésre állási zóna IP-cím](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, a virtuális gép létrehozása egy rendelkezésre állási zónában. Itt további információkat talál az Azure-beli virtuális gépek [régióiról és rendelkezésre állásáról](regions-and-availability.md).
