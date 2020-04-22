---
title: Vevőérdeklődők konfigurálása | Azure Piactér
description: Ügyfélérdeklődők konfigurálása a kereskedelmi piacon.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: a1ec89dfd2dda91a10f2cc00b6ca4d9d7abbf032
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731143"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Érdeklődők a Marketplace-ajánlatban

Az érdeklődők az [Azure Piactérről](https://azuremarketplace.microsoft.com) vagy az [AppSource-ból](https://appsource.microsoft.com)származó ajánlatok iránt érdeklődnek, illetve azok üzembe helyezése. Az ajánlat marketplace-en való közzétételét követően ügyfélérdeklődőket fog kapni. Ez a cikk elmagyarázza:

* Hogyan generál ja a marketplace-ajánlat az ügyfelek et, biztosítva, hogy ne szalassza el az üzleti lehetőségeket. 
* Csatlakoztassa crm-ját az ajánlatához, így egyetlen központi helyen kezelheti az érdeklődőket.
* Ismerje meg az általunk küldött érdeklődői adatokat, hogy nyomon követhesse azokat az ügyfeleket, akik megkeresik Önt.

## <a name="generate-customer-leads"></a>Vevői érdeklődők létrehozása

Az alábbiakban az érdeklődő keletkezésének helyei:

1. Amikor egy ügyfél beleegyezik, hogy megosztja az adatait, miután kiválasztotta a "Kapcsolat" lehetőséget a piactérről. Ez az érdeklődő egy **kezdeti érdeklődési** kör, ahol megosztjuk az információt arról az ügyfélről, aki érdeklődést mutatott a termék megszerzése iránt. Az érdeklődő az akvizíciós tölcsér teteje.

      ![Dynamics 365 kapcsolat](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Amikor egy ügyfél a "Get it Now" vagy a "Create" (az [Azure Portalon)](https://portal.azure.com/)lehetőséget választja az ajánlat letöltéséhez, ez az érdeklődő egy **aktív érdeklődő,** ahol megosztjuk a termék üzembe helyezését megkezdett ügyfél adatait.

    ![SQL Beszerezni most](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server létrehozás](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Az ügyfél "Test Drive"-ot vesz igénybe, vagy elindítja az ajánlat "ingyenes próbaidőszakát". A tesztmeghajtók vagy az ingyenes próbaverziók felgyorsítják a lehetőséget, hogy vállalkozását azonnal megossza a potenciális ügyfelekkel, mindenféle belépési korlát nélkül.

    ![Dynamics 365 tesztvezetés](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 tesztvezetés](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Csatlakozás a CRM-rendszerhez

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Az érdeklődők adatainak ismertetése

Az ügyfélszerzési folyamat során kapott minden érdeklődő adott mezőkben található adatokkal rendelkezik. Az első mező, hogy `LeadSource` néz ki a mező, amely követi ezt a formátumot: **Source-Action** | **Offer**.

**Források**: A mező értéke az érdeklődőt létrehozó piactér alapján kerül feltöltésre. A lehetséges `"AzureMarketplace"` `"AzurePortal"`értékek `"AppSource (SPZA)"`a , és a .

**Műveletek**: A mező értéke az ügyfél által a piactéren végrehajtott művelet alapján kerül feltöltésre, amely létrehozta az érdeklődőt. 

Lehetséges értékek:

- "INS" - telepítés. Ez a művelet az Azure Marketplace-en vagy az AppSource-on történik, amikor egy ügyfél megvásárolja a terméket.
- "PLT" - A Partner Led trial rövidítése. Ez a művelet az AppSource-on történik, ha az ügyfél a Kapcsolatfelvétel lehetőséget használja.
- "DNC" -- Ne érintkezz. Ez a művelet az AppSource-on történik, amikor egy partner, aki keresztlistára került az alkalmazás oldalán, felkérést kap a kapcsolatfelvételre. Megosztjuk a figyelmeztetést arról, hogy ez az ügyfél keresztlistára került az alkalmazásban, de nem kell velük kapcsolatba lépni.
- "Létrehozás" – Ez a művelet csak az Azure Portalon belül történik, és akkor jön létre, amikor egy ügyfél megvásárolja az ajánlatot a fiókjába.
- "StartTestDrive" – Ez a művelet csak tesztmeghajtókra szolgál, és akkor jön létre, amikor egy ügyfél elindítja a tesztvezetést.

**Ajánlatok**: Lehet, hogy több ajánlat a piacon. A mező értéke az érdeklődőt létrehozó ajánlat alapján kerül feltöltésre. A Publisher-azonosító és az ajánlatazonosító is ebben a mezőben kerül elküldésre, és az ajánlat marketplace-en való közzétételekor megadott értékek.

A következő példák a várt `publisherid.offerid`formátumú példaértékeket mutatják be: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Ügyféladatok

A vevő adatait a rendszer több mezőn keresztül küldi el. A következő példa az érdeklődőben található ügyféladatokat mutatja be.

- Keresztnév: János
- Vezetéknév: Smith
- E-mail:\@jsmith microsoft.com
- Telefon: 1234567890
- Ország: USA
- Vállalat: Microsoft
- Cím: CTO

>[!Note]
>Az előző példában nem minden adat érhető el mindig az egyes érdeklődőkhöz. Mivel az Érdeklődők körei több lépésből kap nak érdeklődőket, az érdeklődők kezelésének legjobb módja a bejegyzések másolásának törlése és a nyomon követés személyre szabása. Így minden ügyfél megkapja a megfelelő üzenetet, és ön egyedi kapcsolatot hoz létre.

## <a name="best-practices-for-lead-management"></a>Ajánlott eljárások az érdeklődőkezeléshez

1. *Folyamat* – Egyértelmű értékesítési folyamat definiálása mérföldkövekkel, kpi-kkel és a csapat tulajdonjogának egyértelműségével.
2. *Minősítés* – Előfeltételek meghatározása, amelyek azt jelzik, hogy egy érdeklődő teljesen képzett-e. Győződjön meg róla, hogy az értékesítési vagy marketingképviselők gondosan minősítik az érdeklődőket, mielőtt végigvennék őket a teljes értékesítési folyamaton.
3. *Nyomon követés* - Ne felejtsük el, hogy kövesse nyomon, várják a tipikus tranzakció igényel 5-12 nyomon követési hívások
4. *Ápolják* - ápolják a vezet, annak érdekében, hogy neked az utat a magasabb haszonkulcs.

## <a name="leads-frequently-asked-questions"></a>Gyakran ismételt kérdések et vezet

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Hol kaphatok segítséget az érdeklődő célhelyének beállításához?

Itt megtalálhatja [a](#connect-to-your-crm-system) dokumentációt, https://partner.microsoft.com/support/v2/?stage=1 vagy támogatási jegyet küldhet be, majd válassza az **"ajánlat létrehozása"** → **az ajánlat típusa** → **"érdeklődőkezelési konfiguráció" lehetőséget.**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Be kell állítanom egy érdeklődő célállomást ahhoz, hogy ajánlatot tegyek közzé a piacon?

A válasz a közzéteszáltal kínált ajánlat típusától függ. A SaaS és a Dynamics 365 for Customer Engagement felajánlja, hogy "Kapcsolatfelvétel" néven szerepel, az összes Dynamics 365 for Operations ajánlat, az összes Dynamics 365 Business Central ajánlat és minden Consulting Service ajánlat hoz létre egy érdeklődőcélhoz való csatlakozást. Ha az ajánlat típusa nem szerepel a listában, akkor nem szükséges. Javasoljuk azonban, hogy állítson be egy érdeklődő célállomását, hogy ne maradjon le az üzleti lehetőségekről.

### <a name="how-can-i-find-the-test-lead"></a>Hogyan találhatom meg a tesztvezetőt?

Keressen `"MSFT_TEST"` rá az érdeklődő célállomására, itt egy mintateszt-érdeklődő a Microsofttól:

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Van egy élő ajánlatom, de nem látok semmi nyomot?

Ellenőrizze, hogy a kapcsolat az érdeklődő célállomásához érvényes-e. A Partnerközpontban közzétett ajánlat a közzététel után küldünk Önnek egy tesztérdeklődőt. Ha látja a tesztvezetéket, akkor a kapcsolat érvényes. Az érdeklődőkeresési kapcsolatot úgy is tesztelheti, hogy megpróbálja megszerezni az ajánlat előnézetét az előnézeti lépés során, ha a "get it now", a "contacts me" vagy az "free trial" gombra kattint a piacon.

Is, győződjön meg róla, hogy keresi a megfelelő adatokat. A jelen dokumentum [Érdeklődőadatok megértése](#understand-lead-data) szakaszában található tartalom az érdeklődő célállomására küldött érdeklődőadatokat ismerteti.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Az Azure BLOB-ot állítottam be érdeklődőcélomként, miért nem látom az érdeklődőt?

Az Azure Blob érdeklődői cél már nem támogatott, így hiányzik az ajánlat által generált ügyfél érdeklődők. Váltson a többi [érdeklődőúti-beállítás bármelyikére.](./commercial-marketplace-get-customer-leads.md) 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Kaptam egy e-mailt a Marketplace-en, miért nem találom a vezető a CRM?

Lehetséges, hogy a végfelhasználó e-mail tartománya .edu-ból származik. Adatvédelmi okokból nem adunk át privát azonosításra alkalmas adatokat .edu tartományból. Támogatási jegy beküldése a keresztül. https://partner.microsoft.com/support/v2/?stage=1

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Az Azure Table-t állítottam be érdeklődőcélként, hogyan tekinthetem meg az érdeklődőket?

Az Azure-táblában tárolt érdeklődői adatokat az Azure Portalon keresztül érheti el, vagy ingyenesen letöltheti és telepítheti az [Azure Storage Explorert](https://azure.microsoft.com/features/storage-explorer/) az Azure storage-fiók táblázatainak megtekintéséhez.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Az Azure Table-t állítottam be érdeklődőcélként, értesítést kaphatok, ha a Marketplace új érdeklődőt küld?

Igen, kövesse az utasításokat egy olyan Microsoft-folyamat beállításához, amely e-mailt küld, ha egy érdeklődő tad hozzá az Azure-táblázathoz az [itt](./commercial-marketplace-lead-management-instructions-azure-table.md)található dokumentációban.

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>A Salesforce-ot állítottam be érdeklődőcélként, miért nem találom az érdeklődőket?

Ellenőrizze, hogy a "web-vezető" űrlap egy választólistán alapuló kötelező mező-e. Ha igen, váltson át a mezőre egy nem kötelező szövegmezőre.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Volt egy kis gond a vezető úti célommal, és lemaradtam néhány nyomról. Elküldhetem őket e-mailben?

A privát, azonosítható adatokra vonatkozó irányelvek miatt nem oszthatjuk meg az érdeklődői adatokat nem biztonságos e-mailben.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Az Azure Table-t állítottam be vezető célállomásként, mennyibe fog kerülni?

Az érdeklődőgen adatok alacsonyak (<1 GB szinte minden megjelenítő esetében). A költség a kapott érdeklődők számától függ, ha egy hónap alatt 1000 érdeklődő érkezik, az körülbelül 50 centbe kerül. A tárolási díjszabásról a [tárolási díjszabás című](https://azure.microsoft.com/pricing/details/storage/)témakörben talál további információt.

Ha a kérdésedre továbbra sem válaszolsz, lépj kapcsolatba az ügyfélszolgálattal aka.ms/marketplacepublishersupport keresztül, majd válaszd az **"ajánlat létrehozása"** → **az ajánlatod** → **"érdeklődőkezelési konfiguráció" lehetőséget.** 

### <a name="i-am-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-who-to-receive-these-emails"></a>E-mailes értesítéseket kapok, amikor új ügyfélérdeklődők érkeznek. Hogyan állíthatom be, hogy ki kapja ezeket az e-maileket?

Érje el ajánlatát a Partnerközpontban, és keresse meg az **Ajánlat beállítási** lapját -> **Érdeklődőkezelés** -> **szerkesztése**. Frissítse az e-mail címeket a **Kapcsolattartó e-mail mezőben.**

## <a name="next-steps"></a>További lépések

Miután a technikai beállítás megtörtént, ezeket az érdeklődőket be kell építenie a jelenlegi értékesítési & marketingstratégiájába és működési folyamataiba. Szeretnénk jobban megérteni a teljes értékesítési folyamatot, és szorosan együtt kívánunk működni Önnel a kiváló minőségű érdeklődők és elegendő adat biztosításában ahhoz, hogy sikeres legyen. Örömmel fogadjuk visszajelzését arról, hogyan optimalizálhatjuk és javíthatjuk az általunk küldött érdeklődőket további adatokkal, hogy ezek az ügyfelek sikeresek legyenek. Tudassa velünk, ha visszajelzést és javaslatokat szeretne [adni](mailto:AzureMarketOnboard@microsoft.com) annak érdekében, hogy értékesítési csapata sikeresebb legyen a Marketplace-érdeklődőkkel.
