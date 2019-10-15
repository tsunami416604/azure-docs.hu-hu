---
title: Azure Blockchain-szolgáltatásbeli tag létrehozása a Azure Portal használatával
description: Hozzon létre egy Blockchain tagot az Azure Blockchain szolgáltatással.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: c87f9a044d6655b2062f2c2657d3b465699e0dd2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329260"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Rövid útmutató: Azure Blockchain Service Blockchain-tag létrehozása a Azure Portal használatával

Az Azure Blockchain Service egy Blockchain platform, amellyel az üzleti logikát intelligens szerződésen belül is végrehajthatja. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy blockchain-tagot a Azure Portal használatával.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Blokklánctagok létrehozása

Hozzon létre egy blockchain-tagot, amely egy új vagy egy meglévő konzorciumban futtatja a kvórum főkönyvének protokollját.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
1. Válassza a **Blockchain** > **Azure Blockchain szolgáltatást**.
1. Fejezze be a sablont.

    ![Szolgáltatás létrehozása](./media/create-member/create-member.png)

    Beállítás | Leírás
    --------|------------
    Blockchain tag | Válasszon egy egyedi nevet az Azure Blockchain Service Blockchain-tag azonosításához. A blockchain-tag neve csak kisbetűket és számokat tartalmazhat. Az első karakternek betűnek kell lennie. Az értéknek 2 – 20 karakter hosszúnak kell lennie.
    Előfizetés | Válassza ki a szolgáltatásához használni kívánt Azure-előfizetést. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
    Erőforráscsoport | Egy új erőforráscsoport neve vagy egy meglévő az előfizetéséből.
    Region (Régió) | A helynek azonosnak kell lennie a konzorcium összes tagja számára.
    Tag fiókjának jelszava | A tag fiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására szolgál. A tag fiókja és a fiók jelszava a konzorciumok kezeléséhez.
    Konzorcium neve | Új konzorcium esetén adjon meg egy egyedi nevet. Ha a konzorciumot egy meghívás használatával csatlakoztatja, akkor az az érték, amelyhez a konzorcium csatlakozik.
    Leírás | A konzorcium leírása.
    Protocol (Protokoll) |  Az előzetes verzió támogatja a kvórum protokollt.
    Díjszabás | Az új szolgáltatás csomópont-konfigurációja. Válassza a **standard**lehetőséget. 2 az alapértelmezett beállítások az ellenőrző csomópontok és 1 tranzakciós csomópontok.
    Tranzakciós csomópont jelszava | A tag alapértelmezett tranzakciós csomópontjának jelszava. Az alapszintű hitelesítéshez használja a jelszót az blockchain-tag alapértelmezett tranzakciós csomópontjának nyilvános végponthoz való csatlakozáskor.

1. Válassza a **Létrehozás** lehetőséget a szolgáltatás kiépítéséhez. A kiépítés körülbelül 10 percet vesz igénybe.
1. A telepítési folyamat figyeléséhez kattintson az **értesítések** elemre az eszköztáron.
1. Az üzembe helyezés után navigáljon a blockchain-taghoz.

Az **Áttekintés**lehetőség kiválasztásával megtekintheti a szolgáltatás alapvető adatait, beleértve a RootContract-címeket és a tagsági fiókot is.

![Blockchain-tagok áttekintése](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő rövid útmutatóhoz vagy oktatóanyaghoz létrehozott tagot használhatja. Ha már nincs rá szükség, törölheti az erőforrásokat az Azure Blockchain szolgáltatás által létrehozott `myResourceGroup` erőforráscsoport törlésével.

Az erőforráscsoport törlése:

1. A Azure Portalban navigáljon az **erőforráscsoporthoz** a bal oldali navigációs ablaktáblán, és válassza ki a törölni kívánt erőforráscsoportot.
2. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzéséhez írja be az erőforráscsoport nevét, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Intelligens szerződés összekapcsolásának és üzembe helyezésének MetaMask használata](connect-metamask.md)