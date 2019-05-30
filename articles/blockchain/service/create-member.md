---
title: Hozzon létre egy Azure Blockchain-szolgáltatás az Azure portal használatával
description: Az Azure Blockchain-szolgáltatás használatával hozzon létre egy consortium tag.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 357dc47027582d5c638bb3c7344c839f37f93dc5
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399129"
---
# <a name="quickstart-create-an-azure-blockchain-service-using-the-azure-portal"></a>Gyors útmutató: Hozzon létre egy Azure Blockchain-szolgáltatás az Azure portal használatával

Az Azure Blockchain-szolgáltatás az blockchain platform, hogy az üzleti logikára belüli egy intelligens szerződés hajthat végre. Ez a rövid útmutató bemutatja, hogyan első lépésként hozza létre az Azure portal használatával felügyelt Főkönyv.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-managed-ledger"></a>Hozzon létre egy felügyelt Főkönyv

Az Azure Blockchain-szolgáltatás számítási és tárolási erőforrások egy meghatározott készletével együtt jön létre.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
1. Válassza ki **Blockchain** > **Azure Blockchain-szolgáltatás**.
1. Fejezze be a sablon.

    ![Szolgáltatás létrehozása](./media/create-member/create-member.png)

    Beállítás | Leírás
    --------|------------
    Blockchain-tag | Válasszon egy egyedi nevet, amely azonosítja az Azure Blockchain-szolgáltatás tag. A blockchain tag neve csak kisbetűket és számokat tartalmazhat. Az első karakternek betűnek kell lennie. Az értéknek 2 és 20 karakter közöttinek kell lennie.
    Előfizetés | Válassza ki a szolgáltatáshoz használni kívánt Azure-előfizetést. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
    Erőforráscsoport | Egy új erőforráscsoport neve vagy egy meglévő az előfizetéséből.
    Régió | Hely legyen ugyanaz a consortium összes tagja számára.
    Tag fiók jelszava | A tag fiók jelszavát a Ethereum-fiókot, amely a tag jön létre a titkos kulcsfájl titkosítására szolgál. A tagsági fiók, és a tag fiók jelszavának használata consortium felügyeleti.
    Consortium neve | Egy új consortium adja meg egy egyedi nevet. Egy consortium meghívót keresztül csatlakozik, ha az értéke a consortium, amelyhez csatlakozik.
    Leírás | A consortium leírása.
    Protocol |  Előzetes verziója támogatja a kvórum protokollt.
    Díjszabás | A csomópont-konfigurációt az új szolgáltatáshoz. Válassza ki **Standard**. 2 érvényesítő csomópont és 1 tranzakció csomópont az alapértelmezett beállítása.
    Tranzakció csomópont jelszó | A tag alapértelmezett tranzakció csomópont jelszava. Használja az egyszerű hitelesítést a jelszót, blockchain-tagok alapértelmezett tranzakció csomópont nyilvános végponthoz való csatlakozáskor.

1. Válassza ki **létrehozás** a szolgáltatás telepítéséhez. Üzembe helyezési idő szükséges nagyjából 10 percet.
1. Válassza ki **értesítések** az eszköztáron az üzembehelyezési folyamat megfigyeléséhez.
1. Az üzembe helyezést követően nyissa meg a blockchain tag.

Válassza ki **áttekintése**, megtekintheti az alapvető információ a szolgáltatásról, beleértve a RootContract címet és a tag fiók.

![Blockchain-tag áttekintése](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő gyorsútmutatóval, vagy az oktatóanyag a létrehozott tagot is használhatja. Ha már nincs rá szükség, törölheti az erőforrások törlése a `myResourceGroup` létrehozott erőforráscsoportot az Azure Blockchain-szolgáltatás.

Az erőforráscsoport törléséhez:

1. Az Azure Portalon lépjen **erőforráscsoport** a bal oldali navigációs panelen, és válassza ki a törölni kívánt erőforráscsoportot.
2. Válassza az **Erőforráscsoport törlése** elemet. Az erőforráscsoport nevének megadásával erősítse meg a törlést, és válassza ki **törlése**.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [MetaMask használatával csatlakozhat, és a egy intelligens szerződés üzembe helyezése](connect-metamask.md)