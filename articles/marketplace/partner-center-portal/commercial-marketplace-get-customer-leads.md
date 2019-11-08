---
title: Ügyfél-érdeklődők konfigurálása | Azure piactér
description: Ügyfél-érdeklődők konfigurálása kereskedelmi piactéren.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 7ead8dee12d4376e6e1058b84a25b91c021a937c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812654"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Érdeklődők a Piactéri ajánlatból

Az érdeklődők az [Azure Marketplace](https://azuremarketplace.microsoft.com) -en vagy a [AppSource](https://appsource.microsoft.com)-on keresztül érdeklik vagy üzembe helyezhetik az ajánlatokat. A vásárlói érdeklődőket akkor kapja meg, ha az ajánlatát közzéteszi a piactéren. Ez a cikk a következőket ismerteti:

* Hogyan hozza létre a piactéren az ügyfelek az érdeklődőket, így biztosítva, hogy ne hagyjon ki üzleti lehetőségeket. 
* A CRM-et összeállíthatja az ajánlatával, így egyetlen központi helyen kezelheti az érdeklődőket.
* Ismerje meg az Ön által küldött érdeklődői adatgyűjtést, így nyomon követheti azokat az ügyfeleket, akikre kijutott.

## <a name="generate-customer-leads"></a>Ügyfél-érdeklődők előállítása

Az alábbi helyeken jönnek létre az érdeklődők:

1. Amikor egy ügyfél beleegyezett, hogy megosztják az adataikat a piactéren a "Kapcsolatfelvétel" lehetőség kiválasztása után. Ez az érdeklődő egy **kezdeti érdeklődést** eredményez, ahol megosztjuk az ügyféllel kapcsolatos információkat, akik érdeklődést adtak a termék beszerzéséhez. Az érdeklődő a beszerzési tölcsér felső része.

      ![Dynamics 365 Kapcsolatfelvétel](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Ha az ügyfél a "Letöltés most" vagy a "létrehozás" ( [Azure Portal](https://portal.azure.com/)) lehetőséget választja az ajánlat beszerzéséhez, ez az érdeklődő egy **aktív érdeklődő**, ahol megosztjuk a termék üzembe helyezését megkezdő ügyféllel kapcsolatos információkat.

    ![SQL azonnali letöltés](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server létrehozása](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Az ügyfél egy "teszt meghajtót" indít el, vagy elindítja az ajánlat ingyenes próbaverzióját. A tesztelési és az ingyenes próbaverziók gyorsított lehetőséget biztosítanak az üzleti tevékenység azonnali megosztására a potenciális ügyfelekkel a belépési korlátok nélkül.

    ![Dynamics 365 Test Drive](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 Test Drive](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Kapcsolódás a CRM rendszerhez

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Az érdeklődők megismerése

Az ügyfél beszerzési folyamata során kapott összes érdeklődő meghatározott mezőkben lévő adattal rendelkezik. A kikeresni kívánt első mező a `LeadSource` mező, amely a következő formátumot követi: **forrás-művelet** | **ajánlat**.

**Források**: a mező értékét a rendszer az érdeklődőt létrehozó piactér alapján tölti fel. A lehetséges értékek a következők: `"AzureMarketplace"`, `"AzurePortal"`és `"AppSource (SPZA)"`.

**Műveletek**: a mező értéke az ügyfél által a piactéren, az érdeklődőt generáló művelet alapján történt. 

Lehetséges értékek:

- "INS" – telepítés. Ez a művelet az Azure Marketplace-en vagy a AppSource, amikor az ügyfél megvásárolja a terméket.
- "PLT" – a partner által vezetett próbaverziót jelenti. Ez a művelet a AppSource, ha az ügyfél a kapcsolatfelvételi lehetőséget használja.
- "DNC –" – ne lépjen kapcsolatba. Ez a művelet a AppSource, ha az alkalmazás oldalán megjelenő partnert kérik a kapcsolatfelvételre. Megosztjuk a vezetőket, hogy az ügyfél az alkalmazáson keresztül szerepel, de nem kell kapcsolatba lépnie.
- "Létrehozás" – Ez a művelet csak a Azure Portal belül történik, és akkor jön létre, amikor egy ügyfél megvásárolja az ajánlatot a fiókjába.
- "StartTestDrive" – Ez a művelet csak tesztelési meghajtók esetében használható, és akkor jön létre, amikor egy ügyfél elindítja a tesztelési meghajtót.

**Ajánlatok**: több ajánlat is lehet a piactéren. A mező értékét a rendszer az érdeklődőt létrehozó ajánlat alapján tölti fel. A közzétevő AZONOSÍTÓját és az ajánlat AZONOSÍTÓját is elküldjük ebben a mezőben, és azokat az értékeket, amelyeket az ajánlatnak a piactéren való közzétételekor adott meg.

Az alábbi példák a várt formátumú értékeket mutatják `publisherid.offerid`: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Vásárlói adatok

Az ügyfél adatait több mezőn keresztül küldik el. A következő példában az érdeklődő ügyfél-információi láthatók.

- FirstName: John
- LastName: Smith
- E-mail: jsmith\@microsoft.com
- Telefon: 1234567890
- Ország: Egyesült Államok
- Vállalat: Microsoft
- Cím: műszaki igazgató

>[!Note]
>Az előző példában szereplő összes érték mindig elérhető az egyes érdeklődők számára. Mivel az ügyfél-érdeklődők szakaszban említettek szerint több lépésből áll, az érdeklődők kezelésének legjobb módja a rekordok visszavonása és a követő ablakok személyre szabása. Így minden ügyfél megfelelő üzenetet kap, és egyedi kapcsolatot hoz létre.

## <a name="best-practices-for-lead-management"></a>Ajánlott eljárások az érdeklődők felügyeletéhez

1. *Folyamat* – egyértelmű értékesítési folyamat definiálása, mérföldköveket, KPI-ket és a csoport egyértelmű tulajdonjogát.
2. *Minősítés* – előfeltételek definiálása, amely azt jelzi, hogy egy érdeklődő teljesen minősített-e. Győződjön meg arról, hogy az értékesítési és a marketing képviselői körültekintően vezetnek be a teljes értékesítési folyamaton keresztül.
3. *Követés* – ne felejtse el nyomon követni a szokásos tranzakciót, amely 5 – 12 követő hívást igényel
4. *Ápolja* az érdeklődőket, hogy minél magasabb haszonkulcsot kapjon.

## <a name="leads-frequently-asked-questions"></a>Gyakran ismételt kérdések

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Hol kaphatok segítséget az érdeklődői cél beállításakor?

[Itt](#connect-to-your-crm-system) megtalálhatja a dokumentációt, vagy beküldhet egy támogatási jegyet a aka.MS/marketplacepublishersupport-on keresztül, majd kiválaszthatja az **"ajánlat létrehozása"** → **az ajánlat típusa** → **"ólom-felügyeleti konfiguráció" lehetőséget.**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Meg kell-e adni egy érdeklődői célhelyet, hogy közzé lehessen tenni egy ajánlatot a piactéren?

A válasz a közzétett ajánlat típusától függ. A SaaS és a Dynamics 365 for Customer engagement ajánlat a "Kapcsolatfelvétel" listára, az összes Dynamics 365 for Operations-ajánlat, az összes Dynamics 365 üzleti központi ajánlat, valamint az összes tanácsadási szolgáltatáshoz való kapcsolódáshoz a vezető célhoz kell csatlakozni. Ha az ajánlat típusa nem szerepel a listáján, akkor nem szükséges. Javasoljuk azonban, hogy egy érdeklődői célt konfiguráljon, hogy ne hagyjon ki üzleti lehetőségeket.

### <a name="how-can-i-find-the-test-lead"></a>Hogyan találhatom meg a tesztelési érdeklődőt?

Keressen `"MSFT_TEST"`t a vezető célhelyen, íme egy minta-teszt a Microsofttól:

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

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Élő ajánlatom van, de nem látok semmilyen érdeklődőt?

Győződjön meg arról, hogy a vezető célhoz való kapcsolódás érvényes. Egy teszt-érdeklődőt küldünk, miután közzétette az ajánlatot a partner Centerben. Ha megjelenik a tesztelési érdeklődő, akkor a kapcsolatok érvényesek. A vezető kapcsolatot úgy is tesztelheti, hogy az előzetes verzióban próbálja meg beszerezni az ajánlat előzetes verzióját. ehhez kattintson a "Letöltés", a "Contact me" vagy az "ingyenes próbaverzió" lehetőségre a piactéren.

Győződjön meg arról is, hogy a megfelelő adathalmazt keresi. A jelen dokumentum útmutató az [érdeklődői adatokat](#understand-lead-data) tartalmazó szakaszának tartalma ismerteti a vezető célhelyre küldött érdeklődői adatokat.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Az Azure BLOBot vezető célként konfiguráltam, miért nem látom az érdeklődőt?

Az Azure Blob Lead célja már nem támogatott, így Ön nem rendelkezik az ajánlat által létrehozott összes ügyfél-érdeklődővel. Váltson át a többi [érdeklődő célhelyére](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Kaptam egy e-mailt a piactérről, miért nem találom a vezetőt a CRM-ben?

Lehetséges, hogy a végfelhasználó e-mail-tartománya az. edu címről származik. Adatvédelmi okokból nem adunk át személyes adatokat az. edu tartományból. Támogatási jegy beküldése a aka.ms/marketplacepublishersupport-on keresztül.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Az Azure-táblázatot vezető célként konfiguráltam, Hogyan tekinthetem meg az érdeklődőket?

Az Azure-táblában tárolt érdeklődői adatok az Azure Portalon érhetők el, de ingyenesen letöltheti és telepítheti az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) az Azure Storage-fiók tábláiban található adatok megtekintéséhez.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Az Azure-táblázatot vezető célként konfiguráltam, értesítést kaphatok arról, ha új érdeklődőt küld a piactér?

Igen, kövesse az utasításokat, és állítson be egy Microsoft flow-t, amely e-mailt küld, ha [a dokumentációban](./commercial-marketplace-lead-management-instructions-azure-table.md)egy érdeklődőt adnak hozzá az Azure-táblához.

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Úgy konfiguráltam a Salesforce, hogy az érdeklődői célként miért nem találom az érdeklődőket?

Ellenőrizze, hogy a "web to Lead" űrlap kötelező mező-e a lista alapján. Ha igen, váltson át a mezőre egy nem kötelező szövegmezőre.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Probléma merült fel a vezető célhelyen, és néhány érdeklődőt kihagytam. Küldhetek nekem egy e-mailt?

A személyes azonosításra alkalmas adatokkal kapcsolatos szabályzatok miatt a nem biztonságos e-mailen keresztül nem oszthatjuk meg az érdeklődői adatokat.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Az Azure table-t a vezető célhelyként konfiguráltam, Mennyibe kerül?

A Lead Gen-adat alacsony (< 1 GB szinte minden közzétevőhöz). A költségek a kapott érdeklődők számától függenek, ha az 1 000-es érdeklődők egy hónapon belül érkeznek, a költségek körülbelül 50 centet vesznek igénybe. További információ a Storage díjszabásáról: [Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

Ha a kérdés még nem válaszol, forduljon az ügyfélszolgálathoz a aka.ms/marketplacepublishersupport-on keresztül, majd válassza az **"ajánlat létrehozása"** → **az ajánlat típusa** → **"ólom-felügyeleti konfiguráció" lehetőséget.** 

## <a name="next-steps"></a>További lépések

A technikai beállítás bevezetését követően ezeket az érdeklődőket be kell építenie a jelenlegi értékesítési & Marketing stratégiába és működési folyamataiba. Fontos, hogy jobban megértsük a teljes értékesítési folyamatot, és szeretne szorosan együttműködni Önnel, hogy kiváló minőségű érdeklődőket és elegendő adatmennyiséget biztosítson a siker érdekében. Örömmel vesszük visszajelzését arról, hogy miként optimalizálható és növelhető az érdeklődők számára elérhető további információk, amelyek segítenek az ügyfelek sikerességében. Tudassa velünk, ha szeretne visszajelzést küldeni, és javaslatokat tesz arra, hogy az értékesítési csapata még nagyobb sikert [biztosítson](mailto:AzureMarketOnboard@microsoft.com) a piactéren.
