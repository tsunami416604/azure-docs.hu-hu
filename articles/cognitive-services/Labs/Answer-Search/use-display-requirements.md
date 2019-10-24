---
title: Használati és megjelenítési követelmények – projekt válaszának keresése
titlesuffix: Azure Cognitive Services
description: A Project answer keresési végpontra vonatkozó követelmények használata és megjelenítése.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 2b42d61fd887f166a08b78510d5eaacb8a7cdcb8
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706709"
---
# <a name="project-answer-search-use-and-display-requirements"></a>A Project answer keresési használati és megjelenítési követelményei

A használati és megjelenítési követelmények a tartalom és a kapcsolódó információk (például kapcsolatok, metaadatok és egyéb jelek) bármely megvalósítására vonatkoznak a Bing Knowledge Search, Bing Custom Search, Entity Search, Image Search, News Search, Video Search, Visual Search, Web Search, Spell Check és automatikus javaslat API-k. Az ezekkel a követelményekkel kapcsolatos megvalósítási részletek a dokumentációban találhatók az adott funkciókhoz és eredményekhez.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Bing Spell Check és Bing Autosuggest API.

nem:

- a Bing Spell Check fogadott adatok másolása, tárolása vagy gyorsítótárazása, vagy Bing Autosuggest API-k
- a Bing Spell Check vagy Bing Autosuggest API-kkal kapott adatokkal bármely gépi tanulási vagy hasonló algoritmikus tevékenység részeként betaníthatja, kiértékelheti vagy fejlesztheti azokat az új vagy meglévő szolgáltatásokat, amelyeket Ön vagy harmadik fél is felkínálhat.

## <a name="2-definitions"></a>2. Definíciók

- a "válasz" a válaszban visszaadott eredmények kategóriájára utal. A Bing Web Search API válasza például tartalmazhat válaszokat a weblapok eredményei, a képek, a videók és a hírek kategóriájában;
- "válasz": a keresési API-ra irányuló egyetlen hívásra válaszként kapott összes válasz és kapcsolódó érték.
- az "eredmény" a válaszban szereplő információ elemére hivatkozik. Az egyetlen újságcikkhez kapcsolódó adathalmaz például a hírek válaszának eredménye.
- "Keresési API-k": együttesen, a Bing Custom Search, Entity Search, Image Search, News Search, Video Search, Visual Search és Web Search API-k. 


## <a name="3-search-apis"></a>3. Keresési API-k

A 3. szakasz követelményei a keresési API-kra vonatkoznak.

**EGY. Internetes keresési élmény.** A válaszokban visszaadott összes adat csak internetes keresési élményekben használható. Az internetes keresési élmény azt jelenti, hogy a tartalom a megfelelő módon jelenik meg: 
- fontos és reagál a végfelhasználó közvetlen lekérdezésére vagy a felhasználó keresési érdeklődésének és szándékának (például a felhasználó által jelzett keresési lekérdezés) egyéb jelzésére; 
- segítségével a felhasználók megkereshetik és megkereshetik az adatforrásokat (például a megadott URL-címeket hiperhivatkozásként kell megvalósítani, hogy a tartalom vagy a hozzárendelés egy kattintható hivatkozás, amely szembetűnően megjelenik az adatokat); Ha Bing Entity Search API, akkor a válaszban megadott bing.com URL-címre mutató hivatkozás látható, amely lehetővé teszi a felhasználó számára, hogy navigáljon a megfelelő lekérdezés keresési eredményeihez a bing.com;
- több eredményt is tartalmaz, amelyekkel a végfelhasználó kiválaszthat (például a hírekből több találat jelenik meg, vagy ha kevesebb, mint több eredményt ad vissza); 
- a keresési cél kiszolgálására alkalmas mennyiségre korlátozódik (például a képminiatűrök miniatűr méretűek, a felhasználó megjelenítési arányában); 
- magában foglalja a végfelhasználó számára, hogy a tartalom internetes keresési eredményei (például egy nyilatkozat arról, hogy a tartalom "a webről"); és
- tartalmazza a keresési API-k által fogadott adatok használatának biztosításához szükséges bármilyen egyéb megoldást, amely nem sérti a vonatkozó jogszabályokat vagy harmadik felektől származó jogokat (például ha egy Creative Commons licencre támaszkodik, amely megfelel a megfelelő licencnek). feltételek). A jogi tanácsadókkal megtekintheti, hogy milyen mértékekre lehet szükség.
Az internetes keresési élmény követelményének egyetlen kivétele az URL-címek felderítése a következő szakaszban ismertetett módon: 3E (nem megjelenített URL-cím felderítése). 

