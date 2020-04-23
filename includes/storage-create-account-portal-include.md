---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ea8ed75bf91850abb95ebe983923989375c0fcf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76759848"
---
Kövesse az alábbi lépéseket egy általános célú v2-tárfiók létrehozásához az Azure Portalon:

1. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be a **Storage-fiókok** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Storage-fiókok**lehetőséget.
2. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.
3. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.
4. Az **Erőforráscsoport** mező alatt válassza az **Új létrehozása** elemet. Az alábbi képen látható módon nevezze el az új erőforráscsoportot.

    ![Erőforráscsoport portálon való létrehozását bemutató képernyőkép](./media/storage-create-account-portal-include/create-resource-group-for-storage.png)

5. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A név 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
6. Válassza ki a tárfiókja helyét, vagy használja az alapértelmezett helyet.
7. Ne módosítsa a következő mezők alapértelmezett értékeit:

   |Mező  |Érték  |
   |---------|---------|
   |Üzemi modell     |Resource Manager         |
   |Teljesítmény     |Standard         |
   |Fióktípus     |StorageV2 (általános célú v2)         |
   |Replikáció     |Írásvédett georedundáns tárolás (RA-GRS)         |
   |Hozzáférési szint     |Gyakori         |

8. Ha [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)használatát tervezi, válassza a **speciális** fület, majd állítsa be a **hierarchikus névteret** **engedélyezve**értékre.
9. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.
10. Kattintson a **Létrehozás** gombra.

További információ a tárfiókok típusairól és a tárfiókok egyéb beállításairól: [Az Azure Storage-fiókok áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview). További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 
