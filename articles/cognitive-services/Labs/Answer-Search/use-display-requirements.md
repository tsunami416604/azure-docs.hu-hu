---
title: Használjon és megjelenítési követelményeihez - projekt válaszkeresés
titlesuffix: Azure Cognitive Services
description: Használja, és megjeleníti a projekt válaszkeresés végpont követelményei.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: answer-search
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: b57e80370c778e7cebdb4a5ccd888ced324be0aa
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469486"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Válaszkeresés projekt használja, és megjelenítési követelményeihez

Használati és megjelenítési követelmények vonatkoznak minden olyan végrehajtása a tartalom és a kapcsolódó információkat, például kapcsolatokat, metaadatokat és egyéb jelekkel, a Bing keresés az Ismeretcikkek között, a Bing egyéni keresés, Entity Search, Képkeresés hívásainak keresztül elérhető Hírkeresési, Videókeresés, vizuális keresés, webes keresés, helyesírás-ellenőrzés és automatikus kiegészítési API-k. Ezek a követelmények kapcsolatos megvalósítási részletei található az a funkciók és az eredmények dokumentációja.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. A Bing Spell Check és a Bing Autosuggest API-t.

nem:

- másolja, tárolni vagy bármilyen adatokat kap, a Bing Spell Check, vagy a Bing Autosuggest API-k
- a Bing helyesírás-ellenőrzési vagy a Bing Autosuggest API-k olyan gépi tanulási vagy hasonló algoritmikus betanításához részeként kap adatait kiértékelése, vagy javíthatja a új vagy meglévő szolgáltatásokat, amelyek az Ön vagy harmadik felek kínálhatják.

## <a name="2-definitions"></a>2. Meghatározások

- a válaszban visszaadott eredmények kategória "válasz" hivatkozik. Például a Bing Web Search API válaszára tartalmazhatnak válaszokat a weblap eredmények, kép, videó és hír; kategóriái
- "response" azt jelenti, hogy minden válaszokat, és a kapcsolódó adatokat egy keresési API-t; egyetlen meghívása válaszként kapott
- "eredménye" választ információk elemre hivatkozik. Például az olyan egyetlen hír segítségével csatlakoztatott adatok, egy news választ az eredményt.
- "Keresési API-k" azt jelenti, hogy együttesen, a Bing Custom Search, Entity Search, Képkeresés, News Search, Videókeresés, vizuális keresés és webes keresési API-k. 


## <a name="3-search-apis"></a>3. Keresési API-k

Ez a szakasz 3 követelményeit, a keresési API-khoz.

**EGY. Internetes keresési funkciót.** Válaszok szereplő összes adatok csak internetes keresési funkciókat is használhatnak. Egy internetes keresési funkciókat jelenti, hogy a megjelenített, a megfelelő tartalom: 
- megfelelő és válaszol-e a felhasználó a közvetlen lekérdezés vagy más arra utalhat, hogy a felhasználó keresési érdeklődés, valamint a leképezés (például felhasználói jelzett keresési lekérdezés); 
- segít a felhasználóknak, keresse meg és nyissa meg az adatforrásokat (például a megadott URL-címek vannak megvalósítva a hivatkozások így a tartalom és tesznek elérhetővé; ilyenek feltűnően jelenik meg az adatokat az kattintható hivatkozás); vagy a Bing Entity Search API, ha jól láthatóan összekapcsolása a bing.com megadott URL-cím, amely lehetővé teszi a felhasználó számára a megfelelő lekérdezés Bing.com; a keresési eredmények között keresse meg a válasz
- Válassza ki a végfelhasználó, aki több eredményeit tartalmazza (például a news választ, több eredmény jelenik meg vagy ad vissza minden eredményt, ha kevesebb, mint számos); 
- egy (például a miniatűrök vannak miniatűr arányosan a felhasználó megjelenítendő kép); keresés a célt szolgálják, hogy megfelelő erejéig 
- magában foglalja a végfelhasználók számára, hogy a tartalom-e az internetes keresési eredmények (például egy utasítást, hogy van-e a tartalom "az internetről"); látható jele és
- bármely egyéb kombinációjáig terjedhet annak érdekében, hogy a keresési API-kon keresztül fogadott adatok használatát nem sérti semmilyen alkalmazandó jogszabályt vagy külső jogait (például, ha a Creative Commons licencet, a megfelelő licenc betartása a függő entitások megfelelő intézkedéseket tartalmaz feltételek). A részletekért a jogi tanácsadók mértékek lehet meghatározni a megfelelő.
Az internetes keresési élményt követelmény az egyetlen kivétel van az URL-cím-felderítési szakasz 3E (felderítési URL-cím nem-megjelenítése) következő leírtak szerint. 

