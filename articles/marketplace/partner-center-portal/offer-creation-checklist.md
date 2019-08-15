---
title: SaaS-ajánlat létrehozása ellenőrzőlista – kereskedelmi piactér az Azure-hoz
description: Az SaaS-ajánlat létrehozási folyamatában megadható részletek. -Kereskedelmi piactér az Azure-hoz
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 66c28039f9126ed9e3f56c3ac15b1b3d82279b64
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036372"
---
# <a name="saas-offer-creation-checklist"></a>SaaS-ajánlat létrehozási ellenőrzőlistája

Az SaaS-ajánlat létrehozási folyamata több oldalt is igénybe vesz. Az egyes lapokon megadható részletek az egyes elemekre mutató hivatkozásokat tartalmaznak.

Az alább megadott vagy megadható elemek szerepelnek. Bizonyos területek nem kötelezőek, vagy a megadott alapértelmezett értékekkel rendelkeznek, amelyeket igény szerint módosíthat. Ezeket a részeket az itt felsorolt sorrendben nem kell feldolgoznia.

| **Elem**    | **Cél**  |
| :---------- | :-------------------|
| [**Új ajánlat – modális**](#new-offer-modal) | Az ajánlatok azonosító adatait gyűjti.  |
| [Ajánlat beállítása lap](#offer-setup-page) | Lehetővé teszi, hogy belépjen a főbb funkciók használatára, és válassza ki, hogyan értékesítheti ajánlatát a Microsofton keresztül.  |
| [Tulajdonságok lap](#properties-page) | Adja meg az ajánlatnak a piactéren való csoportosításához használt kategóriákat és iparágakat, az ajánlatát támogató jogi szerződéseket és az alkalmazás verzióját. |
| [Ajánlati lista lapja](#offer-listing-page) | Adja meg a piactéren megjelenítendő ajánlat részleteit, beleértve az ajánlat és a marketing-eszközök leírását. |
| [Előnézet lap](#preview-page) | Az ajánlat közzétételét megelőzően korlátozott előzetes célközönséget adhat meg az ajánlat közzététele előtt a nagyobb Piactéri közönségnek. |
| [Ajánlat – technikai konfiguráció lap](#technical-configuration-page)  | Csak akkor érhető el, ha az ajánlatot a Microsofton keresztül adja el. Adja meg az ajánlathoz való kapcsolódáshoz használt technikai adatokat (URL-cím, webhook, bérlői azonosító és alkalmazás-azonosító). |
| [**Új modális csomag**](#plan-identity-modal) | Gyűjti a terv azonosító adatait.  |
| [A lista megtervezése lap](#plan-listing-page)  | Csak akkor érhető el, ha az ajánlatot a Microsofton keresztül adja el. A tervnek a piactéren való listázásához használt részletek meghatározása.  |
| [A díjszabási & rendelkezésre állási oldalának megtervezése](#plan-pricing--availability-page)  | Csak akkor érhető el, ha az ajánlatot a Microsofton keresztül adja el.  Összegyűjti az üzleti jellemzőit (díjszabási modell), a célközönséget és a piac rendelkezésre állását az ajánlat minden egyes csomagjában (verzióban).  |
| [A tesztoldal listázása lap](#test-drive-listing-page)  | Csak akkor érhető el, ha kijelöli az ajánlata tesztelésére szolgáló meghajtót. Adja meg a piactéren a tesztelési meghajtó listázásához használt adatokat.  |
| A test Drive technikai konfigurációjának lapja  | Csak akkor érhető el, ha kijelöli az ajánlata tesztelésére szolgáló meghajtót. Adja meg a bemutató (vagy a "teszt meghajtó") technikai részleteit, amelyek lehetővé teszik az ügyfelek számára, hogy a megvásárlása előtt kipróbálják az ajánlatot.  |
| [Áttekintés és közzététel oldal](#review-and-publish-page)  | Válassza ki a közzétenni kívánt módosításokat, tekintse meg az egyes lapok állapotát, és adjon meg megjegyzéseket a minősítési csapatnak.  |


## <a name="new-offer-modal"></a>Új ajánlat – modális 

Az elsőként megjelenő információ az ajánlat AZONOSÍTÓját és aliasát adja meg. 

| **Mező neve**    | **Megjegyzések**   |  
| :---------------- | :-----------| 
| Ajánlat azonosítója  | Kötelező, a létrehozás után nem módosítható. Max 50 karakter, és csak kisbetűkből, alfanumerikus karakterekből, kötőjelből vagy aláhúzásokból állhat. |
| Ajánlat aliasa  | Kötelező. |

## <a name="offer-setup-page"></a>Ajánlat beállítása lap

Az ajánlat beállítása oldalon különböző csatornákat és mozgásokat lehet használni, valamint deklarálhatja a főbb funkciók, például a tesztelési meghajtó és az ügyfél-érdeklődők használatát. 

| **Mező neve**    | **Megjegyzések**   | 
| :---------------- | :-----------|  
| Szeretné eladni a Microsoftot?  | Kötelező. Alapértelmezett: Igen |
| Hogyan kívánja használni a potenciális ügyfeleket az ajánlati listán? (Művelet meghívása)  | Kötelező, ha nem a Microsofton keresztül forgalmaz. Alapértelmezett: Ingyenes próbaverzió, beállítások: "Letöltés most", "ingyenes próbaverzió", "Kapcsolatfelvétel". |
| Próbaverzió URL-címe  | Kötelező, ha az "ingyenes próbaverzió" van kiválasztva, így az ügyfeleknek az ajánlati listához kell lépniük. |
| Ajánlat URL-címe  | Kötelező, ha a "Letöltés most" lehetőség van kiválasztva, mivel az ügyfeleknek az ajánlati listához kell lépniük |
| Csatornák  | Nem kötelező. Alapértelmezett: Nincs bekapcsolva a CSP (viszonteladói) csatornára.  |
| Kipróbálás | Nem kötelező. Alapértelmezett: Nincs engedélyezve a tesztelési meghajtó.  |
| A tesztelési meghajtó típusa | A tesztelési meghajtó engedélyezése kötelező. Alapértelmezett: Egy elem sincs kiválasztva Beállítások Azure Resource Manager, Dynamics 365 for Business Central, Dynamics 365 a Customer engagement, a Dynamics 365 for Operations, a Logic apps, a Power BI.  |
| Érdeklődők felügyelete – kapcsolódás CRM rendszerhez | Akkor szükséges, ha a Microsofton keresztül forgalmaz, vagy ha a lista a "Kapcsolatfelvétel" lehetőséget kínálja. Alapértelmezett: nincs csatlakoztatva CRM-rendszer. CRM-beállítások: Azure Table, Azure Blob, Dynamics CRM Online, HTTPs végpont, Marketo, Salesforce  |

## <a name="properties-page"></a>Tulajdonságok lap

A Tulajdonságok lapon megadhatja az ajánlatnak a piactéren való csoportosításához használt kategóriákat és iparágakat, az ajánlatot támogató jogi szerződéseket és az alkalmazás verzióját. Ügyeljen arra, hogy az Ön ajánlatának teljes és pontos részleteit ezen a lapon lássuk el, hogy az megfelelően jelenjen meg, és a megfelelő ügyfelek számára is elérhető legyen. 

| **Mező neve**    | **Megjegyzések**   | 
| :---------------- | :-----------|  
| Kategória és Alkategória | Kötelező 1 és Max 3. Alapértelmezett: Egy elem sincs kiválasztva |
| Iparágak és alágazatok | Nem kötelező. minden egyes L1 iparágban legfeljebb 2 L1 iparág és legfeljebb 2 alágazat található, alapértelmezés szerint: Nincs kiválasztott |
| Az alkalmazás verziója  | Nem kötelező. Alapértelmezett: Nincs. |
| Normál szerződés használata  | Nem kötelező. Alapértelmezett: nincs kiválasztva.  | |
| Használati feltételek  | Kötelező, ha nincs bejelölve a standard szerződés.  |

## <a name="offer-listing-page"></a>Ajánlati lista lapja

A tőzsdei oldalon megadhatja, hogy az ügyfelek milyen szövegeket és képeket láthassanak, amikor megtekintik az ajánlatát a piactéren. 

| **Mező neve**    | **Megjegyzések**   |
| :---------------- | :-----------| 
| Name (Név)  | Kötelező, Max 50 karakter. |
| Összegzés  | Kötelező, Max 100 karakter. | 
| Leírás  | Kötelező, Max 3000 karakter. |
| Első lépések utasítások  | Kötelező, Max 3000 karakter. |
| Első lépések utasítások  | Kötelező, Max 3000 karakter. |
| Kulcsszavak keresése  | Nem kötelező, ajánlott, legfeljebb 3 kulcsszó. |
| Adatvédelmi szabályzat URL-címe  | Kötelező. |
| CSP program marketing-anyagok URL-címe  | Nem kötelező. |
| Hasznos hivatkozások címe + URL-cím  | Nem kötelező. |
| Támogató dokumentumok cím + fájl  | Kötelező, minimum 1 és Max 3. PDF-fájlformátumnak kell lennie. |
| Képernyőképek  | Kötelező, min. 1 képernyőfelvétel és max. 5; négy vagy több ajánlott. PNG formátumban 1280 X 720 értéknek kell lennie. |
| Emblémák tárolása (kicsi, közepes, nagy, széles, hős)  | Kicsi (48 X 48) és nagy (216 X 216) szükséges; Egyéb méretek nem kötelező, de ajánlott: Közepes (90 x 90), Wide (255 x 115), Hero (815 x 290). PNG formátumúnak kell lennie. |
| Videók neve + URL + miniatűr  | Opcionális, ajánlott, legfeljebb 4 videó. A miniatűr 1280 x 720 PNG formátumúnak kell lennie. A videót a YouTube vagy a Vimeo szolgáltatásban kell tárolni. |
| Névjegyek (CSP program, mérnöki, támogatás)  | Mérnöki és támogatási Kapcsolatfelvétel szükséges (név, e-mail és telefonszám); A CSP program elérhetősége nem kötelező, de ajánlott. |
| Támogatási URL-cím  | Kötelező. |

## <a name="preview-page"></a>Előnézet lap

Az Előnézet lapon megadhatja, hogy az ajánlat előzetes verziójához milyen célközönség férhet hozzá, így ellenőrizheti, hogy az ajánlat megfelel-e az összes követelménynek az azonnali működéshez. 

| **Mező neve**    | **Megjegyzések**   | 
| :---------------- | :-----------| 
| HRE/MSA e-mail és leírás | Kötelező, minimum 1 és legfeljebb 10, ha manuálisan van megadva, vagy legfeljebb 20, ha CSV-fájlt tölt fel. |

## <a name="technical-configuration-page"></a>Technikai konfiguráció lap 

A technikai konfiguráció lapon megadhatja a Microsoft által az ajánlathoz való kapcsolódáshoz használt technikai részleteket. Ez az oldal nem látható, ha úgy döntött, hogy nem értékesíti a Microsoftot.

| **Mező neve**    | **Megjegyzések**   |  
| :---------------- | :-----------| 
| Kezdőlap URL-címe | A Microsofton keresztüli értékesítés esetén kötelező. |
| Kapcsolat webhook | A Microsofton keresztüli értékesítés esetén kötelező. |
| Azure AD-bérlő azonosítója | A Microsofton keresztüli értékesítés esetén kötelező. |
| Azure AD-alkalmazás azonosítója | A Microsofton keresztüli értékesítés esetén kötelező. |

## <a name="plan-identity-modal"></a>Identity modális csomag

A megadható első információ a csomag neve és azonosítója. Ez a lap nem látható, ha úgy döntött, hogy nem értékesíti a Microsoftot.

| **Mező neve**    | **Megjegyzések**   |  
| :---------------- | :-----------| 
| Csomag azonosítója  | A Microsofton keresztüli értékesítés esetén kötelező. A létrehozás után nem módosítható. Max 50 karakter, és csak kisbetűkből, alfanumerikus karakterekből, kötőjelből vagy aláhúzásokból állhat. |
| Csomag neve  | A Microsofton keresztüli értékesítés esetén kötelező. Egyedinek kell lennie az ajánlat összes csomagján. Legfeljebb 50 karakter. |

## <a name="plan-listing-page"></a>A lista megtervezése lap

A csomag listázása lapon megadhatja, hogy a rendszer mikor tekintse meg az ügyfelek számára a terv megtekintését a piactéren. Ez az oldal nem látható, ha úgy döntött, hogy nem értékesíti a Microsoftot.

| **Mező neve**    | **Megjegyzések**   |  
| :---------------- | :-----------| 
| Csomag leírása   | A Microsofton keresztüli értékesítés esetén kötelező. Legfeljebb 500 karakter. | |

## <a name="plan-pricing--availability-page"></a>A díjszabási & rendelkezésre állási oldalának megtervezése

A csomag díjszabása és rendelkezésre állása lapon meghatározhatja az ajánlat minden egyes csomagjának (verziójának) üzleti jellemzőit, a célközönséget és a piacra való rendelkezésre állást. Ez az oldal nem látható, ha úgy döntött, hogy nem értékesíti a Microsoftot.

| **Mező neve**    | **Megjegyzések**   | 
| :---------------- | :-----------| 
| Piac rendelkezésre állása  | Kötelező, minimum 1 és Max 141. |
| Díjszabási modell  | Kötelező. Alapértelmezett: Átalány. Beállítások Átalány, felhasználónként. |
| Minimális és maximális számú ülőhely  | Nem kötelező, csak akkor érhető el, ha ki van választva az ülőhely-alapú díjszabási modell. |
| Számlázási időszak  | Kötelező. Alapértelmezett: Havi. Beállítások Havonta, évente. |
| Ár  | Havonta szükséges USD, ha a havi számlázási időszak van kiválasztva; vagy USD/év, ha az éves számlázási időszak van kiválasztva. |
| Célközönség megtervezése  | Nem kötelező. Alapértelmezett: Nyilvános csomag. Beállítások Nyilvános, privát bérlő azonosítója |
| Korlátozott csomag célközönsége (bérlő azonosítója + Leírás)  | Kötelező, ha a saját csomag van kiválasztva. Ha kézzel van megadva, min. 1 és legfeljebb 10 bérlői azonosítót kell megadni. Maximális 20000, ha a CSV-fájl importálása megtörténjen. |

## <a name="test-drive-listing-page"></a>A tesztoldal listázása lap

Csak akkor érhető el, ha kijelöli az ajánlata tesztelésére szolgáló meghajtót. Adja meg a piactéren a tesztelési meghajtó listázásához használt adatokat.

| **Mező neve**    | **Megjegyzések**   | 
| :---------------- | :-----------| 
| Leírás  | Kötelező. |
| Felhasználói manuális név + fájl  | Kötelező, legfeljebb 1 doc. PDF-formátumúnak kell lennie. |
| Videó neve, URL + miniatűr  | Nem kötelező, ajánlott. A miniatűrnek 533 x 324 JPGP vagy PNG formátumúnak kell lennie. A videót a YouTube vagy a Vimeo szolgáltatásban kell tárolni. |

## <a name="review-and-publish-page"></a>Áttekintés és közzététel oldal

| **Mező neve**    | **Megjegyzések**   | 
| :---------------- | :-----------| 
| Megjegyzések a minősítéshez  | Nem kötelező. |

## <a name="next-steps"></a>További lépések

- [Új SaaS-ajánlat létrehozása](./create-new-saas-offer.md)
