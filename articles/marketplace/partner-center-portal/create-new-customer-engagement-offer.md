---
title: Új Dynamics 365 for Customer Engagement & PowerApps ajánlat létrehozása a kereskedelmi piacon
description: Új Dynamics 365 for Customer Engagement & PowerApps-ajánlat létrehozása az Azure Piactéren, az AppSource-ban vagy a Felhőszolgáltató (CSP) programon keresztül a Microsoft Partner Center Kereskedelmi piactér portálján keresztül.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 10d5449cb77ea09f62db0a2f2bbdab6761646738
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295463"
---
# <a name="create-a-new-dynamics-365-for-customer-engagement--powerapps-offer"></a>Új Dynamics 365 for Customer Engagement & PowerApps ajánlat létrehozása

Ez a témakör bemutatja, hogyan hozhat létre új Dynamics 365 for Customer Engagement & PowerApps-ajánlatot. A Dynamics 365 for Customer Engagement (PowerApps, Sales, Service, Project Service és Field Service) összes alkalmazásának végig kell mennie a tanúsítási folyamaton, és támogatnia kell a próbaverziót. A tanúsítási folyamat ellenőrzi a megoldást a szabványos követelmények, kompatibilitás és a megfelelő eljárások szempontjából. A próbaverzió lehetővé teszi a felhasználók számára, hogy a megoldást élő Dynamics 365 környezetben telepítsék.

A Dynamics 365 for Customer Engagement & PowerApps ajánlatai létrehozásának megkezdéséhez győződjön meg arról, hogy először [hozzon létre partnerközpont-fiókot,](./create-account.md) és nyissa meg a [Kereskedelmi piactér irányítópultját,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)és az **Áttekintés** lapot választotta.

![Kereskedelmi piactér irányítópultja a Partnerközpontban](./media/new-offer-overview.png)

>[!Note]
> Az ajánlat közzétételét követően a Partnerközpontban tett ajánlat szerkesztése csak az újbóli közzétételt követően frissül a rendszerben és az áruházfrontokban. Győződjön meg arról, hogy a módosítások után elküldi az ajánlatot közzétételre.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

Válassza a **+ Új ajánlat** gombot, majd a Dynamics **365 for Customer Engagement & PowerApps** menüelemet. Megjelenik **az Új ajánlat** párbeszédpanel.

### <a name="offer-id-and-alias"></a>Ajánlatazonosító és alias

- **Ajánlatazonosító:** A fiókban lévő minden egyes ajánlat egyedi azonosítója. Ez az azonosító látható lesz az ügyfelek számára a piactéri ajánlat és az Azure Resource Manager-sablonok URL-címében (ha van ilyen). Az ajánlatazonosítónak kisalfanumerikus karaktereknek kell lennie (beleértve a kötőjeleket és az aláhúzásjeleket, de nem lehet szóköz). Az azonosító legfeljebb 50 karakterből állhat, és a **Létrehozás**gombra kerülése után nem módosítható. Ha például itt adja meg az *1-es tesztajánlatot,* az ajánlat URL-címe a . `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Ajánlat alias**: A partnerközpontban az ajánlatra való hivatkozáshoz használt név. Ez a név nem lesz használva a piactéren, és eltér az ajánlat neve és egyéb értékek jelennek meg az ügyfelek számára. Ez az érték nem módosítható a **Létrehozás (Létrehozás) lehetőséget**követően.

Miután megadta **az ajánlatazonosítóját** és **az Ajánlat aliasát,** válassza a **Létrehozás lehetőséget.** Ezután az ajánlat minden különböző részén dolgozhat.

## <a name="offer-setup"></a>Ajánlat beállítása

Az **Ajánlat beállítási** lapja a következő információkat kéri. A mezők **kitöltése** után válassza a Mentés lehetőséget.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hogyan szeretné, hogy a potenciális ügyfelek kapcsolatba lépjenek ezzel a hirdetésajánlattal?

Válassza ki az ajánlathoz használni kívánt lehetőséget.

#### <a name="get-it-now-free"></a>Szerezd meg most (ingyenes)

Sorolja fel az ügyfeleknek kínált ajánlatát ingyenesen egy érvényes URL-cím megadásával *(http* vagy *https-től*kezdve), ahol hozzáférhetnek az alkalmazáshoz.  Például:`https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Ingyenes próbaverzió (lista)

Sorolja fel az ingyenes próbaverzióra mutató hivatkozást az ügyfeleknek egy érvényes URL-lel *(http* vagy *https-től*kezdve), ahol próbaverziót kaphatnak.  Például: `https://contoso.com/trial/my-app`. Az ingyenes próbaverziókat a szolgáltatás hozlétre, kezeli és konfigurálja, és nem rendelkezik a Microsoft által kezelt előfizetésekkel.

> [!NOTE]
> A jogkivonatok az alkalmazás kap a próba-linken keresztül csak az Azure Active Directory (Azure AD) segítségével automatizálhatja a fiók létrehozását az alkalmazásban. A Microsoft-fiókok nem támogatottak a token használatával történő hitelesítéshez.

