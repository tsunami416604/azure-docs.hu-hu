---
title: Felügyelt szolgáltatásajánlat közzététele az Azure Piactéren
description: Ismerje meg, hogyan tehet közzé egy felügyelt szolgáltatásajánlatot, amely az ügyfeleket az Azure delegált erőforrás-kezelésére hozza.
ms.date: 04/08/2020
ms.topic: conceptual
ms.openlocfilehash: 4791b1d2ae233b0cc7aad33dd5b15b6ea94b2018
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984554"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Felügyelt szolgáltatásajánlat közzététele az Azure Piactéren

> [!IMPORTANT]
> 2020. április 14-től megkezdjük a Felügyelt szolgáltatási ajánlatok kezelését a Partnerközpontba. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Az áttelepített ajánlatok kezeléséhez kövesse az [Új felügyelt szolgáltatásajánlatok létrehozása című, az új felügyelt szolgáltatásajánlatok létrehozása](../../marketplace/partner-center-portal/create-new-managed-service-offer.md) című részben található utasításokat.

Ebből a cikkből megtudhatja, hogyan tehet közzé nyilvános vagy privát felügyelt szolgáltatási ajánlatot az [Azure Piactéren](https://azuremarketplace.microsoft.com) a [Cloud Partner Portal](https://cloudpartner.azure.com/)használatával. Az ajánlatot megvásárló ügyfelek ezután előadhatják az [Azure delegált erőforrás-kezeléséhez](../concepts/azure-delegated-resource-management.md)szükséges előfizetéseket és erőforráscsoportokat.

## <a name="publishing-requirements"></a>Közzétételi követelmények

Az ajánlatok létrehozásához és közzétételéhez érvényes fiókkal kell rendelkeznie [a Partnerközpontban.](../../marketplace/partner-center-portal/create-account.md) Ha még nem rendelkezik fiókkal, a [regisztrációs folyamat](https://aka.ms/joinmarketplace) végigvezeti a Partnerközpontban létrehozott és a Kereskedelmi piactér programba való regisztráció lépésein.

A [felügyelt szolgáltatás ajánlatminősítési követelményeinek megfelelően](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements) [Silver vagy Gold Cloud Platform kompetenciaszinttel kell rendelkeznie,](https://docs.microsoft.com/partner-center/learn-about-competencies) vagy Azure Expert [MSP-nek](https://partner.microsoft.com/membership/azure-expert-msp) kell lennie a felügyelt szolgáltatásajánlat közzétételéhez.

A Microsoft Partner Network (MPN) azonosító [automatikusan társítva](../../billing/billing-partner-admin-link-started.md) lesz a közzétett ajánlatokkal, hogy nyomon követhesse az ügyfélkapcsolatokra gyakorolt hatását.

> [!NOTE]
> Ha nem szeretne közzétenni egy ajánlatot az Azure Marketplace-en, manuálisan is bevezetheti az ügyfeleket az Azure Resource Manager-sablonok használatával. További információ: [Az Azure delegált erőforrás-kezelése ügyfél nek.](onboard-customer.md)

A felügyelt szolgáltatások ajánlatának közzététele hasonló az Azure Marketplace-en bármilyen más típusú ajánlat közzétételéhez. Az általános közzétételi folyamatról az Azure Marketplace és az [AppSource közzétételi útmutatócímű témakörben olvashat.](../../marketplace/marketplace-publishers-guide.md) Tekintse át a [kereskedelmi piactér tanúsítási irányelveit](https://docs.microsoft.com/legal/marketplace/certification-policies)is, különösen a [Felügyelt szolgáltatások szakaszt.](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)

Miután egy ügyfél hozzáadja az ajánlatot, delegálhat egy vagy több előfizetést vagy erőforráscsoportot, amelyeket ezután [az Azure delegált erőforrás-kezeléséhez vesznek fel.](#the-customer-onboarding-process)

> [!IMPORTANT]
> A felügyelt szolgáltatásajánlat minden egyes csomagja tartalmaz egy **Manifest Details szakaszt,** ahol meghatározza az Azure Active Directory (Azure AD) entitásokat a bérlőben, amelyek hozzáférhetnek a delegált erőforráscsoportokhoz és/vagy előfizetésekhez az ilyen csomagot megvásárló ügyfelek számára. Fontos tudni, hogy minden csoport (vagy felhasználó vagy egyszerű szolgáltatás) tartalmazza lesz azonos engedélyekkel minden ügyfél, aki megvásárolja a csomagot. Ha különböző csoportokat szeretne hozzárendelni az egyes ügyfelekkel való munkához, külön [privát csomagot](../../marketplace/private-offers.md) kell közzétennie, amely kizárólag az egyes ügyfelek számára elérhető.

## <a name="create-your-offer"></a>Az ajánlat létrehozása

1. Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)
2. A bal oldali navigációs menüben válassza az **Új ajánlat**lehetőséget, majd a **Felügyelt szolgáltatások**lehetőséget.
3. Az ajánlathoz egy **szerkesztőszakasz** jelenik meg, amely négy részből áll: **Ajánlatbeállítások,** **Tervek**, **Piactér**és **Támogatás.** Olvassa el az e szakaszok kitöltésével kapcsolatos útmutatást.

### <a name="enter-offer-settings"></a>Ajánlatbeállításainak megadása

Az **Ajánlat beállításai** csoportban adja meg a következőket:

|Mező  |Leírás  |
|---------|---------|
|**Ajánlat azonosítója**     | Az ajánlat egyedi azonosítója (a közzétevői profilján belül). Ez az azonosító csak kisalfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, legfeljebb 50 karakterből. Ne feledje, hogy az ajánlatazonosító látható lehet az ügyfelek számára olyan helyeken, mint a termék URL-címei és a számlázási jelentések. Miután közzétette az ajánlatot, nem módosíthatja ezt az értéket.        |
|**Közzétevő azonosítója**     | Az ajánlathoz társított közzétevő-azonosító. Ha egynél több közzétevő-azonosítóval rendelkezik, kiválaszthatja azt, amelyet ehhez az ajánlathoz használni kíván.       |
|**Név**     | A név (legfeljebb 50 karakter), amelyet az ügyfelek az Azure Marketplace-en és az Azure Portalon fognak látni. Használjon olyan felismerhető márkanevet, amelyet az ügyfelek megértenek – ha ezt az ajánlatot a saját webhelyén keresztül népszerűsíti, ügyeljen arra, hogy itt pontosan ugyanazt a nevet használja.        |

Ha végzett, válassza a **Mentés gombot.** Most már készen áll a **Tervek** szakaszra való áttérésre.

### <a name="create-plans"></a>Tervek létrehozása

Minden ajánlatnak rendelkeznie kell egy vagy több csomaggal (más néven ska-k). Több csomagot is hozzáadhat a különböző szolgáltatáskészletek különböző árakon való támogatásához, vagy egy adott terv testreszabásához adott ügyfelek korlátozott közönsége számára. Az ügyfelek megtekinthetik a szülőajánlat alatt elérhető csomagokat.

A Tervek csoportban válassza az **Új terv**lehetőséget. Ezután adja meg **a csomagazonosítót**. Ez az azonosító csak kisalfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, legfeljebb 50 karakterből. A csomagazonosító látható lehet az ügyfelek számára olyan helyeken, mint a termék URL-címei és a számlázási jelentések. Miután közzétette az ajánlatot, nem módosíthatja ezt az értéket.

#### <a name="plan-details"></a>Terv részletei

Töltse ki a Következő szakaszokat a **Terv részletei** szakaszban:

|Mező  |Leírás  |
|---------|---------|
|**Cím**     | A megjelenítési terv rövid neve. Legfeljebb 50 karakter hosszú.        |
|**Összefoglalás**     | A cím alatti megjelenítés tervének tömör leírása. Legfeljebb 100 karakter hosszú.        |
|**Leírás**     | Leírás szövege, amely részletesebb magyarázatot ad a tervről.         |
|**Számlázási modell**     | Itt 2 számlázási modell látható, de a **saját licenc szállítása a** felügyelt szolgáltatások ajánlataihoz lehetőséget kell választania. Ez azt jelenti, hogy közvetlenül az ügyfeleknek kell fizetnie az ajánlattal kapcsolatos költségekért, és a Microsoft nem számít fel önnek semmilyen díjat.   |
|**Ez egy privát terv?**     | Azt jelzi, hogy a termékváltozat magán- vagy nyilvános. Az alapértelmezett érték a **Nem** (nyilvános). Ha elhagyja ezt a választást, a csomag nem korlátozódik bizonyos ügyfelekre (vagy bizonyos számú ügyfélre); nyilvános csomag közzététele után később nem módosíthatja magánjellegűre. Ha azt szeretné, hogy a csomag csak bizonyos vevők számára legyen elérhető, válassza az **Igen**lehetőséget. Ha így tesz, az előfizetési azonosítók megadásával azonosítania kell az ügyfeleket. Ezeket egyenként is meglehet adni (legfeljebb 10 előfizetés esetén), vagy feltölthet egy .csv fájlt (legfeljebb 20 000 előfizetésesetén). Ügyeljen arra, hogy a saját előfizetések itt, így tesztelheti és érvényesítheti az ajánlatot. További információ: [Privát ska-k és csomagok](../../marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  |

> [!IMPORTANT]
> Ha egy tervet nyilvánosként tettek közzé, nem módosíthatja magánjellegűvé. Annak szabályozásához, hogy mely ügyfelek fogadhatják el az ajánlatot, és milyen erőforrásokat delegálhatnak, használjon privát csomagot. Nyilvános csomag esetén nem korlátozhatja a rendelkezésre állást bizonyos ügyfelekre, vagy akár bizonyos számú ügyfélre (bár teljesen leállíthatja a csomag értékesítését, ha úgy dönt). A [delegáláshoz való hozzáférést](onboard-customer.md#remove-access-to-a-delegation) csak akkor távolíthatja el, ha az ügyfél csak akkor fogad el egy ajánlatot, ha az ajánlat közzétételekor a [Felügyelt szolgáltatások regisztrációs hozzárendelésének törlése szerepkör-törlési](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) **szerepkör-kóddal** rendelkező **engedélyezést** is tartalmaz. Azt is elérheti az ügyfél, és kérje meg őket, hogy [távolítsa el a hozzáférést.](view-manage-service-providers.md#add-or-remove-service-provider-offers)

#### <a name="manifest-details"></a>Jegyzékrészletek

Töltse ki a **csomag jegyzékfájl részletei** szakaszát. Ez létrehoz egy jegyzékfájlt az ügyfél-erőforrások kezeléséhez szükséges engedélyezési adatokkal. Ez az információ szükséges az Azure delegált erőforrás-kezelésének engedélyezéséhez.

> [!NOTE]
> Amint azt fentebb említettük, az **engedélyezési** bejegyzésekben szereplő felhasználók és szerepkörök minden olyan ügyfélre vonatkoznak, aki megvásárolja a csomagot. Ha korlátozni szeretné a hozzáférést egy adott ügyfélszámára, közzé kell tennie egy privát csomagot kizárólagos használatra.

Először adjon meg egy **verziót** a jegyzékfájlhoz. Használja az *n.n.n* formátumot (például 1.2.5. formátum).

Ezután adja meg a **bérlőazonosítóját.** Ez a szervezet Azure Active Directory-bérlői azonosítójához társított GUID (azaz az a bérlő, amelyen az ügyfelek erőforrásainak kezelésén fog dolgozni). Ha nem rendelkezik ezzel a praktikus, megtalálhatja azt, ha az Azure Portal jobb felső részén a fiókneve fölé viszi az egérmutatót, vagy a **Címtár váltása**lehetőséget választja.

Végül adjon hozzá egy vagy több **engedélyezési** bejegyzést a tervhez. Az engedélyek határozzák meg azokat az entitásokat, amelyek hozzáférhetnek a csomagot megvásárló ügyfelek erőforrásaihoz és előfizetéseihez, és olyan szerepköröket rendelhetnek hozzá, amelyek meghatározott szintű hozzáférést biztosítanak.

> [!TIP]
> A legtöbb esetben érdemes engedélyeket rendelni egy Azure AD felhasználói csoporthoz vagy egyszerű szolgáltatáshoz, nem pedig egyéni felhasználói fiókok sorozatához. Ez lehetővé teszi, hogy az egyes felhasználók hozzáférését anélkül adja hozzá vagy távolítsa el, hogy a hozzáférési követelmények változásakor frissítenie kellene és újra közzé kellene tennie a tervet. További javaslatokért lásd: [Bérlők, szerepkörök és felhasználók az Azure Világítótorony forgatókönyvekben.](../concepts/tenants-users-roles.md)

Minden **engedélyezéshez**a következőket kell megadnia. Ezután kiválaszthatja **az Új engedélyezést** annyiszor, ahányszor csak szükséges további felhasználók és szerepkör-definíciók hozzáadásához.

- **Azure AD-objektum azonosító:** Egy felhasználó, felhasználói csoport vagy alkalmazás Azure AD-azonosítója, amely bizonyos engedélyeket kap (a szerepkör-definíció ban leírtak szerint) az ügyfelek erőforrásait.
- **Azure AD objektum megjelenítendő neve:** Rövid név, amely segít az ügyfélnek megérteni az engedélyezés célját. Az erőforrás-delegáláskor a vevő ezt a nevet fogja látni.
- **Szerepkör-definíció:** Válasszon ki egyet a rendelkezésre álló Azure AD beépített szerepkörök a listából. Ez a szerepkör határozza meg az **Azure AD Objektumazonosító** mezőben szereplő felhasználó által az ügyfelek erőforrásaira vonatkozó engedélyekkel. A szerepkörök leírását lásd: [Beépített szerepkörök](../../role-based-access-control/built-in-roles.md) és [szerepkör-támogatás az Azure delegált erőforrás-kezeléséhez.](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)
  > [!NOTE]
  > A megfelelő új beépített szerepkörök hozzáadása az Azure-ban, akkor elérhetővé válnak itt, bár előfordulhat, hogy némi késéssel, mielőtt azok megjelennek.
- **Hozzárendelhető szerepkörök:** Erre csak akkor van szükség, ha az **engedélyezés szerepkör-definíciójában** a Felhasználói hozzáférés rendszergazdáját választotta. Ha igen, itt egy vagy több hozzárendelhető szerepkört kell hozzáadnia. Az Azure **AD Objektumazonosító** mezőben lévő felhasználó hozzárendelheti ezeket **a hozzárendelhető szerepköröket** [a felügyelt identitásokhoz,](../../active-directory/managed-identities-azure-resources/overview.md)amely a [kiigazítható házirendek telepítéséhez](deploy-policy-remediation.md)szükséges. Ne feledje, hogy a felhasználóra a Felhasználói hozzáférés rendszergazda szerepköréhez általában kapcsolódó egyéb engedélyek nem vonatkoznak. Ha itt nem választ ki egy vagy több szerepkört, a beküldés nem felel meg a minősítésnek. (Ha nem a Felhasználóhozzáférés-felügyelőt választotta ki a felhasználó szerepkör-definíciójához, akkor ennek a mezőnek nincs hatása.)

> [!TIP]
> Annak érdekében, hogy szükség esetén el tudja távolítani a [delegáláshoz való hozzáférést,](onboard-customer.md#remove-access-to-a-delegation) adjon meg egy **engedélyezést** a **Felügyelt** [szolgáltatások regisztrációs hozzárendelésének törlése szerepkör](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)szerepkör-kódja beállítással. Ha ez a szerepkör nincs hozzárendelve, a delegált erőforrásokat csak az ügyfél bérlőjében lévő felhasználó távolíthatja el.

Miután elvégezte az adatokat, kiválaszthatja az **Új terv** lehetőséget, ahányszor további terveket kell létrehoznia. Ha elkészült, válassza a **Mentés**lehetőséget, majd folytassa a **Piactér** szakaszt.

### <a name="provide-marketplace-text-and-images"></a>Marketplace-szöveg és képek biztosítása

A **Piactér** szakasz, ahol adja meg a szöveget és a képeket, amelyeket az ügyfelek az Azure Marketplace-en és az Azure Portalon.

Töltse ki az Áttekintés szakasz következő **mezőit:**

|Mező  |Leírás  |
|---------|---------|
|**Cím**     |  Az ajánlat címe, gyakran a hosszú, hivatalos név. Ez a cím jól láthatóan jelenik meg a piacon. Legfeljebb 50 karakter hosszú. A legtöbb esetben ennek meg kell egyeznie az **Ajánlatbeállítások** szakaszban megadott **névvel.**       |
|**Összefoglalás**     | Ajánlatának rövid célja vagy funkciója. Ez általában a cím alatt jelenik meg. Legfeljebb 100 karakter hosszú.        |
|**Hosszú összegzés**     | Az ajánlat céljának vagy funkciójának hosszabb összefoglalása. Legfeljebb 256 karakter hosszú.        |
|**Leírás**     | További információ az ajánlatról. Ez a mező maximális hossza 3000 karakter, és támogatja az egyszerű HTML-formázást. A leírásban valahol fel kell tüntetnie a "felügyelt szolgáltatás" vagy a "felügyelt szolgáltatások" szavakat.       |
|**Marketing azonosító**     | Egyedi URL-barát azonosító. Ez az azonosító csak kisalfanumerikus karaktereket és kötőjeleket tartalmazhat. Ebben az ajánlatban a Marketplace URL-címeiben lesz használva. Ha például a közzétevő azonosítója *contoso,* és a marketingazonosítója *sampleApp,* *https://azuremarketplace.microsoft.com/marketplace/apps/contoso-sampleApp*az Azure Marketplace-en az ajánlat URL-címe lesz.        |
|**Előfizetés-azonosítók előzetes verzió**     | Adjon hozzá egy-100 előfizetés-azonosítót. Az előfizetésekhez társított ügyfelek megtekinthetik az ajánlatot az Azure Marketplace-en, mielőtt az élesben lépne. Azt javasoljuk, hogy a saját előfizetéseit is ide, így megtekintheti, hogyan jelenik meg az ajánlat az Azure Marketplace-en, mielőtt elérhetővé teszi az ügyfelek számára.  (A Microsoft támogatási és mérnöki csapatai az előzetes verzióban is megtekinthetik az ajánlatot.)   |
|**Hasznos linkek**     | Az ajánlatához kapcsolódó URL-címek, például dokumentáció, kibocsátási megjegyzések, GYIK stb.        |
|**Javasolt kategóriák (max. 5)**     | Egy vagy több kategória (legfeljebb öt) vonatkozik az ajánlatra. Ezek a kategóriák segítenek az ügyfeleknek az ajánlat felfedezésében az Azure Marketplace-en és az Azure Portalon.        |

A **Marketingösszetevők** szakaszban feltölthetem az ajánlattal együtt megjelenő emblémákat és egyéb eszközöket. Lehetőség szerint képernyőképeket vagy olyan videókra mutató hivatkozásokat tölthet fel, amelyek segítségével az ügyfelek megérthetik az ajánlatát.

Négy emblémaméret szükséges: **Kicsi (40x40)**, **Közepes (90x90)**, **Nagy (115x115)** és **Széles (255x115)**. Kövesse az emblémákra vonatkozó alábbi irányelveket:

- Az Azure arculata egyszerű színpalettát használ. Törekedjen minél kevesebb alap- és másodlagos szín használatára az emblémában.
- A portál témaszínei a fehér és a fekete. Ne használja ezeket a színeket az embléma háttérszíneként. Olyan színt használjon, amelynek hatására az embléma felkelti a figyelmet. Javasoljuk az egyszerű alapszínek használatát.
- Ha átlátszó hátteret használ, az embléma és a szöveg ne legyen fehér, fekete vagy kék.
- Az emblémának kétdimenziósnak kell érződnie, és a színátmenetek is kerülendők. Ne használjon színátmenetes hátteret az emblémában.
- Ne helyezzen el szöveget az emblémára, még a vállalat vagy a márka nevét se.
- Ügyeljen arra, hogy az embléma ne legyen széthúzva.

A **Hero (815x290)** embléma nem kötelező, de ajánlott. Ha hősemblémát ad meg, kövesse az alábbi irányelveket:

- Ne írjon szöveget a hős embléma, és győződjön meg róla, hogy hagyja 415 képpont üres helyet a jobb oldalon a logó. Erre azért van szükség, hogy helyet hagyjon a programozottan beágyazandó szövegelemeknek: a közzétevő megjelenítendő neve, tervcíme, hosszú összegzése.
- A hősem emblémájának háttere nem lehet fekete, fehér vagy átlátszó. Győződjön meg arról, hogy a háttérszín nem túl világos, mert a beágyazott szöveg fehér színnel jelenik meg.
- Miután közzétette az ajánlatot egy hős ikonnal, nem tudja eltávolítani (bár szükség esetén más verzióval frissítheti).

Az **Érdeklődőkezelés** szakaszban kiválaszthatja azt a CRM-rendszert, amelyben az érdeklődőket tárolni fogja. Vegye figyelembe, hogy a [felügyelt szolgáltatások hitelesítési házirendjei](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)szerint **érdeklődői cél** szükséges.

Végül adja meg **adatvédelmi irányelveiurl-címét** és **használati feltételeit** a **Jogi** szakaszban. Itt azt is megadhatja, hogy használja-e a [standard szerződést](../../marketplace/standard-contract.md) ehhez az ajánlathoz.

Mielőtt továbblépne a **Támogatás** szakaszra, mentse a módosításokat.

### <a name="add-support-info"></a>Támogatási adatok hozzáadása

A **Támogatás** szakaszban adja meg egy mérnöki kapcsolattartó és egy ügyfélszolgálati kapcsolattartó nevét, e-mail címét és telefonszámát. Támogatási URL-eket is biztosítania kell. A Microsoft felhasználhatja ezeket az adatokat, amikor üzleti és támogatási kérdésekkel kapcsolatban kapcsolatba kell lépnünk Önnel.

Miután hozzáadta ezt az információt, válassza a **Mentés lehetőséget.**

## <a name="publish-your-offer"></a>Az ajánlat közzététele

Miután elvégezte az összes szakaszt, a következő lépés az ajánlat közzététele az Azure Marketplace-en. Válassza a **Közzététel** gombot az ajánlat élő közvetítésének kezdeményezéséhez. Erről a folyamatról az Azure Marketplace és az [AppSource-ajánlatok közzététele.](../../marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer.md)

Az [ajánlat frissített verzióját](../../marketplace/cloud-partner-portal/manage-offers/cpp-update-offer.md) bármikor közzéteheti. Előfordulhat például, hogy új szerepkör-definíciót szeretne hozzáadni egy korábban közzétett ajánlathoz. Ha így tesz, az ajánlathoz már hozzáadott ügyfelek egy ikont fognak látni az Azure Portal [**Szolgáltatói**](view-manage-service-providers.md) lapján, amely tudatja velük, hogy elérhető egy frissítés. Minden ügyfél [áttekintheti a módosításokat,](view-manage-service-providers.md#update-service-provider-offers) és eldöntheti, hogy szeretne-e frissíteni az új verzióra. 

## <a name="the-customer-onboarding-process"></a>Az ügyfél bevezetési folyamata

Miután egy ügyfél hozzáadja az ajánlatot, egy [vagy több konkrét előfizetést vagy erőforráscsoportot delegálhat,](view-manage-service-providers.md#delegate-resources)amelyeket ezután az Azure delegált erőforrás-kezeléséhez beszerveznek. Ha egy ügyfél elfogadott egy ajánlatot, de még nem delegált erőforrásokat, akkor megjelenik egy megjegyzés a **szolgáltató ajánlatok** szakaszának tetején a [**Szolgáltatók**](view-manage-service-providers.md) lap az Azure Portalon.

> [!IMPORTANT]
> A delegálást az ügyfél bérlőjében lévő nem vendégfióknak kell elvégeznie, amely rendelkezik a [tulajdonos beépített szerepkörrel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) (vagy amely a fedélzeti erőforráscsoportokat tartalmazza). Az előfizetésdelegálható összes felhasználó megtekintéséhez az ügyfél bérlőjének egyik felhasználója kiválaszthatja az előfizetést az Azure Portalon, megnyithatja a **hozzáférés-vezérlést (IAM)** és [megtekintheti a Tulajdonos szerepkörrel rendelkező összes felhasználót.](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)

Miután az ügyfél delegál egy előfizetést (vagy egy előfizetésen belül egy vagy több erőforráscsoportot), a **Microsoft.ManagedServices** erőforrás-szolgáltató regisztrálva lesz az adott előfizetéshez, és a bérlő felhasználói hozzáférhetnek a delegált erőforrásokhoz az ajánlatban szereplő engedélyek nek megfelelően.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [kereskedelmi piactérről.](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)
- További információ a [bérlők közötti felügyeleti élményekről.](../concepts/cross-tenant-management-experience.md)
- Az Azure Portalon az **Ügyfeleimmel** tekintheti meg és kezelheti az [ügyfeleket.](view-manage-customers.md)
