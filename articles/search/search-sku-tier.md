---
title: "Válassza ki a Termékváltozat vagy IP-címek az Azure Search |} Microsoft Docs"
description: "Az Azure Search is üzembe ezek termékváltozatok: ingyenes, a Basic és standard szintű, ahol Standard lehetőség a különböző erőforrás-konfigurációk és kapacitása szintjének."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 8d4b7bca-02a5-43ee-b3f8-03551dfb32fd
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/24/2016
ms.author: heidist
ms.openlocfilehash: f9f3a7b2369818791ffac1c8eeccef45216c2ff0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Termékváltozat vagy tarifacsomag választása az Azure Search szolgáltatáshoz
Az Azure Search egy [szolgáltatás kiépítve](search-create-service-portal.md) egy adott árképzési szint vagy a Termékváltozat. A választható lehetőségek **szabad**, **alapvető**, vagy **szabványos**, ahol **szabványos** több konfigurációk és kapacitások érhető el.

Ez a cikk célja segítségével válassza ki a réteg. Ha a réteg kapacitása elemről kiderül, hogy túl alacsony, szüksége lesz egy új szolgáltatás a magasabb szintű használható ellátásához, majd töltse be újra az indexek. Nincs nem ugyanazt a szolgáltatást a egyik Termékváltozatáról egy másikra helybeni frissítését.

> [!NOTE]
> A réteg a választott és [egy keresési szolgáltatás kiépítése](search-create-service-portal.md), növelheti a replika- és partíció adatokra a szolgáltatáson belül. Útmutatásért lásd: [erőforrás szintek lekérdezési és indexelési munkaterhelések méretezése](search-capacity-planning.md).
>
>

## <a name="how-to-approach-a-pricing-tier-decision"></a>Hogyan készíthető elő egy árképzési szint döntési
Az Azure Search a réteg kapacitását, és nem a szolgáltatás rendelkezésre állásával határozza meg. Szolgáltatások általában érhető el minden réteghez, beleértve az előzetes verziójú funkciókat. Az egyetlen kivétel ez alól a S3 HD indexelő esetében nem támogatott.

> [!TIP]
> Azt javasoljuk, hogy mindig mértékben egy **szabad** szolgáltatást (egy előfizetésenként lejárat nélkül), hogy passzív projektek a könnyen elérhető. Használja a **szabad** hozzon létre egy második számlázható szolgáltatás; tesztelésében és értékelésében a szolgáltatás a **alapvető** vagy **szabványos** réteget a termelési vagy nagyobb tesztelési célú alkalmazásokat.
>
>

Kapacitás és a szolgáltatás futtatásával járó költségek nyissa meg kézzel az aktuális. Ebben a cikkben található információk segíthetnek eldönteni, rendszerrel SKU az egyensúlyt a, de bármely azt, hogy hasznos legyen, a következő legalább durva becslést kell:

* Száma és mérete indexek kíván létrehozni
* Száma és mérete dokumentumok feltöltése
* Összeállítottuk lekérdezés kötet, tekintetében lekérdezések egy második (QPS)

Száma és mérete fontosak, mert a legmagasabb keresztül rögzített korlátját indexek vagy egy szolgáltatást a dokumentumok számát, vagy a szolgáltatás által használt erőforrások (tároló- és replikák) elérésekor. A tényleges a szolgáltatás határa attól lekérdezi használt először: erőforrások vagy objektumokat.

Az aktuális becslések az alábbi lépéseket kell egyszerűbbé:

* **1. lépés** tekintse át a rendelkezésre álló beállításokkal kapcsolatos további tudnivalók az alábbi SKU leírása.
* **2. lépés** előzetes döntési érkeznek az alábbi kérdések megválaszolása.
* **3. lépés** döntés véglegesítéséhez szigorú korlátozásokat tároló áttekintése és az árképzés terén.

## <a name="sku-descriptions"></a>Termékváltozat leírása
A következő táblázat ismerteti az egyes rétegek.

