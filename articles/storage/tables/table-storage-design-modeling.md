---
title: Az Azure storage table tervezési kapcsolatok modellezését |} A Microsoft Docs
description: Ismerje meg a modellezési folyamatban, a table storage-megoldás tervezésekor.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 7f629ceb9fe5bd19c0558d1fde45d0bddcee744e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458873"
---
# <a name="modeling-relationships"></a>Kapcsolatok modellezése
Ez a cikk ismerteti az Azure Table storage-megoldások tervezéséhez nyújt segítséget a modellezési folyamatban.

Tartomány modellek készítése Ez a komplex rendszerek kialakítását legfontosabb lépés. A modellezési folyamat jellemzően entitások és a közöttük megismerheti az üzleti tartomány és a kialakítás a rendszer tájékoztatja, hogy a kapcsolatok azonosításához használni. Ez a szakasz összpontosít hogyan tudja lefordítani a közös kapcsolat típusok tartományi modellekkel az eltérések for a Table service tervek található. Egy logikai adatmodell-leképezés egy fizikai NoSQL-alapú-adatmodellbe, a folyamat eltér a relációs adatbázis tervezése során használt. Relációs adatbázisok tervezési általában feltételezi, hogy egy adatok normalizálási folyamat optimalizált minimálisra csökkentik a redundancia – és a egy deklaratív lekérdezési képesség, amely kivonatolja, hogyan megvalósítása az adatbázis működését.  

## <a name="one-to-many-relationships"></a>-A-többhöz kapcsolatok
Üzleti tartományi objektumok közötti egy-a-többhöz kapcsolatok gyakran előfordulnak: például egy részleg sok alkalmazott. Többféleképpen is egy-a-többhöz kapcsolatok megvalósításához a Table service szolgáltatásban minden egyes az előnyei és hátrányai, előfordulhat, hogy meg kell adni az adott forgatókönyv.  

Vegyünk egy nagy, multinacionális corporation például az intézetek, valamint alkalmazotti entitások, ahol minden részlege rendelkezik sok alkalmazott, és minden alkalmazott, egy adott részleg társított tízezer. Egyik lehetőség, hogy külön részleg és alkalmazott entitások, például a következő:  


