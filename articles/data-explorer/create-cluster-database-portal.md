---
title: 'Gyors útmutató: Azure Adatkezelő-fürt létrehozása & DB-ben'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Azure Data Explorer-fürtöt és -adatbázist, és töltheti fel adatokkal.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 07/22/2019
ms.openlocfilehash: e97a712664a5864062fef2bba36dda76175af715
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199852"
---
# <a name="quickstart-create-an-azure-data-explorer-cluster-and-database"></a>Rövid útmutató: Azure Data Explorer-fürt és -adatbázis létrehozása

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Parancssori felület](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM-sablon](create-cluster-database-resource-manager.md)


Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Adatkezelő használatához először létre kell hoznia egy fürtöt, és létre kell hoznia egy vagy több adatbázist a fürtben. Ezután betöltheti az adatterhelést egy adatbázisba, így lekérdezéseket futtathat. Ebben a rövid útmutatóban egy fürtöt és egy adatbázist hozunk létre.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

## <a name="create-a-cluster"></a>Fürt létrehozása

Hozzon létre egy Azure Adatkezelő-fürtöt egy Azure-erőforráscsoport számítási és tárolási erőforrásainak meghatározott készletével.

1. A portál bal felső sarkában válassza az **Erőforrás létrehozása** (+) gombot.

1. Keressen az *Azure Data Explorer* kifejezésre.

   ![Erőforrások keresése](media/create-cluster-database-portal/search-resources.png)

1. Az **Azure Data Explorer** területen, a képernyő alján válassza a **Létrehozás** elemet.

1. Töltse ki az alapszintű fürt részleteit a következő információkkal.

   ![Fürt létrehozása űrlap](media/create-cluster-database-portal/create-cluster-form2.png)

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Előfizetés | Az Ön előfizetése | Válassza ki a fürthöz használni kívánt Azure-előfizetést.|
    | Erőforráscsoport | Az erőforráscsoport | Használjon meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. |
    | Fürt neve | A fürt egyedi neve | Válasszon egy egyedi nevet a fürt azonosításához. A rendszer hozzáfűzi a *[régiónév].kusto.windows.net* tartománynevet a megadott fürtnévhez. A név csak kisbetűket és számokat tartalmazhat, 4 – 22 karakterből kell állnia.
    | Régió | USA *nyugati* régiója vagy *USA 2. nyugati* régiója | Válassza az *USA nyugati* régiója vagy az *USA nyugati* régiója (ha rendelkezésre állási zónák használata) lehetőséget ehhez a rövid útmutatóhoz. Éles üzemben az igényeinek leginkább megfelelő régiót válassza.
    | Rendelkezésre állási zónák | *1*, *2*és/vagy *3* | Helyezze a fürt példányait a különböző rendelkezésre állási zónákba ugyanabban a régióban (opcionális). [Azure Availability Zones](/azure/availability-zones/az-overview) az azonos Azure-régióban található egyedi fizikai helyszínek. Egy Azure Adatkezelő-fürtöt és a részleges régió meghibásodásának adatait védik. A fürtcsomópontok alapértelmezés szerint ugyanabban az adatközpontban jönnek létre. Több rendelkezésre állási zóna kiválasztásával egyetlen meghibásodási pontot törölheti, és biztosíthatja a magas rendelkezésre állást. A rendelkezésre állási zónákra történő központi telepítés csak a fürt létrehozása során lehetséges, és később nem módosítható.
    | Számítási specifikációk | *D13_v2* | Ehhez az útmutatóhoz válassza a legalacsonyabb díjszabást. Éles üzemben az igényeinek leginkább megfelelő díjszabást válassza.
    | | | |

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a fürt adatainak áttekintéséhez, és **hozzon létre** a fürt kiépítéséhez. A kiépítés általában körülbelül 10 percet vesz igénybe.

1. Ha a telepítés befejeződött, válassza az **Ugrás erőforráshoz**lehetőséget.

    ![Erőforrás megnyitása](media/create-cluster-database-portal/notification-resource.png)