**B. Korlátozások.** nem:

- másolja, tárolni vagy válaszok (kivéve a megőrzést az "A szolgáltatás folytonosságának" szakasz alábbi által megengedett legteljesebb mértékben); származó adatok gyorsítótárazása 
- bármely machine learning vagy hasonló algoritmikus tevékenység részeként a Search API-kon keresztül fogadott adatok használatával betanítani, kiértékelése vagy új vagy meglévő szolgáltatást, amelyek, vagy harmadik felek is javíthatja.
- az eredmények (más, mint a formázza újra őket úgy, hogy nem felelnek meg minden egyéb követelmény) a tartalmának módosítása, kivéve, ha a törvény vagy; a Microsoft által egyeztetett 
- hagyja el a felhasználási és eredmény tartalom; társított URL-címek
- átrendezési, például úgy, hogy elhagyása, a válasz jelenik meg, ha egy rendelés vagy rangsoroló megadott eredményeket (a Bing Custom Search API, ez a szabály nem vonatkozik a customsearch.ai portálon keresztül megvalósított átrendezése), kivéve, ha a törvény vagy a Microsoft által egyeztetett ;
- úgy, hogy a végfelhasználó számára úgy véli, hogy a többi tartalom része a válasz; vezetne választ bármelyik részét egyéb tartalmakra megjelenítése 
- hirdetés, amely a Microsoft nem biztosít bármely; választ bármelyik részét megjelenítő lapján megjelenítése – a válaszok (i) a Bing – képek, hírek, vagy a Video Search API-k; hirdetését megjelenítéséhez vagy (ii), amely a szűrt vagy korlátozott elsősorban (vagy kizárólag), kép, hírek és/vagy videó eredményeket.

**C. Értesítések és a Márkakezelés.** 

- Ezzel a beállítással hangsúlyosan közé tartozik a Microsoft adatvédelmi nyilatkozatában, a funkcionális hivatkozás https://go.microsoft.com/fwlink/?LinkId=521839, a felhasználói felület (UX), amely lehetőséget biztosít a felhasználó adjon meg egy keresési lekérdezést minden pontján közelében. A hivatkozás "A Microsoft Privacy Statement" címkét.
- Ezzel a beállítással hangsúlyosan megjelenítése a Bing márkajelzési, konzisztens a rendelkezésre álló irányelvek https://go.microsoft.com/fwlink/?linkid=833278, minden pont, amely lehetőséget biztosít a felhasználó adjon meg egy keresési lekérdezést felhasználói közelében.  Ilyen márkajelzési kell egyértelműen jelöl a felhasználó számára, hogy a Microsoft motorja van az internetes keresési funkciót.
- Előfordulhat, hogy attribútuma, minden válasz (vagy a válasz részét) jelenik meg a Bing webes, kép, hírek és Video API-k a Microsoft leírtak szerint https://go.microsoft.com/fwlink/?linkid=833278, hacsak a Microsoft egyéb megadja írása a használatra. 
- Nem attribútum válaszok (vagy egyes részeiből válaszok) a Microsoft, a Bing Custom Search API megjelenített, kivéve, ha a Microsoft más módon meghatározza az adott használatra írásban.


**D. Válaszok átvitel.** Ha engedélyezi az adatátviteli válasz egy keresési API-ból egy másik felhasználó, például egy üzenetküldési alkalmazást vagy a közösségi média könyvelési, az alábbi esetek keresztül: 
- Átvitt válaszokat kell:
  - Tartalom, módosítás nélküli való továbbítása során a felhasználónak jelenik meg a válaszok tartalmának a áll (formázási módosítások teljesítménykorlátok);
  - Nem foglal bele semmilyen adatot metaadatok űrlapon;
  - A Bing webes, kép, hírek és Video API-k parancsválaszait megjelenítési nyelvét, a válasz jelző lett lekérve egy internetes keresési funkciót, a Bing által működtetett keresztül (például "Szolgáltató által a Bing," "További információ a Bing a lemezkép", vagy a Bing embléma használatával);
  - A Bing Custom Search API parancsválaszait megjelenítési nyelvét, a válasz jelző lett lekérve keresztül egy internetes keresési funkciókat (például "További információ a keresési eredmények");
  - Hangsúlyosan jelenítheti meg a teljes lekérdezést, a válasz; létrehozásához használt és
  - Például egy neves hivatkozás vagy a válasz az alapul szolgáló forrás hasonló tesznek elérhetővé; ilyenek közvetlenül vagy a keresőmotor (bing.com, m.bing.com vagy az egyéni keresési szolgáltatás, társvállalatait) keresztül.
