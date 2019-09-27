---
title: 'Oktatóanyag: Megosztás a szervezeten kívül – az Azure-beli adatmegosztás előzetes verziója'
description: Oktatóanyag – az Azure-beli adatmegosztás előzetes verzióját használó ügyfelekkel és partnerekkel való adatmegosztás
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: f7df46a6a6f149ef0228fda8c967469a25dc3d50
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327419"
---
# <a name="tutorial-share-your-data-using-azure-data-share-preview"></a>Oktatóanyag: Az Azure-beli adatmegosztás előzetes verziójával megoszthatja adatait

Ebből az oktatóanyagból megtudhatja, hogyan állíthat be új Azure-adatmegosztást, és hogyan oszthatja meg adatait az Azure-szervezeten kívüli ügyfelekkel és partnerekkel. 

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy adatmegosztást.
> * Adatkészletek hozzáadása az adatmegosztáshoz.
> * Szinkronizálási ütemterv engedélyezése az adatmegosztáshoz. 
> * Adja hozzá a címzetteket az adatmegosztáshoz. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Egy Azure Storage-fiók: Ha még nem rendelkezik ilyennel, létrehozhat egy [Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Jogosultság a szerepkör-hozzárendelés hozzáadásához a Storage-fiókhoz, amely megtalálható a *Microsoft. Authorization/szerepkör-hozzárendelés/írás* engedélyben. Ez az engedély létezik a tulajdonosi szerepkörben. 
* A címzettek Azure bejelentkezési e-mail-címe (az e-mail alias használata nem fog működni).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Adatmegosztási fiók létrehozása

Azure-beli adatmegosztási erőforrás létrehozása Azure-erőforráscsoporthoz.

1. A portál bal felső sarkában válassza az **Erőforrás létrehozása** (+) gombot.

1. Keresse meg az *adatmegosztást*.

1. Válassza az adatmegosztás (előzetes verzió) lehetőséget, és válassza a **Létrehozás**lehetőséget.

1. Töltse ki az Azure-beli adatmegosztási erőforrás alapvető adatait az alábbi információkkal. 

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Name (Név) | *datashareacount* | Adja meg az adatmegosztási fiók nevét. |
    | Subscription | Az Ön előfizetése | Válassza ki az adatmegosztási fiókhoz használni kívánt Azure-előfizetést.|
    | Resource group | *test-resource-group* | Használjon meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. |
    | Location | *USA 2. keleti régiója* | Válassza ki az adatmegosztási fiókhoz tartozó régiót.
    | | |

1. Válassza a **Létrehozás** lehetőséget az adatmegosztási fiók kiépítéséhez. Az új adatmegosztási fiók üzembe helyezése általában körülbelül 2 percet vesz igénybe. 

1. Ha a telepítés befejeződött, válassza az **Ugrás erőforráshoz**lehetőséget.

## <a name="create-a-data-share"></a>Adatmegosztás létrehozása

1. Navigáljon az adatmegosztás áttekintő oldalára.

    ![Ossza]meg adatait az(./media/share-receive-data.png "adatai megosztásával") 

1. Válassza **az adatmegosztás megkezdése**lehetőséget.

1. Kattintson a **Létrehozás** gombra.   

1. Adja meg az adatmegosztás részleteit. Adja meg a megosztási tartalmak nevét, leírását és használati feltételeit (opcionális). 

    ![EnterShareDetails](./media/enter-share-details.png "adja meg a megosztás részleteit") 

1. Válassza a **Folytatás** lehetőséget.

1. Az adatkészletek adatmegosztáshoz való hozzáadásához válassza az **adatkészletek hozzáadása**elemet. 

    ![Adatkészletek](./media/datasets.png "adatkészletei")

1. Válassza ki a hozzáadni kívánt adatkészlet típusát. 

    ![AddDatasets](./media/add-datasets.png "adatkészletek hozzáadása")    

1. Navigáljon a megosztani kívánt objektumhoz, és válassza az "adatkészletek hozzáadása" lehetőséget. 

    ![SelectDatasets]-(./media/select-datasets.png "adatkészletek kiválasztása")    

1. A címzettek lapon adja meg az adatfogyasztó e-mail-címeit a "+ Címzett hozzáadása" lehetőség kiválasztásával. 

    ![AddRecipients]-(./media/add-recipient.png "Címzettek hozzáadása") 

1. Válassza a **Folytatás** lehetőséget.

1. Ha azt szeretné, hogy az adatokhoz tartozó fogyasztó az adatok növekményes frissítését is lehetővé tegye, engedélyezze a pillanatképek ütemtervét. 

    ![EnableSnapshots](./media/enable-snapshots.png "engedélyezése Pillanatképek") 

1. Válassza ki a kezdési időt és az ismétlődési időközt. 

1. Válassza a **Folytatás** lehetőséget.

1. A felülvizsgálat + létrehozás lapon tekintse át a csomag tartalmát, a beállításokat, a címzetteket és a szinkronizálási beállításokat. Kattintson a **Létrehozás** elemre.

Az Azure-beli adatmegosztás már létrejött, és az adatmegosztás címzettje most már készen áll a meghívás elfogadására. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre Azure-beli adatmegosztást, és hogyan hívhat meg címzetteket. Ha szeretne többet megtudni arról, hogy az adatfogyasztó hogyan fogadhat és fogadhat adatmegosztást, folytassa az [elfogadás és fogadás](subscribe-to-data-share.md) adatgyűjtéssel foglalkozó oktatóanyagot. 
