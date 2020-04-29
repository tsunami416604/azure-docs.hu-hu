---
title: Az alkalmazás beállítása a közzétételi portálon
description: Útmutatás az alkalmazás beállításához a Cloud Publishing Portalon.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2676dbf72309eeb51be1f08e7bae2c1502cc671e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280201"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Az alkalmazás beállítása a közzétételi portálon

Most már készen áll az alkalmazás beállítására a közzétételi portálon.

## <a name="login-and-create-a-new-offer"></a>Jelentkezzen be, és hozzon létre egy új ajánlatot

1. Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).
2. A bal oldali navigációs sávon kattintson az "+ új ajánlat" lehetőségre, és válassza a "Dynamics 365 for Customer engagement" lehetőséget.

![Új ajánlat kiválasztása](./media/CRMScreenShot14.png)

1. Most már megnyílik egy új "szerkesztő"-ajánlat, amely készen áll a szerzői műveletek elindítására.

![Új ajánlat képernyő](./media/CRMScreenShot15.png)

1. A kitöltendő "űrlapok" a "szerkesztő" nézetben láthatók a bal oldalon. Mindegyik "Form" a kitöltendő mezők halmazát tartalmazza. A kötelező mezők piros csillaggal (\*) vannak megjelölve.

Négy fő űrlap létezik a Dynamics 365 for Customer engagement-ajánlat létrehozásához

* Ajánlat beállításai
* Technikai információ
* Kirakat részletei
* Kapcsolatok

## <a name="fill-out-the-offer-settings-form"></a>Adja meg az ajánlati beállítások űrlapot

Az ajánlat beállításai űrlap egy alapszintű űrlap az ajánlat beállításainak megadásához. A különböző mezők az alábbiakban olvashatók.

### <a name="offer-id"></a>Ajánlat azonosítója

Ez az ajánlat egyedi azonosítója a közzétevői profilon belül. Ez az azonosító a termék URL-címeiben jelenik meg. Csak kisbetűs alfanumerikus karakterekből és kötőjelekből (-) állhat. Az azonosító nem végződhet kötőjelre, és legfeljebb 50 karakterből állhat. Ez a mező csak akkor érhető el, ha egy ajánlat élő állapotba kerül.

Ha például a " **contoso"** közzétevő közzétevője egy **"Sample-WebApp"** azonosítójú ajánlatot hoz létre, akkor az a "https:/appsource.microsoft.com/Marketplace/apps/contoso.Sample-WebApp?Tab=Overview" AppSource fog megjelenni.\/

### <a name="publisher-id"></a>Közzétevő azonosítója

Ez a legördülő lista lehetővé teszi, hogy kiválassza azt a közzétevői profilt, amelyen közzé kívánja tenni az ajánlatot. Ez a mező csak akkor érhető el, ha egy ajánlat élő állapotba kerül.

### <a name="name"></a>Name (Név)

