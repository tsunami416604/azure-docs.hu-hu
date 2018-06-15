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
ms.openlocfilehash: 174af83686eba665a729246be7a477b9a5054f30
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "35349940"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Bing keresési API-t használja, és megjeleníti a követelmények

Egyetlen megvalósítása sem a tartalom és a kapcsolódó információkat használhatja, és a megjelenített követelmények vonatkoznak. Például követelményeit kapcsolatokat, metaadatokat és egyéb jeleket. Ezek lehetnek a következő API-k hívásainak keresztül érhető el:

- Bing – Egyéni keresés
- Bing – Entitáskeresés
- Bing – Képkeresés
- Bing – Hírkeresés
- Bing – Videokeresés
- Bing vizuális keresés
- Bing – Internetes keresés
- Bing – Helyesírás-ellenőrzés
- Bing – Automatikus kiegészítés

Ezek a követelmények, a dokumentáció a speciális szolgáltatások és az eredmények kapcsolatos részleteket talál.     

## <a name="bing-spell-check-and-bing-autosuggest-apis"></a>Bing helyesírás-ellenőrző és a Bing automatikus kiegészítési API-k

nem:

- Másolja, tárolni vagy Bing helyesírás-ellenőrzés vagy a Bing automatikus kiegészítési API-k származó adatok gyorsítótárazásához.
- Bing helyesírás-ellenőrzés vagy a Bing automatikus kiegészítési API-k olyan gépi tanulás vagy hasonló algoritmikus részeként kapni adatok felhasználásával. Betanítása, kiértékelheti, vagy új vagy meglévő szolgáltatások, amelyet Ön vagy harmadik felek kínálhat javítására használja ezeket az adatokat.

## <a name="definitions"></a>Meghatározások

- *Válasz* a válaszban visszaadott eredmények kategória hivatkozik. Például a webes Bing keresési API válaszára felvehető válaszok weblap eredmények, kép, kép, visual és hírek kategóriáinak.   
- *Válasz* azt jelenti, hogy minden válaszok és az adatok egy keresési API-t egyetlen meghívása válaszként kapott.
- *Eredmény* választ adatainak elemre hivatkozik. Például egy egyetlen hírek cikk kapcsolódó adatok lesz az eredménylista egy hírek választ.
- *Keresési API-k* : együttesen, a Bing egyéni keresés, entitás keresési, kép keresési, hírek keresési, videó keresési, Visual keresési és webes keresési API-k. 


## <a name="search-apis"></a>Keresési API-k

Ebben a szakaszban a követelmények vonatkoznak a keresési API-k. A keresési API-k nem tartalmaznak, a Bing helyesírás-ellenőrzés vagy Bing automatikus kiegészítési. E két API-k követelményei az előző szakaszban ismertetett.

### <a name="internet-search-experience"></a>Internetes keresési funkciók

Válaszok szereplő összes adatok csak internetes keresési elhatározásuk használhatók. Internetes keresési élményt azt jelenti, hogy a megjelenített, amelyet a tartalom: 
- Megfelelő és a közvetlen lekérdezés a végfelhasználó vagy más arra utal, hogy a felhasználó keresési iránt, és a leképezés (például egy felhasználó jelzett keresési lekérdezés) gyors. 
- Segít a felhasználóknak található, és keresse meg az adatforrások (például a megadott URL-címek valósíthatók meg hivatkozásként, így a tartalom vagy attribútumára egy kattintható hivatkozássá feltűnően jelenik meg, amely az adatok). Vagy a Bing entitás keresési API-JÁNAK használatakor látható módon kapcsolja bing.com URL-cím, amely lehetővé teszi a felhasználó számára keresse meg a keresési eredmények között a megfelelő lekérdezés bing.com a válaszban.
- A felhasználó számára a több eredményeit tartalmaz (például, a hírek válasz több eredményei jelennek meg, vagy ha kevesebb mint több minden eredmény akkor minősül). 
- Egy meghatározott, megfelelő keresési erre a célra (például miniatűrök vannak miniatűr arányában a felhasználó megjelenített kép) kiszolgálására korlátozódik. 
- A látható jele annak a felhasználónak, hogy a tartalom-e internetes keresési eredmények között (például egy utasítást, hogy a tartalma "from a web") tartalmaz.
- A használatára a keresési API-k érkező adatokat nem sért semmilyen vonatkozó jogszabályt vagy külső jogosultságok biztosításához megfelelő intézkedéseket más kombinációját tartalmazza. Például ha Creative Commons licenchez, elfogadja és betartja a vonatkozó licencfeltételt. A jogi tanácsadók intézkedések lehet meghatározni a megfelelő tájékoztatást.
Az internetes keresési élményt követelmény az egyetlen kivétel van URL-címet a felderítéshez, a cikkben leírtak szerint. 

### <a name="restrictions"></a>Korlátozások

nem:

