---
title: Hozzon létre egy Azure Blockchain szolgáltatástagot - Azure portal
description: Hozzon létre egy Azure Blockchain service-tagot egy blokklánc-konzorcium számára az Azure Portal használatával.
ms.date: 03/30/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 7d29382cdedf7e35d186c73c9d32547e10fb10d6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460273"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Rövid útmutató: Hozzon létre egy Azure Blockchain Service blockchain-tagot az Azure Portalhasználatával

Ebben a rövid útmutatóban üzembe helyez egy új blokklánc-tagot és konzorciumot az Azure Blockchain Szolgáltatásban az Azure Portal használatával.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Blokklánc-tag létrehozása

Az Azure Blockchain Service-tag egy blockchain csomópont egy privát konzorcium blockchain hálózatban. Egy tag kiépítésekor létrehozhat vagy csatlakozhat egy konzorciumi hálózathoz. A konzorciumi hálózathoz legalább egy tagra van szükség. A résztvevők által szükséges blockchain tagok száma a forgatókönyvtől függ. A konzorcium résztvevőinek lehet egy vagy több blokklánc-tagja, vagy megoszthatják a tagokat más résztvevőkkel. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service consortium.](consortium.md)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
1. Válassza a **Blockchain** > **Azure Blockchain Service (előzetes verzió) lehetőséget.**

    ![Szolgáltatás létrehozása](./media/create-member/create-member.png)

    Beállítás | Leírás
    --------|------------
    Előfizetés | Válassza ki a szolgáltatáshoz használni kívánt Azure-előfizetést. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
    Erőforráscsoport | Hozzon létre egy új erőforráscsoport nevét, vagy válasszon egy meglévőt az előfizetésből.
    Régió | Válasszon ki egy régiót a tag létrehozásához. A konzorcium minden tagjának ugyanazon a helyen kell lennie.
    Protocol (Protokoll) | Jelenleg az Azure Blockchain Service Preview támogatja a Quorum protokollt.
    Konzorcium | Új konzorcium esetén adjon meg egy egyedi nevet. Ha egy konzorciumhoz egy meghíváson keresztül csatlakozik, válassza ki azt a konzorciumot, amelyhez csatlakozik. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service consortium.](consortium.md)
    Name (Név) | Válasszon egyedi nevet az Azure Blockchain szolgáltatás tag. A blokklánc tag neve csak kisbetűket és számokat tartalmazhat. Az első karakternek betűnek kell lennie. Az értéknek 2 és 20 karakter közötti hosszúnak kell lennie.
    Tagfiók jelszava | A tagfiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására szolgál. A tagfiók és a tagfiók jelszavát használja a konzorciumkezeléséhez.
    Díjszabás | Az új szolgáltatás csomópontkonfigurációja és költsége. Válassza ki a **Módosítás** hivatkozást a **Standard** és **az Alapszintű** szintek közül való választáshoz. Használja az *alapszintű* szintet a fejlesztéshez, teszteléshez és a fogalmak igazolásához. Használja a *standard* szint éles szintű központi telepítések. A *Standard* szint akkor is érdemes használnia, ha blockchain data managert használ, vagy nagy mennyiségű magántranzakciót küld. A tagság létrehozása után nem támogatott a tarifacsomag alap- és szabványos szint közötti módosítása.
    Csomópont-jelszó | A tag alapértelmezett tranzakciós csomódának jelszava. Használja a jelszót az alapfokú hitelesítéshez, amikor a blockchain tag alapértelmezett tranzakciós csomópontnyilvános végpontjához csatlakozik.

1. A beállítások érvényesítéséhez válassza a **Véleményezés + létrehozás** lehetőséget. Válassza a **Létrehozás** lehetőséget a szolgáltatás kiépítéséhez. A kiépítés körülbelül 10 percet vesz igénybe.
1. Válassza **az Értesítések** lehetőséget az eszköztáron a telepítési folyamat figyeléséhez.
1. A telepítés után keresse meg a blockchain tagot.

Válassza **az Áttekintés**lehetőséget, megtekintheti a szolgáltatással kapcsolatos alapvető információkat, beleértve a RootContract címet és a tagfiókot.

![Blokklánc-tag – áttekintés](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használhatja a létrehozott tag a következő rövid útmutató vagy oktatóanyag. Ha már nincs rá szükség, törölheti az `myResourceGroup` erőforrásokat a rövid útmutatóhoz létrehozott erőforráscsoport törlésével.

Az erőforráscsoport törlése:

1. Az Azure Portalon keresse meg az **Erőforráscsoportot** a bal oldali navigációs ablakban, és válassza ki a törölni kívánt erőforráscsoportot.
2. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzése az erőforráscsoport nevének megadásával, majd a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy Azure Blockchain szolgáltatás tagot és egy új konzorciumot. Próbálja ki a következő gyorsindítást az Azure Blockchain Development Kit for Ethereum használatával az Azure Blockchain szolgáltatás egy tagjához való csatoláshoz.

> [!div class="nextstepaction"]
> [Az Azure Blockchain szolgáltatáshoz való csatlakozás hoz a Visual Studio-kód dal](connect-vscode.md)
