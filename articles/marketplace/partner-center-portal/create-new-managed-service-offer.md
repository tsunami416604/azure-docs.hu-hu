---
title: Új felügyelt szolgáltatásajánlat létrehozása a kereskedelmi piactéren
description: Új felügyelt szolgáltatásajánlat létrehozása az Azure Piactéren való listázáshoz a Partnerközpont Kereskedelmi piactér portálján keresztül.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 3d7015d5e1c3b67b271e89b5653f56415bfb722e
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674201"
---
# <a name="create-a-new-managed-service-offer"></a>Új felügyelt szolgáltatásajánlat létrehozása

A Felügyelt szolgáltatás segítséget nyújt az [Azure Lighthouse-forgatókönyvek](../../lighthouse/overview.md) engedélyezéséhez. Ha egy ügyfél elfogadja a felügyelt szolgáltatás ajánlatát, akkor az [Azure delegált erőforrás-kezeléséhez](../../lighthouse/concepts/azure-delegated-resource-management.md)szükséges erőforrásokat is betudják vonni.

A Felügyelt szolgáltatásajánlatok létrehozásának megkezdéséhez győződjön meg arról, hogy először [hozzon létre egy Partnerközpont-fiókot,](./create-account.md) és nyissa meg a [Kereskedelmi piactér irányítópultját,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)és jelölje be az **Áttekintés** lapot. A Felügyelt szolgáltatás ajánlatközzétételéhez [Silver vagy Gold Cloud Platform kompetenciaszinttel kell rendelkeznie,](https://partner.microsoft.com/membership/cloud-platform-competency) vagy Azure Expert [MSP-nek](https://partner.microsoft.com/membership/azure-expert-msp) kell lennie.

![Kereskedelmi piactér irányítópultja a Partnerközpontban](./media/new-offer-overview.png)

>[!Note]
> Az ajánlat közzétételét követően a Partnerközpontban tett ajánlat szerkesztése csak az újbóli közzétételt követően frissül a rendszerben és az áruházfrontokban. Kérjük, győződjön meg arról, hogy a módosítások után benyújtja az ajánlatot közzétételre.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

Válassza a **+ Új ajánlat** gombot, majd a Felügyelt **szolgáltatás** menüelemet. Megjelenik **az Új ajánlat** párbeszédpanel.

### <a name="offer-id-and-alias"></a>Ajánlatazonosító és alias

- **Ajánlatazonosító:** A fiókban lévő minden egyes ajánlat egyedi azonosítója. Ez az azonosító látható lesz az ügyfelek számára a piactéri ajánlat URL-címében. Ez az azonosító csak kisalfanumerikus karaktereket tartalmazhat (beleértve a kötőjeleket és az aláhúzásjeleket, de a szóközöket nem), legfeljebb 50 karakterből áll, és nem módosítható a **Létrehozás**jelölőnégyzet után.  Ha például itt adja meg az *1-es tesztajánlatot,* az ajánlat URL-címe a . `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Ajánlat alias**: A partnerközpontban az ajánlatra való hivatkozáshoz használt név. Ez a név nem lesz használva a piactéren, és eltér az ajánlat neve és egyéb értékek jelennek meg az ügyfelek számára. Ez az érték nem módosítható a **Létrehozás (Létrehozás) lehetőséget**követően.

Miután megadta **az ajánlatazonosítóját** és **az Ajánlat aliasát,** válassza a **Létrehozás lehetőséget.** Ezután az ajánlat minden különböző részén dolgozhat.

## <a name="offer-setup"></a>Ajánlat beállítása

Az **Ajánlat beállítási** lapja a következő információkat kéri. A mezők **kitöltése** után válassza a Mentés lehetőséget.

## <a name="connect-lead-management"></a>Csatlakoztassa az érdeklődőkezelést

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Vegye figyelembe, hogy a [felügyelt szolgáltatások hitelesítési házirendjei](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)szerint **érdeklődői cél** szükséges. Ez rekordot hoz létre a CRM-rendszerben minden alkalommal, amikor egy ügyfél telepíti az ajánlatot.

További információt az [Érdeklődőkezelés áttekintése című témakörben talál.](./commercial-marketplace-get-customer-leads.md)

Ne **felejtse** el menteni a lapot, mielőtt továbblépne a következő szakaszra.

## <a name="properties"></a>Tulajdonságok

A **Tulajdonságok** lapon meghatározhatja az ajánlat csoportosításához használt kategóriákat a piactéren, valamint az ajánlatot támogató jogi szerződéseket. A lap befejezése után válassza a **Mentés** gombot.

### <a name="category"></a>Kategória

Válasszon ki legalább egy és legfeljebb öt kategóriát, amelyek segítségével az ajánlatot a megfelelő piactérkeresési területekre helyezi. Ügyeljen arra, hogy hívja ki, hogy az ajánlat támogatja ezeket a kategóriákat az ajánlat leírása.

### <a name="terms-and-conditions"></a>Feltételek és kikötések

Adja meg saját jogi feltételeit az **Általános Szerződési Feltételek** mezőben. Azt az URL-t is megadhatod, ahol a feltételek találhatók. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt megpróbálhatnák az ajánlatát.

## <a name="offer-listing"></a>Ajánlat lista

**Az Ajánlat listaoldal** lehetővé teszi az ajánlat marketplace-részleteinek (ajánlatneve, leírása, képei stb.) meghatározását.

> [!NOTE]
> Az ajánlat tartalma (például leírás, dokumentumok, képernyőképek, használati feltételek stb.) nem szükséges angol nyelven, amennyiben az ajánlat leírása a következő mondattal kezdődik: "Ez az alkalmazás csak [nem angol nyelven] érhető el." Az is elfogadható, hogy *egy hasznos link URL-t* kínál tartalmat más nyelven, mint amelyet az ajánlat lista tartalmát.

### <a name="name"></a>Név

Az itt megadott név az ajánlathirdetés címeként jelenik meg az ügyfelek számára. Ez a mező előre ki van töltve az **Ajánlat aliashoz** az ajánlat létrehozásakor megadott szöveggel, de ezt az értéket módosíthatja. Ez a név védjeggyel is ellátott lehet (és ön védjegy- vagy szerzői jogi szimbólumokat is tartalmazhat). A név nem lehet több 50 karakternél, és nem tartalmazhat hangulatjeleket.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Adja meg az ajánlat rövid leírását (legfeljebb 100 karakter), amely a piactér keresési eredményei között is használható.

### <a name="long-summary"></a>Hosszú összegzés

Adjon meg hosszabb leírást az ajánlatról (legfeljebb 256 karakter). Ez a hosszú összefoglaló a marketplace keresési eredményeiközött is használható.

### <a name="description"></a>Leírás

Adjon meg hosszabb leírást az ajánlatról (legfeljebb 3000 karakter). Ez a leírás jelenik meg az ügyfelek számára a piactéri lista áttekintése. Adja meg az ajánlat értékajánlatát, a legfontosabb előnyöket, a kategória- és/vagy iparági szövetségeket, az alkalmazáson belüli vásárlási lehetőségeket és a szükséges közzétételeket.

Néhány tipp a leírás megírásához:  

- Egyértelműen írja le az ajánlat értékajánlatát a leírás első néhány mondatában. Az értékajánlatban a következő elemek szerepeljenek:
  - Az ajánlat leírása
  - Az ajánlat előnyeit élvező felhasználó típusa
  - Az ajánlat által megszólított ügyféligények vagy fájdalom
- Ne feledje, hogy az első néhány mondat megjelenhet a keresőmotor találatai között.  
- Ne hagyatkozzon a termék értékesítéséhez szükséges funkciókra és funkciókra. Ehelyett összpontosítson a szállít.  
- A lehető legnagyobb mértékben használjon iparág-specifikus szókincset vagy haszonalapú megfogalmazást.

Annak érdekében, hogy ajánlata leírása vonzóbbá legyen, használja a Rich Text szerkesztőt a leírás formázásához.

![A Rich Text szerkesztő használata](./media/text-editor2.png)

A Rich Text szerkesztő használatához kövesse az alábbi utasításokat:

- A tartalom formátumának módosításához jelölje ki a formázni kívánt szöveget, és jelöljön ki egy szövegstílust az alábbi módon:

     ![Szövegformátum módosítása a Rich Text szerkesztővel](./media/text-editor3.png)

- Ha listajeles vagy számozott listát szeretne hozzáadni a szöveghez, használja az alábbi beállításokat:

     ![Listák hozzáadása a Rich Text szerkesztővel](./media/text-editor4.png)

- A szöveg behúzásának hozzáadásához vagy eltávolításához használja az alábbi beállításokat:

     ![A Rich Text szerkesztő behúzása](./media/text-editor5.png)

### <a name="privacy-policy-link"></a>Adatvédelmi irányelv hivatkozás

Adja meg a szervezet adatvédelmi irányelveinek URL-címét (a webhelyen található). Ön felelős annak biztosításáért, hogy alkalmazása megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint hogy érvényes adatvédelmi szabályzatot biztosítson.

### <a name="useful-links"></a>Hasznos hivatkozások

Adjon meg opcionális kiegészítő online dokumentumokat a megoldásról. További hasznos hivatkozások hozzáadása a **+ Hivatkozás hozzáadása gombra**kattintva .

### <a name="contact-information"></a>Kapcsolattartási adatok

Ebben a szakaszban meg kell adnia egy **támogatási partner** és egy mérnöki partner nevét, e-mail címét és **telefonszámát.** Ez az információ nem jelenik meg az ügyfelek számára, de elérhető lesz a Microsoft számára, és a kripta-partnerek rendelkezésére állhat.

### <a name="support-urls"></a>Támogatási URL-ek

Ha az Azure **Global customers** és/vagy az **Azure Government-ügyfelek**támogatási webhelyeivel rendelkezik, adja meg ezeket az URL-címeket itt.

### <a name="marketplace-images"></a>Marketplace-képek

Ebben a szakaszban olyan emblémákat és képeket adhat meg, amelyeket az ajánlat ügyfélnek való megjelenítésekor fog használni. Minden képnek .png formátumúnak kell lennie.

#### <a name="marketplace-logos"></a>Marketplace-emblémák

Négy emblémaméret szükséges: **Kicsi (40x40)**, **Közepes (90x90)**, **Nagy (115x115)** és **Széles (255x115)**. Kövesse az emblémákra vonatkozó alábbi irányelveket:

- Az Azure arculata egyszerű színpalettát használ. Törekedjen minél kevesebb alap- és másodlagos szín használatára az emblémában.
- A portál témaszínei a fehér és a fekete. Ne használja ezeket a színeket az embléma háttérszíneként. Olyan színt használjon, amelynek hatására az embléma felkelti a figyelmet. Javasoljuk az egyszerű alapszínek használatát.
- Ha átlátszó hátteret használ, az embléma és a szöveg ne legyen fehér, fekete vagy kék.
- Az emblémának kétdimenziósnak kell érződnie, és a színátmenetek is kerülendők. Ne használjon színátmenetes hátteret az emblémában.
- Ne helyezzen el szöveget az emblémára, még a vállalat vagy a márka nevét se.
- Ügyeljen arra, hogy az embléma ne legyen széthúzva.

#### <a name="screenshots"></a>Képernyőképek

Adjon hozzá legfeljebb öt képernyőképet, amelyek megmutatják, hogyan működik az ajánlata. Minden képernyőképnek 1280 x 720 képpontnak kell lennie.

#### <a name="videos"></a>Videók

Az ajánlatot jól látható anamnézisben legfeljebb öt videót adhatsz hozzá. Ezeket a videókat a YouTube-on és/vagy a Vimeo-n kell üzemeltetni. Mindegyikhez adja meg a videó nevét, URL-címét és a videó miniatűr képét (1280 x 720 képpont).

#### <a name="additional-marketplace-listing-resources"></a>További piactér-listaforrások

- [Gyakorlati tanácsok a piactéri ajánlatlistákhoz](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Előzetes verzió

Mielőtt élőben közzétenné az ajánlatot a szélesebb piactéri ajánlatban, először elérhetővé kell tennie egy korlátozott előnézeti közönség számára. Ez lehetővé teszi annak megerősítését, hogy az ajánlat hogyan jelenik meg az Azure Marketplace-en, mielőtt elérhetővé teszi az ügyfelek számára. A Microsoft támogatási és mérnöki csapatai az előzetes verzióban is megtekinthetik az ajánlatot.

Az előzetes verzióközönséget úgy határozhatja meg, hogy beírja az Azure-előfizetés-azonosítókat a **Közönség előnézete** szakaszban. Legfeljebb 10 előfizetési azonosítót adhat meg manuálisan, vagy feltölthet egy .csv fájlt legfeljebb 100 előfizetési azonosítóval.

Az előfizetésekhez társított ügyfelek megtekinthetik az ajánlatot az Azure Marketplace-en, mielőtt az élesben lépne. Ügyeljen arra, hogy a saját előfizetéseit itt adja meg, hogy megtekinthesse az ajánlatát.

## <a name="plan-overview"></a>Terv – áttekintés

Minden ajánlatnak rendelkeznie kell egy vagy több csomaggal (más néven ska-k). Több csomagot is hozzáadhat a különböző szolgáltatáskészletek különböző árakon való támogatásához, vagy egy adott terv testreszabásához adott ügyfelek korlátozott közönsége számára. Az ügyfelek megtekinthetik a szülőajánlat alatt elérhető csomagokat.

A **Terv áttekintése** lapon válassza a **+ Új terv létrehozása**lehetőséget. Ezután adja meg **a tervazonosítót** és a **terv nevét.** Mindkét érték csak kisalfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, legfeljebb 50 karakterből. Ezek az értékek láthatóak lehetnek az ügyfelek számára, és az ajánlat közzététele után nem módosíthatók.

Ha beírta ezeket az értékeket, válassza a **Létrehozás** gombot, ha tovább szeretne dolgozni a terven. Három szakaszt kell kitölteni: **A terv listázása**, **az árak és a rendelkezésre állás**, valamint a Műszaki **konfiguráció**.

### <a name="plan-listing"></a>A terv listája

Először adja meg a **terv keresési eredmények összegzését.** Ez egy rövid leírást a terv (legfeljebb 100 karakter), amely felhasználható a marketplace keresési eredmények.

Ezután adjon meg egy **leírást,** amely részletesebb magyarázatot ad a tervről.

### <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

Jelenleg csak egy díjszabási modell használható a felügyelt szolgáltatásajánlathoz: **Saját licenc (BYOL) magával hozása.** Ez azt jelenti, hogy közvetlenül az ügyfeleknek kell fizetnie az ajánlattal kapcsolatos költségekért, és a Microsoft nem számít fel önnek semmilyen díjat.

A **Terv láthatósága** szakasz ban jelezheti, hogy a tervnek [magánjellegűnek](../../marketplace/private-offers.md)kell-e lennie. Ha bejelölve hagyja a **Privát csomag** mezőt, a csomag nem korlátozódik bizonyos ügyfelekre (vagy bizonyos számú ügyfélre).

Ha azt szeretné, hogy a csomag csak bizonyos vevők számára legyen elérhető, válassza az **Igen**lehetőséget. Ha így tesz, az előfizetési azonosítók megadásával azonosítania kell az ügyfeleket. Ezeket egyenként meglehet adni (legfeljebb 10 előfizetés esetén), vagy feltölthet egy .csv fájlt (legfeljebb 10 000 előfizetést az összes csomagban). Ügyeljen arra, hogy a saját előfizetések itt, így tesztelheti és érvényesítheti az ajánlatot.

> [!IMPORTANT]
> Ha egy tervet nyilvánosként tettek közzé, nem módosíthatja magánjellegűvé. Annak szabályozásához, hogy mely ügyfelek fogadhatják el az ajánlatot, és milyen erőforrásokat delegálhatnak, használjon privát csomagot. Nyilvános csomag esetén nem korlátozhatja a rendelkezésre állást bizonyos ügyfelekre, vagy akár bizonyos számú ügyfélre (bár teljesen leállíthatja a csomag értékesítését, ha úgy dönt). A [delegáláshoz való hozzáférést](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation) csak akkor távolíthatja el, ha az ügyfél csak akkor fogad el egy ajánlatot, ha az ajánlat közzétételekor a [Felügyelt szolgáltatások regisztrációs hozzárendelésének törlése szerepkör-törlési](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) **szerepkör-kóddal** rendelkező **engedélyezést** is tartalmaz. Azt is elérheti az ügyfél, és kérje meg őket, hogy [távolítsa el a hozzáférést.](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)

### <a name="technical-configuration"></a>Műszaki konfiguráció

A terv ezen szakasza létrehoz egy jegyzékfájlt az ügyfél-erőforrások kezeléséhez szükséges engedélyezési adatokkal. Erre az információra az [Azure delegált erőforrás-kezelésének](../../lighthouse/concepts/azure-delegated-resource-management.md)engedélyezéséhez van szükség.

Győződjön meg arról, hogy tekintse át [a bérlők, szerepkörök és a felhasználók az Azure Lighthouse forgatókönyvek, hogy megértsék,](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) mely szerepkörök támogatottak, és az engedélyek meghatározásához ajánlott eljárásokat.

> [!NOTE]
> Amint azt fentebb említettük, az **engedélyezési** bejegyzésekben szereplő felhasználók és szerepkörök minden olyan ügyfélre vonatkoznak, aki megvásárolja a csomagot. Ha korlátozni szeretné a hozzáférést egy adott ügyfélszámára, közzé kell tennie egy privát csomagot kizárólagos használatra.

#### <a name="manifest"></a>Jegyzék

Először adjon meg egy **verziót** a jegyzékfájlhoz. Használja az *n.n.n* formátumot (például 1.2.5. formátum).

Ezután adja meg a **bérlőazonosítóját.** Ez a szervezet Azure Active Directory (Azure AD) bérlői azonosítójához társított GUID; ez azt jelenti, hogy az a kezelő bérlő, amelyből hozzáférhet az ügyfelek erőforrásaihoz. Ha nem rendelkezik ezzel a praktikus, megtalálhatja azt, ha az Azure Portal jobb felső részén a fiókneve fölé viszi az egérmutatót, vagy a **Címtár váltása**lehetőséget választja.

Ha közzéteszi az ajánlat új verzióját, és frissített jegyzékfájlt kell létrehoznia, válassza a **+ Új jegyzékfájl**lehetőséget. Ügyeljen arra, hogy növelje a verziószámot az előző jegyzékverzióból.

#### <a name="authorization"></a>Engedélyezés

Az engedélyek határozzák meg a kezelő bérlő entitásait, akik hozzáférhetnek a csomagot megvásárló ügyfelek erőforrásaihoz és előfizetéseihez. Ezen entitások mindegyike kap egy beépített szerepkört, amely meghatározott szintű hozzáférést biztosít.

Minden tervhez legfeljebb húsz engedélyt hozhat létre.

> [!TIP]
> A legtöbb esetben érdemes szerepköröket rendelni egy Azure AD felhasználói csoporthoz vagy egyszerű szolgáltatáshoz, nem pedig egyéni felhasználói fiókok sorozatához. Ez lehetővé teszi, hogy az egyes felhasználók hozzáférését anélkül adja hozzá vagy távolítsa el, hogy a hozzáférési követelmények változásakor frissítenie kellene és újra közzé kellene tennie a tervet. Amikor szerepköröket rendel az Azure AD-csoportokhoz, [győződjön meg arról, hogy a **csoport típusa** **biztonság,** és nem **az Office 365.**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) További javaslatokért lásd: [Bérlők, szerepkörök és felhasználók az Azure Világítótorony forgatókönyvekben.](../../lighthouse/concepts/tenants-users-roles.md)

Minden **engedélyezéshez**a következőket kell megadnia. Ezután kiválaszthatja **a + Engedélyezés hozzáadása** annyiszor, ahányszor szükséges további felhasználók és szerepkör-definíciók hozzáadásához.

- **Azure AD-objektum azonosító:** Egy felhasználó, felhasználói csoport vagy alkalmazás Azure AD-azonosítója, amely bizonyos engedélyeket kap (a szerepkör-definíció szerint) az ügyfelek erőforrásaihoz.
- **Azure AD objektum megjelenítendő neve:** Rövid név, amely segít az ügyfélnek megérteni az engedélyezés célját. Az erőforrás-delegáláskor a vevő ezt a nevet fogja látni.
- **Szerepkör-definíció:** Válasszon ki egyet a rendelkezésre álló Azure AD beépített szerepkörök a listából. Ez a szerepkör határozza meg az **Azure AD Objektumazonosító** mezőben szereplő felhasználó által az ügyfelek erőforrásaira vonatkozó engedélyekkel. A szerepkörök leírását lásd: [Beépített szerepkörök](../../role-based-access-control/built-in-roles.md) és [szerepkör-támogatás az Azure delegált erőforrás-kezeléséhez.](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)
  > [!NOTE]
  > A megfelelő új beépített szerepkörök hozzáadása az Azure-ban, akkor elérhetővé válnak itt, bár előfordulhat, hogy némi késéssel, mielőtt azok megjelennek.
- **Hozzárendelhető szerepkörök:** Ez a beállítás csak akkor jelenik meg, ha az **engedélyezés szerepkör-definíciójában** a Felhasználói hozzáférés rendszergazdáját választotta. Ha igen, itt egy vagy több hozzárendelhető szerepkört kell hozzáadnia. Az Azure **AD Object ID** mezőben lévő felhasználó hozzá rendelheti ezeket a szerepköröket [a felügyelt identitásokhoz,](../../active-directory/managed-identities-azure-resources/overview.md)ami szükséges a [kiigazítható házirendek üzembe helyezéséhez.](../../lighthouse/how-to/deploy-policy-remediation.md) Ne feledje, hogy a felhasználóra a Felhasználói hozzáférés rendszergazda szerepköréhez általában kapcsolódó egyéb engedélyek nem vonatkoznak.

> [!TIP]
> Annak érdekében, hogy szükség esetén el tudja távolítani a [delegáláshoz való hozzáférést,](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation) adjon meg egy **engedélyezést** a **Felügyelt** [szolgáltatások regisztrációs hozzárendelésének törlése szerepkör](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)szerepkör-kódja beállítással. Ha ez a szerepkör nincs hozzárendelve, a delegált erőforrásokat csak az ügyfél bérlőjében lévő felhasználó távolíthatja el.

Miután elvégezte a csomag összes szakaszát, a **+ Új terv létrehozása** lehetőséget annyiszor választhatja ki, ahányszor további terveket szeretne létrehozni. Amikor elkészült, válassza a **Mentés** lehetőséget.

## <a name="publish"></a>Közzététel

### <a name="submit-offer-to-preview"></a>Ajánlat küldése előnézetbe

Miután elvégezte az ajánlat összes szükséges szakaszát, válassza a **közzététel** lehetőséget a portál jobb felső sarkában. A bizottság átirányítja a **Véleményezés és közzététel** lapra.

Ha ez az első alkalom, hogy közzéteszi ezt az ajánlatot, a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak készültségi állapotát.
  - *Nincs elindítva* - azt jelenti, hogy a szakaszt nem érintette meg, és be kell fejezni.
  - *Hiányos* - azt jelenti, hogy a szakasz hibákat, amelyeket ki kell javítani, vagy több információt kell megadni. Menjen vissza a szakasz(ok)hoz, és frissítse azt.
  - *Kész* - azt jelenti, hogy a szakasz befejeződött, az összes szükséges adatot megadta, és nincsenek hibák. Az ajánlat minden szakaszának teljes állapotban kell lennie az ajánlat elküldése előtt.
- A Megjegyzések a **minősítéshez** szakaszban adjon vizsgálati utasításokat a minősítő csapatnak, hogy az alkalmazás megfelelő tesztelése mellett adjon meg minden olyan kiegészítő megjegyzést, amely hasznos az alkalmazás megértéséhez.
- Küldje el az ajánlatot közzétételre a **Küldés**lehetőség kiválasztásával. E-mailt küldünk Önnek, ha az ajánlat előzetes verziója elérhető, hogy áttekintse és jóváhagyja. Térjen vissza a Partnerközpontba, és válassza a **Go-live** lehetőséget az ajánlat nyilvános közzétételéhez (vagy ha privát ajánlat, a privát közönség számára).

### <a name="customer-experience-and-offer-management"></a>Ügyfélélmény és ajánlatkezelés

Amikor egy ügyfél telepíti az ajánlatot, akkor képes lesz delegálni előfizetések vagy erőforráscsoportok az [Azure delegált erőforrás-kezelés.](../../lighthouse/concepts/azure-delegated-resource-management.md) Erről a folyamatról további információkért [lásd: Az ügyfél bevezetési folyamata](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process).

Az [ajánlat frissített verzióját](update-existing-offer.md) bármikor közzéteheti. Előfordulhat például, hogy új szerepkör-definíciót szeretne hozzáadni egy korábban közzétett ajánlathoz. Ha így tesz, az ajánlathoz már hozzáadott ügyfelek egy ikont fognak látni az Azure Portal [**Szolgáltatói**](../../lighthouse/how-to/view-manage-service-providers.md) lapján, amely tudatja velük, hogy elérhető egy frissítés. Minden ügyfél áttekintheti a módosításokat, és eldöntheti, hogy szeretne-e frissíteni az új verzióra.

## <a name="next-steps"></a>További lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
- [További információ az Azure Világítótoronyról](../../lighthouse/overview.md)