- Másolja, tárolni vagy összes adatát (kivéve a "Szolgáltatás folytonosságának" című cikkben által megengedett mértékben megőrzési) válaszok gyorsítótárazza. 
- Használjon olyan gépi tanulás vagy hasonló algoritmikus részeként a keresési API-k által küldött adatokat. Betanítása, kiértékelheti, vagy új vagy meglévő szolgáltatások, amelyet Ön vagy harmadik felek kínálhat javítására használja ezeket az adatokat.
- Módosítsa a tartalom az eredmények (másik formázza újra őket úgy, hogy nem sért semmilyen más követelmény), kivéve, ha a törvény szerint szükséges, vagy a Microsoft által jóváhagyott. 
- Hagyja ki ezt a jóváírás és eredmény tartalom társított URL-címeket.
- Átrendezése, például úgy, hogy elhagyása, a választ, jelenik meg, ha egy rendelés rangsorolási megadni, ha a törvény szerint szükséges, vagy a Microsoft által jóváhagyott eredmények. (A Bing egyéni keresési API-hoz, ez a szabály nem vonatkozik a customsearch.ai portálon keresztül megvalósított újrarendezése.)
- Jeleníti meg a választ valamely része egyéb tartalmakra oly módon, hogy a felhasználó úgy érzi, hogy a többi tartalom része a választ. 
- Megjeleníti a hirdetési, amely a Microsoft nem biztosítja a bármelyik oldalt, amely megjeleníti a választ valamely része. 
- A Bing lemezképről, hírek keresési, videó-keresési vagy Visual keresési API-k; (i) válaszidejű hirdetését megjelenítése vagy (ii), amely szűrt vagy korlátozott elsősorban (vagy kizárólag) kép, híreket és/vagy videó vagy visual eredmény.

### <a name="notices-and-branding"></a>Márkajelzési és értesítések 

