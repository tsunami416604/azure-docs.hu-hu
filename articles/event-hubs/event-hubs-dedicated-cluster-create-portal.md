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
ms.openlocfilehash: ffdcd3e6314e659443630f74f8f17261659f1246
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158510"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-preview-using-azure-portal"></a>Gyors útmutató: A dedikált Event Hubs-fürt létrehozása (előzetes verzió) az Azure portal használatával 
Event Hubs-fürtök egybérlős központi telepítések a legnagyobb erőforrás-igényű streamelési igényekkel rendelkező ügyfelek számára kínálnak. Az egybérlős ajánlattal rendelkezik garantált 99,99 %-os SLA-t, és csak a saját dedikált tarifacsomagban érhető el. Az Event Hubs-fürt a garantált és subsecond késése másodpercenként több millió bejövő forgalom is. A dedikált fürtön belül létrehozott névterek és az event hubs többek között az összes, a standard ajánlat és más, de minden bejövő forgalom korlátok nélkül. Ezenkívül tartalmazza a népszerű [Event Hubs Capture](event-hubs-capture-overview.md) szolgáltatást további költségek nélkül, amely lehetővé teszi a batch- és naplózási adatok automatikusan streameli az [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) vagy [Azure Data Lake Storage 1. generációs](../data-lake-store/data-lake-store-overview.md). A dedikált Event Hubs-fürtök további információkért lásd: [dedikált Event Hubs – áttekintés](event-hubs-dedicated-overview.md).

Dedikált fürtök kiépítése, és a számlázás alapját **kapacitásegységek (CUs)**, egy előre kiosztott Processzor és memória-erőforrások mennyiségét. Minden egyes fürt az 1, 2, 4, 8, 12, 16 vagy 20 kapacitásegységek vásárolhatók. Fürt vertikális felskálázásakor, először egy CU-fürt létrehozása, majd küldje el a [támogatási jegyet](#submit-a-support-request-for-your-dedicated-cluster). 

Ebben a rövid útmutatóban egy dedikált Event Hubs-fürtöt, az Azure portal használatával fog létrehozni.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez győződjön meg róla, hogy rendelkezik az alábbiakkal:

- Egy Azure-fiók. Ha nem rendelkezik ilyennel, [fiókot vásárol](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) megkezdése előtt. Ez a funkció nem támogatott az ingyenes Azure-fiókra. 
- [A Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Update 3 (verziószám: 15.3, 26730.01) vagy újabb.
- A [.NET Standard SDK](https://dotnet.microsoft.com/download) 2.0-s vagy újabb verziója.
- [Létrehozott egy erőforráscsoport](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Hozzon létre egy Event Hubs dedikált fürt
Egy dedikált Event Hubs-fürt hivatkozik a teljesen minősített tartománynevét, amely egy vagy több névteret hozhat létre egyedi hatókörkezelési tárolót biztosít. Az önkiszolgáló portál előzetes fázisában élmény, a támogatott régiók egy CU fürtöt hozhat létre. Ha a fürt egy CU-nál nagyobb, először az egyik CU hozzon létre egy fürtöt, és majd a fürt vertikális kérelmezéséhez. 

Hozzon létre egy fürtöt az erőforráscsoportban, az Azure portal használatával, hajtsa végre a következő lépéseket:

1. Az a [az Azure portal](https://portal.azure.com)válassza **+ erőforrás létrehozása** a bal oldali navigációs menüjében.
2. Típus **Event Hubs-fürtök** a keresési sávon, majd az ENTER BILLENTYŰT.
3. Az a **-fürt létrehozása** lapon, tegye a következőket:
    1. Adjon meg egy **a fürt neve**. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
    2. Válassza ki a **előfizetés** , amelyre szeretné létrehozni a fürtöt.
    3. Válassza ki a **erőforráscsoport** , amelyre szeretné létrehozni a fürtöt.
    4. Válassza ki a **hely** a fürt számára. Ha az elsődleges régió szürkén jelenik meg, küldje el a [támogatási kérelem](#submit-a-support-request-for-your-dedicated-cluster).
    5. Válassza ki a **tovább: A címkék** gombra a lap alján. Lehet, hogy néhány percet várnia kell, amíg a rendszer teljes mértékben kiépíti az erőforrásokat.

        ![Event Hubs-fürt - alapismeretek lap létrehozása](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
4. Az a **címkék** lapon, tegye a következőket:
    1. Adjon meg egy **neve** és a egy **érték** hozzáadni kívánt címke. Ez a lépés **nem kötelező**.  
    2. Válassza ki a **felülvizsgálat + létrehozás** gombra.

        ![Event Hubs-fürt lap – Címkék lap létrehozása](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
5. Az a **felülvizsgálat + létrehozás** lapon tekintse át a részletes adatait, és válassza ki **létrehozás**. 

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


## <a name="submit-a-support-request-for-your-dedicated-cluster"></a>Támogatási kérelmet a dedikált fürt

Egy támogatási kérést szeretne beküldeni, kövesse az alábbi lépéseket:

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
1. A fürt törléséhez válassza ki **törlése** a felső menüben.
2. Törölje a fürtöt a kívánja megerősítése ekkor megjelenik egy üzenet.
3. Írja be a **a fürt neve** válassza **törlése** törölje a fürtöt.

    ![Fürt oldal törlése](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott egy Event Hubs-fürt. A részletes útmutatást küldhet és események fogadása az eseményközpontok felé, és az Azure storage vagy az Azure Data Lake Store-események rögzítése a következő oktatóanyagokban talál:

- [.NET Core-on események küldéséhez és fogadásához](event-hubs-dotnet-standard-getstarted-send.md)
- [Event Hubs Capture engedélyezése az Azure portal használatával](event-hubs-capture-enable-through-portal.md)
- [Használja az Azure Event Hubs-beli Apache kafka](event-hubs-for-kafka-ecosystem-overview.md)
