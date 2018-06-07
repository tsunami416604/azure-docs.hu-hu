---
title: Az Azure storage Táblatervezés kapcsolatok modellezési |} Microsoft Docs
description: Ismerje meg a modellezési folyamatban, a tábla tárolási megoldás tervezése során.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 561281375273135009a956fd27dfe9f193ab92ac
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660915"
---
# <a name="modeling-relationships"></a>Modellezési kapcsolatok
A cikk ismerteti a modellezési folyamat alakítsa ki az Azure Table storage megoldások.

A Tervező összetett rendszerek kulcsfontosságú lépés létrehozási modelleket tartomány. Általában a modellezési folyamat segítségével azonosíthatja a entitásokat és a köztük lévő viszonyt is egyik módja az üzleti tartomány megértéséhez, valamint a tervezési, a rendszer tájékoztatja. Ez a szakasz összpontosít hogyan tudja lefordítani néhány a közös kapcsolattípusok tartomány modellek mintájára a Table szolgáltatás található. Egy logikai adatmodell-leképezés egy fizikai alapú NoSQL-adatmodellhez folyamata eltér a relációs tervezése során használt. Relációs adatbázisok terv általában azt feltételezi, hogy a normalizálási adatfeldolgozás minimalizálja a redundancia – és a deklaratív lekérdező képessége, hogy hogyan végrehajtásának az adatbázis működésének kivonatolja optimalizálva.  

## <a name="one-to-many-relationships"></a>Egy-a-többhöz kapcsolatok
Egy-a-többhöz kapcsolatok üzleti tartomány objektumok közötti gyakran előfordulnak: például egy részleg rendelkezik sok alkalmazott. Többféleképpen is egy-a-többhöz kapcsolatok szolgáltatásban a tábla minden egyes az előnyei és hátrányai, amely fontos lehet az adott forgatókönyv megvalósításához.  

Vegye figyelembe a példa egy nagy több nemzeti vállalat osztályai és ahol minden részlege rendelkezik sok alkalmazott, és minden alkalmazott, egy adott részleg társított alkalmazott entitások tízezreit tartalmazó. Egy megoldás, külön részleg és az alkalmazott entitások, például a tároló:  