- Funkcionális hivatkozás észrevehető tartalmazza a [Microsoft adatvédelmi nyilatkozatát](https://go.microsoft.com/fwlink/?LinkId=521839), minden egyes ponton a felhasználói élmény (UX), amely a felhasználó segítségével elvégezheti a keresési lekérdezés bemeneti közelében. A hivatkozás címke **Microsoft adatvédelmi nyilatkozatát**.
- Védjegyek, konzisztens Bing észrevehető megjelenítése a [Bing védjegyekre használatára vonatkozó irányelvek](https://go.microsoft.com/fwlink/?linkid=833278), minden pontján, amely a felhasználó keresési lekérdezés bevitel lehetőséget nyújt a UX közelében. Ilyen branding kell egyértelműen jelöl a felhasználó számára, hogy a Microsoft működtetéséhez-e az internetes keresési élményt biztosít.
- Minden válasz (vagy egy válasz része) jelenik meg a keresést a Bing kép keresési, hírek keresési, videó keresési és vizuális keresési API-k, hogy a Microsoft, kivéve, ha a Microsoft ellenkező esetben adja meg a használatra írásban is attribútum. Erről a [Bing védjegyekre használatára vonatkozó irányelvek](https://go.microsoft.com/fwlink/?linkid=833278). 
- Nem attribútum válaszok (vagy a válaszok részeit) látható az egyéni a Bing keresési API a Microsoftnak, kivéve, ha a Microsoft egyéb határozza meg a írása az adott használatra.

### <a name="transferring-responses"></a>Válaszok átvitele

Ha engedélyezi egy másik felhasználónak, a keresés API válaszára adatátviteli, mint egy üzenetkezelési alkalmazáshoz vagy a közösségi média könyvelés, a következő apply keresztül: 
- Átvitt válaszok kell:
  - Tartalom változatlan a átadó felhasználó válaszok tartalmából áll. A módosításokat a rendszer megengedett.
  - A metaadatok formában nem vesz be semmilyen adatot.
  - A válaszok a Bing webes, kép, híreket, videó és vizuális API-k megjelenítési nyelv jelző Bing technológiával internetes keresési élményt keresztül lett lekérve. Például például a "Bing által az alkalmazás bekapcsolja" vagy "További információ" További információk a lemezkép nyelvi megjelenítheti a Bing, vagy a Bing embléma is használhat.
  - A válaszok a Bing egyéni keresési API-n megjelenítési nyelv jelző kapott keresztül egy internetes keresési élményt biztosít. Például megjelenítheti a nyelvet, például a "További információk a keresési eredmény."
  - Hangsúlyosan jelenítheti meg a teljes lekérdezést, a válasz létrehozásához használt.
  - Közé tartozik a jól láthatóan elhelyezett hivatkozás vagy hasonló attribútumára a válasz az alapul szolgáló felé, vagy közvetlenül a keresőmotor (bing.com, m.bing.com vagy az egyéni search szolgáltatás, értelemszerűen) keresztül.
- Előfordulhat, hogy nem automatizálhatja a válaszok átvitelét. Egy átviteli egyértelműen részvényalapba történő tanúsító választ átvitele megjelölésű felhasználói műveletet kell kezdeményeznie.
- Csak a átadó felhasználó lekérdezésre válaszul megjelenő válaszok adatátviteli engedélyezheti.

### <a name="continuity-of-service"></a>A szolgáltatás folytonosságának 

Nem másolja, tárolni vagy keresési API-válaszok adatokat gyorsítótárazza. Ahhoz, hogy a szolgáltatás-hozzáférés és az adatok ábrázolásakor folytonosságának, azonban kizárólag a következő feltételek eredmények megőrzése előfordulhat, hogy:

**eszköz.** Itt engedélyezheti egy felhasználó egy eszközön a kevesebb i 24 órás abból az időből, amikor az eredményeket a lekérdezést, vagy (ii) addig, amíg a felhasználó elküld egy másik a frissített eredmény elérése érdekében megőrizni, feltéve, hogy a megőrzött eredmények csak akkor használhatók:

- Ahhoz, hogy a felhasználót azokon az eszközökön (például esetén szolgáltatáskiesést), hogy a felhasználó a korábban visszaadott eredmény eléréséhez.
- A proaktív lekérdezés eredményének tárolására személyre szabott várható a felhasználói igények, a felhasználó jelek (például esetén várható szolgáltatáskiesést) alapján.

**A kiszolgáló.** Előfordulhat, hogy a biztonságos egy kiszolgálón szabályozhatja a felhasználó egyetlen eredmények megőrzése, és csak a megőrzött eredmények megtekintése:

- Ahhoz, hogy a felhasználó elérheti a megoldás a felhasználó korábban visszaadott eredmények korábbi jelentést. Az eredmények lehet, hogy nem (i) megőrizve a végfelhasználó kezdeti lekérdezés óta több mint 21 napos, és (ii) jelenik meg a felhasználó új vagy ismétlődő lekérdezése adott válaszként.
- A proaktív lekérdezés eredményének tárolására személyre szabott a felhasználói igények alapján, a felhasználó jelek várható. Ezekkel az eredményekkel esetében a kevesebb i 24 órás abból az időből, amikor a lekérdezést, vagy (ii) addig, amíg a felhasználó elküld egy másik a frissített eredmények tárolhatja.

Őrzi meg, amikor egy adott felhasználó eredmények nem commingled egy másik felhasználó eredményekkel. Ez azt jelenti, hogy minden felhasználó eredményeit őrződnek meg kell, és külön-külön tartalmazott.

### <a name="general"></a>Általános kérdések 

Az összes megőrzött eredmények bemutatása:

- Törölje a jelet, látható értesítést az idő, a lekérdezés elküldve tartalmazza.
- A felhasználó vagy hasonló, amelyben egy gomb újra lekérdezni, és megszerezhetik az azt jelenti, hogy jelen eredmények frissítése. 
- A Bing, az eredmények a bemutatóban branding megőrzése mellett.
- Törlése (és szükség esetén új lekérdezés frissítése) belül meghatározott korlátozta tárolt eredménye.

### <a name="non-display-url-discovery"></a>Nem-megjelenítési URL-cím felderítése 

Egy nem internetes keresési élményt nyújt csak használhat keresési válaszok az azzal a céllal, a követező URL-címeket, a felhasználó vagy felhasználói lekérdezés rugalmas információforrás. Ilyen URL-címek kimásolhat egy jelentésben vagy hasonló választ, adja meg:

- Csak az adott felhasználó vagy felhasználói, a válasz a lekérdezésre.
- Csak akkor, ha ez magában foglalja a jelentős további tartalmat, a lekérdezés kapcsolódik.

A korábbi szakaszokban keresési API-k használata, és a megjelenített követelmények nem vonatkoznak a nem megjelenítési használja, kivéve a következőket: 

- Nem gyorsítótárazza, másolni vagy tárolja a adatok vagy a tartalmat, vagy abból származó, a keresési választ, a korlátozott URL-cím másolása a fentiekben ismertetett eltérő.
- Győződjön meg arról, a keresési API-k kapott adatokkal (ideértve az URL-címek) használatát nem sért semmilyen vonatkozó jogszabályt vagy külső jogok.
- Ne használja a keresési API-k érkező minden keresési index vagy a machine learning vagy hasonló algoritmikus tevékenység részeként (beleértve az URL-címek) adatokat. Használja ezeket az adatokat a vonat létrehozására, értékelje ki és szolgáltatás, amelyet Ön vagy harmadik felek kínálhat javítása.

## <a name="gdpr-compliance"></a>GDPR megfelelőségi  

Bármely adatok érvényesek az Európai Unió általános Data Protection szabályozás (GDPR), és hogy a keresési API-k, a Bing helyesírás ellenőrizze API, illetve a Bing automatikus kiegészítési API hívásainak kapcsolódóan dolgoz fel akkor megismeréséhez, hogy Ön és a Microsoft független adatkezelők a GDPR alatt. Egymástól függetlenül való telepítésért felelős a megfelelés a GDPR.  

