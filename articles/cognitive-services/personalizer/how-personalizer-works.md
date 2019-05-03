---
title: Hogyan működik Personalizer - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer gépi tanulási felderítésére végrehajtandó műveletet egy környezetben való használata. Minden tanuló hurok rendelkezik olyan modell, amely kizárólag az adatokat, amelyeket hozzá rang keresztül elküldött ellenszolgáltatás meghívja a be van tanítva. Minden tanuló hurok teljesen egymástól független.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 6b2237f27fba5eaf952932cd6592052649400b96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027130"
---
# <a name="how-personalizer-works"></a>Personalizer működése

Personalizer gépi tanulási felderítésére végrehajtandó műveletet egy környezetben való használata. Minden tanuló hurok rendelkezik olyan modell, amely kizárólag a rá keresztül küldött adatok be van tanítva **rang** és **ellenszolgáltatás** hívások. Minden tanuló hurok teljesen egymástól független. Hozzon létre minden egyes része vagy az alkalmazás személyre kívánt viselkedés learning hurkot.

For-each ciklus **az rang API-hívás** az aktuális környezet alapján:

* A lehetséges műveletek listájában: tartalom elemet, amelyből válassza ki a felső műveletet.
* Listáját [környezet funkciók](concepts-features.md): kontextusban vonatkozó adatokat, például felhasználó, a tartalom és a környezetben.

A **rang** API úgy dönt, hogy használja:

* _Biztonsági rés kiaknázása elleni_: Az aktuális modell dönthet arról, hogy a korábbi adatok alapján ajánlott műveletet.
* _Ismerkedés a_: Válassza ki a felső művelet helyett más művelet.

A **ellenszolgáltatás** API:

* Gyűjti az adatokat a modell betanításához rögzíti a szolgáltatások és minden egyes rangsorolják hívás ellenszolgáltatás pontszámokat.
* Az adatokat használja a fájlban megadott beállítások alapján modell frissítése a _Learning házirend_.

## <a name="architecture"></a>Architektúra

Az alábbi képen a Rank és ellenszolgáltatás hívások hívása architekturális áramlása látható:

![helyettesítő szöveg](./media/how-personalizer-works/personalization-how-it-works.png "személyre szabása működése")

1. Personalizer belső AI modellt használ, a művelet az osztályozáshoz.
1. A szolgáltatás úgy dönt, hogy a biztonsági rés kiaknázása elleni az aktuális modell, vagy a modell új lehetőségek felfedezése.  
1. Ennek a területnek eredménye EventHub küld.
1. Amikor Personalizer megkapja a fejében, a ellenszolgáltatás EventHub elküldi. 
1. A rang és ellenszolgáltatás korrelált.
1. Az AI-modellek frissítése korrelációs eredményei alapján.
1. A következtetésekhez motor frissül az új modell. 

## <a name="research-behind-personalizer"></a>Kutatási Personalizer mögött

Personalizer és a legmodernebb adatelemzési és kutatási területén alapján [megerősítő tanulást](concepts-reinforcement-learning.md) többek között a tanulmányok, a kutatási tevékenységek és a folyamatban lévő területeket feltárása a Microsoft Research.

## <a name="terminology"></a>Terminológia

* **Hurok tanulási**: Tanulási hurkot hozhat létre, amelyek segítségével személyre szabása az alkalmazás minden részében. Ha egynél több élmény személyre szabásához, hozzon létre egy hurkot. 

* **Műveletek**: Művelet a tartalom elemek, például a termékek vagy promóciókkal, amelyek közül választhatnak, is. Personalizer úgy dönt, az ismert, mint a felhasználók számára megjelenítendő felső művelet a _művelet Díjazza_, a rang API-n keresztül. Minden művelet a rangsorolt kérelmet az elküldött funkciókat is rendelkezhet.

* **Környezet**: Adjon meg egy pontosabb rank, adja meg például a környezet kapcsolatos információkat:
    * A felhasználó.
    * Az eszköz vannak. 
    * Az aktuális idő.
    * Az aktuális helyzet kapcsolatos egyéb adatok.
    * A felhasználó vagy a környezet kapcsolatos előzményadatok.

    Előfordulhat, hogy az adott alkalmazás különböző környezeti információkat. 

* **[Szolgáltatások](concepts-features.md)**: Egység tartalomelem vagy felhasználói környezetet kapcsolatos információkat.

* **Ellenszolgáltatás**: Méri, a felhasználó hogyan válaszolt a rang API művelet, mint 0 és 1 közötti pontszámot ad vissza. A 0 – 1 értéket az üzleti logikát, hogyan segített a kiválasztott személyre szabása, az üzleti céljait alapján állítja be. 

* **Feltárás**: Ha az ajánlott művelet visszaadása, helyett egy másik műveletet a felhasználó úgy dönt a Personalizer szolgáltatás tervezi. A Personalizer szolgáltatás eltéréseket, stagnálásához, elkerülheti, és módosíthatja úgy a folyamatban lévő felhasználók viselkedésének elemzésével. 

* **Kísérletezéssel töltött idő**: Mennyi ideig a Personalizer szolgáltatás megvárja, amíg a fejében, az esemény indítása attól a pillanattól kezdve a rangsorolt hívás történt.

* **Inaktív események**: Az inaktív esemény egyike ahol hívott Rank, de nem biztos a felhasználó minden eddiginél jelenik meg az eredményt, miatt ügyfél alkalmazás döntéseket hozhat. Inaktív események létrehozása és személyre szabása eredmények tárolásához, majd úgy dönt, hogy elveti őket később a gépi tanulási modell befolyásolása nélkül.

* **Modell**: Personalizer modell rögzíti a felhasználói viselkedés, a betanítási adatok lekérése, küldjön Rank és ellenszolgáltatás hívások, valamint a betanítási működése Learning házirend határozza meg az argumentumokat kombinációja megismerte az összes adat. 

## <a name="next-steps"></a>További lépések

Megismerheti [használhat Personalizer](where-can-you-use-personalizer.md).