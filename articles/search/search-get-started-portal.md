---
title: "Bevezetés az Azure Search használatába | Microsoft Docs"
description: "Ismerje meg, hogyan hozhatja létre első Azure Search-indexét a bemutató oktatóanyag és a DocumentDB-mintaadatok felhasználásával. Ez a portálalapú, kódmentes gyakorlat az Adatok importálása varázslót használja."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 21adc351-69bb-4a39-bc59-598c60c8f958
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 10/03/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: dddbcbcd82900d7537c2d60631cc1753554d9486


---
# <a name="get-started-with-azure-search-in-the-portal"></a>Bevezetés az Azure Search használatába a portálon
Ez a kódmentes bevezető közvetlenül a portálba épített funkciók segítségével bemutatja Önnek a Microsoft Azure Search használatát. 

Az oktatóanyag feltételezi, hogy Ön rendelkezik egy [minta Azure DocumentDB-adatbázissal](#apdx-sampledata), amelyet az adataink és utasításaink segítségével egyszerűen létrehozhat, de ugyanezeket a lépéseket alkalmazhatja a DocumentDB vagy SQL-adatbázisban már meglévő saját adataira is.

> [!NOTE]
> Az első lépéseket bemutató oktatóanyaghoz szükség van egy [Azure-előfizetésre](/pricing/free-trial/?WT.mc_id=A261C142F) és egy [Azure Search szolgáltatásra](search-create-service-portal.md). 
> 
> 

## <a name="find-your-service"></a>A szolgáltatása megkeresése
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Nyissa meg az Azure Search szolgáltatás irányítópultját. Íme néhány módszer az irányítópult megkereséséhez.
   
   * Az ugrósávon kattintson a **Keresési szolgáltatások** elemre. Az ugrósáv felsorolja az előfizetésében biztosított összes szolgáltatást. Ha definiálva van egy keresési szolgáltatás, a listában megjelenik a **Keresési szolgáltatások** elem.
   * Az ugrósávon kattintson **Tallózás** elemre, majd a majd keresőmezőbe írja be a „search” kifejezést, hogy elkészüljön az előfizetéseiben létrehozott összes keresőszolgáltatást tartalmazó lista.

## <a name="check-for-space"></a>Szabad terület ellenőrzése
Sok ügyfél az ingyenes szolgáltatással kezdi. Ez a verzió három indexre, három adatforrásra és három indexelőre korlátozódik. Mielőtt hozzákezdene, ellenőrizze, hogy elegendő hellyel rendelkezik-e további elemek számára. Ez a bemutató minden objektumból egyet hoz létre.

## <a name="create-an-index-and-load-data"></a>Index létrehozása és az adatok betöltése
A keresési lekérdezések egy *index* segítségével ismétlődnek, amely kereshető adatokat, metaadatokat és bizonyos keresési viselkedések optimalizálásához használt szerkezeteket tartalmaz. Első lépésként határozzon meg és töltsön fel egy indexet.

Az index létrehozásának számos módja van. Ha az adatai az Azure Search által bejárható tárolóban, például az Azure SQL-adatbázisban, egy Azure virtuális gépen lévő SQL Server kiszolgálón vagy egy DocumentDB adatbázisban vannak, egy *indexelő* segítségével nagyon egyszerűen hozhat létre és tölthet fel indexet.

Ahhoz, hogy ez a feladat portálalapú maradjon, olyan DocumentDB-adatbázisból származó adatokat használunk, amely egy indexelővel az **Adatok importálása** varázsló segítségével bejárható. 

A folytatás előtt hozzon létre egy, az oktatóanyaghoz használható [minta DocumentDB-adatbázist](#apdx-sampledata), majd térjen vissza ehhez a szakaszhoz, és hajtsa végre az alábbi lépéseket.

<a id="defineDS"></a>

#### <a name="step-1-define-the-data-source"></a>1. lépés: Az adatforrás meghatározása
1. Az Azure Search szolgáltatás irányítópultján kattintson az **Adatok importálása** parancsra a parancssávon az indexet létrehozó és feltöltő varázsló elindításához.
   
    ![][7]
2. A varázslóban kattintson az **Adatforrás** > **DocumentDB** > **Név** elemre, majd írja be az adatforrás nevét. Az adatforrás egy olyan kapcsolatobjektum az Azure Search szolgáltatásban, amelyet más indexelők is használhatnak. Miután létrehozta, „meglévő adatforrásként” elérhetővé válik a szolgáltatásában.
3. Válassza ki meglévő DocumentDB-fiókját, az adatbázist és a gyűjteményt. Ha az általunk megadott mintaadatokat használja, az adatforrása definíciója a következőképpen néz ki:
   
    ![][2]

Figyelje meg, hogy kihagyjuk a lekérdezést. Ennek az oka az, hogy ezúttal nem végezzük el az adatkészletünk változáskövetését. Ha az adatkészlete olyan mezőt tartalmaz, amely nyomon követi a rekordok frissítését, akkor konfigurálhat egy Azure Search-indexelőt, és a változáskövetés segítségével nyomon követheti az indexe szelektív frissítéseit.

A varázsló ezen lépésének végrehajtásához kattintson az **OK** gombra.

#### <a name="step-2-define-the-index"></a>2. lépés: Az index meghatározása
Még mindig a varázslóban, kattintson az **Index** elemre, és tekintse meg az Azure Search-index létrehozásához használt tervezési felületet. Az indexnek minimálisan egy névre és egy mezőgyűjteményre van szüksége, és egy mezőt dokumentumkulcsként kell megjelölni. Mivel mi DocumentDB-adatkészletet használunk, a varázsló automatikusan észleli a mezőket, és előre feltölti az indexet mezőkkel és adattípus-hozzárendelésekkel. 

  ![][3]

Habár a mezők és adattípusok konfigurálva vannak, az attribútumok hozzárendelését Önnek kell elvégeznie. A mezőlista tetején található jelölőnégyzetek *indexattribútumok*, amelyek a mezők használatát vezérlik. 

* **Lekérhető**: azt jelenti, hogy a mező a keresési eredmények listájában jelenik meg. A jelölőnégyzet törlésével az egyes mezőket a keresési eredmények korlátjain kívül esőként jelölheti meg, például amikor a mezőket csak szűrőkifejezésekben használják. 
* **Szűrhető**, **Rendezhető** és **Kategorizálható**: azt határozzák meg, hogy egy mező használható-e szűrésben, rendezésben vagy jellemzőalapú navigációs szerkezetben. 
* **Kereshető**: azt jelenti, hogy a mező szerepel a teljes szöveges keresésben. A karakterláncok általában kereshetők. A numerikus és logikai mezőket gyakran nem kereshetőként jelölik meg. 

Mielőtt kilép az oldalról, jelölje meg az indexében lévő mezőket a következő beállításokkal (Lekérhető, Kereshető és így tovább). A legtöbb mező Lekérhető. A legtöbb karakterláncmező Kereshető (a kulcsot nem kell kereshetővé tenni). Néhány mező – például a genre, az orderableOnline, a rating és a tags – Szűrhető, Rendezhető és Kategorizálható is. 

| Mező | Típus | Beállítások |
| --- | --- | --- |
| id |Edm.String | |
| albumTitle |Edm.String |Lekérhető, Kereshető |
| albumUrl |Edm.String |Lekérhető, Kereshető |
| genre |Edm.String |Lekérhető, Kereshető, Szűrhető, Rendezhető, Kategorizálható |
| genreDescription |Edm.String |Lekérhető, Kereshető |
| artistName |Edm.String |Lekérhető, Kereshető |
| orderableOnline |Edm.Boolean |Lekérhető, Szűrhető, Rendezhető, Kategorizálható |
| tags |Collection(Edm.String) |Lekérhető, Szűrhető, Kategorizálható |
| price |Edm.Double |Lekérhető, Szűrhető, Kategorizálható |
| margin |Edm.Int32 | |
| rating |Edm.Int32 |Lekérhető, Szűrhető, Rendezhető, Kategorizálható |
| inventory |Edm.Int32 |Lekérhető |
| lastUpdated |Edm.DateTimeOffset | |

Összehasonlítási pontként az alábbi képernyőfelvétel egy, az előző táblázatban szereplő specifikáció alapján létrehozott indexet ábrázol.

 ![][4]

A varázsló ezen lépésének végrehajtásához kattintson az **OK** gombra.

#### <a name="step-3-define-the-indexer"></a>3. lépés: Az indexelő meghatározása
Még mindig az **Adatok importálása** varázslóban, kattintson az **Indexelő** > **Név** elemre, írja be az indexelő nevét, és az összes többi értékhez használja az alapértelmezett értékeket. Ez az objektum egy végrehajtható folyamatot határoz meg. Miután létrehozta, ismétlődő ütemezésbe helyezheti, de most használja az alapértelmezett beállítást, vagyis futtassa le egyszer, azonnal az indexelőt, amikor rákattint az **OK** gombra. 

Az importálandó adatbejegyzései legyenek mind kitöltve és álljanak készen a feldolgozásra.

  ![][5]

A varázsló futtatásához kattintson az **OK** gombra az importálás elindításához és a varázsló bezárásához.

## <a name="check-progress"></a>Folyamat állapotának ellenőrzése
A folyamat állapotának ellenőrzéséhez lépjen vissza a szolgáltatás irányítópultjára, görgessen lefelé, majd az indexelők listájának megnyitásához kattintson duplán az **Indexelő** csempére. Meg kell jelennie a listában az éppen létrehozott indexelőnek, a „folyamatban” vagy sikeres jelzésű állapotnak, valamint az Azure Search szolgáltatásba indexelt dokumentumok számának.

  ![][6]

## <a name="query-the-index"></a>Az index lekérdezése
Most már rendelkezik egy keresési indexszel, amely készen áll a lekérdezésre. 

A **Keresési ablak** a portálba épített lekérdezési eszköz. Egy keresőmezőt tartalmaz, ahol ellenőrizheti, hogy a megadott keresés a várt adatokat adja-e vissza. 

1. A parancssávon kattintson a **Keresési ablak** elemre.
2. Figyelje meg, hogy melyik index aktív. Ha nem az, amelyiket most hozott létre, kattintson a parancssávon az **Index módosítása** elemre, és jelölje ki a megfelelőt.
3. Hagyja üresen a keresőmezőt, majd kattintson a **Keresés** gombra olyan helyettesítő karakteres keresés végrehajtásához, amely visszaadja az összes dokumentumot.
4. Adjon meg néhány teljes szöveges keresési lekérdezést. Megtekintheti a helyettesítő karakteres keresés eredményeit, és megismerheti a lekérdezendő előadókat, albumokat és műfajokat.
5. A [jelen cikk végén bemutatott példák](https://msdn.microsoft.com/library/azure/dn798927.aspx) segítségével más lekérdezési szintaxisokat és ötleteket is kipróbálhat, és úgy módosíthatja a lekérdezést, hogy olyan keresési karakterláncokat használjon, amelyek valószínűleg megtalálhatók az indexében.

## <a name="next-steps"></a>Következő lépések
Miután egyszer lefuttatta a varázslót, visszatérhet és megtekintheti vagy módosíthatja az egyes összetevőket: az indexet, az indexelőt és az adatforrást. Bizonyos szerkesztések, például a mező adattípusának módosítása nem engedélyezett az indexben, de a legtöbb tulajdonságok és beállítás módosítható. Az egyes összetevők megtekintéséhez kattintson az **Index**, **Indexelő** vagy **Adatforrások** csempére az irányítópulton a meglévő objektumok listájának megjelenítéséhez.

A cikkben említett egyéb funkciókkal kapcsolatos további tudnivalókért látogasson el a következő hivatkozásokra:

* [Indexelők](search-indexer-overview.md)
* [Index létrehozása (az index attribútumainak részletes leírását tartalmazza)](https://msdn.microsoft.com/library/azure/dn798941.aspx)
* [Keresési ablak](search-explorer.md)
* [Dokumentumok keresése (a lekérdezési szintaxisra vonatkozó példákat tartalmaz)](https://msdn.microsoft.com/library/azure/dn798927.aspx)

Ugyanezt a munkafolyamatot kipróbálhatja az Adatok importálása varázsló segítségével más adatforrások esetében is, mint például az Azure SQL Database vagy az SQL Server az Azure virtuális gépeken.

> [!NOTE]
> Már bejelentették az Azure Blob Storage bejárására vonatkozó indexelőtámogatást, de ez a funkció előzetes állapotban van, még nem áll rendelkezésre a portálon. Az indexelő kipróbálásához kódot kell írnia. A további információkat lásd: [Az Azure Blob Storage indexelése az Azure Search szolgáltatásban](search-howto-indexing-azure-blob-storage.md).
> <a id="apdx-sampledata"></a>
> 
> 

## <a name="appendix-create-sample-data-in-documentdb"></a>Függelék: Mintaadatok létrehozása a DocumentDB-ben
Ez a szakasz egy kis adatbázist hoz létre a DocumentDB használatával, amelynek a segítségével végrehajthatók az oktatóanyagban szereplő feladatok.

Az alábbi utasítások általános útmutatást nyújtanak, de nem adnak teljes körű tájékoztatást. Ha további segítségre lenne szüksége a DocumentDB portálnavigációjával vagy feladataival kapcsolatban, olvassa el a DocumentDB dokumentációját, azonban a legtöbb parancs, amelyre szüksége van, vagy az irányítópult tetején lévő szolgáltatási parancssávon, vagy az adatbázis paneljén található. 

  ![][1]

### <a name="create-musicstoredb-for-this-tutorial"></a>musicstoredb létrehozása az oktatóanyaghoz
1. [Kattintson ide](https://github.com/HeidiSteen/azure-search-get-started-sample-data) a zeneáruház JSON-adatfájljait tartalmazó ZIP-fájl letöltéséhez. Ehhez az adatkészlethez 246 JSON-dokumentumot biztosítunk.
2. Adja hozzá a DocumentDB-t az előfizetéséhez, majd nyissa meg a szolgáltatás irányítópultját.
3. Kattintson az **Adatbázis hozzáadása** gombra, és hozzon létre egy új, `musicstoredb` azonosítójú adatbázist. A létrehozását követően megjelenik az adatbáziscsempén, az oldal alján.
4. Az adatbázis paneljének megnyitásához kattintson az adatbázis nevére.
5. Kattintson a **Gyűjtemény hozzáadása** gombra, és hozzon létre egy `musicstorecoll` azonosítójú gyűjteményt.
6. Kattintson a **Dokumentumkezelő** elemre.
7. Kattintson a **Feltöltés** gombra.
8. A **Dokumentum feltöltése** panelen keresse meg azt a helyi mappát, amely a korábban letöltött JSON-fájlokat tartalmazza. Válassza ki a JSON-fájlokat 100-as vagy kisebb kötegekben.
   * 386.json
   * 387.json
   * . . .
   * 486.json
9. Ismételje meg a következő fájlköteg beolvasását mindaddig, amíg az utolsót (669.json) fel nem töltötte.
10. Kattintson a **Lekérdezéskezelő** elemre, és ellenőrizze, hogy az adatok feltöltése a Dokumentumkezelő követelményeinek megfelelően történt-e.

Ez egyszerűen elvégezhető az alapértelmezett lekérdezéssel, amelyet azonban úgy is módosíthat, hogy az első 300 elemet választja ki (ez az adatkészlet kevesebb mint 300 elemet tartalmaz).

JSON kimeneti adatokat kell visszakapnia, a 386. számú dokumentummal kezdődően és a 669. számú dokumentummal végződően. Az adatok feltöltése után [visszatérhet a bemutató lépéseihez](#defineDS), és az **Adatok importálása varázslóval** létrehozhat egy indexet.

<!--Image references-->
[1]: ./media/search-get-started-portal/AzureSearch-GetStart-Docdbmenu1.png
[2]: ./media/search-get-started-portal/AzureSearch-GetStart-DataSource.png
[3]: ./media/search-get-started-portal/AzureSearch-GetStart-DefaultIndex.png
[4]: ./media/search-get-started-portal/AzureSearch-GetStart-FinishedIndex.png
[5]: ./media/search-get-started-portal/AzureSearch-GetStart-ImportReady.png
[6]: ./media/search-get-started-portal/AzureSearch-GetStart-IndexerList.png
[7]: ./media/search-get-started-portal/search-data-import-wiz-btn.png



<!--HONumber=Nov16_HO2-->


