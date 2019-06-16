---
title: Hogyan működik Personalizer - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer gépi tanulási felderítésére végrehajtandó műveletet egy környezetben való használata. Minden tanuló hurok rendelkezik olyan modell, amely kizárólag az adatokat, amelyeket hozzá rang keresztül elküldött ellenszolgáltatás meghívja a be van tanítva. Minden tanuló hurok teljesen egymástól független.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: d0a0a6101d876493188426d19f2fa845d20ee274
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055494"
---
# <a name="how-personalizer-works"></a>A Personalizer működése

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

* **[Szolgáltatások](concepts-features.md)** : Egység tartalomelem vagy felhasználói környezetet kapcsolatos információkat.

* **Ellenszolgáltatás**: Méri, a felhasználó hogyan válaszolt a rang API művelet, mint 0 és 1 közötti pontszámot ad vissza. A 0 – 1 értéket az üzleti logikát, hogyan segített a kiválasztott személyre szabása, az üzleti céljait alapján állítja be. 

* **Feltárás**: Ha az ajánlott művelet visszaadása, helyett egy másik műveletet a felhasználó úgy dönt a Personalizer szolgáltatás tervezi. A Personalizer szolgáltatás eltéréseket, stagnálásához, elkerülheti, és módosíthatja úgy a folyamatban lévő felhasználók viselkedésének elemzésével. 

* **Kísérletezéssel töltött idő**: Mennyi ideig a Personalizer szolgáltatás megvárja, amíg a fejében, az esemény indítása attól a pillanattól kezdve a rangsorolt hívás történt.

* **Inaktív események**: Az inaktív esemény egyike ahol hívott Rank, de nem biztos a felhasználó minden eddiginél jelenik meg az eredményt, miatt ügyfél alkalmazás döntéseket hozhat. Inaktív események létrehozása és személyre szabása eredmények tárolásához, majd úgy dönt, hogy elveti őket később a gépi tanulási modell befolyásolása nélkül.

* **Modell**: Personalizer modell rögzíti a felhasználói viselkedés, a betanítási adatok lekérése, küldjön Rank és ellenszolgáltatás hívások, valamint a betanítási működése Learning házirend határozza meg az argumentumokat kombinációja megismerte az összes adat. 

## <a name="example-use-cases-for-personalizer"></a>Példa használati esetei Personalizer

* Leképezési pontosítása & Egyértelműsítő: a felhasználók rendelkeznek a jobb felhasználói élményt, amikor azok célja súgó nem egyértelmű szabott lehetőség azáltal, hogy minden felhasználó számára.
* Javaslatok a menük & beállítások alapértelmezett: javasoljuk a legnagyobb valószínűséggel elem egy személyre szabott módon, helyett egy impersonal menüben vagy az alternatív megoldások listáját bemutatja az első lépés a robot rendelkezik.
* A robot képességekre vonatkozó és a képviselő hangvételét: robotok, vagy eltérőek lehetnek a képviselő hangvételét részletességi és stílus írása, fontolja meg ezek a jellemzők különböző egy személyre szabott módon.
* Értesítés & riasztás tartalom: döntse el, milyen szövegét az riasztást küld, annak érdekében, hogy a felhasználók több igényeik szerint.
* Értesítés & riasztási időzítési: rendelkezhetnek személyre szabott, hogy mikor értesítések küldéséhez felhasználók számára őket több tanuló.

## <a name="checklist-for-applying-personalizer"></a>Ellenőrzőlista az Personalizer alkalmazása

Personalizer alkalmazhat helyzetekben ahol:

