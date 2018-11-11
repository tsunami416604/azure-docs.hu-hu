---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: MikeDodaro
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/19/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: f166ceac1ae848565f861a94781ce0500c24747e
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289155"
---
Használati és megjelenítési követelmények vonatkoznak minden olyan végrehajtása a tartalom és a kapcsolódó információkat. Például a követelmények érvényesek, kapcsolatok, metaadatokat és egyéb jelekkel. Ezek lehetnek a következő API-kat keresztül érhető el:

- Bing – Egyéni keresés
- Bing – Entitáskeresés
- Bing – Képkeresés
- Bing – Hírkeresés
- Bing – Videokeresés
- Bing vizuális keresés
- Bing – Internetes keresés
- Bing – Helyesírás-ellenőrzés
- Bing – Automatikus kiegészítés

Megvalósítási részletei kapcsolatos ezeknek a követelményeknek, az a funkciók és az eredmények dokumentációjában talál.     

## <a name="bing-spell-check-and-bing-autosuggest-apis"></a>A Bing Spell Check és a Bing Autosuggest API-k

nem:

- Másolja, tárolásához, vagy bármilyen adatokat, a Bing Spell Check és a Bing Autosuggest API-k fogadása.
- A Bing helyesírás-ellenőrzési vagy a Bing Autosuggest API-k minden machine learning vagy hasonló algoritmikus tevékenység részeként kap adatait. Betanításához, kiértékelése vagy, amelyet Ön vagy harmadik felek kínálhat új vagy meglévő szolgáltatások fejlesztésére használja ezeket az adatokat.

## <a name="definitions"></a>Meghatározások

- *Válasz* a válaszban visszaadott eredmények kategória hivatkozik. Például a Bing Web Search API válaszára belefoglalhatja válaszokat a weblap eredményeket, kép, videó, vizualizációt és hírek kategóriáit.   
- *Válasz* azt jelenti, hogy minden válaszokat és a kapcsolódó egyetlen keresési API-hívással adott válaszként kapott adatokat.
- *Eredmény* választ információk elemre hivatkozik. Például az olyan egyetlen hír segítségével csatlakoztatott adatok, egy news választ az eredményt.
- *Keresési API-kat* azt jelenti, hogy együttesen, a Bing Custom Search, Entity Search, Képkeresés, News Search, Videókeresés, vizuális keresés és webes keresési API-k. 


## <a name="search-apis"></a>Keresési API-k

Ebben a szakaszban található követelmények vonatkoznak a Search API-k. A Search API-k nem tartalmaznak, a Bing Spell Check, vagy a Bing automatikus kiegészítés. Az előző szakaszban ismertetett két API-k követelményeinek.

### <a name="internet-search-experience"></a>Internetes keresési funkciót

Válaszok szereplő összes adatok csak internetes keresési funkciókat is használhatnak. Egy internetes keresési funkciókat jelenti, hogy a megjelenített, a megfelelő tartalom: 
- Válaszol-e a felhasználó a közvetlen lekérdezés vagy más arra utalhat, hogy a felhasználó keresési érdeklődés, valamint a leképezés (például egy felhasználó által megadott keresési lekérdezést). 
- Segít a felhasználóknak, keresse meg és nyissa meg az adatforrásokat (például a megadott URL-címek vannak megvalósítva a hivatkozások, így a tartalom és tesznek elérhetővé; ilyenek feltűnően jelenik meg az adatokat az kattintható hivatkozás). Vagy a Bing Entity Search API használata esetén láthatóan kapcsolja a bing.com megadott URL-cím, amely lehetővé teszi a felhasználót, hogy a keresési eredmények között, a megfelelő lekérdezés a Bing.com oldalon keresse meg a válaszban.
- A felhasználó számára a több eredményeit tartalmazza (például a news választ, több eredmény jelenik meg vagy ha kevesebb mint több összes eredményeket ad vissza). 
- Keresés erre a célra (például a miniatűrök vannak miniatűr arányosan a felhasználó megjelenítendő kép) kiszolgálására megfelelő összeget korlátozódik. 
- Egy eszközt a felhasználóhoz, hogy a tartalom-e az internetes keresési eredmények (például egy utasítást, hogy van-e a tartalom "az internetről") tartalmaz.
- Bármely egyéb kombinációjáig terjedhet annak érdekében, hogy a keresési API-kon keresztül fogadott adatok használatát nem sérti semmilyen alkalmazandó jogszabályt vagy külső rights megfelelő intézkedéseket tartalmaz. Például, hagyatkoznia a Creative Commons licencet, ha be tartania a vonatkozó feltételeket. A részletekért a jogi tanácsadók mértékek lehet meghatározni a megfelelő.
Az internetes keresési élményt követelmény az egyetlen kivétel van felderítési URL-cím, az ebben a cikkben leírtak szerint. 

### <a name="restrictions"></a>Korlátozások

nem:

