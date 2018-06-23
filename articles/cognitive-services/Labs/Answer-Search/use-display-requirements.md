---
title: Válasz keresés használata projektre, és megjeleníti a követelmények – Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Használja, és megjeleníti a projekt válasz keresése végpont követelményei.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 6e8eaaaa2c83a1420f2de86b23e15f4f19f7a565
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348707"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Projekt válasz keresése használja, és megjeleníti a követelmények

Használatát és a megjelenített követelmények érvényesek egyetlen megvalósítása sem a tartalom és a kapcsolódó információkat, például kapcsolatokat, a metaadatok és a más jeleket, a Bing keresés az Ismeretcikkek között, a Bing egyéni keresés, a entitás-keresés, a lemezkép keresési hívásainak keresztül érhető el Hírek keresési, videó keresési, Visual keresési webes keresés, helyesírás-ellenőrzés és automatikus kiegészítési API-k. Ezek a követelmények kapcsolatos részleteket a speciális szolgáltatások és az eredmények dokumentációjában található.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Bing helyesírás-ellenőrző és a Bing automatikus kiegészítési API-t.

nem:

- másolja, tárolni vagy kapni a Bing helyesírás-ellenőrzés, vagy a Bing automatikus kiegészítési API-k adatok gyorsítótárazása
- használati adatokat kapott a Bing helyesírás-ellenőrzés vagy a Bing automatikus kiegészítési API-k olyan gépi tanulás vagy hasonló algoritmikus betanításához részeként értékeli, vagy kínálhat, vagy harmadik felek számára új vagy meglévő szolgáltatások javítása.

## <a name="2-definitions"></a>2. Meghatározások

- a válaszban visszaadott eredmények kategória "válasz" hivatkozik. Például a webes Bing keresési API válaszára tartalmazhatnak válaszok a weblap eredményeket, kép, videó és hírek; kategóriái
- "válasz" azt jelenti, hogy minden válaszok és az adatok egy keresési API-JÁNAK; egyetlen meghívása válaszként kapott
- "eredménye" választ adatainak elemre hivatkozik. Például egy egyetlen hírek cikk kapcsolódó adatok lesz az eredménylista egy hírek választ.
- "Keresési API-k" azt jelenti, együttesen, a Bing egyéni keresés, entitás keresési, kép keresési, hírek keresési, videó keresési, Visual keresési, és webes keresési API-k. 


## <a name="3-search-apis"></a>3. Keresési API-k

Ez a szakasz 3 követelményeit a keresési API-k.

**EGY. Internetes keresési élményt biztosít.** Válaszok szereplő összes adatok csak internetes keresési elhatározásuk használhatók. Internetes keresési élményt azt jelenti, hogy a megjelenített, amelyet a tartalom: 
- megfelelő és rugalmas, a végfelhasználó a közvetlen lekérdezési vagy más arra utal, hogy a felhasználó keresési iránt, és a leképezés (például felhasználói jelzett keresési lekérdezés); 
- segít a felhasználóknak található, és keresse meg az adatforrások (például a megadott URL-címek valósíthatók hiperhivatkozásként így a tartalom vagy attribútumára feltűnően jelenik meg, amely az adatok egy kattintható hivatkozássá); vagy, ha a Bing entitás keresési API-t látható módon csatolni bing.com URL-cím, amely lehetővé teszi a felhasználó számára keresse meg a keresési eredmények között a megfelelő lekérdezés bing.com; a válaszban
- a végfelhasználó számára a több eredmény tartalmazza (például a hírek válasz több eredményei jelennek meg vagy összes Ha kevesebb mint több eredményeinek); 
- csak egy meghatározott, megfelelő tudja keresési (például a miniatűrök vannak miniatűr arányában a felhasználó megjelenített kép); 
- látható jele annak, hogy a tartalom-e internetes keresési eredmények között (például egy utasítást, hogy a tartalma "From a web"); a végfelhasználó tartalmazza és
- Győződjön meg arról, a használatára a keresési API-k érkező adatokat nem sért semmilyen vonatkozó jogszabályt vagy külső rights (Ha például egy Creative Commons licenc, a megfelelő licenc megfelel a függő megfelelő intézkedéseket más kombinációját tartalmazza feltételek). A jogi tanácsadók intézkedések lehet meghatározni a megfelelő tájékoztatást.
Az internetes keresési élményt követelmény az egyetlen kivétel van URL-címet a felderítéshez szakasz 3E (URL-cím nem-megjelenítése felderítési) következő leírtak szerint. 