![Külön részleg és az alkalmazott entitások tárolása](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Ez a példa bemutatja egy implicit egy-a-többhöz kapcsolat alapján típusa közötti a **PartitionKey** érték. Minden részleghez rendelkezhet sok alkalmazott.  

Ez a példa egy szervezeti egység és a kapcsolódó alkalmazott entitásokat is tartalmazó partícióra szerepel. Megadhatja, hogy a különböző entitástípusok különböző partíciók, táblák, vagy még akkor is, storage-fiókok használandó.  

Egy másik módszert is, hogy az adatok denormalize és csak alkalmazott entitások denormalizált részleg adatokkal a következő példában látható módon tárolja. Adott esetben ez a megközelítés denormalizált nem lehet a legjobb lehet módosítani a részleg vezetője részleteit, mert ehhez frissítenie kell az osztály minden alkalmazott számára, ha.  

![alkalmazott entitás](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

További információkért lásd: a [Denormalization mintát](table-storage-design-patterns.md#denormalization-pattern) című útmutatóban.  

Az alábbi táblázat foglalja össze, és az egyes az alkalmazottak és a részleg entitások, amelyek egy-a-többhöz tárolását a fent vázolt módszerekkel. Emellett érdemes milyen gyakran várható a különböző műveletek végrehajtásához: elfogadható kell rendelkeznie, amely tartalmaz egy drága művelet, ha ez a művelet csak akkor fordul elő, a ritkán lehet.  

<table>
<tr>
<th>Módszer</th>
<th>Informatikai szakemberek</th>
<th>Hátrányok</th>
</tr>
<tr>
<td>Külön entitástípusok, egyazon partícióra kerüljenek, ugyanahhoz a táblához</td>
<td>
<ul>
<li>A részleg entitás egyetlen művelettel frissítheti.</li>
<li>Egy EGT használatával biztosítja az egységességet, ha egy szervezeti egység módosítása követelmény amikor Ön frissítés/Beszúrás/törlés alkalmazott entitás. Ha például a részlegszintű alkalmazott száma minden részleg számára megmaradjanak.</li>
</ul>
</td>
<td>
<ul>
<li>Az alkalmazottak és a bizonyos ügyfél tevékenységek egy részleg entitást szeretne.</li>
<li>Tárolási műveletekre kerül sor a partícióra. A magas tranzakció kötetek Ez azt eredményezheti, interaktív terület.</li>
<li>Egy alkalmazott egy új osztály használatával egy EGT nem helyezhető át.</li>
</ul>
</td>
</tr>
<tr>
<td>Külön entitástípusok, a különböző partíciók vagy a táblák vagy a storage-fiókok</td>
<td>
<ul>
<li>A részleg entitás vagy az alkalmazott entitás egyetlen művelettel frissítheti.</li>
<li>A magas tranzakció kötetek ez elősegítheti a terhelés elosztva több partíciót.</li>
</ul>
</td>
<td>
<ul>
<li>Az alkalmazottak és a bizonyos ügyfél tevékenységek egy részleg entitást szeretne.</li>
<li>Nem használhat EGTs biztosítja az egységességet amikor akkor frissítés/Beszúrás/Törlés az alkalmazottak és a frissítés részleget. Például frissítése folyamatban van egy szervezeti egységben egy alkalmazott száma.</li>
<li>Egy alkalmazott egy új osztály használatával egy EGT nem helyezhető át.</li>
</ul>
</td>
</tr>
<tr>
<td>Az egyetlen entitás típusa denormalize</td>
<td>
<ul>
<li>Az egy kérelemhez szükséges összes információ kérheti le.</li>
</ul>
</td>
<td>
<ul>
<li>Biztosítja az egységességet, ha a szervezeti adatok (Ez akkor miatt frissítenie kell egy osztály minden alkalmazott) kell drága lehet.</li>
</ul>
</td>
</tr>
</table>

Hogyan választja ezeket a beállításokat, és mely előnyének és hátrányának jelentős, függ az adott alkalmazás forgatókönyvek között. Például milyen gyakran tegye módosítása részleg entitások; szükség van a további részlegre vonatkozó információkat; az alkalmazott lekérdezés milyen közel legyenek, a partíciók vagy a tárfiók a méretezhetőségének korlátai?  

## <a name="one-to-one-relationships"></a>-Az-egyhez kapcsolat
Tartomány modellek egy az egyhez típusú entitások közötti kapcsolatok is tartalmazhat. Kell megvalósítani a Table szolgáltatás egy az egyhez kapcsolat, a két kapcsolódó entitás csatolása, ha vissza mindkét kell is ki kell választania. Ez a hivatkozás lehet implicit, egy egyezmény értékek alapján, vagy explicit hivatkozás formájában elhelyezésével **PartitionKey** és **RowKey** értékek az entitásban, a kapcsolódó entitás. E tárolja a kapcsolódó entitásokból partícióra tárgyalását című [egy-a-többhöz kapcsolatok](#one-to-many-relationships).  

Nincsenek is vezethet, hogy egy az egyhez kapcsolat megvalósíthatja a Table szolgáltatás megvalósítási szempontokat:  

* Nagy entitások kezelése (további információkért lásd: [nagy entitások mintát](table-storage-design-patterns.md#large-entities-pattern)).  
* A végrehajtási hozzáférés-vezérlést (további információkért lásd: [megosztott hozzáférési aláírásokkal-hozzáférés szabályozásáról](#controlling-access-with-shared-access-signatures)).  

## <a name="join-in-the-client"></a>Az ügyfél csatlakozott
Bár vannak lehetőségek kapcsolatok modellezésére a Table szolgáltatásban, érdemes nem elfelejti, hogy a két elsődleges oka a Table szolgáltatás használatára vonatkozó-e a méretezhetőséget és teljesítményt nyújt. Ha úgy találja, akkor a több kapcsolatot, amelyek veszélyeztetik a teljesítmény és méretezhetőség a megoldás modellezési, kérdezze meg saját kezűleg esetén az adatok minden olyan kapcsolat rendszerbe tábla létrehozásához szükséges. Előfordulhat, hogy a tervezési és javítását célzó méretezhetőségét és teljesítményét a megoldás, ha az ügyfélalkalmazást, hajtsa végre a szükséges illesztések.  

Például ha kis-táblázatot, amely nem változik gyakran adatokat tartalmaznak, majd után lekérdezhetik ezeket az adatokat többször is gyorsítótárazása azt az ügyfélen. Ennek elkerülése érdekében ismételt használatával ugyanazokat az adatokat beolvasni. A példákban azt kell venni, a jelen útmutató kis szervezet szervezeti egységek készletét valószínű, hogy rövid, és módosítsa a ritkán így egy jó jelölt az ügyfélalkalmazás tölthetik le egyszer adatok és a gyorsítótár, keresse meg a adatok.  

## <a name="inheritance-relationships"></a>Öröklődés kapcsolatok
Ha az ügyfélalkalmazást használ, amely egy üzleti entitásokat képviselő öröklési kapcsolatban részét osztályok, egyszerűen a a Table szolgáltatásban entitásokból maradnak meg. Például lehetséges, hogy az ügyfél alkalmazásban megadott üzenetosztályának beolvasása a következő funkciócsoport ahol **személy** absztrakt osztály.

![Absztrakt osztály személy](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

A Table szolgáltatásban entitások használata, hogy a hely egyetlen személy tábla használatával két konkrét osztályok példányai is továbbra is fennáll:  

![Személy tábla](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

További információt az Ügyfélkód ugyanabban a táblában a több entitás, című [használata heterogén entitástípusok](#working-with-heterogeneous-entity-types) Ez az útmutató későbbi. Ez az Ügyfélkód entitástípus felismerése példákat tartalmaz.  


## <a name="next-steps"></a>További lépések

- [Táblázat kialakítási minták](table-storage-design-patterns.md)
- [Tervezési lekérdezése](table-storage-design-for-query.md)
- [Tábla adatok titkosítása](table-storage-design-encrypt-data.md)
- [Adatmódosítás kialakítása](table-storage-design-for-modification.md)