#### <a name="contact-me"></a>Kapcsolat

Az ügyfélkapcsolati adatok at az ügyfélkapcsolat-kezelés (CRM) rendszer összekapcsolásával gyűjtheti. Az ügyfélengedélyt kér adatai megosztásához. Ezek az ügyféladatok, valamint az ajánlat neve, azonosítója és piactéri forrás, ahol az ajánlat található, a rendszer elküldi a CRM rendszer, amely már konfigurált. A CRM konfigurálásáról további információt az [Érdeklődőkezelés csatlakoztatása](#connect-lead-management)című témakörben talál. 

### <a name="test-drive"></a>Tesztvezetés

A tesztvezetés nagyszerű módja annak, hogy bemutassa ajánlatát a potenciális ügyfeleknek azáltal, hogy lehetőséget ad nekik arra, hogy "vásárlás előtt próbáljanak ki", ami fokozott konverziót és magasan képzett érdeklődők létrehozását eredményezi. [További információ a tesztvezetésekről.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

A tesztvezetés engedélyezéséhez jelölje be a **Tesztmeghajtó engedélyezése** jelölőnégyzetet. Ezután be kell állítania egy demonstrációs környezetet a [Tesztmeghajtó műszaki konfigurációkonfigurálásában,](#test-drive-technical-configuration) hogy az ügyfelek meghatározott ideig kipróbálhatják az ajánlatot. 

#### <a name="type-of-test-drive"></a>A tesztvezetés típusa

Válasszon az alábbi lehetőségek közül:

- **[Azure Resource Manager:](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** Egy központi telepítési sablon, amely tartalmazza az összes Azure-erőforrások, amelyek tartalmazzák a megoldást. A forgatókönyvnek megfelelő termékek csak Azure-erőforrásokat használnak.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)**: A Microsoft üzemelteti és karbantartja a business central vállalati erőforrás-tervezési rendszer (pénzügyek, műveletek, ellátási lánc, CRM stb.) tesztmeghajtó-szolgáltatását (beleértve a kiépítést és üzembe helyezést is).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)**: A Microsoft üzemelteti és fenntartja a Customer Engagement rendszer (értékesítés, szolgáltatás, projektszolgáltatás, helyszíni szolgáltatás stb.) tesztmeghajtó-szolgáltatását (beleértve a kiépítést és a telepítést is).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)**: A Microsoft üzemelteti és karbantartja a tesztmeghajtó szolgáltatást (beleértve a kiépítést és a telepítést) egy pénzügyi és műveleti vállalati erőforrás-tervezési rendszerhez (pénzügyek, műveletek, gyártás, ellátási lánc stb.). 
- **[Logikai alkalmazás](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)**: Az összes összetett megoldásarchitektúrát felölelő központi telepítési sablon. Minden egyéni terméknek ilyen típusú tesztvezetést kell használnia.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)**: Beágyazott hivatkozás egy egyéni irányítópultra. Azoknak a termékeknek, amelyek interaktív Power BI-vizualizációt szeretnének bemutatni, az ilyen típusú tesztvezetést kell használniuk. Itt mindössze a beágyazott Power BI URL-címét kell feltöltenie.

#### <a name="additional-test-drive-resources"></a>További tesztmeghajtó-erőforrások

