---
title: Új rugalmas készlet létrehozása az Azure Portalon | Microsoft Docs
description: A cikk ismerteti, hogyan adható méretezhető rugalmas adatbáziskészlet az SQL-adatbázis konfigurációjához, amely megkönnyíti a felügyeletet, valamint az erőforrások számos adatbázis közti megosztását.
keywords: méretezhető adatbázis,adatbázis-konfiguráció
services: sql-database
documentationcenter: ''
author: ninarn
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 07/20/2016
ms.author: ninarn
ms.workload: data-management
ms.topic: get-started-article
ms.tgt_pltfrm: NA

---
# Új rugalmas adatbáziskészlet létrehozása az Azure Portallal
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-elastic-pool-create-portal.md)
> * [PowerShell](sql-database-elastic-pool-create-powershell.md)
> * [C##](sql-database-elastic-pool-create-csharp.md)
> 
> 

Ebből a cikkből megtudhatja, hogyan hozhat létre méretezhető [rugalmas adatbáziskészletet](sql-database-elastic-pool.md) az [Azure Portallal](https://portal.azure.com/). Készlet két különböző módon hozható létre. Létrehozhatja a készletet a nulláról is, ha tisztában van a használni kívánt beállításokkal, de alapul veheti a szolgáltatás javaslatait is. Az SQL Database beépített funkciói képesek készletbeállítást javasolni, ha a rendszer úgy ítéli meg, hogy a múltbeli használat telemetriai adatai alapján ez az adatbázisok esetében használható legköltséghatékonyabb megoldás.

Egy kiszolgálóhoz több készlet adható, de egy készlethez nem adhatók különböző kiszolgálókról származó adatbázisok. Készlet létrehozásához egy legalább 12-es verziójú kiszolgálón üzemelő adatbázis szükséges. Ha még nem rendelkezik Azure SQL-adatbázissal, olvassa el a [Create your first Azure SQL database](sql-database-get-started.md) (Az első Azure SQL-adatbázis létrehozása) című cikket. Egyetlen adatbázissal is létrehozhatja a készletet, de a készletek több adatbázis használata esetén biztosítanak költséghatékony működést. További információk: [Price and performance considerations for an elastic database pool](sql-database-elastic-pool-guidance.md) (Rugalmas adatbáziskészletek ára és teljesítménye).

> [!NOTE]
> A rugalmas készletek minden Azure-régióban általánosan elérhetők, kivéve Nyugat-Indiát, ahol a szolgáltatás jelenleg előzetes verzióként érhető el.  A rugalmas készletek a lehető leghamarabb általánosan elérhetők lesznek ebben a régióban. 
> 
> 

## 1. lépés: Új készlet létrehozása
A cikk bemutatja, hogy miként lehet új készletet létrehozni a portál egy meglévő **kiszolgáló** paneljéről, amely a meglévő adatbázisok készletbe történő áthelyezésének legegyszerűbb módja. 

> [!NOTE]
> Függetlenül attól, hogy rendelkezik-e már kiszolgálóval, a **Rugalmas SQL-készletek** panelről is létrehozhat egy új készletet (a portál bal oldalán lévő lista alatt kattintson a **Tallózás** **>** **Rugalmas SQL-készletek** elemre). A **Rugalmas SQL-készletek** panel **+Hozzáadás** elemére kattintva megjelenítheti egy új kiszolgáló létrehozásának lépéseit a készletet érintő kiépítési munkafolyamat során.
> 
> 

1. A bal oldali lista alatti [Azure Portal](http://portal.azure.com/) területen kattintson a **Tallózás** **>** **SQL-kiszolgálók** elemre, majd kattintson a készlethez hozzáadni kívánt adatbázisokat tartalmazó kiszolgálóra.
2. Kattintson a **Új készlet** lehetőségre.
   
    ![Készlet hozzáadása a kiszolgálóhoz](./media/sql-database-elastic-pool-create-portal/new-pool.png)
   
    **-VAGY-**
   
    Elképzelhető, hogy egy üzenet jelenik meg, amely tájékoztatja, hogy a kiszolgálón javasolt rugalmas adatbáziskészletek érhetők el (csak 12-es verzió esetében). Kattintson az üzenetre a korábbi adatbázis-használat telemetriai adatai alapján javasolt készletek megtekintéséhez, majd kattintson a csomagra a további részletek megjelenítéséhez és a készlet testre szabásához. A javaslatokkal kapcsolatos további információkért olvassa el a témakör későbbi részében található [A készlettel kapcsolatos javaslatok megértése](#understand-pool-recommendations) című részt.
   
    ![javasolt készlet](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)
   
    Megjelenik az **Rugalmas adatbáziskészlet** panel, amely a készlet beállítására szolgál. Ha az előző lépésnél az **Új készlet** lehetőséget választotta, a portál a **Tarifacsomag** lehetőségnél a **Szabványos készlet** értéket választja, ad egy egyedi nevet a készletnek a **Név** mezőbe, és beállítja a készlet alapértelmezett konfigurációját. Ha úgy dönt, hogy a javasolt készletek egyikét használja, a rendszer kiválasztja Ön helyett a javasolt csomagot és készletkonfigurációt, de ezek is módosíthatók.
   
    ![A rugalmas készlet konfigurálása](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)
3. Adjon nevet a rugalmas készletnek, vagy hagyja meg az alapértelmezett nevet.

## 2. lépés: Tarifacsomag kiválasztása
A készlethez tartozó tarifacsomag határozza meg, hogy mely funkciók érhetők el a készlethez tartozó rugalmas adatbázisokban, illetve hogy az egyes adatbázisokon legfeljebb hány eDTU (eDTU MAX) és mennyi tárterület (GB) használható fel. A részletekért lásd a tarifacsomagokról szóló cikket.

A készlet tarifacsomagjának módosításához kattintson a **Tarifacsomag** lehetőségre, majd a kívánt tarifacsomagra, azután kattintson a **Kiválasztás** elemre.

> [!IMPORTANT]
> Miután kiválasztotta a tarifacsomagot, és az **OK** gombra kattintva mentette a módosításokat az utolsó lépésnél, már nem fogja tudni megváltoztatni a készlet tarifacsomagját. Meglévő rugalmas készlet tarifacsomagjának módosításához hozzon létre egy új rugalmas készletet, állítsa be a kívánt tarifacsomagot, majd telepítse át a rugalmas adatbázisokat az új készletbe.
> 
> 

![Tarifacsomag kiválasztása](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

## 3. lépés: A készlet konfigurálása
A tarifacsomag beállítását követően kattintson a Készlet beállítása elemre. A megnyíló lapon vehet fel adatbázisokat, állíthatja be a készlethez tartozó eDTU-kat és a tárterület mennyiségét (GB-ban), és állíthatja be a készlethez tartozó rugalmas adatbázisokban használt eDTU-k minimális és maximális számát.

1. Kattintson a **Készlet beállítása** elemre.
2. Válassza ki a készletbe felvenni kívánt adatbázisokat. Ezt a lépést nem kötelező a készlet létrehozása során elvégezni. Adatbázisokat a készlet létrehozását követően is fel lehet venni.
    Az adatbázisok hozzáadásához kattintson az **Adatbázis hozzáadása** gombra, kattintson a felvenni kívánt adatbázisokra, majd a **Kiválasztás** gombra.
   
    ![Adatbázisok hozzáadása](./media/sql-database-elastic-pool-create-portal/add-databases.png)
   
    Ha a felvenni kívánt adatbázisokhoz elegendő korábbi használati telemetriai adat áll rendelkezésre, a rendszer frissíti az **Estimated eDTU and GB usage** (Becsült eDTU- és GB-használat) diagramot és az **Actual eDTU usage** (Tényleges eDTU-használat) sávdiagramot, amelyek segítenek Önnek meghozni a konfigurációval kapcsolatos döntéseket. Ezenfelül egyes esetekben a szolgáltatás javaslatot tartalmazó üzenetet is megjelenít, amely segít a készlet megfelelő méretének kiválasztásában. Lásd: [Dinamikus javaslatok](#dynamic-recommendations).
3. A **Készlet beállítása** lapon elérhető vezérlők segítségével áttekintheti a beállításokat, és konfigurálhatja a készletet. Az egyes tarifacsomagok korlátaival kapcsolatban az [Elastic pools limits](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases) (A rugalmas készlet korlátai) című cikk nyújt tájékoztatást, a készlet megfelelő méretének kiválasztását a következő cikk ismerteti: [Price and performance considerations for elastic database pools](sql-database-elastic-pool-guidance.md) (Rugalmas adatbáziskészletek ára és teljesítménye). A készlet beállításaival kapcsolatos további információkért lásd: [Elastic database pool properties](sql-database-elastic-pool.md#elastic-database-pool-properties) (A rugalmas adatbáziskészlet tulajdonságai).
   
    ![A rugalmas készlet konfigurálása](./media/sql-database-elastic-pool-create-portal/configure-performance.png)
4. Ha módosította a beállításokat, kattintson a **Készlet beállítása** panel **Kiválasztás** elemére.
5. A készlet létrehozásához kattintson az **OK** gombra.

## A készlettel kapcsolatos javaslatok megértése
Az SQL Database szolgáltatás a használati előzmények elemzésével megállapítja, hogy megéri-e önálló adatbázisok helyett készleteket használni, és ha igen, javasol egy vagy több készletet. A javaslatokat a rendszer a kiszolgáló adatbázisainak a készlethez leginkább illő egyedi részhalmazával konfigurálja.

![javasolt készlet](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)  

A készletjavaslat a következőkből áll:

* A készletre vonatkozó tarifacsomag (Alapszintű, Standard vagy Prémium)
* A megfelelő **POOL eDTU** száma (amelyet készletenkénti maximális eDTU-ként is meg lehet határozni)
* Az adatbázisonkénti **eDTU MAX** és **eDTU Min** érték
* A készletbe javasolt adatbázisok listája

A szolgáltatás az elmúlt 30 nap telemetriai adatai alapján javasol készleteket. A rendszer csak a legalább már 7 napja létező adatbázisokat vonja be a rugalmas adatbáziskészlet létrehozására irányuló javaslatokba. Azokat az adatbázisokat, amelyeket korábban már elhelyezett egy másik rugalmas adatbáziskészletben, a rendszer nem javasolja újabb adatbáziskészletbe való bevonásra.

A szolgáltatás értékeli az erőforrásigényeket, illetve azt, hogy megéri-e a különböző csomagokhoz tartozó önálló adatbázisokat ugyanahhoz a csomaghoz tartozó készletekbe vonni. A rendszer például megvizsgálja, hogy érdemes-e a kiszolgálón található Standard adatbázisokat Standard rugalmas készletté alakítani. Ez azt is jelenti, hogy a szolgáltatás különböző csomagokat tartalmazó javaslatokat nem tesz, azaz soha nem javasolja például, hogy Prémium készletbe helyezzen egy Standard adatbázist.

### Dinamikus javaslatok
Miután hozzáadta az adatbázisokat a készlethez, a rendszer a kiválasztott adatbázisok korábbi használati adatai alapján automatikusan létrehozza a javaslatokat. Ezek a javaslatok megjelennek az eDTU- és GB-használati diagramon, illetve a **Készlet beállítása** panel felső részén található javaslatsávban is. A javaslatok célja, hogy segítsenek az Ön konkrét adatbázisaihoz optimalizált készlet létrehozásában.

![dinamikus javaslatok](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## További források
* [Manage a SQL Database elastic pool with the portal (SQL Database rugalmas készlet felügyelete a portálon)](sql-database-elastic-pool-manage-portal.md)
* [Manage a SQL Database elastic pool with PowerShell (SQL Database rugalmas készlet felügyelete PowerShell-lel)](sql-database-elastic-pool-manage-powershell.md)
* [Manage a SQL Database elastic pool with C (SQL Database rugalmas készlet felügyelete C-vel)#](sql-database-elastic-pool-manage-csharp.md)
* [Scaling out with Azure SQL Database (Horizontális felskálázás az Azure SQL Database segítségével)](sql-database-elastic-scale-introduction.md) 

<!--HONumber=Sep16_HO5-->


