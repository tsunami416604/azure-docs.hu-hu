---
title: Azure rövid útmutató – hozzon létre egy dedikált Event Hubs-fürtöt, az Azure portal használatával |} A Microsoft Docs
description: Ez a rövid útmutatóban megismerheti, hogyan hozhat létre az Azure Event Hubs-fürtöt az Azure portal használatával.
services: event-hubs
documentationcenter: ''
author: xurui203
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/02/2019
ms.author: xurui
ms.openlocfilehash: 269ecca8683229a56d40dfacc354abbd7ce10762
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688587"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Gyors útmutató: Hozzon létre egy dedikált Event Hubs-fürt Azure portal használatával 
Event Hubs-fürtök egybérlős központi telepítések a legnagyobb erőforrás-igényű streamelési igényekkel rendelkező ügyfelek számára kínálnak. Ezt az ajánlatot rendelkezik garantált 99,99 %-os SLA-t, és csak a dedikált tarifacsomagban elérhető. Egy [Event Hubs-fürt](event-hubs-dedicated-overview.md) bejövő forgalom több millió esemény garantált kapacitás és subsecond késéssel másodpercenként is. A fürtön belül létrehozott névterek és az event hubs többek között az összes, a standard ajánlat és más, de minden bejövő forgalom korlátok nélkül. A dedikált ajánlat is tartalmaz a népszerű [Event Hubs Capture](event-hubs-capture-overview.md) szolgáltatást további költségek nélkül, így automatikusan batch és a naplófájlok adatfolyamok [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) vagy [ Az Azure Data Lake Storage 1. generációs gyűjtések](../data-lake-store/data-lake-store-overview.md).

Dedikált fürtök kiépítése, és a számlázás alapját **kapacitásegységek (CUs)** , egy előre kiosztott Processzor és memória-erőforrások mennyiségét. Minden egyes fürt az 1, 2, 4, 8, 12, 16 vagy 20 kapacitásegységek vásárolhatók. Ez a rövid útmutatóban azt végigvezeti egy 1 CU az Event Hubs-fürtöt az Azure Portalon keresztül.