Ez az ajánlat megjelenítendő neve. Ez a név jelenik meg a [AppSource](https://appsource.microsoft.com/). Legfeljebb 50 karakterből állhat.

A folyamat mentéséhez kattintson a Save (Mentés) gombra. A következő lépés az ajánlat technikai adatainak hozzáadása.

## <a name="fill-out-the-technical-info-form"></a>Adja meg a technikai adatok űrlapot


A technikai információ űrlapja a Dynamics 365-hez tartozó ügyfél-engagement megoldásra vonatkozó adatokat adja meg. Ha a fölé viszi, további információkat talál. Lásd az alábbi példát.

![Technikai információk képernyő](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Alkalmazás adatai

A legtöbb közzétevő elhagyja ezeket a mezőket az alapértelmezett értékekkel, a felhasználóval, a nem, a nem és egy üres alkalmazás-konfigurációs URL-címmel, a fenti képernyőképen.

### <a name="crm-package"></a>CRM-csomag

![CRM-csomag adatai](./media/CRMScreenShot17.png)

Itt látható a következő mezők magyarázata:

* A csomag fájlneve: a CRM AppSource-csomagot tartalmazó Zip-fájl létrehozásakor a fenti lépésben létrehozott fájlnév. A fenti példában ez a "Microsoft\_SamplePackage. zip".
* A csomag helyének URL-címe: ez az az Azure Storage-fiók URL-címe, amely a fent megadott csomagfájl nevét tartalmazza. Ez a fenti szakasz 9. lépésében létrehozott URL-cím.
* A csomagfájl több CRM-csomagot is használhat: válassza az Igen lehetőséget, **Ha a** CRM több verzióját is támogatja különböző csomagokkal. A legtöbb partner esetében ez a következő lesz: "nem". Ha az Igen lehetőséget választja, a megoldás egyes verzióihoz létre kell hoznia AppSource-csomagokat. _Megjegyzés: ez nem kérdezi le, hogy van-e több **zip** -fájlja. Ha több Solution. zip fájllal rendelkezik, de csak egy verziót használ, a "nem" lehetőséget kell választania. A csomagoló eszköz automatikusan együtt fog működni._

### <a name="crm-package-availability"></a>CRM-csomag elérhetősége

Ebben a szakaszban kiválaszthatja, hogy a CRM mely régióit fogja elérhetővé tenni a csomag számára. Arról, hogy mely CRM-régiók mely országokat/régiókat szolgálják ki, tekintse meg a következő hivatkozást:[https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Megjegyzés: a németországi "szuverén és US Gov felhő" szuverén telepítése a minősítés során speciális engedélyeket és ÉRVÉNYESÍTÉSt igényel

## <a name="storefront-details"></a>Kirakat részletei

### <a name="offer-summary"></a>Ajánlat összegzése

Ez az ajánlat értékének összefoglalása. Ekkor megjelenik az ajánlat keresési oldalán. Legfeljebb 100 karakter hosszú lehet.

### <a name="offer-description"></a>Ajánlat leírása

Ezt a leírást fogja megjelenni az alkalmazás részletei oldalon. A maximális engedélyezett érték 1300 karakter

### <a name="industries"></a>Ágazatok

Válassza ki azt az iparágat, amelyhez az alkalmazás a legjobban igazodik. Ha az alkalmazás több iparágra is vonatkozik, ezt a mezőt üresen hagyhatja.

### <a name="categories"></a>Kategóriák

Válassza ki az alkalmazáshoz kapcsolódó kategóriákat. Legfeljebb 3 értéket válasszon ki.

### <a name="app-type"></a>Alkalmazástípus

Válassza ki azt a próbaverziót, amelyet az alkalmazás engedélyezni fog a AppSource. Az "ingyenes" kifejezés azt jelenti, hogy az alkalmazás ingyenes. A "próbaverzió" kifejezés azt jelenti, hogy az ügyfelek rövid időn belül kipróbálhatják az alkalmazást a AppSource. A "próbaverzióra vonatkozó kérelem" nem támogatott a Dynamics 365 for Customer engagement-alkalmazások esetében. Ne jelölje be ezt a beállítást.

### <a name="help-link-for-your-app"></a>Súgó hivatkozás az alkalmazáshoz

Adja meg az alkalmazáshoz kapcsolódó információkat tartalmazó oldal URL-címét.

### <a name="supported-countriesregions"></a>Támogatott országok/régiók

Ez a mező határozza meg azokat az országokat/régiókat, amelyekben az ajánlat próbaverzióhoz elérhető lesz.

### <a name="supported-languages"></a>Támogatott nyelvek

Válassza ki az alkalmazás által támogatott nyelveket. Ha az alkalmazása olyan további nyelveket is támogat, amelyek nem szerepelnek ezen a listán, folytassa az ajánlat közzétételével [appsource@microsoft.com](mailto:appsource@microsoft.com) , és küldje el nekünk a következő címen:, hogy tudassa velünk.

### <a name="app-version"></a>Az alkalmazás verziója

Adja meg az alkalmazás verziószámát

### <a name="app-release-date"></a>Alkalmazás kiadásának dátuma

Adja meg az alkalmazás kiadási dátumát

### <a name="products-your-app-works-with-max-3"></a>Az alkalmazás által használható termékek (max. 3)

Az alkalmazás által használható listára vonatkozó termékek. Legfeljebb három terméket listázhat fel. Egy termék listázásához kattintson a pluszjelre (az új mellett), és egy új nyílt szövegmezőt fog létrehozni az alkalmazás által használt termék nevének megadásához.

### <a name="search-keywords-max-3"></a>Keresési kulcsszavak (max. 3)

A AppSource lehetővé teszi, hogy az ügyfél kulcsszavak alapján végezzen keresést. Megadhatja azokat a kulcsszavakat, amelyekhez az alkalmazás megjelenik az ügyfelek számára.

Például ha az alkalmazás "e-mail-szolgáltatás" e-mail-címe, e-mail-cím, levelezési szolgáltatás lehet néhány kulcsszó. Válassza ki azokat a szavakat, amelyeket a felhasználók valószínűleg az alkalmazás keresésére fognak használni a AppSource keresési mezőjében.

### <a name="hide-key"></a>Kulcs elrejtése

Ez egy olyan kulcs, amely az ajánlat előzetes URL-címével együtt elrejti a nyilvános nézetből. Nem jelszó. Itt megadhat bármilyen karakterláncot.

### <a name="offer-logo-png-format-48x48"></a>Ajánlat emblémája (PNG-formátum, 48x48)

Ekkor megjelenik az alkalmazás keresési oldalán. **Csak a PNG formátum engedélyezett.** PNG-kép feltöltése a 48PX\*48PX felbontásával

### <a name="offer-logo-png-format-216x216"></a>Ajánlat emblémája (PNG-formátum, 216x216)

Ez az alkalmazás részleteit tartalmazó oldalon fog megjelenni. **Csak a PNG formátum engedélyezett.** PNG-kép feltöltése a 216PX\*216PX felbontásával

### <a name="videos"></a>Videók

Legfeljebb négy videó tölthető fel. Minden feltölteni kívánt videóhoz ki kell töltenie a videó nevét, URL-címét (csak YouTube vagy Vimeo) és a miniatűrt a videóhoz való hozzárendeléshez. A miniatűrnek PNG formátumban kell lennie, és\*1280PX 720PX kell lennie. Új videó (k) hozzáadásához kattintson a plusz jelre. A videók miniatűrje az alkalmazás részleteit tartalmazó oldalon fog megjelenni.

### <a name="documents"></a>Dokumentumok

PDF-formátumban legfeljebb három dokumentum tölthető fel. Minden feltölteni kívánt dokumentumhoz ki kell töltenie a dokumentum nevét, és fel kell töltenie a dokumentumot. A dokumentumnak PDF formátumúnak kell lennie.

Új dokumentum (ok) hozzáadásához kattintson a plusz jelre.

### <a name="screenshots"></a>Képernyőképek

Ezek a képernyőképek az alkalmazás AppSource részletek lapján jelennek meg.

### <a name="privacy-policy"></a>Adatvédelmi irányelvek

Adja meg az alkalmazás adatvédelmi szabályzatának URL-címét

### <a name="terms-of-use"></a>Használati feltételek

Adja meg az alkalmazás használati feltételeit. Az AppSource ügyfeleknek el kell fogadniuk ezeket a feltételeket az alkalmazás kipróbálása előtt

### <a name="support-url"></a>Támogatási URL-cím

Adja meg az alkalmazás támogatási URL-címét.

### <a name="lead-destination"></a>Érdeklődő célhelye

Válassza ki azt a CRM-rendszert, amelybe a kívánt érdeklődőt tárolni fogja. Válassza az "Azure Table" lehetőséget, ha a következő CRM-rendszerek valamelyikét tartalmazza: Salesforce, Marketo, Microsoft Dynamics CRM. Az itt kiválasztott CRM-rendszer azon végfelhasználók adatait írja le, akik a AppSource (leads) alkalmazásban próbálják meg az alkalmazást. A kiválasztott CRM-rendszertől függően kattintson az alábbi megfelelő URL-címre a következő mezők kitöltéséhez:

* [Azure-tábla](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Kirakat részletei

A kapcsolattartási adatok a partner és a Microsoft közötti belső kommunikációhoz használatosak. Megjegyzés: fontos, hogy az ezekben a mezőkben figyelt e-mail-címet használja. Ezt az e-mailt fogjuk használni a AppSource való közzététel előrehaladásával kapcsolatos kommunikációhoz. Csak a támogatási URL-cím jelenik meg az ügyfelek számára.