| Szint | Elsődleges forgatókönyvek |
| --- | --- |
| **Ingyenes** |Megosztott szolgáltatás, díjmentesen, értékelése, a vizsgálat vagy a kis munkaterhelés használt. Más előfizetők van megosztva, mivel lekérdezés átviteli sebesség és a indexelő függ ki más használja a szolgáltatást. A kapacitása kis (50 MB vagy 3 indexek 10 000 fel dokumentumokat egyes). |
| **Basic** |Kis termelési számítási feladatokhoz a dedikált hardveren. Magas rendelkezésre állású. A kapacitása legfeljebb 3 replikák és 1 partíció (2 GB-ot). |
| **S1** |Standard 1 támogatja a partíciók (12) és a replikák (12), medium termelési számítási feladatokhoz a dedikált hardveren használt rugalmas kombinációi. Partíciók és replikák maximális száma 36 számlázható keresési egység által támogatott kombinációkban foglalhatja le. Ezen a szinten partíció található, 25 GB és QPS körülbelül 15 lekérdezések száma másodpercenként. |
| **S2** |Standard 2 fut a azonos 36 keresési egységek használatával S1 megegyezik, azonban nagyobb méretű partíciókat és a replikák nagyobb termelési számítási feladatokhoz. Ezen a szinten partíció található, a 100 GB és QPS készül 60 lekérdezések száma másodpercenként. |
| **S3** |Standard 3 futó arányosan nagyobb termelési számítási feladatokhoz nagyobb kapacitású rendszerek, legfeljebb 12 partíciók vagy 12 replikák beállításait a 36 keresési egység. Ezen a szinten partíció található, a 200 GB és QPS másodpercenként több mint 60 lekérdezések. |
| **S3 HD** |Standard 3 nagy sűrűségű kisebb indexek nagy számú tervezték. Legfeljebb 3 partíció 200 GB lehet. QPS másodpercenként több mint 60 lekérdezések. |

> [!NOTE]
> Replika és a partíció méretkorlát egységként keresési (36 egységek maximális száma a szolgáltatás), mint mi a legnagyobb azt jelenti, arc értéken hatékony alsó határának előíró számlázzuk ki. Például a legfeljebb 12 replikák használatához lehet legfeljebb 3 partíció (12 * 3 = 36 egységek). Ehhez hasonlóan maximális partíciók használatához csökkentse replikák 3. Lásd: [erőforrás szintjeinek lekérdezési és indexelési munkaterhelések az Azure Search méretezése](search-capacity-planning.md) megengedett kombinációk diagram.
>
>

## <a name="review-limits-per-tier"></a>Tekintse át a határon belül az egyes réteg
A következő diagram része a korlátok [az Azure Search szolgáltatásra vonatkozó korlátozások](search-limits-quotas-capacity.md). Felsorolja a legnagyobb valószínűséggel egy SKU döntés befolyásolja tényezőket. Ez a diagram hivatkozni lehet az alábbi kérdésekre felülvizsgálata során.

| Erőforrás | Ingyenes | Alapszintű | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Szolgáltatói szerződés (SLA) |No <sup>1</sup> |Igen |Igen |Igen |Igen |Igen |
| Index korlátok |3 |5 |50 |200 |200 |1000 <sup>2</sup> |
| A dokumentum korlátok |10 000 teljes |1 millió szolgáltatás |15 millió partíciónként |60 millió partíciónként |120 millió partíciónként |1 millió index |
| Maximális partíciók |N/A |1 |12 |12 |12 |3 <sup>2</sup> |
| Partíció mérete |Összesen 50 MB |2 GB-ot szolgáltatás |Partíciónként 25 GB |100 GB-ot partíció (legfeljebb 1.2-es TB-szolgáltatás esetében) |200 GB-ot partíció (legfeljebb 2,4 TB-szolgáltatás esetében) |200 GB-os (legfeljebb 600 GB-szolgáltatás esetében) |
| Maximális replikák |N/A |3 |12 |12 |12 |12 |
| Lekérdezések száma másodpercenként |N/A |~3 replikánként |~15 replikánként |~60 replikánként |>60 replikánként |>60 replikánként |

<sup>1</sup> ingyenes szint és az előzetes funkciók nem tartoznak a szolgáltatásszint-szerződések (SLA). Az összes számlázható rétegek SLA-k érvénybe, ha a szolgáltatás megfelelő redundancia kiépítése. Két vagy több replikák szükségesek (olvasás) lekérdezés SLA-t. Három vagy több replikák lekérdezés és az indexelés (írásra) SLA szükségesek. A partíciók számának nincs egy SLA-t szempont. 

