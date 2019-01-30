---
title: Mi a Személyes hatású csevegés projekt?
titlesuffix: Azure Cognitive Services
description: Ez a cikk áttekintést nyújt az Azure Personality Chat projektjéről, amely egy, a robotok beszélgetési képességeinek továbbfejlesztésére szolgáló felhőalapú API.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: overview
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: 053d43d0f17df2008106bda38318615e60778935
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207513"
---
# <a name="what-is-project-personality-chat"></a>Mi a Személyes hatású csevegés projekt?

A Personality Chat projekt megnöveli robotjának beszélgetési képességeit a társalgás egy sajátos, választott személyiségnek megfelelő kezelésével. A Personality Chat szándékbesorolókat használ a gyakori társalgási szándékok azonosítására, és egy adott személyiséggel konzisztens válaszokat hoz létre. A szándék és a megbízhatósági pontszámok alapján a robot vagy kiválasztja a legjobb választ egy válogatott szerkesztői alapkészletből, vagy valós időben hoz létre egyet mély neurális hálózatok (deep neural networks, DDN-ek) használatával. Három alapértelmezett egyéniség közül választhat. Az egyéniségmodell a választott személyiséghez leginkább illő válaszokat adja vissza.

Elérhető a gyakori társalgási lekérdezések egy testre szabható szerkesztői készlete. A készlet könnyedén integrálható a Microsoft Bot Framework SDK használatával. Ez az SDK megtakarítja Önnek a teljesen új lekérdezések megírásához szükséges időt és költségeket.

## <a name="getting-started-with-project-personality-chat"></a>A Personality Chat projekt használatának első lépései

