---
title: 'Oktatóanyag: Adatok fogadásának elfogadása & - Azure Data Share'
description: Oktatóanyag – Adatok elfogadása és fogadása az Azure Data Share használatával
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 5b7d9cd7e7d438cf2beac76d5d8bcc78d377a8f4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77083103"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Oktatóanyag: Adatok elfogadása és fogadása az Azure Data Share használatával  

Ebben az oktatóanyagban megtudhatja, hogyan fogadhat el egy adatmegosztási meghívót az Azure Data Share használatával. Megtudhatja, hogyan fogadhatja el az Önnel megosztott adatokat, valamint azt, hogy miként engedélyezheti a rendszeres frissítési időközt annak érdekében, hogy mindig a legfrissebb pillanatképet kapja meg az Önnel megosztott adatokról. 

> [!div class="checklist"]
> * Azure-adatmegosztási meghívó elfogadása
> * Azure-adatmegosztási fiók létrehozása
> * Az adatok célhelynek megadása
> * Előfizetés létrehozása az adatmegosztáshoz az ütemezett frissítéshez

## <a name="prerequisites"></a>Előfeltételek
Az adatmegosztási meghívók elfogadása előtt ki kell építenie számos Azure-erőforrást, amelyek az alábbiakban vannak felsorolva. 

