---
title: Felügyelt szolgáltatások ajánlatának közzététele az Azure Marketplace-en
description: Megtudhatja, hogyan tehet közzé egy felügyelt szolgáltatási ajánlatot, amely az ügyfeleket az Azure-beli delegált erőforrás-kezeléshez irányítja.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 09/19/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 4781126bc4fcfb6391db42a75553a13e0e4cc4f9
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155153"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Felügyelt szolgáltatások ajánlatának közzététele az Azure Marketplace-en

Ebből a cikkből megtudhatja, hogyan tehet közzé egy nyilvános vagy privát felügyelt szolgáltatást az [Azure Marketplace](https://azuremarketplace.microsoft.com) -en a [Cloud Partner Portal](https://cloudpartner.azure.com/)használatával felügyeleti.

> [!NOTE]
> Az ajánlatok létrehozásához és közzétételéhez érvényes fiókkal kell rendelkeznie a [partner Centerben](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) . Ha még nem rendelkezik fiókkal, a [regisztrációs folyamat](https://aka.ms/joinmarketplace) végigvezeti a fiók létrehozásának lépésein a partner Centerben és a kereskedelmi piactér programban való regisztrálásban. A [rendszer automatikusan hozzárendeli](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) az Ön Microsoft Partner Network (MPN) azonosítóját a közzétett ajánlatokhoz, és nyomon követheti az ügyfelek bevonásának következményeit.
>
> Ha nem szeretne ajánlatot közzétenni az Azure Marketplace-en, Azure Resource Manager-sablonok használatával manuálisan is bevezetheti az ügyfeleket. További információ: az ügyfél beléptetése [Az Azure által delegált erőforrás-felügyeletbe](onboard-customer.md).

A felügyelt szolgáltatások ajánlatának közzététele hasonló az Azure Marketplace-en bármilyen más típusú ajánlat közzétételéhez. Ennek a folyamatnak a megismeréséhez tekintse meg az [Azure Marketplace és a AppSource közzétételi útmutatóját](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) , és [kezelje az Azure és a AppSource Marketplace ajánlatait](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers). Tekintse át a [kereskedelmi piactérre vonatkozó minősítési szabályzatokat](https://docs.microsoft.com/legal/marketplace/certification-policies), különösen a [felügyelt szolgáltatások](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) szakaszt.

Ha egy ügyfél felveszi az ajánlatot, egy vagy több konkrét előfizetést vagy erőforráscsoportot delegálhat, amelyek ezután bekerülnek [Az Azure-beli delegált erőforrás-kezelésbe](#the-customer-onboarding-process). Vegye figyelembe, hogy az előfizetés (vagy az előfizetésen belüli erőforráscsoportok) bevezetése előtt a **Microsoft. ManagedServices** erőforrás-szolgáltató manuális regisztrálásával jóvá kell hagynia az előfizetést a bevezetéshez.

> [!IMPORTANT]
> A felügyelt szolgáltatások ajánlatának minden csomagja tartalmaz egy **jegyzékfájl részleteit** tartalmazó szakaszt, ahol megadhatja a bérlőben a Azure Active Directory (Azure ad) azon entitásait, amelyek hozzáférhetnek a delegált erőforráscsoportok és/vagy előfizetésekhez azon ügyfelek számára, akik a csomag megvásárlása. Fontos megjegyezni, hogy az itt foglalt bármely csoport (vagy felhasználó vagy szolgáltatásnév) ugyanazokkal az engedélyekkel fog rendelkezni minden olyan ügyfélnél, aki megvásárolja a csomagot. Ha különböző csoportokat szeretne hozzárendelni az egyes ügyfelekkel való munkához, egy külön privát csomagot kell közzétennie, amely kizárólag az egyes ügyfelekre vonatkozik.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Ajánlat létrehozása a Cloud Partner Portalban

1. Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).
2. A bal oldali navigációs menüben válassza az **új ajánlat**lehetőséget, majd válassza a **felügyelt szolgáltatások**elemet.
3. Az ajánlathoz tartozó **szerkesztői** szakaszt a következő négy részből töltheti le: **Ajánlati beállítások**, **csomagok**, **piactér**és **támogatás**. A fejezetek végrehajtásával kapcsolatos útmutatásért olvassa el a következőt:.

## <a name="enter-offer-settings"></a>Adja meg az ajánlat beállításait

Az **ajánlat beállításai** szakaszban adja meg a következőket:

|Mező  |Leírás  |
|---------|---------|
|**Ajánlat azonosítója**     | Az ajánlat egyedi azonosítója (a közzétevői profilján belül). Ez az azonosító csak kisbetűs alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhat, legfeljebb 50 karakter hosszú lehet. Ne feledje, hogy az ajánlat azonosítója látható az ügyfelek számára olyan helyeken, mint például a termék URL-címei és a számlázási jelentések. Az ajánlat közzététele után nem módosíthatja ezt az értéket.        |
|**Közzétevő azonosítója**     | Az ajánlattal társított közzétevő azonosítója. Ha egynél több közzétevő-AZONOSÍTÓval rendelkezik, kiválaszthatja az ajánlathoz használni kívánt elemet.       |
|**Name**     | Az ügyfelek számára az Azure Marketplace-en és a Azure Portalban megjelenő név (legfeljebb 50 karakter). Olyan felismerhető márkanév használata, amelyet az ügyfelek megértettek – ha Ön a saját webhelyén keresztül népszerűsíti ezt az ajánlatot, ügyeljen arra, hogy itt pontosan ugyanazt a nevet használja.        |

Ha elkészült, válassza a **Mentés**lehetőséget. Most már készen áll a **csomagok** szakaszra való áttérésre.

## <a name="create-plans"></a>Csomagok létrehozása

Minden ajánlatnak egy vagy több csomaggal (más néven SKU) kell rendelkeznie. Több csomagot is hozzáadhat, hogy támogassa a különböző szolgáltatáskészlet-készleteket különböző árakon, vagy hogy testre szabjon egy adott tervet az egyes ügyfelek korlátozott célközönsége számára. Az ügyfelek megtekinthetik a fölérendelt ajánlat keretében elérhető csomagokat.

A csomagok szakaszban minden létrehozni kívánt csomag esetében válassza az **új csomag**lehetőséget. Ezután adja meg a **csomag azonosítóját**. Ez az azonosító csak kisbetűs alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhat, legfeljebb 50 karakter hosszú lehet. Előfordulhat, hogy a csomag azonosítója látható az ügyfelek számára olyan helyeken, mint például a termék URL-címei és a számlázási jelentések. Az ajánlat közzététele után nem módosíthatja ezt az értéket.

Ezután hajtsa végre a következő szakaszokat a **terv részletei** szakaszban:

|Mező  |Leírás  |
|---------|---------|
|**Cím**     | A megjelenő terv felhasználóbarát neve. Legfeljebb 50 karakter hosszú lehet.        |
|**összegzés**     | A cím alatt megjelenítendő terv tömör leírása. Legfeljebb 100 karakter hosszú lehet.        |
|**Leírás**     | Leírás szövege, amely részletesebb magyarázatot nyújt a tervről.         |
|**Számlázási modell**     | Itt 2 számlázási modell látható, de **a saját licencét** kell választania a felügyelt szolgáltatások ajánlatait illetően. Ez azt jelenti, hogy az ügyfeleket közvetlenül az ajánlattal kapcsolatos költségekkel számoljuk fel, és a Microsoft semmilyen díjat nem számít fel Önnek.   |
|**Ez egy privát csomag?**     | Azt jelzi, hogy az SKU magán-vagy nyilvános. Az alapértelmezett érték **nem** (nyilvános). Ha ezt a beállítást választja, a csomag nem korlátozódik meghatározott ügyfelekre (vagy bizonyos számú ügyfélre); Miután közzétett egy nyilvános csomagot, később nem módosíthatja magánjellegűre. Ha ezt a csomagot csak bizonyos ügyfelek számára szeretné elérhetővé tenni, válassza az **Igen**lehetőséget. Ha így tesz, azonosítania kell az ügyfeleket az előfizetési azonosítók megadásával. Ezek megadhatók egyenként (legfeljebb 10 előfizetés esetén) vagy egy. csv-fájl feltöltésével (legfeljebb 20 000 előfizetés esetén). Ügyeljen arra, hogy a saját előfizetéseit is tartalmazza, hogy tesztelje és érvényesítse az ajánlatot. További információ: [Private SKU-i és csomagok](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  |

Végül fejezze be a **manifest details** szakaszt. Ezzel létrehoz egy jegyzékfájlt, amely engedélyezési információkat biztosít az ügyfelek erőforrásainak kezeléséhez. Az itt megadott információk az Azure-beli delegált erőforrás-kezelésre vonatkozó ügyfeleinek bevezetéséhez szükségesek. A fentiekben leírtak szerint ezek az engedélyek minden olyan ügyfélre érvényesek lesznek, aki megvásárolja a csomagot, így ha korlátozni szeretné egy adott ügyfél hozzáférését, közzé kell tennie egy privát csomagot a kizárólagos használatra.

- Először adja meg a jegyzékfájl **verzióját** . Használja az *n. n. n* formátumot (például 1.2.5).
- Ezután adja meg a **bérlői azonosítóját**. Ez a szervezet Azure Active Directory bérlői azonosítójával társított GUID (azaz a bérlő, amely az ügyfelek erőforrásainak kezeléséhez fog dolgozni). Ha nem rendelkezik ezzel a hasznos megoldással, a fiók neve fölé helyezve a Azure Portal jobb felső sarkában vagy a **címtár váltása**lehetőségre kattintva. 
- Végül adjon hozzá egy vagy több **engedélyezési** bejegyzést a csomaghoz. Az engedélyek határozzák meg azokat az entitásokat, akik hozzáférhetnek a csomagot megvásárló ügyfelek erőforrásaihoz és előfizetésekhez. Meg kell adnia ezt az információt ahhoz, hogy az ügyfél nevében hozzáférhessen az erőforrásokhoz az Azure-beli delegált erőforrás-kezelés használatával.
  Minden egyes engedélyezéshez adja meg a következőket. Ezután több felhasználó/szerepkör-definíció hozzáadásához szükség szerint többször is kiválaszthatja az **új engedélyt** .
  - **Azure ad-objektum azonosítója**: Egy felhasználó, felhasználói csoport vagy alkalmazás Azure AD-azonosítója, amely bizonyos engedélyeket kap (a szerepkör-definíció szerint) az ügyfelek erőforrásai számára.
  - **Azure ad-objektum megjelenített neve**: Egy rövid név, amely segít az ügyfélnek megérteni az engedélyezés célját. Az ügyfél ezt a nevet fogja látni az erőforrások delegálásakor.
  - **Szerepkör-definíció**: Válassza ki az elérhető Azure AD-beli szerepkörök egyikét a listából. Ez a szerepkör határozza meg, hogy az **Azure ad-objektum azonosító** mezőjében szereplő felhasználó milyen engedélyekkel fog rendelkezni az ügyfelek erőforrásaiban. További információ ezekről a szerepkörökről: [beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  - **Hozzárendelhető szerepkörök**: Erre csak akkor van szükség, ha az engedélyezéshez a szerepkör- **definícióban** a felhasználói hozzáférés rendszergazdája lehetőséget választotta. Ha igen, hozzá kell adnia egy vagy több hozzárendelhető szerepkört. Az **Azure ad-objektum azonosító** mezőjében szereplő felhasználó hozzá tudja rendelni ezeket a **hozzárendelhető szerepköröket** a [felügyelt identitásokhoz](https://docs.microsoft.com/azure/managed-applications/publish-managed-identity). Vegye figyelembe, hogy a felhasználói hozzáférés-rendszergazdai szerepkörhöz tartozó egyéb engedélyek nem lesznek érvényesek erre a felhasználóra. Ha itt nem választ ki egy vagy több szerepkört, a beküldés nem ad át minősítést. (Ha nem választott felhasználói hozzáférési rendszergazdát a felhasználó szerepkör-definíciójában, ennek a mezőnek nincs hatása.)

> [!TIP]
> A legtöbb esetben egy Azure AD-felhasználói csoporthoz vagy egyszerű szolgáltatáshoz kell engedélyeket rendelni, nem pedig egyéni felhasználói fiókokhoz. Ez lehetővé teszi az egyes felhasználók hozzáférésének hozzáadását vagy eltávolítását anélkül, hogy a hozzáférési követelmények változásakor frissítenie és újból közzé kellene tennie a tervet.

Ha elkészült a csomagok hozzáadásával, válassza a **Mentés**lehetőséget, majd folytassa a **piactérsel** szakaszt.

## <a name="provide-marketplace-text-and-images"></a>Piactér szövegének és képeinek megadása

A **piactér** szakaszból megtudhatja, hogy mely szövegeket és képeket láthatja az ügyfeleknek az Azure piactéren és a Azure Portal.

Adja meg a következő mezők adatait az **Áttekintés** szakaszban:

|Mező  |Leírás  |
|---------|---------|
|**Cím**     |  Az ajánlat címe, gyakran a hosszú, formális név. Ez a cím kiemelten jelenik meg a piactéren. Legfeljebb 50 karakter hosszú lehet. A legtöbb esetben ez megegyezik az **ajánlat beállításai** szakaszban megadott **névvel** .       |
|**összegzés**     | Az ajánlat rövid célja vagy funkciója. Ez általában a cím alatt jelenik meg. Legfeljebb 100 karakter hosszú lehet.        |
|**Hosszú összefoglalás**     | Az ajánlat céljának vagy funkciójának összefoglalása. Legfeljebb 256 karakter hosszú lehet.        |
|**Leírás**     | További információ az ajánlatról. A mező maximális hossza 3000 karakter, és támogatja az egyszerű HTML-formázást. A leírásban szerepelnie kell a "felügyelt szolgáltatás" vagy a "felügyelt szolgáltatások" kifejezésnek.       |
|**Marketing azonosítója**     | Egy egyedi URL-cím-barát azonosító. ezt az ajánlatot a piactér URL-címeiben fogjuk használni. Ha például a közzétevő azonosítója *contoso* , a marketing azonosítója pedig *PéldaAlkalmazás*, akkor az Azure Marketplace-en elérhető *https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp* ajánlatának URL-címe a következő lesz:.        |
|**Előfizetési azonosítók előnézete**     | Adjon hozzá egyet a 100-es előfizetés-azonosítóhoz. Az ehhez az előfizetéshez társított ügyfelek az Azure Marketplace-en tekinthetik meg az ajánlatot, mielőtt a szolgáltatás megkezdi a működést. Javasoljuk, hogy a saját előfizetéseit is itt tekintheti meg, így megtekintheti, hogy az ajánlat hogyan jelenik meg az Azure piactéren, mielőtt elérhetővé tenné az ügyfelek számára.  (A Microsoft támogatási és mérnöki csapatai az előzetes verzió ideje alatt is megtekinthetik az ajánlatot.)   |
|**Hasznos hivatkozások**     | Az ajánlathoz kapcsolódó URL-címek, például dokumentáció, kibocsátási megjegyzések, GYIK stb.        |
|**Javasolt kategóriák (max. 5)**     | Egy vagy több (legfeljebb öt) kategória, amely az ajánlatára vonatkozik. Ezek a kategóriák segítik az ügyfeleket az Azure Marketplace piactéren és a Azure Portalban.        |

A **marketing** -összetevők szakaszban feltölthet fel emblémákat és más eszközöket az ajánlatával megjelenítve. Lehetősége van a képernyőképek feltöltésére vagy olyan videókra mutató hivatkozásokra, amelyek segítségével az ügyfelek megismerhetik az ajánlatot.

Négy embléma mérete szükséges: **Kicsi (40x40cm)** , **közepes (90x90)** , **nagy (115x115)** és **széles (255x155)** . Kövesse az alábbi irányelveket a logókhoz:

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

Végül adja meg az **adatvédelmi szabályzat URL-címét** és **használati feltételek** a **jogi** szakaszban. Itt megadhatja azt is, hogy a [standard szerződést](https://docs.microsoft.com/azure/marketplace/standard-contract) használja-e az ajánlathoz.

Ügyeljen arra, hogy mentse a módosításokat, mielőtt továbblép a **támogatási** szakaszra.

## <a name="add-support-info"></a>Támogatási információk hozzáadása

A **támogatás** szakaszban adja meg egy mérnöki kapcsolattartó nevét, e-mail-címét és telefonszámát, valamint egy ügyfélszolgálati kapcsolattartót. Emellett támogatási URL-címeket is meg kell adnia. A Microsoft ezeket az adatokat akkor használhatja, ha az üzleti és támogatási problémákkal kapcsolatban kapcsolatba kell lépnie Önnel.

Miután hozzáadta ezt az információt, válassza a **Mentés lehetőséget.**

## <a name="publish-your-offer"></a>Az ajánlat közzététele

Ha már elégedett az összes megadott információval, a következő lépés az ajánlat közzététele az Azure Marketplace-en. Válassza a **Közzététel** gombot, hogy elindítsa az ajánlatát élőben. További információ erről a folyamatról: [Azure Marketplace-és AppSource-ajánlatok közzététele](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer).

## <a name="the-customer-onboarding-process"></a>Az ügyfél bevezetési folyamata

Amikor egy ügyfél felveszi az ajánlatot, [egy vagy több konkrét előfizetést vagy erőforráscsoportot delegálhat](view-manage-service-providers.md#delegate-resources) , amelyek ezután bekerülnek az Azure-beli delegált erőforrás-kezelésbe. Ha egy ügyfél elfogadta az ajánlatot, de még nem delegált erőforrást, akkor a Azure Portal [**szolgáltatók**](view-manage-service-providers.md) lapján a **szolgáltatói ajánlatok** szakaszának felső részén egy megjegyzés jelenik meg.

Ahhoz, hogy előkészítse az előfizetést (vagy az előfizetéshez tartozó erőforráscsoportokat), az előfizetést a **Microsoft. ManagedServices** erőforrás-szolgáltató manuális regisztrálásával kell engedélyezni a bevezetéshez. Az ügyfél bérlője és a tulajdonos szerepkör felhasználója ezt az [Azure Resource Providers és types](../../azure-resource-manager/resource-manager-supported-services.md)című témakörben ismertetett lépéseket követve teheti meg.

Az ügyfél ezután ellenőrizheti, hogy az előfizetés készen áll-e a bevezetésre az alábbi módszerek egyikével.

### <a name="azure-portal"></a>Azure Portal

1. A Azure Portal válassza ki az előfizetést.
1. Válassza ki az **Erőforrás-szolgáltatók** elemet.
1. Ellenőrizze, hogy a **Microsoft. ManagedServices** **regisztráltként**jelenik-e meg.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

Ennek a következőhöz hasonló eredményeket kell visszaadnia:

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show --namespace "Microsoft.ManagedServices" --output table
```

Ennek a következőhöz hasonló eredményeket kell visszaadnia:

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="next-steps"></a>További lépések

- További információ a [bérlők közötti felügyeleti élményekről](../concepts/cross-tenant-management-experience.md).
- [Megtekintheti és kezelheti az ügyfeleket](view-manage-customers.md) a Azure Portalban lévő **ügyfelekkel** .
