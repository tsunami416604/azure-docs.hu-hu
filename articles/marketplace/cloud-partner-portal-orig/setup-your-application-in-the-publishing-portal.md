---
title: Az alkalmazás beállítása a közzétételi portálon
description: Az alkalmazás felhőalapú közzétételi portálon történő beállításának útmutatója.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2676dbf72309eeb51be1f08e7bae2c1502cc671e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280201"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Az alkalmazás beállítása a közzétételi portálon

Most már készen áll az alkalmazás beállítására a közzétételi portálon.

## <a name="login-and-create-a-new-offer"></a>Jelentkezzen be és hozzon létre egy új ajánlatot

1. Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)
2. A bal oldali navigációs sávon kattintson a "+ Új ajánlat" gombra, és válassza a "Dynamics 365 for Customer Engagement" lehetőséget.

![Új ajánlat kiválasztása](./media/CRMScreenShot14.png)

1. Egy új ajánlat "Szerkesztő" nézet most megnyílt az Ön számára, és készen állunk a szerzői.

![Új ajánlat képernyő](./media/CRMScreenShot15.png)

1. A kitöltendő "űrlapok" a "Szerkesztő" nézetben láthatók a bal oldalon. Minden "űrlap" egy kitöltendő mezőkészletből áll. A kötelező mezőket piros csillag\*( jelöli.

A Dynamics 365 for Customer Engagement ajánlat hozása négy fő formája

* Ajánlat beállításai
* Technikai információk
* Kirakat részletei
* Kapcsolatok

## <a name="fill-out-the-offer-settings-form"></a>Töltse ki az Ajánlatbeállítások űrlapot

Az ajánlatbeállítások képernyő egy alapvető űrlap az ajánlatbeállításainak megadásához. A különböző mezőket az alábbiakban ismertetjük.

### <a name="offer-id"></a>Ajánlat azonosítója

Ez a közzétevői profilon belüli ajánlat egyedi azonosítója. Ez az azonosító látható lesz a termék URL-címeiben. Csak kisbetűs alfanumerikus karakterekből és kötőjelekből (-) állhat. Az azonosító nem végződhet kötőjellel, és legfeljebb 50 karakterből állhat. Ez a mező zárolva van, amint egy ajánlat élesben jelenik meg.

ha például egy **közzétevő "contoso"** közzétevői **"sample-WebApp"** ajánlatazonosítóval hoznak létre ajánlatot, akkor az AppSource-ban "https:\//appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview" néven jelenik meg.

### <a name="publisher-id"></a>Közzétevő azonosítója

Ez a legördülő menü lehetővé teszi, hogy kiválassza azt a közzétevői profilt, amely alatt közzé szeretné tenni ezt az ajánlatot. Ez a mező zárolva van, amint egy ajánlat élesben jelenik meg.

### <a name="name"></a>Név

Ez az ajánlat megjelenítendő neve. Ez az a név, amely megjelenik az [AppSource alkalmazásban.](https://appsource.microsoft.com/) Legfeljebb 50 karakterből állhat.

Kattintson a "Mentés", hogy mentse a haladást. A következő lépés az lenne, hogy adjunk műszaki információk az ajánlathoz.

## <a name="fill-out-the-technical-info-form"></a>Töltse ki a Műszaki adatok űrlapot


A technikai információs űrlapon töltheti ki a Dynamics 365 for Customer Engagement megoldásra vonatkozó információkat. Az egérmutatót a jelen meg több információt. Lásd az alábbi példát.

![Műszaki információs képernyő](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Alkalmazás adatai

A legtöbb közzétevő ezeket a mezőket az alapértelmezett értékekkel, felhasználó, nem, nem és egy üres alkalmazáskonfigurációs URL-címmel hagyja a fenti képernyőképen.

### <a name="crm-package"></a>CRM-csomag

![CRM-csomag adatai](./media/CRMScreenShot17.png)

Íme egy magyarázat az alábbi mezőkre:

* A csomag fájlneve: A fenti lépésben létrehozott fájlnév a CRM AppSource csomagzip fájl létrehozásakor. A fenti példában ez\_a "Microsoft SamplePackage.zip".
* A csomag helyének URL-címe: Ez az Azure Storage-fiók URL-címe, amely a csomagfájl fent megadott nevét tartalmazza. Ez a fenti szakasz 9.
* Több crm csomag van a csomagfájlban: Válassza az Igen **CSAK** lehetőséget, ha a crm több verzióját támogatja különböző csomagokkal. A legtöbb partner esetében ez a "Nem" lesz. Ha az Igen lehetőséget választja, a megoldás minden verziójához létre kell hoznia az AppSource-csomagokat. _Megjegyzés: Ez nem azt kérdezi, ha több **zip** fájlokat. Ha több solution.zip fájlja van, de csak egy verziója van, akkor is válassza a "Nem" lehetőséget. A csomagolóeszköz automatikusan összehozza ezeket._

### <a name="crm-package-availability"></a>A CRM-csomag elérhetősége

Ebben a szakaszban válassza ki, hogy a csomag milyen CRM-régiók számára lesz elérhető. Arról, hogy mely CRM-régiók melyik országokat/régiókat szolgálják ki, kérjük, olvassa el a következő linket:[https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Megjegyzés: A "Szuverén és amerikai kormányfelhő" szuverén üzembe helyezés különleges engedélyt és érvényesítést igényel a tanúsítás során

## <a name="storefront-details"></a>Kirakat részletei

### <a name="offer-summary"></a>Ajánlat összegzése

Ez az ajánlat értékajánlatának összegzése. Megjelenik az ajánlat keresési oldalán. Legfeljebb 100 karakter lehet.

### <a name="offer-description"></a>Ajánlat leírása

Ez az a leírás, amely megjelenik az alkalmazás részletes oldalán. A maximális megengedett érték 1300 karakter

### <a name="industries"></a>Ágazatok

Válassza ki azt az iparágat, amelyhez az alkalmazás a legjobban igazodik. Ha az alkalmazás több iparágra vonatkozik, ezt üresen hagyhatja.

### <a name="categories"></a>Kategóriák

Válassza ki az alkalmazás szempontjából releváns kategóriákat. Legfeljebb 3-at válasszon.

### <a name="app-type"></a>Alkalmazástípus

Válassza ki, hogy az alkalmazás milyen típusú próbaverziót engedélyez az AppSource-on. Az "ingyenes" azt jelenti, hogy az alkalmazás ingyenes. A "próbaverzió" azt jelenti, hogy az ügyfelek rövid ideig kipróbálhatják az alkalmazást az AppSource-on. A Dynamics 365 for Customer Engagement alkalmazások esetében a "Próbakérelem" nem támogatott. Ne válassza ezt a lehetőséget.

### <a name="help-link-for-your-app"></a>Az alkalmazás súgóhivatkozása

Adja meg az alkalmazáshoz kapcsolódó súgóinformációkat tartalmazó oldal URL-címét.

### <a name="supported-countriesregions"></a>Támogatott országok/régiók

Ez a mező azokat az országokat/régiókat határozza meg, amelyekben az ajánlat próbaverzióra elérhető lesz.

### <a name="supported-languages"></a>Támogatott nyelvek

Válassza ki az alkalmazás által támogatott nyelveket. Ha az alkalmazás odébban további olyan nyelveket is támogat, amelyek [appsource@microsoft.com](mailto:appsource@microsoft.com) nem szerepelnek a listán, továbbra is tegye közzé ajánlatát, és írjon nekünk e-mailt a következő címre: hogy tudassa velünk.

### <a name="app-version"></a>Az alkalmazás verziója

Adja meg az alkalmazás verziószámát

### <a name="app-release-date"></a>Alkalmazás megjelenési dátuma

Adja meg az alkalmazás megjelenési dátumát

### <a name="products-your-app-works-with-max-3"></a>Az alkalmazás által (max. 3)

Az alkalmazás által támogatott, listaspecifikus termékek. Legfeljebb három terméket sorolhat fel. Egy termék listázásához kattintson a pluszjelre (az új mellett), és létrejön egy új nyitott szövegmező, amely beírja az alkalmazás által létrehozott termék nevét.

### <a name="search-keywords-max-3"></a>Keresési kulcsszavak (max. 3)

AppSource lehetővé teszi az ügyfél számára, hogy nem keresés kulcsszavak alapján. Megadhatja azokat a kulcsszavakat, amelyekhez az alkalmazás megjelenik a vevők számára.

Például, ha az alkalmazás "My Emailing Service" E-mailek, Levelezés, Mail szolgáltatás lehet néhány kulcsszót. Olyan szavakat válasszon, amelyeket a felhasználók valószínűleg az alkalmazás kereséséhez használnak az AppSource keresőmezőjében.

### <a name="hide-key"></a>Kulcs elrejtése

Ez egy olyan kulcs, amely et kombinálni kell az ajánlat előnézeti URL-címével, hogy elrejtse a nyilvános nézetből. Ez nem jelszó. Itt bármilyen karakterláncot megadhat.

### <a name="offer-logo-png-format-48x48"></a>Ajánlat embléma (png formátum, 48x48)

Ez megjelenik az alkalmazás keresési oldalán. **Csak png formátum engedélyezett.** Töltsön fel egy png képet\*48PX 48PX felbontással

### <a name="offer-logo-png-format-216x216"></a>Ajánlat embléma (png formátum, 216x216)

Ez megjelenik az alkalmazás részletes oldalán. **Csak png formátum engedélyezett.** Töltsön fel egy png képet felbontása 216PX\*216PX

### <a name="videos"></a>Videók

Legfeljebb négy videót tölthetsz fel. Minden feltölteni kívánt videóhoz ki kell töltened a videó nevét, URL-címét (csak A YouTube vagy a Vimeo) és a Miniatűrt a videóhoz való társításhoz. A miniatűrnek png formátumúnak kell lennie,\*és 1280PX 720PX formátumúnak kell lennie. Új videó(k) hozzáadásához kattintson a pluszjelre. A videók miniatűrje(i) megjelennek az alkalmazás részletes oldalán.

### <a name="documents"></a>Dokumentumok

Legfeljebb három dokumentumot tölthet fel PDF formátumban. Minden feltölteni kívánt dokumentumhoz ki kell töltenie a dokumentum nevét, és fel kell töltenie a dokumentumot. A dokumentumnak pdf formátumúnak kell lennie.

Új dokumentum(ok) hozzáadásához kattintson a pluszjelre

### <a name="screenshots"></a>Képernyőképek

Ezek olyan képernyőképek, amelyek az alkalmazás AppSource-részletoldalán jelennek meg.

### <a name="privacy-policy"></a>Adatvédelmi irányelvek

Adja meg az alkalmazás adatvédelmi irányelveinek URL-címét

### <a name="terms-of-use"></a>Használati feltételek

Adja meg az alkalmazás használati feltételeit. Az AppSource-ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az alkalmazást

### <a name="support-url"></a>Támogatási URL-cím

Adja meg az alkalmazás támogatási URL-címét.

### <a name="lead-destination"></a>Érdeklődő célja

Válassza ki azt a CRM rendszert, amelyen a rendszer tárolja az érdeklődőt. Válassza az "Azure Table" lehetőséget, ha az alábbi CRM-rendszerek valamelyikével rendelkezik: Salesforce, Marketo, Microsoft Dynamics CRM. Az itt kiválasztott CRM-rendszer az a hely, ahol megírjuk az alkalmazás sal kapcsolatos adatait az AppSource (érdeklődők) szolgáltatásban. A kiválasztott CRM-rendszertől függően kattintson az alábbi URL-címre a következő mezőkészlet kitöltésével kapcsolatos információkért.

* [Azure-táblázat](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Kirakat részletei

Az elérhetőségi adatok kizárólag a partner és a Microsoft közötti belső kommunikációhoz használatosak. Megjegyzés: Fontos, hogy olyan e-mail címet használjon, amelyet ezeken a mezőkön figyelnek. Ezt az e-mailt arra használjuk, hogy kommunikáljunk Önnel az AppSource-on való közzététel előrehaladásáról. Csak a támogatási URL lesz látható az ügyfelek számára.
