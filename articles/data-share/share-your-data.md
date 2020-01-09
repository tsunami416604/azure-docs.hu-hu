---
title: 'Oktatóanyag: megosztás a szervezeti környezeten kívül – Azure-adatmegosztás'
description: Oktatóanyag – az Azure-adatmegosztást használó ügyfelekkel és partnerekkel való adatmegosztás
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 8749f7dee2ceeb09e37cc97d4e5bfe76c52e2da6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438738"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Oktatóanyag: adatmegosztás az Azure-adatmegosztás használatával  

Ebből az oktatóanyagból megtudhatja, hogyan állíthat be új Azure-adatmegosztást, és hogyan oszthatja meg adatait az Azure-szervezeten kívüli ügyfelekkel és partnerekkel. 

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy Data Share-t.
> * Adjon hozzá adathalmazokat a Data Share-hez.
> * Adja meg a Data Share szinkronizálási ütemezését. 
> * Adjon hozzá címzetteket a Data Share-hez. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* A címzett Azure bejelentkezési e-mail-címe (az e-mail alias használata nem fog működni).

### <a name="share-from-a-storage-account"></a>Megosztás egy Storage-fiókból:

* Azure Storage-fiók: Ha még nem rendelkezik ilyennel, létrehozhat egy [Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Jogosultság a szerepkör-hozzárendelés hozzáadásához a Storage-fiókhoz, amely megtalálható a *Microsoft. Authorization/szerepkör-hozzárendelés/írás* engedélyben. Ez az engedély létezik a tulajdonosi szerepkörben. 

### <a name="share-from-a-sql-based-source"></a>Megosztás SQL-alapú forrásból:

* Egy Azure SQL Database vagy Azure SQL Data Warehouse a megosztani kívánt táblázatokkal és nézetekkel.
* Az adatraktár eléréséhez szükséges engedély. Ezt a következő lépések végrehajtásával teheti meg: 
    1. Állítsa be saját magát a kiszolgáló Azure Active Directory-rendszergazdájaként.
    1. Kapcsolódjon a Azure SQL Database/adattárházhoz Azure Active Directory használatával.
    1. A lekérdezés-szerkesztő (előzetes verzió) használatával hajtsa végre a következő parancsfájlt az adatmegosztási MSI-fájl db_owner való hozzáadásához. Active Directory használatával kell kapcsolódnia, nem SQL Server a hitelesítéshez. 
    
```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```                   
Vegye figyelembe, hogy a *< share_acc_name >* az adatmegosztási fiók neve. Ha még nem hozott létre adatmegosztási fiókot, később is visszatérhet ehhez az előfeltételhöz.  

* Egy [Azure SQL Database felhasználó, aki `db_owner` hozzáféréssel rendelkezik](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users) a megosztható táblák és/vagy nézetek kiválasztásához. 

* Ügyfél IP-SQL Server tűzfallal való hozzáférés: ezt a következő lépések végrehajtásával teheti meg: 1. Navigáljon a *tűzfalakhoz és a virtuális hálózatokhoz* 1. Kattintson a **be** kapcsolóra az Azure-szolgáltatásokhoz való hozzáférés engedélyezéséhez. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Adatmegosztási fiók létrehozása

Azure-beli adatmegosztási erőforrás létrehozása Azure-erőforráscsoporthoz.

1. A portál bal felső sarkában válassza az **Erőforrás létrehozása** (+) gombot.

1. Keresse meg az *adatmegosztást*.

1. Válassza az adatmegosztás lehetőséget, majd válassza a **Létrehozás**lehetőséget.

1. Töltse ki az Azure-beli adatmegosztási erőforrás alapvető adatait az alábbi információkkal. 

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Név | *datashareacount* | Adja meg az adatmegosztási fiók nevét. |
    | Előfizetés | Az Ön előfizetése | Válassza ki az adatmegosztási fiókhoz használni kívánt Azure-előfizetést.|
    | Erőforráscsoport | *test-resource-group* | Használjon meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. |
    | Földrajzi egység | *USA 2. keleti régiója* | Válassza ki az adatmegosztási fiókhoz tartozó régiót.
    | | |

1. Válassza a **Létrehozás** lehetőséget az adatmegosztási fiók kiépítéséhez. Az új adatmegosztási fiók üzembe helyezése általában körülbelül 2 percet vesz igénybe. 

1. Ha a telepítés befejeződött, válassza az **Ugrás erőforráshoz**lehetőséget.

## <a name="create-a-data-share"></a>Adatmegosztás létrehozása

1. Navigáljon az adatmegosztás áttekintő oldalára.

    ![Az adatai megosztása](./media/share-receive-data.png "Az adatok megosztása") 

1. Válassza **az adatmegosztás megkezdése**lehetőséget.

1. Kattintson a **Létrehozás** gombra.   

1. Adja meg az adatmegosztás részleteit. Adja meg a megosztási tartalmak nevét, leírását és használati feltételeit (opcionális). 

    ![EnterShareDetails](./media/enter-share-details.png "Adja meg a megosztás részleteit") 

1. Válassza a **Folytatás** lehetőséget.

1. Az adatkészletek adatmegosztáshoz való hozzáadásához válassza az **adatkészletek hozzáadása**elemet. 

    ![Adatkészletek](./media/datasets.png "Adathalmazok")

1. Válassza ki a hozzáadni kívánt adatkészlet típusát. Ha Azure SQL Database vagy Azure SQL-Datawarehouse oszt meg megosztást, a rendszer kérni fog néhány SQL-hitelesítő adatot. A hitelesítést az előfeltételek részeként létrehozott felhasználó használatával végezheti el.

    ![AddDatasets](./media/add-datasets.png "Adatkészletek hozzáadása")    

1. Navigáljon a megosztani kívánt objektumhoz, és válassza az "adatkészletek hozzáadása" lehetőséget. 

    ![SelectDatasets](./media/select-datasets.png "Adatkészletek kiválasztása")    

1. A címzettek lapon adja meg az adatfogyasztó e-mail-címeit a "+ Címzett hozzáadása" lehetőség kiválasztásával. 

    ![AddRecipients](./media/add-recipient.png "Címzettek hozzáadása") 

1. Válassza a **Folytatás** lehetőséget.

1. Ha azt szeretné, hogy az adatokhoz tartozó fogyasztó az adatok növekményes frissítését is lehetővé tegye, engedélyezze a pillanatképek ütemtervét. 

    ![EnableSnapshots](./media/enable-snapshots.png "Pillanatképek engedélyezése") 

1. Válassza ki a kezdési időt és az ismétlődési időközt. 

1. Válassza a **Folytatás** lehetőséget.

1. A felülvizsgálat + létrehozás lapon tekintse át a csomag tartalmát, a beállításokat, a címzetteket és a szinkronizálási beállításokat. Kattintson a **Létrehozás** elemre.

Az Azure-beli adatmegosztás már létrejött, és az adatmegosztás címzettje most már készen áll a meghívás elfogadására. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre Azure-beli adatmegosztást, és hogyan hívhat meg címzetteket. Ha szeretne többet megtudni arról, hogy az adatfogyasztó hogyan fogadhat és fogadhat adatmegosztást, folytassa az [elfogadás és fogadás](subscribe-to-data-share.md) adatgyűjtéssel foglalkozó oktatóanyagot. 
