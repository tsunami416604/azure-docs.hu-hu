---
title: A felügyelt szolgáltatások ajánlat közzététele az Azure piactéren
description: Ismerje meg, hogyan tehet közzé egy felügyelt ajánlat, hogy az előkészítő ügyfelek az Azure-bA delegált erőforrás-kezelés.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: bb2f26a170bbd60eb927bd00f6def7d033fafee9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809847"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>A felügyelt szolgáltatások ajánlat közzététele az Azure piactéren

Ebből a cikkből megtudhatja, hogyan közzététele egy nyilvános vagy privát felügyelt szolgáltatások ajánlattal [Azure Marketplace-en](https://azuremarketplace.microsoft.com) használatával a [Cloud Partner Portalon](https://cloudpartner.azure.com/), olyan ügyfelünk, akit előkészíteni az ajánlatot vásárol engedélyezése az Azure resource management delegált. 

> [!NOTE]
> Szüksége lesz egy érvényes [Partnerközpont a fiók](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) hozhat létre, és ezek az ajánlatok közzététele. Ha már nem rendelkezik fiókkal a [fiókadatait](https://aka.ms/joinmarketplace) végigvezeti a lépéseken, hozzon létre egy fiókot a Partner Center és a kereskedelmi Piactérrel való regisztrációval. A Microsoft Partner Network (MPN)-azonosító [automatikusan társított](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) nyomon követésére a hatás az ügyfélesetekből közzététele az ajánlatokkal.
>
> Ha nem szeretné az ajánlat közzététele az Azure piactéren, lehetősége van ügyfelek manuálisan az Azure Resource Manager-sablonok használatával. További információ: [felvétele a az Azure-bA egy ügyfél meghatalmazott erőforrás-kezelés](onboard-customer.md).

Közzétételi egy felügyelt szolgáltatások ajánlat hasonlít bármilyen más típusú ajánlat közzététele az Azure Marketplace-en. A folyamat kapcsolatos további információkért lásd: [Azure Marketplace és AppSource közzétételi útmutató](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) és [kezelése az Azure és az AppSource piactérről ajánlatokat](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

> [!IMPORTANT]
> A felügyelt szolgáltatások ajánlat minden csomag egy **Manifest részletek** szakaszt, itt adhatja meg az Azure Active Directory (Azure AD) entitások, amelyek a delegált erőforráscsoportok és/vagy az előfizetések hozzáférést kap a bérlőben a csomagot is megvásárló ügyfeleinknek. Fontos figyelembe, hogy minden olyan csoport (vagy a felhasználónév vagy szolgáltatásnév), amely itt lesz ugyanazokkal az engedélyekkel minden ügyfél számára a csomag vásárlása. Minden egyes ügyfél együttműködni különböző csoportokhoz rendeléséhez kell közzétenni kizárólagos minden egyes ügyfélnek külön privát csomagot.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Az ajánlat létrehozása a Cloud Partner portálra

1. Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2. A bal oldali navigációs menüben válassza ki a **új ajánlat**, majd **felügyelt szolgáltatások**.
3. Megjelenik egy **szerkesztő** az Ön ajánlatát a négy részből töltse ki a következő szakaszban: **Ajánlat beállításai**, **csomagok**, **Marketplace**, és **támogatási**. Olvassa el az ezekben a szakaszokban végrehajtásához útmutatást.

## <a name="enter-offer-settings"></a>Adja meg az ajánlat beállításait

Az a **ajánlat beállításai** területén adja meg az alábbi:

|Mező  |Leírás  |
|---------|---------|
|**Ajánlat azonosítója**     | Az ajánlat (belül a közzétevő profil) egyedi azonosítója. Ez az azonosító csak tartalmazhat kisbetűs alfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, amely legfeljebb 50 karakter hosszú lehet. Ne feledje, hogy a Ajánlatazonosító látható, az ügyfelek számára a termék URL-címek és a számlázási jelentésekben például a helyen lehet. Miután közzéteszi az ajánlatot, ez az érték nem módosítható.        |
|**Közzétevő azonosítója**     | A Gyártóazonosítóval, amelyek az ajánlat társítva lesz. Ha egynél több közzétevő-azonosító, kiválaszthatja az egyik szeretné használni ezt az ajánlatot.       |
|**Name**     | A név (legfeljebb 50 karakter hosszú lehet), amely ügyfelek jelenik meg az ajánlat az Azure Marketplace-en és az Azure Portalon. Használja, hogy megértse az ügyfelek könnyen felismerhető névre márkanév – Ha ezt az ajánlatot a saját webhelyen keresztül van támogatása, ügyeljen arra, hogy itt pontosan ugyanazt a nevet használja.        |

Amikor elkészült, válassza ki **mentése**. Most már készen áll a áthelyezése a **csomagok** szakaszban.

## <a name="create-plans"></a>Csomagok létrehozása

Minden ajánlat rendelkeznie kell egy vagy több csomag (más néven termékváltozatok). Több csomagok különböző szolgáltatáskészleteket különböző árakon támogatásához, vagy egy adott terv testreszabása az adott ügyfelek korlátozott közönség számára adhat hozzá. Ügyfelek tekintheti meg a csomagok, amelyek a szülő ajánlatnak számára elérhető.

A csomagok területen minden egyes csomagot szeretne létrehozni, válassza ki a **új terv**. Majd adja meg egy **szolgáltatáscsomag-Azonosítóval**. Ez az azonosító csak tartalmazhat kisbetűs alfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, amely legfeljebb 50 karakter hosszú lehet. Előfordulhat, hogy a csomag azonosítója jelennek meg az ügyfelek számára a termék URL-címek és a számlázási jelentésekben például a helyen. Miután közzéteszi az ajánlatot, ez az érték nem módosítható.

Ezután hajtsa végre a következő szakaszai a **csomag részletei** szakaszban:

|Mező  |Leírás  |
|---------|---------|
|**Cím**     | A csomag megjelenített rövid nevét. Legfeljebb 50 karakter hosszúságú.        |
|**összegzés**     | A képernyő címe alatt megjelenő tervezése tömör leírása. A hosszabb 100 karakternél.        |
|**Leírás**     | Leíró szöveg, amely a csomag több részletesen ismerteti.         |
|**Számlázási modell**     | Itt látható 2 számlázási modellekkel, de ki kell választania **saját licenc használata** felügyelt szolgáltatások, amelyek. Ez azt jelenti, hogy Ön a számlát az ügyfelek közvetlenül az ehhez az ajánlathoz kapcsolódó költségek, és a Microsoft nem számolunk fel díjakat is.   |
|**Az egy privát tervet?**     | Azt jelzi, hogy a Termékváltozat nyilvános vagy privát. Az alapértelmezett érték **nem** (nyilvános). Ha elhagyja ezt a választást, a csomag nem lesz korlátozva, egy adott ügyfélnek (vagy ügyfelek bizonyos számú); egy nyilvános terv a közzététel után már nem módosítható, személyes. Válassza ki ahhoz, hogy ebben a csomagban elérhető csak egy adott ügyfélnek, **Igen**. Ha így tesz, kell azonosítja az ügyfél azáltal, hogy az előfizetés azonosítókat. Ezek lehetnek a megadott egy (legfeljebb 10 előfizetések) eggyel vagy, ha feltölt egy .csv fájlt (legfeljebb 20 000 előfizetések). Mindenképp adja hozzá a saját előfizetések itt, így tesztelése és ellenőrzése az ajánlatot. További információkért lásd: [privát SKU-k és a tervek](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  |

Végül, végezze el a **Manifest részletek** szakaszban. Ez hoz létre egy jegyzéket engedélyezési adatok ügyfél-erőforrások kezeléséhez. Az adatok, adja meg az alábbi szükséges, hogy az Azure-ügyfelek delegált erőforrás-kezelést. A fentiek szerint ezeket az engedélyeket érvénybe lépnek minden olyan ügyfél, a csomagot vásárol, ha szeretné korlátozni a hozzáférést egy adott ügyfél, szüksége lesz egy privát terv kizárólagos használatú közzététele.

- Először adja meg egy **verzió** számára a jegyzékfájlban. Használja a következő formátumot *n.n.n* (például 1.2.5).
- Írja be a **Bérlőazonosító**. Ez az az Azure Active Directory-bérlő azonosítója a szervezet (azaz a bérlői amely dolgozni fog a az ügyfelek-erőforrások kezeléséhez) társított GUID Azonosítót. Ha nem rendelkezik ilyen praktikus, megtalálja az egérmutatót a fiók neve az Azure Portal jobb felső oldalán, vagy a kiválasztásával **címtár váltása**. 
- Végül adja hozzá egy vagy több **engedélyezési** azokat a bejegyzéseket. Engedélyek megadása az entitások érheti el az erőforrások és -előfizetések ügyfelek esetében, akik a csomag. Ezt az információt meg kell adnia annak érdekében, hogy az erőforrások eléréséhez az Azure delegált erőforrás-kezelés használatával a vásárló nevében.
  Minden egyes engedély adja meg a következő. Ezután kiválaszthatja **új engedélyezési** , ahányszor szükséges további felhasználók vagy szerepkör-definíciók hozzáadásához.
  - **Az Azure AD-csoportobjektum azonosítója**: Egy felhasználó, felhasználói csoportot vagy alkalmazást, amely megkapja bizonyos engedélyeket (leírtak szerint a szerepkör-definíció szerint) az ügyfelek erőforrásokhoz az Azure ad-ben azonosítója.
  - **Az Azure AD-objektum megjelenített neve**: Egy rövid nevet, amely az ügyfél, az engedélyezés rendeltetésének megismerése. Az ügyfél delegálása erőforrások esetén ez a név jelenik meg.
  - **Szerepkör-definíció**: Válasszon ki egy, a rendelkezésre álló Azure ad-ben beépített szerepkörök a listából. Ez a szerepkör határozza meg az engedélyeket, amelyek a felhasználó a **Azure AD-csoportobjektum azonosítója** mező lesz, az ügyfelek erőforrásokon. Ezek a szerepkörök kapcsolatos információk: [beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  - **Hozzárendelhető szerepköröket**: Ha be van jelölve a felhasználói hozzáférés rendszergazdája a **szerepkör-definíció** Ez az engedélyezési legalább egy hozzárendelhető szerepköröket Itt adhat hozzá. A felhasználó a **Azure AD-csoportobjektum azonosítója** mező fogja tudni hozzárendelni ezeket **hozzárendelhető szerepköröket** való [felügyelt identitások](https://docs.microsoft.com/azure/managed-applications/publish-managed-identity). Vegye figyelembe, hogy nem más, általában a felhasználói hozzáférés rendszergazdája szerepkörrel társított engedélyek vonatkoznak a felhasználó. (Ha a felhasználói hozzáférés rendszergazdája nem jelölte be, a jelen felhasználói szerepkör-definíció, ez a mező nem befolyásolja.)

> [!TIP]
> A legtöbb esetben érdemes engedélyek hozzárendelése az Azure AD felhasználói csoport vagy a szolgáltatás egyszerű, hanem több egyéni felhasználói fiókokhoz. Ez lehetővé teszi, hogy hozzáadását vagy eltávolítását az egyes felhasználók hozzáférését anélkül, hogy frissítse és tegye közzé újra a tervet, a hozzáférési követelmények változásakor kellene.

Hozzáadásának tervek elkészült, válassza ki a **mentése**, majd folytassa a **Marketplace** szakasz.

## <a name="provide-marketplace-text-and-images"></a>Adja meg a Marketplace-en szövegek és képek

A **Marketplace** szakaszban, ahol megadta a szöveget és képeket, amely ügyfelek jelenik meg az Azure Marketplace-en és az Azure Portalon.

Adja meg a kért adatokat a következő mezőket a a **áttekintése** szakaszban:

|Mező  |Leírás  |
|---------|---------|
|**Cím**     |  Az ajánlat, gyakran hosszabb, formális neve címe. Ezzel a beállítással hangsúlyosan a cím fog megjelenni a Marketplace-en. Legfeljebb 50 karakter hosszúságú. A legtöbb esetben ez legyen ugyanaz, mint a **neve** megadott a **ajánlat beállítások** szakaszban.       |
|**összegzés**     | Rövid célját vagy az Ön ajánlatát függvény. Ez általában a cím alatt jelenik meg. A hosszabb 100 karakternél.        |
|**Hosszú összegzése**     | A cél vagy a funkció az Ön ajánlatát hosszabb összegzése. Legfeljebb 256 karakter hosszúságú.        |
|**Leírás**     | További információ az ajánlatot. Ez a mező 3000 karakterek maximális hossza, és támogatja az egyszerű HTML-formázás.        |
|**Marketing-azonosító**     | URL-cím-leíró egyedi azonosítója. azt használható a Marketplace-en URL-címek ezt az ajánlatot. Például, ha a Gyártóazonosítóval *contoso* és a marketing-azonosító *PéldaAlkalmazás*, az URL-cím, az ajánlat az Azure Marketplace-en lesz *https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp* .        |
|**Előfizetés azonosítókat előzetes verzió**     | Adjon hozzá egy és 100 közötti előfizetés azonosítókat. Az ügyfeleknek ezen előfizetésekkel társított megtekintheti az ajánlat az Azure Marketplace-en előtt kerül élő lesz. Javasoljuk, hogy így megtekintheti, hogyan az ajánlat előtt jelenik meg az Azure Marketplace-en elérhetővé tétele az ügyfeleknek, többek között a saját előfizetések itt.  (A Microsoft támogatási és mérnöki csapataival is megtekintheti az ajánlat az előzetes verzió ideje alatt.)   |
|**Hasznos hivatkozások**     | Kibocsátási megjegyzések – gyakori kérdések, az ajánlatot, például a dokumentáció, kapcsolódó URL-címek stb.        |
|**Javasolt kategóriák (max. 5)**     | Egy vagy több kategóriához (legfeljebb ötöt) amely a alkalmazni az ajánlatban. Ezekben a kategóriákban segíthet az ügyfeleknek az ajánlat az Azure Marketplace-en és az Azure Portalon felderítése.        |

Az a **Marketing összetevők** szakaszban is feltölthet, emblémák és egyéb eszközök jelennek meg az előfizetési csomag keretében. Igény szerint tölthet képernyőképeket, vagy, amelyek segítségével az ügyfelek videókra mutató hivatkozások megismerheti az ajánlatot.

Négy embléma méretek szükség: **Kicsi (40 x 40)** , **közepes (90 x 90)** , **nagy méretű (115 x 115)** , és **Wide (255 x 155)** . Kövesse a az emblémát:

- Az Azure arculata egyszerű színpalettát használ. Törekedjen minél kevesebb alap- és másodlagos szín használatára az emblémában.
- A portál témaszínei a fehér és a fekete. Ne használja ezeket a színeket az embléma háttérszíneként. Olyan színt használjon, amelynek hatására az embléma felkelti a figyelmet. Javasoljuk az egyszerű alapszínek használatát.
- Ha áttetsző háttérrel használ, győződjön meg arról, hogy az embléma és a szöveg nem fehér, fekete, vagy a kék.
- Az emblémának kétdimenziósnak kell érződnie, és a színátmenetek is kerülendők. Ne használjon színátmenetes hátteret az emblémában.
- Ne helyezzen el szöveget az emblémára, még a vállalat vagy a márka nevét se.
- Ügyeljen arra, hogy az embléma ne legyen széthúzva.

A **Hero (815 x 290)** embléma opcionális de javasolt. Ha egy hero emblémájának, kövesse az alábbi irányelveket:

- Nem tartalmaz szöveget az hero embléma, és ügyeljen arra, hogy hagyja meg az üres hely 415 képpont a jobb oldalon az embléma. Ez szükséges hagyjon helyet az programozott módon beágyazott szöveges elemek: a közzétevő megjelenítési nevet, a csomag címét, ajánlat hosszú összegzése.
- A hero embléma háttér nem lehet fekete, a fehér vagy a transzparens. Ellenőrizze, hogy a háttér színe nem túl világos, mert a beágyazott szöveg fog megjelenni a fehér.
- Miután közzéteszi az ajánlatot egy hero ikon, nem távolítható el (bár a frissíthető azt egy másik verziója szükséges).

Az a **vezethet felügyeleti** szakaszban kiválaszthatja a CRM-rendszerrel, az érdeklődőket a rendszer hol tárolja, ha szükséges. 

Végül adja meg a **adatvédelmi szabályzat URL-címe** és **használati feltételeket tartalmazó fájl** a a **jogi** szakaszban. Megadhatja azt is ide-e használni a [általános szerződési](https://docs.microsoft.com/azure/marketplace/standard-contract) ezt az ajánlatot.

Ne felejtse el menteni a módosításokat a váltás előtt a **támogatási** szakaszban.

## <a name="add-support-info"></a>Támogatási információ hozzáadása

Az a **támogatja** területén adja meg a nevét, e-mail és telefonszámát, a vevő és a egy engineering contact támogatási kapcsolattartó. Akkor is kell adnia URL-címeket támogatja. A Microsoft felhasználhatja ezeket az adatokat, ha ellenőriznünk kell a kapcsolatba lépni Önnel, üzleti kapcsolatban, és a támogatási problémák.

Miután hozzáadta ezt az információt, válassza ki a **mentéséhez.**

## <a name="publish-your-offer"></a>Az ajánlat közzététele

Ha elégedett a megadott információ minden, a következő lépés az ajánlat közzététele az Azure piactéren. Válassza ki a **közzététel** gombra kattintva elindíthatja a folyamatot az, hogy az ajánlat élő. További információ erről a folyamatról: [ajánlatok közzététele az Azure Marketplace és AppSource](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer).

## <a name="next-steps"></a>További lépések

- Ismerje meg [több-bérlős felhasználói élmény](../concepts/cross-tenant-management-experience.md).
- [Megtekintheti és kezelheti az ügyfelek](view-manage-customers.md) a **ügyfeleim** az Azure Portalon.
