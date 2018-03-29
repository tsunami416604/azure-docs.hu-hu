---
title: Szolgáltatási korlátait, az Azure Search |} Microsoft Docs
description: A kapacitástervezés használt szolgáltatásra vonatkozó korlátozások és kérelmeit és válaszait az Azure Search maximális korlátozásait.
services: search
documentationcenter: ''
author: HeidiSteen
manager: jhubbard
editor: ''
tags: azure-portal
ms.assetid: 857a8606-c1bf-48f1-8758-8032bbe220ad
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/26/2018
ms.author: heidist
ms.openlocfilehash: fb2234e79e8deb98a94068f31a40c8f0b415d7ba
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="service-limits-in-azure-search"></a>Az Azure Search szolgáltatásra vonatkozó korlátozások
Maximális korlátozza a tárolási, számítási feladatok és indexek, dokumentumok, mennyiségét, és más objektumok függenek, hogy Ön [kiépíteni az Azure Search](search-create-service-portal.md) , egy **szabad**, **alapvető**, vagy **Szabványos** tarifacsomagra vált.

* **Szabad** egy több-bérlős megosztott szolgáltatás, amely az Azure-előfizetéssel rendelkezik. 
* **Alapszintű** dedikált számítási erőforrások biztosít egy kisebb léptékű termelési számítási feladatokhoz.
* **Standard** minden szinten további tárolási és feldolgozási kapacitással rendelkező dedikált gépeken futtatja. Standard elérhető lesz a négy szinten lévő: S1, S2, S3 és S3 nagy sűrűségű (S3 HD).

> [!NOTE]
> A szolgáltatás van üzembe helyezve egy konkrét csomagot kiválasztani. Ahhoz, hogy a kapacitás rétegek Ugrás magában foglalja a (nincs nincs frissítés) egy új szolgáltatás kiépítését. További információkért lásd: [válasszon egy SKU vagy a réteg](search-sku-tier.md). Beállítja egy már kiépített szolgáltatás belül kapcsolatos további információkért lásd: [erőforrás szintek lekérdezési és indexelési munkaterhelések méretezése](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Előfizetési korlátozásait
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="service-limits"></a>Szolgáltatási korlátozások
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

## <a name="index-limits"></a>Index korlátok

| Erőforrás | Ingyenes | Alapszintű | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Egy index maximális mezők |1000 |100 <sup>1</sup> |1000 |1000 |1000 |1000 |
| Egy index maximális pontozási profilok |100 |100 |100 |100 |100 |100 |
| Profilonként maximális funkciók |8 |8 |8 |8 |8 |8 |

<sup>1</sup> alapvető érvényben lévő korlát miatt az index / 100 mezők alsó határának csak Termékváltozat.

## <a name="indexer-limits"></a>Az indexelő korlátok

| Erőforrás | Ingyenes | Alapszintű | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Hívásonkénti maximális indexelési betöltése |10 000 dokumentumok |Maximális dokumentumokat csak korlátozva |Maximális dokumentumokat csak korlátozva |Maximális dokumentumokat csak korlátozva |Maximális dokumentumokat csak korlátozva |N/A <sup>1</sup> |
| Maximális futási időt | 1 – 3 percet <sup>2</sup> |24 óra |24 óra |24 óra |24 óra |N/A <sup>1</sup> |
| A BLOB indexelő: blob maximális mérete, MB |16 |16 |128 |256 |256 |N/A <sup>1</sup> |
| A BLOB indexelő: blob kinyert tartalom maximális karakterszám |32,000 |64,000 |4 millió |4 millió |4 millió |N/A <sup>1</sup> |

<sup>1</sup> S3 HD jelenleg nem támogatja az indexelők. Ha ezt a képességet sürgős van, forduljon az Azure támogatási szolgálatához.

<sup>2</sup> indexelő maximális végrehajtási ingyenes szint ideje 3 perc, a blob-forrásoknak és más adatforrások 1 perc.


## <a name="document-size-limits"></a>A dokumentum méretkorlátai
| Erőforrás | Ingyenes | Alapszintű | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Egyes dokumentum méretet a Index API |<16 MB |<16 MB |<16 MB |<16 MB |<16 MB |<16 MB |

A dokumentum maximális méretét jelenti az Index API-k hívásakor. A dokumentum mérete ténylegesen maximális mérete a Index API-kérés törzsében. Átadhatók egy kötegelt több dokumentumot az Index API egyszerre, mivel a méretkorlátot ténylegesen hány dokumentumok szerepelnek a kötegelt függ. Egyetlen dokumentum kötegben a dokumentum maximális mérete 16 MB JSON.

Tartsa a dokumentum mérete, ne felejtse el nem lekérdezhető adatok kihagyása a kérelemből. Képek és egyéb bináris adatokat nem közvetlenül lekérdezhető, és nem szabad az index. Nem lekérdezhető adatok integrálja a keresési eredmények között, adja meg az erőforrás URL-cím hivatkozást tartalmazó nem kereshető mező.

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
API-kulcsokat használnak a szolgáltatás-hitelesítéshez. Két típusa van. A kérelem fejlécében megadott adminisztrációs kulcsok, és a szolgáltatás teljes olvasási és írási hozzáférést. Lekérdezés kulcsai csak olvasható, az URL-cím a megadott, és általában elosztott ügyfélalkalmazások számára.

* Legfeljebb 2 adminisztrációs kulcsok-szolgáltatás
* Legfeljebb 50 lekérdezési kulcsok-szolgáltatás
