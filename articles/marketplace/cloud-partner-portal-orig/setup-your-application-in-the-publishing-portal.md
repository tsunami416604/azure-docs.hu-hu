---
title: Az alkalmazás a közzétételi portál beállítása |} A Microsoft Docs
description: Hogyan állíthatja be az alkalmazás a felhő közzétételi portál utasításait.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 8ac0fbb1c62e4162e1c4ad040365a16d055e4552
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310017"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Az alkalmazás a közzétételi portál beállítása

Most már készen áll az alkalmazás a közzétételi portál beállítása.

## <a name="login-and-create-a-new-offer"></a>Bejelentkezés és a egy új ajánlat létrehozása

1. Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2. A bal oldali navigációs sávban kattintson a "+ új ajánlat", és válassza ki a "Dynamics 365 for Customer Engagement."

![Egy új ajánlat kiválasztása](./media/CRMScreenShot14.png)

1. Egy új ajánlat "Szerkesztő" nézet most megnyílik az Ön számára, és szerzői készen vagyunk.

![Új ajánlat képernyő](./media/CRMScreenShot15.png)

1. A "űrlapok" igénylő ki kell tölteni a bal oldalon a "Szerkesztő" nézetben láthatók. Minden egyes "form" automatikusan kitölti a mezőket egy készlete áll. Szükséges, piros csillaggal jelölt mezők (\*).

Nincsenek a négy fő űrlapok egy Dynamics 365 Customer Engagement-ajánlat készítése

* Ajánlat beállításai
* Technikai információ
* Storefront részletei
* Kapcsolatok

## <a name="fill-out-the-offer-settings-form"></a>Töltse ki az ajánlat beállítások képernyő

Az ajánlat beállítások képernyő művelet egy alapszintű az ajánlat beállításainak megadásához. A különböző mezők az alábbiakban tekintheti át.

### <a name="offer-id"></a>Ajánlat azonosítója

Ez a közzétevő-profilon belül az ajánlat egyedi azonosítója. Ezt az Azonosítót meg fognak jelenni a termék URL-címeket. Csak kisbetűs alfanumerikus karakterekből és kötőjelekből (-) állhat. Az azonosító nem végződhet kötőjellel, és legfeljebb 50 karakter hosszú lehet. Ez a mező után egy ajánlatot élesíti zárolva van.

például, ha a kiadó **"contoso"** közzétevők ajánlatot hoz létre Ajánlatazonosítót **"minta-WebApp"**, ez fog megjelenni az appsource-ban, mint "https:\//appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview"

### <a name="publisher-id"></a>Közzétevő azonosítója

A legördülő listából válassza ki ezt az ajánlatot a közzétenni kívánt közzétevő profilt teszi lehetővé. Ez a mező után egy ajánlatot élesíti zárolva van.

### <a name="name"></a>Name (Név)

