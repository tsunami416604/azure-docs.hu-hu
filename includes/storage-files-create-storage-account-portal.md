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
ms.openlocfilehash: d4054760c77a7a70b7ed84a9f95b88a3bcf2bda3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "76021156"
---
A tárfiókok az Azure-fájlmegosztások vagy más tárolási erőforrások, például blobok vagy üzenetsorok üzembe helyezéséhez használható tárolók közös készletei. A tárfiókok korlátlan számú megosztást tartalmazhatnak, a megosztásokban pedig tetszőleges számú fájlt tárolhat, egészen a tárfiókja kapacitásának korlátjáig.

Tárfiók létrehozása:

1. A bal oldali menüben válassza ki **+** az erőforrás létrehozásához.
2. Írja be a **tárfiók** kifejezést a keresőmezőbe, válassza a **Tárfiók – blob, fájl, tábla, üzenetsor** elemet, majd válassza a **Létrehozás** gombot.
    ![Az erőforrás-keresési párbeszédpanelen megjelenő tárfiókbejegyzés kinézetének képernyőképe](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. A **Név** mezőbe írja be a *mystorageacct* kifejezést, majd írjon utána néhány véletlenszerű számot, amíg meg nem jelenik a név egyediségét jelző zöld pipa. A tárfiók nevének globálisan egyedinek kell lennie, és csak kisbetűkből állhat. Jegyezze fel a tárfiók nevét. Erre később még szüksége lesz. 
4. A **telepítési modellben**hagyja meg a **Resource Manager**alapértelmezett értékét. További információ az Azure Resource Manager és a klasszikus üzembe helyezési modell közötti különbségekről: [Az üzembe helyezési modellek és az erőforrások állapotának ismertetése](../articles/azure-resource-manager/management/deployment-models.md).
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