---
title: Szószedet - Face API-szolgáltatás
titleSuffix: Azure Cognitive Services
description: A szószedet megadja azoknak a kifejezéseknek a magyarázatát, amelyekkel a Face API-szolgáltatással dolgozva találkozhat.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: d627c3c4419affa0d71cdb23df945c96d9fd7585
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652433"
---
# <a name="glossary"></a>Szószedet

## <a name="a"></a>A

#### <a name="attributes"></a>Attribútumok

Attribútumok észlelhető, mint például opcionális arcfelismerési funkciókat [életkor](#age-attribute), [nemek](#gender-attribute), [fő testtartás](#head-pose-attribute), [arcfelismerési haj](#facial-hair-attribute), és [smajlíka](#smile-attribute). Ezek szerezhető az észlelési API megadásával a _returnFaceAttributes_ lekérdezési paraméter.

Arctulajdonságok teljes listájáért tekintse meg a dokumentációja: [A Face – észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="age-attribute"></a>Kor (attribútum)

Kor az egyik a [attribútumok](#attributes) , amely leírja, hogy egy adott arcfelismerési kora. Az életkor attribútum nem kötelező megadni az észlelési eredmények között, és a returnFaceAttributes paraméter megadásával észlelési kéréssel szabályozható.

További információ a referencia-dokumentációjában talál: [A Face – észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="c"></a>C

#### <a name="candidate"></a>Candidate

A deduplikációra lényegében [azonosító](#identification) eredmények (pl. azonosított személyek és az észleléseket megbízhatósága). Egy jelölt képviseli a [PersonID](#person-id) és [megbízhatósági](#confidence), jelezve, hogy a személy magabiztosan magas szintű azonosítja.

További információ a referencia-dokumentációjában talál: [A Face – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="confidence"></a>Megbízhatóság

Megbízhatóság: egy mérték, hogy felfedné az koszinuszhasonlóságát [arcok](#face) vagy [személy](#person) a numerikus értékek – szabályban használt [azonosító](#identification), és [ ellenőrzési](#verification) a keresett, azonosította és ellenőrzött eredmények hasonlóságokat jelzi.

További információ a referencia-dokumentációjában talál: [A Face – keresés hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [arc - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>Észlelés és Arcfelismerés is használható

Arcfelismerés képeken arcok megkeresése művelet. A felhasználók töltsön fel egy képet, vagy adjon meg egy kép URL-címe a kérelemben. Az észlelt arcok a visszaadott [azonosítók között](#face-id) jelzi a Face API egyedi azonosítóval. Téglalapok képpont, valamint a választható a lemezkép face helyének jelzése [attribútumok](#attributes) például minden lap [életkor](#age-attribute), [nemek](#gender-attribute), [fő testtartás ](#head-pose-attribute), [arcfelismerési haj](#facial-hair-attribute) és [mosolygó](#smile-attribute).

További információ a referencia-dokumentációjában talál: [A Face – észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

#### <a name="emotion-attribute"></a>Érzelemfelismerési (attribútum)

Érzelemfelismerési az egyik a [attribútumok arc](#attributes). Történő lekérdezéskor, érzelmeket és azok az adott személyt az észlelési megbízhatósági listáját adja vissza. Megbízhatósági pontszámok van normalizálva: közötti összes érzelmek pontszámokat felveszi, akár egy. A visszaadott érzelmek boldogság, szomorúság, semleges, düh, megvetés, undor, meglepő és lehetőségének.

További információ a referencia-dokumentációjában talál: [A Face – észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="f"></a>F

#### <a name="face"></a>Arcfelismerés

Face a kapcsolódó észlelt arcok a Face API-ból származó eredményeket egy egységes kifejezés. Végső soron face képviseli egy egységes identitás ([Face ID](#face-id)), egy meghatározott régióban képeken ([négyszög meghatározása](#face-rectangle)), és a face kapcsolatos további attribútumok, például [életkor](#age-attribute), [nemek](#gender-attribute), arcrész és [fő testtartás](#head-pose-attribute). Ezenkívül arcok észlelése a adhatók vissza.

További információ a referencia-dokumentációjában talál: [A Face – észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-api"></a>Face API

A Face API egy felhőalapú API-t, amely a leginkább korszerű algoritmusokat biztosít a arcok észlelése és felismerése. A Face API fő funkcióit két kategóriába oszthatók: arcfelismerés attribútumokkal, és a face [felismerés](#recognition).

További információ a referencia-dokumentációjában talál: [A Face API – áttekintés](./Overview.md), [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [arc – keresés hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [arc - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [ A Face – ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="face-attributesfacial-attributes"></a>Attribútumok és videofelismerést, arc Arctulajdonságok

Lásd: [attribútumok](#attributes).

#### <a name="face-id"></a>Arc azonosítója

Face ID van származtatva, amelyben egy karakterláncot jelöl az észlelési eredmények egy [face](#face) a [Face API](#face-api).

További információ a referencia-dokumentációjában talál: [A Face – észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-landmarksfacial-landmarks"></a>Arcrészek arcrész és videofelismerést, arc

Arcrész megadása nem kötelező a észlelési eredmények; melyek a szemantikai arcfelismerési pontok, például a szemet, orr és szájához beszéd (szemlélteti az alábbi ábra). A logikai szám returnFaceLandmarks arcrész szabályozhatja észlelési kéréssel. Ha returnFaceLandmarks igaz értékre van beállítva, a visszaadott arcok tereptárgyak attribútumokat kell.

További információ a referencia-dokumentációjában talál: [A Face – észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>Négyszög meghatározása

Négyszög meghatározása származó észlelésének eredménye, azaz egy függőleges téglalap (balra, felső, szélesség, magasság) képeken (képpontban). A bal felső sarkában egy [face](#face) (balra, top), szélességét és magasságát, mellett azt jelzi, hogy face méretek az x és y tengely, illetve.

További információ a referencia-dokumentációjában talál: [A Face – észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facial-hair-attribute"></a>Arcfelismerési haj (attribútum)

Arcfelismerési haj az egyik a [attribútumok](#attributes) a rendelkezésre álló arcok arcfelismerési haj hossza leírására szolgál. Az arcfelismerési haj attribútum nem kötelező megadni az észlelési eredmények között, és returnFaceAttributes észlelési kéréssel szabályozhatók. Ha returnFaceAttributes tartalmaz "facialHair", a visszaadott arcok arcfelismerési haj attribútumokat kell.

További információ a referencia-dokumentációjában talál: [A Face – észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facelist"></a>FaceList

FaceList gyűjteménye, [PersistedFace](#persistedface) és egysége [hasonló](#find-similar). Egy FaceList tartalmaz egy [FaceList azonosító](#facelist-id), valamint a más attribútumok, például a nevét és a felhasználói adatokat.

További információ a referencia-dokumentációjában talál: [FaceList - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="facelist-id"></a>FaceList azonosítója

FaceList ID azonosítóként használt felhasználó által megadott karakterlánc egy [FaceList](#facelist). A FaceList Azonosítójú előfizetésen belül egyedinek kell lennie.

További információ a referencia-dokumentációjában talál: [FaceList - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="find-similar"></a>Hasonló keresése

Az API segítségével hasonló arcokat keres egy téglalapot gyűjtemény alapján lekérdezés. Lekérdezési arc és face gyűjtemények jelentésekként jelennek meg [azonosítók között](#face-id) vagy [FceList azonosító](#facelist-id)/[LargeFaceList azonosító](#largefacelist-id) a kérésben. Visszaadott eredmények keresett hasonló arcokat keres, által képviselt [azonosítók között](#face-id) vagy PersistedFace azonosítók.

További információ a referencia-dokumentációjában talál: [A Face – keresés hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="gender-attribute"></a>Gender (attribútum)

Nemek az egyik a [attribútumok](#attributes) a rendelkezésre álló téglalapot a nemek leírására szolgál. A nemek attribútum nem kötelező megadni az észlelési eredmények között, és returnFaceAttributes észlelési kéréssel szabályozhatók. Ha returnfaceAttributes tartalmaz "gender", a visszaadott arcok nemek attribútumokat kell.

További információ a referencia-dokumentációjában talál: [A Face – észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="grouping"></a>Csoportosítás

Arcok csoportosítása gyűjteménye, arcfelismerési Hasonlóságok alapján arcok csoportosítása. A face ID gyűjtemények a kérelemben szereplő arcok gyűjtemények jelzik. Az egyesülés következtében hasonló arcokat keres vannak csoportosítva [csoportok](#groups), és az arcok, amelyek nem hasonlít bármilyen más face együtt egyesítésekor rendezetlen csoportként. Van a legtöbb ilyen [rendezetlen csoport](#messy-group) a csoportosítási eredményei.

További információ a referencia-dokumentációjában talál: [A Face - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="groups"></a>Csoportok

Csoportok származnak az [csoportosítási](#grouping) eredményeket. Minden csoport hasonló arcokat keres, amelyben arcok változáskövető által gyűjteményét tartalmazza [azonosítók között](#face-id).

További információ a referencia-dokumentációjában talál: [A Face - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>A fő jelentenek (attribútum)

A fő testtartás az egyik a [attribútumok](#attributes) , hogy jelöli a 3D terület szerint üzembe, a terv lényegét és a kitérése szögek tájolás között, az alábbi ábrán látható módon. Az érték tartományait állítja, és azok [-180, 180] és [-90, 90] yaw fokban. A jelenlegi verzióban felébresztve észlelési által visszaadott érték mindig 0. A fő testtartás attribútum nem kötelező megadni az észlelési eredmények között, és a returnFaceAttributes paraméter által szabályozható észlelési kéréssel. ReturnFaceAttributes paraméter tartalmazza a "headPose", a visszaadott arcok attribútumok megoldás fő fog rendelkezni.

További információ a referencia-dokumentációjában talál: [A Face – észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>Azonosító

Az azonosító egy vagy több olyan LargePersonGroup/is lehet PersonGroup arcokat azonosításához.
A [is lehet PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup) gyűjteménye [személyek](#person).
Arcok, és a LargePersonGroup/is lehet PersonGroup képviseli [azonosítók között](#face-id) és [LargePersonGroup azonosítók](#largepersongroup-id)/[is lehet PersonGroup azonosítók](#persongroup-id) , illetve a a kérést.
Meghatározott eredmények [jelöltek](#candidate), által képviselt [személyek](#person) magabiztosan.
A bemeneti adatok több arcok külön-külön számít, és minden saját azonosított eredményt fog rendelkezni.

> [!NOTE]
> A LargePersonGroup/is lehet PersonGroup tájékoztatni kell sikeresen azonosító előtt. Ha a LargePersonGroup/is lehet PersonGroup nem be van tanítva, vagy a betanítási [állapot](#status-train) nem jelenik meg, mint a "sikeres" (azaz "fut", "sikertelen" vagy "timeout"), a kérés-válasz a rendszer 400-as.
> 

További információ a referencia-dokumentációjában talál: [A Face – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [személy LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [is lehet PersonGroup Személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [is lehet PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="isidentical"></a>IsIdentical

IsIdentical egy logikai típusú mezőt a [ellenőrzési](#verification) eredményeket, amely azt jelzi, hogy két arc ugyanazt az embert.

További információ a referencia-dokumentációjában talál: [A Face – ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="l"></a>L

#### <a name="landmarks"></a>Arcrész

Tekintse meg a arcrészek.

#### <a name="largefacelist"></a>LargeFaceList

LargeFaceList gyűjteménye, [PersistedFace](#persistedface) és egysége [hasonló](#find-similar). Egy LargeFaceList tartalmaz egy [LargeFaceList azonosító](#largefacelist-id), valamint a más attribútumok, például a nevét és a felhasználói adatokat.

További információ a referencia-dokumentációjában talál: [LargeFaceList – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList – lista Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="largefacelist-id"></a>LargeFaceList azonosítója

LargeFaceList ID azonosítóként használt felhasználó által megadott karakterlánc egy [LargeFaceList](#largefacelist). A LargeFaceList Azonosítójú előfizetésen belül egyedinek kell lennie.

További információ a referencia-dokumentációjában talál: [LargeFaceList – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup gyűjteménye, [személyek](#person) és egysége [azonosító](#identification). Egy LargePersonGroup tartalmaz egy [LargePersonGroup azonosító](#largepersongroup-id), valamint a más attribútumok, például a nevét és a felhasználói adatokat.

További információ a referencia-dokumentációjában talál: [LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [LargePersonGroup személy - listában](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="largepersongroup-id"></a>LargePersonGroup azonosítója

LargePersonGroup ID azonosítóként használt felhasználó által megadott karakterlánc egy [LargePersonGroup](#largepersongroup). A LargePersonGroup Azonosítójú előfizetésen belül egyedinek kell lennie.

További információ a referencia-dokumentációjában talál: [LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="messy-group"></a>Rendezetlen csoport

Rendezetlen csoport származik a [csoportosítási](#grouping) eredmények; nem hasonlít bármilyen más face arcokat tartalmazó. Rendezetlen csoport minden egyes face jelzi a [a face ID](#face-id).

További információ a referencia-dokumentációjában talál: [A Face - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>N

#### <a name="name-person"></a>Név (személy)

Név felhasználóbarát leíró karakterláncát [személy](#person). Ellentétben a [személy azonosítója](#person-id), személyek nevét is kettőzhető csoporton belül.

További információ a referencia-dokumentációjában talál: [Személy LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [személy LargePersonGroup – első](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [személy is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [személy is lehet PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="name-largepersongrouppersongroup"></a>Név (LargePersonGroup/is lehet PersonGroup)

Név is felhasználóbarát leíró karakterláncát [LargePersonGroup](#largepersongroup)/[is lehet PersonGroup](#persongroup). Ellentétben a [LargePersonGroup azonosító](#largepersongroup-id)/[is lehet PersonGroup azonosító](#persongroup-id), LargePersonGroups/Persongroup nevét is duplikálható az egy előfizetésen belül.

További információ a referencia-dokumentációjában talál: [LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [is lehet PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace olyan adatstruktúra Face API-ban. PersistedFace a PersistedFace ID, valamint más tulajdonságok, például nevét, és a felhasználói adatokat tartalmaz.

További információ a referencia-dokumentációjában talál: [LargeFaceList – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [személy LargePersonGroup – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [személy is lehet PersonGroup – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person-id"></a>Személy azonosítója

Személy azonosítója jön létre, amikor egy [PersistedFace](#persistedface) létrehozása sikerült. Egy karakterlánc jön létre, amelyek ezt a Face [Face API](#face-api).

További információ a referencia-dokumentációjában talál: [LargeFaceList – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [személy LargePersonGroup – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [személy is lehet PersonGroup – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person"></a>Személy

Személy a Face API-ban felügyelt adatstruktúra. Személy tartalmaz egy [személy azonosítója](#person-id), valamint a más attribútumok, például nevét, a gyűjteményét [PersistedFace](#persistedface), és a felhasználói adatokat.

További információ a referencia-dokumentációjában talál: [Személy LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [személy LargePersonGroup – első](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [személy is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [személy is lehet PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="person-id"></a>Személy azonosítója

Személy azonosítója jön létre, amikor egy [személy](#person) létrehozása sikerült. Egy karakterlánc jön létre, amelyek ennek a személynek a [Face API](#face-api).

További információ a referencia-dokumentációjában talál: [Személy LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [személy LargePersonGroup – első](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [személy is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [személy is lehet PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="persongroup"></a>PersonGroup

Is lehet PersonGroup gyűjteménye, [személyek](#person) és egysége [azonosító](#identification). Egy is lehet PersonGroup tartalmaz egy [is lehet PersonGroup azonosító](#persongroup-id), valamint a más attribútumok, például a nevét és a felhasználói adatokat.

További információ a referencia-dokumentációjában talál: [Is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [is lehet PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [személy is lehet PersonGroup - listában](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="persongroup-id"></a>Is lehet PersonGroup azonosítója

Is lehet PersonGroup ID azonosítóként használt felhasználó által megadott karakterlánc egy [is lehet PersonGroup](#persongroup). A csoport azonosítója az előfizetésen belül egyedinek kell lennie.

További információ a referencia-dokumentációjában talál: [Is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [is lehet PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Megoldás (attribútum)

Lásd: [fő jelentenek](#head-pose-attribute).

## <a name="r"></a>R

#### <a name="recognition"></a>Felismerés

Elismerés el a népszerű alkalmazási terület face technológiák, például [hasonló](#find-similar), [csoportosítási](#grouping), [azonosítása](#identification),[ellenőrzése két arcokat ugyanaz-e ](#verification).

További információ a referencia-dokumentációjában talál: [A Face – keresés hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [arc - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [arc - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Téglalap (Face)

Lásd: [négyszög meghatározása](#face-rectangle).

## <a name="s"></a>S

#### <a name="similar-face-searching"></a>Hasonló arcok keresése

Lásd: [hasonló](#find-similar).

#### <a name="smile-attribute"></a>Mosolyogjon (attribútum)

Mosoly az egyik a [attribútumok](#attributes) a mosoly kifejezés a rendelkezésre álló téglalapot leírására szolgál. A mosoly attribútum nem kötelező megadni az észlelési eredmények között, és returnFaceAttributes észlelési kéréssel szabályozhatók. Ha returnFaceAttributes tartalmaz "smajlíka", akkor a visszaadott arcok mosoly tulajdonságokkal fog rendelkezni.

További információ a referencia-dokumentációjában talál: [A Face – észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="snapshot"></a>Pillanatkép

Pillanatkép egy ideiglenes távtároló bizonyos Face adattípus. A vágólap másolhat adatokat egy előfizetésből egy másikba egyfajta működik. Először a felhasználó "" egy pillanatképet készít az adatokat a forrás-előfizetés, és ezek "alkalmazza" azt egy új objektum a cél előfizetésben. 

További részletekért lásd: [Face áttelepítési útmutató](./face-api-how-to-topics/how-to-migrate-face-data.md) , valamint a [érvénybe a pillanatkép -](/rest/api/cognitiveservices/face/snapshot/take) és [a alkalmazni a pillanatkép -](/rest/api/cognitiveservices/face/snapshot/apply) referenciadokumentációt (REST).

#### <a name="status-train"></a>Állapot (Train)

Karakterlánc, amellyel ismertetik az eljárás a képzési LargeFaceList/LargePersonGroups/Persongroup, beleértve a "notstarted", "fut", "sikeres", "sikertelen" állapota.

További információ a referencia-dokumentációjában talál: [LargeFaceList – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [is lehet PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Előfizetői azonosító

Az előfizetői azonosító karakterlánc, amely meg kell adnia egy lekérdezési karakterlánc paramétereként, annak érdekében, hogy a Face API meghívásához. Az előfizetési kulcs után jelentkezzen be a Microsoft Cognitive Services portálon található saját előfizetések lapon. Az egyes előfizetésekhez tartozó két kulcs lesz: egy elsődleges kulcs és a egy másodlagos kulcs. Mindkét API meghívása azonos módon használható. Az előfizetési kulcsok biztonságos megőrzése, és létrehozhatja saját előfizetések lapon is bármikor előfizetési kulcsok.

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>Vonat (LargeFaceList/LargePersonGroup/is lehet PersonGroup)

Ez az API előtti feldolgozásához használt a [LargeFaceList](#largefacelist)/[LargePersonGroup](#largepersongroup)/[is lehet PersonGroup](#persongroup) annak biztosítása érdekében a [keresése Hasonló](#find-similar)/[azonosító](#identification) teljesítményét. A képzésekre itt nem működik, ha vagy a [képzési állapot](#status-train) nem jelenik meg, sikeres volt, mert ez is lehet PersonGroup azonosítója hibát eredményez.

További információ a referencia-dokumentációjában talál: [LargeFaceList – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [is lehet PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>Felhasználói adatok és felhasználói adatok

Felhasználói adatokat az társított további információt [személy](#person) és [is lehet PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup). Felhasználói adatok értéke a felhasználók adatokat könnyebben használja, és ne felejtse el.

További információ a referencia-dokumentációjában talál: [LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – frissítés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [személy LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup személy - frissítés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [Is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [is lehet PersonGroup - frissítés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [személy is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [személy is lehet PersonGroup - frissítés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="verification"></a>Ellenőrzés

Ez az API segítségével győződjön meg arról, hogy két arc ugyanazok, vagy nem. Mindkét arcok jelennek meg, a kérelemben szereplő azonosítók között. Eredmények tartalmazzák, egy logikai típusú mező (isIdentical) jelző azonos, amennyiben az értéke igaz, és a egy számmező ellenőrzése ([megbízhatósági](#confidence)) megbízhatósági szintjét jelzi.

További információ a referencia-dokumentációjában talál: [A Face – ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).
