---
title: Szószedet - Face API-szolgáltatás
titleSuffix: Azure Cognitive Services
description: A szószedet megadja azoknak a kifejezéseknek a magyarázatát, amelyekkel a Face API-szolgáltatással dolgozva találkozhat.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 17167271df81914c76882fc4d06717043e1befc1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214296"
---
# <a name="glossary"></a>Szószedet

## <a name="a"></a>A

#### <a name="attributes"></a>Attribútumok

Attribútumok megadása nem kötelező a a [észlelési](#Detection-Face-Detection) eredményezi, mint például [életkor](#Age-Attribute), [nemek](#Gender-Attribute), [fő testtartás](#Head-Pose-Attribute), [arcfelismerési haj](#Facial-Hair-Attribute), [mosolygó](#Smile-Attribute).
A lehet beszerezni a [észlelési](#Detection-Face-Detection) API a lekérdezési paraméterek megadásával: returnFaceAttributes. Attribútumok adjon további adatok vonatkozó kiválasztott [arcok](#Face); mellett a [a face ID](#Face-ID) és a [téglalap](#Face-Rectangle).

További részletekért tekintse meg az útmutató [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="age-attribute"></a>Kor (attribútum)

Kor az egyik a [attribútumok](#Attributes) , amely leírja, hogy egy adott arcfelismerési kora. Az életkor attribútum nem kötelező a [észlelési](#Detection-Face-Detection) eredményeket, és szabályozhatja az egy [észlelési](#Detection-Face-Detection) kérelem a returnFaceAttributes paraméter megadásával.

További részletekért tekintse meg az útmutató [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="b"></a>B

## <a name="c"></a>C

#### <a name="candidate"></a>Candidate

A deduplikációra lényegében [azonosító](#Identification) eredmények (pl. azonosított személyek és az észleléseket megbízhatósága). Egy jelölt képviseli a [PersonID](#Person-ID) és [megbízhatósági](#Confidence), jelezve, hogy a személy magabiztosan magas szintű azonosítja.

További részletekért tekintse meg az útmutató [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="confidence"></a>Megbízhatóság

Megbízhatóság: egy mérték, hogy felfedné az koszinuszhasonlóságát [arcok](#Face) vagy [személy](#Person) a numerikus értékek – szabályban használt [azonosító](#Identification), és [ ellenőrzési](#Verification) a keresett, azonosította és ellenőrzött eredmények hasonlóságokat jelzi.

További részletekért tekintse meg a következő útmutatókat: [A Face – keresés hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [arc - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>Észlelés és Arcfelismerés is használható

Arcfelismerés képeken arcok megkeresése művelet. A felhasználók töltsön fel egy képet, vagy adjon meg egy kép URL-címe a kérelemben. Az észlelt arcok a visszaadott [azonosítók között](#Face-ID) jelzi a Face API egyedi azonosítóval. Téglalapok képpont, valamint a választható a lemezkép face helyének jelzése [attribútumok](#Attributes) például minden lap [életkor](#Age-Attribute), [nemek](#Gender-Attribute), [fő testtartás ](#Head-Pose-Attribute), [arcfelismerési haj](#Facial-Hair-Attribute) és [mosolygó](#Smile-Attribute).

További részletekért tekintse meg az útmutató [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

## <a name="f"></a>F

#### <a name="face"></a>Arcfelismerés

Face a kapcsolódó észlelt arcok a Face API-ból származó eredményeket egy egységes kifejezés. Végső soron face képviseli egy egységes identitás ([Face ID](#Face-ID)), egy meghatározott régióban képeken ([négyszög meghatározása](#Face-Rectangle)), és a kapcsolódó további face [attribútumok](#Face-Attributes-Facial-Attributes), például [életkor](#Age-Attribute), [nemek](#Gender-Attribute), [arcrész](#Face-Landmarks-Facial-Landmarks) és [fő testtartás](#Head-Pose-Attribute). Ezenkívül arcok a adhatók vissza [észlelési](#Detection-Face-Detection).

További részletekért tekintse meg az útmutató [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-api"></a>Face API

A Face API egy felhőalapú API-t, amely a leginkább korszerű algoritmusokat biztosít a arcok észlelése és felismerése. A Face API fő funkcióit két kategóriába oszthatók: face [észlelési](#Detection-Face-Detection) a [attribútumok](#Face-Attributes-Facial-Attributes), és az arcok [felismerés](#Recognition).

További részletekért tekintse meg a következő útmutatókat: [A Face API – áttekintés](./Overview.md), [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [arc – keresés hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [arc - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [ A Face – ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="face-attributesfacial-attributes"></a>Attribútumok és videofelismerést, arc Arctulajdonságok

Lásd: [attribútumok](#Attributes).

#### <a name="face-id"></a>Arc azonosítója

Face ID származik a [észlelési](#Detection-Face-Detection) eredményt, egy karakterlánc jelöli, amelyben egy [face](#Face) a [Face API](#Face-API).

További részletekért tekintse meg az útmutató [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-landmarksfacial-landmarks"></a>Arcrészek arcrész és videofelismerést, arc

Arcrész megadása nem kötelező a a [észlelési](#Detection-Face-Detection) eredményét, amely szemantikai arcfelismerési pontok, például a szemet, orr és szájához beszéd (szemlélteti az alábbi ábra). Arcrész vezérelhető a [észlelési](#Detection-Face-Detection) a logikai szám returnFaceLandmarks szerint. Ha returnFaceLandmarks igaz értékre van beállítva, a visszaadott arcok tereptárgyak attribútumokat kell.

További részletekért tekintse meg az útmutató [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>Négyszög meghatározása

Négyszög meghatározása származik a [észlelési](#Detection-Face-Detection) eredményeket, amely egy függőleges téglalap (balra, felső, szélesség, magasság) képeken (képpontban). A bal felső sarkában egy [face](#Face) (balra, top), szélességét és magasságát, mellett azt jelzi, hogy face méretek az x és y tengely, illetve.

További részletekért tekintse meg az útmutató [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facial-hair-attribute"></a>Arcfelismerési haj (attribútum)

Arcfelismerési haj az egyik a [attribútumok](#Attributes) a rendelkezésre álló arcok arcfelismerési haj hossza leírására szolgál. Az arcfelismerési haj attribútum nem kötelező a [észlelési](#Detection-Face-Detection) eredményeket, és szabályozhatja az egy [észlelési](#Detection-Face-Detection) returnFaceAttributes kérelmét. Ha returnFaceAttributes tartalmaz "facialHair", a visszaadott arcok arcfelismerési haj attribútumokat kell.

További részletekért tekintse meg az útmutató [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facelist"></a>FaceList

FaceList gyűjteménye, [PersistedFace](#PersistedFace) és egysége [hasonló](#Find-Similar). Egy FaceList tartalmaz egy [FaceList azonosító](#FaceList-ID), valamint a más attribútumok például [neve](#Name) és [felhasználói adatok](#UserData-User-Data).

További részletekért tekintse meg a következő útmutatókat: [FaceList - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="facelist-id"></a>FaceList azonosítója

FaceList ID azonosítóként használt felhasználó által megadott karakterlánc egy [FaceList](#FaceList). A FaceList Azonosítójú előfizetésen belül egyedinek kell lennie.

További részletekért tekintse meg a következő útmutatókat: [FaceList - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="find-similar"></a>Hasonló keresése

Az API segítségével hasonló arcokat keres egy téglalapot gyűjtemény alapján lekérdezés. Lekérdezési arc és face gyűjtemények jelentésekként jelennek meg [azonosítók között](#Face-ID) vagy [FceList azonosító](#FaceList-ID)/[LargeFaceList azonosító](#LargeFaceList-ID) a kérésben. Visszaadott eredmények keresett hasonló arcokat keres, által képviselt [azonosítók között](#Face-ID) vagy [PersistedFace azonosítók](#PersistedFace-ID).

További részletekért tekintse meg a következő útmutatókat: [A Face – keresés hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="gender-attribute"></a>Gender (attribútum)

Nemek az egyik a [attribútumok](#Attributes) a rendelkezésre álló téglalapot a nemek leírására szolgál. A nemek attribútum nem kötelező a [észlelési](#Detection-Face-Detection) eredményeket, és szabályozhatja az egy [észlelési](#Detection-Face-Detection) returnFaceAttributes kérelmét. Ha returnfaceAttributes tartalmaz "gender", a visszaadott arcok nemek attribútumokat kell.

További részletekért tekintse meg az útmutató [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="grouping"></a>Csoportosítás

Arcok csoportosítása gyűjteménye, arcfelismerési Hasonlóságok alapján arcok csoportosítása. A face ID gyűjtemények a kérelemben szereplő arcok gyűjtemények jelzik. Az egyesülés következtében hasonló arcokat keres vannak csoportosítva [csoportok](#Groups), és az arcok, amelyek nem hasonlít bármilyen más face együtt egyesítésekor rendezetlen csoportként. Van a legtöbb ilyen [rendezetlen csoport](#Messy-Group) a csoportosítási eredményei.

További részletekért tekintse meg az útmutató [arc - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="groups"></a>Csoportok

Csoportok származnak az [csoportosítási](#Grouping) eredményeket. Minden csoport hasonló arcokat keres, amelyben arcok változáskövető által gyűjteményét tartalmazza [azonosítók között](#Face-ID).

További részletekért tekintse meg az útmutató [arc - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>A fő jelentenek (attribútum)

A fő testtartás az egyik a [attribútumok](#Attributes) , hogy jelöli a 3D terület szerint üzembe, a terv lényegét és a kitérése szögek tájolás között, az alábbi ábrán látható módon. Az érték tartományait állítja, és azok [-180, 180] és [-90, 90] yaw fokban. A jelenlegi verzióban felébresztve észlelési által visszaadott érték mindig 0. A fő testtartás attribútum nem kötelező a [észlelési](#Detection-Face-Detection) eredményeket, és szabályozhatja az egy [észlelési](#Detection-Face-Detection) kérelem a returnFaceAttributes paraméterben. ReturnFaceAttributes paraméter tartalmazza a "headPose", a visszaadott arcok attribútumok megoldás fő fog rendelkezni.

További részletekért tekintse meg az útmutató [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>Azonosító

Az azonosító egy vagy több olyan LargePersonGroup/is lehet PersonGroup arcokat azonosításához.
A [is lehet PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup) gyűjteménye [személyek](#Person).
Arcok, és a LargePersonGroup/is lehet PersonGroup képviseli [azonosítók között](#Face-ID) és [LargePersonGroup azonosítók](#LargePersonGroup-ID)/[is lehet PersonGroup azonosítók](#PersonGroup-ID) , illetve a a kérést.
Meghatározott eredmények [jelöltek](#Candidate), által képviselt [személyek](#Person) magabiztosan.
A bemeneti adatok több arcok külön-külön számít, és minden saját azonosított eredményt fog rendelkezni.

> [!NOTE]
> A LargePersonGroup/is lehet PersonGroup tájékoztatni kell sikeresen azonosító előtt. Ha a LargePersonGroup/is lehet PersonGroup nem be van tanítva, vagy a betanítási [állapot](#Status-Train) nem jelenik meg, mint a "sikeres" (azaz "fut", "sikertelen" vagy "timeout"), a kérés-válasz a rendszer 400-as.
> 

További részletekért tekintse meg a következő útmutatókat: [A Face – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [személy LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [is lehet PersonGroup Személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [is lehet PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="isidentical"></a>IsIdentical

IsIdentical egy logikai típusú mezőt a [ellenőrzési](#Verification) eredményeket, amely azt jelzi, hogy két arc ugyanazt az embert.

További részletekért tekintse meg az útmutató [arc - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="j"></a>C

## <a name="k"></a>K

## <a name="l"></a>L

#### <a name="landmarks"></a>Arcrész

Lásd: [arcrész arc](#Face-Landmarks-Facial-Landmarks).

#### <a name="largefacelist"></a>LargeFaceList

LargeFaceList gyűjteménye, [PersistedFace](#PersistedFace) és egysége [hasonló](#Find-Similar). Egy LargeFaceList tartalmaz egy [LargeFaceList azonosító](#LargeFaceList-ID), valamint a más attribútumok például [neve](#Name) és [felhasználói adatok](#UserData-User-Data).

További részletekért tekintse meg a következő útmutatókat: [LargeFaceList – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList – lista Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="largefacelist-id"></a>LargeFaceList azonosítója

LargeFaceList ID azonosítóként használt felhasználó által megadott karakterlánc egy [LargeFaceList](#LargeFaceList). A LargeFaceList Azonosítójú előfizetésen belül egyedinek kell lennie.

További részletekért tekintse meg a következő útmutatókat: [LargeFaceList – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup gyűjteménye, [személyek](#Person) és egysége [azonosító](#Identification). Egy LargePersonGroup tartalmaz egy [LargePersonGroup azonosító](#LargePersonGroup-ID), valamint a más attribútumok például [neve](#Name) és [felhasználói adatok](#UserData-User-Data).

További részletekért tekintse meg a következő útmutatókat: [LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [LargePersonGroup személy - listában](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="largepersongroup-id"></a>LargePersonGroup azonosítója

LargePersonGroup ID azonosítóként használt felhasználó által megadott karakterlánc egy [LargePersonGroup](#LargePersonGroup). A LargePersonGroup Azonosítójú előfizetésen belül egyedinek kell lennie.

További részletekért tekintse meg a következő útmutatókat: [LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="messy-group"></a>Rendezetlen csoport

Rendezetlen csoport származik a [csoportosítási](#Grouping) eredmények; nem hasonlít bármilyen más face arcokat tartalmazó. Rendezetlen csoport minden egyes face jelzi a [a face ID](#Face-ID).

További részletekért tekintse meg az útmutató [arc - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>N

#### <a name="name-person"></a>Név (személy)

Név felhasználóbarát leíró karakterláncát [személy](#Person). Ellentétben a [személy azonosítója](#Person-ID), személyek nevét is kettőzhető csoporton belül.

További részletekért tekintse meg a következő útmutatókat: [Személy LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [személy LargePersonGroup – első](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [személy is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [személy is lehet PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="name-largepersongrouppersongroup"></a>Név (LargePersonGroup/is lehet PersonGroup)

Név is felhasználóbarát leíró karakterláncát [LargePersonGroup](#LargePersonGroup)/[is lehet PersonGroup](#PersonGroup). Ellentétben a [LargePersonGroup azonosító](#LargePersonGroup-ID)/[is lehet PersonGroup azonosító](#PersonGroup-ID), LargePersonGroups/Persongroup nevét is duplikálható az egy előfizetésen belül.

További részletekért tekintse meg a következő útmutatókat: [LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [is lehet PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="o"></a>O

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace olyan adatstruktúra Face API-ban. PersistedFace tartalmaz egy [PersistedFace azonosító](#PersistedFace-ID), valamint a más attribútumok például [neve](#Name), és [felhasználói adatok](#UserData-User-Data).

További részletekért tekintse meg a következő útmutatókat: [LargeFaceList – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [személy LargePersonGroup – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [személy is lehet PersonGroup – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person-id"></a>Személy azonosítója

Személy azonosítója jön létre, amikor egy [PersistedFace](#PersistedFace) létrehozása sikerült. Egy karakterlánc jön létre, amelyek ezt a Face [Face API](#Face-API).

További részletekért tekintse meg a következő útmutatókat: [LargeFaceList – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [személy LargePersonGroup – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [személy is lehet PersonGroup – Face hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person"></a>Személy

Személy a Face API-ban felügyelt adatstruktúra. Személy tartalmaz egy [személy azonosítója](#Person-ID), valamint a más attribútumok például [neve](#Name), gyűjteménye [PersistedFace](#PersistedFace), és [felhasználói adatok](#UserData-User-Data).

További részletekért tekintse meg a következő útmutatókat: [Személy LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [személy LargePersonGroup – első](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [személy is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [személy is lehet PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="person-id"></a>Személy azonosítója

Személy azonosítója jön létre, amikor egy [személy](#Person) létrehozása sikerült. Egy karakterlánc jön létre, amelyek ennek a személynek a [Face API](#Face-API).

További részletekért tekintse meg a következő útmutatókat: [Személy LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [személy LargePersonGroup – első](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [személy is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [személy is lehet PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="persongroup"></a>PersonGroup

Is lehet PersonGroup gyűjteménye, [személyek](#Person) és egysége [azonosító](#Identification). Egy is lehet PersonGroup tartalmaz egy [is lehet PersonGroup azonosító](#PersonGroup-ID), valamint a más attribútumok például [neve](#Name) és [felhasználói adatok](#UserData-User-Data).

További részletekért tekintse meg a következő útmutatókat: [Is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [is lehet PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [személy is lehet PersonGroup - listában](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="persongroup-id"></a>Is lehet PersonGroup azonosítója

Is lehet PersonGroup ID azonosítóként használt felhasználó által megadott karakterlánc egy [is lehet PersonGroup](#PersonGroup). A csoport azonosítója az előfizetésen belül egyedinek kell lennie.

További részletekért tekintse meg a következő útmutatókat: [Is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [is lehet PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Megoldás (attribútum)

Lásd: [fő jelentenek](#Head-Pose-Attribute).

## <a name="q"></a>Q

## <a name="r"></a>R

#### <a name="recognition"></a>Felismerés

Elismerés el a népszerű alkalmazási terület face technológiák, például [hasonló](#Find-Similar), [csoportosítási](#Grouping), [azonosítása](#Identification),[ellenőrzése két arcokat ugyanaz-e ](#Verification).

További részletekért tekintse meg a következő útmutatókat: [A Face – keresés hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [arc - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [arc - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Téglalap (Face)

Lásd: [négyszög meghatározása](#Face-Rectangle).

## <a name="s"></a>S

#### <a name="smile-attribute"></a>Mosolyogjon (attribútum)

Mosoly az egyik a [attribútumok](#Attributes) a mosoly kifejezés a rendelkezésre álló téglalapot leírására szolgál. A mosoly attribútum nem kötelező a [észlelési](#Detection-Face-Detection) eredményeket, és szabályozhatja az egy [észlelési](#Detection-Face-Detection) returnFaceAttributes kérelmét. Ha returnFaceAttributes tartalmaz "smajlíka", akkor a visszaadott arcok mosoly tulajdonságokkal fog rendelkezni.

További részletekért tekintse meg az útmutató [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="similar-face-searching"></a>Hasonló arcok keresése

Lásd: [hasonló](#Find-Similar).

#### <a name="status-train"></a>Állapot (Train)

Állapot: karakterlánc, amellyel ismertetik az eljárás [LargeFaceList/LargePersonGroups/Persongroup képzési](#Train), beleértve a "notstarted", "fut", "sikeres", "sikertelen".

További részletekért tekintse meg az útmutató [LargeFaceList – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [is lehet PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Előfizetői azonosító

Az előfizetői azonosító karakterlánc, amely meg kell adnia egy lekérdezési karakterlánc paramétereként, annak érdekében, hogy a Face API meghívásához. Az előfizetési kulcs után jelentkezzen be a Microsoft Cognitive Services portálon található saját előfizetések lapon. Az egyes előfizetésekhez tartozó két kulcs lesz: egy elsődleges kulcs és a egy másodlagos kulcs. Mindkét API meghívása azonos módon használható. Az előfizetési kulcsok biztonságos megőrzése, és létrehozhatja saját előfizetések lapon is bármikor előfizetési kulcsok.

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>Vonat (LargeFaceList/LargePersonGroup/is lehet PersonGroup)

Ez az API előtti feldolgozásához használt a [LargeFaceList](#LargeFaceList)/[LargePersonGroup](#LargePersonGroup)/[is lehet PersonGroup](#PersonGroup) annak biztosítása érdekében a [keresése Hasonló](#Find-Similar)/[azonosító](#Identification) teljesítményét. A képzésekre itt nem működik, ha vagy a [képzési állapot](#Status-Train) nem jelenik meg, sikeres volt, mert ez is lehet PersonGroup azonosítója hibát eredményez.

További részletekért tekintse meg a következő útmutatókat: [LargeFaceList – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [is lehet PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [arc - azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>Felhasználói adatok és felhasználói adatok

Felhasználói adatokat az társított további információt [személy](#Person) és [is lehet PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup). Felhasználói adatok értéke a felhasználók adatokat könnyebben használja, és ne felejtse el.

További részletekért tekintse meg a következő útmutatókat: [LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – frissítés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [személy LargePersonGroup – hozzon létre](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup személy - frissítés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [Is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [is lehet PersonGroup - frissítés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [személy is lehet PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [személy is lehet PersonGroup - frissítés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="verification"></a>Ellenőrzés

Ez az API segítségével győződjön meg arról, hogy két arc ugyanazok, vagy nem. Mindkét arcok jelennek meg, a kérelemben szereplő azonosítók között. Ellenőrzött eredmények tartalmaznak egy logikai típusú mező ([isIdentical](#Is-Identical)) jelző azonos, amennyiben az értéke igaz, és a egy számmező ([megbízhatósági](#Confidence)) megbízhatósági szintjét jelzi.

További részletekért tekintse meg az útmutató [arc - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="w"></a>W

## <a name="x"></a>X

## <a name="y"></a>I

## <a name="z"></a>Z
