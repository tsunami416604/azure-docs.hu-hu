---
title: Piactéri ajánlatok és szolgáltatások közzétételi feltételei | Azure piactér
description: Ez a cikk azt ismerteti, hogyan lehet az Azure Marketplace-en való közzétevővé válni.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: dsindona
ms.openlocfilehash: ca40e8e00cb72d6ecc3246ad7aef3b3bac4fbedd
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160477"
---
# <a name="azure-marketplace-listing-guidelines"></a>Az Azure Marketplace listázási irányelvei

Ez a dokumentum az Azure Marketplace-en listázható új ajánlatok és szolgáltatások követelményeit és irányelveit tartalmazza.

Minden ajánlatnak meg kell felelnie az "összes ajánlatra vonatkozó követelmények" szakaszban felsorolt követelményeknek.

A speciális listaelemekhez további követelmények és ellenőrzőlisták tartoznak, beleértve a következőket: 
* [Próbaverziós ajánlatra vonatkozó követelmények](#trial-offer-requirements)
* [SaaS-alkalmazásokra vonatkozó követelmények](#saas-app-requirements)
* [A Container-ajánlat követelményei](#container-offer-requirements)
* [A tanácsadási ajánlat követelményei](#consulting-offer-requirements)

## <a name="listing-requirements-for-all-offers"></a>Az összes ajánlatra vonatkozó követelmények listázása

|  | Elem listázása | Alapkövetelmény | Optimális követelmény |
|:--- |:--- |:--- |:--- |
| 1 | Érdeklődő célhelye | Egy vezető célhely van konfigurálva.| Az egyik kereskedelmi partneri (OCP-) katalógus a partneri megoldás lapon is szerepel a vezető cél CRM-információkkal. | 
| 2 | Ajánlat címe | Röviden leírja a megoldás ajánlatát. Megfelel a megoldás online promóciójának a partner webhelyén. | A legfontosabb keresési szavakat tartalmazza. |
| 3 | Embléma | Az embléma helyesen jelenik meg. | Az embléma helyesen jelenik meg, és tartalmaz egy "Hero-rendszerképet", amely a Azure Portal nagy formátumú képe. |
| 4 |  Ajánlat leírása | <ul><li> 2-3 bekezdést tartalmaz.</li><li>A megoldási ajánlat könnyen értelmezhető egy pillantással.</li><li>A helyesírási és nyelvtani hibáktól mentes.</li><li>Az átfogó és a célközönséget, a felhasználó típusát és az értékes (Value kiosztást) is rögzíti.</li><li>A bekezdésben a rövid mondatok alapján könnyen értelmezhető.</li></ul> | <ul><li> A célként megadott iparág (ha van ilyen).</li><li>A megfelelő stílus formázása minden bekezdéssel együtt, amely egyetlen mondattal vagy kifejezéssel összegzi az alábbi tartalmakat, és szükség esetén felsorolásjeles pontokat használ, hogy kiemelje a legfontosabb előnyöket. A cél az, hogy az olvasó könnyen látható formában tekintse meg az ajánlatot, és nem kell hosszú bekezdéseket olvasnia.</li><li>Az egyes bekezdések között térköz van. Például egy autós brosúra beolvasása. Ez azt mutatja, hogy átfogó, és az ajánlat leírása egyszerűen, technikai szakzsargon nélkül történik. | 
| 5 | Kategóriák és iparágak | <ul><li>A kategóriák megfelelnek a megoldás ajánlatának képességeinek.</li><li>Ne terjesszen olyan kategóriákra, amelyeken a megoldás nem fér el.</li><li>Az optimális iparág vagy az összes kiválasztott iparág (ha nem az optimális iparágak). | <ul><li>Legfeljebb 3 kategória.</li><li>Legfeljebb 3 iparág. |
| 6 | Képek | <ul><li>Nincs szükség rendszerképre, de ha meg van adni, nagy felbontású képként kell megjeleníteni.</li><li>A rendszerképekre vonatkozó követelmények a Cloud Partner Portal (CPP) és itt láthatók.</li><li>A képernyőképen látható szöveg olvasható, a rendszerkép pedig egyértelmű. | A megoldási ajánlat könnyen értelmezhető egy pillantással. |
| 7 | Videók | <ul><li>Nincs szükség videóra, de ha meg van adni, hiba nélkül kell visszajátszania.</li><li>Ha meg van adni, előfordulhat, hogy nem hivatkozik a versenytárs vállalatokra, *kivéve* , ha egy áttelepítési megoldást mutat be. |<ul><li>Ideális esetben a hossz 3 perc vagy több.</li><li>A megoldási ajánlat könnyen értelmezhető a videotartalom használatával.</li><li>A megoldás képességeinek bemutatóját biztosítja. |
| 8 | Lista állapota (művelet meghívása) | <ul><li>A címkét a következő típusok egyikének kell megadnia: <ul><li>*Kapcsolatfelvétel*</li><li>*Trial*/*Test Drive* *Get Trial Now*/*Start Trial*Próbaverzió lekérése – próbaverzió kipróbálása/</li><li>*Buy Now*/*Azonnali* vásárlás</li></ul></ul> | Az ügyfél könnyen megismerheti a következő lépéseket: <ol><li>Próbálja ki a próbaverziót.</li><li>Vásároljon most.</li><li>Vegye fel a kapcsolatot e-mailben vagy telefonszámon, hogy intézkedik a megvalósíthatósági igazolásról (POC), az értékelésről vagy a tájékoztatásról.</li></ol> | 
| 9 | Megoldás díjszabása | A megoldás díjszabási lapjának/részleteinek kell lennie, és a díjszabásnak a partneri megoldás ajánlatának helyi pénznemében kell lennie. | A vásárlói beállítások megadásához több számlázási lehetőség is rendelkezésre áll. |    
| 10 | Részletek | Az alján található hivatkozások (a Leírás alatt, nem az Azure Marketplace hivatkozásai a bal oldalon) a megoldással kapcsolatos további információkhoz vezethetnek, és nyilvánosan elérhetők és megfelelően jelennek meg. |    Adott elemekre mutató hivatkozások (például a partner webhelyén található spec-lapok) és nem csak a partner kezdőlapja. | 
| 11 | Megoldás támogatása és Súgó | Hivatkozás a következők legalább egyikére: <ul><li>Telefonszámok</li><li>E-mail-támogatás</li><li>Csevegési ügynökök</li><li>Közösségi fórumok |<ul><li>Az összes támogatási módszer szerepel a felsorolásban.</li><li>A fizetős támogatás díjmentesen használható a *próbaidőszak* vagy a *tesztelési* időszak alatt. | 
| 12 | Jogi tudnivalók | A szabályzatok vagy kifejezések nyilvános URL-címen keresztül érhetők el. |  |

--- 

## <a name="trial-offer-requirements"></a>Próbaverziós ajánlatra vonatkozó követelmények

|  | Elem listázása | Alapkövetelmény | Optimális követelmény | 
|:--- |:--- |:--- |:--- |
|  | Lista állapota (művelet meghívása) | A hivatkozásnak az ügyfél által vezetett *próbaidőszakot* kell eredményeznie. | Más műveletekre irányuló hívások (például a *vásárlás most*) is elérhetők. | 

---

## <a name="saas-app-requirements"></a>SaaS-alkalmazásokra vonatkozó követelmények

|  | Elem listázása | Alapkövetelmény | Optimális követelmény | 
|:--- |:--- |:--- |:--- |
| 1 | Ajánlat címe |<ul><li>Csak kisbetűket, alfanumerikus karaktereket, kötőjeleket vagy aláhúzásokat tartalmazhat. A cím közzététele után nem módosítható.</li><li>A megoldás ajánlatát ismerteti.</li><li>A megoldás online előléptetésének felel meg a partner webhelyén. | A legfontosabb keresési szavakat tartalmazza. |
| 2 | Technikai információ: konfiguráció |<ul><li>SaaS-alkalmazások esetén válassza ki, hogy csak az alkalmazást kívánja-e listázni, vagy hogy az ügyfelek megvásárolhatják az alkalmazást az Azure-ban.</li><li>Válassza ki az ajánlat beszerzése gombjának kívánt szövegét: *ingyenes*, *ingyenes próbaverzió*vagy *Kapcsolatfelvétel*.</li><li>Az előugró ablakban válassza ki a csak egy megfelelő terméket, ha az alkalmazás a technológiát használja: Cortana Intelligence, Power BI megoldás-sablonok vagy a Power apps. |  |
| 3 | Tesztelési meghajtó | Válassza az *Igen* vagy a *Nem* lehetőséget. | Az ügyfél könnyen megismerheti a következő lépéseket: <ol><li>Próbálja ki a próbaverziót.</li><li>Vásároljon most.</li><li>Kapcsolatfelvétel e-mailben vagy telefonszámon a POC, az értékelés vagy a tájékoztatás megszervezéséhez.</li></ol> |
| 4 | Kirakat részletei: ajánlat összegzése | Megjelenik az alkalmazás keresési oldalán, és legfeljebb 100 karakterből állhat. | |
| 5 | Kirakat részletei: iparágak | Iparágak (max. 2): válassza ki azokat az iparágakat, amelyekhez az alkalmazás a legmegfelelőbb és alkalmazható. | |
| 6 | Ajánlat leírása | <ul><li>Az egyszerű HTML használata megengedett, \<beleértve a p \<>, az \<em>, \<az UL> \<, a Li>, az ol> és a fejléc címkéit. Legfeljebb 3000 karakter.</li><li>2-3 bekezdést tartalmaz.</li><li>A megoldási ajánlat könnyen értelmezhető egy pillantással.</li><li>A Leírás átfogó, és rögzíti a célközönséget, a felhasználói típust, valamint azt, hogy miért fontos (az érték kiosztása).</li><li>A bekezdés narratív formája, rövid mondatokkal, amelyek könnyen megérthetők. | <ul><li>A célként megadott iparág (ha van ilyen).</li><li>A megfelelő stílus formázása minden bekezdéssel együtt, amely egyetlen mondattal vagy kifejezéssel összegzi az alábbi tartalmakat, és szükség esetén felsorolásjeles pontokat használ, hogy kiemelje a legfontosabb előnyöket. A cél az, hogy az olvasó megértse az ajánlatot egy könnyen megtekinthető formátumban, és nem kell a bekezdéseket beolvasnia.</li><li>Az egyes bekezdések között térköz van. Például egy autós brosúra beolvasása. Ez azt mutatja, hogy átfogó, és az ajánlat leírása egyszerűen, technikai szakzsargon nélkül történik. | 
| 7 | Marketing-összetevők | A logók helyesen jelennek meg. | <ul><li>Az embléma egy "Hero-képet" tartalmaz, amely a Azure Portal nagy formátumú képe.</li><li>Emblémák: kicsi (48 &#215; 48 képpont) és nagyméretű (216 &#215; 216 képpont) szükséges.</li><li>A [Hero-rendszerképekre vonatkozó követelmények](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-azure-apps-offer).</li><li>Képernyőfelvétel (max. 5): a CPP használatához PNG-kép szükséges, 1280 &#215; 720 képpont felbontással. |
| 8 | Kategóriák és iparágak | <ul><li>A kategóriák megfelelnek a megoldás-ajánlati lehetőségeknek.</li><li>Válasszon ki legalább egy elemet az előugró ablakban.</li><li>Ne terjesszen olyan kategóriákra, amelyeken a megoldás nem fér el.</li><li>Az optimális iparág vagy az összes kiválasztott iparág (ha nem az optimális iparágak). | Legfeljebb 3 kategória van kiválasztva, ha van ilyen. |
| 9 | Érdeklődők kezelése | Válassza ki a rendszer helyét, ahol az érdeklődőket tárolni fogja. Lásd: [ügyfél-érdeklődők beszerzése](./partner-center-portal/commercial-marketplace-get-customer-leads.md) a CRM-rendszer összekapcsolásához. |  |
| 10 | Névjegyek: megoldás-támogatás és Súgó | <ul><li>Mérnöki kapcsolattartó neve: az alkalmazáshoz tartozó mérnöki kapcsolattartó neve. Ez a kapcsolat technikai kommunikációt fog kapni a Microsofttól.</li><li>Mérnöki kapcsolattartási e-mail cím: az alkalmazás mérnöki kapcsolatának e-mail-címe.</li><li>Engineering Contacts Phone: a mérnöki Kapcsolattartó telefonszáma. Az [ISO-telefonszámok jelölései](https://en.wikipedia.org/wiki/E.123) támogatottak.</li><li>Támogatási kapcsolattartó neve: az alkalmazás támogatási partnerének neve. Ez a partner a Microsoft támogatással kapcsolatos kommunikációját fogja kapni.</li><li>Támogatási kapcsolattartási e-mail cím: az alkalmazás támogatási partnerének e-mail-címe.</li><li>Támogatási Kapcsolattartó telefonszáma: a támogatási partner telefonszáma. Az [ISO-telefonszámok jelölései](https://en.wikipedia.org/wiki/E.123) támogatottak.</li><li>Támogatási URL-cím: a támogatási oldal URL-címe. | <ul><li>Az összes támogatási módszer szerepel a felsorolásban.</li><li>A *próbaverziós* és a *tesztelési* időszak alatt ingyenes fizetős támogatást kínálunk. |
| 11 | Jogi tudnivalók |<ul><li>Adatvédelmi szabályzat URL-címe: az alkalmazás adatvédelmi szabályzatának URL-címe a CPP adatvédelmi szabályzat URL-címe mezőjében.</li><li>Használati feltételek: az alkalmazás használati feltételei. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket az alkalmazás kipróbálása előtt. |    A szabályzatok vagy kifejezések nyilvános URL-címen keresztül érhetők el. |

---

## <a name="container-offer-requirements"></a>A Container-ajánlat követelményei

|  | Elem listázása | Alapkövetelmény | Optimális követelmény | 
|:--- |:--- |:--- |:--- |
|1 |Ajánlat beállításai | <ul><li>Ajánlat azonosítója: max. 50 karakter.</li><li>Közzétevő azonosítója: válassza ki a legördülő listából.</li><li>Név: max. 50 karakter. | A leírásban már elérhető Cím stílusát tükrözi. Kerülje a hosszú címek használatát. | 
| 2 | Termékváltozatok | A partner kiválasztja az új SKU-ket.| A cím a leírásban már elérhető Cím stílusát tükrözi. Kerülje a hosszú címek használatát.  |
| 3 | Piactéri összetevők | A logók helyesen jelennek meg. |<ul><li>Az embléma egy "Hero-képet" tartalmaz, amely a Azure Portal nagy formátumú képe.</li><li>Emblémák: kicsi (48 &#215; 48 képpont) és nagyméretű (216 &#215; 216 képpont) szükséges.</li><li>A [Hero-rendszerképekre vonatkozó követelmények](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-azure-apps-offer).</li><li>Képernyőfelvétel (max. 5): a CPP használatához PNG-kép szükséges, 1280 &#215; 720 képpont felbontással.|
| 4 | Érdeklődők kezelése |<ul><li>Érdeklődők kezelése: válassza ki a rendszer helyét, ahol az érdeklődőket tárolni fogja.</li><li>Lásd: [ügyfél-érdeklődők beszerzése](./partner-center-portal/commercial-marketplace-get-customer-leads.md) a CRM-rendszer összekapcsolásához. | |

---

## <a name="consulting-offer-requirements"></a>A tanácsadási ajánlat követelményei

|  | Elem listázása | Alapkövetelmény | Optimális követelmény |
|:--- |:--- |:--- |:--- |
| 1 | Ajánlat címe  |  Egyértelműen fel kell sorolni a szolgáltatás típusát és időtartamát a formátumnév *: időtartam típusa mezőben* (például "ajánlat bevonása: 1 hetes megvalósíthatóság").  | <ul><li>A nem ismétli meg a közzétevő nevét.</li><li>A cím a leírásban már elérhető Cím stílusát tükrözi. Kerülje a hosszú címek használatát. | 
| 2 | Ajánlat leírása  |<ul><li>A [Microsoft-termékek nevének](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/en-us.aspx)megfelelő használatának biztosítása.</li><li>A (z) *árként* megjelölt ajánlatok: a becsült értéknek meg kell egyeznie a felső vagy az alsó megjegyzéssel, hogy ismertesse a változékonyságt (az ügyfélre való utazás, a migrált kiszolgálók száma stb.).</li><li>Az egyes ajánlati típusok leírási követelményekkel rendelkeznek, az alábbiak szerint: <ul><li>A tájékoztatóknak legalább négy vagy öt felsorolásjelre van szükségük, és meg kell adni a tárgyalt témakörökre vonatkozó információkat.</li><li>Az összes workshopnak napirendre van szüksége.</li><li>Az összes napirendet naponta vagy hetente, a workshop időtartamától függően kell lebontani.</li><li>Az értékelés, a POC, a nem betanítási workshopok és a megvalósítási ajánlatok szükségesek.</li><li>A képzési workshopoknak nincs szükségük a termékekre, de részletesebb napirendre van szükségük, amelyekkel foglalkozni fog.</li></ul></ul> | <ul><li>Minden ajánlatnak van napirendje és teljesítése.</li><li>Az ajánlat tartalmaz egy olyan bekezdést, amely a szolgáltatást a felső szakaszban biztosító vállalattal kapcsolatos környezettel rendelkezik.</li><li>Az ajánlat tartalmaz egy, a szolgáltatás értékével kapcsolatos bekezdést, amely a legfelső szakasz. |
| 3 | Markdown formázása  | Minden ajánlatnak Markdown-formázást kell használnia, hogy az ajánlat megfelelően legyen megjelenítve, amikor HTML-re konvertálja.  |  |
| 4 | Kategóriák és iparágak | A kategóriák nem relevánsak. |  | 
| 5 | Lista állapota (művelet meghívása) | Automatikusan *kapcsolatba lépni velem*. |  | 
| 6 | Megoldás támogatása és Súgó  | Támogatás és segítség nem szükséges.  | | 
| 7 | Az adatvédelmi szabályzat és az elérhető használati feltételek  |  A szabályzatok vagy kifejezések nem szükségesek.  | | 
| 8 | Szolgáltatások típusai | Győződjön meg arról, hogy a szolgáltatás típusa megegyezik a címmel. | |
| 9 | Kompetenciák | A következők legalább egyikében rendelkeznie kell kompetenciával:<ul><li>Alkalmazásfejlesztés</li><li>Alkalmazásintegráció</li><li>Alkalmazások életciklusának kezelése</li><li>Felhőalapú platform<li>Adatelemzés</li><li>Adatközpont</li><li>Adatplatform</li><li>DevOps |
| 10 | Termékek | Azure-termékeknek kell lennie. | |
| 11 | Ország/régió | Győződjön meg arról, hogy az ország/régió megfelel a kiválasztott pénznemnek. | |
| 12 | Részletek  | <ul><li>A lenti hivatkozások (a Leírás alatt, nem az Azure Marketplace-hivatkozások a bal oldalon) a megoldással kapcsolatos további információkhoz vezethetnek, és nyilvánosan elérhetők, és helyesen jelennek meg.</li><li>A hivatkozásoknak "felhasználóbarát" névvel kell rendelkezniük, és nem jelennek meg a letöltésekhez tartozó fájlnévként. | |

---

## <a name="next-steps"></a>További lépések
* Ismerje meg az Azure Marketplace különböző ajánlati típusait:
  * [SaaS-alkalmazások](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)
  * [Containers](https://docs.microsoft.com/azure/marketplace/marketplace-containers)
  * [Tanácsadói szolgáltatások](https://docs.microsoft.com/azure/marketplace/consulting-services)

* [A közzétételi beállítások meghatározása](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)