**B. Korlátozások.** nem:

- másolja, tárolni vagy összes adatát (kivéve a megőrzési a "Szolgáltatás folytonosságának" szakasz következő által megengedett mértékben); válaszok gyorsítótárazása 
- a keresési API-k olyan gépi tanulás vagy hasonló algoritmikus részeként fogadott adatok segítségével betanítása, értékelje ki, vagy kínálhat, vagy harmadik felek számára új vagy meglévő szolgáltatások javítása.
- az eredmények (eltérő formázza újra őket úgy, hogy nem sért semmilyen más követelmény) tartalmának módosítása, kivéve, ha a törvény szerint szükséges, vagy azokkal egyetértek. a Microsoft; 
- hagyja ki ezt a jóváírás és eredmény tartalom; társított URL-címek
- újrarendelési, például úgy, hogy elhagyása, a válasz jelenik meg, ha egy sorrend, illetve rangsorolási megadott eredmények (a Bing egyéni keresési API-hoz, ez a szabály nem vonatkozik a customsearch.ai portálon keresztül megvalósított átrendezése), kivéve, ha a törvény szerint szükséges, vagy a Microsoft által ;
- a választ valamely része egyéb tartalmakra megjelenítése oly módon, hogy a végfelhasználó számára úgy érzi, hogy a többi tartalom része a válasz; 
- a Microsoft által bármely lapján, amely megjeleníti a választ; valamely része nem biztosított hirdetési -a Bing lemezképről, híreket, vagy videó keresési API-k; (i) válaszidejű hirdetését megjelenítése vagy (ii), amely szűrt vagy korlátozott elsősorban (vagy kizárólag) kép, híreket és/vagy videó eredményét.

**C. Megjegyzések és Branding.** 

- Kiemelt helyen tartalmazza a Microsoft adatvédelmi nyilatkozatát, elérhető funkcionális hivatkozás https://go.microsoft.com/fwlink/?LinkId=521839, minden egyes ponton a felhasználói élmény (UX), amely a felhasználó segítségével elvégezheti a keresési lekérdezés bemeneti közelében. A hyperlink "Microsoft Privacy Statement" címkével.
- Védjegyek, konzisztens a rendelkezésre álló útmutatókat Bing észrevehető megjelenítése https://go.microsoft.com/fwlink/?linkid=833278, minden egyes ponton az, hogy a felhasználó segítségével elvégezheti a keresési lekérdezés bemeneti UX közelében.  Ilyen branding kell egyértelműen jelöl a felhasználó számára, hogy a Microsoft működtetéséhez-e az internetes keresési élményt biztosít.
- Minden válasz (vagy egy válasz része) jelenik meg a Bing webes, kép, híreket és videó API-k a Microsoft a lehetséges, hogy attribútum https://go.microsoft.com/fwlink/?linkid=833278, kivéve, ha a Microsoft másképpen írásban a használatra. 
- Nem attribútum válaszok (vagy a válaszok részeit) látható az egyéni a Bing keresési API a Microsoftnak, kivéve, ha a Microsoft egyéb határozza meg a írása az adott használatra.


**D. Válaszok átvitele.** Ha engedélyezi egy másik felhasználónak, a keresés API válaszára adatátviteli, mint egy üzenetkezelési alkalmazáshoz vagy a közösségi média könyvelés, a következő apply keresztül: 
- Átvitt válaszok kell:
  - Tartalom változatlan a átadó felhasználó válaszok tartalmából áll (a formázási módosítások megengedett);
  - Nem vesz be semmilyen adatot metaadatok formában;
  - A válaszok a Bing webes, kép, híreket és videó API-k, megjelenítési nyelv jelző kapott egy internetes keresési élményt biztosít a Bing technológiával keresztül (például "Az alkalmazás bekapcsolja által a Bing," "ismerje a Bing a lemezkép", vagy a Bing embléma használatával);
  - A válaszok a Bing egyéni keresési API-n megjelenítési nyelv, amely jelzi, a válaszban kapott keresztül egy internetes keresési funkciók (például "További információ a keresési eredmény");
  - A teljes válasz; létrehozásához használt lekérdezés észrevehető megjelenítése és
  - Közé tartozik a jól láthatóan elhelyezett hivatkozás vagy hasonló attribútumára a válasz az alapul szolgáló felé, vagy közvetlenül a keresőmotor (bing.com, m.bing.com vagy az egyéni search szolgáltatás, értelemszerűen) keresztül.
