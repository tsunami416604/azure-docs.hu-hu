---
title: "Azure Search szolgáltatás létrehozása a portálon |} Microsoft Docs"
description: "A portál Azure Search szolgáltatás telepítéséhez."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: c8c88922-69aa-4099-b817-60f7b54e62df
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: heidist
ms.openlocfilehash: 58f4eab190e40e16ed261c165ffdfc8155eeb434
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Azure Search szolgáltatás létrehozása a portálon

Ez a cikk ismerteti, hogyan létrehozásához vagy a portál Azure Search szolgáltatás telepítéséhez. PowerShell útmutatásért lásd: [Azure Search kezelése a PowerShell-lel](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Előfizetés (ingyenes és fizetős)

[Nyisson meg egy ingyenes Azure-fiók](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) és szabad kreditek kipróbálhatja a fizetős Azure-szolgáltatásokhoz. Miután a jóváírásokat el is használta, megtarthatja a fiókot, és továbbra is használhatja az ingyenes Azure-szolgáltatások például webhelyek. A hitelkártya soha nem feladata, kivéve, ha explicit módon a beállítások módosításához és engedélyezéséhez.

Másik lehetőségként [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Az MSDN előfizetői biztosít Önnek krediteket havonta is használhatja a fizetős Azure-szolgáltatásokat. 

## <a name="find-azure-search"></a>Az Azure Search keresése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a plusz jelre ("+") a bal felső sarokban.
3. Válassza ki **Web + mobil** > **az Azure Search**.

![](./media/search-create-service-portal/find-search2.png)

## <a name="name-the-service-and-url-endpoint"></a>A szolgáltatás és az URL-végpontjának neve

A szolgáltatás nevét az URL-végpontjának szemben, amelyek az API-hívásokban kiállított részét képezi. Írja be a szolgáltatás nevét a **URL-cím** mező. 

Szolgáltatás vonatkozó követelményeknek:
   * 2-60 karakter hosszúságú
   * kisbetűk, számjegyek és kötőjelek ("-")
   * nincs a kötőjel ("-") a először 2 karakternél vagy utolsó egyetlen karakter
   * Nincs egymást követő kötőjeleket ("--")

## <a name="select-a-subscription"></a>Előfizetés kiválasztása
Ha egynél több előfizetéssel rendelkezik, válasszon egyet, adatok vagy a fájl tárolási szolgáltatások is vannak. Az Azure Search is automatikus észlelésű Azure Table és a Blob storage, az SQL Database és az Azure Cosmos DB használatával az indexelés *indexelők*, de csak a szolgáltatások ugyanazt az előfizetést.

## <a name="select-a-resource-group"></a>Erőforráscsoport kiválasztása
Erőforráscsoport gyűjteményei, Azure-szolgáltatások és erőforrások együtt használja őket. Például ha az Azure Search indexelheti az SQL-adatbázis használ, majd mindkét szolgáltatás részét kell képezniük ugyanabban az erőforráscsoportban.

> [!TIP]
> Erőforráscsoport törlésével a hozzá tartozó szolgáltatások is törli. Prototípus projektek több szolgáltatás használatával és az összes ugyanabban az erőforráscsoportban egyszerűbbé teszi a tisztítás követően a projekt. 

## <a name="select-a-hosting-location"></a>Adjon meg egy üzemeltetési helyet 
Az Azure-szolgáltatások, Azure Search a világ különböző adatközpontokban lehet üzemeltetni. Vegye figyelembe, hogy [árak eltérőek lehetnek](https://azure.microsoft.com/pricing/details/search/) földrajzi hely.

## <a name="select-a-pricing-tier-sku"></a>Válassza ki a tarifacsomagot (SKU)
[Az Azure Search jelenleg kínált több árképzési szinteket a](https://azure.microsoft.com/pricing/details/search/): szabad, alapszintű vagy Standard. Minden szinthez tartozik a saját [kapacitás és korlátai](search-limits-quotas-capacity.md). Lásd: [válasszon egy tarifacsomagot réteg vagy SKU](search-sku-tier.md) útmutatót.

Ebben a bemutatóban a Standard csomagot választott azt szolgáltatás.

## <a name="create-your-service"></a>A szolgáltatás létrehozása

Ne felejtse el egyszerűen hozzáférhetnek az irányítópultot a szolgáltatás rögzíti, amikor bejelentkezik.

![](./media/search-create-service-portal/new-service2.png)

## <a name="scale-your-service"></a>A szolgáltatás méretezésére
A szolgáltatás (15 perc vagy több, attól függően, hogy a réteg) létrehozásához néhány percet is igénybe vehet. A szolgáltatás üzembe helyezése után is méretezhető úgy, hogy az igényeinek. Azt választotta, a Standard csomagot az Azure Search szolgáltatás, mert a szolgáltatás is skálázható a két dimenziókban: replikák és a partíciók. Kellett alapszintű rétegben választása esetén csak adhat hozzá replikákat. Ha a szabad szolgáltatás kiépítése, a méretezési nem érhető el.

***Partíciók*** teszi lehetővé a szolgáltatásnak a tárolja, és keressen további dokumentumok keresztül.

***Replikák*** teszi lehetővé a szolgáltatásnak a keresési lekérdezések egy magasabb terhelés kezelésére.

> [!Important]
> Rendelkeznie kell egy szolgáltatás [írásvédett garantált szolgáltatási szintje 2 replikákat és a 3-replikáit az olvasási/írási SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Nyissa meg a search szolgáltatás paneljét az Azure portálon.
2. Jelölje ki a bal oldali navigációs sávon ablaktáblán **beállítások** > **méretezési**.
3. A slidebar használatával adhatja hozzá a replikák és a partíciók.

![](./media/search-create-service-portal/settings-scale.png)

> [!Note] 
> Minden szinthez tartozik különböző [korlátok](search-limits-quotas-capacity.md) engedélyezett egyetlen szolgáltatás a keresési egységek teljes száma (replikák * partíciók teljes keresési egység =).

## <a name="when-to-add-a-second-service"></a>Ha egy második szolgáltatás hozzáadása

Az ügyfelek többsége szolgáltatással egy olyan réteghez, amely üzembe a [jobb gombbal az erőforrások egyensúlyára](search-sku-tier.md). Egy szolgáltatás közölt tárolhatja, több index a [választja a réteg maximális korlátok](search-capacity-planning.md), az egyes index egymástól el vannak különítve egymástól. Az Azure Search kérelmek csak irányítható egy index, minimalizálja az esélye, más indexek ugyanazt a szolgáltatást a véletlen vagy szándékos adatok lekérését.

Bár a legtöbb felhasználó csak egy szolgáltatás használatához szolgáltatás redundancia lehet szükség, ha a működési követelmények a következők:

+ Vész-helyreállítási (az Adatközpont kimaradás). Az Azure Search nem biztosítanak az azonnali kimaradás esetén. Javaslatok és útmutatást: [felügyeleti szolgáltatás](search-manage.md).
+ A több-bérlős modellezési vizsgálata megállapította, hogy további szolgáltatások optimális tervét. További információkért lásd: [terv a több-bérlős](search-modeling-multitenant-saas-applications.md).
+ Globálisan központilag telepített alkalmazások esetén az Azure Search több régióba késés az alkalmazás nemzetközi forgalom csökkentése érdekében érdemes példánya lehet szükség.

> [!NOTE]
> Az Azure Search elkülönítse nem indexelési és lekérdező munkaterhelések; így akkor soha nem kell létrehoznia elkülönített munkaterhelések több szolgáltatás. Az index mindig lekérik a szolgáltatásban, amelyben az informatikai készült (nem lehet indexet létrehozni egy szolgáltatás és másolja azt egy másik).
>

Egy második szolgáltatás nincs szükség a magas rendelkezésre állás érdekében. Magas rendelkezésre állás a lekérdezések érhető el, ha a 2 vagy több replikákat az ugyanazt a szolgáltatást használ. A replika frissítései egymást követő, ami azt jelenti, hogy legalább egy akkor működik, ha a szolgáltatásfrissítés megkezdődött. Hasznos üzemidő kapcsolatos további információkért lásd: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Következő lépések
Egy Azure Search szolgáltatás kiépítését követően készen áll [index definiálása az](search-what-is-an-index.md) , töltse fel, és keresse meg az adatokat.

A szolgáltatás eléréséhez kódot vagy parancsprogramot, adja meg az URL-cím (*szolgáltatásnév*. search.windows.net) és a kulcsot. Adminisztrációs kulcsok teljes hozzáférést; lekérdezési kulcsok hozzáférést csak olvasható. Lásd: [használata Azure Search .NET](search-howto-dotnet-sdk.md) a kezdéshez.

Lásd: [felépítéséhez és az első index lekérdezése](search-get-started-portal.md) gyors portálalapú oktatóanyag.