<sup>2</sup> S3 és S3 HD azonos nagy kapacitású infrastruktúra által támogatott, de azok eléri a maximális korlátját különböző módon. S3 nagyon nagy indexek kevesebb célozza. Mint ilyen, a maximális korlátját az erőforrás-kötött (minden egyes szolgáltatás 2,4 TB). S3 HD nagyon kis indexek nagy számú célozza. S3 HD 1000 indexek eléri index megkötések formájában teljes kapacitásukkal működjenek. Ha egy több mint 1000 indexek szükséges S3 HD ügyfelünk, lépjen kapcsolatba a Microsoft Support információt arról, hogyan.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Kiküszöbölése termékváltozatok követelményeinek nem megfelelő.
A következő kérdések segíthetnek a megfelelő SKU döntés a munkaterheléshez érkeznek.

1. Rendelkezik **szolgáltatási szint szerződés (SLA)** követelmények? Használhat bármilyen számlázható réteg (alapvető a mentést), de konfigurálnia kell a redundancia érdekében. Két vagy több replikák szükségesek (olvasás) lekérdezés SLA-t. Három vagy több replikák lekérdezés és az indexelés (írásra) SLA szükségesek. A partíciók számának nincs egy SLA-t szempont.
2. **Hány indexeli** szükség van-e? A legnagyobb változók faktoring SKU döntést az egyik minden SKU által támogatott indexek száma. Index funkció az alsó tarifacsomagok jelentősen különböző szinteken. Indexek száma vonatkozó követelmények egy SKU döntés elsődlegesen meghatározó tényező lehet.
3. **Hány dokumentumok** lesz betöltve minden indexbe? Számát és méretét, a dokumentumok az index végleges méretét határozza meg. Ha az index tervezett méretét megbecsülheti, összehasonlíthatja ezt a számot, szemben a partíció méretet a Termékváltozat, így ez a méret index tárolásához szükséges partíciók száma.
4. **Mi az a várt lekérdezés terhelését**? Miután tárhellyel kapcsolatos követelmények értendők, fontolja meg a lekérdezés munkaterhelések. S2 és mindkét S3 termékváltozatok biztosítják a közelében egyenértékű átviteli, miközben a szolgáltatásszint-szerződési követelményeit minden előzetes termékváltozatok fog kizárása.
5. Ha a S2 vagy S3 réteg, szükségességének eldöntése [indexelők](search-indexer-overview.md). Indexelő még nem állnak rendelkezésre a S3 HD szinthez. Alternatív megoldás, a leküldési modelljével használandó adatkészlet leküldéses indexek alkalmazáskód ismertetheti index frissítések.

A legtöbb ügyfél szabály is egy adott SKU bejövő vagy kimenő a kérdésekre adott válaszok a fenti alapján. Ha még nem meg arról, hogy mely SKU-nal, MSDN vagy StackOverflow fórumokban kérdéseit, vagy Azure ügyfélszolgálatához kapcsolatos további iránymutatásért.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>Döntési érvényesítése: az SKU elegendő tárhely és QPS ajánlja fel?
Utolsó lépésként le újra a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/search/) és a [szolgáltatásra vonatkozó korlátozások szolgáltatás és az-index részeiben](search-limits-quotas-capacity.md) ellenőrizze az előfizetés és a szolgáltatás korlátozások becslése.

Ha a ár vagy a tárolási követelmények értéktartományon kívül esik, előfordulhat, hogy szeretné több kisebb szolgáltatás munkaterhelésének refactor (például). Részletesebb felügyeletét akkor sikerült Újratervezés indexek kisebb legyen, vagy használja szűrők hatékonyabbá teszi lekérdezések.

> [!NOTE]
> Tárolási követelmények túlzott felfújt lehet, ha a dokumentumok oda nem tartozó adatokat tartalmaznak. Ideális esetben a dokumentumok csak a kereshető adatokat vagy a metaadatokat tartalmaznak. Bináris adatok nem kereshető és kell tárolni külön-külön (lehet, hogy az Azure tábla vagy a blob storage) mezőt a index URL-cím hivatkozni kell a külső adatok tárolásához. Egy adott dokumentumot maximális mérete 16 MB (vagy ha kevesebb mint egy kérelem több dokumentum feltöltése tömeges). Lásd: [szolgáltatási korlátait, az Azure Search](search-limits-quotas-capacity.md) további információt.
>
>

## <a name="next-step"></a>Következő lépés
Miután eldöntötte, amely SKU megfelelő a méretezése, folytassa a ezeket a lépéseket:

* [A search szolgáltatás létrehozása a portálon](search-create-service-portal.md)
* [Csökkentheti a lefoglalást partíciókat és a replikákat a szolgáltatás méretezésére](search-capacity-planning.md)
