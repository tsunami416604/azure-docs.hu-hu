---
title: Szószedet a Arcfelismerési API-szolgáltatáshoz |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: A szószedet kifejezések magyarázatát a Arcfelismerési API-szolgáltatás használatakor esetleg előforduló.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ec3068c013be9f2fa4ff34b1c24596e46e7c5d05
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347235"
---
# <a name="glossary"></a>Szószedet

## <a name="a"></a>A

#### <a name="Attributes"></a>Attribútumok

Attribútumok nem kötelező a a [észlelési](#Detection-Face-Detection) annak az eredménye, például a [kora](#Age-Attribute), [nemét](#Gender-Attribute), [központi jelentő](#Head-Pose-Attribute), [arcfelismerést haj](#Facial-Hair-Attribute), [mosolygó](#Smile-Attribute).
Akkor lehet lekérni a [észlelési](#Detection-Face-Detection) API a lekérdezési paraméterek megadásával: returnFaceAttributes. Attribútumok adjon további információkat vonatkozó kijelölt [lapok](#Face); kívül a [azonosító szembesülhetnek](#Face-ID) és a [téglalap](#Face-Rectangle).

További részletekért tekintse meg az útmutató [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Age-Attribute"></a>Kora (attribútum)

Kor egyike a [attribútumok](#Attributes) , amely leírja, hogy egy adott arc korát. A korszűrő attribútum megadása nem kötelező a a [észlelési](#Detection-Face-Detection) elemre, majd vezérelhető egy [észlelési](#Detection-Face-Detection) kérelmet a returnFaceAttributes paraméter megadásával.

További részletekért tekintse meg az útmutató [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="b"></a>B

## <a name="c"></a>C

#### <a name="Candidate"></a>Jelölt

Alternatívák: lényegében [azonosító](#Identification) eredmények (pl. azonosított személyek és az észlelések megbízhatósága). Egy jelölt által képviselt a [PersonID](#Person-ID) és [abban, hogy](#Confidence), amely azt jelzi, hogy a személy, amelynél az vetett bizalmat magas szintű.

További részletekért tekintse meg az útmutató [szembesülhetnek – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="Confidence"></a>Megbízhatóság

Abban, hogy egy mérték közötti hasonlóság felfedése [lapok](#Face) vagy [személy](#Person) numerikus értékeket – a használt [azonosító](#Identification), és [ ellenőrzési](#Verification) keresett, azonosított és ellenőrzött eredmények Hasonlóságok jelzi.

További részletekért tekintse meg az alábbi útmutatókban: [Arcfelismerési - hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [szembesülhetnek – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [szembesülhetnek - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="Detection-Face-Detection"></a>Észlelési/Arcfelismerési észlelése

Arcfelismerési észlelési a lapok keresése a képek művelet. A felhasználók lemezkép feltöltése vagy adjon meg egy kép URL-CÍMÉT a kérelemben. Az észlelt lapok küld vissza a rendszer [azonosítók szembesülhetnek](#Face-ID) jelző Arcfelismerési API-ban egy egyedi azonosító. A téglalap jelezze arcfelismerési képpont, valamint a választható a lemezkép [attribútumok](#Attributes) például minden lap [kora](#Age-Attribute), [nemét](#Gender-Attribute), [központi jelentő ](#Head-Pose-Attribute), [arcfelismerést haj](#Facial-Hair-Attribute) és [mosolygó](#Smile-Attribute).

További részletekért tekintse meg az útmutató [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

## <a name="f"></a>F

#### <a name="Face"></a>Arcfelismerési

Arcfelismerési a kapcsolódó észlelt lapok Arcfelismerési API származó eredmények egyesített kifejezés. Végső soron arcfelismerési egy egységes identitást képvisel ([Arcfelismerési azonosító](#Face-ID)), egy adott területen képek ([Arcfelismerési téglalap](#Face-Rectangle)), és a kapcsolódó további arcfelismerési [attribútumok](#Face-Attributes-Facial-Attributes), például [kora](#Age-Attribute), [nemét](#Gender-Attribute), [jellegzetes hely](#Face-Landmarks-Facial-Landmarks) és [központi jelentő](#Head-Pose-Attribute). Emellett felületei is adhatók vissza felügyelt kódból [észlelési](#Detection-Face-Detection).

További részletekért tekintse meg az útmutató [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Face-API"></a>Arcfelismerési API

Arcfelismerési API egy felhőalapú API-t, amely a legtöbb speciális algoritmusok arcfelismerési észlelési és felismerést. Arcfelismerési API fő funkcióinak két kategóriába oszthatók: arcfelismerési [észlelési](#Detection-Face-Detection) rendelkező [attribútumok](#Face-Attributes-Facial-Attributes), és a tapasztalt [felismerés](#Recognition).

További részletekért tekintse meg az alábbi útmutatókban: [Arcfelismerési API – áttekintés](./Overview.md), [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Arcfelismerési - hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [szembesülhetnek - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Szembesülhetnek – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [szembesülhetnek - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="Face-Attributes-Facial-Attributes"></a>Arcfelismerési attribútumok/és attribútumok

Ellenőrizze a [attribútumok](#Attributes).

#### <a name="Face-ID"></a>Arcfelismerési azonosítója

Arcfelismerési azonosítója, amelyből származik a [észlelési](#Detection-Face-Detection) eredmény elérése érdekében ahol karakterlánc jelöli egy [arcfelismerési](#Face) a [Arcfelismerési API](#Face-API).

További részletekért tekintse meg az útmutató [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Face-Landmarks-Facial-Landmarks"></a>Arcfelismerési jellegzetes hely/és jellegzetes hely

Jellegzetes hely a nem kötelező a [észlelési](#Detection-Face-Detection) eredményeket; szemantikai arcfelismerést pontok, például a szem, orrot és szájától (következő ábra mutatja be). Jellegzetes hely vezérelhető a [észlelési](#Detection-Face-Detection) a logikai számú returnFaceLandmarks kérésére. Ha returnFaceLandmarks értéke igaz, a visszaadott lapok lesz jellegzetes attribútumok.

További részletekért tekintse meg az útmutató [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="Face-Rectangle"></a>Arcfelismerési négyszöge

Arcfelismerési téglalap származik a [észlelési](#Detection-Face-Detection) eredményeket, amely egy függőleges téglalap (bal, felső, szélesség, magasság) képek képpont. A bal felső sarkában a [arcfelismerési](#Face) (bal oldali, top), szélességét és magasságát, mellett azt jelzi, arc-méretek x és y tengely, illetve.

További részletekért tekintse meg az útmutató [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Facial-Hair-Attribute"></a>Arcfelismerést haj (attribútum)

Arcfelismerést haj egyike a [attribútumok](#Attributes) leírására a rendelkezésre álló lapok arcfelismerést haj hosszát. A arcfelismerést haj attribútum nem kötelező megadni a a [észlelési](#Detection-Face-Detection) elemre, majd vezérelhető a [észlelési](#Detection-Face-Detection) returnFaceAttributes kérésére. Ha returnFaceAttributes "facialHair", a visszaadott lapok lesz arcfelismerést haj attribútumok.

További részletekért tekintse meg az útmutató [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="FaceList"></a>FaceList

FaceList gyűjteménye [PersistedFace](#PersistedFace) és a egysége [hasonló](#Find-Similar). Egy FaceList tartalmaz egy [FaceList azonosító](#FaceList-ID), például egyéb attribútumok valamint [neve](#Name) és [felhasználói adatok](#UserData-User-Data).

További részletekért tekintse meg az alábbi útmutatókban: [FaceList - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="FaceList-ID"></a>FaceList azonosítója

FaceList azonosító: egy felhasználó által megadott karakterláncot azonosítóként használt egy [FaceList](#FaceList). A FaceList Azonosítójú előfizetésen belül egyedinek kell lennie.

További részletekért tekintse meg az alábbi útmutatókban: [FaceList - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="Find-Similar"></a>Hasonló keresése

Ez az API felületei gyűjteménye alapján hasonló lapok keresés segítségével. Lekérdezés lapokat és a tapasztalt gyűjtemények helyettesítik [azonosítók szembesülhetnek](#Face-ID) vagy [FceList azonosító](#FaceList-ID)/[LargeFaceList azonosító](#LargeFaceList-ID) a kérelemben. Visszaadott eredményei keresett hasonló arc, által képviselt [azonosítók szembesülhetnek](#Face-ID) vagy [PersistedFace azonosítók](#PersistedFace-ID).

További részletekért tekintse meg az alábbi útmutatókban: [Arcfelismerési - hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="Gender-Attribute"></a>Nemét (attribútum)

Nemét egyike a [attribútumok](#Attributes) , a rendelkezésre álló lapok genders leírására. A nemét attribútum nem kötelező megadni a a [észlelési](#Detection-Face-Detection) elemre, majd vezérelhető a [észlelési](#Detection-Face-Detection) returnFaceAttributes kérésére. Ha returnfaceAttributes "nemét", a visszaadott lapok lesz nemét attribútumok.

További részletekért tekintse meg az útmutató [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Grouping"></a>Csoportosítás

Arcfelismerési a csoportosítás akkor csoportosítása szerint arcfelismerést Hasonlóságok lapok gyűjteménye. A kérelem arcfelismerési azonosító gyűjteményeket arcfelismerési gyűjtemények jelzi. Csoportosítás, miatt hasonló lapok csoportba kerülnek [csoportok](#Groups), és, amely nem hasonlítanak a bármely más arcfelismerési együtt egyesítődnek zavaró csoportként. Már van a tartalom legfeljebb egy [zavaró csoport](#Messy-Group) a csoportosítási eredmény.

További részletekért tekintse meg az útmutató [szembesülhetnek - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="Groups"></a>Csoportok

Csoportok képzi a rendszer a [csoportosítási](#Grouping) eredmények. Minden egyes csoport hasonló arc, ahol a lapok által jelzett gyűjteményét tartalmazza [azonosítók szembesülhetnek](#Face-ID).

További részletekért tekintse meg az útmutató [szembesülhetnek - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="Head-Pose-Attribute"></a>HEAD jelent (attribútum)

HEAD jelentő egyike a [attribútumok](#Attributes) , hogy jelöli szembesülhetnek tájolás 3D terület szerint összesítő, térközt és kitérése szögek, az alábbi ábrán látható módon. Az érték tartományait visszaállítani, és azok [-180, 180] és [-90, 90] yaw fokban. A jelenlegi verzióban betűközű észlelési által visszaadott érték mindig 0. A head jelentő attribútum nem kötelező megadni a a [észlelési](#Detection-Face-Detection) elemre, majd vezérelhető egy [észlelési](#Detection-Face-Detection) kérelmet a returnFaceAttributes paraméterrel. Ha returnFaceAttributes paraméter "headPose" tartalmaz, a visszaadott lapok attribútumok jelent head fog rendelkezni.

További részletekért tekintse meg az útmutató [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="Identification"></a>Azonosító

Az azonosító egy vagy több lapok LargePersonGroup/PersonGroup az azonosításához.
A [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup) gyűjteménye [személyek](#Person).
Lapok és a LargePersonGroup/PersonGroup jelölik [azonosítók szembesülhetnek](#Face-ID) és [LargePersonGroup azonosítók](#LargePersonGroup-ID)/[PersonGroup azonosítók](#PersonGroup-ID) , illetve a a kérést.
Azonosított eredményei [jelöltek](#Candidate), által képviselt [személyek](#Person) az vetett bizalmat.
A bemeneti adatok több lapok külön minősülnek, és minden lapot a saját azonosított eredményt fog rendelkezni.

> [!NOTE]
> A LargePersonGroup/PersonGroup sikeresen be kell tanítva azonosító előtt. Ha a LargePersonGroup/PersonGroup nem be van tanítva, illetve a [állapot](#Status-Train) nem jelenik meg, a "sikeres" (azaz "fut", "sikertelen" vagy "timeout"), a rendszer 400 a kérelem választ.
> 

További részletekért tekintse meg az alábbi útmutatókban: [szembesülhetnek – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [LargePersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [ LargePersonGroup - vonat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - vonat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="Is-Identical"></a>IsIdentical

IsIdentical logikai mezőt [ellenőrzési](#Verification) eredményeket, amely azt jelzi, hogy két oldalát tartozik-e azonos.

További részletekért tekintse meg az útmutató [szembesülhetnek - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="j"></a>C

## <a name="k"></a>K

## <a name="l"></a>L

#### <a name="landmarks"></a>Jellegzetes hely

Ellenőrizze a [jellegzetes hely szembesülhetnek](#Face-Landmarks-Facial-Landmarks).

#### <a name="LargeFaceList"></a>LargeFaceList

LargeFaceList gyűjteménye [PersistedFace](#PersistedFace) és a egysége [hasonló](#Find-Similar). Egy LargeFaceList tartalmaz egy [LargeFaceList azonosító](#LargeFaceList-ID), például egyéb attribútumok valamint [neve](#Name) és [felhasználói adatok](#UserData-User-Data).

További részletekért tekintse meg az alábbi útmutatókban: [LargeFaceList - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList - lista Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="LargeFaceList-ID"></a>LargeFaceList azonosítója

LargeFaceList azonosító: egy felhasználó által megadott karakterláncot azonosítóként használt egy [LargeFaceList](#LargeFaceList). A LargeFaceList Azonosítójú előfizetésen belül egyedinek kell lennie.

További részletekért tekintse meg az alábbi útmutatókban: [LargeFaceList - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="LargePersonGroup"></a>LargePersonGroup

LargePersonGroup gyűjteménye [személyek](#Person) és a egysége [azonosító](#Identification). Egy LargePersonGroup tartalmaz egy [LargePersonGroup azonosító](#LargePersonGroup-ID), például egyéb attribútumok valamint [neve](#Name) és [felhasználói adatok](#UserData-User-Data).

További részletekért tekintse meg az alábbi útmutatókban: [LargePersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [LargePersonGroup személy - lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="LargePersonGroup-ID"></a>LargePersonGroup azonosítója

LargePersonGroup azonosító: egy felhasználó által megadott karakterláncot azonosítóként használt egy [LargePersonGroup](#LargePersonGroup). A LargePersonGroup Azonosítójú előfizetésen belül egyedinek kell lennie.

További részletekért tekintse meg az alábbi útmutatókban: [LargePersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="Messy-Group"></a>Zavaró csoport

Zavaró csoport származik a [csoportosítási](#Grouping) eredmények; nem hasonlít bármilyen más arcfelismerési lapokat tartalmaz. Minden lapot zavaró csoportban jelzi a [azonosító szembesülhetnek](#Face-ID).

További részletekért tekintse meg az útmutató [szembesülhetnek - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>N

#### <a name="name-person"></a>Name (személy)

Értéke a felhasználó rövid leíró karakterláncát [személy](#Person). Ellentétben a [személy azonosító](#Person-ID), személyek neve megkettőzhetők csoporton belül.

További részletekért tekintse meg az alábbi útmutatókban: [LargePersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup személy - beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ PersonGroup személy - beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="Name"></a>Name (LargePersonGroup/PersonGroup)

Neve egyben felhasználóbarát leíró karakterláncát [LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup). Ellentétben a [LargePersonGroup azonosító](#LargePersonGroup-ID)/[PersonGroup azonosító](#PersonGroup-ID), LargePersonGroups/PersonGroups nevét is duplikálódnak előfizetésen belül.

További részletekért tekintse meg az alábbi útmutatókban: [LargePersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [ PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="o"></a>O

## <a name="p"></a>P

#### <a name="PersistedFace"></a>PersistedFace

PersistedFace olyan adatszerkezet Arcfelismerési API-ban. PersistedFace tartalmaz egy [PersistedFace azonosító](#PersistedFace-ID), például egyéb attribútumok valamint [neve](#Name), és [felhasználói adatok](#UserData-User-Data).

További részletekért tekintse meg az alábbi útmutatókban: [LargeFaceList - Arcfelismerési hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup személy - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ PersonGroup személy - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="Person-ID"></a>Személy-azonosító

Személy azonosító jön létre. Ha egy [PersistedFace](#PersistedFace) létrehozása sikerült. Egy karakterlánc létrehozása a néző képviselő [Arcfelismerési API](#Face-API).

További részletekért tekintse meg az alábbi útmutatókban: [LargeFaceList - Arcfelismerési hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup személy - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ PersonGroup személy - hozzáadása Arcfelismerési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="Person"></a>Személy

Személy Arcfelismerési API-ban kezelt adatszerkezet. Személy rendelkezik egy [személy azonosító](#Person-ID), például egyéb attribútumok valamint [neve](#Name), gyűjteménye [PersistedFace](#PersistedFace), és [felhasználói adatok](#UserData-User-Data).

További részletekért tekintse meg az alábbi útmutatókban: [LargePersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup személy - beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ PersonGroup személy - beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="Person-ID"></a>Személy-azonosító

Személy azonosító jön létre. Ha egy [személy](#Person) létrehozása sikerült. Egy karakterlánc jön létre, ennek a személynek a képviselő [Arcfelismerési API](#Face-API).

További részletekért tekintse meg az alábbi útmutatókban: [LargePersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup személy - beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ PersonGroup személy - beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="PersonGroup"></a>PersonGroup

PersonGroup gyűjteménye [személyek](#Person) és a egysége [azonosító](#Identification). Egy PersonGroup tartalmaz egy [PersonGroup azonosító](#PersonGroup-ID), például egyéb attribútumok valamint [neve](#Name) és [felhasználói adatok](#UserData-User-Data).

További részletekért tekintse meg az alábbi útmutatókban: [PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [PersonGroup személy - lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="PersonGroup-ID"></a>PersonGroup azonosítója

PersonGroup azonosító: egy felhasználó által megadott karakterláncot azonosítóként használt egy [PersonGroup](#PersonGroup). A Csoportazonosító előfizetésen belül egyedinek kell lennie.

További részletekért tekintse meg az alábbi útmutatókban: [PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Jelent (attribútum)

Ellenőrizze a [Head jelent](#Head-Pose-Attribute).

## <a name="q"></a>A Q

## <a name="r"></a>R

#### <a name="Recognition"></a>Felismerés

Olyan népszerű alkalmazás terület arcfelismerési technológiák, például a [hasonló](#Find-Similar), [csoportosítási](#Grouping), [azonosítása](#Identification),[két ellenőrzése előtt álló megegyezik-e ](#Verification).

További részletekért tekintse meg az alábbi útmutatókban: [Arcfelismerési - hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [szembesülhetnek - csoport](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [szembesülhetnek – azonosítása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [szembesülhetnek - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>A téglalap (Arcfelismerési)

Ellenőrizze a [arcfelismerési téglalap](#Face-Rectangle).

## <a name="s"></a>S

#### <a name="Smile-Attribute"></a>Felül konvex ív (attribútum)

Mosolynál egyike a [attribútumok](#Attributes) leírására a rendelkezésre álló lapok mosolynál definiáló kifejezés. A mosolynál attribútum nem kötelező megadni a a [észlelési](#Detection-Face-Detection) elemre, majd vezérelhető a [észlelési](#Detection-Face-Detection) returnFaceAttributes kérésére. Ha returnFaceAttributes "felül konvex ív", akkor a visszaadott lapok mosolynál tulajdonságokkal fog rendelkezni.

További részletekért tekintse meg az útmutató [szembesülhetnek - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="similar-face-searching"></a>Hasonló arcok keresése

Ellenőrizze a [hasonló](#Find-Similar).

#### <a name="Status-Train"></a>Állapot (szerelvény)

Állapot: egy karakterlánc, amely írja le a [LargeFaceList/LargePersonGroups/PersonGroups képzési](#Train), például "getnextbatch", "fut", "sikeres", "sikertelen".

További részletekért tekintse meg az útmutató [LargeFaceList - vonat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - vonat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - vonat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Előfizetői azonosító

Előfizetés kulcs egy olyan karakterláncot, meg kell adnia egy lekérdezési karakterlánc paraméterként ahhoz, hogy Arcfelismerési API-k hívása. Az Előfizetés kulcs Microsoft kognitív Services portálra való bejelentkezés után található a saját előfizetések lapra. Az egyes az előfizetéshez tartozó két kulcs lesz: egy elsődleges és egy másodlagos kulcsot. Mindkét API meghívása azonos módon használható. Az előfizetés-kulcsok biztonsága kell, és újragenerálás előfizetés kulcsok, valamint a saját előfizetések oldalon bármikor.

## <a name="t"></a>T

#### <a name="Train"></a>Vonat (LargeFaceList/LargePersonGroup/PersonGroup)

Ez az API segítségével előre feldolgozzák a [LargeFaceList](#LargeFaceList)/[LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup) biztosításához a [keresése Hasonló](#Find-Similar)/[azonosító](#Identification) teljesítményét. Ha a képzés nem működik, vagy a [képzési állapota](#Status-Train) nem jelenik meg, mivel sikeres volt, a PersonGroup azonosítója hibát eredményez.

További részletekért tekintse meg az alábbi útmutatókban: [LargeFaceList - vonat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - vonat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - vonat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [szembesülhetnek – azonosítása ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="UserData-User-Data"></a>Felhasználói adatok/felhasználói adatok

Felhasználói adatok további információk is társítva vannak [személy](#Person) és [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup). Felhasználói adatok adatok könnyebben lehessen használni, ismertetése, és ne feledje felhasználók által beállított.

További részletekért tekintse meg az alábbi útmutatókban: [LargePersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - frissítés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [LargePersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup személy - frissítés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [PersonGroup - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - frissítés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [PersonGroup személy - létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ PersonGroup személy - frissítés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="Verification"></a>Ellenőrzés

Ez az API segítségével győződjön meg arról, hogy két oldalát megegyeznek-e. Két módon azonosítók szembesülhetnek a kérelemben szereplő vannak megadva. Eredmények tartalmaznak egy logikai mező ellenőrzése ([isIdentical](#Is-Identical)) jelző azonos, amennyiben az értéke igaz, és egy számmező ([abban, hogy](#Confidence)) megbízhatósági szintjét jelzi.

További részletekért tekintse meg az útmutató [szembesülhetnek - ellenőrzése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="w"></a>W

## <a name="x"></a>X

## <a name="y"></a>I

## <a name="z"></a>Z-IG
