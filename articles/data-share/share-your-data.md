---
title: Oktatóanyag – a megosztott adatok az ügyfelekkel és partnerekkel az Azure Data megosztás előzetes verzió
description: Oktatóanyag – a megosztott adatok az ügyfelekkel és partnerekkel az Azure Data megosztás előzetes verzió
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: ebd60607f9a24074e0fa985973dfc35674f0d66c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788226"
---
# <a name="tutorial-share-your-data-using-azure-data-share-preview"></a>Oktatóanyag: Az Azure Data megosztás előzetes verzió használata esetén az adatok megosztása

Ebben az oktatóanyagban megismerheti, hogyan állíthatja be egy új Azure-adatok megosztása, és az adatok megosztása az ügyfelekkel és az Azure cégen kívüli lesz. 

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy adat-megosztást.
> * Adja hozzá az adatkészletek az adatok megosztása.
> * Az ütemezett szinkronizálás engedélyezése esetén az adatok megosztása. 
> * Az adatok megosztása, adja hozzá a címzetteket. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Azure Storage-fiók: Ha még nem rendelkezik egy, létrehozhat egy [Azure Storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Adatok megosztása fiók létrehozása

Hozzon létre egy Azure-adatok megosztása erőforrást az Azure-erőforráscsoportban.

1. A portál bal felső sarkában válassza az **Erőforrás létrehozása** (+) gombot.

1. Keresse meg *az adatok megosztása*.

1. Válassza ki az adatok megosztása (előzetes verzió), és válassza ki **létrehozás**.

1. Adja meg az adatok megosztása az Azure-erőforrás a következő információkat az alapvető adatokat. 

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Name (Név) | *datashareacount* | Adja meg az adatok megosztása fiók nevét. |
    | Subscription | Az Ön előfizetése | Válassza ki, hogy az adatok megosztása fiókhoz használni kívánt Azure-előfizetést.|
    | Resource group | *test-resource-group* | Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. |
    | Location | *USA keleti RÉGIÓJA 2* | Válassza ki a régiót, a megosztás fiókjához.
    | | |

1. Válassza ki **létrehozás** megosztás fiókja kiépítéséhez. Az üzembe helyezés új adatok megosztás fiók általában eltarthat körülbelül 2 percet vagy kisebb. 

1. Az üzembe helyezés befejeződése után válassza ki a **erőforrás megnyitása**.

## <a name="create-a-data-share"></a>Hozzon létre egy az adatok megosztása

1. Lépjen az adatok megosztása – áttekintés oldalra.

    ![Az adatok megosztása](./media/share-receive-data.png "az adatok megosztása") 

1. Válassza ki **az adatok megosztása**.

1. Kattintson a **Létrehozás** gombra.   

1. Adja meg a részleteket az adatok megosztás. Adjon meg egy nevet, a megosztott tartalmat leírását, valamint a használati feltételeket (nem kötelező). 

    ![EnterShareDetails](./media/enter-share-details.png "adja meg a megosztás adatai") 

1. Válassza ki **folytatása**

1. Adatkészletek a Data-megosztás hozzáadásához válassza **hozzáadása adatkészletek**. 

    ![Az adatkészletek](./media/datasets.png "adatkészletek")

1. Válassza ki a hozzáadni kívánt adatkészlet típusa. 

    ![AddDatasets](./media/add-datasets.png "adatkészletek hozzáadása")    

1. Keresse meg az objektum szeretné megosztani, és válassza az adatkészletek hozzáadása. 

    ![SelectDatasets](./media/select-datasets.png "adatkészlet kiválasztása")    

1. A címzettek lap kiválasztásával adja meg az e-mail-címeit az adatfogyasztó "+ címzettjének hozzáadása". 

    ![AddRecipients](./media/add-recipient.png "adja hozzá a címzetteket") 

1. Válassza ki **folytatása**

1. Ha szeretné, hogy az adatfogyasztó lehessen az adatok növekményes frissítéseket, a Pillanatkép ütemezés engedélyezése. 

    ![EnableSnapshots](./media/enable-snapshots.png "pillanatképek engedélyezése") 

1. Válassza ki a kezdési idővel és ismétlődéssel időköz. 

1. Válassza ki **folytatása**

1. A felülvizsgálat + lap létrehozása, a csomag tartalma, a beállítások, a címzettek és a szinkronizálási beállítások áttekintése. Kattintson a **Létrehozás** elemre.

Az Azure az adatok megosztása létrehozása megtörtént, és a címzett, az adatok megosztása, készen áll a meghívó elfogadása. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban hogyan hozhat létre egy Azure-adatok megosztása, és a címzettek meghívása tanulással. Ha arról, hogyan adatfogyasztó elfogadja és kap egy adatmegosztáshoz, lépjen tovább a [fogadja el, és fogadni az adatokat](subscribe-to-data-share.md) oktatóanyag. 