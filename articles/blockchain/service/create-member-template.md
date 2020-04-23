---
title: Azure Blockchain szolgáltatástag létrehozása az Azure Resource Manager-sablon használatával
description: Ismerje meg, hogyan hozhat létre egy Azure Blockchain szolgáltatástagot az Azure Resource Manager-sablon használatával.
services: azure-resource-manager
author: PatAltimore
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: patricka
ms.date: 04/22/2020
ms.openlocfilehash: 8fd5a1775a6a217ce9dd8c80439dd2c54f9cd727
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087596"
---
# <a name="create-an-azure-blockchain-service-member-using-an-azure-resource-manager-template"></a>Azure Blockchain szolgáltatástag létrehozása Azure Resource Manager-sablon használatával

Ebben a rövid útmutatóban üzembe helyez egy új blokklánc-tagot és konzorciumot az Azure Blockchain Szolgáltatásban egy Azure Resource Manager-sablon használatával.

Az Azure Blockchain Service-tag egy blockchain csomópont egy privát konzorcium blockchain hálózatban. Egy tag kiépítésekor létrehozhat vagy csatlakozhat egy konzorciumi hálózathoz. A konzorciumi hálózathoz legalább egy tagra van szükség. A résztvevők által szükséges blockchain tagok száma a forgatókönyvtől függ. A konzorcium résztvevőinek lehet egy vagy több blokklánc-tagja, vagy megoszthatják a tagokat más résztvevőkkel. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service consortium.](consortium.md)

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Nincs.

## <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-blockchain-asaservice/)

[!code-json[<Azure Resource Manager template create blockchain member>](~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json)]

A sablonban definiált Azure-erőforrások:

* [**Microsoft.Blockchain/blockchainMembers**](https://docs.microsoft.com/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza ki az alábbi hivatkozást az Azure-ba való bejelentkezéshez és egy sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](./media/create-member-template/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Adja meg az Azure Blockchain szolgáltatás tag beállításait.

    Beállítás | Leírás
    --------|------------
    Előfizetés | Válassza ki a szolgáltatáshoz használni kívánt Azure-előfizetést. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
    Erőforráscsoport | Hozzon létre egy új erőforráscsoport nevét, vagy válasszon egy meglévőt az előfizetésből.
    Hely | Válassza ki a tag létrehozásához helyet. A konzorcium minden tagjának ugyanazon a helyen kell lennie. A rendelkezésre álló helyek a telepítés *westeurope, eastus, délkelet-, westeurope, észak-európai, westus2*, és *japaneast*.
    Tag neve | Válasszon egyedi nevet az Azure Blockchain szolgáltatás tag. A blokklánc tag neve csak kisbetűket és számokat tartalmazhat. Az első karakternek betűnek kell lennie. Az értéknek 2 és 20 karakter közötti hosszúnak kell lennie.
    Konzorcium neve | Adjon meg egy egyedi nevet. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service consortium.](consortium.md)
    Tag jelszava | A tagfiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására szolgál. A tagfiók és a tagfiók jelszavát használja a konzorciumkezeléséhez.
    Sku szint | Az új szolgáltatás tarifacsomagja. Válasszon **a standard** és az **alapszintű** szintek közül. Használja az *alapszintű* szintet a fejlesztéshez, teszteléshez és a fogalmak igazolásához. Használja a *standard* szint éles szintű központi telepítések. A *Standard* szint akkor is érdemes használnia, ha blockchain data managert használ, vagy nagy mennyiségű magántranzakciót küld. A tagság létrehozása után nem támogatott a tarifacsomag alap- és szabványos szint közötti módosítása.
    Sku neve | Az új szolgáltatás csomópontkonfigurációja és költsége.

1. A sablon üzembe helyezéséhez válassza a **Vásárlás** lehetőséget.

  Az Azure Portal itt a sablon üzembe helyezéséhez. Használhatja az Azure PowerShell, az Azure CLI és a REST API-t is. További telepítési módszerekről a [Sablonok telepítése ..](../../azure-resource-manager/templates/deploy-powershell.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használhatja a blockchain tag létrehozott a következő rövid útmutató vagy oktatóanyag. Ha már nincs rá szükség, törölheti az erőforrásokat a rövid útmutatóhoz létrehozott erőforráscsoport törlésével.

Az erőforráscsoport törlése:

1. Az Azure Portalon keresse meg az **Erőforráscsoportot** a bal oldali navigációs ablakban, és válassza ki a törölni kívánt erőforráscsoportot.
2. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzése az erőforráscsoport nevének megadásával, majd a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy Azure Blockchain szolgáltatás tagot és egy új konzorciumot. Próbálja ki a következő gyorsindítást az Azure Blockchain Development Kit for Ethereum használatával az Azure Blockchain szolgáltatás egy tagjához való csatoláshoz.

> [!div class="nextstepaction"]
> [Az Azure Blockchain szolgáltatáshoz való csatlakozás hoz a Visual Studio-kód dal](connect-vscode.md)