---
title: Új felügyelt szolgáltatási ajánlat létrehozása a kereskedelmi piactéren
description: Új felügyelt szolgáltatási ajánlat létrehozása az Azure Marketplace-en való listázáshoz a partner Center kereskedelmi piactér portáljának használatával.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c82c3e0b6e6d11ccc33bf8556b06aeb04a6d37b5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147913"
---
# <a name="create-a-new-managed-service-offer"></a>Új felügyelt szolgáltatási ajánlat létrehozása

> [!IMPORTANT]
> A felügyelt szolgáltatási ajánlatok felügyeletét a Cloud Partner Portalról a partneri központba helyezi át. Amíg az ajánlatokat át nem telepíti, kövesse a [felügyelt szolgáltatás-ajánlat közzététele az Azure Marketplace](../../lighthouse/how-to/publish-managed-services-offers.md) -en című témakör útmutatásait az ajánlatok Cloud Partner Portal-ban való kezeléséhez.

A felügyelt szolgáltatás segítséget nyújt az [Azure Lighthouse](../../lighthouse/overview.md) -forgatókönyvek engedélyezéséhez. Ha egy ügyfél felügyelt szolgáltatási ajánlatot fogad, akkor az Azure-beli [delegált erőforrás-kezelésre](../../lighthouse/concepts/azure-delegated-resource-management.md)képes erőforrásokat bevezetni.

A felügyelt szolgáltatások létrehozásának megkezdéséhez először létre kell [hoznia egy partner Center-fiókot](./create-account.md) , és meg kell nyitnia a [kereskedelmi piactér irányítópultját](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), ahol a kiválasztott **Áttekintés** lap van kiválasztva. A felügyelt szolgáltatások ajánlatának közzétételéhez [ezüst vagy arany szintű Cloud platform kompetenciával](https://partner.microsoft.com/membership/cloud-platform-competency) kell rendelkeznie, vagy [Azure szakértői MSP](https://partner.microsoft.com/membership/azure-expert-msp) -nek kell lennie.

![Kereskedelmi piactér irányítópultja a partner Centerben](./media/new-offer-overview.png)

>[!Note]
> Ha egy ajánlat közzé lett téve, a partner Centerben végzett ajánlat szerkesztése csak a rendszeren történik meg, és az újbóli közzététel után tárolja az előfizetéseket. Győződjön meg arról, hogy a módosítások elvégzése után elküldi az ajánlatot közzétételre.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

Válassza az **+ új ajánlat** gombot, majd válassza a **felügyelt szolgáltatás** menüelemet. Ekkor megjelenik az **új ajánlat** párbeszédpanel.

### <a name="offer-id-and-alias"></a>Ajánlat azonosítója és alias

- **Ajánlat azonosítója**: egyedi azonosító a fiókban található összes ajánlathoz. Ez az azonosító látható lesz az ügyfelek számára a Piactéri ajánlat URL-címében. Ez az azonosító csak kisbetűkből álló alfanumerikus karaktereket (például kötőjeleket és aláhúzásokat, de nem szóközt) tartalmazhat, és legfeljebb 50 karakter hosszúságú lehet, és a **create (létrehozás**) gombra kattintva nem módosítható.  Ha például a *test-Offer-1* értéket adja meg, az ajánlat URL-címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- **Ajánlat aliasa**: az ajánlatnak a partner Centerben való hivatkozásához használt név. Ez a név nem lesz használatban a piactéren, és nem egyezik meg az ajánlat nevével és más értékekkel, amelyek megjelennek az ügyfeleknek. Ez az érték nem módosítható a **Létrehozás**gombra kattintva.

Miután megtörtént az **ajánlat-azonosító** és az **ajánlati alias**megadása, válassza a **Létrehozás**lehetőséget. Ezt követően az ajánlat különböző részein dolgozhat.

## <a name="offer-setup"></a>Ajánlat beállítása

Az **ajánlat beállítása** lap a következő információkat kéri. A mezők kitöltése után válassza a **Mentés** lehetőséget.

## <a name="connect-lead-management"></a>Az érdeklődők felügyeletének összekötése

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Vegye figyelembe, hogy a [felügyelt szolgáltatásokra vonatkozó minősítési házirendek](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)alapján szükség van egy **érdeklődő célhelyre** . Ez egy rekordot hoz létre a CRM-rendszerben minden alkalommal, amikor egy ügyfél üzembe helyezi az ajánlatot.

További információ: az [ólom kezelése – áttekintés](./commercial-marketplace-get-customer-leads.md).

Ne felejtse el **menteni** a lapot, mielőtt továbblép a következő szakaszra.

## <a name="properties"></a>Tulajdonságok

A **Tulajdonságok** lapon megadhatja az ajánlatnak a piactéren való csoportosításához használt kategóriákat, valamint az ajánlatot támogató jogi szerződéseket. A lap befejezése után válassza a **Mentés** lehetőséget.

### <a name="category"></a>Kategória

Válasszon legalább egyet, és legfeljebb öt kategóriát, amelyek az ajánlatnak a piactér megfelelő keresési területeire való elhelyezésére szolgálnak. Ügyeljen arra, hogy az ajánlat leírásában ne adja meg, hogy az ajánlat hogyan támogatja ezeket a kategóriákat.

### <a name="terms-and-conditions"></a>használati feltételei

A **feltételek és** kikötések mezőben adja meg a saját jogi feltételeit. Megadhatja azt az URL-címet is, ahol a feltételek és kikötések megtalálhatók. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

## <a name="offer-listing"></a>Ajánlati lista

Az **ajánlati lista** lapon megadhatja az ajánlathoz tartozó piactér adatait (az ajánlat nevét, leírását, képeit stb.).

> [!NOTE]
> Az ajánlat tartalmának listázása (például a leírás, a dokumentumok, a képernyőképek, a használati feltételek stb.) nem kötelező angol nyelven lennie, amennyiben az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelvű] verzióban érhető el." Azt is elfogadható, hogy egy *hasznos hivatkozási URL-címet* adjon meg, amely nem az ajánlatban szereplő tartalomban használt tartalmat tartalmazza.

