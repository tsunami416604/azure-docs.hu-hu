---
title: Az Azure Portal használatával hozzon létre egy dedikált fürthöz dedikált eseményközpontot
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre egy Azure Event Hubs-fürtöt az Azure Portal használatával.
services: event-hubs
documentationcenter: ''
author: femila
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/20/2019
ms.author: femila
ms.openlocfilehash: 5b1574eaac8771043e09500225b65e4835c8e627
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77157482"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Rövid útmutató: Hozzon létre egy dedikált Event Hubs-fürtöt az Azure Portal használatával 
Az Event Hubs-fürtök egybérlős telepítéseket kínálnak a legigényesebb streamelési igényekkel rendelkező ügyfelek számára. Ez az ajánlat garantált9,99%-os SLA-val rendelkezik, és csak a dedikált tarifacsomagunkban érhető el. Az [Event Hubs-fürt](event-hubs-dedicated-overview.md) másodpercenként több millió eseményt képes befeketésni garantált kapacitással és másodperc alatti késéssel. A fürtön belül létrehozott névterek és eseményközpontok tartalmazzák a szabványos ajánlat összes szolgáltatását és egyebeket, de a be- és információtér-korlátok nélkül. A dedikált ajánlat tartalmazza a népszerű [Event Hubs Capture](event-hubs-capture-overview.md) funkciót is, további költségek nélkül, lehetővé téve az adatfolyamok automatikus kötegelését és naplózását [az Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) vagy az Azure Data Lake Storage [Gen 1](../data-lake-store/data-lake-store-overview.md)szolgáltatásba.

A dedikált fürtöket **kapacitásegységek (CUs)** terheli, amely előre lefoglalt mennyiségű PROCESSZOR- és memória-erőforrás. Fürthöz 1, 2, 4, 8, 12, 16 vagy 20 e-t vásárolhat. Ebben a rövid útmutatóban bemutatjuk, hogy hozzon létre egy 1 CU-eseményközpontok fürtaz Azure Portalon keresztül.