- Előfordulhat, hogy nem automatizálhatja a válaszok átvitelét. Egy átviteli egyértelműen részvényalapba történő tanúsító választ átvitele megjelölésű felhasználói műveletet kell kezdeményeznie.
- Csak a átadó felhasználó lekérdezésre válaszul megjelenő válaszok adatátviteli engedélyezheti.

**E. Szolgáltatási folytonosság érdekében.** Nem másolja, tárolni vagy keresési API-válaszok adatokat gyorsítótárazza. Ahhoz, hogy a szolgáltatás-hozzáférés és az adatok ábrázolásakor folytonosságának, azonban kizárólag a következő feltételek eredmények megőrzése előfordulhat, hogy:

**Eszköz.** Itt engedélyezheti a végfelhasználó számára tartsa meg a lekérdezés vagy (ii) addig, amíg a felhasználó egy másik frissített eredmények elküld egy eszközön a kevesebb i 24 órás abból az időből, amikor az eredményeket, feltéve, hogy a megőrzött eredmények csak akkor használhatók:

- Ahhoz, hogy a végfelhasználó azokon az eszközökön (például esetén szolgáltatáskiesést); záró felhasználó korábban visszaadott eredmény eléréséhez vagy
- a proaktív lekérdezés eredményének tárolására személyre szabott várható a végfelhasználó kell, hogy végfelhasználói jelek (például esetén várható szolgáltatáskiesést) alapján.

**A kiszolgáló.** Eredmények biztonságosan kiszolgálón vezérlik, és csak a megőrzött eredmények megtekintése a záró egyetlen felhasználó megőrizheti:

- Ahhoz, hogy a felhasználó a korábban adott vissza a megoldásban, felhasználó, feltéve, hogy az eredmények lehet, hogy nem (i) őrzi meg a végfelhasználó kezdeti lekérdezés óta több mint 21 napos és (ii) jelenik meg egy záró u válaszul eredmények előzményjelentés eléréséhez új vagy ismétlődő lekérdezés ser meg; vagy
- a proaktív lekérdezés eredményének tárolására személyre szabott várható a végfelhasználó kell a kevesebb i 24 órás a óta, a lekérdezés vagy (ii) addig, amíg a felhasználó egy másik frissített eredmények elküld a végfelhasználó jelek alapján.

Őrzi meg, amikor egy adott felhasználó eredmények nem lehet commingled eredményekkel egy másik felhasználó, azaz, minden felhasználóhoz eredményeit kell maradnak, és külön-külön tartalmazott.

**Általános.** Az összes megőrzött eredmények bemutatása:

- az idő, a lekérdezés küldték, törölje a jelet, látható értesítést tartalmazza
- a felhasználók bemutatásához egy gombra való kattintást vagy hasonló azt jelenti, hogy újra lekérdezéséhez és frissített eredmények 
- tartsa meg a Bing, az eredmények a bemutatóban branding és
- törlése (és szükség esetén új lekérdezés frissítése) belül meghatározott korlátozta tárolt eredménye.

**F Nem-megjelenítési URL-cím felderítés.** Egy nem internetes keresési élményt nyújt csak használhat keresési válaszok az azzal a céllal, a követező URL-címeket, a felhasználó vagy felhasználói lekérdezés rugalmas információforrás. Ilyen URL-címek egy jelentésben vagy hasonló válasz (i) csak a felhasználó számára biztosított másolja át, vagy -ügyfelekre válaszként, hogy a lekérdezés és (ii), amely jelentős értékes tartalmakra kapcsolódik a lekérdezés tartalmazza. Szakaszok 3A-3E ezek használatát és a megjelenített követelmények követelményei nem vonatkozik a nem megjelenítési használatára, kivéve: 

- Nem kell gyorsítótárazni, másolása, vagy tárolja az adatokat, vagy a tartalom, vagy abból származó, a keresési válasz eltérő korlátozott URL-cím másolása, korábban leírt;
- Győződjön meg arról, a keresési API-k kapott adatokkal (ideértve az URL-címek) használatát nem sért semmilyen vonatkozó jogszabályt vagy külső jogosultságok; és
- Meg kell nem segítségével (beleértve az URL-címek) vagy a keresési API-k olyan search-index vagy gépi tanulás vagy hasonló algoritmikus részeként fogadott adatok vonat létrehozása, kiértékelheti és javíthatja a szolgáltatásokat, akkor vagy harmadik felek kínálhat.

## <a name="next-steps"></a>További lépések
[Válasz keresési áttekintése](overview.md)
