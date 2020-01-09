---
title: Felügyelt szolgáltatások ajánlatának közzététele az Azure Marketplace-en
description: Megtudhatja, hogyan tehet közzé egy felügyelt szolgáltatási ajánlatot, amely az ügyfeleket az Azure-beli delegált erőforrás-kezeléshez irányítja.
ms.date: 12/16/2019
ms.topic: conceptual
ms.openlocfilehash: d1eb06794551be498e05e2b9c3b893013b718ce9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453537"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Felügyelt szolgáltatások ajánlatának közzététele az Azure Marketplace-en

Ebből a cikkből megtudhatja, hogyan tehet közzé egy nyilvános vagy privát felügyelt szolgáltatást az [Azure Marketplace](https://azuremarketplace.microsoft.com) -en a [Cloud Partner Portal](https://cloudpartner.azure.com/)használatával, amely lehetővé teszi, hogy egy ügyfél az Azure-beli delegált erőforrás-kezeléshez bevezetésre felkínálja az ajánlatot.

> [!NOTE]
> Az ajánlatok létrehozásához és közzétételéhez érvényes fiókkal kell rendelkeznie a [partner Centerben](../../marketplace/partner-center-portal/create-account.md) . Ha még nem rendelkezik fiókkal, a [regisztrációs folyamat](https://aka.ms/joinmarketplace) végigvezeti a fiók létrehozásának lépésein a partner Centerben és a kereskedelmi piactér programban való regisztrálásban. A [rendszer automatikusan hozzárendeli](../../billing/billing-partner-admin-link-started.md) az Ön Microsoft Partner Network (MPN) azonosítóját a közzétett ajánlatokhoz, és nyomon követheti az ügyfelek bevonásának következményeit.
>
> Ha nem szeretne ajánlatot közzétenni az Azure Marketplace-en, Azure Resource Manager-sablonok használatával manuálisan is bevezetheti az ügyfeleket. További információ: az ügyfél beléptetése [Az Azure által delegált erőforrás-felügyeletbe](onboard-customer.md).

A felügyelt szolgáltatások ajánlatának közzététele hasonló az Azure Marketplace-en bármilyen más típusú ajánlat közzétételéhez. Ennek a folyamatnak a megismeréséhez tekintse meg az [Azure Marketplace és a AppSource közzétételi útmutatóját](../../marketplace/marketplace-publishers-guide.md) , és [kezelje az Azure és a AppSource Marketplace ajánlatait](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md). Tekintse át a [kereskedelmi piactérre vonatkozó minősítési szabályzatokat](https://docs.microsoft.com/legal/marketplace/certification-policies), különösen a [felügyelt szolgáltatások](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) szakaszt.

Ha egy ügyfél felveszi az ajánlatot, egy vagy több konkrét előfizetést vagy erőforráscsoportot delegálhat, amelyek ezután bekerülnek [Az Azure-beli delegált erőforrás-kezelésbe](#the-customer-onboarding-process). Vegye figyelembe, hogy az előfizetés (vagy az előfizetésen belüli erőforráscsoportok) bevezetése előtt a **Microsoft. ManagedServices** erőforrás-szolgáltató manuális regisztrálásával jóvá kell hagynia az előfizetést a bevezetéshez.

> [!IMPORTANT]
> A felügyelt szolgáltatások ajánlatának minden csomagja tartalmaz egy **jegyzékfájl részleteit** tartalmazó szakaszt, ahol megadhatja a bérlő azon Azure Active Directory (Azure ad) entitásait, amelyek hozzáférnek a megvásárolt erőforrásokhoz és/vagy előfizetésekhez a tervet megvásárló ügyfelek számára. Fontos megjegyezni, hogy az itt foglalt bármely csoport (vagy felhasználó vagy szolgáltatásnév) ugyanazokkal az engedélyekkel fog rendelkezni minden olyan ügyfélnél, aki megvásárolja a csomagot. Ha különböző csoportokat szeretne hozzárendelni az egyes ügyfelekkel való munkához, egy külön privát csomagot kell közzétennie, amely kizárólag az egyes ügyfelekre vonatkozik.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Ajánlat létrehozása a Cloud Partner Portalban

1. Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).
2. A bal oldali navigációs menüben válassza az **új ajánlat**lehetőséget, majd válassza a **felügyelt szolgáltatások**elemet.
3. Az ajánlathoz négy részből megtekinthető egy **szerkesztő** szakasz, amely a következőt adja meg: **Beállítások**, **csomagok**, **piactér**és **támogatás**. A fejezetek végrehajtásával kapcsolatos útmutatásért olvassa el a következőt:.

## <a name="enter-offer-settings"></a>Adja meg az ajánlat beállításait

Az **ajánlat beállításai** szakaszban adja meg a következőket:

|Mező  |Leírás  |
|---------|---------|
|**Ajánlat azonosítója**     | Az ajánlat egyedi azonosítója (a közzétevői profilján belül). Ez az azonosító csak kisbetűs alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhat, legfeljebb 50 karakter hosszú lehet. Ne feledje, hogy az ajánlat azonosítója látható az ügyfelek számára olyan helyeken, mint például a termék URL-címei és a számlázási jelentések. Az ajánlat közzététele után nem módosíthatja ezt az értéket.        |
|**Közzétevő azonosítója**     | Az ajánlattal társított közzétevő azonosítója. Ha egynél több közzétevő-AZONOSÍTÓval rendelkezik, kiválaszthatja az ajánlathoz használni kívánt elemet.       |
|**Name (Név)**     | Az ügyfelek számára az Azure Marketplace-en és a Azure Portalban megjelenő név (legfeljebb 50 karakter). Olyan felismerhető márkanév használata, amelyet az ügyfelek megértettek – ha Ön a saját webhelyén keresztül népszerűsíti ezt az ajánlatot, ügyeljen arra, hogy itt pontosan ugyanazt a nevet használja.        |

Ha elkészült, válassza a **Mentés**lehetőséget. Most már készen áll a **csomagok** szakaszra való áttérésre.

## <a name="create-plans"></a>Csomagok létrehozása

Minden ajánlatnak egy vagy több csomaggal (más néven SKU) kell rendelkeznie. Több csomagot is hozzáadhat, hogy támogassa a különböző szolgáltatáskészlet-készleteket különböző árakon, vagy hogy testre szabjon egy adott tervet az egyes ügyfelek korlátozott célközönsége számára. Az ügyfelek megtekinthetik a fölérendelt ajánlat keretében elérhető csomagokat.

A csomagok szakaszban válassza az **új csomag**lehetőséget. Ezután adja meg a **csomag azonosítóját**. Ez az azonosító csak kisbetűs alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhat, legfeljebb 50 karakter hosszú lehet. Előfordulhat, hogy a csomag azonosítója látható az ügyfelek számára olyan helyeken, mint például a termék URL-címei és a számlázási jelentések. Az ajánlat közzététele után nem módosíthatja ezt az értéket.

### <a name="plan-details"></a>Csomag részletei

Hajtsa végre a következő szakaszokat a **csomag részletei** szakaszban:

|Mező  |Leírás  |
|---------|---------|
|**Cím**     | A megjelenő terv felhasználóbarát neve. Legfeljebb 50 karakter hosszú lehet.        |
|**Összefoglalás**     | A cím alatt megjelenítendő terv tömör leírása. Legfeljebb 100 karakter hosszú lehet.        |
|**Leírás**     | Leírás szövege, amely részletesebb magyarázatot nyújt a tervről.         |
|**Számlázási modell**     | Itt 2 számlázási modell látható, de **a saját licencét** kell választania a felügyelt szolgáltatások ajánlatait illetően. Ez azt jelenti, hogy az ügyfeleket közvetlenül az ajánlattal kapcsolatos költségekkel számoljuk fel, és a Microsoft semmilyen díjat nem számít fel Önnek.   |
|**Ez egy privát csomag?**     | Azt jelzi, hogy az SKU magán-vagy nyilvános. Az alapértelmezett érték **nem** (nyilvános). Ha ezt a beállítást választja, a csomag nem korlátozódik meghatározott ügyfelekre (vagy bizonyos számú ügyfélre); Miután közzétett egy nyilvános csomagot, később nem módosíthatja magánjellegűre. Ha ezt a csomagot csak bizonyos ügyfelek számára szeretné elérhetővé tenni, válassza az **Igen**lehetőséget. Ha így tesz, azonosítania kell az ügyfeleket az előfizetési azonosítók megadásával. Ezek megadhatók egyenként (legfeljebb 10 előfizetés esetén) vagy egy. csv-fájl feltöltésével (legfeljebb 20 000 előfizetés esetén). Ügyeljen arra, hogy a saját előfizetéseit is tartalmazza, hogy tesztelje és érvényesítse az ajánlatot. További információ: [Private SKU-i és csomagok](../../marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  |

> [!IMPORTANT]
> Miután közzétett egy csomagot nyilvánosként, nem módosíthatja magánjellegűre. Egy privát csomag használatával szabályozhatja, hogy mely ügyfelek fogadhatják el az ajánlatot, és hogyan delegálhat erőforrásokat. Egy nyilvános csomaggal nem korlátozhatja a rendelkezésre állást bizonyos ügyfelek számára, vagy akár bizonyos számú ügyfél számára is (bár ha úgy dönt, hogy teljesen megszüntetheti a csomag értékesítését). Jelenleg nincs olyan mechanizmus, amely elutasítja vagy eltávolítja a delegálásokat, ha az ügyfél elfogad egy ajánlatot, de bármikor elérheti az ügyfelet, és megkérheti, hogy [távolítsa el a hozzáférését](view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="manifest-details"></a>Jegyzékfájl részletei

Fejezze be a tervhez tartozó **manifest details** szakaszt. Ezzel létrehoz egy jegyzékfájlt, amely engedélyezési információkat biztosít az ügyfelek erőforrásainak kezeléséhez. Ez az információ az Azure-beli delegált erőforrás-kezelés engedélyezéséhez szükséges.

> [!NOTE]
> A fentiekben leírtaknak megfelelően az **engedélyezési** bejegyzések felhasználói és szerepkörei minden olyan ügyfélre érvényesek lesznek, akik megvásárolják a csomagot. Ha korlátozni szeretné egy adott ügyfél hozzáférését, közzé kell tennie egy privát csomagot a kizárólagos használatra.

Először adja meg a jegyzékfájl **verzióját** . Használja az *n. n. n* formátumot (például 1.2.5).

Ezután adja meg a **bérlői azonosítóját**. Ez a szervezet Azure Active Directory bérlői azonosítójával társított GUID (azaz a bérlő, amely az ügyfelek erőforrásainak kezeléséhez fog dolgozni). Ha nem rendelkezik ezzel a hasznos megoldással, a fiók neve fölé helyezve a Azure Portal jobb felső sarkában vagy a **címtár váltása**lehetőségre kattintva.

Végül adjon hozzá egy vagy több **engedélyezési** bejegyzést a csomaghoz. Az engedélyek határozzák meg azokat az entitásokat, akik hozzáférhetnek a csomagot megvásárló ügyfelek erőforrásaihoz és előfizetéséhez, valamint olyan szerepköröket, amelyek adott hozzáférési szinteket biztosítanak. A támogatott szerepkörökkel kapcsolatos részletekért lásd: [bérlők, szerepkörök és felhasználók az Azure Lighthouse-forgatókönyvekben](../concepts/tenants-users-roles.md).

Minden egyes **engedélyezéshez**meg kell adnia a következőket. Ezután több felhasználó és szerepkör-definíció hozzáadásához több alkalommal is kiválaszthatja az **új engedélyezést** .

- **Azure ad-objektum azonosítója**: egy felhasználó, felhasználói csoport vagy alkalmazás Azure ad-azonosítója, amely bizonyos engedélyeket kap (a szerepkör-definíció szerint) az ügyfelek erőforrásai számára.
- Az **Azure ad-objektum megjelenített neve**: egy rövid név, amely segítségével az ügyfél megismerheti az engedélyezés célját. Az ügyfél ezt a nevet fogja látni az erőforrások delegálásakor.
- **Szerepkör-definíció**: válassza ki az elérhető Azure ad beépített szerepkörök egyikét a listából. Ez a szerepkör határozza meg, hogy az **Azure ad-objektum azonosító** mezőjében szereplő felhasználó milyen engedélyekkel fog rendelkezni az ügyfelek erőforrásaiban. A szerepkörök leírását lásd: [beépített szerepkörök](../../role-based-access-control/built-in-roles.md) és [szerepkör-támogatás az Azure-beli delegált erőforrás-kezeléshez](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
- **Hozzárendelhető szerepkörök**: erre csak akkor van szükség, ha a **szerepkör-definícióban** a felhasználói hozzáférés rendszergazdája lehetőséget választotta az engedélyezéshez. Ha igen, hozzá kell adnia egy vagy több hozzárendelhető szerepkört. Az **Azure ad-objektum azonosító** mezőjében szereplő felhasználó hozzá tudja rendelni ezeket a **hozzárendelhető szerepköröket** a [felügyelt identitásokhoz](../../active-directory/managed-identities-azure-resources/overview.md), ami szükséges a [szervizelhető házirendek telepítéséhez](deploy-policy-remediation.md). Vegye figyelembe, hogy a felhasználói hozzáférés-rendszergazdai szerepkörhöz tartozó egyéb engedélyek nem lesznek érvényesek erre a felhasználóra. Ha itt nem választ ki egy vagy több szerepkört, a beküldés nem ad át minősítést. (Ha nem választott felhasználói hozzáférési rendszergazdát a felhasználó szerepkör-definíciójában, ennek a mezőnek nincs hatása.)

> [!TIP]
> A legtöbb esetben egy Azure AD-felhasználói csoporthoz vagy egyszerű szolgáltatáshoz kell engedélyeket rendelni, nem pedig egyéni felhasználói fiókokhoz. Ez lehetővé teszi az egyes felhasználók hozzáférésének hozzáadását vagy eltávolítását anélkül, hogy a hozzáférési követelmények változásakor frissítenie és újból közzé kellene tennie a tervet. További javaslatokért lásd: [bérlők, szerepkörök és felhasználók az Azure Lighthouse-forgatókönyvekben](../concepts/tenants-users-roles.md).

Miután elvégezte az információkat, az új csomagok lehetőség kiválasztásával tetszőleges számú további **csomagot** hozhat létre. Ha elkészült, válassza a **Mentés**lehetőséget, majd folytassa a **piactérsel** szakaszt.

## <a name="provide-marketplace-text-and-images"></a>Piactér szövegének és képeinek megadása

A **piactér** szakaszból megtudhatja, hogy mely szövegeket és képeket láthatja az ügyfeleknek az Azure piactéren és a Azure Portal.

Hajtsa végre a következő mezőket az **Áttekintés** szakaszban:

|Mező  |Leírás  |
|---------|---------|
|**Cím**     |  Az ajánlat címe, gyakran a hosszú, formális név. Ez a cím kiemelten jelenik meg a piactéren. Legfeljebb 50 karakter hosszú lehet. A legtöbb esetben ez megegyezik az **ajánlat beállításai** szakaszban megadott **névvel** .       |
|**Összefoglalás**     | Az ajánlat rövid célja vagy funkciója. Ez általában a cím alatt jelenik meg. Legfeljebb 100 karakter hosszú lehet.        |
|**Hosszú összefoglalás**     | Az ajánlat céljának vagy funkciójának összefoglalása. Legfeljebb 256 karakter hosszú lehet.        |
|**Leírás**     | További információ az ajánlatról. A mező maximális hossza 3000 karakter, és támogatja az egyszerű HTML-formázást. A leírásban szerepelnie kell a "felügyelt szolgáltatás" vagy a "felügyelt szolgáltatások" kifejezésnek.       |
|**Marketing azonosítója**     | Egy egyedi URL-cím-barát azonosító. Ez az azonosító csak kisbetűket, alfanumerikus karaktereket és kötőjeleket tartalmazhat. Ezt az ajánlatot a piactér URL-címeiben fogjuk használni. Ha például a közzétevő azonosítója *contoso* , a marketing azonosítója pedig *PéldaAlkalmazás*, akkor az Azure Marketplace-en elérhető ajánlatának URL-címe *https://azuremarketplace.microsoft.com/marketplace/apps/contoso-sampleApp* .        |
|**Előfizetési azonosítók előnézete**     | Adjon hozzá egyet a 100-es előfizetés-azonosítóhoz. Az ehhez az előfizetéshez társított ügyfelek az Azure Marketplace-en tekinthetik meg az ajánlatot, mielőtt a szolgáltatás megkezdi a működést. Javasoljuk, hogy a saját előfizetéseit is itt tekintheti meg, így megtekintheti, hogy az ajánlat hogyan jelenik meg az Azure piactéren, mielőtt elérhetővé tenné az ügyfelek számára.  (A Microsoft támogatási és mérnöki csapatai az előzetes verzió ideje alatt is megtekinthetik az ajánlatot.)   |
|**Hasznos hivatkozások**     | Az ajánlathoz kapcsolódó URL-címek, például dokumentáció, kibocsátási megjegyzések, GYIK stb.        |
|**Javasolt kategóriák (max. 5)**     | Egy vagy több (legfeljebb öt) kategória, amely az ajánlatára vonatkozik. Ezek a kategóriák segítik az ügyfeleket az Azure Marketplace piactéren és a Azure Portalban.        |

A **marketing** -összetevők szakaszban feltölthet fel emblémákat és más eszközöket az ajánlatával megjelenítve. Lehetősége van a képernyőképek feltöltésére vagy olyan videókra mutató hivatkozásokra, amelyek segítségével az ügyfelek megismerhetik az ajánlatot.

Négy embléma mérete szükséges: **kis (40x40cm)** , **közepes (90x90)** , **nagy (115x115)** és **széles (255x115)** . Kövesse az alábbi irányelveket a logókhoz:

- Az Azure arculata egyszerű színpalettát használ. Törekedjen minél kevesebb alap- és másodlagos szín használatára az emblémában.
- A portál témaszínei a fehér és a fekete. Ne használja ezeket a színeket az embléma háttérszíneként. Olyan színt használjon, amelynek hatására az embléma felkelti a figyelmet. Javasoljuk az egyszerű alapszínek használatát.
- Ha transzparens hátteret használ, ügyeljen arra, hogy az embléma és a szöveg ne legyen fehér, fekete vagy kék.
- Az emblémának kétdimenziósnak kell érződnie, és a színátmenetek is kerülendők. Ne használjon színátmenetes hátteret az emblémában.
- Ne helyezzen el szöveget az emblémára, még a vállalat vagy a márka nevét se.
- Ügyeljen arra, hogy az embléma ne legyen széthúzva.

A **Hero (815x290)** embléma nem kötelező, de ajánlott. Ha egy Hero-emblémát is tartalmaz, kövesse az alábbi irányelveket:

- Ne adjon meg szöveget a Hero emblémában, és ügyeljen arra, hogy az embléma jobb oldalán hagyjon 415 képpont üres helyet. Erre azért van szükség, hogy a szöveges elemek legyenek kihagyva, amelyek programozott módon lesznek beágyazva: a közzétevő megjelenítendő neve, a csomag címe, az ajánlat hosszú összegzése.
- A hős emblémájának háttere nem lehet fekete, fehér vagy átlátszó. Győződjön meg arról, hogy a háttérszín nem túl világos, mert a beágyazott szöveg fehér színnel jelenik meg.
- Miután közzétette az ajánlatát egy hős ikonnal, nem távolíthatja el (bár szükség esetén más verzióval is frissítheti).

Az **érdeklődői felügyelet** szakaszban kiválaszthatja azt a CRM-rendszert, amelyen az érdeklődőket tárolni fogja. Vegye figyelembe, hogy a [felügyelt szolgáltatásokra vonatkozó minősítési házirendek](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)alapján szükség van egy **érdeklődő célhelyre** .

Végül adja meg az **adatvédelmi szabályzat URL-címét** és **használati feltételek** a **jogi** szakaszban. Itt megadhatja azt is, hogy a [standard szerződést](../../marketplace/standard-contract.md) használja-e az ajánlathoz.

Ügyeljen arra, hogy mentse a módosításokat, mielőtt továbblép a **támogatási** szakaszra.

## <a name="add-support-info"></a>Támogatási információk hozzáadása

A **támogatás** szakaszban adja meg egy mérnöki kapcsolattartó nevét, e-mail-címét és telefonszámát, valamint egy ügyfélszolgálati kapcsolattartót. Emellett támogatási URL-címeket is meg kell adnia. A Microsoft ezeket az információkat felhasználhatja, amikor kapcsolatba kell lépnie Önnel az üzleti és támogatási problémákkal kapcsolatban.

Miután hozzáadta ezt az információt, válassza a **Mentés lehetőséget.**

## <a name="publish-your-offer"></a>Az ajánlat közzététele

Miután elvégezte az összes szakaszt, a következő lépés az ajánlat közzététele az Azure Marketplace-en. Válassza a **Közzététel** gombot, hogy elindítsa az ajánlatát élőben. A folyamattal kapcsolatos további információkért lásd: [Azure Marketplace-és AppSource-ajánlatok közzététele](../../marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer.md).

Bármikor [közzéteheti az ajánlat frissített verzióját](../../marketplace/cloud-partner-portal/manage-offers/cpp-update-offer.md) . Előfordulhat például, hogy új szerepkör-definíciót szeretne hozzáadni egy korábban közzétett ajánlathoz. Ha így tesz, a már hozzáadott ügyfelek egy ikont láthatnak a Azure Portal [**szolgáltatók**](view-manage-service-providers.md) lapján, amely lehetővé teszi, hogy a frissítés elérhető legyen. Minden ügyfél [áttekintheti a módosításokat](view-manage-service-providers.md#update-service-provider-offers) , és eldöntheti, hogy szeretné-e frissíteni az új verzióra. 

## <a name="the-customer-onboarding-process"></a>Az ügyfél bevezetési folyamata

Miután az ügyfél hozzáadja az ajánlatot, [egy vagy több konkrét előfizetést vagy erőforráscsoportot delegálhat](view-manage-service-providers.md#delegate-resources), amelyek ezután bekerülnek az Azure-beli delegált erőforrás-kezelésbe. Ha egy ügyfél elfogadta az ajánlatot, de még nem delegált erőforrást, akkor a Azure Portal [**szolgáltatók**](view-manage-service-providers.md) lapján a **szolgáltatói ajánlatok** szakaszának felső részén egy megjegyzés jelenik meg.

> [!IMPORTANT]
> A delegálást olyan nem vendég fiókkal kell végrehajtani az ügyfél bérlője számára, amely az előfizetéshez tartozó [tulajdonos beépített szerepkörrel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) rendelkezik (vagy amely tartalmazza az előkészítés alatt álló erőforráscsoportokat). Ha szeretné megtekinteni az összes olyan felhasználót, aki delegálhatja az előfizetést, az ügyfél bérlője kiválaszthatja az előfizetést a Azure Portalban, megnyithatja a **hozzáférés-vezérlés (iam)** elemet, [listázhatja az összes szerepkört](../../role-based-access-control/role-definitions-list.md#list-all-roles), majd a **tulajdonos** lehetőséget választva megtekintheti az adott szerepkörrel

Miután az ügyfél delegált egy előfizetést (vagy egy vagy több erőforráscsoportot egy előfizetésen belül), a **Microsoft. ManagedServices** erőforrás-szolgáltató regisztrálva lesz az adott előfizetéshez, és a bérlő felhasználói hozzáférhetnek a delegált erőforrásokhoz az ajánlatban foglalt jogosultságok alapján.

> [!NOTE]
> Jelenleg az előfizetések (vagy az előfizetésen belüli erőforráscsoportok) nem delegálható, ha az előfizetés Azure Databricks használ. Hasonlóképpen, ha egy előfizetést (vagy egy előfizetéshez tartozó erőforráscsoportot) már delegáltak, jelenleg nem lehet Databricks-munkaterületeket létrehozni az előfizetésben.

## <a name="next-steps"></a>Következő lépések

- További információ a [bérlők közötti felügyeleti élményekről](../concepts/cross-tenant-management-experience.md).
- [Megtekintheti és kezelheti az ügyfeleket](view-manage-customers.md) a Azure Portalban lévő **ügyfelekkel** .
