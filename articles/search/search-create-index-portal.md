---
title: "(Portál – Azure Search) index létrehozása |} Microsoft Docs"
description: "Az Azure portál használatával index létrehozása."
services: search
manager: jhubbard
author: heidisteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/20/2017
ms.author: heidist
ms.openlocfilehash: a7d98ab0937a7d3f932d5df34c19ae091129804e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Az Azure portál használata az Azure Search-index létrehozása
> [!div class="op_single_selector"]
> * [Áttekintés](search-what-is-an-index.md)
> * [Portál](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

A beépített index-tervező használata Azure-portálon prototípusként vagy hozzon létre egy [search-index](search-what-is-an-index.md) az Azure Search szolgáltatás futtatásához. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy egy [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) és [Azure Search szolgáltatás](search-create-service-portal.md).  

## <a name="find-your-search-service"></a>Keresse meg a keresési szolgáltatáshoz
1. Jelentkezzen be az Azure portálon, és tekintse át a [keresési az előfizetéshez tartozó szolgáltatások](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Válassza ki az Azure Search szolgáltatást.

## <a name="name-the-index"></a>Az index neve

1. Kattintson a **index hozzáadása** gombra a parancssávon az oldal tetején.
2. Nevezze el az Azure Search-index. 
   * Betűvel kezdődhet.
   * Csak kisbetűk, számjegyek és kötőjelek használja ("-").
   * Korlátozza a nevét, 60 karakter hosszúságú lehet.

  Az index neve kapcsolatok használata esetén az index és az Azure Search REST API a HTTP-kérelmek küldéséhez használt végponti URL-Címének részévé válik.

## <a name="define-the-fields-of-your-index"></a>Az index mezők megadása

Index összeállítás tartalmaz egy *gyűjtemény mezők* a kereshető adatokat, amely meghatározza az indexben. Pontosabban adja meg azt jelzi, hogy külön-külön feltöltése a struktúra. A mezők gyűjteménybe szükséges és választható mezőket, és a adta-e, annak meghatározásához, hogy miként legyen használható a mező index attribútumokkal.

1. A a **Index hozzáadása** panelen kattintson a **mezők >** diák nyitott a mező definition panel. 

2. Fogadja el a létrehozott *kulcs* Edm.String típusú mező. Alapértelmezés szerint a mező neve *azonosító* azonban mindaddig, amíg a karakterlánc megfelel a átnevezheti [elnevezési szabályait](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). A következő kulcsmező minden Azure Search-index esetén kötelező, és karakterláncnak kell lennie.

3. Adja hozzá a mezőt fel kell töltenie a dokumentumok teljes megadásához. Ha dokumentumok alkotják egy *azonosító*, *Szálloda neve*, *cím*, *Város*, és *régió*, hozzon létre egy megfelelő mező mindegyiknél az index. Tekintse át a [kialakítási útmutató az alábbi részben](#design) segítségre attribútum.

4. Opcionálisan adja hozzá a mezőket használt belső szűrőkifejezéseket. Zárja ki a mezőket a keresési műveletek attribútumok használatát a mezőben állítható be.

5. Ha befejezte, kattintson a **OK** mentéséhez, és hozza létre az indexet.

## <a name="tips-for-adding-fields"></a>Tippek a mezők hozzáadása

Az index létrehozása a portálon intenzív billentyűzet. Ez a munkafolyamat következő minimalizálása érdekében a lépéseket:

1. Először is hozhat létre a mezőlista nevet, és az adattípus beállítása.

2. Ezután használja a jelölőnégyzeteket minden tetején attribútumot tömeges engedélyezi a beállítást, minden mezőbe, és szelektív módon törölheti a kevés mezőből áll, amelyek nem igényelnek jelölőnégyzetéből. Például karakterlánc mező kitöltése általában kereshetők. Így előfordulhat, hogy kattintson **lekérhető** és **kereshető** mindkét adja vissza a mező értékének a találatok között, valamint a mező a teljes szöveges keresés engedélyezése. 

<a name="design"></a>
## <a name="design-guidance-for-setting-attributes"></a>Tervezési útmutatást nyújt attribútumainak beállítása

Habár bármikor hozzáadhat új mezőket, meglévő mező definíciók zárolta élettartamát az index. Emiatt fejlesztők általában használni a portál egyszerű indexek létrehozása, ötleteket tesztelési vagy a portál lapjai használatával kereshet meg egy beállítást. Egy index terv keresztül gyakori iteráció egy sokkal hatékonyabb, ha a kódalapú megközelítés követi, hogy az index könnyedén létre újra.

Elemzőkkel és a javaslattevők társítva a mezőket, az index mentése előtt. Ne kattintson végig lapokra nyelvi elemzőkkel és a javaslattevők hozzáadását az index definícióját.

A karakterlánc típusú gyakran fel van tüntetve **kereshető** és **lekérhető**.

Keresési eredmények szűkítéséhez használt mezőkkel következők **rendezhető**, **Filterable**, és **kategorizálható**.

A mező attribútumok határozza meg, hogyan mező használata esetén például, hogy használatba a teljes szöveges keresés, jellemzőalapú navigációs, rendezési műveletet, és így tovább. A következő táblázat ismerteti az összes attribútumot.

|Attribútum|Leírás|  
|---------------|-----------------|  
|**kereshető**|Teljes szöveges kereshető, például szóhatároló indexelés során lexikális elemzés vonatkoznak. Ha például a "napfényes" értékre van beállítva kereshető mezőt, belső azt lesznek osztva az egyes jogkivonatok "moziba" és "day". További információkért lásd: [hogyan teljes szöveges keresés works](search-lucene-query-architecture.md).|  
|**szűrhető**|A hivatkozott **$filter** lekérdezések. Típusú szűrhető mezők `Edm.String` vagy `Collection(Edm.String)` nem kerülnek szóhatároló, így csak pontosan megegyezik az összehasonlítást. Például, ha a mező f "moziba Day" `$filter=f eq 'sunny'` megkeresi, nincs találat, de `$filter=f eq 'sunny day'` lesz. |  
|**rendezhető**|Alapértelmezés szerint a rendszer rendezése az eredmények pontszám, de be lehet állítani a rendezési a a dokumentumokban lévő mezők alapján. Típusú mezők `Collection(Edm.String)` nem lehet **rendezhető**. |  
|**kategorizálható**|A keresési eredmények kategória (például egy adott városban szállodák) találati számát tartalmazó bemutató jellemzően használt. Ez a beállítás nem használható típusú mezők `Edm.GeographyPoint`. Típusú mezők `Edm.String` , amelyek **szűrhető**, **rendezhető**, vagy **kategorizálható** legfeljebb 32 kilobájt hosszabb használható. További információkért lásd: [a Create Index (REST API-t)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**kulcs**|Az index dokumentumokon egyedi azonosítója. Pontosan egy mezőt ki kell választani, a következő kulcsmező és típusúnak kell lennie `Edm.String`.|  
|**lekérhető**|Meghatározza, hogy a mező a keresési eredmény adhatók vissza. Ez akkor hasznos, ha egy mező használni kívánt (például *nyereség margó*) szűrőként, rendezés, vagy pontozási mechanizmus, de nem szeretné, hogy a végfelhasználók számára megjelenített a mező. Ennek az attribútumnak kell lennie `true` a `key` mezőket.|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>Példa API szakaszokban használt szállodák index létrehozása

Az Azure Search API dokumentációja tartalmaz egy egyszerű kiemeli kódpéldák *szállodák* index. Az alábbi képernyőfelvételnek tekintse meg az index definícióját, beleértve az index definícióját, során megadott francia nyelvű analyzer amely létrehozhatja a gyakorlat során a portálon.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>További lépések

Miután létrehozta az Azure Search-index, áthelyezheti a következő lépéssel: [kereshető adatokat feltölteni az indexbe](search-what-is-data-import.md).

Másik lehetőségként is készíthet, indexek mélyebb betekintést. Mellett a mezők gyűjteményében index azt is meghatározza, elemzőkkel, a javaslattevők, a pontozási profil és a CORS-beállítások. A portál lapokra lapokat biztosít a leggyakrabban használt elemek meghatározásához: mezők, lekérdezések és javaslattevők. Hozzon létre, és más elemek módosítása, használhatja a REST API-t vagy a .NET SDK-val.

## <a name="see-also"></a>Lásd még

 [A teljes szöveges keresés működése](search-lucene-query-architecture.md)  
 [Keresési szolgáltatás REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK-val](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