### <a name="name"></a>Name (Név)

Az itt megadott név jelenik meg az ügyfelek számára az ajánlati lista címének megfelelően. Ez a mező előre fel van töltve az ajánlat- **aliashoz** megadott szöveggel az ajánlat létrehozásakor, de ez az érték módosítható. Ez a név lehet védjeggyel ellátott (és előfordulhat, hogy védjegyeket vagy szerzői jogi szimbólumokat is tartalmaz). A név nem lehet hosszabb 50 karakternél, és nem tartalmazhat hangulatjelek.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Adja meg az ajánlat rövid leírását (legfeljebb 100 karakter), amelyet a piactér keresési eredményeiben használhat.

### <a name="long-summary"></a>Hosszú összefoglalás

Adja meg az ajánlat hosszabb leírását (legfeljebb 256 karakter). Ez a hosszú összefoglalás a piactér keresési eredményei között is felhasználható.

### <a name="description"></a>Leírás

Adja meg az ajánlat hosszabb leírását (legfeljebb 3 000 karakter). Ez a leírás az ügyfelek számára jelenik meg a piactér listázásának áttekintésében. Adja meg az ajánlat értékeit, a főbb előnyöket, a kategóriát és/vagy az iparági társításokat, az alkalmazáson belüli vásárlási lehetőségeket és a szükséges közzétételeket.

Néhány tipp a Leírás írásához:  

- A Leírás első néhány mondatában egyértelműen ismertesse ajánlata értékét. Adja meg a következő elemeket az érték-kiosztásban:
  - Az ajánlat leírása
  - Az ajánlat előnyeit élvező felhasználó típusa
  - Az ajánlat címén az ügyfél igényeinek vagy fájdalmát
- Ne feledje, hogy az első néhány mondat a keresőmotor eredményei között jelenhet meg.  
- Ne használja a szolgáltatásait és funkcióit a termék értékesítéséhez. Ehelyett a megadott értékre kell összpontosítania.  
- A lehető legnagyobb mértékben használja az iparági specifikus szókincset vagy a juttatás-alapú szövegezést.

Ha szeretné, hogy az ajánlat leírása jobban megtörténjen, a Rich Text Editor használatával formázhatja a leírást.

![A Rich Text Editor használata](./media/text-editor2.png)

A Rich Text Editor használatához kövesse az alábbi utasításokat:

- A tartalom formátumának módosításához jelölje ki a formázni kívánt szöveget, és válasszon egy szövegstílus-stílust az alábbi ábrán látható módon:

     ![A Rich Text Editor használata szöveg formátumának módosításához](./media/text-editor3.png)

