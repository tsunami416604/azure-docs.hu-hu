---
title: Használja, és megjeleníti a Bing Search APIs-követelményei
titleSuffix: Azure Cognitive Services
description: A Megjelenítés követelményei a találatokat a a Bing Search APIs az alkalmazásokban.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 088d4ba763e8f2996dfbed1dbcc11c8eeaad3a06
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513130"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Bing Search API – követelmények használata és megjelenítése

Bármely megvalósítása a tartalom és a kapcsolódó információkat a következő Bing Search APIs, beleértve a kapcsolatokat, metaadatokat és egyéb jelekkel való ezek használatát és a megjelenített követelmények vonatkoznak.

- Bing – Egyéni keresés
- Bing – Entitáskeresés
- Bing – Képkeresés
- Bing – Hírkeresés
- Bing – Videokeresés
- Bing Visual Search
- Bing – Internetes keresés
- Bing – Helyesírás-ellenőrzés
- Bing – Automatikus kiegészítés

## <a name="definitions"></a>Meghatározások


|Időtartam  |Leírás  |
|---------|---------|
|Válasz     | Az eredmények kategória választ adja vissza. Például a Bing Web Search API válaszára belefoglalhatja válaszokat a weblap eredményeket, kép, videó, vizualizációt és hírek kategóriáit. |
|Válasz     | Bármely és minden válaszokat és kapcsolódó adatait egyetlen keresési API-hívással adott válaszként kapott. |
|Eredmény    | Egy elem a válaszban szereplő információk. Például az olyan egyetlen hír segítségével csatlakoztatott adatok, egy news választ az eredményt. |
|Keresési API-k    | együttesen a Bing Custom Search, Entity Search, Képkeresés, News Search, Videókeresés, vizuális keresés, helyi üzleti keresési és webes keresési API-k. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>A Bing Spell Check és a Bing Autosuggest API-korlátozások

nem:

- Másolja, tárolásához, vagy bármilyen adatokat, a Bing helyesírás-ellenőrzési vagy a Bing Autosuggest API-k kap.
- A Bing helyesírás-ellenőrzési vagy a Bing Autosuggest API-k minden machine learning vagy hasonló algoritmikus tevékenység részeként kap adatait. Betanításához, kiértékelése vagy, amelyet Ön vagy harmadik felek kínálhat új vagy meglévő szolgáltatások fejlesztésére használja ezeket az adatokat.

## <a name="bing-search-apis"></a>Bing Search API-k

> [!NOTE]
> Ebben a szakaszban a követelmények csak a keresési API-k, amely nem tartalmazza a Bing Spell Check és a Bing Autosuggest érvényesek. 

### <a name="internet-search-experience-requirements"></a>Internetes keresési élményt követelmények

Válaszok szereplő összes adatok csak internetes keresési funkciókat is használhatnak. Egy internetes keresési funkciókat jelenti, hogy a megjelenített tartalom: 

- Megfelelő és a felhasználó a közvetlen lekérdezés vagy más, a Keresés és szándékot (például egy felhasználó által megadott keresési lekérdezés) jelzés gyors. 

- Segít a felhasználóknak, keresse meg és nyissa meg a választ az adatforrásokat. Például hogy a válaszban lévő hiperhivatkozások kattintható hivatkozások.

- A felhasználó számára a több eredményeit tartalmazza. 

- Van egy replikája, amely lehetővé teszi a felhasználók kereséséhez.

- Tartalmaz egy látható annak jelzése, hogy a tartalom egy internetes keresési eredmény. Ha például egy utasítás arról, hogy a tartalom "webes".

- Annak érdekében, hogy a keresési Bing-API adatok nem nem felelnek meg semmilyen alkalmazandó jogszabályt vagy külső rights más megfelelő intézkedéseket tartalmaz. A részletekért a jogi tanácsadók mértékek lehet meghatározni a megfelelő.

Internetes keresési élményt a követelményeket az egyetlen kivétel van a felderítési URL-címet, a cikk későbbi részében leírtak szerint. 

### <a name="restrictions"></a>Korlátozások

nem:

- Másolja, tárolni vagy bármely válaszok adatainak gyorsítótárba (kivéve által megengedett legteljesebb mértékben megőrzési [a szolgáltatás folytonosságának](#continuity-of-service). 

- Használja bármely machine learning vagy hasonló algoritmikus tevékenység részeként a Search API-k által küldött adatokat. Betanításához, kiértékelése vagy, amelyet Ön vagy harmadik felek kínálhat új vagy meglévő szolgáltatások fejlesztésére használja ezeket az adatokat.

- Módosítsa a tartalmakat az eredmények (egyéb, mint a formázza újra őket úgy, hogy nem felelnek meg minden egyéb követelmény), kivéve, ha erre törvény kötelezi, vagy a Microsoft által jóváhagyott. 

- URL-címek társított eredmény tartalmat, és hagyja el a felhasználási adatokat.

- Átrendezése, például úgy, hogy elhagyása, akkor egy rendelés vagy rangsoroló, kivéve, ha erre törvény kötelezi, vagy a Microsoft által jóváhagyott válasz jelenik meg az eredményeket. 

    > [!NOTE]
    > Ez a követelmény nem vonatkozik átrendezése alkalmazzák a a Bing Custom Search API a portálon keresztül.

- Jelenítse meg a többi tartalmakat belül választ bármelyik részét úgy, hogy egy felhasználó úgy érzi, hogy a többi tartalom-e a válasz részét vezetne. 

- Hirdetés, amely a Microsoft nem biztosít bármely választ bármelyik részét megjelenítő lapján jelennek meg. 

- Megjeleníti a hirdetését válaszok tartalmazó lapok:
    - A Bing – képek, a News Search, a Videókeresési vagy a Visual Search API-k
    - A szűrt vagy korlátozott elsősorban (vagy kizárólag) lemezképet, hírek és/vagy videó- és vizuális keresési eredmények.

### <a name="notices-and-branding"></a>Értesítések és a márkakezelés 
Hajtsa végre:

- Ezzel a beállítással hangsúlyosan tartalmazzon a egy működési hivatkozást a [Microsoft adatvédelmi nyilatkozatát](https://go.microsoft.com/fwlink/?LinkId=521839), a felhasználói felület (UX), amely lehetőséget biztosít a felhasználó adjon meg egy keresési lekérdezést minden pontján közelében. A hivatkozás címke **Microsoft adatvédelmi nyilatkozatát**.

- Ezzel a beállítással hangsúlyosan megjelenítése a Bing márkajelzési, konzisztens a [Bing védjegy használatára vonatkozó irányelvek](https://go.microsoft.com/fwlink/?linkid=833278), minden pont, amely lehetőséget biztosít a felhasználó adjon meg egy keresési lekérdezést felhasználói közelében. Az ilyen márkajelzési kell egyértelműen állapot, a felhasználó számára, hogy a Microsoft van tárolóház az internetes keresési funkciót.

- Minden válasz (vagy a válasz részét) jelenik meg a webes keresési Bing, Képkeresés, News Search, Videókeresés és Visual Search API-k a Microsoft is attribútum, kivéve, ha a Microsoft egyéb megadja írása a használatra. Erről a [Bing védjegy használatára vonatkozó irányelvek](https://go.microsoft.com/fwlink/?linkid=833278). 

nem:

- Attribútum válaszok (vagy egyes részeiből válaszok) jelenik meg, a Bing Custom Search API a Microsoftnak, kivéve, ha a Microsoft egyéb megadja írása az adott használatra.

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

#### <a name="device"></a>Eszköz

Előfordulhat, hogy engedélyezi a felhasználót, hogy a lekérdezést, vagy (ii) addig, amíg a felhasználó elküldi a frissített eredményt, egy másik lekérdezést egy eszközön (i) 24 órát is várhatnak a kisebb az eredmények megőrzése, feltéve, hogy a megőrzött eredmények csak akkor használhatók:

- Ahhoz, hogy a felhasználó számára, hogy az eszközről (például esetén a szolgáltatás megszakadásának), hogy a felhasználó korábban visszaadott eredmények elérését.
- A személyre szabott várható a felhasználói igényeket, annak a felhasználónak a jeleket (például esetén várható szolgáltatáskiesést) alapján, a proaktív lekérdezésben visszaadott eredményeket tárolja.

#### <a name="server"></a>Kiszolgáló

Hogy előfordulhat, hogy biztonságosan szabályozhatja kiszolgálón egy felhasználó adott eredményeket, és csak a megőrzött találatok megjelenítéséhez:

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

