---
title: Azure rövid útmutató – üzenetsor létrehozása az Azure portal segítségével Azure Storage-ban |} A Microsoft Docs
description: Ebben a rövid útmutatóban az Azure Portalon használhatja egy üzenetsor létrehozásához. Ezután használhatja az Azure Portalon adjon meg egy üzenetet, az üzenet tulajdonságainak megtekintése és az üzenet eltávolítása a sorból.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/06/2019
ms.author: tamram
ms.openlocfilehash: 8a5dba5f949d5c20e0fbf752caac4125b8aecd52
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726243"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Gyors útmutató: Hozzon létre egy üzenetsort, és adjon meg egy üzenetet az Azure portal használatával

Ebben a rövid útmutató az a [az Azure portal](https://portal.azure.com/) üzenetsor létrehozása az Azure Storage, és a hozzáadása és eltávolítása a sorból üzeneteket.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Várólista létrehozása az Azure Portalon, kövesse az alábbi lépéseket:

1. Az Azure Portalon lépjen az új tárfiókjára.
2. A bal oldali menüben, a tárfiók, görgessen a **Queue szolgáltatás** területen, majd válassza ki **üzenetsorok**.
3. Válassza ki a **+ üzenetsor** gombra.
4. Adjon meg egy nevet az új várólista. Az üzenetsor neve csak kisbetűket, betűvel vagy számmal kell kezdődnie, és tartalmazhat csak betűket, számokat és kötőjelet (-) tartalmazhat.
6. Válassza ki **OK** a várólista létrehozásához.

    ![Üzenetsor létrehozása az Azure Portalon történő bemutató képernyőkép](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Üzenet hozzáadása

Ezután vegyen fel egy üzenetet az új üzenetsort. Az üzenet legfeljebb 64 KB méretű is lehet.

1. Válassza ki az új üzenetsornak a tárfiókban lévő üzenetsorok listájából.
1. Válassza ki a **+ Hozzáadás üzenet** gombra kattintva adjon meg egy üzenetet az üzenetsorba. Adjon meg egy üzenetet a **üzenet** mező. 
1. Adja meg, ha az üzenet lejár. A maximális időt, hogy egy üzenetet is marad, hogy a várólista befejezéséig 7 nap.
1. Adja meg, hogy kódolni az üzenetet, Base64-e. A bináris adatok kódolás ajánlott.
1. Válassza ki a **OK** gombra kattintva adhat hozzá az üzenetet.

    ![Adjon meg egy üzenetet egy üzenetsorba történő bemutató képernyőkép](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Üzenet tulajdonságainak megtekintése

Miután hozzáadott egy üzenetet, az Azure Portalon listáját összes üzenetet a várólistában lévő jeleníti meg. Az üzenet azonosítója, az üzenet, ideje, és az üzenet lejárati idejét az üzenet tartalmát is megtekintheti. Hány alkalommal lett, hogy ez az üzenet dequeued is látható.

![Képernyőkép – bemutató üzenet tulajdonságai](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Egy üzenet eltávolítása a sorból

Az Azure Portalról a várólista elejére üzenetét is eltávolítása a sorból. Amikor a eltávolítása a sorból egy üzenetet, az üzenet törlődik. 

Dequeueing mindig eltávolítja a legrégebbi üzenetet az üzenetsorba. 

![Képernyőfelvétel: hogyan kell a portálról egy üzenet eltávolítása a sorból](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>További lépések

Ebből a gyors útmutatóból megtudhatta, hogyan hozzon létre egy üzenetsort, adjon meg egy üzenetet, üzenet tulajdonságainak megtekintése és az Azure Portalon egy üzenet eltávolítása a sorból.

> [!div class="nextstepaction"]
> [Mik az Azure-üzenetsorok?](storage-queues-introduction.md)