Látogasson el a Personality Chat projekt tesztkörnyezetének oldalára, csevegjen az elérhető bemutatóval, és kérjen korai hozzáférést a szolgáltatáshoz, amikor az elérhetővé válik.
Jelenleg a testre szabható, csak szerkesztői könyvtárat is integrálhatja robotjaiba a Microsoft Bot Framework SDK segítségével. <br>
[Példák: A robot integrálhatja az személyes Csevegés](https://github.com/Microsoft/BotBuilder-PersonalityChat/) <br>
[A Personality Chat könyvtárának kipróbálása](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp)

## <a name="generating-responses-using-neural-networks"></a>Válaszok létrehozása neurális hálózatok használatával

A Personality Chat mélytanulási modelleket használ az általános beszélgetési minták megtanulásához és a válaszok létrehozásához. A válaszlétrehozó modell egy ismétlődő neurális hálózat (recurrent neural network, RNN). Ez a modell beszélgetések millióival van betanítva, amelyekben megérti és megtanulja az emberi interakciók mintáit. A tanult mondatszerkezeti mintákkal új lekérdezések és válaszok jönnek létre. Az új válasz létrehozásakor a modell szavak egy „írási szókészletében” keres, és kiválasztja az n számú legjobb első szót a válaszhoz. A modell nyalábkereséssel folytatja a keresést az n számú legjobb második szavakért minden létrehozott első szóhoz. Egy válasz akkor minősül teljesnek, amikor a modell kiválasztja a „mondatvégi” (End of sentence, EOS) szót. Amikor létrejött az összes válasz, a modell a valószínűségi pontszám alapján kiválaszt n számú legjobb választ a teljes válaszhoz.

A modell megtanulja, hogyan hozzon létre a kontextusnak megfelelő és helyes szerkezetű feleleteket. A „Szeretne most beszélgetni?” kérdésből például nagy mértékben következik egy valószínű válasz szerkezete: feltehetően az „igen” vagy a „nem” valamilyen parafrázisával fog kezdődni, a személy pedig vélhetően az „én” lesz. Egy elutasító válasz nagyobb valószínűséggel fog tartalmazni egy udvarias magyarázatot, és így tovább.

A rendszer megpróbálja megtanulni a beszélgetés alapszerkezetének egy nyelvi modelljét. Ez a szerkezet lehetővé teszi, hogy a válaszokra részben hatással legyenek olyan külső korlátozások, mint a témakör, a személyiség és így tovább.  Mivel a modell ezeket a korlátozásokat minták tág köréből tanulja meg, azok főleg (de nem kizárólag) Társalgásban történő használatra alkalmasak.

![Szekvenciától szekvenciáig terjedő modell a válaszok létrehozásához](./media/overview/sequence-to-sequence-model.png)

## <a name="personality-modeling"></a>Személyiségmodellezés

 Az adatvezérelt beszélgetési modelleknél kihívást jelent a koherens „személyiség” konzisztens biztosítása. Az EGYÉNISÉG a beszélgetési interakciók során tapasztalt személyiségként van meghatározva. Egy egyéniség az indentitás, a nyelvi viselkedés és az interakciós stílus elemei összességének tekinthető. A Personality Chat aktuális verziója a nyelvi viselkedésre és az interakciós stílusra koncentrál.

Ez a modell az egyes beszélőket vektorként vagy beágyazásként ábrázolja. Kódolja a beszélőtől származó információkat, amelyek befolyásolják a válasz tartalmát és stílusát. A modell ezután megtanulja a beszélő ábrázolásait a különböző beszélőktől származó beszélgetési tartalom alapján. A hasonló válaszokat adó beszélők általában hasonló beágyazással rendelkeznek, amelyek egymáshoz közeli pozíciókat foglalnak el a vektortérben. Ily módon a vektortérben egymáshoz közel lévő beszélők betanulási adatai segítik a beszélői modell általánosítási képességének növelését. A modell hatékonyan csoportosítja a vektortérben hasonló ábrázolással rendelkező beszélőket egy egyéniségcsoportba, amelyet „egyéniségazonosítóval” lát el.

Az alapértelmezett egyéniségek esetében attribútumok és válogatott válaszok használhatók a leginkább megfelelő beszélői csoport megtalálására. A modell ezután kijelöli ezt a csoportot az egyes alapértelmezett személyiségek egyéniségazonosítójaként. Bármelyik személyiségtípust folyamatosan testre lehet szabni egy robot-/márkafüggő válaszokból álló készlet segítségével. Ezután létrejönnek olyan beszélgetések, amelyek pontosan emulálják az egyén egyéniségét, például nyelvi válaszadási viselkedését és egyéb jellemző tulajdonságait.

![Egyéniségmodellezés beszélői csoportok használatával](./media/overview/persona-modeling.png)

## <a name="small-talk-intent-understanding"></a>A társalgási szándék megértése

A Personality Chat szándékbesorolókkal rendelkezik annak érdekében, hogy biztosítsa a társalgási szándékokra adott választ. Ezek a besorolók nem fogják akadályozni a feladatok vagy az információk lekérdezésének végrehajtását. Egy magas szintű csevegési besoroló meghatározza, hogy a lekérdezés szándéka a társalgás vagy a csevegés. A besoroló lexikai és szemantikai besorolók használatával, illetve azok pontszámainak kombinálásával dönt. Ezek a szándékok beszélgetési adatok (mint pozitív szándékminták) és keresési/feladatlekérdezések (mint negatív szándékminták) által vannak betanítva.

Egyéb alszándék-besorolók azonosítják a társalgás alosztályait annak érdekében, hogy korlátozhassák a társalgás azon típusait, amelyekre a szolgáltatás válaszol. Ilyenek például a köszönések, a dicséretek, a vélemények és így tovább. Ezek a mélytanulási besorolók minden lekérdezést vektorrá alakítanak a konvolúciós mélyen strukturált szemantikai modell (Convolutional Deep Structured Semantic Model, CDSSM) segítségével. A besorolók több tízezer válogatott alszándék-lekérdezés által vannak betanítva. A besoroló ezután meglévő, azonosított szándékosztályoknak felelteti meg a lekérdezést, úgy, hogy megkeresi a leginkább megfelelő elemet a vektortérben.

Számos vezérlő gondoskodik a kedvezőtlen válaszok elkerüléséről a Zóhoz hasonló intelligens ügynököktől származó ismeretekre építve. Alapértelmezés szerint a Personality Chat projekt úgy van beállítva, hogy kizárólag felismert felhasználói szándékokra válaszoljon. Érdemes tesztelnie, hogy a Personality Chat projekt alkalmazható-e az Ön körülményei között. Szívesen vesszük visszajelzését, ha észrevesz valamit, ami további betanítást igényel.
