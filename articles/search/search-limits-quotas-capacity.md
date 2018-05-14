---
title: Szolgáltatási korlátait, az Azure Search |} Microsoft Docs
description: A kapacitástervezés használt szolgáltatásra vonatkozó korlátozások és kérelmeit és válaszait az Azure Search maximális korlátozásait.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: heidist
ms.openlocfilehash: 9fd046efd01281de6d5b46cca37d22a48671b1b2
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="service-limits-in-azure-search"></a>Az Azure Search szolgáltatásra vonatkozó korlátozások
Maximális korlátozza a tárolási, számítási feladatok és indexek, dokumentumok, mennyiségét, és más objektumok függenek, hogy Ön [kiépíteni az Azure Search](search-create-service-portal.md) : **szabad**, **alapvető**, vagy **Szabványos** tarifacsomag szükséges.

+ **Szabad** egy több-bérlős megosztott szolgáltatás, amely az Azure-előfizetéssel rendelkezik.

+ **Alapszintű** dedikált számítási erőforrások biztosít egy kisebb léptékű termelési számítási feladatokhoz.

+ **Standard** minden szinten további tárolási és feldolgozási kapacitással rendelkező dedikált gépeken futtatja. Standard elérhető lesz a négy szinten lévő: S1, S2, S3 és S3 HD.

  S3 nagy sűrűségű (S3 HD) van fejthetők vissza az adott munkaterhelés konkrét: [több-bérlős](search-modeling-multitenant-saas-applications.md) és nagy mennyiségű kis indexek (1 millió dokumentumok / index, három év indexek-szolgáltatás esetében). Ez a szint nem biztosít a [indexelő szolgáltatás](search-indexer-overview.md). S3 HD, az adatfeldolgozást a leküldéses módszerrel, index forrástól elküldik az adatokat az API-hívásokban használatával kell használni. 