![Store külön osztály és alkalmazottak entitások](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Ez a példa bemutatja egy implicit egy-a-többhöz kapcsolat a típusok alapján között a **PartitionKey** értéket. Bármelyik részleg lehet sok alkalmazott.  

Ez a példa bemutatja egy szervezeti egység és a kapcsolódó alkalmazott entitásokat is ugyanazon a partíción. A különböző partíciók, táblái vagy még akkor is, a storage-fiókok használata a különböző entitások esetében dönthet.  

Egy másik módszert, ha denormalizálja az adatokat, és csak az adatok denormalizált részleg, az alábbi példában látható módon alkalmazott entitásokat. Adott esetben ez denormalizált megközelítésében rejlik-kezelő nem lehet a legjobb, ha a követelmény, hogy tudja majd módosítani egy részleg vezetője részleteit, mivel ehhez frissítenie a részleg minden alkalmazott rendelkezik.  

![Alkalmazott entitás](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

További információkért lásd: a [Denormalizáció minta](table-storage-design-patterns.md#denormalization-pattern) Ez az útmutató későbbi.  

Az alábbi táblázat foglalja össze, és a hátrányai az alkalmazottak és a egy-a-többhöz kapcsolat áll fenn részleg entitások tárolására szolgáló fent vázolt módszerek előnyeit. Érdemes azt is, hogy várhatóan milyen gyakran csatlakozva különféle műveleteket végezhet: Elképzelhető, hogy elfogadható, amely magában foglalja a drága művelet, ha a művelet csak akkor fordul elő, ritkán kell.  

<table>
<tr>
<th>A módszer</th>
<th>Szakemberek számára</th>
<th>Hátrányok</th>
</tr>
<tr>
<td>Külön entitástípusok, ugyanazon a partíción, ugyanazon a táblán</td>
<td>
<ul>
<li>A részleg entitás egyetlen művelettel frissítheti.</li>
<li>Egy EGT használatával fenntartani a konzisztenciát, ha van egy szervezeti egység módosítása követelmény, amikor Ön frissítési, beszúrási vagy törlési alkalmazott entitás. Ha például egy részlegszintű alkalmazottak száma az egyes részlegek megmaradjanak.</li>
</ul>
</td>
<td>
<ul>
<li>Szükség lehet egy alkalmazott és a egy részleg entitás néhány ügyfél tevékenységek.</li>
<li>Tárolási műveletekre kerül sor, ugyanazon a partíción. Magas tranzakciós mennyiségben előfordulhat, hogy emiatt typu hotspot.</li>
<li>Egy új osztály használatával egy EGT nem helyezhetők át egy alkalmazott.</li>
</ul>
</td>
</tr>
<tr>
<td>Külön entitástípusok, a különböző partíciók vagy táblák vagy a storage-fiókok</td>
<td>
<ul>
<li>Egyetlen művelettel frissítheti egy részleg vagy alkalmazotti entitásokból.</li>
<li>Magas tranzakciós mennyiségben Ez segíthet több partíció között elosztani a terhelést.</li>
</ul>
</td>
<td>
<ul>
<li>Szükség lehet egy alkalmazott és a egy részleg entitás néhány ügyfél tevékenységek.</li>
<li>Nem használhat EGTs biztosítja az egységességet amikor, frissítési, beszúrási vagy törlési egy alkalmazott és a egy részleg frissítés. Például frissítése folyamatban van egy szervezeti egységben az alkalmazottak száma.</li>
<li>Egy új osztály használatával egy EGT nem helyezhetők át egy alkalmazott.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalizálja az egyetlen entitás típusa</td>
<td>
<ul>
<li>Egyetlen kérelem szükséges összes információt kérheti le.</li>
</ul>
</td>
<td>
<ul>
<li>Elképzelhető, hogy költséges fenntartani a konzisztenciát, ha részleg adatokat (Ez megköveteli, hogy frissítse a részleg minden alkalmazott) frissíteni kell.</li>
</ul>
</td>
</tr>
</table>

Hogyan választja ezeket a beállításokat, és mely és a hátrányai jelentős, attól függ, az adott alkalmazás-forgatókönyvek között. Például hogy milyen gyakran tegye módosítása részleg entitások; az alkalmazott összes lekérdezés szükséges a további részlegszintű információt; Hogyan zárja be, hogy a partíciók vagy a tárfiók skálázási korlátaival való?  

## <a name="one-to-one-relationships"></a>-Az-egyhez kapcsolat
Tartományi modellekkel tartalmazhat-az-egyhez kapcsolatot az entitások között. Egy-az-egyhez kapcsolat megvalósítása a Table service szolgáltatásban van szüksége, a két kapcsolódó entitások összekapcsolása, amikor szüksége van kérheti le azokat mindkét is ki kell választania. Ez a hivatkozás lehet implicit, egy egyezmény található értékek alapján, vagy explicit hivatkozás formájában való tárolásával **PartitionKey** és **rowkey tulajdonságok esetén** minden entitás, a kapcsolódó entitás értékeit. Az e tárolja a kapcsolódó entitások ugyanazon a partíción, lásd a szakasz [egy-a-többhöz kapcsolatok](#one-to-many-relationships).  

Is vannak, amelyek a Table service szolgáltatásban való kapcsolatok implementálási szempontok:  

* Nagy entitások kezelése (további információkért lásd: [nagy entitások minta](table-storage-design-patterns.md#large-entities-pattern)).  
* Végrehajtási hozzáférés-vezérlés (további információkért lásd: [közös hozzáférésű jogosultságkódokkal hozzáférés szabályozása](#controlling-access-with-shared-access-signatures)).  

## <a name="join-in-the-client"></a>Csatlakozás az ügyfél
Bár vannak kapcsolatok modellezésére a Table service szolgáltatásban, meg kell felejtse el, hogy a két elsődleges oka a Table service használatával-e a méretezhetőséget és teljesítményt. Ha talál, amelyek veszélyeztetik a teljesítményét és méretezhetőségét, hogy a megoldás több kapcsolatot vannak modellezés, kérdezze meg saját maga, hogy az adatkapcsolatok beépítése az Táblatervezés szükséges. Előfordulhat, hogy a kialakítás és javítását célzó méretezhetőségét és teljesítményét, hogy a megoldás, ha hagyja, hogy az ügyfélalkalmazás, hajtsa végre a szükséges illesztések.  

Például ha rendelkezik, amelyek adatokat tartalmaznak, amelyek nem változik gyakran kis táblák, ezután is egyszer lekérdezhetik ezeket az adatokat, és gyorsítótárazza azt az ügyfélen. Ez elkerülhető, ismétlődő életű könyvtárgyorsítótárból ugyanazokat az adatokat lekérdezni. Megvizsgáltunk van, ebben az útmutatóban a példákban az egy kis szervezetben lévő részlegek számára rendkívül kicsi, és módosítsa a ritkán így jó jelöltnek számít egy ügyfélalkalmazás egyszer töltheti le adatokat, és a gyorsítótár adatainak megkeresésére, valószínűleg.  

## <a name="inheritance-relationships"></a>Öröklés kapcsolatok
Ha az ügyfélalkalmazás osztályokat, amelyek részét képezik az öröklési kapcsolatban, amelyek az üzleti entitásokat használ, egyszerűen megőrizheti ezen entitások a Table service szolgáltatásban. Például előfordulhat, hogy rendelkezik a következő csoportját az ügyfélalkalmazásban található megadott ahol **személy** absztrakt osztály.

![Absztrakt osztály személy](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Az a Table service használatával egyetlen személy tábla entitások használatával, hogy néz ki ez a két tényleges osztály példányainak megőrizheti a:  

![Személy tábla](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

A több entitástípusok ugyanabban a táblában az Ügyfélkód működő kapcsolatos további információkért lásd: a szakasz [használata heterogén entitástípusok](#working-with-heterogeneous-entity-types) Ez az útmutató későbbi. Ez példákat az Ügyfélkód entitástípus felismerése.  


## <a name="next-steps"></a>További lépések

- [Táblázat kialakítási minták](table-storage-design-patterns.md)
- [Lekérdezés tervezése](table-storage-design-for-query.md)
- [Táblák adatainak titkosítása](table-storage-design-encrypt-data.md)
- [Adatmódosítás tervezése](table-storage-design-for-modification.md)