Az adatmegosztási meghívás elfogadása előtt győződjön meg arról, hogy minden előfeltétel teljes. 

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* Adatmegosztási meghívó: Meghívás a Microsoft Azure-ból az "Azure-adatmegosztási **<yourdataprovider@domain.com>** meghívó" címmel.
* Regisztrálja a [Microsoft.DataShare erőforrás-szolgáltatót](concepts-roles-permissions.md#resource-provider-registration) az Azure-előfizetésben, ahol létrehoz egy adatmegosztási erőforrást és az Azure-előfizetést, ahol a cél Azure-adattárak találhatók.

### <a name="receive-data-into-a-storage-account"></a>Adatok fogadása tárfiókba: 

* Egy Azure Storage-fiók: Ha még nem rendelkezik ilyen, létrehozhat egy [Azure Storage-fiókot.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) 
* Írási engedély a *Microsoft.Storage/storageAccounts/write*könyvtárfiókba történő íráshoz. Ez az engedély a közreműködői szerepkörben található. 
* A *Microsoft.Authorization/role assignments/write*. Ez az engedély a Tulajdonos szerepkörben található.  

### <a name="receive-data-into-a-sql-based-source"></a>Adatok fogadása SQL-alapú forrásba:

* Engedélyt kap a *Microsoft.Sql/servers/databases/write*. Ez az engedély a közreműködői szerepkörben található. 
* Az Azure SQL Database vagy az Azure SQL Data Warehouse eléréséhez az adatmegosztási erőforrás felügyelt identitásának engedélyezéséhez. Ez a következő lépéseken keresztül hajtható végre: 
    1. Állítsa be magát az SQL-kiszolgáló Azure Active Directory-rendszergazdájaként.
    1. Csatlakozzon az Azure SQL Database/Data Warehouse az Azure Active Directory használatával.
    1. A Lekérdezésszerkesztő (előzetes verzió) segítségével hajtsa végre a következő parancsfájlt, és adja hozzá az adatmegosztási felügyelt identitást "db_datareader, db_datawriter, db_ddladmin" néven. Az Active Directory használatával kell csatlakoznia, nem pedig SQL Server-hitelesítéssel. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Ne feledje, hogy a *<share_acc_name>* az adatmegosztási erőforrás neve. Ha még nem hozott létre adatmegosztási erőforrást, később visszatérhet erre az előfeltételre.         

* Ügyfél IP SQL Server tűzfal-hozzáférés. Ez a következő lépéseken keresztül hajtható végre: 
    1. Az Azure Portal SQL-kiszolgálóján keresse meg a *tűzfalakat és a virtuális hálózatokat*
    1. Kattintson a **bekapcsolt** kapcsolóra az Azure Services elérésének engedélyezéséhez.
    1. Kattintson **az ügyfél IP-címének hozzáadása** gombra, majd a Mentés **gombra.** Az ügyfél IP-címe változhat. Előfordulhat, hogy ezt a folyamatot meg kell ismételni, amikor legközelebb adatokat fogad egy SQL-célba az Azure Portalról. IP-tartományt is hozzáadhat. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Adatok fogadása Egy Azure Data Explorer-fürtbe: 

* Az Azure Data Explorer-fürt ugyanabban az Azure-adatközpontban található, mint az adatszolgáltató Adatkezelő-fürtje: Ha még nem rendelkezik ilyentel, létrehozhat egy [Azure Data Explorer-fürtöt.](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) Ha nem ismeri az adatközpont az adatközpont az adatközpont, a folyamat későbbi részében létrehozhatja a fürt.
* A *Microsoft.Kusto/clusters/write*programban található Azure Data Explorer-fürtbe való írásra vonatkozó engedély. Ez az engedély a közreműködői szerepkörben található. 
* A *Microsoft.Authorization/role assignments/write*programban található Azure Data Explorer-fürthöz szerepkör-hozzárendelés hozzáadására vonatkozó engedély. Ez az engedély a Tulajdonos szerepkörben található. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="open-invitation"></a>Meghívó megnyitása

1. Ellenőrizze, hogy a postafiókjában van-e meghívó az adatszolgáltatótól. A meghívó a Microsoft Azure-ból származik, melynek címe **Azure Data Share meghívó a-tól. <yourdataprovider@domain.com> ** Jegyezze fel a megosztás nevét, hogy több meghívás esetén biztosan elfogadja a megfelelő megosztást. 

1. Válassza ki a **Meghívó megtekintése** az Azure-ban megtekintheti a meghívót. Ezzel a Beérkezett megosztások nézetben jelenik meg.

   ![Meghívások](./media/invitations.png "Meghívók listája") 

1. Válassza ki a megtekinteni kívánt megosztást. 

## <a name="accept-invitation"></a>Meghívó elfogadása
1. Győződjön meg arról, hogy minden mező tanusított, beleértve a **Használati feltételeket is.** Ha elfogadja a használati feltételeket, be kell jelölnie a jelölőnégyzetet, hogy jelezze, egyetért. 

   ![Használati feltételek](./media/terms-of-use.png "Használati feltételek") 

1. A *Céladatmegosztási fiók csoportban*válassza ki azt az előfizetési és erőforráscsoportot, amelybe az adatmegosztást telepíteni fogja. 

   Az **Adatmegosztási fiók mezőben** válassza az **Új létrehozása lehetőséget,** ha nincs meglévő adatmegosztási fiókja. Ellenkező esetben válasszon ki egy meglévő adatmegosztási fiókot, amelybe el szeretné fogadni az adatmegosztást. 

   A **Fogadott megosztási név** mezőben meghagyhatja az adatmegadása által megadott alapértelmezett értéket, vagy új nevet adhat meg a fogadott megosztásnak. 

   ![Céladat-megosztási fiók](./media/target-data-share.png "Céladat-megosztási fiók") 

1. Miután elfogadta a használati feltételeket, és megadta a megosztás helyét, válassza az *Elfogadás és konfigurálás lehetőséget.* Létrejön egy megosztási előfizetés.

   Pillanatkép-alapú megosztás esetén a következő képernyő megfogja kérni, hogy válassza ki az adatokat másolni kívánt céltárfiókot. 

   ![Elfogadási beállítások](./media/accept-options.png "Elfogadási beállítások") 

   Ha most szeretné elfogadni a meghívást, de később konfigurálja a céladattárolót, válassza *az Elfogadás és a későbbi konfigurálás*lehetőséget. A tárhely későbbi konfigurálásának folytatásához olvassa el az [adatkészlet-leképezések konfigurálása](how-to-configure-mapping.md) lapon, ahol részletesen ismerteti, hogyan folytathatja az adatmegosztási konfigurációt. 

   A helyen történő megosztásról az [adatkészlet-leképezések konfigurálása](how-to-configure-mapping.md) lapon részletesen megtudhatja, hogyan folytathatja az adatmegosztási konfigurációt. 

   Ha nem szeretné elfogadni a meghívást, válassza az *Elutasítás*lehetőséget. 

## <a name="configure-storage"></a>A tárolás konfigurálása
1. A *Céltárolási beállítások csoportban*válassza ki azt az Előfizetést, erőforráscsoportot és tárfiókot, amelybe az adatokat szeretné befogadni. 

   ![Céltárolási beállítások](./media/target-storage-settings.png "Céltárolás") 

1. Az adatok rendszeres frissítésének fogadásához győződjön meg arról, hogy engedélyezi a pillanatkép-beállításokat. Ne feledje, hogy csak akkor jelenik meg a pillanatkép-beállításütemezés, ha az adatszolgáltató belefoglalta azt az adatmegosztásba. 

   ![Pillanatkép-beállítások](./media/snapshot-settings.png "Pillanatkép-beállítások") 

1. Kattintson a *Mentés* gombra. 

> [!IMPORTANT]
> Ha SQL-alapú adatokat fogad, és ezeket az adatokat SQL-alapú forrásból szeretné fogadni, látogasson el [az adatkészlet leképezési útmutatókonfigurálásának](how-to-configure-mapping.md) ellátogatására, amelyből megtudhatja, hogyan konfigurálhatja az SQL Servert az adatkészlet célállomásaként. 

## <a name="trigger-a-snapshot"></a>Pillanatkép aktiválása
Ezek a lépések csak a pillanatkép-alapú megosztásra vonatkoznak.

1. A Beérkezett megosztások -> részletek lapon az **Eseményindító pillanatkép**lehetőség kiválasztásával indíthat el pillanatképet. Itt elindíthatja az adatok teljes vagy növekményes pillanatképét. Ha ez az első alkalom, hogy adatokat fogad az adatszolgáltatótól, válassza a teljes másolatot. 

   ![Eseményindító pillanatképe](./media/trigger-snapshot.png "Eseményindító pillanatképe") 

1. Ha az utolsó futtatás iadállapota *sikeres,* lépjen a céladattárba a fogadott adatok megtekintéséhez. Válassza **az Adatkészletek**lehetőséget, és kattintson a célelérési út hivatkozására. 

   ![Fogyasztói adatkészletek](./media/consumer-datasets.png "Fogyasztói adatkészlet-leképezés") 

## <a name="view-history"></a>Előzmények megtekintése
A pillanatképek előzményeinek megtekintéséhez keresse meg a Beérkezett megosztások -> előzmények című területet. Itt megtalálja az elmúlt 60 napban létrehozott összes pillanatkép előzményeit. 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan fogadhat el és fogadhat el egy Azure-adatmegosztást. Ha többet szeretne megtudni az Azure Data Share-fogalmakról, folytassa [a Fogalmak: Azure-adatmegosztási terminológia](terminology.md)című részt.