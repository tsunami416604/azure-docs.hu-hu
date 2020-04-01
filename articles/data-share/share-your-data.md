---
title: 'Oktatóanyag: Megosztás a szervezeten kívül – Azure-adatmegosztás'
description: Oktatóanyag – Adatok megosztása az Azure Data Share használatával az ügyfelekkel és partnerekkel
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: a8265680f74b2d5679d1ebfbb2873dd096f498a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77083045"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Oktatóanyag: Adatok megosztása az Azure Data Share használatával  

Ebben az oktatóanyagban megtudhatja, hogyan állíthat be egy új Azure-adatmegosztást, és hogyan oszthatja meg adatait az Azure-szervezeten kívüli ügyfelekkel és partnerekkel. 

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy Data Share-t.
> * Adjon hozzá adathalmazokat a Data Share-hez.
> * Az adatmegosztás pillanatkép-ütemezésének engedélyezése. 
> * Adjon hozzá címzetteket a Data Share-hez. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* A címzett Azure-beli bejelentkezési e-mail címe (az e-mail aliasuk használatával nem fog működni).
* Ha a forrás Azure-adattár egy másik Azure-előfizetésben van, mint amelyet az adatmegosztási erőforrás létrehozásához fog használni, regisztrálja a [Microsoft.DataShare erőforrás-szolgáltatót](concepts-roles-permissions.md#resource-provider-registration) abban az előfizetésben, amelyben az Azure-adattára található. 

### <a name="share-from-a-storage-account"></a>Megosztás tárfiókból:

* Egy Azure Storage-fiók: Ha még nem rendelkezik ilyen, létrehozhat egy [Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Írási engedély a *Microsoft.Storage/storageAccounts/write*könyvtárfiókba történő íráshoz. Ez az engedély a közreműködői szerepkörben található.
* A *Microsoft.Authorization/role assignments/write*. Ez az engedély a Tulajdonos szerepkörben található. 


### <a name="share-from-a-sql-based-source"></a>Megosztás SQL-alapú forrásból:

* Egy Azure SQL Database vagy Az Azure Synapse Analytics (korábbi években Azure SQL Data Warehouse) a megosztani kívánt táblákkal és nézetekkel.
* Engedélyt kap a *Microsoft.Sql/servers/databases/write*. Ez az engedély a közreműködői szerepkörben található.
* Az adattárház eléréséhez szükséges adatmegosztás ii. Ez a következő lépéseken keresztül hajtható végre: 
    1. Állítsa be magát az SQL-kiszolgáló Azure Active Directory-rendszergazdájaként.
    1. Csatlakozzon az Azure SQL Database/Data Warehouse az Azure Active Directory használatával.
    1. A Lekérdezésszerkesztő vel (előzetes verzió) hajthatja végre a következő parancsfájlt az adatmegosztási erőforrás felügyelt identitásának db_datareader. Az Active Directory használatával kell csatlakoznia, nem pedig SQL Server-hitelesítéssel. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Ne feledje, hogy a *<share_acc_name>* az adatmegosztási erőforrás neve. Ha még nem hozott létre adatmegosztási erőforrást, később visszatérhet erre az előfeltételre.  

* Egy Azure SQL Database-felhasználó "db_datareader" hozzáféréssel navigálni, és válassza ki a táblákat és/vagy nézeteket meg szeretné osztani. 

* Ügyfél IP SQL Server tűzfal-hozzáférés. Ez a következő lépéseken keresztül hajtható végre: 
    1. Az Azure Portal SQL-kiszolgálóján keresse meg a *tűzfalakat és a virtuális hálózatokat*
    1. Kattintson a **bekapcsolt** kapcsolóra az Azure Services elérésének engedélyezéséhez.
    1. Kattintson **az ügyfél IP-címének hozzáadása** gombra, majd a Mentés **gombra.** Az ügyfél IP-címe változhat. Előfordulhat, hogy ezt a folyamatot meg kell ismételni, amikor legközelebb SQL-adatokat oszt meg az Azure Portalról. IP-tartományt is hozzáadhat. 

### <a name="share-from-azure-data-explorer"></a>Megosztás az Azure Data Explorerből
* Egy Azure Data Explorer-fürt, amelynek adatbázisait meg szeretné osztani.
* Írási engedély a *Microsoft.Kusto/clusters/write*programban található Azure Data Explorer-fürtbe. Ez az engedély a közreműködői szerepkörben található.
* A *Microsoft.Authorization/role assignments/write*programban található Azure Data Explorer-fürthöz szerepkör-hozzárendelés hozzáadására vonatkozó engedély. Ez az engedély a Tulajdonos szerepkörben található.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="create-a-data-share-account"></a>Adatmegosztási fiók létrehozása

Hozzon létre egy Azure-adatmegosztási erőforrást egy Azure-erőforráscsoportban.

1. A portál bal felső sarkában válassza az **Erőforrás létrehozása** (+) gombot.

1. Adatmegosztás *Data Share*keresése .

1. Válassza az Adatmegosztás és a **Létrehozás**lehetőséget.

1. Töltse ki az Azure-adatmegosztási erőforrás alapvető adatait az alábbi információkkal. 

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Név | *datashareacount (adatmegosztási szám* | Adja meg az adatmegosztási fiók nevét. |
    | Előfizetés | Az Ön előfizetése | Válassza ki az adatmegosztási fiókjához használni kívánt Azure-előfizetést.|
    | Erőforráscsoport | *teszt-erőforrás-csoport* | Használjon meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. |
    | Hely | *USA 2. keleti régiója* | Válasszon ki egy régiót az adatmegosztási fiókjához.
    | | |

1. Válassza a **Létrehozás** lehetőséget az adatmegosztási fiók kiépítéséhez. Egy új adatmegosztási fiók kiépítése általában körülbelül 2 percet vagy annál kevesebbet vesz igénybe. 

1. Amikor a központi telepítés befejeződött, válassza **az Ugrás az erőforrásra**lehetőséget.

## <a name="create-a-data-share"></a>Adatmegosztás létrehozása

1. Nyissa meg az adatmegosztás áttekintése lapot.

    ![Az adatok megosztása](./media/share-receive-data.png "Az adatok megosztása") 

1. Válassza **az Adatok megosztásának megkezdése**lehetőséget.

1. Kattintson a **Létrehozás** gombra.   

1. Töltse ki az adatmegosztás részleteit. Adja meg a nevet, a megosztás típusát, a megosztástartalmának leírását és a használati feltételeket (nem kötelező). 

    ![EnterShareDetails](./media/enter-share-details.png "Megosztás részleteinek megadása") 

1. Válassza a **Continue (Folytatás) lehetőséget.**

1. Ha adatkészleteket szeretne hozzáadni az adatmegosztáshoz, válassza **az Adatkészletek hozzáadása**lehetőséget. 

    ![Adathalmazok](./media/datasets.png "Adathalmazok")

1. Válassza ki a hozzáadni kívánt adatkészlettípust. Az előző lépésben kiválasztott megosztástípustól (pillanatkép vagy helyben) függően az adatkészlettípusok egy másik listája jelenik meg. Ha egy Azure SQL-adatbázisból vagy az Azure SQL Data Warehouse-ból történő megosztás, a rendszer kérni fogja néhány SQL-hitelesítő adatokat. Hitelesítse magát az előfeltételek részeként létrehozott felhasználó használatával.

    ![AddDatasets](./media/add-datasets.png "Adatkészletek hozzáadása")    

1. Keresse meg a megosztani kívánt objektumot, és válassza az "Adatkészletek hozzáadása" lehetőséget. 

    ![SelectDatasets (Adatkészletek) kijelölése](./media/select-datasets.png "Adatkészletek kijelölése")    

1. A Címzettek lapon a "+ Címzett hozzáadása" lehetőséget választva írja be az adatfogyasztó e-mail címét. 

    ![Címzettek hozzáadása](./media/add-recipient.png "Címzettek hozzáadása") 

1. Válassza a **Continue (Folytatás) lehetőséget.**

1. Ha a pillanatképmegosztás típusát választotta, beállíthatja a pillanatkép ütemezését, hogy az adatok frissítéseit az adatfogyasztó számára biztosítsa. 

    ![Pillanatképek engedélyezése](./media/enable-snapshots.png "Pillanatképek engedélyezése") 

1. Válassza ki a kezdési időt és az ismétlődési időközt. 

1. Válassza a **Continue (Folytatás) lehetőséget.**

1. A Véleményezés + Létrehozás lapon tekintse át a Csomag tartalmát, a Beállításokat, a Címzettek és a Szinkronizálási beállításokat. Válassza a **Létrehozás lehetőséget**

Az Azure-adatmegosztás létrejött, és az adatmegosztás címzettje készen áll a meghívás elfogadására. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy Azure-adatmegosztást, és hogyan hívhat meg címzetteket. Ha többet szeretne megtudni arról, hogy az adatfogyasztó hogyan fogadhat el és fogadhat adatmegosztást, folytassa az [adatelfogadási és -fogadási](subscribe-to-data-share.md) oktatóanyaggal. 