Ez az ajánlat megjelenített neve. Ez a név fog megjelenni a [AppSource](https://appsource.microsoft.com/). Legfeljebb 50 karakterből állhat.

Kattintson a "Mentés" gombra az előrehaladást. Következő lépés az ajánlatban technikai információ hozzáadása lenne.

## <a name="fill-out-the-technical-info-form"></a>Töltse ki a technikai információ képernyő


A műszaki adatok képernyő az, ahol fog kitölti konkrét információk, a Dynamics 365 Customer Engagement megoldás. A rendszer felett, további információt jelenthet. Lásd az alábbi példát.

![Technikai információkat képernyőre](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Informace aplikace

A legtöbb közzétevők hagyja ezeket az alapértelmezett értékeket, a felhasználó, rendelkező mezők nem, nem, és a egy üres alkalmazás konfigurációs URL-címe a fenti képernyőképen megfelelően.

### <a name="crm-package"></a>CRM-csomag

![CRM-csomag adatai](./media/CRMScreenShot17.png)

A következő magyarázatot ezeket a mezőket:

* A csomag neve: A fájl nevét a fenti lépésben létrehozott esetén a zip-fájl létrehozása, amely a CRM AppSource csomag. A fenti példában, a "Microsoft\_SamplePackage.zip".
* A csomaghely URL-címe: Ez az URL-CÍMÉT az Azure Storage-fiókot, amely tartalmazza a fent megadott fájl nevét. 9. lépés a fenti szakaszban létrehozott URL-címe.
* Van több crm-csomagot a csomag fájlban: Válassza az Igen **csak** Ha crm, a különböző csomagok több verzióját támogatja. A legtöbb partnerek számára ez lesz a "No". Ha az Igen lehetőséget választja, meg kell AppSource csomagok létrehozása a megoldás minden egyes verziója. _Megjegyzés: Ez azt nem kéri, ha azok több **zip** fájlokat. Ha több solution.zip fájlt, de csak egyetlen verziója, továbbra is választhat "nem" található. A csomagolás eszköz fog hozzák ezek automatikusan._

### <a name="crm-package-availability"></a>CRM-csomag rendelkezésre állása

Ebben a szakaszban válassza ki, milyen régiók CRM, a csomag számára elérhető lesz. Információ, amelyre régiókban mely országokban szolgálnak tekintse meg a hivatkozást: [https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Megjegyzés: Németország való üzembe helyezés "Szuverén és Egyesült Államokbeli kormányzati felhő" szuverén REQUIRES speciális engedélyek és az érvényesítés során a hitelesítésszolgáltató

## <a name="storefront-details"></a>Storefront részletei

### <a name="offer-summary"></a>Ajánlat-összefoglaló

Ez az az ajánlat értékajánlat összegzését. Az ajánlat keresés lapon fog megjelenni. Legfeljebb 100 karakterből kell lennie.

### <a name="offer-description"></a>Az ajánlat leírása

Ez az a leírást, amely megjelenik az alkalmazás részletei lapon. Engedélyezett maximális hossza 1300 karaktert

### <a name="industries"></a>Ágazatok

Válassza ki az iparág legjobb igazított az alkalmazást. Ha az alkalmazás vonatkozik több iparágban is ezt üresen hagyja.

### <a name="categories"></a>Kategóriák

Válassza ki, amely az alkalmazás a kategóriák. Válasszon legfeljebb 3.

### <a name="app-type"></a>Alkalmazástípus

Válassza ki a próbaverziót, amely az alkalmazás lehetővé teszi az appsource-ban. "Ingyenes" azt jelenti, hogy az alkalmazás ingyenes. "Próbaverzió" azt jelenti, hogy ügyfeleink próbálhatja ki az alkalmazást az appsource-ban rövid idő. 'A próbaverzió kérése' Dynamics 365 for Customer Engagement alkalmazásokat nem támogatott. Válassza ezt a beállítást.

### <a name="help-link-for-your-app"></a>Az alkalmazáshoz Súgó hivatkozásra

Egy oldal, amely rendelkezik az alkalmazáshoz kapcsolódó információk súgó URL-CÍMÉT adja meg.

### <a name="supported-countriesregions"></a>Támogatott országok/régiók

Ez a mező határozza meg, amelyben az ajánlat lesz elérhető, próba országok vagy régiók.

### <a name="supported-languages"></a>Támogatott nyelvek

Válassza ki a nyelvet, amely az alkalmazás támogatja-e. Ha az alkalmazás támogatja a további nyelveket, amelyek nem látható a listában, továbbra is az ajánlat közzététele és e-mail-minket: [ appsource@microsoft.com ](mailto:appsource@microsoft.com) , hogy jelezze nekünk.

### <a name="app-version"></a>Az alkalmazás verziója

Adja meg a verziószám az alkalmazás

### <a name="app-release-date"></a>Alkalmazás kiadási dátum

Adja meg az alkalmazás a kiadási dátum

### <a name="products-your-app-works-with-max-3"></a>Az alkalmazás működik együtt (max. 3) termékek

Működik az alkalmazás-specifikus-termékek. Legfeljebb három termék listázhatja. Termék listázásához, kattintson a a melletti plusz jelre (új), és a egy új nyílt szövegmező jön létre egy terméket, amely együttműködik az alkalmazás nevét adhatja meg.

### <a name="search-keywords-max-3"></a>A keresési kulcsszavak (max. 3)

Appsource-ban lehetővé teszi, hogy az ügyfél alapján kulcsszavak keresése. A kulcsszavak, amelynek az alkalmazás nem jelenik meg az ügyfelek halmaza is megadhatja.

Például ha az alkalmazás "Saját érdemes felkeresni szolgáltatás" e-maileket, levelezés, levelezési szolgáltatás lehet, hogy bizonyos kulcsszavakat. Válassza ki a szavak, amelyek a felhasználók valószínűleg használatával keres az alkalmazás az appsource-ban keresőmezőbe.

### <a name="hide-key"></a>Kulcs elrejtése

Ez az egy kulcsot, az ajánlat előnézeti URL-cím a nyilvánosság elől való elrejtéséhez értékkel egyesítve jön létre. Akkor sem a jelszót. Bármilyen karakterlánc itt is megadhatja.

### <a name="offer-logo-png-format-48x48"></a>Az ajánlat embléma (png formátumú, 48 x 48)

Ez az alkalmazás keresési oldalon fog megjelenni. **Csak png formátumú használata engedélyezett.** 48PX felbontású png-kép feltöltése\*48PX

### <a name="offer-logo-png-format-216x216"></a>Az ajánlat embléma (png formátumú, 216 x 216)

Ez az alkalmazás részletei lapon fog megjelenni. **Csak png formátumú használata engedélyezett.** 216PX felbontású png-kép feltöltése\*216PX

### <a name="videos"></a>Videók

Legfeljebb négy videókat tölthet fel. Minden videó szeretne feltölteni adja meg a videó neve, URL-címe (YouTube vagy Vimeo csak) és a miniatűr videó társítani kell. Miniatűr png formátumúnak kell lennie, és lehet 1280PX\*720PX. Új video(s) hozzáadásához kattintson a plusz jelre. Videók thumbnail(s) lesz az alkalmazás részletei lap jelenik meg.

### <a name="documents"></a>Dokumentumok

Legfeljebb három, a PDF formátumú dokumentumok tölthet fel. A feltölteni kívánt minden egyes dokumentumhoz kell a dokumentum nevét adja meg, és töltse fel a dokumentumot. A dokumentum pdf formátumban kell lennie.

Új dokumentum hozzáadásához kattintson a plusz jelre.

### <a name="screenshots"></a>Képernyőképek

Ezek a képernyőképeket, amely az appsource-ban az alkalmazás részletek lapon fog megjelenni.

### <a name="privacy-policy"></a>Adatvédelmi szabályzat

Adja meg az alkalmazás adatvédelmi szabályzat URL-címe

### <a name="terms-of-use"></a>Használati feltételek

Adja meg a használati feltételeket az alkalmazás. Appsource-on ügyfelei elfogadja ezeket a feltételeket, mielőtt az alkalmazás próbálkozhatnak van szükség.

### <a name="support-url"></a>Adresa URL

Adja meg az alkalmazás a támogatási URL-CÍMÉT.

### <a name="lead-destination"></a>Lead Destination

Válassza ki a CRM-rendszerrel, ahol vezető rendszer tárolja. Ha rendelkezik ilyennel, a következő CRM rendszereket, válassza ki itt "Azure Table": A Salesforce, a Marketo, a Microsoft Dynamics CRM. A CRM-rendszerrel, itt, ahol írunk a végfelhasználók számára, hogy kipróbálják az alkalmazását az appsource-ban (érdeklődők) részleteit. Attól függően, a CRM-rendszerrel választja kattintson a megfelelő URL-címet, végezze el a következő mezők információt

* [Azure-tábla](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Storefront részletei

A kapcsolattartási adatait csak a partner és a Microsoft belső kommunikációra szolgálnak. Megjegyzés: Fontos ezeket a mezőket a figyelt e-mail címet használ. E-mailt a kommunikálni Önnel a folyamat állapotát a közzététel az appsource-ban a használjuk. Csak a támogatási URL-CÍMÉT az ügyfelek számára látható lesz.
