---
title: Megosztás a szervezeten kívül (Azure Portal) – Azure-beli adatmegosztási útmutató
description: Ebből a rövid útmutatóból megtudhatja, hogyan oszthat meg az ügyfelekkel és partnerekkel az Azure-adatmegosztás használatával.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: quickstart
ms.date: 08/19/2020
ms.openlocfilehash: 41598c04af78d4366435259357d8f897ac178942
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489935"
---
# <a name="quickstart-share-data-using-azure-data-share-in-the-azure-portal"></a>Gyors útmutató: adatmegosztás az Azure-adatmegosztás használatával a Azure Portal

Ebből a rövid útmutatóból megtudhatja, hogyan állíthat be új Azure-adatmegosztást a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .


## <a name="create-a-data-share-account"></a>Adatmegosztási fiók létrehozása

Azure-beli adatmegosztási erőforrás létrehozása Azure-erőforráscsoporthoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza az **erőforrás létrehozása** gombot (+) a portál bal felső sarkában.

1. Keresse meg az *adatmegosztást*.

1. Válassza **az adatmegosztás** lehetőséget, majd válassza a **Létrehozás**lehetőséget.

1. Töltse ki az Azure-beli adatmegosztási erőforrás alapvető adatait az alábbi információkkal. 

   **Beállítás** | **Ajánlott érték** | **Mező leírása**
   |---|---|---|
   | Előfizetés | Az Ön előfizetése | Válassza ki az adatmegosztási fiókhoz használni kívánt Azure-előfizetést.|
   | Erőforráscsoport | *teszt – erőforrás-csoport* | Használjon meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. |
   | Hely | *USA 2. keleti régiója* | Válassza ki az adatmegosztási fiókhoz tartozó régiót.
   | Name | *datashareaccount* | Adja meg az adatmegosztási fiók nevét. |

1. Válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás** lehetőséget az adatmegosztási fiók kiépítéséhez. Az új adatmegosztási fiók üzembe helyezése általában körülbelül 2 percet vesz igénybe.

1. Ha a telepítés befejeződött, válassza az **Ugrás erőforráshoz**lehetőséget.

## <a name="create-a-share"></a>Megosztás létrehozása

1. Navigáljon az adatmegosztás áttekintő oldalára.

   ![Az adatok megosztása](./media/share-receive-data.png "Az adatok megosztása") 

1. Válassza **az adatmegosztás megkezdése**lehetőséget.

1. Kattintson a **Létrehozás** gombra.

1. Adja meg a megosztás részleteit. Adja meg a nevet, a megosztás típusát, a megosztási tartalmak leírását és a használati feltételeket (opcionális). 

   ![EnterShareDetails](./media/enter-share-details.png "Adja meg a megosztás részleteit") 

1. Válassza a **Folytatás**lehetőséget.

1. Az adatkészletek megosztáshoz való hozzáadásához válassza az **adatkészletek hozzáadása**elemet. 

   ![Adatkészletek hozzáadása a megosztáshoz](./media/datasets.png "Adathalmazok")

1. Válassza ki a hozzáadni kívánt adatkészlet típusát. Az adathalmazok eltérő listáját fogja látni az előző lépésben kiválasztott megosztási típustól (pillanatkép vagy helyben) függően. Ha Azure SQL Database vagy Azure szinapszis Analytics használatával oszt meg megosztást, a rendszer néhány SQL-hitelesítő adatot kér. A hitelesítést az előfeltételek részeként létrehozott felhasználó használatával végezheti el.

   ![AddDatasets](./media/add-datasets.png "Adatkészletek hozzáadása")    

1. Navigáljon a megosztani kívánt objektumhoz, és válassza az "adatkészletek hozzáadása" lehetőséget. 

   ![SelectDatasets](./media/select-datasets.png "Adatkészletek kiválasztása")    

1. A címzettek lapon adja meg az adatfogyasztó e-mail-címeit a "+ Címzett hozzáadása" lehetőség kiválasztásával.

   ![AddRecipients](./media/add-recipient.png "Címzettek hozzáadása") 

1. Válassza a **Folytatás**lehetőséget.

1. Ha kiválasztotta a pillanatkép-megosztás típusát, beállíthatja a pillanatkép-ütemtervet, hogy az adatokra vonatkozó frissítéseket biztosítson az adatfogyasztónak. 

   ![EnableSnapshots](./media/enable-snapshots.png "Pillanatképek engedélyezése") 

1. Válassza ki a kezdési időt és az ismétlődési időközt. 

1. Válassza a **Folytatás**lehetőséget.

1. A felülvizsgálat + létrehozás lapon tekintse át a csomag tartalmát, a beállításokat, a címzetteket és a szinkronizálási beállításokat. Kattintson a **Létrehozás** gombra.

Az Azure-beli adatmegosztás már létrejött, és az adatmegosztás címzettje most már készen áll a meghívás elfogadására.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az erőforrásra már nincs szükség, lépjen az **adatmegosztás áttekintése** lapra, és válassza a **Törlés** lehetőséget az eltávolításához.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure-adatmegosztást. Ha szeretne többet megtudni arról, hogy az adatfogyasztó hogyan fogadhat és fogadhat adatmegosztást, folytassa az [elfogadás és fogadás](subscribe-to-data-share.md) adatgyűjtéssel foglalkozó oktatóanyagot. 
