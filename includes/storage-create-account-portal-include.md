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
ms.openlocfilehash: f21710354572c9b54dbf0e08d02809bb68a8e120
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49805052"
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
1. Kattintson a **Create** (Létrehozás) gombra.

További információ a tárfiókok típusairól és a tárfiókok egyéb beállításairól: [Az Azure Storage-fiókok áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview). További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 
