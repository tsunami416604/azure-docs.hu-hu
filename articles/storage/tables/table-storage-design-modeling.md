---
title: Kapcsolatok modellezése az Azure Table storage designban | Microsoft dokumentumok
description: Ismerje meg a modellezési folyamatot a táblatárolási megoldás tervezésekor.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 25082c107fbc0feeb533aa2b4fc56cff960e778d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457559"
---
# <a name="modeling-relationships"></a>Kapcsolatok modellezése
Ez a cikk ismerteti a modellezési folyamatot, amely segít az Azure Table tárolási megoldások tervezésében.

A tartománymodellek létrehozása kulcsfontosságú lépés az összetett rendszerek tervezésében. A modellezési folyamat segítségével általában az entitások és a köztük lévő kapcsolatok azonosítására használja az üzleti tartomány megértését és a rendszer tervezésének tájékoztatását. Ez a szakasz arra összpontosít, hogyan fordíthatja le a tartománymodellekben található egyes gyakori kapcsolattípusokat a Table szolgáltatás terveire. A logikai adatmodellből a fizikai NoSQL-alapú adatmodellre való leképezés folyamata eltér a relációs adatbázis tervezésekor használt tól. A relációs adatbázisok tervezése általában a redundancia minimalizálására optimalizált adatnormalizálási folyamatot feltételezi – és egy deklaratív lekérdezési képességet, amely absztrakt módon absztrakt az adatbázis működésének megvalósításában.  

## <a name="one-to-many-relationships"></a>Egy-a-többhöz kapcsolatok
Az üzleti tartomány objektumok közötti egy-a-többhöz kapcsolatok gyakran előfordulnak: például egy részlegnek sok alkalmazottja van. A Table szolgáltatásban többféleképpen is megvalósíthat egy-a-többhöz kapcsolatokat, amelyek mindegyike az adott forgatókönyv szempontjából releváns előnyökkel és hátrányokkal jár.  

Vegyünk példát egy nagy multinacionális vállalatra, amelyben több tízezer részleg és alkalmazott idomított, ahol minden részlegnek sok alkalmazottja van, és minden egyes alkalmazott egy adott részleghez van társítva. Az egyik megközelítés az, hogy tárolja a különböző szervezeti egységek és a munkavállalói szervezetek, mint például ezek:  