> [!NOTE]
> Az önkiszolgáló felületet jelenleg előzetes verzióban érhető el [az Azure Portal](https://aka.ms/eventhubsclusterquickstart). Ha a dedikált ajánlat kapcsolatban bármilyen kérdése van, vegye fel a kapcsolatot a [Event Hubs-csapattal](mailto:askeventhubs@microsoft.com).


## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez győződjön meg róla, hogy rendelkezik az alábbiakkal:

- Egy Azure-fiók. Ha nem rendelkezik ilyennel, [fiókot vásárol](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) megkezdése előtt. Ez a funkció nem támogatott az ingyenes Azure-fiókra. 
- [A Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Update 3 (verziószám: 15.3, 26730.01) vagy újabb.
- A [.NET Standard SDK](https://dotnet.microsoft.com/download) 2.0-s vagy újabb verziója.
- [Létrehozott egy erőforráscsoport](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Hozzon létre egy Event Hubs dedikált fürt
Az Event Hubs-fürt egy vagy több névteret hozhat létre egy egyedi hatókörkezelési tárolót biztosít. Az önkiszolgáló portálja előzetes fázisában, létrehozhat 1 CU-fürtök minden régióban. Ha a fürtre van szüksége egy 1-nél nagyobb CU, a fürt vertikális felskálázása létrehozása után az Azure-támogatási kérést küldhet.

Fürt létrehozása az erőforráscsoportban, az Azure portal használatával, kérjük, végezze el az alábbi lépéseket:

1. Hajtsa végre a [ezt a hivatkozást](https://aka.ms/eventhubsclusterquickstart) a fürt létrehozása az Azure Portalon. Ezzel szemben, válassza ki **minden szolgáltatás** a bal oldali navigációs panelen, majd írja be az "Event Hubs-fürtök" a keresősávba, és az eredmények listájából válassza ki a "Event Hubs-fürtök".
2. Az a **-fürt létrehozása** lap, adja meg a következőket:
    1. Adjon meg egy **a fürt neve**. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
    2. Válassza ki a **előfizetés** , amelyre szeretné létrehozni a fürtöt.
    3. Válassza ki a **erőforráscsoport** , amelyre szeretné létrehozni a fürtöt.
    4. Válassza ki a **hely** a fürt számára. Ha az elsődleges régió szürkén jelenik meg, ideiglenesen kapacitás van, és küldhet egy [támogatási kérelem](#submit-a-support-request) az Event Hubs csapatának.
    5. Válassza ki a **tovább: A címkék** gombra a lap alján. Lehet, hogy néhány percet várnia kell, amíg a rendszer teljes mértékben kiépíti az erőforrásokat.

        ![Event Hubs-fürt - alapismeretek lap létrehozása](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Az a **címkék** lap, adja meg a következőket:
    1. Adjon meg egy **neve** és a egy **érték** hozzáadni kívánt címke. Ez a lépés **nem kötelező**.  
    2. Válassza ki a **felülvizsgálat + létrehozás** gombra.

        ![Event Hubs-fürt lap – Címkék lap létrehozása](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Az a **felülvizsgálat + létrehozás** lapon tekintse át a részletes adatait, és válassza ki **létrehozás**. 

    ![Felülvizsgálat létrehozása az Event Hubs-fürt lap – + lap létrehozása](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Egy fürtön belüli névtér és az eseményközpont létrehozása

1. Az egy fürtön belüli névtér létrehozása az **Event Hubs-fürt** a fürthöz, válassza a lap **+ Namespace** a felső menüben.

    ![Fürt kezelése lap – névtér gomb hozzáadása](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. A névtér-lap létrehozása tegye a következőket:
    1. Adjon meg egy **a névtér neve**.  A rendszer ellenőrzi, hogy a név elérhető-e.
    2. A névtér örökli a következő tulajdonságokkal:
        1. Előfizetés azonosítója
        2. Erőforráscsoport
        3. Location egység
        4. Fürt neve
    3. Válassza ki **létrehozás** a névteret létre kívánja hozni. Mostantól felügyelheti a fürthöz.  

        ![Névtér létrehozása a fürt lapján](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. A névtér létrehozása után is [létrehoz egy eseményközpontot](event-hubs-create.md#create-an-event-hub) , akkor általában létrehoznia egyet egy adott névtéren belül. 


## <a name="submit-a-support-request"></a>Támogatási kérés küldése

Ha a fürt méretének módosítása után létrehozásakor, vagy ha az elsődleges régióban nem érhető el, adjon be támogatási kérést az alábbi lépéseket:

1. A [az Azure portal](https://portal.azure.com)válassza **súgó + támogatás** a bal oldali menüből.
2. Válassza ki **+ új támogatási kérelem** a támogatási menüből.
3. A támogatási oldalon tegye a következőket:
    1. A **Issue Type**válassza **műszaki** a legördülő listából.
    2. Az **Előfizetés** beállításnál válassza ki az előfizetését.
    3. A **szolgáltatás**válassza **saját szolgáltatások**, majd válassza ki **az Event Hubs**.
    4. A **erőforrás**, válassza ki a fürtöt, ha már létezik, ellenkező esetben válasszon **általános kérdés/erőforrás nem érhető el**.
    5. A **Problématípus**válassza **kvóta**.
    6. A **probléma altípus**, válassza ki a legördülő listából válassza ki az alábbi értékeket:
        1. Válassza ki **kérelem dedikált termékváltozat** kérése a szolgáltatás támogatott az Ön régiójában.
        2. Válassza ki **méretezési felfelé vagy lefelé dedikált fürt méretezése kérelmet** Ha növelheti vagy csökkentheti a dedikált fürt. 
    7. A **tulajdonos**, ismertesse a problémát.

        ![Támogatási jegy lap](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Dedikált fürt törlése
 
1. A fürt törléséhez válassza ki **törlése** a felső menüben. Vegye figyelembe, hogy a fürt díjat kell fizetni egy legalább 4 óra létrehozása után. 
2. Törölje a fürtöt a kívánja megerősítése ekkor megjelenik egy üzenet.
3. Írja be a **a fürt neve** válassza **törlése** törölje a fürtöt.

    ![Fürt oldal törlése](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott egy Event Hubs-fürt. A részletes útmutatást küldhet és események fogadása az eseményközpontok felé, és az Azure storage vagy az Azure Data Lake Store-események rögzítése a következő oktatóanyagokban talál:

- [.NET Core-on események küldéséhez és fogadásához](event-hubs-dotnet-standard-getstarted-send.md)
- [Event Hubs Capture engedélyezése az Azure portal használatával](event-hubs-capture-enable-through-portal.md)
- [Használja az Azure Event Hubs-beli Apache kafka](event-hubs-for-kafka-ecosystem-overview.md)
