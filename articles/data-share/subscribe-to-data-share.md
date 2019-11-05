---
title: 'Oktatóanyag: & fogadása az Azure-adatmegosztásról'
description: Oktatóanyag – az Azure-adatmegosztás használatával elfogadhatja és fogadhatja az adatfogadást
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 9c24f54fe846459187488b0a65b2582914e25e2a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499346"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Oktatóanyag: az Azure-adatmegosztás használatával fogadja el és fogadja az adatfogadást  

Ebből az oktatóanyagból megtudhatja, hogyan fogadhatja el az adatmegosztási meghívásokat az Azure-adatmegosztás használatával. Megtudhatja, hogyan fogadhatja Önnel az Önnel megosztott adatmennyiséget, valamint hogyan engedélyezheti a rendszeres frissítési időközt annak biztosítása érdekében, hogy mindig a legfrissebb pillanatképet kapjon az Önnel megosztott adatokról. 

> [!div class="checklist"]
> * Azure-beli adatmegosztási meghívás elfogadása
> * Azure-beli adatmegosztási fiók létrehozása
> * Adja meg az adatai célhelyét
> * Előfizetés létrehozása az adatmegosztásra ütemezett frissítéshez

## <a name="prerequisites"></a>Előfeltételek
Az adatmegosztási Meghívások elfogadása előtt számos Azure-erőforrást kell kiépíteni, amelyek alább láthatók. 

Az adatmegosztási Meghívások elfogadása előtt győződjön meg arról, hogy az összes előfeltétel befejeződik. 

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* Egy adatmegosztás meghívása: Microsoft Azure egy meghívót az "Azure adatmegosztási meghívása **<yourdataprovider@domain.com>** " című témakörben.

### <a name="receive-data-into-a-storage-account"></a>Adatfogadás egy Storage-fiókba: 

* Azure Storage-fiók: Ha még nem rendelkezik ilyennel, létrehozhat egy [Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)is. 
* Jogosultság a szerepkör-hozzárendelés hozzáadásához a Storage-fiókhoz, amely megtalálható a *Microsoft. Authorization/szerepkör-hozzárendelés/írás* engedélyben. Ez az engedély létezik a tulajdonosi szerepkörben. 
* A Microsoft. DataShare erőforrás-szolgáltatói regisztrációja. Ennek módjáról az [Azure Resource Providers](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) dokumentációjában talál további információt. 

> [!IMPORTANT]
> Az Azure-beli adatmegosztások elfogadásához és fogadásához először regisztrálnia kell a Microsoft. DataShare erőforrás-szolgáltatót, és annak a Storage-fióknak a tulajdonosának kell lennie, amelybe befogadja az adatgyűjtést Az adatmegosztási erőforrás-szolgáltató regisztrálásához, valamint a Storage-fiók tulajdonosként való hozzáadásához kövesse az Azure-beli [Adatmegosztások hibakeresése című cikkben](data-share-troubleshoot.md) leírt utasításokat. 

### <a name="receive-data-into-a-sql-based-source"></a>Adatfogadás SQL-alapú forrásba:

* Az Azure SQL Database vagy Azure SQL Data Warehouse eléréséhez az adatmegosztási MSI-fájlhoz tartozó engedély. Ezt a következő lépések végrehajtásával teheti meg: 
    1. Állítsa be saját magát a kiszolgáló Azure Active Directory-rendszergazdájaként.
    1. Kapcsolódjon a Azure SQL Database/adattárházhoz Azure Active Directory használatával.
    1. A lekérdezés-szerkesztő (előzetes verzió) használatával hajtsa végre a következő parancsfájlt az adatmegosztási MSI db_owner való hozzáadásához. Active Directory használatával kell kapcsolódnia, nem SQL Server a hitelesítéshez. 

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Vegye figyelembe, hogy a *< share_acc_name >* az adatmegosztási fiók neve. Ha még nem hozott létre adatmegosztási fiókot, később is visszatérhet ehhez az előfeltételhöz.         

* Ügyfél IP-SQL Server tűzfallal való hozzáférés: ezt a következő lépések végrehajtásával teheti meg: 1. Navigáljon a *tűzfalakhoz és a virtuális hálózatokhoz* 1. Kattintson a **be** kapcsolóra az Azure-szolgáltatásokhoz való hozzáférés engedélyezéséhez. 