- Ha felsorolásjeles vagy számozott listát szeretne hozzáadni a szöveghez, használja az alábbi beállításokat:

     ![A Rich Text Editor használata a listák hozzáadásához](./media/text-editor4.png)

- A szöveg behúzásának hozzáadásához vagy eltávolításához használja az alábbi beállításokat:

     ![A Rich Text Editor használata a behúzáshoz](./media/text-editor5.png)

### <a name="privacy-policy-link"></a>Adatvédelmi szabályzat hivatkozása

Adja meg a szervezete adatvédelmi szabályzatának URL-címét (a webhelyén található). Ön felelős azért, hogy az alkalmazás megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint érvényes adatvédelmi szabályzatot biztosítson.

### <a name="useful-links"></a>Hasznos hivatkozások

Adjon meg opcionális kiegészítő online dokumentumokat a megoldásáról. További hasznos hivatkozások hozzáadásához kattintson **a + hivatkozás hozzáadása**lehetőségre.

### <a name="contact-information"></a>Kapcsolattartási adatok

Ebben a szakaszban meg kell adnia egy **támogatási kapcsolattartó** nevét, e-mail-címét és telefonszámát, valamint egy **mérnöki kapcsolattartót**. Ez az információ nem jelenik meg az ügyfelek számára, de a Microsoft számára elérhetővé válik, és a CSP-partnerek számára is biztosítható.

### <a name="support-urls"></a>Támogatási URL-címek

Ha az **Azure globális ügyfeleinek** és/vagy **Azure Government ügyfeleinek**is támogatnia kell a webhelyeket, itt adja meg ezeket az URL-címeket.

### <a name="marketplace-images"></a>Marketplace-rendszerképek

Ebben a szakaszban megadhatja azokat az emblémákat és képeket, amelyeket az ajánlat vásárlónak való megjelenítésekor használni fog. Minden képnek. png formátumúnak kell lennie.

>[!Note]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat https://upload.xboxlive.com nem blokkolja a partner központ által használt szolgáltatást.

#### <a name="marketplace-logos"></a>Piactéri emblémák

Négy embléma mérete szükséges: **kis (40x40cm)**, **közepes (90x90)**, **nagy (115x115)** és **széles (255x115)**. Kövesse az alábbi irányelveket a logókhoz:

- Az Azure arculata egyszerű színpalettát használ. Törekedjen minél kevesebb alap- és másodlagos szín használatára az emblémában.
- A portál témaszínei a fehér és a fekete. Ne használja ezeket a színeket az embléma háttérszíneként. Olyan színt használjon, amelynek hatására az embléma felkelti a figyelmet. Javasoljuk az egyszerű alapszínek használatát.
- Ha átlátszó hátteret használ, az embléma és a szöveg ne legyen fehér, fekete vagy kék.
- Az emblémának kétdimenziósnak kell érződnie, és a színátmenetek is kerülendők. Ne használjon színátmenetes hátteret az emblémában.
- Ne helyezzen el szöveget az emblémára, még a vállalat vagy a márka nevét se.
- Ügyeljen arra, hogy az embléma ne legyen széthúzva.

#### <a name="screenshots"></a>Képernyőképek

Akár öt képernyőképet is felvehet, amelyek bemutatják, hogyan működik az ajánlata. Minden képernyőképnek 1280 x 720 képpont-nek kell lennie.

#### <a name="videos"></a>Videók

Opcionálisan akár öt videót is hozzáadhat az ajánlat bemutatásához. Ezeket a videókat a YouTube és/vagy a Vimeo szolgáltatásban kell üzemeltetni. Mindegyiknél írja be a videó nevét, URL-címét és a videó miniatűr képét (1280 x 720 képpont).

#### <a name="additional-marketplace-listing-resources"></a>További erőforrások listázása a piactéren