* Az alkalmazás valamilyen üzleti vagy használhatósági cél tartozik.
* Az alkalmazás, ahol egy környezetfüggő döntés, megjelenítése, hogy mi tételére felhasználók javulni fog a cél elérésében tett olyan helyen van.
* A legjobb választás lehet, és csoportos felhasználói viselkedést és az összes ellenszolgáltatás pontszám nek ismernie kell.
* A machine learning használatát a személyre szabás követi [felelős használati irányelvek](ethics-responsible-use.md) és csapata lehetőségeit.
* A döntést a legjobb lehetőség ranglistán megjelenő kifejezhető ([művelet](concepts-features.md#actions-represent-a-list-of-options) korlátozott lehetőségek közül.
* Arról, hogy a választott működött is ki kell számítani az üzleti logika szerint felhasználói viselkedést bizonyos elemeit méri, és kifejező, a -1 és 1 közötti szám.
* Túl sok válozók vagy külső tényezők nem betöltheti a ellenszolgáltatás pontszám, kifejezetten a kísérletezéssel töltött idő elég alacsony, hogy a ellenszolgáltatás pontszám kiszámítása, miközben továbbra is fontos.
* A rang kontextusa fejezhető ki, valamint a úgy gondolja, hogy legalább 5 jellemzőket tartalmazó segít a megfelelő választás, amelyek nem tartalmaznak személyes azonosításra alkalmas adatokat.
* Minden művelet, egy szótárban legalább 5 attribútumok információ rendelkezik, vagy úgy véli, hogy funkciók segítségével Personalizer győződjön meg arról, a megfelelő választás.
* Adatok hosszú ideig továbbra is elegendő gyűlnek legalább 100 000 interakciók előzményeit.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>A Machine learning Personalizer alkalmazására vonatkozó szempontok

A megerősítő tanulás, gépi tanulás lekérdezi színesített által visszajelzést, adjon meg hozzá egy megközelítést personalizer alapján történik. 

Personalizer helyzetekben ajánlott tekintjük át, ahol:
* Nincs elegendő események figyelemmel optimális személyre szabása, ha a probléma idővel (például a beállítások a hírek és reagálás) drifts. Personalizer alkalmazkodik a méretváltozásokhoz folyamatos módosítása a való világból, de eredmények nem optimális, ha nem érhető el elegendő eseményeket és ismerkedjen meg a felderíteni és a új minták létrehozását az adatokat. Válasszon egy alkalmazási helyzet, hogy elég gyakran történik. Fontolja meg a használati esetek, amely naponta legalább 500-szor keres.
* Környezet és műveletek rendelkezik elegendő szolgáltatások tanulás egyszerűbbé tételével.
* Nincsenek rang / hívás kevesebb mint 50 műveletek.
* Az adatmegőrzési beállításokat Personalizer elegendő adatgyűjtéshez offline értékelések és házirend-optimalizálás engedélyezése. Ez általában az adatpontok legalább 50 000.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Egy webalkalmazás Personalizer használata

Webalkalmazás hozzáadása hurkot tartalmazza:

* Határozza meg, melyik élmény személyre szabásához, milyen műveleteket és a szolgáltatások rendelkezik, milyen környezeti funkciókat szeretne használni, és milyen ellenszolgáltatás lesz beállítva.
* Vegyen fel egy hivatkozást a személyre szabás SDK csomagot az alkalmazásban.
* A rang API hívása, ha személyre készen áll.
* Az eseményazonosító Store. Küldünk egy ellenszolgáltatás ellenszolgáltatás újabb API-val.
1. Aktiválás hívja meg az esemény, ha biztos benne, hogy a felhasználó számára már eddig is a személyre szabott lapot.
1. Várjon, amíg a felhasználó által választott rangsorolt tartalom. 
1. Arról, hogy a kimenet a rang API fejeződött megadásához ellenszolgáltatás API meghívásához.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Csevegőrobotot Personalizer használata

Ebben a példában bemutatjuk, hogyan személyre szabása használandó alapértelmezett helyett a felhasználó elem vagy elemek menük vonatkozó összes javaslatot tenni.

* Első a [kód](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) ehhez a mintához.
* A robot megoldás beállítása. Ellenőrizze, hogy a LUIS alkalmazás közzététele. 
* Rang kezeléséhez, és a bot ellenszolgáltatás API-khoz.
    * Adja hozzá a kódot a LUIS szándék feldolgozás kezelésére. Ha a **nincs** adja vissza, a felső leképezés vagy a felső célt pontszám az üzleti logika küszöbérték alatt van, küldjön a leképezések listában Personalizer Rank, a leképezések.
    * Folyamatban van a felső rangsorolt célt rang API-válaszból első készítésében választható hivatkozásokként felhasználó szándékának lista megjelenítése.
    * Rögzítheti a felhasználó által választott, és küldje el ezt a ellenszolgáltatás API-hívással. 

### <a name="recommended-bot-patterns"></a>Ajánlott bot minták

* API-hívásokat Personalizer rang minden alkalommal, amikor egy Egyértelműsítő van szüksége, ellentétben az egyes felhasználók eredmények gyorsítótárazás. Leképezés nevek egyértelműségének eredménye egy személy idővel változhat, és lehetővé teszi a rang API-t az eltérések felfedezése, annak érdekében, átfogó learning.
* Válassza ki, amely közös számos felhasználókkal, hogy elég adat személyre szabása közben. Például bevezető jellegű kérdések lehet, hogy jobban megfelelő pontosítások kisebb, mint a beszélgetés grafikon, amely csak néhány felhasználó számára, előfordulhat, hogy kap a mély.
* Rang API-hívás használatával engedélyezhető az "első javaslat, a jobb oldali" beszélgetések, ahol a felhasználó lekérdezi gyakori "Szeretné X?" vagy "Javaslat X?" a felhasználó egyszerűen ellenőrizheti; és és nem a beállítások megadását a felhasználónak, ahol azok közül kell választania egy menü. Ha például a felhasználó: "Szeretném, ha egy kávét order" Bot: "Szeretné a kettős espresso?". Ezzel a módszerrel a ellenszolgáltatás jel is erős közvetlenül a egy javaslat a készítésével.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Personalizer használata javasolt megoldás

Az ajánlásokat készítő motor használatával néhány elemet, majd a rang API-nak küldött 30 lehetséges műveleteket kell bemutatni, amely nagy katalógus szűréséhez.

Javaslati motorok Personalizer használhatja:

* Állítsa be a [javaslat megoldás](https://github.com/Microsoft/Recommenders/). 
* Oldal megjelenítésekor hívja meg a javaslatok modell ajánlások rövid listába.
* Hívja meg a javasolt megoldás kimenete rangsor személyre szabása.
* A felhasználói művelet a ellenszolgáltatás API-hívással visszajelzést küldhet.


## <a name="pitfalls-to-avoid"></a>Nehézségek elkerülése érdekében

* Ne használjon Personalizer, ahol a személyre szabott viselkedés nem hiba, amely könnyen megtalálhatók legyenek az minden olyan felhasználóra, de inkább valamit, amelyek adott felhasználókra vonatkozóan szabad elfeledkezni, vagy alternatívák felhasználóspecifikus listáját származik. Használata esetén például Personalizer javasolja a 20 lehetséges menüelemek listája első kétpizzás haladva hasznos, de a hívása a felhasználók mely névjegy kapcsolatlista Ha igénylő segítséget gyermekgondozási szolgáltatást (például "Nagymama") nem olyan között engedélyezve a felhasználói bázis.


## <a name="adding-content-safeguards-to-your-application"></a>Tartalom védelmi funkciók hozzáadása az alkalmazáshoz

Ha az alkalmazás lehetővé teszi, hogy a tartalom nem biztonságos, vagy nem megfelelő felhasználók lehet, meg kell tervez, ügyeljen arra, hogy a felhasználók, illetve az egyes látható tartalom nagy eltéréseket a megfelelő védelmi funkciók vannak érvényben, hogy a felhasználók lássák elfogadhatatlan tartalom. A védelmi funkciók megvalósításához ajánlott minta a következő: A védelmi funkciók megvalósításához ajánlott minta a következő:
    * Rang műveletek listájának beszerzése.
    * Szűrje ki, amelyek nem megvalósítható a célközönség az azokat.
    * Csak rangsor életképes műveletekkel.
    * A művelet a felhasználó az első felső jelennek meg.

Bizonyos architektúrák a fenti feladatütemezési lehet nehezen megvalósíthatók. Ebben az esetben van egy alternatív módszer a védelmi funkciók megvalósítása után rangsoroló, de egy kiépítése kell tenni, így nem használja a Personalizer modell betanításához műveleteket, amelyeket a biztonságosabb kívül esik.

* Szerezze be a műveleteknek a listája, rank, hogy az inaktiválása tanulással.
* Rangsorolt műveleteket.
* Ellenőrizze, hogy a felső művelet életképes-e.
    * Ha a felső művelet működőképes, aktiválja a tanulás a sorrend meghatározására, majd jelenítse meg azt a felhasználó számára.
    * Ha a felső művelet nem működőképes, nem aktiválja a rangsorolás kurzusán, és döntse el, a saját logikai vagy alternatív módszerek mi megjelenítése a felhasználónak. Akkor is, ha a második legjobb rangsorolt beállítást használja, ne aktiválja a rangsorolás kurzusán.

## <a name="verifying-adequate-effectiveness-of-personalizer"></a>Megfelelő hatékonyságát Personalizer ellenőrzése

Rendszeres időközönként figyelheti a Personalizer hatékonyságát a elvégzésével [offline értékelések](how-to-offline-evaluation.md)

## <a name="next-steps"></a>További lépések

Megismerheti [használhat Personalizer](where-can-you-use-personalizer.md).