Az előfeltételek befejezését követően készen áll arra, hogy befogadja az adatait a SQL Serverba.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Bejelentkezés az [Azure Portalra](https://portal.azure.com/).

## <a name="open-invitation"></a>Meghívás megnyitása

Az adatszolgáltatótól érkező meghívót a Beérkezett üzenetek között találja. A meghívó Microsoft Azure származik, és az **Azure-beli adatmegosztási meghívót <yourdataprovider@domain.com>** . Jegyezze fel a megosztás nevét annak biztosításához, hogy a megfelelő megosztást fogadja el, ha több meghívót is használ. 

Az Azure-beli meghívót a meghívás **megtekintése** lehetőségre kattintva tekintheti meg. Ekkor a kapott megosztások nézetre kerül.

![Meghívók](./media/invitations.png "Meghívások listája") 

Válassza ki a megtekinteni kívánt megosztást. 

## <a name="accept-invitation"></a>Meghívás elfogadása
Győződjön meg arról, hogy az összes mezőt felülvizsgálják, beleértve a használati **feltételeket**is. Ha elfogadja a használati feltételeket, a jelölőnégyzet bejelölésével jelezheti, hogy elfogadja. 

![Használati feltételek](./media/terms-of-use.png "Használati feltételek") 

A *cél adatmegosztási fiók*területen válassza ki azt az előfizetést és erőforráscsoportot, amelybe az adatmegosztást telepíteni fogja. 

Az **adatmegosztási fiók** mezőben válassza az **új létrehozása** lehetőséget, ha nem rendelkezik meglévő adatmegosztási fiókkal. Ellenkező esetben válasszon ki egy meglévő adatmegosztási fiókot, amelyet el szeretne fogadni az adatmegosztásban. 

A *Beérkezett megosztás neve* mezőben hagyhatja az alapértelmezett által megadott értéket, vagy megadhat egy új nevet a fogadott megosztáshoz. 

![Célként megadott adatmegosztási fiók](./media/target-data-share.png "Célként megadott adatmegosztási fiók") 

Ha jóváhagyta a használati feltételeket, és megadta a megosztás helyét, válassza az *elfogadás és konfigurálás*lehetőséget. Ha ezt a beállítást választja, a rendszer létrehoz egy megosztási előfizetést, és a következő képernyő megkéri, hogy válasszon ki egy célként szolgáló Storage-fiókot, amelybe másolni kívánja az adatait. 

![Elfogadás beállításai](./media/accept-options.png "Elfogadás beállításai") 

Ha azt szeretné, hogy a meghívót most is elfogadja, de később konfigurálja a tárhelyet, válassza az *elfogadás és konfigurálás később*lehetőséget. Ez a beállítás lehetővé teszi, hogy később konfigurálja a cél Storage-fiókot. A tárterület későbbi konfigurálásának folytatásához tekintse meg a [Storage-fiók konfigurálása](how-to-configure-mapping.md) című oldalt, amely részletesen ismerteti az adatmegosztási konfiguráció folytatásának lépéseit. 

Ha nem szeretné elfogadni a meghívót, válassza az *elutasítás*lehetőséget. 

## <a name="configure-storage"></a>A tárolás konfigurálása
A tároló *beállításai*területen válassza ki azt az előfizetést, erőforráscsoportot és Storage-fiókot, amelyet az adataihoz szeretne fogadni. 

![Cél tárolási beállításai](./media/target-storage-settings.png "Cél tárterülete") 

Az adatok rendszeres frissítésének engedélyezéséhez győződjön meg arról, hogy engedélyezi a pillanatkép-beállításokat. Vegye figyelembe, hogy ha az adatszolgáltató belefoglalta az adatmegosztásba, csak egy pillanatkép-beállításra vonatkozó ütemterv jelenik meg. 

![Pillanatkép-beállítások](./media/snapshot-settings.png "Pillanatkép-beállítások") 

Kattintson a *Mentés* gombra. 

> [!IMPORTANT]
> Ha SQL-alapú adatokat kap, és ezeket az adatokat SQL-alapú forrásként szeretné fogadni, látogasson el az [adatkészlet konfigurálása](how-to-configure-mapping.md) – útmutató című témakörből megtudhatja, hogyan konfigurálhat egy SQL Server az adatkészlet célhelyének megadásához. 

## <a name="trigger-a-snapshot"></a>Pillanatkép indítása

A kapott megosztások – > Részletek lapon elindíthat egy pillanatképet a **Pillanatkép indítása**lehetőség kiválasztásával. Itt aktiválhatja az adatok teljes vagy növekményes pillanatképét. Ha az első alkalommal fogadja az adatait az adatszolgáltatótól, válassza a teljes másolás lehetőséget. 

![Pillanatkép indítása](./media/trigger-snapshot.png "Pillanatkép indítása") 

Ha a legutóbbi futtatási állapot *sikeres*, nyissa meg a Storage-fiókot a kapott adatértékek megtekintéséhez. 

A használt Storage-fiók megadásához válassza az **adatkészletek**lehetőséget. 

![Fogyasztói adatkészletek](./media/consumer-datasets.png "Fogyasztói adatkészlet megfeleltetése") 

## <a name="view-history"></a>Előzmények megtekintése
A pillanatképek előzményeinek megtekintéséhez navigáljon a fogadott megosztások – > előzmények elemre. Itt megtalálhatja az elmúlt 60 napban létrehozott összes pillanatkép előzményeit. 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan fogadhat és fogadhat Azure-adatmegosztást. Ha többet szeretne megtudni az Azure-beli adatmegosztási fogalmakról, folytassa a [fogalmakkal: az Azure adatmegosztási terminológiáját](terminology.md).