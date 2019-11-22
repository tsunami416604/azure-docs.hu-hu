---
title: Azure Blockchain szolgáltatásbeli tag létrehozása – Azure Portal
description: Hozzon létre egy Azure Blockchain-szolgáltatási tagot egy Blockchain Consortium számára a Azure Portal használatával.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/18/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 9b08bf030d66c058ef1ec7c57f1fb2517046c522
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285108"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Rövid útmutató: Azure Blockchain Service Blockchain-tag létrehozása a Azure Portal használatával

Ebben a rövid útmutatóban egy új blockchain-tagot és-konzorciumot helyez üzembe az Azure Blockchain Service-ben a Azure Portal használatával.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Blokklánctagok létrehozása

Hozzon létre egy blockchain-tagot, amely egy új vagy egy meglévő konzorciumban futtatja a kvórum főkönyvének protokollját.

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
1. Válassza a **Blockchain** > **Azure Blockchain szolgáltatás (előzetes verzió)** lehetőséget.

    ![Szolgáltatás létrehozása](./media/create-member/create-member.png)

    Beállítás | Leírás
    --------|------------
    Előfizetést | Válassza ki a szolgáltatásához használni kívánt Azure-előfizetést. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
    Erőforráscsoport | Hozzon létre egy új erőforráscsoport-nevet, vagy válasszon ki egy meglévőt az előfizetésből.
    Régió | Válasszon ki egy régiót a tag létrehozásához. A konzorcium összes tagjának ugyanazon a helyen kell lennie.
    Protocol | Jelenleg az Azure Blockchain Service előzetes verziója támogatja a kvórum protokollt.
    Konzorcium | Új konzorcium esetén adjon meg egy egyedi nevet. Ha a konzorciumot egy meghívás használatával csatlakoztatja, válassza ki azt a konzorciumot, amelyhez csatlakozik.
    Name (Név) | Válasszon egyedi nevet az Azure Blockchain szolgáltatás tagjának. A blockchain-tag neve csak kisbetűket és számokat tartalmazhat. Az első karakternek betűnek kell lennie. Az értéknek 2 – 20 karakter hosszúnak kell lennie.
    Tag fiókjának jelszava | A tag fiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására szolgál. A tag fiókja és a fiók jelszava a konzorciumok kezeléséhez.
    Leírás | A konzorcium leírása.
    Díjszabás | A csomópont-konfiguráció és az új szolgáltatás díja. Válassza a **módosítás** hivatkozást a **standard** **és az alapszintű** csomagok választásához.
    Csomópont jelszava | A tag alapértelmezett tranzakciós csomópontjának jelszava. Az alapszintű hitelesítéshez használja a jelszót az blockchain-tag alapértelmezett tranzakciós csomópontjának nyilvános végponthoz való csatlakozáskor.

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a beállítások érvényesítéséhez. Válassza a **Létrehozás** lehetőséget a szolgáltatás kiépítéséhez. A kiépítés körülbelül 10 percet vesz igénybe.
1. A telepítési folyamat figyeléséhez kattintson az **értesítések** elemre az eszköztáron.
1. Az üzembe helyezés után navigáljon a blockchain-taghoz.

Az **Áttekintés**lehetőség kiválasztásával megtekintheti a szolgáltatás alapvető adatait, beleértve a RootContract-címeket és a tagsági fiókot is.

![Blockchain-tagok áttekintése](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő rövid útmutatóhoz vagy oktatóanyaghoz létrehozott tagot használhatja. Ha már nincs rá szükség, törölheti az erőforrásokat a gyors üzembe helyezéshez létrehozott `myResourceGroup` erőforráscsoport törlésével.

Az erőforráscsoport törlése:

1. A Azure Portalban navigáljon az **erőforráscsoporthoz** a bal oldali navigációs ablaktáblán, és válassza ki a törölni kívánt erőforráscsoportot.
2. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzéséhez írja be az erőforráscsoport nevét, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy Azure Blockchain-szolgáltatási tagot és egy új konzorciumot. Próbálja ki a következő rövid útmutatót a Ethereum készült Azure Blockchain Development Kit használatával az Azure Blockchain Service-konzorciumhoz való csatlakoztatáshoz.

> [!div class="nextstepaction"]
> [A Visual Studio Code használata Azure Blockchain Service Consortium-hálózathoz való kapcsolódáshoz](connect-vscode.md)
