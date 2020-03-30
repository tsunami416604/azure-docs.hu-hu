---
title: 'Rövid útmutató: Hozzon létre egy Azure Data Explorer-fürt& DB'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Azure Data Explorer-fürtöt és -adatbázist, és töltheti fel adatokkal.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 07/22/2019
ms.openlocfilehash: ed0c570449a0c21e9eace1273228539db7c208da
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80238644"
---
# <a name="quickstart-create-an-azure-data-explorer-cluster-and-database"></a>Rövid útmutató: Azure Data Explorer-fürt és -adatbázis létrehozása

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [parancssori felület](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM sablon](create-cluster-database-resource-manager.md)


Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer használatához először egy fürtöt hozunk létre, majd egy vagy több adatbázist a fürtben. Ezután adatokat töltünk be az adatbázisba, hogy lekérdezéseket futtathassunk rajta. Ebben a rövid útmutatóban egy fürtöt és egy adatbázist hozunk létre.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="create-a-cluster"></a>Fürt létrehozása

Hozzon létre egy Azure Data Explorer-fürtegy meghatározott számítási és tárolási erőforrások egy Azure-erőforráscsoportban.

1. Válassza a **+ Erőforrás létrehozása** gombot a portál bal felső sarkában.

1. Keressen az *Azure Data Explorer* kifejezésre.

   ![Erőforrások keresése](media/create-cluster-database-portal/search-resources.png)

1. Az **Azure Data Explorer** területen, a képernyő alján válassza a **Létrehozás** elemet.

1. Töltse ki az alapvető fürt adatait a következő információkkal.

   ![Fürt létrehozása űrlap](media/create-cluster-database-portal/create-cluster-form2.png)

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Előfizetés | Az Ön előfizetése | Válassza ki a fürthöz használni kívánt Azure-előfizetést.|
    | Erőforráscsoport | Az erőforráscsoport | Használjon meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. |
    | Fürt neve | A fürt egyedi neve | Válasszon egy egyedi nevet a fürt azonosításához. A rendszer hozzáfűzi a *[régiónév].kusto.windows.net* tartománynevet a megadott fürtnévhez. A név csak kisbetűket és számokat tartalmazhat, 4 és 22 karakter között kell lennie.
    | Régió | *USA nyugati* vagy *usa nyugati 2* | Válassza az *USA nyugati* vagy az USA nyugati *felé 2* (ha rendelkezésre állási zónákat) lehetőséget ehhez a rövid útmutatóhoz. Éles üzemben az igényeinek leginkább megfelelő régiót válassza.
    | Rendelkezésre állási zónák | *1,* *2*és/vagy *3* | Helyezze el a fürtpéldányokat az ugyanabban a régióban található különböző rendelkezésre állási zónákban (nem kötelező). [Az Azure rendelkezésre állási zónái](/azure/availability-zones/az-overview) egyedi fizikai helyek ugyanabban az Azure-régióban. Egy Azure Data Explorer-fürt és az adatok részleges régióhiba elleni védelmét. A fürtcsomópontok alapértelmezés szerint ugyanabban az adatközpontban jönnek létre. Több rendelkezésre állási zóna kiválasztásával kiküszöbölheti az egyetlen meghibásodási pontot, és magas rendelkezésre állást biztosíthat. A rendelkezésre állási zónákba való telepítés csak a fürt létrehozása során lehetséges, és később nem módosítható.
    | Számítási specifikációk | *D13_v2* | Ehhez az útmutatóhoz válassza a legalacsonyabb díjszabást. Éles üzemben az igényeinek leginkább megfelelő díjszabást válassza.
    | | | |

1. Válassza **a Véleményezés + létrehozás** lehetőséget a fürt részleteinek áttekintéséhez, és a **Létrehozás lehetőséget** a fürt kiépítéséhez. Kiépítése általában körülbelül 10 percet vesz igénybe.

1. Amikor a központi telepítés befejeződött, válassza **az Ugrás az erőforrásra**lehetőséget.

    ![Erőforrás megnyitása](media/create-cluster-database-portal/notification-resource.png)

