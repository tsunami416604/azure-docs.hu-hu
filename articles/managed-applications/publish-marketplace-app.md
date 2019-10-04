---
title: Azure-beli felügyelt alkalmazások a Marketplace piactéren | Microsoft Docs
description: A cikk a Marketplace piactéren elérhető Azure-beli felügyelt alkalmazásokat ismerteti.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 07/17/2019
ms.author: tomfitz
ms.openlocfilehash: 372c65f1ac9a08d066f26e637f3af86807b35f11
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182806"
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Azure-beli felügyelt alkalmazások a Marketplace piactéren

A szállítók Azure-beli felügyelt alkalmazások segítségével kínálhatják megoldásaikat az Azure Marketplace ügyfeleinek mindegyike számára. Ezek a szállítók lehetnek felügyelt szolgáltatások szolgáltatói (MSP-k), független szoftverszállítók (ISV-k) és rendszerintegrátorok (SI-k). A felügyelt alkalmazások csökkentik a karbantartás és a szervizelés miatt az ügyfelekre háruló terheket. A szállítók infrastruktúrát és szoftvert értékesíthetnek a piactéren. Szolgáltatásokat és működtetéshez kapcsolódó támogatást kínálhatnak a felügyelt alkalmazásokkal. További információért tekintse meg a [felügyelt alkalmazások áttekintésével](overview.md) foglalkozó cikket.

Ez a cikk azt ismerteti, hogy hogyan tehet közzé egy alkalmazást a piactéren, és teheti széles körben elérhetővé az ügyfelek számára.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Felügyelt alkalmazás közzétételének előfeltételei

Az oktatóanyag elvégzéséhez szüksége lesz a felügyelt alkalmazás definíciójához tartozó .zip fájlra. További információért tekintse meg a [szolgáltatáskatalógusban elérhető alkalmazás létrehozásával](publish-service-catalog-app.md) foglalkozó cikket.

Számos üzleti előfeltétel van. Ezek a következők:

* A vállalatnak vagy leányvállalatának olyan országban vagy régióban kell lennie, ahol a piactér támogatja az értékesítést.
* A terméket olyan módon kell licencelni, amely kompatibilis a piactér által támogatott számlázási modellekkel.
* Műszaki támogatást kell biztosítania az ügyfelek számára üzleti szempontból észszerű módon. A támogatás lehet ingyenes, fizetős vagy közösség által nyújtott támogatás.
* Licencelnie kell a szoftvert, valamint a külső szoftverfüggőségeket.
* Olyan tartalmat kell biztosítania, amely teljesíti az ajánlatnak a Marketplace piactéren és az Azure Portalon való meghirdetésének feltételeit.
* El kell fogadnia az Azure Marketplace részvételi szabályzatának és a közzétevői megállapodásnak a feltételeit.
* El kell fogadnia a használati feltételeket, a Microsoft adatvédelmi nyilatkozatát és a Microsoft Azure Certified Program Agreement feltételeit.

