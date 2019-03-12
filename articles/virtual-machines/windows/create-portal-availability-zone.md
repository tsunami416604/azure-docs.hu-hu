---
title: Zónázott Windows virtuális gép létrehozása az Azure Portallal |} A Microsoft Docs
description: Windows virtuális gép létrehozása egy rendelkezésre állási zónában az Azure portal használatával
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 23d1e53785ece50943d732db12b4cf460ba6752a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767481"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Windows virtuális gép létrehozása egy rendelkezésre állási zónában az Azure portal használatával

Ez a cikk részletesen bemutatja egy virtuális gép létrehozása az Azure rendelkezésre állási zónában az Azure portal használatával. A [rendelkezésre állási zónák](../../availability-zones/az-overview.md) egy Azure-régió fizikailag elkülönített zónáit jelentik. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére.

Rendelkezésre állási zóna használatához egy [támogatott Azure-régióban](../../availability-zones/az-overview.md#regions-that-support-availability-zones) hozza létre a virtuális gépet.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 

3. Adja meg a virtuális gép adatait. Az itt megadott felhasználónévvel és jelszóval bejelentkezhet a virtuális gépbe. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](faq.md#what-are-the-password-requirements-when-creating-a-vm). Válasszon egy helyet, például az USA keleti RÉGIÓJA 2, amely támogatja a rendelkezésre állási zónák. Amikor végzett, kattintson az **OK** gombra.

    ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Válasszon méretet a virtuális gép számára. Válasszon ki egy ajánlott méretet, vagy szűrő szolgáltatások alapján. Győződjön meg arról, a méret érhető el a zóna is használni szeretné.

    ![Válassza ki a virtuális gép méretét](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Alatt **beállítások** > **magas rendelkezésre állású**, válasszon ki egy, a számozott zónákat a **rendelkezésre állási zónában** legördülő menüből, hagyja változatlanul a többi alapértelmezett beállításokat, és Kattintson a **OK**.

    ![Válassza ki a rendelkezésre állási zónában](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Az összefoglalás lapon kattintson a **létrehozás** a virtuális gép üzembe helyezésének megkezdéséhez.

7. A virtuális gép rögzítve lesz az Azure Portal irányítópultján. Az üzembe helyezés befejeztével a virtuális gép összefoglalás panelje automatikusan megnyílik.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>IP-cím és a felügyelt lemez zónájának megerősítése

Ha a virtuális gép egy rendelkezésre állási zónában, egy felügyelt lemezt a virtuális gép ugyanabban a rendelkezésre állási zónában jön létre. Alapértelmezés szerint egy nyilvános IP-címet is létrejön a zóna.

A zóna beállításait a portálon erőforrások ellenőrizheti.  

1. Kattintson a **erőforráscsoportok** , és ezután az erőforrás nevének csoportosíthatja a virtuális gép például *myResourceGroup*.

2. Kattintson a lemez erőforrás nevét. A **áttekintése** lap tartalmazza a hely és a rendelkezésre állási zónában az erőforrás részleteit.

    ![Felügyelt lemez a rendelkezésre állási zónában](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Kattintson a nyilvános IP-cím erőforrás nevére. A **áttekintése** lap tartalmazza a hely és a rendelkezésre állási zónában az erőforrás részleteit.

    ![IP-cím rendelkezésre állási zónában](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan hozható létre virtuális gép egy rendelkezésre állási zónában. Itt további információkat talál az Azure-beli virtuális gépek [régióiról és rendelkezésre állásáról](regions-and-availability.md).