**B. Korlátozások.** nem:

- a válaszokból származó adatok másolása, tárolása vagy gyorsítótárazása (kivéve a megőrzési időt, amelyet a "szolgáltatás folytonossága" szakasz a következő szakaszban engedélyez); 
- a keresési API-k által fogadott adatok felhasználása bármely gépi tanulási vagy hasonló algoritmikus tevékenység keretében az Ön vagy harmadik felek által kínált új vagy meglévő szolgáltatások betanítására, kiértékelésére vagy javítására.
- az eredmények tartalmának módosítása (kivéve, ha olyan módon formázza őket, amely nem sérti a többi követelményt), hacsak a törvény nem írja elő vagy nem fogadja el a Microsoftot; 
- kihagyhatja az eredmény tartalmához társított hozzárendeléseket és URL-címeket;
- Átrendezés, beleértve a kihagyásokat is, a válaszban megjelenő eredmények egy adott megrendelés vagy rangsorolás megadásakor (a Bing Custom Search API esetében ez a szabály nem vonatkozik a customsearch.ai-portálon keresztül megvalósított átrendezésre), kivéve, ha törvény vagy a Microsoft nem fogadta el. ;
- más tartalmak megjelenítése a válasz bármely részén, ami azt eredményezné, hogy a végfelhasználó azt feltételezi, hogy a másik tartalom a válasz részét képezi. 
- megjelenítheti a Microsoft által nem biztosított hirdetéseket bármely olyan oldalon, amely a válasz bármely részét megjeleníti; – a Bing-rendszerképből, a hírekből vagy a Video Search API-ból származó válaszokkal (i) kapcsolatos hirdetések megjelenítése; vagy (II) a képek, Hírek és/vagy videók eredményeire elsősorban (vagy kizárólag) szűrve vagy korlátozva.

**C. Megjegyzések és védjegyezés.** 

- Kiemelten tartalmaz egy funkcionális hiperhivatkozást a Microsoft adatvédelmi nyilatkozatára, amely https://go.microsoft.com/fwlink/?LinkId=521839 a következő címen érhető el: a felhasználói élmény (UX) minden pontján, amely lehetővé teszi a felhasználó számára a keresési lekérdezés bevitelét. Címkézze fel a "Microsoft adatvédelmi nyilatkozat" hivatkozást.
- Kiemelten jeleníti meg a Bing branding-et, https://go.microsoft.com/fwlink/?linkid=833278 amely összhangban van a-ben elérhető irányelvekkel, az UX minden pontja közelében, amely lehetővé teszi a felhasználó számára a keresési lekérdezés bevitelét.  Az ilyen védjegyezésnek egyértelműen meg kell jelölnie azt a felhasználót, akit a Microsoft az internetes keresési élményben bekapcsol.
- A következő https://go.microsoft.com/fwlink/?linkid=833278 témakörben leírtak szerint a Bing web, a képek, a hírek és a videó API-k által megjelenített válaszokat (vagy válaszokat) a Microsoft számára is meghatározhatja, hacsak a Microsoft nem adja meg a használatát. 
- Ne adjon meg a Bing Custom Search APIból a Microsoftnak megjelenő válaszokat (vagy válaszokat), kivéve, ha a Microsoft másképpen ír az adott használatra.


