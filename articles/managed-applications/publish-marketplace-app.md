---
title: "Azure által felügyelt alkalmazások a piactéren |} Microsoft Docs"
description: "Ismerteti az Azure által felügyelt alkalmazások elérhető a piactéren keresztül."
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/18/2018
ms.author: tomfitz
ms.openlocfilehash: fccc2dbb7623f4ceb0d3decc7037f75a05858910
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="azure-managed-applications-in-the-marketplace"></a>A piactéren Azure kezelt alkalmazások

A szállítók használhatja az Azure által felügyelt alkalmazások számára a megoldásokat nyújtsanak az összes Azure piactéren ügyfeleknek. Azok a szállítók felügyelt szolgáltató (MSPs), a független szoftverszállítók (ISV) és a rendszerintegrátorok (SIs) tartalmazhatnak. Felügyelt alkalmazások csökken, a karbantartási és a terhelés karbantartási ügyfelek esetén. A szállítók eladásra infrastruktúra és a szoftver a piactéren keresztül. Felügyelt alkalmazások szolgáltatások és működési támogatás csatolhat. További információkért lásd: [felügyelt használatát áttekintő cikkben](overview.md).

Ez a cikk azt ismerteti, hogyan közzétenni egy alkalmazást a piactéren, és széles körben elérhető legyen az ügyfél számára.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Kezelt alkalmazás közzététele előfeltételei

Ez a cikk befejezéséhez már a kezelt alkalmazás definíciójának kell rendelkeznie a .zip-fájlt. További információkért lásd: [katalógus szolgáltatásalkalmazás létrehozása](publish-service-catalog-app.md).

Emellett nincsenek több üzleti előfeltételek. Ezek a következők:

* A vállalat vagy a helyi leányvállalatnál olyan országban, ahol értékesítési a piactér által támogatott kell lennie.
* A termék oly módon, amely kompatibilis a számlázási modellt támogat a piactér licenccel kell rendelkezniük.
* A technikai támogatási szolgálathoz elérhetővé teszi az ügyfél számára minden üzleti szempontból ésszerű módon. A támogatási ingyenes, a fizetős, és közösségi keresztül támogatja.
* A szoftver- és bármely harmadik féltől származó szoftverek függőségek licenc.
* Adjon meg, amely megfelel az elérhető a piactéren, és az Azure portálon listázásának tartalmat.
* Fogadja el az Azure piactér részvételét házirendek és a Publisher szerződés feltételeit.
* A használati feltételeket, a Microsoft adatvédelmi nyilatkozatát és a Microsoft Azure hitelesített Program megállapodás ahhoz, hogy fogadja el a licencfeltételeket.

## <a name="become-a-publisher"></a>A publisher válik

Az Azure piactéren közzétevő válik, a következőket kell tennie:

