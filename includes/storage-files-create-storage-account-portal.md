---
title: tároló-fájlok-létrehozás-tároló-fiók-portál
description: Tárfiók létrehozása az Azure Files számára.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: a09e9206b3ecd23a6208572a88c16b149033f123
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
A tárfiókok az Azure-fájlmegosztások vagy más tárolási erőforrások, például blobok vagy üzenetsorok üzembe helyezéséhez használható tárolók közös készletei. A tárfiókok korlátlan számú megosztást tartalmazhatnak, a megosztásokban pedig tetszőleges számú fájlt tárolhat, egészen a tárfiókja kapacitásának korlátjáig.

Tárfiók létrehozása:

1. Válassza a bal oldali menü **+** elemét erőforrás létrehozásához.
2. Írja be a **tárfiók** kifejezést a keresőmezőbe, válassza a **Tárfiók – blob, fájl, tábla, üzenetsor** elemet, majd válassza a **Létrehozás** gombot.
    ![Az erőforrás-keresési párbeszédpanelen megjelenő tárfiókbejegyzés kinézetének képernyőképe](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. A **Név** mezőbe írja be a *mystorageacct* kifejezést, majd írjon utána néhány véletlenszerű számot, amíg meg nem jelenik a név egyediségét jelző zöld pipa. A tárfiók nevének globálisan egyedinek kell lennie, és csak kisbetűkből állhat. Jegyezze fel a tárfiók nevét. Erre később még szüksége lesz. 
4. Az **Üzembe helyezési modell** mezőben hagyja meg az alapértelmezett **Erőforrás-kezelő** értéket. További információ az Azure Resource Manager és a klasszikus üzembe helyezési modell közötti különbségekről: [Az üzembe helyezési modellek és az erőforrások állapotának ismertetése](../articles/azure-resource-manager/resource-manager-deployment-model.md).
5. A **Fiók típusa** mezőben válassza a **StorageV2** lehetőséget. További információ a különböző típusú tárfiókokkal kapcsolatban: [Az Azure-tárfiókok ismertetése](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
6. A **Teljesítmény** mezőben hagyja meg a **Standard szintű Storage** alapértelmezett értéket. Az Azure Files jelenleg csak a Standard szintű tárolást támogatja, és a fájlmegosztás akkor is Standard szintű tárolóban lesz tárolva, ha az Azure Premium Storage lehetőséget választja.
7. A **Replikáció** mezőben válassza a **Helyileg redundáns tárolás (LRS)** lehetőséget. 
8. A **Biztonságos átvitelre van szükség** mezőben ajánlott mindig az **Engedélyezve** értéket választani. További információ erről a beállításról: [Az átvitel közbeni titkosítás ismertetése](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
9. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyben létrehozza a tárfiókot. Ha csak egy előfizetéssel rendelkezik, akkor az az alapértelmezett.
10. Az **Erőforráscsoport** területen válassza az **Új létrehozása** lehetőséget. Névként adja meg a *myResourceGroup* elnevezést.
11. A **Hely** mezőben válassza az **USA keleti régiója** lehetőséget.
12. A **Virtuális hálózatok** mezőben hagyja meg a **Letiltva** alapértelmezett beállítást. 
13. A tárfiók könnyebb megtalálásához jelölje be a **Rögzítés az irányítópulton** lehetőséget.
14. Ha végzett, válassza a **Létrehozás** gombot az üzembe helyezés indításához.