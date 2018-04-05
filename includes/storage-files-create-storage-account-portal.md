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
ms.openlocfilehash: 82e141eb4941dbd58be95e19dae186acd18caf94
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
A tárfiókok az Azure-fájlmegosztások vagy más tárolási erőforrások, például blobok vagy üzenetsorok üzembe helyezéséhez használható tárolók közös készletei. Egy tárfiók korlátlan számú megosztást tartalmazhat, egy megosztás pedig korlátlan számú fájl tárolására használható, egészen a tárfiók kapacitásának korlátjáig.

Tárfiók létrehozása:

1. A bal oldali menüben kattintson a **+** elemre egy erőforrás létrehozásához.
2. Írja be a **tárfiók** kifejezést a keresőmezőbe (1), válassza a **Tárfiók – blob, fájl, tábla, üzenetsor** elemet (2), majd kattintson a **Létrehozás** gombra.
    ![Az erőforrás-keresési párbeszédpanelen megjelenő tárfiókbejegyzés kinézetének képernyőképe](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. A **Név** mezőbe írja be a *mystorageacct* kifejezést, majd írjon utána néhány véletlenszerű számot, amíg meg nem jelenik a név egyediségét jelző zöld pipa. A tárfiók nevének globálisan egyedinek kell lennie, és csak kisbetűkből állhat. Jegyezze fel a tárfiók nevét, mert később még használni fogja. 
4. Az **Üzemi modell** mezőben hagyja meg az **Erőforrás-kezelő** alapértelmezett értéket. További információ az Azure Resource Manager és a klasszikus üzemelő példányok közötti különbségekről: [Az üzemi modellek és az erőforrások állapotának ismertetése](../articles/azure-resource-manager/resource-manager-deployment-model.md).
5. A **Fiók típusa** mezőben válassza a **StorageV2** lehetőséget. További információ a különböző típusú tárfiókokkal kapcsolatban: [Az Azure-tárfiókok ismertetése](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
6. A **Teljesítmény** mezőben hagyja meg a **Standard szintű Storage** alapértelmezett értéket. Az Azure Files jelenleg csak a Standard szintű tárolást támogatja; a fájlmegosztás akkor is Standard szintű tárolóban lesz tárolva, ha a Prémium szintű tárolást választja.
7. A **Replikáció** mezőben válassza a *Helyileg redundáns tárolás (LRS)* lehetőséget. 
8. A **Biztonságos átvitelre van szükség** mezőben ajánlott mindig az **Engedélyezve** értéket választani. További információ erről a beállításról: [Az átvitel közbeni titkosítás ismertetése](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
9. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyben létrehozza a tárfiókot. Ha csak egy előfizetéssel rendelkezik, akkor az az alapértelmezett.
10. Az **Erőforráscsoport** mezőben válassza az **Új létrehozása*lehetőséget, és írja be a*myResourceGroup** nevet.
11. A **Hely** mezőben válassza az **USA keleti régiója** lehetőséget.
12. A **Virtuális hálózatok** mezőben hagyja meg a *Letiltva* alapértelmezett beállítást. 
13. Jelölje be a **Rögzítés az irányítópulton** lehetőséget, hogy könnyebben megtalálja a tárfiókot.
14. Amikor végzett, kattintson a **Létrehozás** gombra az üzembe helyezés elindításához.