> [!NOTE]
> Ez az önkiszolgáló élmény jelenleg előzetes verzióban érhető el az [Azure Portalon.](https://aka.ms/eventhubsclusterquickstart) Ha bármilyen kérdése van a dedikált ajánlattal kapcsolatban, kérjük, forduljon az [Event Hubs csapatához.](mailto:askeventhubs@microsoft.com)


## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez győződjön meg róla, hogy rendelkezik az alábbiakkal:

- Egy Azure-fiók. Ha még nem rendelkezik ilyenel, a kezdés előtt [vásároljon fiókot.](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) Ez a funkció nem támogatott egy ingyenes Azure-fiókkal. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 3-as vagy újabb verzió (15.3-as, 26730.01-es verzió) vagy újabb verzió.
- A [.NET Standard SDK](https://dotnet.microsoft.com/download) 2.0-s vagy újabb verziója.
- [Létrehozott egy erőforráscsoportot.](../event-hubs/event-hubs-create.md#create-a-resource-group)

## <a name="create-an-event-hubs-dedicated-cluster"></a>Dedikált fürt létrehozása
Az Event Hubs-fürt egy egyedi hatókörtárolót biztosít, amelyben egy vagy több névteret hozhat létre. A portál önkiszolgálási élményének ebben az előzetes fázisában 1 CU-fürtöket hozhat létre bizonyos régiókban. Ha 1 CU-nál nagyobb fürtre van szüksége, küldhet egy Azure-támogatási kérelmet a fürt létrehozása után.

Ha az Azure Portal használatával fürtöt szeretne létrehozni az erőforráscsoportban, hajtsa végre az alábbi lépéseket:

1. Ezen [a hivatkozáson](https://aka.ms/eventhubsclusterquickstart) keresztül hozzon létre egy fürtöt az Azure Portalon. Ezzel szemben válassza a bal oldali navigációs ablak **Minden szolgáltatás elemét,** majd írja be a keresősávba az "Eseményközpontok fürtjei" kifejezést, és válassza az "Eseményközpontok fürtjei" lehetőséget a találatok listájából.
2. A **Fürt létrehozása** lapon konfigurálja a következőket:
    1. Adja meg **a fürt nevét**. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
    2. Válassza ki azt az **előfizetést,** amelyben létre szeretné hozni a fürtöt.
    3. Jelölje ki azt az **erőforráscsoportot,** amelyben a fürtöt létre kívánja hozni.
    4. Válassza ki a fürt **helyét.** Ha a kívánt régió szürkén jelenik meg, ideiglenesen kapacitáson kívül van, és [támogatási kérelmet](#submit-a-support-request) küldhet az Event Hubs csapatának.
    5. Válassza a **Tovább: Címkék** gombot a lap alján. Lehet, hogy néhány percet várnia kell, amíg a rendszer teljes mértékben kiépíti az erőforrásokat.

        ![Eseményközpontok létrehozása – Alapismeretek lap](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. A **Címkék** lapon állítsa be a következőket:
    1. Adja meg a hozzáadni kívánt címke **nevét** és **értékét.** Ez a lépés **nem kötelező**.  
    2. Válassza a **Véleményezés + Létrehozás** gombot.

        ![Eseményközpontok létrehozása – Címkék lap](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. A **Véleményezés + Létrehozás** lapon tekintse át a részleteket, és válassza a **Létrehozás gombot.** 

    ![Eseményközpontok létrehozása fürtlap – Véleményezés + Létrehozás lap](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Névtér és eseményközpont létrehozása fürtön belül

1. Ha egy fürtön belül szeretne névteret létrehozni, a fürt **Eseményközpont-fürtlapján** válassza a felső menü **+Névtér elemét.**

    ![Fürtkezelés lap – névtér hozzáadása gomb](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. A Névtér létrehozása lapon tegye a következő lépéseket:
    1. Adja meg **a névtér nevét.**  A rendszer ellenőrzi, hogy a név elérhető-e.
    2. A névtér a következő tulajdonságokat örökli:
        1. Előfizetés azonosítója
        2. Erőforráscsoport
        3. Hely
        4. Fürt neve
    3. A névtér létrehozásához válassza a **Létrehozás** gombot. Most már kezelheti a fürtöt.  

        ![Névtér létrehozása a fürtlapon](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. A névtér létrehozása után [létrehozhat egy eseményközpontot,](event-hubs-create.md#create-an-event-hub) ahogy általában egy névtérben is létrehozna egyet. 


## <a name="submit-a-support-request"></a>Támogatási kérelem benyújtása

Ha a létrehozás után módosítani szeretné a fürt méretét, vagy ha a kívánt régió nem érhető el, az alábbi lépésekkel nyújtson be támogatási kérelmet:

1. Az [Azure Portalon](https://portal.azure.com)válassza a **Súgó + támogatás** lehetőséget a bal oldali menüből.
2. Válassza a **+ Új támogatási kérelem lehetőséget** a Támogatás menüből.
3. A támogatási lapon hajtsa végre az alábbi lépéseket:
    1. A **Problématípus listában**válassza a **Műszaki** elemet a legördülő listából.
    2. Az **Előfizetés** beállításnál válassza ki az előfizetését.
    3. A **Szolgáltatás**esetében válassza a **Saját szolgáltatások**lehetőséget, majd az **Eseményközpontok**lehetőséget.
    4. Az **Erőforrás**területen válassza ki a fürtöt, ha már létezik, ellenkező esetben válassza az **Általános kérdés/Erőforrás nem érhető el**lehetőséget.
    5. A **Probléma típushoz**válassza **a Kvóta lehetőséget.**
    6. A **Probléma altípus**listában válasszon az alábbi értékek közül a legördülő listából:
        1. Válassza a **Dedikált termékváltozat kérése** lehetőséget, ha a funkciót a régióban támogatni szeretné.
        2. Válassza **a Dedikált fürt méretezése vagy leskálázása** lehetőséget, ha a dedikált fürt felskálázását vagy kicsinyítését szeretné felskálázni vagy csökkenteni. 
    7. A **Tárgy**esetében írja le a problémát.

        ![Támogatási jegy oldal](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Dedikált fürt törlése
 
1. A fürt törléséhez válassza a felső menü **Törlés parancsát.** Kérjük, vegye figyelembe, hogy a fürt a létrehozás után legalább 4 órányi használatot számlázunk. 
2. Megjelenik egy üzenet, amely megerősíti a fürt törlését.
3. Írja be **a fürt nevét,** és a Fürt törléséhez válassza a **Törlés** gombot.

    ![Fürtlap törlése](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott egy Eseményközpontok-fürtöt. Az események eseményközpontból történő küldésére és fogadására, valamint az események Azure-tárhelyre vagy Azure Data Lake Store-ba történő rögzítésére vonatkozó részletes útmutatásért tekintse meg az alábbi oktatóanyagokat:

- Események küldése és fogadása 
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
- [Az Azure Portal használata az Eseményközpontok rögzítésének engedélyezéséhez](event-hubs-capture-enable-through-portal.md)
- [Az Azure Event Hubs használata az Apache Kafka-hoz](event-hubs-for-kafka-ecosystem-overview.md)