![Külön részleg- és alkalmazottentitások tárolása](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Ez a példa implicit egy-a-többhöz kapcsolatot mutat be a típusok között a **PartitionKey** érték alapján. Minden részlegnek sok alkalmazottja lehet.  

Ebben a példában egy részlegentitást és annak kapcsolódó alkalmazotti entitásait is megjeleníti ugyanazon a partíción. Választhat, hogy különböző partíciókat, táblákat vagy akár tárfiókokat használ a különböző entitástípusokhoz.  

Egy másik módszer az adatok denormalizálása és csak a denormalizált részlegadatokkal rendelkező alkalmazottentitások tárolása, ahogy az a következő példában látható. Ebben a konkrét esetben ez a denormalizált megközelítés nem feltétlenül a legjobb, ha követelmény, hogy képes legyen megváltoztatni a részleteket a részlegvezetője, mert ehhez frissítenie kell minden alkalmazott a részlegen.  

![Alkalmazotti entitás](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

További információt az útmutató [későbbi, denormalizációs mintájában](table-storage-design-patterns.md#denormalization-pattern) talál.  

Az alábbi táblázat összefoglalja az egy-a-többhöz kapcsolattal rendelkező alkalmazotti és részlegentitások tárolására vonatkozó, fent vázolt megközelítések előnyeit és hátrányait. Azt is figyelembe kell venni, hogy milyen gyakran várható, hogy különböző műveleteket hajtson végre: elfogadható lehet, hogy egy olyan terv, amely drága műveletet tartalmaz, ha ez a művelet csak ritkán fordul elő.  

<table>
<tr>
<th>Módszer</th>
<th>Előnyök</th>
<th>Hátrányok</th>
</tr>
<tr>
<td>Különálló entitástípusok, ugyanaz a partíció, ugyanaz a tábla</td>
<td>
<ul>
<li>A részleg entitást egyetlen művelettel frissítheti.</li>
<li>Az EGT-vel a konzisztencia fenntartásához akkor tarthat fenn konzisztenciát, ha egy alkalmazott entitás frissítésekelésekekén/beszúrásakor/törlésekor módosítania kell egy részlegentitást. Ha például az egyes részlegek osztályának számát tartja karban.</li>
</ul>
</td>
<td>
<ul>
<li>Előfordulhat, hogy bizonyos ügyféltevékenységekhez egy alkalmazottat és egy részlegentitást is be kell olvasnia.</li>
<li>A tárolási műveletek ugyanabban a partíción történnek. Nagy tranzakciós volumen mellett ez hotspotot eredményezhet.</li>
<li>EGT-vel nem helyezhet át alkalmazottat új részlegre.</li>
</ul>
</td>
</tr>
<tr>
<td>Különálló entitástípusok, különböző partíciók, táblák vagy tárfiókok</td>
<td>
<ul>
<li>A részleg entitást vagy alkalmazottentitást egyetlen művelettel frissítheti.</li>
<li>Nagy tranzakciós kötetek, ez segíthet a terhelés tágíthatja a további partíciók között.</li>
</ul>
</td>
<td>
<ul>
<li>Előfordulhat, hogy bizonyos ügyféltevékenységekhez egy alkalmazottat és egy részlegentitást is be kell olvasnia.</li>
<li>Az EGT-k nem használhatók a konzisztencia fenntartásához, amikor frissít/beszúr/töröl egy alkalmazottat, és frissít egy részleget. Például egy alkalmazott számának frissítése egy részleg entitásban.</li>
<li>EGT-vel nem helyezhet át alkalmazottat új részlegre.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalizálás egyetlen entitástípussá</td>
<td>
<ul>
<li>Egyetlen kéréssel minden szükséges információt lekérhet.</li>
</ul>
</td>
<td>
<ul>
<li>A konzisztencia fenntartása költséges lehet, ha frissítenie kell a részleg adatait (ehhez a részleg összes alkalmazottjának frissítését kell igényelnie).</li>
</ul>
</td>
</tr>
</table>

Az adott alkalmazási forgatókönyvektől függ, hogy hogyan választhat a lehetőségek között, és mely előnyei és hátrányai a legjelentősebbek. Például milyen gyakran módosítja a részlegentitásokat; az összes alkalmazotti lekérdezéshez szükség van a további részlegi információkra; milyen közel van a méretezhetőségi korlátok a partíciók vagy a tárfiók?  

## <a name="one-to-one-relationships"></a>Egy-az-egyhez kapcsolatok
A tartományi modellek tartalmazhatnak entitások közötti egy-az-egyhez kapcsolatokat. Ha egy-az-egyhez kapcsolatot kell megvalósítania a Table szolgáltatásban, azt is meg kell választania, hogy miként kapcsolja össze a két kapcsolódó entitást, amikor mindkettőt be kell olvasnia. Ez a hivatkozás lehet implicit, a kulcsértékekben lévő konvención alapuló, vagy explicit, ha egy hivatkozást **partitionkey** és **RowKey** értékek formájában tárol az egyes entitásokban a kapcsolódó entitással. A kapcsolódó entitások ugyanabban a partícióban való tárolásáról az [Egy-a-többhöz kapcsolatok](#one-to-many-relationships)című szakaszban található.  

Vannak olyan megvalósítási szempontok is, amelyek arra késztethetik, hogy egy-az-egyhez kapcsolatokat valósítson meg a Table szolgáltatásban:  

* Nagy entitások kezelése (további információ: [Nagy entitások mintázata](table-storage-design-patterns.md#large-entities-pattern)).  
* Hozzáférés-vezérlés megvalósítása (további információt a Hozzáférés szabályozása megosztott hozzáférésű aláírásokkal című témakörben talál).  

## <a name="join-in-the-client"></a>Csatlakozás az ügyfélhez
Bár a Table szolgáltatásban a kapcsolatok modellezésére is van mód, ne felejtse el, hogy a Table szolgáltatás használatának két fő oka a méretezhetőség és a teljesítmény. Ha úgy találja, hogy sok olyan kapcsolatot modellez, amely veszélyezteti a megoldás teljesítményét és méretezhetőségét, kérdezze meg magától, hogy szükség van-e az összes adatkapcsolat nak a táblatervbe való felépítésére. Ha hagyja, hogy az ügyfélalkalmazás végrehajtsa a szükséges illesztéseket, egyszerűsítse a tervet, és javítsa a megoldás méretezhetőségét és teljesítményét.  

Ha például olyan kis táblákkal rendelkezik, amelyek nem gyakran változnak, akkor ezeket az adatokat egyszer bekeresheti és gyorsítótárazhatja az ügyfélen. Ezzel elkerülhetők az ismétlődő körbeutak, hogy ugyanazokat az adatokat beolvashassa. Az ebben az útmutatóban megvizsgált példákban egy kis szervezet részlegei valószínűleg kicsik, és ritkán változnak, így jó jelöltaz olyan adatokhoz, amelyeket az ügyfélalkalmazás egyszer letölthet, és gyorsítótárazni lehet az adatok kereséséhez.  

## <a name="inheritance-relationships"></a>Öröklési kapcsolatok
Ha az ügyfélalkalmazás az öröklési kapcsolat részét képező osztályok készletét használja az üzleti entitások képviseletére, könnyen megőrizheti ezeket az entitásokat a Table szolgáltatásban. Előfordulhat például, hogy az ügyfélalkalmazásban a következő osztályok vannak definiálva, ahol a **Személy** egy absztrakt osztály.

![Absztrakt személy osztály](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

A Table szolgáltatás két konkrét osztályának példányait egyetlen Személy tábla használatával is megőrizheti, amelyek az így néznek ki:  

![Személy tábla](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Ha többet szeretne tudni arról, hogy miként dolgozhat több entitástípussal ugyanabban a táblában az ügyfélkódban, olvassa el az útmutató későbbi, Heterogén entitástípusokkal való együttműködés című szakaszát. Ez példákat tartalmaz arra, hogyan ismerheti fel az entitástípust az ügyfélkódban.  


## <a name="next-steps"></a>További lépések

- [Táblatervezési minták](table-storage-design-patterns.md)
- [Tervezés lekérdezéshez](table-storage-design-for-query.md)
- [Táblaadatok titkosítása](table-storage-design-encrypt-data.md)
- [Tervezés adatmódosításhoz](table-storage-design-for-modification.md)