1. Hozzon létre egy Microsoft ID – egy e-mail-címét használja a vállalati tartományhoz, de nem egy személy a Microsoft-fiók létrehozásához. Ez az e-mail cím a Microsoft Developer Center és a Cloud Partner portálra szolgál. További információkért lásd: [Azure piactér Publisher útmutató](https://aka.ms/sellerguide).
1. Küldje el [Azure piactér jelölési űrlap](https://aka.ms/ampnomination) – **megoldás, amely közzé kívánja?**, jelölje be **kezelt alkalmazás**. Az űrlap elküldése, miután a piactér bevezetési csapat ellenőrzi, hogy az alkalmazás, és érvényesíti a kérelmet. A jóváhagyási folyamat eltarthat egy-három nap. Ha a jelölési jóváhagyják, kap egy promóciós kódot arra a regisztrációs díja a fejlesztői központban. Ha így tesz **nem** töltse ki a piactér jelölési űrlapot, a rendszer felkéri $99 regisztrációs díjat kell fizetnie.
1. Regisztrálja a [fejlesztői központ](https://developer.microsoft.com) -a Microsoft ellenőrzi, hogy a szervezet egy érvényes jogalany az ország, amelyen regisztrálva van egy érvényes adó azonosítóval. A jóváhagyási folyamat 5 – 10 nap vehet igénybe. A regisztrációs díj elkerülése érdekében használja a promóciós e-mailben a jelölési eljárás a kapott kódot. További információkért lásd: [Azure piactér Publisher útmutató](https://aka.ms/sellerguide).
1. Jelentkezzen be [Cloud Partner portálra](https://cloudpartner.azure.com) - közzétevő profilban a fejlesztői központban regisztrált fiókjában társítsa a piactér Publisher profilhoz. További információkért lásd: [Azure piactér Publisher útmutató](https://aka.ms/sellerguide).

## <a name="create-a-new-azure-application-offer"></a>Hozzon létre egy új Azure-alkalmazásokban ajánlatot

A partner portál fiók létrehozását követően készen áll a felügyelt alkalmazási ajánlat létrehozásához.

### <a name="set-up-an-offer"></a>Az ajánlat beállítása

A kezelt alkalmazás-ajánlat az ajánlat közzétevőtől származó termék osztály felel meg. Ha egy alkalmazás, amely a piactéren elérhetővé tenni kívánt új típusú, állíthat be, mint egy új ajánlat. Az ajánlat termékváltozatok gyűjteménye. Minden ajánlat saját entitás a piactéren jelenik meg.

1. Jelentkezzen be a [Cloud Partner portálra](https://cloudpartner.azure.com/).

1. Jelölje ki a bal oldali navigációs ablaktáblán, **+ új ajánlat** > **Azure alkalmazások**.

1. Az a **szerkesztő** nézetben a szükséges űrlap megjelenik. A cikk későbbi részében leírt minden egyes űrlap.

## <a name="offer-settings-form"></a>Az ajánlat beállítások képernyő

A mezőket a **beállításokat kínálnak** formában vannak:

* **Ajánlat azonosítója**: Ez az egyedi azonosító azonosítja az ajánlat egy publisher profilon belül. Ezt az Azonosítót látható termék URL-címek, Resource Manager-sablonok, és számlázási jelenti. Csak összeállítható kisbetűs alfanumerikus karaktereket és kötőjelet (-). Az azonosító nem végződhet kötőjellel. Annak legfeljebb egy legfeljebb 50 karakter hosszú lehet. Miután ajánlatot élő kerül, ez a mező zárolva van.
* **Gyártó Azonosítóját**: a legördülő lista segítségével válassza ki ezt az ajánlatot a közzétenni kívánt közzétevő-profilt. Miután ajánlatot élő kerül, ez a mező zárolva van.
* **Név**: ezt a megjelenítési nevet az előfizetéshez megjelenik a piactéren, és a portálon. Rendelkezhet egy legfeljebb 50 karakter hosszú lehet. A termék márkáját egy felismerhető nevet tartalmaz. A vállalat nevét itt nem tartalmaznak, kivéve, ha ezt forgalmazott hogyan. Ha ez az ajánlat még marketing a saját webhelyén, győződjön meg arról, hogy a neve pontosan hogyan webhelye jelenik meg.

Ha elkészült, válassza ki a **mentése** menteni az előrehaladást.

## <a name="skus-form"></a>SKU képernyő

A következő lépés az SKU hozzáadása az előfizetéshez.

Egy termékváltozata a legkisebb purchasable egysége ajánlatot. A Termékváltozat belül az azonos termék osztály (ajánlat) segítségével megkülönböztetéséhez között:

* Támogatott különböző szolgáltatások
* Az ajánlat felügyelt vagy nem felügyelt e
* Támogatott számlázási modellek

A Termékváltozat jelenik meg, az a szülő-ajánlat a piactéren. Úgy tűnik, az Azure-portálon a saját purchasable egységként.

1. Válassza ki **termékváltozatok** > **új SKU**.

1. Adjon meg egy **SKU-ID**. A SKU-ID egy egyedi azonosítót a termékváltozat ajánlatot belül. Ezt az Azonosítót látható termék URL-címek, Resource Manager-sablonok, és számlázási jelenti. Csak összeállítható kisbetűs alfanumerikus karaktereket és kötőjelet (-). Az azonosító nem végződhet kötőjellel, és azok csak a legfeljebb 50 karakter hosszú lehet. Miután ajánlatot élő kerül, ez a mező zárolva van. Az ajánlat belül több SKU lehet. Az egyes lemezképek közzé szeretné tenni a Termékváltozat van szüksége.

1. Töltse ki a **SKU részletek** szakaszt, a következő formában:

   Töltse ki a következő mezőket:

   * **Cím**: Adjon meg egy címet a Termékváltozat. Ez a cím jelenik meg, az elem a gyűjteményben.
   * **Összegző**: Adjon meg egy rövid összefoglaló a termékváltozat. Ez a szöveg a cím alatt jelenik meg.
   * **Leírás**: Adja meg a Termékváltozat kapcsolatos részletes leírását.
   * **SKU típusú**: az engedélyezett értékek a következők *kezelt alkalmazás* és *Solution Templates*. Ebben az esetben válassza a *kezelt alkalmazás*.
   * **Ország vagy régió rendelkezésre állási**: a országok, amennyiben rendelkezésre áll-e a kezelt alkalmazás kiválasztása.
   * **Árképzési**: Adjon meg egy ár az alkalmazás felügyelete szempontjából. Válassza ki a rendelkezésre álló országokban az ár beállítása előtt.

1. Adja hozzá a következő új csomagba. Töltse ki a **csomag részletei** szakaszt, a következő formában:

   Töltse ki a következő mezőket:

   * **Aktuális verzió**: Adjon meg egy verziót a feltöltött csomag. Ez a formátumúnak kell lennie `{number}.{number}.{number}{number}`.
   * **Válassza ki a csomagfájl**: Ez a csomag tartalmaz egy .zip-csomagja tömörített két szükséges fájlokat. Egy fájl egy Resource Manager-sablon, amely meghatározza a felügyelt alkalmazás üzembe helyezendő erőforrásokat. A többi fájl határozza meg a [felhasználói felület](create-uidefinition-overview.md) a fogyasztók számára a portálon keresztül a felügyelt alkalmazás telepítése. A felhasználói felületen elemeket, amelyek lehetővé teszik a fogyasztók paraméter értékének megadására, adja meg.
   * **PrincipalId**: Ez a tulajdonság az Azure Active Directory (Azure AD) azonosító egy felhasználó, a felhasználói csoport vagy az alkalmazás számára biztosított, amely az ügyfél előfizetéséhez az erőforrásokhoz való hozzáférést. A szerepkör-definíció ismerteti azokat az engedélyeket.
   * **Szerepkör-definíció**: Ez a tulajdonság esetén a beépített szerepköralapú hozzáférés-vezérlést (RBAC) szerepkörök az Azure AD által biztosított listáját. Kiválaszthatja, hogy a szerepkör, amely a legjobban megfelelő, az ügyfél nevében az erőforrásokat kezeljen.

Több engedélyeket adhat hozzá. Javasoljuk, hogy hozzon létre az Active Directory-felhasználók csoportnak, és adja meg az Azonosítót a **PrincipalId**. Ezzel a módszerrel adhat hozzá további felhasználókat a felhasználói csoport a Termékváltozat módosítása nélkül.

Az RBAC kapcsolatos további információkért lásd: [RBAC az első lépései az Azure portálon](../active-directory/role-based-access-control-what-is.md).

## <a name="marketplace-form"></a>Piactér képernyő

A piactér képernyő jelenik meg a mezők kér a [Azure piactér](https://azuremarketplace.microsoft.com) és a [Azure-portálon](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Előzetes előfizetés-azonosítók

Írjon be egy Azure-előfizetés azonosítók, az ajánlat által elérhető, hogy közzététele után. Az előzetesen megtekintett ajánlat tesztelése előtt használhatja a fehér felsorolt előfizetések live. A partnerportálon legfeljebb 100 előfizetések fehér listájának állíthat össze.

### <a name="suggested-categories"></a>Javasolt kategóriák

A listában a létrehozott ajánlatát társítható legjobb legfeljebb öt kategóriák kijelölése. Az ajánlat leképezése által biztosított termékkategóriák használt ezen kategóriák a [Azure piactér](https://azuremarketplace.microsoft.com) és a [Azure-portálon](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Piactér

A felügyelt alkalmazás összegzését jeleníti meg a következő mezőket:

![Piactér-összefoglaló](./media/publish-marketplace-app/publishvm10.png)

A **áttekintése** lapján a felügyelt alkalmazás jeleníti meg a következő mezőket:

![Piactér áttekintése](./media/publish-marketplace-app/publishvm11.png)

A **tervek + árazás** lapján a felügyelt alkalmazás jeleníti meg a következő mezőket:

![Piactér tervek](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>Azure Portal

A felügyelt alkalmazás összegzését jeleníti meg a következő mezőket:

![A portál összefoglaló](./media/publish-marketplace-app/publishvm12.png)

A felügyelt alkalmazás áttekintése jeleníti meg a következő mezőket:

![Portál áttekintése](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>Emblémáinak használatáról

Kövesse a bármely a Cloud Partner portálra a feltöltött embléma:

*   Az Azure terv egy egyszerű színpaletta rendelkezik. A szám elsődleges és másodlagos színek az embléma korlátozza.
*   A portál a témának színeket fehér és fekete. Nem használja ezeket a színeket háttérszínnek az embléma. Használjon, amely lehetővé teszi az embléma jól láthatóan elhelyezett a portálon színt. Azt javasoljuk, hogy egyszerű alapszínek. *Ha átlátszó háttérrel használ, győződjön meg arról, hogy az embléma és a szöveg nem fehér, fekete, vagy a kék.*
*   Az embléma színátmenetes hátterének nem használja.
*   Az embléma, még a vállalat vagy a márka neve ne helyezze szövegét. Az embléma megjelenését és működését legyen egyszerű és átmenetek elkerülése érdekében.
*   Ellenőrizze, hogy az embléma nincs archiválva a felhőbe.

#### <a name="hero-logo"></a>Hero-embléma

A kiemelt embléma nem kötelező megadni. A közzétevő nem szeretné kiemelt embléma feltöltéséhez. A kiemelt ikon feltöltése, után nem lehet törölni. Ekkor a partner hero ikonok piactér irányelveiről kell követnie.

Kövesse a kiemelt embléma ikon:

*   A közzétevő megjelenített név, a csomag nevét és hosszú összefoglalás ajánlat fehér jelennek meg. Ezért ne használjon egy világos színt a kiemelt ikon a háttérben. Egy fekete, fehér vagy átlátszó háttér hero ikonok nem engedélyezett.
*   Az ajánlat szerepel, miután elemek beágyazott programozott módon belül a kiemelt embléma. A beágyazott elemek közé tartozik a közzétevő megjelenített név, a csomag nevét, az ajánlat hosszú összegzése, és a **létrehozása** gombra. Ezért ne adjon meg szöveget a kiemelt embléma tervezése közben. Hagyja a jobb oldali üres területet, mert a szöveg programozott módon szerepel, hogy a hely. A szöveg üres területet 415 x 100 képpont, a jobb oldalon kell lennie. Bal oldali 370 képpont ellensúlyozza.

    ![Hero embléma – példa](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>Támogatja az űrlap

Töltse ki a **támogatja** űrlap-támogatással rendelkező kapcsolatba lép a vállalat. Ezek az információk előfordulhat, hogy mérnöki, névjegyek és ügyfél-támogatási kapcsolattartók.

## <a name="publish-an-offer"></a>Ajánlat közzététele

Után adja meg az összes szakaszok, válassza ki **közzététel** a folyamat, amely elérhetővé teszi az ajánlatot az ügyfél számára.

## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazások bemutatásáért tekintse meg a [felügyelt alkalmazások áttekintését](overview.md).
* További információ a szolgáltatáskatalógus kezelt alkalmazás közzététele: [létrehozása és a szolgáltatáskatalógus kezelt alkalmazás közzététele](publish-service-catalog-app.md).