## <a name="create-a-database"></a>Adatbázis létrehozása

Most már készen áll a folyamat második lépésének, az adatbázis létrehozásának végrehajtására.

1. Az **Áttekintés** lapon válassza az **Adatbázis létrehozása** lehetőséget.

    ![2. lépés: adatbázis létrehozása](media/create-cluster-database-portal/database-creation.png)

1. Adja meg az alábbi adatokat az űrlapon.

    ![Adatbázis létrehozása űrlap](media/create-cluster-database-portal/create-database.png)

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Adatbázis neve | *TestDatabase* | Az adatbázis nevének egyedinek kell lennie a fürtön belül.
    | Megőrzési időszak | *3650* | Az az időtartam (napokban), amelyhez garantált, hogy az adatok lekérdezésre rendelkezésre állnak. Az időtartam az adatok betöltésének időpontjával kezdődik.
    | Gyorsítótárazási időszak | *31* | Az az időtartam (napokban), amelynél a gyakran lekérdezett adatok elérhetők maradnak az SSD-tárolóban vagy a RAM-ban, nem pedig a hosszabb távú tárolóban.
    | | | |

1. Az adatbázis létrehozásához válassza a **Létrehozás** gombot. Az adatbázis létrehozása általában kevesebb mint egy percet vesz igénybe. Amikor a folyamat befejeződött, a rendszer visszalép a fürt **Áttekintés** lapjára.

## <a name="run-basic-commands-in-the-database"></a>Alapvető parancsok futtatása az adatbázison

Most, hogy rendelkezik egy fürttel és egy adatbázissal, lekérdezéseket és parancsokat futtathat. Az adatbázisban még nincsenek adatok, mindazonáltal már áttekinthető az eszközök működése.

1. A fürt alatt válassza a **Lekérdezés** lehetőséget. Illessze `.show databases` be a parancsot a lekérdezési ablakba, majd válassza a **Futtatás lehetőséget.**

    ![Adatbázisparancsok megjelenítése](media/create-cluster-database-portal/show-databases.png)

    Az eredményhalmaz a **TestDatabase** találatot tartalmazza, amely az egyetlen adatbázis a fürtben.

1. Illessze `.show tables` be a parancsot a lekérdezési ablakba, és válassza a **Futtatás lehetőséget.**

    Ez a parancs egy üres eredményhalmazt ad vissza, mivel még nem rendelkezik táblákkal. A sorozat következő cikkében felveszünk egy táblát.

## <a name="stop-and-restart-the-cluster"></a>A fürt leállítása és újraindítása

A fürtöt az üzleti igényektől függően bármikor leállíthatja és újraindíthatja.

1. A fürt leállításához válassza az **Áttekintés** oldalon a **Leállítás** lehetőséget.

    Ha a fürt leállt, az adatok nem kérdezhetők le, és új adatok sem tölthetők be.

1. A fürt újraindításához válassza az **Áttekintés** oldalon az **Indítás** lehetőséget.

    A fürt újraindítása után körülbelül 10 percet vesz igénybe, amíg elérhetővé válik (például amikor eredetileg kiépítésre került). További időt vesz igénybe, mire az adatok a gyakori elérésű gyorsítótárba is betöltődnek.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha más rövid útmutatókat és oktatóanyagokat is követni kíván, tartsa meg a létrehozott erőforrásokat. Ellenkező esetben tisztítsa meg az erőforráscsoportot, hogy elkerülje a költségeket.

1. Az Azure Portalon válassza ki az **erőforráscsoportokat** a bal szélen, majd válassza ki az adatkezelő-fürtöt tartalmazó erőforráscsoportot.  

1. Válassza **az Erőforráscsoport törlése** lehetőséget a teljes erőforráscsoport törléséhez. Meglévő erőforráscsoport használata esetén csak az Adatkezelő-fürtet törölheti.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: Adatok betöltése az Event Hubsból az Azure Data Explorerbe](ingest-data-event-hub.md)