- [A tesztvezetés műszaki gyakorlati gyakorlatai](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [A Test Drive marketingjének bevált módszerei](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Tesztvezetés – áttekintés egy pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Csatlakoztassa az érdeklődőkezelést

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

További információt az [Érdeklődőkezelés áttekintése című témakörben talál.](./commercial-marketplace-get-customer-leads.md)

Ne felejtse el **menteni,** mielőtt továbblépne a következő szakaszra.

## <a name="properties"></a>Tulajdonságok

A **Tulajdonságok** lapon meghatározhatja az ajánlat csoportosításához használt kategóriákat és iparágakat a piactéren, az alkalmazás verzióján és az ajánlatot támogató jogi szerződéseken. A lap befejezése után válassza a **Mentés** gombot.

### <a name="category"></a>Kategória

Válasszon legalább egy és legfeljebb három kategóriát. A **kategória** segítségével az ajánlatot a megfelelő piactérkeresési területekre helyezheti el. Ügyeljen arra, hogy hívja ki, hogy az ajánlat támogatja ezeket a kategóriákat az ajánlat leírása.

### <a name="industry"></a>Iparág

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Alkalmazható Dynamics 365 termékek

Válassza ki az összes Dynamics 365 terméket, amelyre ez az ajánlat vonatkozik.

### <a name="app-version"></a>Az alkalmazás verziója

Adja meg az ajánlat verziószámát. Az ügyfelek ezt a verziót az ajánlat részletes oldalán fogják látni. Ha csak marketing/leíró módosítások miatt frissíti a verziószámot, jelölje be a **Csak marketingmódosítás** jelölőnégyzetet. Ez a beállítás lehetővé teszi, hogy az ajánlat megkerülje a tanúsítási és kiépítési szakaszok.

### <a name="terms-and-conditions"></a>Feltételek és kikötések

Adja meg saját jogi feltételeit az **Általános Szerződési Feltételek** mezőben. Azt az URL-t is megadhatod, ahol a feltételek találhatók. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt megpróbálhatnák az ajánlatát.

## <a name="offer-listing"></a>Ajánlat lista

Az Ajánlat listalap azokat a nyelveket jeleníti meg, amelyeken az ajánlat megjelenik. Jelenleg az **angol (Egyesült Államok)** az egyetlen elérhető lehetőség.

Meg kell adnia a piactér adatait (ajánlat neve, leírása, képek stb.) az egyes nyelvekhez/piacokhoz. Válassza ki az információ megadásához szükséges nyelvet/piacnevet.

> [!NOTE]
> Az ajánlat tartalma (például leírás, dokumentumok, képernyőképek, használati feltételek stb.) nem szükséges angol nyelven, amennyiben az ajánlat leírása a következő mondattal kezdődik: "Ez az alkalmazás csak [nem angol nyelven] érhető el." Az is elfogadható, hogy *egy hasznos link URL-t* kínál tartalmat más nyelven, mint amelyet az ajánlat lista tartalmát.

### <a name="name"></a>Név

Az itt megadott név az ajánlathirdetés címeként jelenik meg az ügyfelek számára. Ez a mező előre ki van töltve az **Ajánlat aliashoz** az ajánlat létrehozásakor megadott szöveggel, de ezt az értéket módosíthatja. Ez a név védjeggyel is ellátott lehet (és ön védjegy- vagy szerzői jogi szimbólumokat is tartalmazhat). A név nem lehet több 50 karakternél, és nem tartalmazhat hangulatjeleket.

### <a name="short-description"></a>Rövid leírás

Adja meg az ajánlat rövid leírását (legfeljebb 100 karakter). Ez a leírás használható a piactér keresési eredményei között.











### <a name="description"></a>Leírás

Adjon meg hosszabb leírást az ajánlatról (legfeljebb 3000 karakter). Ez a leírás jelenik meg az ügyfelek számára a piactéri lista áttekintése. Adja meg az ajánlat értékajánlatát, a legfontosabb előnyöket, a kategória- és/vagy iparági szövetségeket, az alkalmazáson belüli vásárlási lehetőségeket és a szükséges közzétételeket.

Néhány tipp a leírás megírásához:  

- Egyértelműen írja le az ajánlat értékajánlatát a leírás első néhány mondatában. Az értékajánlatban a következő információk szerepeljenek:
  - A termék leírása
  - A termék előnyeit élvező felhasználó típusa
  - A termék által megszólított vásárlói igények vagy fájdalom
- Ne feledje, hogy az első néhány mondat megjelenhet a keresőmotor találatai között.  
- Ne hagyatkozzon a termék értékesítéséhez szükséges funkciókra és funkciókra. Ehelyett összpontosítson a szállít.  
- A lehető legnagyobb mértékben használjon iparág-specifikus szókincset vagy haszonalapú megfogalmazást.
- Fontolja meg a HTML-címkék használatát a leírás formázásához és vonzóbbá.

Annak érdekében, hogy ajánlata leírása vonzóbbá legyen, használja a Rich Text szerkesztőt a leírás formázásához.

![A Rich Text szerkesztő használata](./media/text-editor2.png)

A Rich Text szerkesztő használatához kövesse az alábbi utasításokat:

- A tartalom formátumának módosításához jelölje ki a formázni kívánt szöveget, és jelöljön ki egy szövegstílust az alábbi módon:

     ![Szövegformátum módosítása a Rich Text szerkesztővel](./media/text-editor3.png)

- Ha listajeles vagy számozott listát szeretne hozzáadni a szöveghez, használja az alábbi beállításokat:

     ![Listák hozzáadása a Rich Text szerkesztővel](./media/text-editor4.png)

- A szöveg behúzásának hozzáadásához vagy eltávolításához használja az alábbi beállításokat:

     ![A Rich Text szerkesztő behúzása](./media/text-editor5.png)







### <a name="search-keywords"></a>Kulcsszavak keresése

Tetszés szerint legfeljebb három keresési kulcsszót adhat meg, hogy az ügyfelek megtalálják az ajánlatot a piacon. A legjobb eredmény érdekében próbálja meg ezeket a kulcsszavakat is használni a leírásában.

### <a name="products-your-app-works-with"></a>Az alkalmazás által feldolgozott termékek

Ha tudatni szeretné az ügyfelekkel, hogy az alkalmazás bizonyos termékekkel működik, itt legfeljebb három terméknevet adhat meg.

### <a name="support-urls"></a>Támogatási URL-ek

Ebben a szakaszban hivatkozásokat adhat meg, amelyek segítségével az ügyfelek jobban megismerhetik az ajánlatot.

#### <a name="help-link"></a>Súgó hivatkozás

Adja meg azt az URL-címet, ahol az ügyfelek többet tudhatnak meg az ajánlatról.

#### <a name="privacy-policy-url"></a>Adatvédelmi irányelvek URL-címe

Adja meg a szervezet adatvédelmi irányelveinek URL-címét. Ön felelős annak biztosításáért, hogy alkalmazása megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint hogy érvényes adatvédelmi szabályzatot biztosítson.

### <a name="contacts"></a>Kapcsolatok

Ebben a szakaszban meg kell adnia egy **támogatási partner** és egy mérnöki partner nevét, e-mail címét és **telefonszámát.** Ez az információ nem jelenik meg az ügyfelek számára, de elérhető lesz a Microsoft számára, és a kripta-partnerek rendelkezésére állhat.

A **Támogatás kapcsolatrészben** meg kell adnia a **támogatási URL-címet** is, ahol a CSP-partnerek támogatást találhatnak az ajánlathoz.

### <a name="supporting-documents"></a>Igazoló dokumentumok

Itt legalább egy (és legfeljebb három) kapcsolódó marketingdokumentumot kell megadnia, például tanulmányokat, brosúrákat, ellenőrző listákat vagy prezentációkat. Ezeknek a dokumentumoknak .pdf formátumúaknak kell lenniük.

### <a name="marketplace-images"></a>Marketplace-képek

Ebben a szakaszban olyan emblémákat és képeket adhat meg, amelyeket az ajánlat ügyfélnek való megjelenítésekor fog használni. Minden képnek .png formátumúnak kell lennie.

#### <a name="store-logos"></a>Áruházi emblémák

Az ajánlat logóját három méretben adhatod meg: **Kicsi (48 x 48)**, **Nagy (216 x 216)** és **Széles (255 x 115)**. A kis és nagy méreteket kell megadni.

#### <a name="hero"></a>Hős

A hőskép nem kötelező. Ha ön beszerez egy, ez must mér 815 x betű 290 fénykép.

#### <a name="screenshots"></a>Képernyőképek

Képernyőképek hozzáadása, amelyek bemutatják az ajánlat működését. Legalább egy képernyőkép szükséges, és legfeljebb öt. Minden képernyőképnek 1280 x 720 képpontnak kell lennie.

#### <a name="videos"></a>Videók

Az ajánlatot jól látható anamnézisben legfeljebb négy videót adhatsz hozzá. Ezeket a videókat a YouTube-on és/vagy a Vimeo-n kell üzemeltetni. Mindegyikhez írja be a videó nevét, URL-címét és a videó miniatűr képét (1280 x 720 képpont)

#### <a name="additional-marketplace-listing-resources"></a>További piactér-listaforrások

- [Gyakorlati tanácsok a piactéri ajánlatlistákhoz](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Rendelkezésre állás

Az **Elérhetőség** oldalon lehetőség nyílik arra, hogy hol és hogyan teheti elérhetővé az ajánlatot.

### <a name="markets"></a>Piacok

Ebben a szakaszban megadhatja azokat a piacokat, amelyeken az ajánlatnak elérhetőnek kell lennie. Ehhez válassza a **Piacok szerkesztése**lehetőséget, amely megjeleníti a **Piackiválasztása** előugró ablakot.

Alapértelmezés szerint nincs piac kiválasztva, de legalább egy piacot ki kell választania az ajánlat közzétételéhez. Kattintson **az Összes kiválasztása** lehetőségre, ha minden lehetséges piacon elérhetővé szeretné tenni az ajánlatát, vagy válassza ki a hozzáadni kívánt piacokat. Miután végzett, válassza a **Mentés lehetőséget.**

Az itt megadott beállítások csak az új akvizíciókra vonatkoznak; ha valaki már rendelkezik az alkalmazásegy bizonyos piacon, és később távolítsa el a piacon, az emberek, akik már az ajánlatot, hogy a piacon továbbra is használni, de nem új ügyfelek ezen a piacon lesz képes-hoz kap az ajánlatot.

> [!IMPORTANT]
> Az Ön felelőssége, hogy megfeleljen a helyi jogi követelményeknek, még akkor is, ha ezek a követelmények nem szerepelnek itt vagy a Partnerközpontban. Még akkor is, ha az összes piacot választja, a helyi törvények és korlátozások vagy egyéb tényezők megakadályozhatják, hogy bizonyos ajánlatok egyes országokban és régiókban felkerüljenek a listára.

### <a name="preview-audience"></a>Közönség előnézete

Mielőtt élőben közzétenné az ajánlatot a szélesebb piactéri ajánlatban, először elérhetővé kell tennie azt egy korlátozott **előnézeti közönség számára.** Itt adjon meg egy **Elrejtés gombot** (olyan karakterláncot, amely csak kisbetűket és/vagy számokat használ). Az előnézeti közönség tagjai ezt az elrejtési kulcsot használhatják tokenként az ajánlat előnézetének megtekintéséhez a piactéren.

Ezt követően, amikor készen áll az ajánlat elérhetővé téválására és az előnézeti korlátozás eltávolítására, el kell távolítania az **Elrejtés kulcsot,** és újra közzé kell tennie.

## <a name="technical-configuration"></a>Műszaki konfiguráció

A **Műszaki konfiguráció** lap határozza meg az ajánlathoz való csatlakozáshoz használt technikai részleteket. Ez a kapcsolat lehetővé teszi számunkra, hogy az Ön ajánlatát a végfelhasználó számára, ha úgy döntenek, hogy megszerzi azt.

### <a name="base-license-model"></a>Alaplicenc-modell

Az alaplicenc-modell határozza meg, hogy az ügyfelek hogyan vannak hozzárendelve az alkalmazáshoz a CRM Felügyeleti központban. Válassza **az Erőforrás lehetőséget** a példányalapú licenceléshez vagy a Felhasználó **lehetőséget,** ha a licencek bérlőnként egy-egy licencet kapnak.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>S2S kimenő és CRM biztonságos tárolóbeli hozzáférés szükséges

Jelölje be ezt a jelölőnégyzetet a CRM Secure Store vagy a Server-to-Server (S2S) kimenő hozzáférés konfigurálásának engedélyezéséhez. Ez a szolgáltatás a Dynamics 365 csapat speciális megfontolását igényli a minősítési fázis ban. A Microsoft felveszi Önnel a kapcsolatot, hogy további lépéseket hajtson végre a szolgáltatás támogatásához.

### <a name="application-configuration-url"></a>Alkalmazáskonfiguráció URL-címe

Adja meg a konfigurációs weblap URL-címét, amely lehetővé teszi az ügyfél számára az alkalmazás konfigurálását.

### <a name="crm-package"></a>CRM csomag

A **csomag helymezőjének URL-címében** adja meg a feltöltött CRM-csomag .zip fájlját tartalmazó Azure Storage-fiók URL-címét. Ennek az URL-címnek tartalmaznia kell egy írásvédett SAS-kulcsot, amely lehetővé teszi a Microsoft számára, hogy ellenőrzés céljából felvegye a csomagot.

Jelölje be a **"Több CRM-csomag van a csomagfájlban"** feliratú jelölőnégyzet, ha van ilyen. Ha igen, mindenképpen foglalja bele az összes csomagot a .zip fájlba.

### <a name="crm-package-availability"></a>A CRM-csomag elérhetősége

Ebben a szakaszban válassza a **+ Régió hozzáadása** lehetőséget, ha meg szeretné adni azokat a földrajzi régiókat, amelyekben a CRM-csomag elérhető lesz az ügyfelek számára. A következő szuverén régiókban történő üzembe helyezéshez a minősítési folyamat során különleges engedélyre és ellenőrzésre van szükség: [Németország](https://docs.microsoft.com/azure/germany/), [Us Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)és TIP.

Alapértelmezés szerint a fent megadott **alkalmazáskonfigurációs URL-cím** lesz minden régióban használható. Ha szeretné, megadhat egy külön alkalmazáskonfigurációs URL-címet egy vagy több adott régióhoz. 

## <a name="test-drive-technical-configuration"></a>Tesztvezetés műszaki konfigurációja

Ha az [Ajánlat beállítási](#offer-setup) lapján a **Tesztvezetés engedélyezése** lehetőséget választotta, itt meg kell adnia a részleteket, hogy az ügyfelek megtapasztalhassák az ajánlat tesztvezetését.

A **Tesztmeghajtó** oldal lehetővé teszi egy bemutató (vagy "tesztmeghajtó") beállítását, amely lehetővé teszi az ügyfelek számára, hogy kipróbálják az ajánlatot, mielőtt elkötelezné magukat a megvásárlás mellett. További információ: [Mi az a Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Ha már nem szeretne tesztvezetést biztosítani az ajánlatához, térjen vissza az **[Ajánlat beállítási](#offer-setup)** oldalára, és törölje a jelet **a Tesztvezetés engedélyezése**jelölőnégyzetből.

A következő típusú tesztmeghajtók állnak rendelkezésre, mindegyik saját műszaki konfigurációs követelményekkel.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logikai alkalmazás](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (műszaki konfiguráció nem szükséges)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Műszaki konfiguráció az Azure Resource Manager tesztmeghajtójához

Egy központi telepítési sablon, amely tartalmazza a megoldásösszes Azure-erőforrást. A forgatókönyvnek megfelelő termékek csak Azure-erőforrásokat használnak. További információ az [Azure Resource Manager tesztmeghajtójának](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)beállításáról.

- **Régiók** (kötelező): Jelenleg 26 Azure által támogatott régió van, ahol a tesztmeghajtó elérhetővé tehető. A tesztmeghajtót általában azokban a régiókban szeretné elérhetővé tenni, ahol a legtöbb ügyfél várható, hogy a legjobb teljesítmény hez legközelebb eső régiót választhassák ki. Győződjön meg arról, hogy az előfizetés e kiválasztott régiókban az összes szükséges erőforrás üzembe helyezéséhez engedélyezett.

- **Példányok:** Válassza ki a típus (meleg vagy hideg) és a rendelkezésre álló példányok számát, amely megszorozza a régiók száma, ahol az ajánlat elérhető.

**Gyors**: Az ilyen típusú példány telepítve van, és a hozzáférésre vár a kiválasztott régiónként. Az ügyfelek azonnal hozzáférhetnek a tesztmeghajtó *kokszpéldányaihoz,* ahelyett, hogy meg kellene várniuk a központi telepítést. A kompromisszum az, hogy ezek a példányok mindig fut nak az Azure-előfizetés, így nagyobb uptime költség merülnek fel. Erősen ajánlott, hogy legalább egy *hot* példány, mivel a legtöbb ügyfél nem akar várni a teljes központi telepítések, ami egy lemorzsolódás az ügyfél használat, ha nincs *hot* példány nem érhető el.

**Hideg**: Ez a példánytípus a régiónként esetlegesen üzembe helyezhető példányok teljes számát jelöli. A hideg példányok megkövetelik, hogy a teljes Test Drive Resource Manager-sablon üzembe helyezze, amikor egy ügyfél kéri a tesztmeghajtót, így *a hideg* példányok betöltése sokkal lassabb, mint a hot-példányok. *Hot* A kompromisszum az, hogy csak akkor kell fizetnie a tesztvezetés időtartama alatt, *nem* mindig fut az Azure-előfizetés, mint egy *hot* példány.

- **Tesztmeghajtó Azure Resource Manager sablon:** Töltse fel az Azure Resource Manager sablont tartalmazó .zip.  Az Azure Resource Manager-sablon létrehozásáról az [Azure Resource Manager-sablonok létrehozása és üzembe helyezése az Azure Portal használatával című](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)rövid útmutató című cikkben olvashat bővebben.

- **A tesztvezetés időtartama** (kötelező): Adja meg, hogy a tesztvezetés mennyi ideig maradjon aktív, az óra számalatt. A tesztvezetés automatikusan leáll ezen időszak lejárta után. Ezt az időtartamot csak egész számú órával lehet beállítani (például "2" óra; "1.5" érvénytelen).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>A Dynamics 365 tesztvezetés műszaki konfigurációja

A Microsoft eltávolíthatja a tesztmeghajtó konklúdi beállításának összetettségét a szolgáltatás kiépítésének és üzembe helyezésének üzemeltetésével és karbantartásával az ilyen típusú tesztmeghajtó használatával. Az ilyen típusú üzemeltetett tesztmeghajtókonfiguráció ugyanaz, függetlenül attól, hogy a tesztvezetés Business Central, Customer Engagement vagy Operations közönséget céloz meg.

- **Maximális egyidejű tesztmeghajtók** (kötelező): Állítsa be a tesztvezetés egyszerre használható ügyfeleinek maximális számát. Minden egyidejű felhasználó egy Dynamics 365-licencet használ fel, amíg a tesztmeghajtó aktív, ezért gondoskodnia kell arról, hogy elegendő licenc áll rendelkezésre a maximális korlát beállításának támogatásához. Ajánlott értéke 3-5.

- **A tesztvezetés időtartama** (kötelező): Adja meg azt az időtartamot, amaddig, amíg a tesztvezetés aktív marad az órák számának meghatározásával. Ennyi óra elteltével a munkamenet véget ér, és már nem használja fel az egyik licencet. Javasoljuk, hogy az ajánlat összetettségétől függően 2-24 órás értéket nyújtson be. Ezt az időtartamot csak egész számú órával lehet beállítani (például "2" óra; "1.5" érvénytelen). A felhasználó új munkamenetet kérhet, ha kifut az időből, és újra hozzá szeretne férni a tesztvezetéshez.

- **Példány URL-címe** (kötelező): Az URL-cím, ahol az ügyfél megkezdi a tesztvezetést. Általában az alkalmazást futtató Dynamics 365-példány URL-címe telepített `https://testdrive.crm.dynamics.com`mintaadatokkal (például).

- **Példány webes API-URL-címe** (kötelező): A Dynamics 365-példány webes API-URL-címének lekérése a Microsoft 365-fiókba való bejelentkezéssel és a **Beállítások (Beállítások)** \&lapra való navigálással; **Testreszabás;** \& **Fejlesztői erőforrások** \&gt; **Példánywebes API -t (Service Root URL)** másolja `https://testdrive.crm.dynamics.com/api/data/v9.0`az itt található URL-címre (például ).

- **Szerepkör neve** (kötelező): Adja meg az egyéni Dynamics 365 tesztmeghajtóban megadott biztonsági szerepkörnevét. Ez a biztonsági szerepkör neve lesz hozzárendelve a felhasználónak a tesztvezetés során (például teszt-meghajtó-szerepkör).

### <a name="technical-configuration-for-logic-app-test-drive"></a>A Logic alkalmazás tesztmeghajtójának műszaki konfigurációja

Minden egyéni terméknek az ilyen típusú tesztmeghajtó-telepítési sablont kell használnia, amely számos összetett megoldásarchitektúrát foglal magában. A Logic App tesztmeghajtóinak beállításával kapcsolatos további információkért látogasson el [az Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) and [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) oldalra a GitHubon.

- **Régió** (kötelező, egyválasztós legördülő lista): Jelenleg 26 Azure által támogatott régió van, ahol a tesztmeghajtó elérhetővé tehető. A logikai alkalmazás erőforrásai a kiválasztott régióban lesznek telepítve. Ha a logikai alkalmazás rendelkezik egy adott régióban tárolt egyéni erőforrásokat, győződjön meg arról, hogy a régió itt van kiválasztva. A legjobb módja ennek az, hogy teljes mértékben üzembe helyezi a logic app helyileg az Azure-előfizetésa a portálon, és ellenőrizze, hogy megfelelően működik-e, mielőtt ezt a kiválasztást.

- **Maximális egyidejű tesztmeghajtók** (kötelező): Állítsa be a tesztvezetés egyszerre használható ügyfeleinek maximális számát. Ezek a tesztmeghajtók már telepítve vannak, így az ügyfelek azonnal hozzáférhetnek a központi telepítésre való várakozás nélkül.

- **A tesztvezetés időtartama** (kötelező): Adja meg, hogy a tesztvezetés mennyi ideig maradjon aktív, az óra számalatt. A tesztvezetés ezen időszak lejárta után automatikusan leáll.

- **Azure-erőforráscsoport neve** (kötelező): Adja meg az [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) nevét, ahol a logic app tesztmeghajtó menti.

- **Azure-logikai alkalmazás neve** (kötelező): Adja meg a tesztmeghajtót a felhasználóhoz hozzárendelő logikai alkalmazás nevét. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

- **A logic app neve (kötelező):** Adja meg annak a logikai alkalmazásnak a nevét, amely az ügyfél befejezése után dekátlanítja a tesztmeghajtót. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>A Power BI tesztmeghajtóihoz nem szükséges műszaki konfiguráció

Azok a termékek, amelyek interaktív Power BI-vizualizációt szeretnének bemutatni, egy beágyazott hivatkozás segítségével megoszthatják az egyéni irányítópultot tesztmeghajtóként, további műszaki konfiguráció nélkül. További információ a[Power BI-sablonalkalmazások](https://docs.microsoft.com/power-bi/service-template-apps-overview) beállításáról.

### <a name="deployment-subscription-details"></a>Üzembe helyezési előfizetés részletei

A Test Drive az Ön nevében történő üzembe helyezéséhez hozzon létre és biztosítson egy külön, egyedi Azure-előfizetést (nem szükséges a Power BI tesztmeghajtóihoz).

- **Azure-előfizetés-azonosító** (az Azure Resource Manager és a Logic alkalmazások esetén szükséges): Adja meg az előfizetés-azonosítót, amely hozzáférést biztosít az Azure-fiókszolgáltatásaihoz az erőforrás-használati jelentések hez és a számlázáshoz. Azt javasoljuk, hogy fontolja meg [egy külön Azure-előfizetés tesztelésére,](https://docs.microsoft.com/azure/billing/billing-create-subscription) ha még nem rendelkezik. Az Azure-előfizetés-azonosítóját az Azure [Portalra](https://portal.azure.com/) való bejelentkezéssel és a bal oldali menü **Előfizetések** lapjára való navigálással találhatja meg. A lapon megjelenik az előfizetésazonosítója (például "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD-bérlőazonosító** (kötelező): Adja meg az Azure Active Directory (AD) [bérlői azonosítóját.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Az azonosító megkereséséhez jelentkezzen be az [Azure Portalra,](https://portal.azure.com/)válassza a bal oldali menü Active Directory lapját, válassza a **Tulajdonságok**lehetőséget, majd keresse meg a felsorolt **címtárazonosító** számot (például 50c464d3-4930-494c-963c-1e951d15360e). A tartománynév URL-címével is megkeresheti a szervezet [https://www.whatismytenantid.com](https://www.whatismytenantid.com)bérlői azonosítóját a következő címen: .

- **Azure AD-bérlői név** (dinamikus 365 esetén szükséges): Adja meg az Azure Active Directory (AD) nevét. Ennek a névnek a megkereséséhez jelentkezzen be az [Azure Portalra,](https://portal.azure.com/)a jobb felső sarokban a bérlő neve a fiók neve alatt jelenik meg.

- **Azure AD alkalmazásazonosító** (kötelező): Adja meg az Azure Active Directory (AD) [alkalmazásazonosítóját.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Az azonosító megkereséséhez jelentkezzen be az [Azure Portalra,](https://portal.azure.com/)válassza a bal oldali menü Active Directory lapját, válassza az **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a felsorolt **alkalmazásazonosítószámot** (például 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD alkalmazásügyfél-titkos** (kötelező): Adja meg az Azure [AD-alkalmazásügyfél-titkos.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) Ennek az értéknek a megkereséséhez jelentkezzen be az [Azure Portalra.](https://portal.azure.com/) Válassza az **Azure Active Directory** fülét a bal oldali menüben, válassza az **Alkalmazásregisztrációk**lehetőséget, majd válassza ki a tesztmeghajtó alkalmazást. Ezután válassza **a Tanúsítványok és titkos kulcsok**lehetőséget, válassza az Új **ügyféltitok**lehetőséget, adjon meg egy leírást, válassza a **Soha** **a Lejáratok**csoportban a Soha lehetőséget, majd válassza **a Hozzáadás lehetőséget.** Győződjön meg róla, hogy másolja le az értéket. Ne navigáljon el az oldalról, mielőtt lemásolna az értéket, különben nem lesz hozzáférése az értékhez.)

### <a name="test-drive-marketplace-listings"></a>A Tesztmeghajtó piactérlistái

A **Tesztmeghajtó** lapon található **Marketplace-listabeállítás** azokat a nyelveket jeleníti meg, amelyeken a tesztmeghajtó elérhető. Jelenleg az **angol (Egyesült Államok)** az egyetlen elérhető hely. Válassza ki a nyelv nevét, amely a tesztvezetés élményét leíró adatokat adja meg.

- **Leírás** (kötelező): Írja le a tesztvezetést, a bemutatást, a felhasználó által kísérletezendő célokat, a feltárandó funkciókat és minden olyan releváns információt, amely segít a felhasználónak eldönteni, hogy beszerezze-e az ajánlatot. Ebben a mezőben legfeljebb 3000 karakter nyi szöveg adható meg. 

- **Hozzáférés-információk** (az Azure Resource Manager és logic tesztmeghajtók esetén szükséges): Magyarázza el, hogy mit kell tudnia az ügyfélnek a tesztmeghajtó eléréséhez és használatához. Végigvezethet egy forgatókönyvet az ajánlat használatáról, és pontosan azt, hogy az ügyfélnek mit kell tudnia a funkciók eléréséhez a tesztvezetés során. Ebben a mezőben legfeljebb 10 000 karakternyi szöveg adható meg.

- **Használati útmutató** (kötelező): A tesztvezetés élményének részletes áttekintése. A felhasználói kézikönyvnek pontosan azt kell kiterjednie, hogy mit szeretne az ügyfélnek a tesztvezetés megtapasztalásából, és referenciaként kell szolgálnia az esetleges kérdéseikhez. A fájlnak PDF formátumúnak kell lennie, és feltöltés után el kell nevezni (legfeljebb 255 karakter).

- **Videók hozzáadása** (nem kötelező): A videókat fel lehet tölteni a YouTube-ra vagy a Vimeo-ra, és itt hivatkozással lehet hivatkozni egy linkre és egy miniatűr képre (533 x 324 képpont), hogy az ügyfél megtekinthesse az információk átjárását, hogy jobban megérthesse a tesztvezetést, beleértve az ajánlat funkcióinak sikeres használatát és az előnyeiket kiemelő forgatókönyveket.
  - **Név** (kötelező)
  - **URL (csak YouTube vagy Vimeo)** (kötelező)
  - **Thumbnail (533 x 324px)**: A képfájlnak PNG formátumúnak kell lennie.

## <a name="supplemental-content"></a>Kiegészítő tartalom

Ezen az oldalon további információkat adhat meg az ajánlatáról, hogy segítsen nekünk az ajánlat érvényesítésében. Ez az információ nem jelenik meg az ügyfelek számára, és nem teszik közzé a piactéren.

### <a name="key-usage-scenario"></a>Kulcshasználati forgatókönyv

Egy .pdf fájlt kell feltöltenie, amely felsorolja az ajánlat legfontosabb használati forgatókönyveit, amelyek et egy dokumentumban (.pdf formátumban) sorolnak fel. Az itt felsorolt összes forgatókönyvet az ellenőrző csapatunk ellenőrizheti, mielőtt jóváhagynánk az ajánlatot a piactérre.

## <a name="publish"></a>Közzététel

### <a name="submit-offer-to-preview"></a>Ajánlat küldése előnézetbe

Miután elvégezte az ajánlat összes szükséges szakaszát, válassza a **közzététel** lehetőséget a portál jobb felső sarkában. A bizottság átirányítja a **Véleményezés és közzététel** lapra.

Ha ez az első alkalom, hogy közzéteszi ezt az ajánlatot, a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak készültségi állapotát.
    - *Nincs elindítva* - azt jelenti, hogy a szakaszt nem érintette meg, és ki kell tölteni.
    - *Hiányos* - azt jelenti, hogy a szakasz hibákat, amelyeket ki kell javítani, vagy több információt kell megadni. Menjen vissza a szakasz(ok)hoz, és frissítse azt.
    - *Kész* - azt jelenti, hogy a szakasz befejeződött, az összes szükséges adatot megadta, és nincsenek hibák. Az ajánlat minden szakaszának teljes állapotban kell lennie az ajánlat elküldése előtt.
- A Megjegyzések a **minősítéshez** szakaszban adjon vizsgálati utasításokat a minősítő csapatnak, hogy az alkalmazás megfelelő tesztelése mellett adjon meg minden olyan kiegészítő megjegyzést, amely hasznos az alkalmazás megértéséhez.
- Küldje el az ajánlatot közzétételre a **Küldés**lehetőség kiválasztásával. Küldünk Önnek egy e-mailt, hogy tudd, ha az ajánlat előnézeti verziója elérhető, hogy áttekintsd és jóváhagyd. Térjen vissza a Partnerközpontba, és válassza a **Go-live** lehetőséget a nyilvánosan közzéteendő ajánlathoz (vagy ha egy privát ajánlat, a privát közönség számára).

## <a name="next-steps"></a>További lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