## <a name="create-a-database"></a>Adatbázis létrehozása

Most már készen áll a folyamat második lépésének, az adatbázis létrehozásának végrehajtására.

1. Az **Áttekintés** lapon válassza az **Adatbázis létrehozása** lehetőséget.

    ![2\. lépés: adatbázis létrehozása](media/create-cluster-database-portal/database-creation.png)

1. Adja meg az alábbi adatokat az űrlapon.

    ![Adatbázis létrehozása űrlap](media/create-cluster-database-portal/create-database.png)

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Adatbázis neve | *TestDatabase* | Az adatbázis nevének egyedinek kell lennie a fürtön belül.
    | Megőrzési időszak | *3650* | Az az időtartam (nap), ameddig garantált, hogy az adat a lekérdezés számára elérhető marad. Az időtartam az adatok betöltésének időpontjával kezdődik.
    | Gyorsítótárazási időszak | *31* | Az az időtartam (napban megadva), ameddig a gyakran lekérdezett adatmennyiséget SSD-tárolóban vagy RAM-ban szeretné tárolni, nem pedig hosszabb távú tárolás esetén.
    | | | |

1. A **Létrehozás** gombra kattintva hozza létre az adatbázist. Az adatbázis létrehozása általában kevesebb mint egy percet vesz igénybe. Amikor a folyamat befejeződött, a rendszer visszalép a fürt **Áttekintés** lapjára.

## <a name="run-basic-commands-in-the-database"></a>Alapvető parancsok futtatása az adatbázison

Most, hogy rendelkezik egy fürttel és egy adatbázissal, lekérdezéseket és parancsokat futtathat. Az adatbázisban még nincsenek adatok, mindazonáltal már áttekinthető az eszközök működése.

1. A fürt alatt válassza a **Lekérdezés** lehetőséget. Illessze be a parancsot `.show databases` a lekérdezési ablakba, majd válassza a **Futtatás**lehetőséget.

    ![Adatbázisparancsok megjelenítése](media/create-cluster-database-portal/show-databases.png)

    Az eredményhalmaz a **TestDatabase** találatot tartalmazza, amely az egyetlen adatbázis a fürtben.

1. Illessze be a parancsot `.show tables` a lekérdezési ablakba, és válassza a **Futtatás**lehetőséget.

    Ez a parancs egy üres eredményhalmazt ad vissza, mivel még nem rendelkezik táblákkal. A sorozat következő cikkében felveszünk egy táblát.

## <a name="stop-and-restart-the-cluster"></a>A fürt leállítása és újraindítása

A fürtöt az üzleti igényektől függően bármikor leállíthatja és újraindíthatja.

1. A fürt leállításához válassza az **Áttekintés** oldalon a **Leállítás** lehetőséget.

    Ha a fürt leállt, az adatok nem kérdezhetők le, és új adatok sem tölthetők be.

1. A fürt újraindításához válassza az **Áttekintés** oldalon az **Indítás** lehetőséget.

    A fürt újraindítása után körülbelül 10 percet vesz igénybe ahhoz, hogy elérhető legyen (például amikor eredetileg kiosztották). További időt vesz igénybe, mire az adatok a gyakori elérésű gyorsítótárba is betöltődnek.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha más rövid útmutatók és oktatóanyagok követését tervezi, tartsa meg a létrehozott erőforrásokat. Ellenkező esetben szüntesse meg az erőforráscsoport tisztítását, hogy elkerülje a költségek felmerülését.

1. A Azure Portal válassza ki a bal szélen az **erőforráscsoportok** elemet, majd válassza ki a adatkezelő fürtöt tartalmazó erőforráscsoportot.  

1. Válassza az **erőforráscsoport törlése** elemet a teljes erőforráscsoport törléséhez. Ha meglévő erőforráscsoportot használ, dönthet úgy, hogy csak a Adatkezelő-fürtöt törli.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: Adatok betöltése az Event Hubsból az Azure Data Explorerbe](ingest-data-event-hub.md)


