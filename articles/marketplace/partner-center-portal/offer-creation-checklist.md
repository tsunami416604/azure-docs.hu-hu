---
title: SaaS-ajánlatlétrehozási ellenőrzőlista – Kereskedelmi piactér az Azure-hoz
description: A SaaS-ajánlat létrehozási folyamatában megadható részletek. - Kereskedelmi piactér az Azure-hoz
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 60111b6a23c76314383c5f95be3eb4b38f90d3e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281340"
---
# <a name="saas-offer-creation-checklist"></a>SaaS-ajánlatlétrehozási ellenőrzőlista

A SaaS ajánlat készítési folyamat végigvezeti több oldalt. Itt vannak a részleteket meg tudja adni az egyes oldalakon, a linkeket, hogy többet tudjon meg az egyes elemeket.

Az alábbiakban felsoroljuk azokat az elemeket, amelyeket meg kell adnia vagy megadnia kell. Egyes területek nem kötelezőek, vagy alapértelmezett értékekkel rendelkeznek, amelyeket igény szerint módosíthat. Nem kell dolgozni a szakaszok a sorrendben itt felsorolt.

| **Elem**    | **Cél**  |
| :---------- | :-------------------|
| [**Új ajánlat Modal**](#new-offer-modal) | Identitásadatokat gyűjt.  |
| [Ajánlat beállítási lapja](#offer-setup-page) | Lehetővé teszi, hogy engedélyezd a legfontosabb funkciók használatát, és eldöntse, hogyan értékesítse ajánlatát a Microsofton keresztül.  |
| [Tulajdonságok lap](#properties-page) | Határozza meg az ajánlat csoportosításához használt kategóriákat és iparágakat a piactereken, az ajánlatot támogató jogi szerződéseket és az alkalmazás verzióját. |
| [Ajánlatlistázási oldal](#offer-listing-page) | Határozza meg a piactéren megjelenítendő ajánlat részleteit, beleértve az ajánlat leírását és a marketingeszközöket. |
| [Oldal előnézete](#preview-page) | Korlátozott előnézeti közönséget határozhat meg az ajánlat közzétételéhez, mielőtt élőben közzétenné az ajánlatot a szélesebb piactéri közönség(ek)nek. |
| [Ajánlat műszaki konfigurációs oldal](#technical-configuration-page)  | Csak akkor érhető el, ha úgy választja, hogy eladja az ajánlatot a Microsofton keresztül. Adja meg az ajánlathoz való csatlakozáshoz használt technikai részleteket (URL-elérési út, webhook, bérlői azonosító és alkalmazásazonosító). |
| [**Új terv Moddal**](#plan-identity-modal) | A terv identitásadatainak gyűjtése.  |
| [A csomag hirdetéslistázási lapja](#plan-listing-page)  | Csak akkor érhető el, ha úgy választja, hogy eladja az ajánlatot a Microsofton keresztül. Adja meg a terv a piactéren való listázásához használt részleteket.  |
| [Díjszabás & rendelkezésre állási oldal ának megtervezése](#plan-pricing--availability-page)  | Csak akkor érhető el, ha úgy választja, hogy eladja az ajánlatot a Microsofton keresztül.  Összegyűjti az ajánlat egyes csomagjainak (verzióinak) üzleti jellemzőit (árképzési modelljét), közönségét és piaci elérhetőségét.  |
| [A tesztvezetés listázási lapja](#test-drive-listing-page)  | Csak akkor érhető el, ha úgy választja, hogy felajánl egy tesztvezetést az ajánlatához. Adja meg a tesztmeghajtó marketplace-en való listázásához használt részleteket.  |
| Test Drive műszaki konfigurációs lap  | Csak akkor érhető el, ha úgy választja, hogy felajánl egy tesztvezetést az ajánlatához. Adja meg a bemutató (vagy "tesztvezetés") technikai adatait, amelyek lehetővé teszik az ügyfelek számára, hogy kipróbálják az ajánlatot, mielőtt elkötelezné magukat a vásárlás mellett.  |
| [Lap áttekintése és közzététele](#review-and-publish-page)  | Jelölje ki a közzétenni kívánt módosításokat, tekintse meg az egyes lapok állapotát, és adjon meg megjegyzéseket a minősítő csoportnak.  |


## <a name="new-offer-modal"></a>Új ajánlat modális 

Az első információ, amelyet meg kell adnia, az ajánlatának azonosítója és aliasa. 

| **Mezőnév**    | **Megjegyzések**   |  
| :---------------- | :-----------| 
| Ajánlat azonosítója  | Kötelező, nem módosítható a létrehozás után. Legfeljebb 50 karakterből állhat, és csak kisbetűs, alfanumerikus karakterekből, kötőjelekből vagy aláhúzásjelekből állhat. |
| Ajánlat aliasa  | Kötelező. |

## <a name="offer-setup-page"></a>Ajánlat beállítási lapja

Az ajánlat beállítási oldal, ahol választhat a különböző csatornák és értékesítési mozgások, valamint deklarálni a használatát kulcsfontosságú funkciók, mint például a tesztvezetés és az ügyfél vezet. 

| **Mezőnév**    | **Megjegyzések**   | 
| :---------------- | :-----------|  
| Szeretne eladni a Microsofton keresztül?  | Kötelező. Alapértelmezett: Igen |
| Hogyan szeretné, hogy a potenciális ügyfelek kapcsolatba lépjenek az ajánlathirdetéssel? (Cselekvésre való felhívás)  | Kötelező, ha nem értékesíti a Microsofton keresztül. Alapértelmezett: Ingyenes próbaverzió, Beállítások: "Szerezd meg most", "Ingyenes próbaverzió", "Kapcsolatfelvétel" |
| Próba URL-címe  | Kötelező, ha az "Ingyenes próbaverzió" van kiválasztva, mivel az ügyfeleknek hogyan kell együttműködniük az ajánlatlistával. |
| Ajánlat URL-címe  | Kötelező, ha a "Get it Now" van kiválasztva, mivel az ügyfeleknek hogyan kell együttműködniük az ajánlatlistával |
| Csatornák  | Választható. Alapértelmezett: Nem iratkozott fel a kripta-szolgáltató (viszonteladói) csatornára.  |
| Tesztverzió | Választható. Alapértelmezett: Nincs engedélyezve a tesztvezetés.  |
| A tesztvezetés típusa | Tesztvezetés engedélyezése esetén kötelező. Alapértelmezett: Nincs kijelölve. Lehetőségek: Azure Resource Manager, Dynamics 365 for Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 for Operations, Logic app, Power BI.  |
| Érdeklődőkezelés – csatlakozás CRM-rendszerhez | Kötelező, ha a Microsofton keresztül értékesít, vagy ha a listaelem "Kapcsolatfelvétel" néven kínál. Alapértelmezett: nincs CRM rendszer csatlakoztatva. CRM-beállítások: Azure-tábla, Azure blob, Dynamics CRM online, HTTPs végpont, Marketo, Salesforce  |

## <a name="properties-page"></a>Tulajdonságok lap

A tulajdonságok lap, ahol meghatározza a kategóriák és iparágak a piactereken az ajánlat csoportosításához használt, az ajánlatot támogató jogi szerződések és az alkalmazás verziója. Ügyeljen arra, hogy teljes és pontos részleteket adjon meg az ajánlatáról ezen az oldalon, hogy az megfelelően jelenjen meg, és a megfelelő ügyfélkészletnek ajánlja fel. 

| **Mezőnév**    | **Megjegyzések**   | 
| :---------------- | :-----------|  
| Kategória és alkategória | Szükséges 1 és max 3. Alapértelmezett: Nincs kijelölve. |
| Iparágak és aliparágak | Választható. max 2 L1 Iparágak és max 2 subindustries belül minden L1 ipar, Alapértelmezett: Nincs kiválasztva |
| Az alkalmazás verziója  | Választható. Alapértelmezett: Nincs. |
| Általános szerződés használata  | Választható. Alapértelmezett: nincs kijelölve.  | |
| Használati feltételek  | Kötelező, ha a Szabványos szerződés nincs kiválasztva.  |

## <a name="offer-listing-page"></a>Ajánlatlista oldala

A listalap az a hely, ahol megkell adnia azokat a szöveget és képeket, amelyeket az ügyfelek az ajánlat hirdetésének a piactéren való megtekintésekor látnak. 

| **Mezőnév**    | **Megjegyzések**   |
| :---------------- | :-----------| 
| Név  | Kötelező, max 50 karakter. |
| Összefoglalás  | Kötelező, max 100 karakter. | 
| Leírás  | Kötelező, max 3000 karakter. |
| Útmutató az első lépésekhez  | Kötelező, max 3000 karakter. |
| Útmutató az első lépésekhez  | Kötelező, max 3000 karakter. |
| Kulcsszavak keresése  | Nem kötelező, ajánlott, max 3 kulcsszavakat. |
| Adatvédelmi irányelvek URL-címe  | Kötelező. |
| CSP Program marketing anyagok URL-címe  | Választható. |
| Hasznos linkek Cím + URL  | Választható. |
| Támogató dokumentumok címe + fájl  | Kötelező, min 1 és max 3. PDF fájlformátumnak kell lennie. |
| Képernyőképek  | Kötelező, min 1 screenshot és max 5; négy vagy több ajánlott. Png formátumban 1280 X 720 lehet. |
| Store logók (kicsi, közepes, nagy, széles, hős)  | Kicsi (48 X 48) és nagy (216 X 216) szükséges; egyéb méretek opcionális, de ajánlott: Közepes (90 x 90), Széles (255 x 115), Hero (815 x 290). PNG formátumúnak kell lennie. |
| Videók neve + URL + miniatűr  | Opcionális, ajánlott, max 4 videó. A miniatűrnek PNG formátumban 1280 x 720-nak kell lennie. A videót a YouTube-on vagy a Vimeo-ban kell üzemeltetni. |
| Kapcsolatok (CSP Program, Mérnöki, Támogatás)  | Műszaki és támogatási kapcsolattartó szükséges (név, e-mail cím és telefonszám); CSP Program kapcsolat nem kötelező, de ajánlott. |
| Támogatási URL-cím  | Kötelező. |

## <a name="preview-page"></a>Oldal megtekintése

Az előnézeti oldalon adhatja meg, hogy a közönség hozzáférhessen-e az ajánlat előnézetéhez, és ellenőrizze, hogy az ajánlat megfelel-e az összes követelménynek, mielőtt az élesben megmaradna. 

| **Mezőnév**    | **Megjegyzések**   | 
| :---------------- | :-----------| 
| AAD/MSA e-mail + leírás | Kötelező, min 1 és max 10, ha manuálisan adja meg, vagy akár 20, ha feltöltegy CSV fájlt. |

## <a name="technical-configuration-page"></a>Műszaki konfigurációs lap 

A műszaki konfigurációs lap az a hely, ahol megadhatja a Microsoft által az ajánlathoz való csatlakozáshoz használt technikai részleteket. Ez az oldal nem látható az Ön számára, ha úgy döntött, hogy nem adja el a Microsofton keresztül.

| **Mezőnév**    | **Megjegyzések**   |  
| :---------------- | :-----------| 
| Céloldal URL-címe | Szükséges, ha a Microsofton keresztül értékesít. |
| Kapcsolat webhook | Szükséges, ha a Microsofton keresztül értékesít. |
| Az Azure AD-bérlő azonosítója | Szükséges, ha a Microsofton keresztül értékesít. |
| Az Azure AD alkalmazás azonosítója | Szükséges, ha a Microsofton keresztül értékesít. |

## <a name="plan-identity-modal"></a>Identitás terv

A rendszer elsőként a terv nevét és azonosítóját adja meg. Ez az oldal nem látható az Ön számára, ha úgy döntött, hogy nem értékesít a Microsofton keresztül.

| **Mezőnév**    | **Megjegyzések**   |  
| :---------------- | :-----------| 
| Terv azonosítója  | Szükséges, ha a Microsofton keresztül értékesít. Nem lehet megváltoztatni a teremtés után. Legfeljebb 50 karakterből állhat, és csak kisbetűs, alfanumerikus karakterekből, kötőjelekből vagy aláhúzásjelekből állhat. |
| Plan Name  | Szükséges, ha a Microsofton keresztül értékesít. Egyedinek kell lennie az ajánlat összes tervében. Legfeljebb 50 karakter. |

## <a name="plan-listing-page"></a>A listaelem megtervezése lap

A csomaglista lap az a hely, ahol megkell adnia a szöveget, amelyet az ügyfelek a csomag marketplace-en való megtekintésekor tekinthetnek meg. Ez az oldal nem látható az Ön számára, ha úgy döntött, hogy nem adja el a Microsofton keresztül.

| **Mezőnév**    | **Megjegyzések**   |  
| :---------------- | :-----------| 
| Terv leírása   | Szükséges, ha a Microsofton keresztül értékesít. Legfeljebb 500 karakter. | |

## <a name="plan-pricing--availability-page"></a>Díjszabás megtervezése & elérhetőségi oldal

A csomag díjszabása és rendelkezésre állási oldala az a hely, ahol meghatározhatja az ajánlat egyes csomagainak (verzióinak) üzleti jellemzőit, közönségét és piaci elérhetőségét. Ez az oldal nem látható az Ön számára, ha úgy döntött, hogy nem adja el a Microsofton keresztül.

| **Mezőnév**    | **Megjegyzések**   | 
| :---------------- | :-----------| 
| A piac elérhetősége  | Kötelező, min 1 és max 141. |
| Árképzési modell  | Kötelező. Alapértelmezett: Átalánydíjas. Beállítások: Átalánydíjas, felhasználónként. |
| Minimális és maximális ülések  | Opcionális, csak akkor érhető el, ha az ülésalapú árképzési modell t választotta. |
| Számlázási időszak  | Kötelező. Alapértelmezett: Havi. Opciók: Havi, Éves. |
| Price  | Havonta kötelező USD, ha a havi számlázási időszak van kiválasztva; vagy USD évente, ha az éves számlázási időszak van kiválasztva. |
| Célközönség megtervezése  | Választható. Alapértelmezett: Nyilvános terv. Beállítások: Nyilvános, Privát bérlőazonosító szerint |
| Korlátozott csomagcélközönség (bérlőazonosító + leírás)  | Kötelező, ha a privát csomag van kiválasztva. Min. 1 és max 10 bérlői azonosító, ha manuálisan adja meg. Max 20000 CSV-fájl importálása esetén. |

## <a name="test-drive-listing-page"></a>A Test Drive listázási lapja

Csak akkor érhető el, ha úgy választja, hogy felajánl egy tesztvezetést az ajánlatához. Adja meg a tesztmeghajtó marketplace-en való listázásához használt részleteket.

| **Mezőnév**    | **Megjegyzések**   | 
| :---------------- | :-----------| 
| Leírás  | Kötelező. |
| Felhasználói kézikönyv neve + fájl  | Kötelező, max 1 doc. |
| Videó neve, URL + miniatűr  | Nem kötelező, ajánlott. A miniatűrnek JPGP vagy PNG formátumban 533 x 324-nek kell lennie. A videót a YouTube-on vagy a Vimeo-ban kell üzemeltetni. |

## <a name="review-and-publish-page"></a>Lap áttekintése és közzététele

| **Mezőnév**    | **Megjegyzések**   | 
| :---------------- | :-----------| 
| A minősítéssel kapcsolatos megjegyzések  | Választható. |

## <a name="next-steps"></a>További lépések

- [Új SaaS-ajánlat létrehozása](./create-new-saas-offer.md)
