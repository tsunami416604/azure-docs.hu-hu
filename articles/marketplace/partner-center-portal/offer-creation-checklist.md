---
title: Ajánlat létrehozása feladatlista - kereskedelmi Marketplace-en az Azure-hoz
description: Az adatokat, megadhatja a ajánlat létrehozási folyamat során. – Azure Marketplace kereskedelmi
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: eb824eb67e84ec4bdb93bc355ac6a6afa844ceb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701154"
---
# <a name="offer-creation-checklist"></a>Ajánlat-létrehozási ellenőrzőlista

Az ajánlat létrehozási folyamata megoldáscsomagra oldalai között. Az alábbiakban megadhatja, hogy minden oldalon, ha többet szeretne megtudni az egyes elemek mutató hivatkozásokat tartalmaz.

Adja meg, vagy adjon meg kell elemek alatti jeleztük. Néhány területe nem kötelező, vagy az alapértelmezett értékeket a megadott, hogy igény szerint módosíthatja. Nem kell működését az ezekben a szakaszokban a itt megadott sorrendben.

| **Elem**    | **Cél**  |
| :---------- | :-------------------|
| [**Új ajánlat modális paneljére**](#new-offer-modal) | Gyűjti a azonosító adatok kínálnak.  |
| [Az ajánlat beállítása lapon](#offer-setup-page) | Részvétel a legfontosabb funkcióit használja, és válassza ki, hogy az ajánlat keretében a Microsoft hogyan teszi lehetővé.  |
| [Tulajdonságok lap](#properties-page) | Adja meg a kategóriák és csoportosíthatók az ajánlat a piactér, a jogi szerződések, az ajánlat és az alkalmazás verziójának támogatása az iparágban. |
| [Ajánlat ajánlatuk információs oldalán](#offer-listing-page) | Adja meg a Marketplace-en, beleértve az ajánlat leírásának és marketing-eszközök megjeleníteni az ajánlat részleteit. |
| [Előzetes verziójú szolgáltatásainak weblapján](#preview-page) | Korlátozott előzetes verzió közönség számára ad ki az ajánlatot, a piactér szélesebb körű audience(s) élő ajánlat közzététele előtt határozza meg. |
| [Ajánlat műszaki konfigurációs lapja](#technical-configuration-page)  | Csak akkor érhető el, ha kiválasztja, hogy az ajánlat keretében a Microsoft. Adja meg a technikai részleteket (URL-cím, webhookot, bérlő azonosítója és alkalmazás azonosítója) az ajánlat való kapcsolódáshoz használt. |
| [**Új csomag modális paneljére**](#plan-identity-modal) | Gyűjti a azonosító adatok megtervezése.  |
| [Ajánlatuk információs oldalán olyan megtervezése](#plan-listing-page)  | Csak akkor érhető el, ha kiválasztja, hogy az ajánlat keretében a Microsoft. A terv a Marketplace-en használható részleteinek megadása.  |
| [Tarifacsomag és rendelkezésre állás](#plan-pricing--availability-page)  | Csak akkor érhető el, ha kiválasztja, hogy az ajánlat keretében a Microsoft.  Gyűjti az üzleti jellemzői (díjszabási modell), a célközönség és piaci rendelkezésre állásának minden csomag (verzió) az Ön ajánlatát.  |
| [Test Drive ajánlatuk információs oldalán](#test-drive-listing-page)  | Csak akkor érhető el, ha az ajánlat az ajánlat próbálja ki. A teszt a Marketplace-en meghajtó listájához használt részleteinek megadása.  |
| Teszt meghajtó technikai konfiguráció lap  | Csak akkor érhető el, ha az ajánlat az ajánlat próbálja ki. Adja meg a technikai részleteket a bemutató (vagy "próbálja ki a"), amely lehetővé teszi az ügyfeleknek az ajánlat végrehajtása előtt meg kell vásárolnia, próbálkozzon.  |
| [Tekintse át, és tegye közzé az oldalon](#review-and-publish-page)  | Válassza ki a szükséges módosításokat közzétételéhez, megnézheti az állapotukat minden lapon, és adjon meg jegyzeteket a tanúsítvány csapatának.  |


## <a name="new-offer-modal"></a>Új ajánlat modális 

Az első adatok megadását kéri egy azonosító és az alias az ajánlatban. 

| **Mező neve**    | **Megjegyzések**   |  
| :---------------- | :-----------| 
| Ajánlat azonosítója  | Szükséges, a létrehozás után nem módosítható. Legfeljebb 50 karakter hosszú lehet, és kell állnia csak kisbetűket, alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhatnak. |
| Az ajánlat alias  | Kötelező. |

## <a name="offer-setup-page"></a>Az ajánlat beállítása lapon

Az ajánlat beállítása lap, ahol meg is mekkorák a különböző csatornákon és értékesítési mozdulatok, valamint deklarálja a legfontosabb funkcióit, például a tesztverziós és az ügyfél használatát az érdeklődők. 

| **Mező neve**    | **Megjegyzések**   | 
| :---------------- | :-----------|  
| Szeretne Microsoft keresztül?  | Kötelező. alapértelmezett érték: Igen |
| Hogyan szeretne lehetséges ügyfeleink számára, hogy az ajánlat keretében bejegyzés? (A műveleti hívás)  | Ha nem a Microsoft értékesítési szükséges. alapértelmezett érték: Ingyenes próbaverzió, beállítások: "Letöltés most", "Ingyenes próbaverzió", "Velem a kapcsolatot." |
| Próbaverziós URL-címe  | Szükséges, ha az "Ingyenes próbaverzió" van kijelölve, így ügyfeleink az működik együtt az ajánlat listáján. |
| Az ajánlat URL-címe  | Szükséges, ha a "Get most" van kijelölve, így ügyfeleink az működik együtt az ajánlat listaelem |
| Csatornák  | Választható. alapértelmezett érték: A kriptográfiai Szolgáltató (viszonteladói) csatorna nem jelentkezett.  |
| Tesztverzió | Választható. alapértelmezett érték: Nincs tesztverziós engedélyezve van.  |
| Test Drive típusa | Ha engedélyezve van a tesztverziós szükséges. alapértelmezett érték: Egy elem sincs kijelölve. Beállítások: Az Azure Resource Manager, a Dynamics 365 for Business központi, a Dynamics 365 for Customer Engagement, Dynamics 365, a műveletek logikai alkalmazást, a Power bi-ban.  |
| Felügyeleti vezető – egy CRM-rendszerrel connect | Szükséges a Microsoft értékesítési, vagy ha listaelem kínál, mint a "Velem a kapcsolatot." Alapértelmezés: nincs csatlakoztatva CRM-rendszerrel. CRM-beállítások: Az Azure table HTTPs' végpont, a Marketo, a Salesforce, az Azure blob, a Dynamics CRM online,  |

## <a name="properties-page"></a>Tulajdonságok lap

A Tulajdonságok lap, ahol megadhatja a kategóriák és csoportosíthatók az ajánlat a piactér, a jogi szerződések, az ajánlat és az alkalmazás verziójának támogatása az iparágban. Mindenképpen adja meg a teljes és pontos adatait ajánlata ezen a lapon, hogy megfelelően jelenik meg, majd a megfelelő készletét az ügyfelek számára. 

| **Mező neve**    | **Megjegyzések**   | 
| :---------------- | :-----------|  
| Kategória és alkategóriája | 1 és legfeljebb 3 szükséges. alapértelmezett érték: Egy elem sincs kijelölve. |
| Iparágakban és subindustries | Választható. legfeljebb 2 L1 ágazatok, valamint a maximális 2 subindustries belül minden egyes L1 iparági, alapértelmezett: Nincs kiválasztva egy sem |
| Az alkalmazás verziója  | Választható. alapértelmezett érték: Nincs. |
| Általános szerződési használata  | Választható. Alapértelmezés: nincs bejelölve.  | |
| Használati feltételek  | Szükséges, ha a standard szintű szerződés nincs bejelölve.  |

## <a name="offer-listing-page"></a>Ajánlatuk információs oldalán

A rendszer a ajánlatuk információs oldalán kell megadni a szöveg és képek, amelyeket a felhasználók a piactéren az ajánlat listaelem megtekintésekor. 

| **Mező neve**    | **Megjegyzések**   |
| :---------------- | :-----------| 
| Name (Név)  | Szükséges, legfeljebb 50 karakter hosszúságú. |
| Összegzés  | Szükséges, legfeljebb 100 karakter. | 
| Leírás  | Kötelező, maximum 3000 karakter. |
| Első lépések útmutató  | Kötelező, maximum 3000 karakter. |
| Első lépések útmutató  | Kötelező, maximum 3000 karakter. |
| Kulcsszavak keresése  | Ajánlott, legfeljebb 3 kulcsszavak nem kötelező. |
| Adatvédelmi szabályzat URL-címe  | Kötelező. |
| CSP Program Marketing anyagok URL-címe  | Választható. |
| Hasznos oldalak címének + URL-címe  | Választható. |
| Cím + fájl támogató dokumentumok  | Szükség esetén 1 perc és legfeljebb 3. A PDF formátumú kell lennie. |
| Képernyőképek  | Szükség esetén a képernyőképen 1 perc és legfeljebb 5; négy vagy több javasolt. 1280 X 720 a PNG formátumúnak kell lenniük. |
| Emblémák Store (kicsi, közepes, nagy, teljes Hero)  | Kicsi (48 X 48) és a nagy méretű (216 X 216) szükséges; További méretek nem kötelező, de javasolt: Közepes (90 x 90) Wide (255 x 115), a Hero (815 x 290). PNG formátumúnak kell lennie. |
| Videó neve + URL-cím + miniatűrje  | Nem kötelező, ajánlott, max. 4 videókat. Miniatűr 1280 x 720 PNG formátumúnak kell lennie. A videó YouTube vagy Vimeo kiszolgálón kell futnia. |
| Névjegyek (CSP Program, mérnöki támogatás)  | Mérnöki és támogatási kapcsolattartó szükséges (név, e-mail és telefonszám); Nem kötelező, de ajánlott a CSP Program forduljon. |
| Adresa URL  | Kötelező. |

## <a name="preview-page"></a>Előzetes verziójú szolgáltatásainak weblapján

Az előzetes verziójú szolgáltatásainak weblapján adhatja meg a célközönséget hozzáférése legyen az ajánlat előzetes verziójára, ellenőrizze, hogy az ajánlat az összes követelményének megfelel, mielőtt élő kerül. 

| **Mező neve**    | **Megjegyzések**   | 
| :---------------- | :-----------| 
| Aad-ben/MSA e-mail + leírása | Szükség esetén 1 perc és legfeljebb 10 megadása manuálisan, vagy pedig akár 20, ha egy CSV-fájl feltöltése. |

## <a name="technical-configuration-page"></a>Technikai konfigurációs lapja 

Technikai konfiguráció lap, amelyben meghatározhatja azokat a technikai részleteket csatlakozhat a Microsoft az ajánlat. Ezen a lapon nincs az Ön számára láthatók, ha Ön úgy döntött, hogy nem értékesítsen rajuk keresztül a Microsoft.

| **Mező neve**    | **Megjegyzések**   |  
| :---------------- | :-----------| 
| Kezdőlap URL-címe | Ha a Microsoft értékesítési szükséges. |
| Connection webhook | Ha a Microsoft értékesítési szükséges. |
| Azure AD-bérlő azonosítója | Ha a Microsoft értékesítési szükséges. |
| Azure AD app ID | Ha a Microsoft értékesítési szükséges. |

## <a name="plan-identity-modal"></a>Identitás modális megtervezése

Az első információt kell adnia egy nevet és a csomag Azonosítóját. Ezen a lapon nincs az Ön számára láthatók, ha úgy döntött, nem Microsoft keresztül.

| **Mező neve**    | **Megjegyzések**   |  
| :---------------- | :-----------| 
| Csomagazonosító  | Ha a Microsoft értékesítési szükséges. Létrehozás után nem módosítható. Legfeljebb 50 karakter hosszú lehet, és kell állnia csak kisbetűket, alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhatnak. |
| Csomag neve  | Ha a Microsoft értékesítési szükséges. Egyedinek kell lennie az ajánlat csomagokról. Max. 50 karakter lehet. |

## <a name="plan-listing-page"></a>Csomag ajánlatuk információs oldalán

A terv felsoroló lap, ahol meg kell hogy látni, amikor a csomagot a piactéren az ügyfelek a szöveget. Ezen a lapon nincs az Ön számára láthatók, ha Ön úgy döntött, hogy nem értékesítsen rajuk keresztül a Microsoft.

| **Mező neve**    | **Megjegyzések**   |  
| :---------------- | :-----------| 
| Csomag leírása   | Ha a Microsoft értékesítési szükséges. Maximális száma 500 karakter. | |

## <a name="plan-pricing--availability-page"></a>Díjszabás és a rendelkezésre állási lap megtervezése

Csomag díjszabása és a rendelkezésre állási lap, amikor Ön határozza meg az üzleti jellemzőit, célközönség és a piaci minden csomaghoz (verzió) az Ön ajánlatát. Ezen a lapon nincs az Ön számára láthatók, ha Ön úgy döntött, hogy nem értékesítsen rajuk keresztül a Microsoft.

| **Mező neve**    | **Megjegyzések**   | 
| :---------------- | :-----------| 
| Piaci rendelkezésre állása  | Szükség esetén 1 perc, valamint a maximális 141. |
| Díjszabási modell  | Kötelező. alapértelmezett érték: Alapján, átalánydíjjal. Beállítások: Alapján, átalánydíjjal, felhasználónként. |
| Minimális és maximális munkaállomások  | Nem kötelező, csak érhető el, ha a munkaállomás-alapú díjszabási modellje kiválasztva. |
| Számlázási időszak  | Kötelező. alapértelmezett érték: Havi. Beállítások: Havi, éves. |
| Ár  | Szükséges USD / hó, ha a havi számlázási időszak-e jelölve. vagy, ha a kijelölt feltétel számlázási éves évenként USD. |
| Célközönség megtervezése  | Választható. alapértelmezett érték: Nyilvános tervet. Beállítások: Nyilvános, magán a bérlői azonosító alapján |
| Csomag korlátozott közönség (bérlő azonosítója + leírása)  | Szükséges, ha személyes terv kiválasztva. 1 perc és legfeljebb 10 bérlőazonosítók Ha manuálisan. 20000 maximális száma, ha a CSV-fájl importálása. |

## <a name="test-drive-listing-page"></a>Test Drive ajánlatuk információs oldalán

Csak akkor érhető el, ha az ajánlat az ajánlat próbálja ki. A teszt a Marketplace-en meghajtó listájához használt részleteinek megadása.

| **Mező neve**    | **Megjegyzések**   | 
| :---------------- | :-----------| 
| Leírás  | Kötelező. |
| Felhasználónév manuális + fájl  | Kötelező, maximum 1 doc. PDF formátumban kell lennie. |
| Videó neve, URL-cím + miniatűrje  | Nem kötelező, ajánlott. Miniatűr 533 x 324 JPGP vagy PNG formátumúnak kell lennie. A videó YouTube vagy Vimeo kiszolgálón kell futnia. |

## <a name="review-and-publish-page"></a>Tekintse át, és tegye közzé az oldalon

| **Mező neve**    | **Megjegyzések**   | 
| :---------------- | :-----------| 
| Minősítési megjegyzései  | Választható. |

## <a name="next-steps"></a>További lépések

- [Hozzon létre egy új SaaS-ajánlat](./create-new-saas-offer.md)
