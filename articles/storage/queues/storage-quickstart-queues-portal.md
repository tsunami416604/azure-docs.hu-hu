---
title: 'Rövid útmutató: Azure Storage-várólisták létrehozása a portálon'
description: Az Azure Portal segítségével hozzon létre egy várólistát. Ezután az Azure Portalon egy üzenet hozzáadása, az üzenet tulajdonságainak megtekintése és az üzenet várólistájának törlése.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/06/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.reviewer: cbrooks
ms.openlocfilehash: 2876d2a680aa41372557a9f04c1d6a5eed56b8ae
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74269248"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Rövid útmutató: Hozzon létre egy várólistát, és adjon hozzá egy üzenetet az Azure Portalon

Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre egy várólistát [az](https://portal.azure.com/) Azure Storage-ban, és hogyan hozhat létre várólistát, és hogyan adhat hozzá és vonhat le üzeneteket.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Ha várólistát szeretne létrehozni az Azure Portalon, kövesse az alábbi lépéseket:

1. Az Azure Portalon lépjen az új tárfiókjára.
2. A tárfiók bal oldali menüjében görgessen a **Várólista szolgáltatás** szakaszhoz, majd válassza a **Várólisták**lehetőséget.
3. Válassza a **+ Várólista** gombot.
4. Írja be az új várólista nevét. A várólista nevének kisbetűsnek kell lennie, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és a kötőjel (-) karaktert tartalmazhat.
6. A várólista létrehozásához válassza az **OK gombot.**

    ![Képernyőkép, amely bemutatja, hogyan hozhat létre várólistát az Azure Portalon](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Üzenet hozzáadása

Ezután adjon hozzá egy üzenetet az új várólistához. Az üzenetek mérete legfeljebb 64 KB lehet.

1. Válassza ki az új várólistát a tárfiók várólistáilistájából.
1. Az **üzenet** várólistához való hozzáadásához kattintson az üzenet hozzáadása gombra. Írjon be egy üzenetet az **Üzenet szövegmezőbe.** 
1. Adja meg, hogy mikor jár le az üzenet. Az üzenet várólistán maradásának maximális ideje 7 nap.
1. Adja meg, hogy az üzenetet Base64-ként kódolja-e. Bináris adatok kódolása ajánlott.
1. Az üzenet hozzáadásához **kattintson** az OK gombra.

    ![Képernyőkép, amely bemutatja, hogyan lehet üzenetet hozzáadni egy várólistához](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Üzenet tulajdonságainak megtekintése

Miután hozzáadott egy üzenetet, az Azure Portal megjeleníti a várólistában lévő összes üzenet listáját. Megtekintheti az üzenetazonosítót, az üzenet tartalmát, az üzenet beszúrási idejét és az üzenet lejárati idejét. Azt is láthatja, hogy az üzenet várólistán kívül lett.

![Az üzenetek tulajdonságairól készült képernyőkép](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Üzenet eltávolítása az üzenetsorból

Az Azure Portalról a várólista elejéről is leválaszthat egy üzenetet. Üzenet várólistájának törlésekor az üzenet törlődik. 

A várólistán való törlés mindig eltávolítja a legrégebbi üzenetet a várólistából. 

![Képernyőkép, amely bemutatja, hogyan lehet üzenetet dequeue-t küldeni a portálról](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre várólistát, adhat hozzá egy üzenetet, tekintheti meg az üzenetek tulajdonságait, és hogyan vonhatja vissza az üzenetek várólistáját az Azure Portalon.

> [!div class="nextstepaction"]
> [Mi az az Azure Queues?](storage-queues-introduction.md)