- Másolja, tárolásához, vagy bármely válaszok (kivéve a "Szolgáltatás folytonosságát" szakaszt a cikk későbbi részében által megengedett legteljesebb mértékben megőrzési) adatainak gyorsítótárba. 
- Használja bármely machine learning vagy hasonló algoritmikus tevékenység részeként a Search API-k által küldött adatokat. Betanításához, kiértékelése vagy, amelyet Ön vagy harmadik felek kínálhat új vagy meglévő szolgáltatások fejlesztésére használja ezeket az adatokat.
- Módosítsa a tartalmakat az eredmények (egyéb, mint a formázza újra őket úgy, hogy nem felelnek meg minden egyéb követelmény), kivéve, ha erre törvény kötelezi, vagy a Microsoft által jóváhagyott. 
- Hagyja el a felhasználási és eredmény tartalom társított URL-címek.
- Átrendezése, például úgy, hogy elhagyása, egy válasz jelenik meg, ha egy rendelés vagy rangsoroló megadni, ha erre törvény kötelezi, vagy a Microsoft által jóváhagyott eredményeket. (A Bing Custom Search API, ez a szabály nem vonatkozik a customsearch.ai portálon keresztül megvalósított átrendezése.)
- Jelenítse meg a többi tartalmakat belül választ bármelyik részét úgy, hogy egy felhasználó úgy érzi, hogy a többi tartalom-e a válasz részét vezetne. 
- Hirdetés, amely a Microsoft nem biztosít bármely választ bármelyik részét megjelenítő lapján jelennek meg. 
- A válaszok (i) a Bing – képek, News Search, Video Search vagy Visual Search API-k; hirdetését megjelenítése vagy (ii), amely a szűrt vagy korlátozott elsősorban (vagy kizárólag) lemezképet, hírek és/vagy videó vagy vizuális eredmény.

### <a name="notices-and-branding"></a>Értesítések és a márkakezelés 