- [Ajánlott eljárások a piactér ajánlati listáihoz](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Előzetes verzió

Mielőtt közzéteszi ajánlatát a szélesebb körű Piactéri ajánlatban, először elérhetővé kell tennie egy korlátozott előzetes verzió célközönségét. Ezzel megerősítheti, hogy az ajánlat hogyan jelenik meg az Azure piactéren, mielőtt elérhetővé tenné az ügyfelek számára. Az előzetes verzió ideje alatt a Microsoft támogatási és mérnöki csapatai is megtekinthetik az ajánlatot.

Megadhatja az előnézeti célközönséget az Azure-előfizetési azonosítók megadásával a **célközönség megtekintése** szakaszban. Akár 10 előfizetés-azonosítót is megadhat manuálisan, vagy akár 100 előfizetés-azonosítóval rendelkező. csv-fájlt is feltölthet.

Az ehhez az előfizetéshez kapcsolódó ügyfelek az Azure Marketplace-en tekinthetik meg az ajánlatot, mielőtt élőben tennének. Ügyeljen arra, hogy a saját előfizetéseit is tartalmazza, így előzetesen megtekintheti az ajánlatát.

## <a name="plan-overview"></a>A terv áttekintése

Minden ajánlatnak egy vagy több csomaggal (más néven SKU) kell rendelkeznie. Több csomagot is hozzáadhat, hogy támogassa a különböző szolgáltatáskészlet-készleteket különböző árakon, vagy hogy testre szabjon egy adott tervet az egyes ügyfelek korlátozott célközönsége számára. Az ügyfelek megtekinthetik a fölérendelt ajánlat keretében elérhető csomagokat.

A **terv áttekintése** lapon válassza az **+ új terv létrehozása**lehetőséget. Ezután adja meg a **csomag azonosítóját** és a **csomag nevét**. Mindkét érték csak kisbetűs alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhat, legfeljebb 50 karakter hosszú lehet. Előfordulhat, hogy ezek az értékek láthatók az ügyfelek számára, és az ajánlat közzététele után nem módosíthatók.

Válassza a **Létrehozás** lehetőséget, ha megadta ezeket az értékeket a tervben való munka folytatásához. Három szakaszt kell végrehajtani: a lista, a **díjszabás és a rendelkezésre állás**, valamint a **technikai konfiguráció** **megtervezése**.

### <a name="plan-listing"></a>Csomag listázása

Először adja meg a csomag **keresési eredményeinek összegzését** . Ez a csomag rövid leírása (legfeljebb 100 karakter), amelyet a piactér keresési eredményeiben használhat.

Ezután adjon meg egy **leírást** , amely részletesebb magyarázatot nyújt a tervről.

### <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

Jelenleg csak egy díjszabási modell használható a felügyelt szolgáltatási ajánlathoz: **saját licenc használata (BYOL)**. Ez azt jelenti, hogy az ügyfeleket közvetlenül az ajánlattal kapcsolatos költségekkel számoljuk fel, és a Microsoft semmilyen díjat nem számít fel Önnek.

A **terv láthatósága** szakaszban megadhatja, hogy a tervnek [privátnak](../../marketplace/private-offers.md)kell lennie. Ha nem jelöli **be a jelölőnégyzetet, a csomag** nem lesz korlátozva meghatározott ügyfelekre (vagy adott számú ügyfélre).

Ha ezt a csomagot csak bizonyos ügyfelek számára szeretné elérhetővé tenni, válassza az **Igen**lehetőséget. Ha így tesz, azonosítania kell az ügyfeleket az előfizetési azonosítók megadásával. Ezek megadhatók egyenként (legfeljebb 10 előfizetés esetén) vagy egy. csv-fájl feltöltésével (legfeljebb 10 000 előfizetésre az összes csomag esetében). Ügyeljen arra, hogy a saját előfizetéseit is tartalmazza, hogy tesztelje és érvényesítse az ajánlatot.

> [!IMPORTANT]
> Miután közzétett egy csomagot nyilvánosként, nem módosíthatja magánjellegűre. Egy privát csomag használatával szabályozhatja, hogy mely ügyfelek fogadhatják el az ajánlatot, és hogyan delegálhat erőforrásokat. Egy nyilvános csomaggal nem korlátozhatja a rendelkezésre állást bizonyos ügyfelek számára, vagy akár bizonyos számú ügyfél számára is (bár ha úgy dönt, hogy teljesen megszüntetheti a csomag értékesítését). A [delegáláshoz való hozzáférést csak akkor távolíthatja el,](../../lighthouse/how-to/remove-delegation.md) ha az ügyfél csak akkor fogadja el az **ajánlatot, ha** az ajánlat közzétételekor a [felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörét](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) beállította a **szerepkör-definícióban** . Az ügyfelet is elérheti, és megkérheti, hogy [távolítsa el a hozzáférését](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="technical-configuration"></a>Technikai konfiguráció

A terv ezen szakasza egy jegyzékfájlt hoz létre az ügyfél-erőforrások kezeléséhez szükséges engedélyezési információkkal. Ez az információ az Azure-beli [delegált erőforrás-kezelés](../../lighthouse/concepts/azure-delegated-resource-management.md)engedélyezéséhez szükséges.

Ügyeljen arra, hogy a [bérlőket, szerepköröket és felhasználókat az Azure Lighthouse-forgatókönyvekben](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) tekintse át, hogy megértse, mely szerepkörök támogatottak, valamint az engedélyek meghatározásának ajánlott eljárásai.

> [!NOTE]
> A fentiekben leírtaknak megfelelően az **engedélyezési** bejegyzések felhasználói és szerepkörei minden olyan ügyfélre érvényesek lesznek, akik megvásárolják a csomagot. Ha korlátozni szeretné egy adott ügyfél hozzáférését, közzé kell tennie egy privát csomagot a kizárólagos használatra.

#### <a name="manifest"></a>Jegyzék

Először adja meg a jegyzékfájl **verzióját** . Használja az *n. n. n* formátumot (például 1.2.5).

Ezután adja meg a **bérlői azonosítóját**. Ez a szervezet Azure Active Directory (Azure AD) bérlői azonosítójával társított GUID; Ez azt a bérlőt kezeli, amelyről el fogja érni az ügyfelek erőforrásait. Ha nem rendelkezik ezzel a hasznos megoldással, a fiók neve fölé helyezve a Azure Portal jobb felső sarkában vagy a **címtár váltása**lehetőségre kattintva.

Ha közzéteszi az ajánlat új verzióját, és egy frissített jegyzékfájlt kell létrehoznia, válassza az **+ új jegyzékfájl**lehetőséget. Ügyeljen arra, hogy az előző jegyzékfájl verziószámát növelje.

#### <a name="authorization"></a>Engedélyezés

Az engedélyek határozzák meg a felügyeleti bérlő azon entitásait, akik hozzáférhetnek a csomagot megvásárló ügyfelek erőforrásaihoz és előfizetésekhez. Ezen entitások mindegyike egy beépített szerepkörhöz van rendelve, amely bizonyos hozzáférési szinteket biztosít.

Az egyes csomagokhoz legfeljebb húsz engedély hozható létre.

> [!TIP]
> A legtöbb esetben a szerepköröket egy Azure AD-felhasználói csoporthoz vagy egy egyszerű szolgáltatáshoz szeretné rendelni, nem pedig az egyes felhasználói fiókokhoz. Ez lehetővé teszi az egyes felhasználók hozzáférésének hozzáadását vagy eltávolítását anélkül, hogy a hozzáférési követelmények változásakor frissítenie és újból közzé kellene tennie a tervet. Amikor szerepköröket rendel az Azure AD-csoportokhoz, [Győződjön meg arról, hogy a **csoport típusa** **biztonsági** , nem pedig az **Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). További javaslatokért lásd: [bérlők, szerepkörök és felhasználók az Azure Lighthouse-forgatókönyvekben](../../lighthouse/concepts/tenants-users-roles.md).

Minden egyes **engedélyezéshez**meg kell adnia a következőket. Ezután több felhasználó és szerepkör-definíció hozzáadásához több alkalommal is kiválaszthatja az **Engedélyezés** lehetőséget.

- **Azure ad-objektum azonosítója**: egy felhasználó, felhasználói csoport vagy alkalmazás Azure ad-azonosítója, amely bizonyos engedélyeket (a szerepkör-definícióban meghatározott módon) biztosít az ügyfelek erőforrásai számára.
- Az **Azure ad-objektum megjelenített neve**: egy rövid név, amely segítségével az ügyfél megismerheti az engedélyezés célját. Az ügyfél ezt a nevet fogja látni az erőforrások delegálásakor.
- **Szerepkör-definíció**: válassza ki az elérhető Azure ad beépített szerepkörök egyikét a listából. Ez a szerepkör határozza meg, hogy az **Azure ad-objektum azonosító** mezőjében szereplő felhasználó milyen engedélyekkel fog rendelkezni az ügyfelek erőforrásaiban. A szerepkörök leírását lásd: [beépített szerepkörök](../../role-based-access-control/built-in-roles.md) és [szerepkör-támogatás az Azure-beli delegált erőforrás-kezeléshez](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
  > [!NOTE]
  > Ahogy a megfelelő új beépített szerepköröket hozzáadjuk az Azure-hoz, itt lesznek elérhetők, de a megjelenésük előtt némi késés is előfordulhat.
- **Hozzárendelhető szerepkörök**: Ez a beállítás csak akkor jelenik meg, ha a **szerepkör-definícióban** a felhasználói hozzáférés rendszergazdája lehetőséget választotta az engedélyezéshez. Ha igen, hozzá kell adnia egy vagy több hozzárendelhető szerepkört. Az **Azure ad-objektum azonosító** mezőjében szereplő felhasználó hozzá tudja rendelni ezeket a szerepköröket a [felügyelt identitásokhoz](../../active-directory/managed-identities-azure-resources/overview.md), ami a [szervizelhető házirendek telepítéséhez](../../lighthouse/how-to/deploy-policy-remediation.md)szükséges. Vegye figyelembe, hogy a felhasználói hozzáférés-rendszergazdai szerepkörhöz tartozó egyéb engedélyek nem lesznek érvényesek erre a felhasználóra.

> [!TIP]
> Ha meg szeretné győződni arról, hogy szükség esetén [el tudja távolítani a hozzáférést a delegáláshoz](../../lighthouse/how-to/remove-delegation.md) , adjon **meg egy, a** [felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköréhez](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)beállított **szerepkör-definíciót** . Ha ez a szerepkör nincs hozzárendelve, a delegált erőforrásokat csak egy felhasználó távolíthatja el az ügyfél bérlője számára.

Miután befejezte a csomag összes részét, válassza az **+ új csomag létrehozása** lehetőséget, ahányszor további csomagokat kell létrehoznia. Amikor elkészült, válassza a **Mentés** lehetőséget.

## <a name="publish"></a>Közzététel

### <a name="submit-offer-to-preview"></a>Ajánlat beküldése az előzetes verzióra

Miután befejezte az ajánlat összes szükséges szakaszt, válassza a **Közzététel** lehetőséget a portál jobb felső sarkában. A rendszer átirányítja a **felülvizsgálat és közzététel** lapra.

Ha első alkalommal teszi közzé ezt az ajánlatot, a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát.
  - *Nincs elindítva* – azt jelenti, hogy a szakasz nem lett megérintve, és el kell végezni.
  - *Hiányos* – azt jelenti, hogy a szakasznak meg kell oldania a hibákat, vagy további információkat kell megadni. Térjen vissza a szakasz (ok) hoz, és frissítse azt.
  - *Complete (Befejezés* ) – azt jelenti, hogy a szakasz elkészült, minden szükséges adattal rendelkezik, és nincsenek hibák. Az ajánlat minden részének teljes állapotban kell lennie ahhoz, hogy el tudja küldeni az ajánlatot.
- A **minősítési megjegyzések** szakaszban adja meg a minősítési csoport tesztelési utasításait, hogy az alkalmazás megfelelően legyen tesztelve, valamint az alkalmazás megértéséhez szükséges kiegészítő megjegyzések mellett.
- Küldje el az ajánlatot közzétételre a **Submit (Küldés**) gombra kattintva. A rendszer e-mailt küld Önnek, ha az ajánlat előzetes verziója elérhető az Ön számára az áttekintéshez és a jóváhagyáshoz. Térjen vissza a partneri központba, és válassza a **Go-Live** lehetőséget az ajánlat nyilvános közzétételére (vagy ha egy privát ajánlatra a privát közönség számára).

### <a name="customer-experience-and-offer-management"></a>Felhasználói élmény és ajánlat-kezelés

Amikor egy ügyfél üzembe helyezi az ajánlatot, az Azure-beli [delegált erőforrás-kezelésre](../../lighthouse/concepts/azure-delegated-resource-management.md)vonatkozó előfizetéseket vagy erőforráscsoportokat delegálhat. A folyamattal kapcsolatos további információkért tekintse meg az ügyfél bevezetési [folyamatát](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process).

Bármikor [közzéteheti az ajánlat frissített verzióját](update-existing-offer.md) . Előfordulhat például, hogy új szerepkör-definíciót szeretne hozzáadni egy korábban közzétett ajánlathoz. Ha így tesz, a már hozzáadott ügyfelek egy ikont láthatnak a Azure Portal [**szolgáltatók**](../../lighthouse/how-to/view-manage-service-providers.md) lapján, amely lehetővé teszi, hogy a frissítés elérhető legyen. Minden ügyfél áttekintheti a módosításokat, és eldöntheti, hogy szeretné-e frissíteni az új verzióra.

## <a name="next-steps"></a>További lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
- [További tudnivalók az Azure Lighthouse-ról](../../lighthouse/overview.md)