**D. Válaszok átvitele.** Ha engedélyezi a felhasználó számára, hogy egy keresési API-ról egy másik felhasználóra vigye át a választ, például egy üzenetküldési alkalmazás vagy egy közösségi média-közzététel segítségével, a következők érvényesek: 
- Az átvitt válaszoknak a következőket kell tartalmazniuk:
  - Olyan tartalmat tartalmaz, amely nem módosult az átadó felhasználó számára megjelenített válaszok tartalmától (a formázás módosításai megengedettek);
  - Nem tartalmaz adatokat a metaadatok űrlapján;
  - A Bing web, a képek, a hírek és a videó API-któl érkező válaszokért, amely azt jelzi, hogy a válasz a Bing által működtetett internetes keresési élményen (például "a Bing által," "
  - A Bing Custom Search APIra adott válaszok esetében a válasz internetes keresési élményen (például "További információ a találatról") való megadását jelző nyelv megjelenítése.
  - Kiemelten megjeleníti a válasz létrehozásához használt teljes lekérdezést. és
  - Vegyen fel egy kiemelt hivatkozást, vagy hasonlítson a válasz alapjául szolgáló forráshoz, közvetlenül vagy a keresőmotoron keresztül (bing.com, m.bing.com vagy az egyéni keresési szolgáltatáson keresztül).
- Előfordulhat, hogy nem automatizálja a válaszok átvitelét. Az átvitelt egy felhasználói műveletnek kell kezdeményeznie, amely egyértelműen igazolja a válaszok átvitelének szándékát.
- Csak akkor engedélyezheti a felhasználók számára, hogy átvigye az átadó felhasználó lekérdezésére adott válaszban megjelenített válaszokat.

**E. A szolgáltatás folytonossága.** Ne másolja, tárolja vagy gyorsítótárazza a keresési API-válaszok adatait. A szolgáltatás-hozzáférés és az adatmegjelenítés folytonosságának lehetővé tételéhez azonban az eredményeket kizárólag az alábbi feltételek teljesülése esetén lehet megőrizni:

**Eszköz.** Lehetővé teheti a végfelhasználók számára, hogy az eredményeket a lekérdezéstől számított 24 órán belül (i), vagy (II), amíg a végfelhasználó egy másik lekérdezést nem küld a frissített eredményekre, amennyiben a megőrzött eredmények csak a következő adatokhoz használhatók:

- annak lehetővé tétele, hogy a végfelhasználó hozzáférjen az adott eszközhöz korábban visszaadott eredményekhez (például szolgáltatás megszakítása esetén); vagy
- a proaktív lekérdezés eredményeinek tárolása személyre szabottan, a végfelhasználók által a végfelhasználói igények alapján várhatóan felszámított módon (például a szolgáltatás megszakadása esetén).

**Server.** Az egyetlen végfelhasználótól származó eredményeket biztonságosan megtarthatja egy olyan kiszolgálón, amelyet Ön vezérel, és csak a megőrzött eredményeket jeleníti meg:

- annak lehetővé tétele érdekében, hogy a végfelhasználó hozzáférhessen az adott felhasználóhoz korábban visszaadott eredmények korábbi jelentéseihez a megoldásban, ha az eredmények a végfelhasználó kezdeti lekérdezésének időpontjától számítva több mint 21 napig megmaradnak, és (II) az u végére adott válaszban láthatók. a ser új vagy ismétlődő lekérdezése; vagy
- a proaktív lekérdezés eredményeinek tárolásához a végfelhasználói igények alapján, a lekérdezés időpontjának (i) 24 órájában, vagy (II) számítva, amíg a végfelhasználó nem küld egy másik lekérdezést a frissített eredményekre.

Ha megtartja, az adott felhasználóhoz tartozó eredmények nem commingled egy másik felhasználó eredményeivel, azaz az egyes felhasználók eredményeit külön kell megőrizni és teljesíteni.

**Általános.** A megőrzött eredmények összes megjelenítéséhez:

- adja meg a lekérdezés elküldésekor megjelenő egyértelmű és látható értesítést.
- a felhasználó egy gomb vagy hasonló módon jelenik meg az ismételt lekérdezéshez és a frissített eredmények beszerzéséhez. 
- tartsa meg a Bing védjegyezését az eredmények megjelenítésében, és
- törölje (és szükség esetén frissítsen egy új lekérdezéssel) a tárolt eredményeket a megadott időkereten belül.

**F. Nem megjelenített URL-felderítés.** A keresési válaszokat csak a nem internetes keresési élményekben használhatja arra, hogy a felhasználó vagy az ügyfél által küldött lekérdezésre válaszoló információforrások URL-címeinek felfedésére legyen lehetőség. Az ilyen URL-címeket egy jelentésbe vagy hasonló válaszba másolhatja (i) csak az adott felhasználóra vagy ügyfélre, a lekérdezésre válaszul, és (II), amely a lekérdezéshez szükséges jelentős további értékes tartalmat tartalmazza. A jelen használati és megjelenítési követelmények a 3A – 3. szakaszában szereplő követelmények nem vonatkoznak erre a nem megjeleníthető használatra, kivéve a következőket: 

- Nem kell gyorsítótárazni, másolni vagy tárolni a keresési válaszból származó adatokat és tartalmakat, illetve nem kell azokat a nem a korábban ismertetett korlátozott URL-címekről lemásolni;
- Győződjön meg arról, hogy a keresési API-kkal kapott adatok (beleértve a URL-címeket is) nem sértik a vonatkozó jogszabályokat vagy harmadik féltől származó jogokat; és
- Nem használhatja a keresési API-k által fogadott adatok (beleértve a URL-címeket) a keresési indexek, a gépi tanulási vagy a hasonló algoritmikus tevékenységek részeként, hogy kitanítsa az Ön vagy harmadik felek által kínált szolgáltatásokat.

## <a name="next-steps"></a>További lépések
[Válaszok keresése – áttekintés](overview.md)