- Ezzel a beállítással hangsúlyosan tartalmazzon a egy működési hivatkozást a [Microsoft adatvédelmi nyilatkozatát](https://go.microsoft.com/fwlink/?LinkId=521839), a felhasználói felület (UX), amely lehetőséget biztosít a felhasználó adjon meg egy keresési lekérdezést minden pontján közelében. A hivatkozás címke **Microsoft adatvédelmi nyilatkozatát**.
- Ezzel a beállítással hangsúlyosan megjelenítése a Bing márkajelzési, konzisztens a [Bing védjegy használatára vonatkozó irányelvek](https://go.microsoft.com/fwlink/?linkid=833278), minden pont, amely lehetőséget biztosít a felhasználó adjon meg egy keresési lekérdezést felhasználói közelében. Ilyen márkajelzési kell egyértelműen jelöl a felhasználó számára, hogy a Microsoft motorja van az internetes keresési funkciót.
- Minden válasz (vagy a válasz részét) jelenik meg a webes keresési Bing, Képkeresés, News Search, Videókeresés és Visual Search API-k a Microsoft is attribútum, kivéve, ha a Microsoft egyéb megadja írása a használatra. Erről a [Bing védjegy használatára vonatkozó irányelvek](https://go.microsoft.com/fwlink/?linkid=833278). 
- Nem attribútum válaszok (vagy egyes részeiből válaszok) a Microsoft, a Bing Custom Search API megjelenített, kivéve, ha a Microsoft más módon meghatározza az adott használatra írásban.

### <a name="transferring-responses"></a>Válaszok átvitel

Ha engedélyezi az adatátviteli válasz egy keresési API-ból egy másik felhasználó, például egy üzenetküldési alkalmazást vagy a közösségi média könyvelési, az alábbi esetek keresztül: 
- Átvitt válaszokat kell:
  - A válaszok való továbbítása során a felhasználónak megjelenő tartalmából módosítás nélküli tartalmat állnak. Teljesítménykorlátok formázási módosításokat.
  - A metaadatok képernyőn nem foglal bele semmilyen adatot.
  - A Bing webes, kép, hírek, videó és vizuális API-k olyan válaszoknál megjelenítési nyelvét, a válasz jelző egy internetes keresési funkciót, a Bing által működtetett keresztül lett lekérve. Ha például nyelvet, például a "Bing által működtetett" vagy "További" További információk a lemezkép megjelenítheti a Bing, vagy használhatja a Bing embléma.
  - A Bing Custom Search API a válaszok megjelenítési nyelvét, a válasz jelző lett kombináljuk egy internetes keresési funkciót. Ha például tudja megjeleníteni a nyelvet, például a "További információ a keresési eredmények."
  - Ezzel a beállítással hangsúlyosan jelennek meg a teljes lekérdezést, a válasz létrehozására szolgál.
  - Például egy neves hivatkozás vagy a válasz az alapul szolgáló forrás hasonló tesznek elérhetővé; ilyenek közvetlenül vagy a keresőmotor (bing.com, m.bing.com vagy az egyéni keresési szolgáltatás, társvállalatait) keresztül.
- Nem, előfordulhat, hogy automatizálhatja a válaszok átvitelét. Egy átviteli egyértelműen részvényalapba történő tanúsító egy célja, hogy a válasz átviteli felhasználói műveletet kell kezdeményeznie.
- Csak a felhasználó átadó lekérdezést válaszként megjelenített válaszok adatátviteli engedélyezheti.

### <a name="continuity-of-service"></a>A szolgáltatás folytonosságának 

Ne másolja, tárolásához, vagy bármilyen keresési API-válaszok adatainak gyorsítótárba. Ahhoz, hogy a szolgáltatás-hozzáférés és az adatok ábrázolásakor folytonosságának, azonban kizárólag a következő feltételek eredmények megőrzése előfordulhat, hogy:

**Az eszköz.** Előfordulhat, hogy engedélyezi a felhasználót, hogy a lekérdezést, vagy (ii) addig, amíg a felhasználó elküldi a frissített eredményt, egy másik lekérdezést egy eszközön (i) 24 órát is várhatnak a kisebb az eredmények megőrzése, feltéve, hogy a megőrzött eredmények csak akkor használhatók:

- Ahhoz, hogy a felhasználó számára, hogy az eszközről (például esetén a szolgáltatás megszakadásának), hogy a felhasználó korábban visszaadott eredmények elérését.
- A személyre szabott várható a felhasználói igényeket, annak a felhasználónak a jeleket (például esetén várható szolgáltatáskiesést) alapján, a proaktív lekérdezésben visszaadott eredményeket tárolja.

**A kiszolgáló.** Hogy előfordulhat, hogy biztonságosan szabályozhatja kiszolgálón egy felhasználó adott eredményeket, és csak a megőrzött találatok megjelenítéséhez:

- Ahhoz, hogy a felhasználó korábbi jelentések a megoldásban a felhasználó korábban visszaadott eredmények eléréséhez. Az eredmények lehet, hogy nem kell (i) több mint 21 napig idő a felhasználó kezdeti lekérdezést a, és (ii) jelenik meg a felhasználó új vagy ismétlődő lekérdezésre válaszul.
- A proaktív lekérdezésben visszaadott eredményeket tárolja személyre szabott várható a felhasználói igényeket, annak a felhasználónak a jelek alapján. Ezeket az eredményeket a (i) 24 órát is várhatnak a kisebb a lekérdezést, vagy (ii) addig, amíg egy felhasználó beküld egy másik lekérdezést a frissített eredmények is tárolhatja.

Minden alkalommal, amikor őrzi, eredményeket egy adott felhasználó nem commingled az eredmények egy másik felhasználó. Azt jelenti az eredményeket a minden felhasználó őrződnek meg kell, és külön-külön tartalmazott.

### <a name="general"></a>Általános kérdések 

A megőrzött eredmények összes bemutatása:

- Például egy világos, látható figyelje meg az idő a lekérdezés elküldve.
- Jelen van a felhasználó egy gombot, vagy hasonló azt jelenti, hogy újra lekérdezi és beszerezhetik az eredményeket frissítése. 
- A Bing, az eredmények a bemutató márkajelzési megőrzése.
- Törlés (és szükség esetén egy új lekérdezést a frissítés) a megadott időkeretének meghatározása belül tárolt eredménye.

### <a name="non-display-url-discovery"></a>Nem-megjelenítési URL-cím-felderítési 

Használhat keresési találatok csak nem internetes keresési funkciókat azzal a céllal, az URL-címei reagálnak a lekérdezést a felhasználó vagy felhasználói információk felderítéséhez. Egy jelentés vagy az Ön hasonló válasz is másolhatja az ilyen URL-címek:

- Csak az adott felhasználó vagy az ügyfél, a lekérdezésre válaszul.
- Csak akkor, ha a jelentős értékes tartalmakra, a lekérdezés tartalmazza.

Az előző szakaszokban keresési API-t használja, és megjelenített követelmények nem vonatkoznak a nem megjelenített használja, a következők kivételével: 

- Nem gyorsítótárazzák, másolni vagy tárolja az adatokat vagy tartalmat, vagy abból származó, a keresési választ, a korlátozott URL-cím másolása korábban leírtak kivételével.
- Győződjön meg arról, az adatok (az URL-címeket is beleértve) kapott a Search API-k használatát nem sérti semmilyen alkalmazandó jogszabályt vagy külső jogok.
- Ne használja a keresési API-k érkező minden keresési index vagy a machine learning vagy hasonló algoritmikus tevékenység részeként (beleértve az URL-címek) adatokat. Train létrehozása, kiértékelheti és javíthatja a szolgáltatás, amelyet Ön vagy harmadik felek kínálhat, ne használja ezeket az adatokat.

## <a name="gdpr-compliance"></a>GDPR-megfelelőség  

Személyes adatok vonatkoznak az Európai Unió általános adatvédelmi rendelet (GDPR), és hogy a gyűjthetünk a Search API-k, a Bing Spell Check API vagy a Bing Autosuggest API-hívások feldolgozását, garanciát, megismeréséhez, hogy Ön és a Microsoft független adatkezelők a GDPR alapján. Ön felelős egymástól függetlenül az általános adatvédelmi rendelet a megfelelést.  

