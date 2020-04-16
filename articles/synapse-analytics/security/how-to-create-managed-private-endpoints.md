---
title: Hozzon létre egy felügyelt magánvégpontot az adatforrás-eredményekhez való csatlakozáshoz.
description: Ez a cikk bemutatja, hogyan hozhat létre felügyelt magánvégpontot az Azure Synapse-munkaterületről származó adatforrásokhoz.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 363dc4d469d912c14f5f89ef6ff433a2243587e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428900"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Felügyelt magánvégpont létrehozása az adatforráshoz (előzetes verzió)

Ez a cikk bemutatja, hogyan hozhat létre felügyelt magánvégpontot az Azure-beli adatforráshoz. További információ: [Felügyelt magánvégpontok.](./synapse-workspace-managed-private-endpoints.md)

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>1. lépés: Nyissa meg az Azure Synapse-munkaterületet az Azure Portalon

Létrehozhat egy felügyelt magánvégpontot az adatforráshoz az Azure Synapse Studio-ból. Válassza az **Áttekintés** lapot az Azure Portalon, és válassza **a Synapse Studio indítása**lehetőséget.
![Az Azure Synapse Studio elindítása](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>2. lépés: Keresse meg a Synapse Studio felügyelt virtuális hálózatok lapját

Az Azure Synapse Studio,válassza ki a **Kezelés** lapot a bal oldali navigációs. Válassza **a Felügyelt virtuális hálózatok,** majd a + **Új**lehetőséget.
![Új felügyelt magánvégpont létrehozása](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>3. lépés: Az adatforrás típusának kiválasztása

Válassza ki az adatforrás típusát. Ebben az esetben a cél adatforrás egy ADLS gen2 fiók. Válassza a **Folytatás** elemet.
![Céladatforrás-típus kiválasztása](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>4. lépés: Az adatforrásadatainak megadása

A következő ablakban adja meg az adatforrás adatait. Ebben a példában egy felügyelt privát végpontot hozunk létre egy ADLS gen2-fiókhoz. Adja meg a felügyelt magánvégpont **nevét.** Adjon meg egy **Azure-előfizetést** és egy **Storage-fiók nevét.** Kattintson a **Létrehozás** gombra.
![A céladatforrás részleteinek megadása](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>5. lépés: A felügyelt magánvégpont sikeres létrehozásának ellenőrzése

A kérelem elküldése után megjelenik az állapota. A felügyelt magánvégpont sikeres létrehozásának ellenőrzéséhez ellenőrizze a *létesítési állapotát.* Előfordulhat, hogy várnia kell 1 percet, és a **kiépítési** állapot frissítéséhez válassza a Frissítés lehetőséget. Láthatja, hogy az ADLS gen2 fiók felügyelt magánvégpontja sikeresen létrejött.

Azt is láthatja, hogy a *jóváhagyási állapot* *függőben*van. A célerőforrás tulajdonosa jóváhagyhatja vagy elutasíthatja a privát végponti kapcsolatkérelmet. Ha a tulajdonos jóváhagyja a privát végponti kapcsolatkérést, akkor létrejön egy privát kapcsolat. Ha nincs megkapcsolva, akkor nincs privát kapcsolat létrehozva.
![Felügyelt személyes végpont-létrehozási kérelem állapota](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>További lépések

További információ a [felügyelt magánvégpontokról](./synapse-workspace-managed-private-endpoints.md)