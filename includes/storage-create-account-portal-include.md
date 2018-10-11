---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d59cb64e1499f703bf6913ab422d0f979caca324
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47008732"
---
Kövesse az alábbi lépéseket egy általános célú v2-tárfiók létrehozásához az Azure Portalon:

1. Az Azure Portalon nyissa ki bal oldalon a szolgáltatásmenüt, és válassza a **Minden szolgáltatás** lehetőséget. Ezután görgessen le a **Storage** szakaszig, és válassza a **Storage-fiókok** lehetőséget. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.
1. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.
1. Az **Erőforráscsoport** mező alatt kattintson az **Új létrehozása** elemre. Az alábbi képen látható módon nevezze el az új erőforráscsoportot.

    ![Erőforráscsoport portálon való létrehozását bemutató képernyőkép](./media/storage-create-account-portal-include/create-resource-group.png)

1. Ezután adja meg a tárfiók nevét. A választott névnek egyedinek kell lennie az Azure-ban, 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
1. Válassza ki a tárfiókja helyét, vagy használja az alapértelmezett helyet.
1. Ne módosítsa a következő mezők alapértelmezett értékeit:
    - Az **Üzemi modell** mező alapértelmezett értéke **Resource Manager**.
    - A **Teljesítmény** mező alapértelmezett értéke **Standard**.
    - A **Fiók típusa** mező alapértelmezett értéke **StorageV2 (általános célú v2)**.
    - A **Replikáció** mező alapértelmezett értéke **Helyileg redundáns tárolás (LRS)**.
    - A **Hozzáférési szint** alapértelmezett beállítása **Gyakori elérésű**.

1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához kattintson az **Áttekintés + létrehozás** elemre.

További információ a tárfiókok típusairól és a tárfiókok egyéb beállításairól: [Az Azure Storage-fiókok áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview). További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 