> [!NOTE]
> A szolgáltatás van üzembe helyezve egy konkrét csomagot kiválasztani. Ahhoz, hogy a kapacitás rétegek Ugrás magában foglalja a (nincs nincs frissítés) egy új szolgáltatás kiépítését. További információkért lásd: [válasszon egy SKU vagy a réteg](search-sku-tier.md). Beállítja egy már kiépített szolgáltatás belül kapcsolatos további információkért lásd: [erőforrás szintek lekérdezési és indexelési munkaterhelések méretezése](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Előfizetési korlátozásait
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Tárolási korlátai
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Index korlátok

| Erőforrás | Ingyenes | Basic&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD |
| -------- | ---- | ------------------- | --- | --- | --- | --- |
| Indexek maximális száma |3 |5 vagy 15 |50 |200 |200 |1000 partíciónként vagy 3000 szolgáltatásonként |
| Egy index maximális mezők |1000 |100 |1000 |1000 |1000 |1000 |
| Egy index maximális pontozási profilok |100 |100 |100 |100 |100 |100 |
| Profilonként maximális funkciók |8 |8 |8 |8 |8 |8 |

<sup>1</sup> alapvető szolgáltatások létrehozása után késői 2017 megnövekedett legfeljebb 15 indexek, az adatforrások és az indexelők. A korábban létrehozott szolgáltatások 5 rendelkeznek. Az alapszintű csomag az index / 100 mezők alsó határának csak SKU.

## <a name="document-limits"></a>A dokumentum korlátok 

A legtöbb régióban tarifacsomagok (Basic, S1, S2, S3, S3 HD) Azure Search korlátlan dokumentumok számát az összes szolgáltatás létrehozása utáni novemberi/December 2017. Ez a szakasz azonosítja a régiókban, ahol a határok vonatkoznak, és annak megállapítása, hogy a szolgáltatás hatással van. 

Annak megállapításához, hogy a szolgáltatás rendelkezik-e a dokumentum korlátok, ellenőrizze a használata csempe a áttekintése lapon a szolgáltatást. Dokumentálja száma korlátlan, vagy a réteg alapján korlátozni alá.

  ![Használata csempe](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-and-services-having-document-limits"></a>Régiók és dokumentum korlátok rendelkező szolgáltatások

Korlátok rendelkező szolgáltatások késői 2017 előtt hozták létre, vagy alacsonyabb teljesítményű fürtök használata az Azure Search szolgáltatás üzemeltetési adatközpontok futtatja. Érintett adatközpontokban a következő régiók a következők:

+ Kelet-Ausztrália
+ Kelet-Ázsia
+ Közép-India
+ Nyugat-Japán
+ USA nyugati középső régiója

A dokumentum korlátai szolgáltatásokra a következő maximális korlátok vonatkoznak:

|  Ingyenes | Alapszintű | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1 millió |15 millió partíciónként vagy 180 millió szolgáltatásonként |60 millió partíciónként vagy 720 millió szolgáltatásonként |120 millió partíciónként vagy 1,4 milliárd szolgáltatásonként |1 millió indexenként vagy 200 millió partíciónként |

> [!Note] 
> S3 nagy sűrűségű szolgáltatások késői 2017 után létrehozott a 200 millió dokumentumot partíciónként el lett távolítva, azonban az 1 millió dokumentumot index korlát marad.


### <a name="document-size-limits-per-api-call"></a>A dokumentum méretkorlátait API-hívások száma

A maximális dokumentum egy Index API meghívásakor mérete körülbelül 16 MB.

A dokumentum mérete ténylegesen maximális mérete a Index API-kérés törzsében. Átadhatók egy kötegelt több dokumentumot az Index API egyszerre, mivel a méretkorlátot reálisan hány dokumentumok szerepelnek a kötegelt függ. Egyetlen dokumentum kötegben a dokumentum maximális mérete 16 MB JSON.

Tartsa a dokumentum mérete, ne felejtse el nem lekérdezhető adatok kihagyása a kérelemből. Képek és egyéb bináris adatokat nem közvetlenül lekérdezhető, és nem szabad az index. Nem lekérdezhető adatok integrálja a keresési eredmények között, adja meg az erőforrás URL-cím hivatkozást tartalmazó nem kereshető mező.

## <a name="indexer-limits"></a>Az indexelő korlátok

Késői 2017 után létrehozott alapvető szolgáltatások megnövekedett legfeljebb 15 indexek, az adatforrások, skillsets és indexelők rendelkeznek.

| Erőforrás | Szabad&nbsp;<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|
| -------- | ----------------- | ----------------- | --- | --- | --- | --- |
| Indexelők maximális száma |3 |5 vagy 15|50 |200 |200 |– |
| Adatforrások maximális száma |3 |5 vagy 15 |50 |200 |200 |– |
| Maximális skillsets |3 |5 vagy 15 |50 |200 |200 |– |
| Hívásonkénti maximális indexelési betöltése |10 000 dokumentumok |Maximális dokumentumokat csak korlátozva |Maximális dokumentumokat csak korlátozva |Maximális dokumentumokat csak korlátozva |Maximális dokumentumokat csak korlátozva |– |
| Maximális futási időt | 1-3 perc |24 óra |24 óra |24 óra |24 óra |–  |
| A BLOB indexelő: blob maximális mérete, MB |16 |16 |128 |256 |256 |–  |
| A BLOB indexelő: blob kinyert tartalom maximális karakterszám |32,000 |64,000 |4 millió |4 millió |4 millió |– |

<sup>1</sup> az ingyenes szolgáltatásokhoz indexelő maximális végrehajtási idő 3 perc alatt van, a blob-forrásoknak és más adatforrások 1 perc.

<sup>2</sup> alapvető szolgáltatások létrehozása után késői 2017 megnövekedett legfeljebb 15 indexek, az adatforrások és az indexelők. A korábban létrehozott szolgáltatások 5 rendelkeznek.

<sup>3</sup> S3 HD services nem támogatja az indexelő.

## <a name="queries-per-second-qps"></a>Lekérdezések / másodperc (QPS)

QPS becslése egymástól függetlenül kifejlesztett minden ügyfél. Index mérete és összetettségét, lekérdezés méretét és összetettségét és az adatforgalom mennyisége olyan elsődleges befolyásoló QPS. Nincs semmilyen módon nem értelmezhető becslése ajánlatot tényezőket számára ismeretlen.

Megbecsli több előre jelezhető, ha a dedikált erőforrások (Basic és Standard rétegek) futó szolgáltatások számított. További QPS megbecsülheti szorosan mert befolyásolni több paramétert. Megközelítés becslés kapcsolatos útmutatásért lásd: [Azure Search teljesítmény- és optimalizálási](search-performance-optimization.md).

## <a name="api-request-limits"></a>API-kérelmekre vonatkozó korlátok
* 16 MB kérelmenként maximálisan <sup>1</sup>
* 8 KB-os URL-cím maximális hossza
* Legfeljebb 1000 dokumentumok kötegenként index feltöltését, egyesíti vagy törlése
* A $orderby záradékban maximális 32 mezők
* Maximális keresési kifejezés mérete 32766 bájt (32 KB-os mínusz 2 bájt) UTF-8 kódolású szöveg

<sup>1</sup> az Azure Search kérelem törzse 16 MB, a gyakorlati küszöbérték egyéni mezők vagy gyűjtemények, ellenkező esetben nem korlátozott elméleti korlátok tartalmára előíró felső korlát vonatkozik (lásd: [támogatott adatok típusok](https://msdn.microsoft.com/library/azure/dn798938.aspx) mező összetételűnek és korlátozások olvashat).

## <a name="api-response-limits"></a>API-válaszból korlátok
* A keresési eredmények laponként visszaadott legfeljebb 1000 dokumentumok
* Javaslat API kérelmenként visszaadott legfeljebb 100 javaslatok

## <a name="api-key-limits"></a>Az API-kulcs korlátok
API-kulcsokat használnak a szolgáltatás-hitelesítéshez. Két típusukat különböztetjük meg. A kérelem fejlécében megadott adminisztrációs kulcsok, és a szolgáltatás teljes olvasási és írási hozzáférést. Lekérdezés kulcsai csak olvasható, az URL-cím a megadott, és általában elosztott ügyfélalkalmazások számára.

* Legfeljebb 2 adminisztrációs kulcsok-szolgáltatás
* Legfeljebb 50 lekérdezési kulcsok-szolgáltatás