Emellett piactér-fiókkal is rendelkeznie kell. A fiók létrehozásával kapcsolatban lásd: [kereskedelmi piactér fiók létrehozása a partner Centerben](..//marketplace/partner-center-portal/create-account.md).

## <a name="create-a-new-azure-application-offer"></a>Új Azure-alkalmazásajánlat létrehozása

A partnerportálfiók létrehozását követően készen áll a felügyelt alkalmazást kínáló ajánlat létrehozására.

### <a name="set-up-an-offer"></a>Ajánlat beállítása

A felügyelt alkalmazásra vonatkozó ajánlat a közzétevőtől származó termékosztály-ajánlatnak felel meg. Ha új típusú alkalmazást szeretne a piactéren elérhetővé tenni, beállíthatja azt új ajánlatként. Az ajánlatok SKU-k gyűjteményei. Minden ajánlat saját entitásaként jelenik meg a piactéren.

1. Jelentkezzen be a [Felhőpartnerportálra](https://cloudpartner.azure.com/).

1. A bal oldali navigációs panelen válassza a **+ New offer** > **Azure Applications** (+ Új ajánlat > Azure alkalmazások) lehetőséget.

1. Az **Editor** (Szerkesztő) nézetben láthatja a szükséges űrlapokat. Az egyes űrlapokat a cikk alábbi részei ismertetik.

## <a name="offer-settings-form"></a>Offer Settings (Ajánlatbeállítások) űrlap

Az **Offer Settings** (Ajánlatbeállítások) űrlap mezői a következők:

* **Ajánlat azonosítója**: Ez az egyedi azonosító a közzétevői profilban található ajánlatot azonosítja. Az azonosító a termék URL-címeiben, a Resource Manager-sablonokban és a számlázási jelentésekben látható. Csak kisbetűs alfanumerikus karakterekből és kötőjelekből (-) állhat. Az azonosító nem végződhet kötőjellel. Legfeljebb 50 karakterből állhat. Miután egy ajánlat elérhetővé válik, ezt a mezőt zárolja a rendszer.
* **KÖZZÉTEVŐ azonosítója**: Ezzel a legördülő listából kiválaszthatja azt a közzétevői profilt, amelynek az ajánlatát közzé szeretné tenni. Miután egy ajánlat elérhetővé válik, ezt a mezőt zárolja a rendszer.
* **Név**: Az ajánlat megjelenítendő neve megjelenik a piactéren és a portálon. Legfeljebb 50 karakterből állhat. A termék számára egy felismerhető márkanevet adjon meg. A vállalat nevét ne adja meg itt, kivéve, ha így forgalmazza a terméket. Ha az ajánlatot a saját webhelyén is forgalmazza, gondoskodjon arról, hogy a név pontosan úgy jelenik meg, mint a webhelyén.

Ha elkészült, válassza a **Save** (Mentés) elemet a megadott adatok mentéséhez.

## <a name="skus-form"></a>SKUs (SKU-k) űrlapja

A következő lépés az SKU-k hozzáadása az ajánlathoz.

Az SKU egy ajánlat legkisebb megvásárolható egysége. Az SKU-kat ugyanazon a termékosztályon (ajánlaton) belül használhatja az alábbiak megkülönböztetéséhez:

* a különféle támogatott funkciók,
* az ajánlat felügyelt-e vagy sem,
* a támogatott számlázási modellek.

Az SKU-k a fő ajánlat alatt jelennek meg a piactéren. Saját megvásárolható entitásként jelennek meg az Azure Portalon.

1. Válassza az **SKUs** > **New SKU** (SKU-k > Új SKU) elemet.

1. Adjon meg egy értéket az **SKU ID** (SKU-azonosító) mezőben. Az SKU-azonosító egy adott SKU egyedi azonosítója az ajánlaton belül. Az azonosító a termék URL-címeiben, a Resource Manager-sablonokban és a számlázási jelentésekben látható. Csak kisbetűs alfanumerikus karakterekből és kötőjelekből (-) állhat. Az azonosító nem végződhet kötőjellel, és legfeljebb 50 karakterből állhat. Miután egy ajánlat elérhetővé válik, ezt a mezőt zárolja a rendszer. Több SKU-val is rendelkezhet egy ajánlaton belül. Minden közzétenni kívánt rendszerkép esetében külön SKU-t kell megadni.

1. Töltse ki a következő űrlapon található **SKU Details** (SKU részletei) szakaszt:

   Töltse ki az alábbi mezőket:

   * **Cím**: Adja meg az SKU címét. Ez a cím jelenik meg a katalógusban ennél az elemnél.
   * **Összefoglalás**: Adjon meg egy rövid összefoglalást ehhez az SKU-hoz. Ez a szöveg a cím alatt jelenik meg.
   * **Leírás**: Adja meg az SKU részletes leírását.
   * **SKU típusa**: Az engedélyezett értékek *felügyelt alkalmazás* -és *megoldás-sablonok*. Ebben az esetben a *Managed Application* (Felügyelt alkalmazás) lehetőséget válassza.
   * **Ország/régió elérhetősége**: Válassza ki azokat az országokat/régiókat, ahol a felügyelt alkalmazás elérhető.
   * **Díjszabás**: Adja meg az alkalmazás felügyeletének díját. Az ár beállítása előtt válassza ki az elérhető országokat/régiókat.

1. Adjon hozzá egy új csomagot. Töltse ki a következő űrlapon található **Package Details** (Csomag részletei) szakaszt:

   Töltse ki az alábbi mezőket:

   * **Verzió**: Adja meg a feltöltött csomag verziószámát. A következő formátumban kell lennie: `{number}.{number}.{number}{number}`.
   * **Csomagfájl (. zip)** : Ez a csomag a. zip csomagba tömörített két szükséges fájlt tartalmaz. Az egyik fájl a Resource Manager-sablon, amely a felügyelt alkalmazáshoz üzembe helyezendő erőforrásokat határozza meg. A másik fájl a [felhasználói felületet](create-uidefinition-overview.md) határozza meg a felügyelt alkalmazást a portálon keresztül üzembe helyező felhasználók számára. A felhasználói felületen elemeket ad meg, amelyek lehetővé teszik a felhasználók számára paraméterértékek megadását.
   * **Bérlő azonosítója**: A fiókhoz tartozó bérlő azonosítója, amelyhez hozzáférést szeretne kapni.
   * **JIT-hozzáférés engedélyezése**: Az **Igen** lehetőség kiválasztásával engedélyezheti az igény szerinti [hozzáférés](request-just-in-time-access.md) -vezérlést a fiókhoz. Ha engedélyezve van, egy adott időszakra kéri a fogyasztó fiókjához való hozzáférést. Ha szeretné megkövetelni, hogy a felügyelt alkalmazás felhasználói hozzáférhessenek a fiók állandó eléréséhez, válassza a **nem**lehetőséget.
   * **Testreszabhatja az engedélyezett ügyfelek műveleteit?** : Válassza az **Igen** lehetőséget a felügyelt erőforrásokon a felhasználók által végrehajtható műveletek megadásához.
   * **Engedélyezett felhasználói műveletek**: Ha az előző beállításnál az **Igen** lehetőséget választja, megadhatja, hogy mely műveletek engedélyezettek a felhasználók számára az [Azure-erőforrások](../role-based-access-control/deny-assignments.md)megtagadási hozzárendeléseinek használatával.

     Az elérhető műveletekért lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md). Ha például engedélyezni szeretné a felhasználóknak a virtuális gépek újraindítását `Microsoft.Compute/virtualMachines/restart/action` , adja hozzá az engedélyezett műveleteket. A `*/read` művelet automatikusan engedélyezve van, így nincs szükség a beállítás belefoglalására.
   * **PrincipalId**: Ez a tulajdonság egy felhasználó, felhasználói csoport vagy alkalmazás Azure Active Directory (Azure AD) azonosítója, amely hozzáférést kapott az ügyfél előfizetésében lévő erőforrásokhoz. A Role Definition (Szerepkör-definíció) az engedélyeket ismerteti.
   * **Szerepkör-definíció**: Ez a tulajdonság az Azure AD által biztosított összes beépített szerepköralapú Access Control (RBAC) szerepkör listája. Kiválaszthatja az erőforrásoknak az ügyfél nevében történő felügyeletéhez leginkább megfelelőbb szerepkört.
   * **Házirend-beállítások**: [Azure Policy](../governance/policy/overview.md) alkalmazása a felügyelt alkalmazáson a telepített megoldások megfelelőségi követelményeinek megadásához. Válassza ki az alkalmazandó szabályzatokat az elérhető lehetőségek közül. **Szabályzatparaméterek** esetén adjon meg egy JSON-karakterláncot a paraméter értékeivel. A szabályzatdefiníciókról és a paraméterértékek formátumáról tekintse meg a következő dokumentumot: [Azure Policy-minták](../governance/policy/samples/index.md).

Több engedélyt is hozzáadhat. Javasoljuk, hogy hozzon létre egy AD-felhasználócsoportot, és adja meg annak azonosítóját a **PrincipalId** (Résztvevő-azonosító) tulajdonságban. Így több felhasználót is hozzáadhat a felhasználócsoporthoz anélkül, hogy frissítenie kellene az SKU-t.

Az RBAC-vel kapcsolatban az [RBAC Azure Portalon való használatának megkezdését ismertető](../role-based-access-control/overview.md) témakörben tekinthet meg további információt.

## <a name="marketplace-form"></a>Marketplace (Piactér) űrlap

A Marketplace (Piactér) űrlap az [Azure Marketplace](https://azuremarketplace.microsoft.com) és az [Azure Portal](https://portal.azure.com/) felületén megjelenő mezők megadását kéri.

### <a name="preview-subscription-ids"></a>Preview subscription IDs (Előzetes verzióhoz hozzáférő azonosítók)

Adja meg azon Azure-előfizetések azonosítóinak listáját, amelyek hozzáférhetnek az ajánlathoz, miután közzétette azt. Az engedélyezési listán szereplő előfizetéseket az ajánlat előzetes verziójának teszteléséhez használhatja, mielőtt elérhetővé tenné az ajánlatot mindenki számára. A partner portálon akár 100-előfizetés engedélyezési listáját is lefordíthatja.

### <a name="suggested-categories"></a>Suggested categories (Javasolt kategóriák)

Legfeljebb öt olyan kategóriát választhat ki a listából, amelyekhez az ajánlata a lehető legjobban kapcsolódik. Ezekkel a kategóriákkal sorolható be ajánlata az [Azure Marketplace](https://azuremarketplace.microsoft.com) piactéren és az [Azure Portalon](https://portal.azure.com/) elérhető termékkategóriákba.

#### <a name="azure-marketplace"></a>Azure Marketplace

A felügyelt alkalmazás összefoglalása az alábbi mezőket jeleníti meg:

![A piactéren megjelenő összefoglalás](./media/publish-marketplace-app/publishvm10.png)

A felügyelt alkalmazás **Overview** (Áttekintés) lapján az alábbi mezők jelennek meg:

![Piactér áttekintése](./media/publish-marketplace-app/publishvm11.png)

A felügyelt alkalmazás **Plans + Pricing** (Csomagok és díjszabás) lapján az alábbi mezők jelennek meg:

![A piactéren elérhető csomagok](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>Azure Portal

A felügyelt alkalmazás összefoglalása az alábbi mezőket jeleníti meg:

![A portálon megjelenő összefoglalás](./media/publish-marketplace-app/publishvm12.png)

A felügyelt alkalmazás áttekintése az alábbi mezőket jeleníti meg:

![A portálon megjelenő áttekintése](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>Emblémával kapcsolatos irányelvek

Az alábbi irányelveket kövesse a Felhőpartnerportálra feltölteni kívánt emblémákra vonatkozóan:

*   Az Azure arculata egyszerű színpalettát használ. Törekedjen minél kevesebb alap- és másodlagos szín használatára az emblémában.
*   A portál témaszínei a fehér és a fekete. Ne használja ezeket a színeket az embléma háttérszíneként. Olyan színt használjon, amelynek hatására az embléma felkelti a figyelmet. Javasoljuk az egyszerű alapszínek használatát. *Ha átlátszó hátteret használ, az embléma és a szöveg ne legyen fehér, fekete vagy kék.*
*   Ne használjon színátmenetes hátteret az emblémában.
*   Ne helyezzen el szöveget az emblémára, még a vállalat vagy a márka nevét se. Az emblémának kétdimenziósnak kell érződnie, és a színátmenetek is kerülendők.
*   Ügyeljen arra, hogy az embléma ne legyen széthúzva.

#### <a name="hero-logo"></a>Főképembléma

A főképembléma nem kötelező. A közzétevő dönthet úgy, hogy nem tölt fel főképemblémát. A főképikon a feltöltését követően nem törölhető. Ekkor a partnernek követnie kell a főképikonokra vonatkozó Marketplace-irányelveket.

A főképembléma ikonjára vonatkozóan az alábbi irányelveket kövesse:

*   A közzétevő megjelenítendő neve, a csomag címe és az ajánlat részletes összefoglalása fehéren jelenik meg. Ezért ne használjon világos színt a főképikon hátteréhez. A fekete, fehér vagy átlátszó háttér nem engedélyezett a főképikonok esetén.
*   Az ajánlat meghirdetését követően az elemeket a rendszer szoftvere ágyazza be a főképemblémába. A beágyazott elemek közé tartozik a közzétevő megjelenítendő neve, a csomag címe, az ajánlat részletes összefoglalása és a **Create** (Létrehozás) gomb. Ezért ne adjon meg szöveget a főképembléma tervezése közben. Hagyja üresen a jobb oldali területet, mert a szöveget a rendszer szoftvere helyezi oda. A szöveg számára a jobb oldalon fenntartott üres helynek 415 x 100 képpont méretűnek kell lennie. A bal oldalról számítva 370 képpontnyi az eltolása.

    ![Példa a főképemblémára](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>Support (Támogatás) űrlap

A **Support** (Támogatás) űrlapon a vállalat támogatási csapatának kapcsolattartási adatait adja meg. Ezek az adatok a fejlesztési csapat és az ügyfélszolgálat elérhetőségei lehetnek.

## <a name="publish-an-offer"></a>Ajánlat közzététele

Miután az összes szakaszt kitöltötte, válassza a **Publish** (Közzététel) lehetőséget azon folyamat elindításához, amely elérhetővé teszi az ajánlatot az ügyfelek számára.

## <a name="next-steps"></a>További lépések

* További információ arról, hogy mi történik a **Közzététel**gombra kattintás után: [Azure-alkalmazási ajánlat közzététele](../marketplace/cloud-partner-portal/azure-applications/cpp-publish-offer.md)
* A felügyelt alkalmazások bemutatásáért tekintse meg a [felügyelt alkalmazások áttekintését](overview.md).
* A szolgáltatáskatalógusban elérhető felügyelt alkalmazások közzétételével kapcsolatban tekintse meg a [szolgáltatáskatalógusban elérhető felügyelt alkalmazások létrehozását és közzétételét](publish-service-catalog-app.md) ismertető témakört.