- Nem, előfordulhat, hogy automatizálhatja a válaszok átvitelét. Egy átviteli egyértelműen részvényalapba történő tanúsító egy célja, hogy a válasz átviteli felhasználói műveletet kell kezdeményeznie.
- Csak a felhasználó átadó lekérdezést válaszként megjelenített válaszok adatátviteli engedélyezheti.

**E. A szolgáltatás folytonosságának.** Ne másolja, tárolásához, vagy bármilyen keresési API-válaszok adatainak gyorsítótárba. Ahhoz, hogy a szolgáltatás-hozzáférés és az adatok ábrázolásakor folytonosságának, azonban kizárólag a következő feltételek eredmények megőrzése előfordulhat, hogy:

**Az eszköz.** Előfordulhat, hogy engedélyezi a végfelhasználó számára a lekérdezés vagy (ii) addig, amíg a felhasználó elküldi a frissített eredményt, egy másik lekérdezést egy eszközön (i) 24 órát is várhatnak a kisebb az eredmények megőrzése, feltéve, hogy a megőrzött eredmények csak akkor használhatók:

- Ahhoz, hogy a végfelhasználó számára, hogy a végfelhasználók számára, hogy az eszközről (például esetén a szolgáltatás megszakadásának); korábban visszaadott eredmények eléréséhez vagy
- a végfelhasználó jeleket (például esetén várható szolgáltatáskiesést) alapján a felhasználó igényeit várható a személyre szabott proaktív lekérdezésben visszaadott eredményeket tárolja.

**A kiszolgáló.** Biztonságosan a vezérlőelemet, és csak a megőrzött találatok megjelenítéséhez kiszolgálón egy egyetlen felhasználónak adott eredmények is megőrzése:

- Ahhoz, hogy a végfelhasználó az eredmények korábban adott vissza a felhasználó a megoldásban, feltéve, hogy az eredmények nem (i) tarthatók 21 napnál hosszabb idő a felhasználó kezdeti lekérdezést a, és (ii) jelenik meg a válasz egy teljes körű u korábbi jelentések eléréséhez új vagy ismétlődő lekérdezés elhasználó; vagy
- a proaktív lekérdezésben visszaadott eredményeket tárolja személyre szabott várható a végfelhasználó igények esetében a kisebb (i) 24 órával az idő a lekérdezés vagy (ii) addig, amíg a felhasználó elküldi a frissített eredmények egy másik lekérdezést a végfelhasználó jelek alapján.

Minden alkalommal, amikor őrzi, eredményeket egy adott felhasználó nem lehet commingled az eredmények egy másik felhasználó, azaz, az eredményeket a minden felhasználó őrződnek meg kell lehetnek, és külön-külön tartalmazott.

**Általános.** A megőrzött eredmények összes bemutatása:

- egyértelmű, látható az idő a lekérdezés elküldve, értesítést tartalmaznak
- egy gomb vagy újból lekérdezése, és beszerezni a frissített eredmények hasonló azt jelenti, hogy a felhasználók bemutatásához 
- a Bing, az eredmények a bemutató márkajelzési megőrzése és
- Törlés (és szükség esetén egy új lekérdezést a frissítés) a megadott időkeretének meghatározása belül tárolt eredménye.

**F. Nem-megjelenítési URL-cím felderítés.** Használhat keresési találatok csak nem internetes keresési funkciókat azzal a céllal, az URL-címei reagálnak a lekérdezést a felhasználó vagy felhasználói információk felderítéséhez. Egy jelentésben vagy (i) kizárólag a felhasználó számára biztosított hasonló válasz is másolhatja az ilyen URL-címek vagy ügyfél, ez a lekérdezés és (ii) hogy válaszként jelentős értékes tartalmakra vonatkozó lekérdezéshez tartalmazza. A használati és megjelenítési követelménynek szakaszok 3A-3E követelményeinek nem vonatkozik ez nem megjelenítendő használati kivételével: 

- Nem kell gyorsítótárazni, másolja, vagy tárol adatokat, vagy a tartalom vagy abból, a keresési választ, nem a korlátozott URL-cím másolása, korábban leírt;
- Győződjön meg arról, az adatok (az URL-címeket is beleértve) kapott a Search API-k használatát nem sérti semmilyen alkalmazandó jogszabályt vagy külső jogosultságok; és
- Kell nem használhatja a keresési API-k érkező bármely search-index vagy a machine learning vagy hasonló algoritmikus tevékenység részeként (beleértve az URL-címek) adatokat train létrehozása, kiértékelheti és javíthatja a szolgáltatások, amelyek ajánlhat, vagy harmadik felek.

## <a name="next-steps"></a>További lépések
[Válasz Search áttekintése](overview.md)
