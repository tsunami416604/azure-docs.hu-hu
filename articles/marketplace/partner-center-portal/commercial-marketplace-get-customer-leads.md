---
title: Érdeklődők konfigurálása a Microsoft kereskedelmi Marketplace-ajánlatból
description: Ügyfél-érdeklődők konfigurálása a Microsoft kereskedelmi piactéren.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 54c67656d7847b44c8fc83b33a4e03be3838cf76
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131086"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Érdeklődők a Marketplace-ajánlatban

Az érdeklődők a Microsoft [AppSource](https://appsource.microsoft.com) és az [Azure Marketplace](https://azuremarketplace.microsoft.com)-ről származó ajánlatait vagy üzembe helyezését érdeklik. A vásárlói érdeklődőket az ajánlat közzétételét követően kapja meg a piactéren. Ez a cikk a következőket ismerteti:

* Hogyan hozza létre a piactéren az ügyfeleket, hogy ne hagyjon ki üzleti lehetőségeket. 
* Az Ügyfélkapcsolat-kezelési (CRM) rendszernek az ajánlathoz való kapcsolódása, hogy az érdeklődők egyetlen központi helyen kezelhetők legyenek.
* Az Ön által elküldött érdeklődői adatküldés után nyomon követheti az Ön számára elérhető ügyfeleket.

## <a name="generate-customer-leads"></a>Ügyfél-érdeklődők előállítása

Az alábbi helyeken jönnek létre az érdeklődők:

- Az ügyfél beleegyezett, hogy megosztja az adatait, miután kiválasztotta a **kapcsolatot** a kereskedelmi piactéren. Ez az érdeklődő *kezdeti érdeklődést* eredményez. Az Önnel kapcsolatos információkat a termék beszerzésére vonatkozó érdeklődéssel rendelkező ügyféllel osztjuk meg. Az érdeklődő a beszerzési tölcsér felső része.

    ![Dynamics 365 Kapcsolatfelvétel](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Az ügyfél most kiválasztja a **Letöltés** lehetőséget (vagy kiválasztja a **létrehozás** elemet a [Azure Portal](https://portal.azure.com/)) az ajánlat beszerzéséhez. Ez az érdeklődő egy *aktív* érdeklődő. Az Önnel kapcsolatos információkat megosztjuk a termék üzembe helyezését megkezdő ügyféllel.

    ![SQL-Letöltés most gomb](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server létrehozás gomb](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Egy ügyfél kiválasztja a **tesztelési meghajtót** vagy az **ingyenes próbaverziót** az ajánlat kipróbálásához. A tesztelési és az ingyenes próbaverziók gyorsított lehetőséget biztosítanak az üzleti tevékenység azonnali megosztására a potenciális ügyfelekkel a belépési korlátok nélkül.

    ![Dynamics 365 Test Drive gomb](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 ingyenes próbaverzió gomb](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Kapcsolódás a CRM rendszerhez

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Az érdeklődők megismerése

Az ügyfél beszerzési folyamata során kapott összes érdeklődő meghatározott mezőkben lévő adattal rendelkezik. Az első kikeresni kívánt mező az a `LeadSource` mező, amely a következő formátumot követi: **forrás-művelet** | **ajánlat**.

**Források**: a mező értékét a rendszer az érdeklődőt létrehozó piactér alapján tölti fel. A `"AzureMarketplace"` `"AzurePortal"`lehetséges értékek:, és `"AppSource (SPZA)"`.

**Műveletek**: a mező értéke az ügyfél által az érdeklődőt létrehozó piactéren végrehajtott művelet alapján van feltöltve.

Lehetséges értékek:

- **"Ins"**: a *telepítésre*áll. Ez a művelet az Azure Marketplace-en vagy a AppSource van, amikor az ügyfél megvásárolja a terméket.
- **"PLT"**: a *partner által vezetett próbaverzióra*áll. Ez a művelet a AppSource, ha az ügyfél kiválasztja a **Kapcsolatfelvétel** lehetőséget.
- **"DNC –"**: a (z) nem *kapcsolatra*vonatkozó. Ez a művelet abban az esetben AppSource, ha az alkalmazás oldalán felsorolt partnereket kérik a kapcsolatfelvételre. Megosztunk egy értesítést arról, hogy az ügyfél szerepel az alkalmazáson, de nem kell kapcsolatba lépnie.
- **"Létrehozás"**: Ez a művelet csak a Azure Portal belül történik, és akkor jön létre, amikor egy ügyfél megvásárolja az ajánlatot a fiókjába.
- **"StartTestDrive"**: Ez a művelet csak a **tesztvezetés** beállítás esetében jön létre, amikor egy ügyfél elindítja a tesztelési meghajtót.

**Ajánlatok**: több ajánlat is lehet a kereskedelmi piactéren. A mező értékét a rendszer az érdeklődőt létrehozó ajánlat alapján tölti fel. A közzétevő AZONOSÍTÓját és az ajánlat AZONOSÍTÓját is elküldjük ebben a mezőben, és azokat az értékeket, amelyeket az ajánlatnak a piactéren való közzétételekor adott meg.

Az alábbi példák a várt formátumú `publisherid.offerid`értékeket mutatják: 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Vásárlói adatok

Az ügyfél adatait több mezőn keresztül küldik el. Az alábbi példa egy érdeklődő ügyfél-információit mutatja be:

- FirstName: John
- LastName: Smith
- E-mail: jsmith\@Microsoft.com
- Telefon: 1234567890
- Ország: Egyesült Államok
- Vállalat: Microsoft
- Cím: műszaki igazgató

>[!NOTE]
>Az előző példában szereplő összes érték mindig elérhető az egyes érdeklődők számára. Mivel az "ügyfél-érdeklődők létrehozása" című szakaszban említett lépések több lépésből állnak, az érdeklődők kezelésének legjobb módja a rekordok visszavonása és a követő ablakok személyre szabása. Így minden ügyfél megfelelő üzenetet kap, és létrehoz egy egyedi kapcsolatot.

## <a name="best-practices-for-lead-management"></a>Ajánlott eljárások az érdeklődők felügyeletéhez

- **Folyamat**: adjon meg egy egyértelmű értékesítési folyamatot, a mérföldköveket, az elemzést és a csoport törlését.
- **Minősítés**: az előfeltételek meghatározása, amely azt jelzi, hogy egy érdeklődő teljesen minősített-e. Győződjön meg arról, hogy az értékesítési vagy a marketing képviselői körültekintően vezetnek be, mielőtt a teljes értékesítési folyamaton átveszik őket.
- **Követés**: ne felejtsen nyomon követni. A tipikus tranzakció várhatóan 5 – 12 követő hívást igényel.
- **Ápolás**: táplálja az érdeklődőket, hogy minél magasabb haszonkulcsot kapjon.

## <a name="leads-frequently-asked-questions"></a>Gyakran ismételt kérdések

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Hol kaphatok segítséget az érdeklődői cél beállításakor?

Kövesse a [Kapcsolódás a CRM rendszerhez](#connect-to-your-crm-system)című szakasz lépéseit, vagy nyújtson be támogatási jegyet a [partner Center Súgó és támogatás](https://partner.microsoft.com/support/v2/?stage=1)szolgáltatásán keresztül. Ezután válassza az **ajánlat létrehozása** > lehetőséget**az ajánlati** > **érdeklődők felügyeletének beállításához**.

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Meg kell-e adni egy vezető célhelyet, hogy közzé lehessen tenni egy ajánlatot a kereskedelmi piactéren?

A válasz a közzétett ajánlat típusától függ. A szolgáltatott szoftverként (SaaS) és a Dynamics 365 Customer engagement használatával **kapcsolatba léphetnek** az összes Dynamics 365 a pénzügyi és üzemeltetési ajánlatok, a Dynamics 365 üzleti központi ajánlatai és az összes tanácsadási szolgáltatás ajánlatának listázásához. Ennek eredményeképpen szükségük van egy érdeklődői célhoz való kapcsolódásra. Ha az ajánlat típusa nem szerepel a felsorolásban, nem kötelező csatlakozni egy érdeklődő céljához. Javasoljuk, hogy állítson be egy érdeklődői célhelyet, hogy ne hagyjon ki üzleti lehetőségeket.

### <a name="how-can-i-find-the-test-lead"></a>Hogyan találhatom meg a tesztelési érdeklődőt?

`"MSFT_TEST"` Keresse meg a kifejezést az érdeklődő célhelyén. Íme egy példa a Microsoft tesztelési folyamatára:

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

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Élő ajánlatom van, de miért nem látok semmilyen érdeklődőt?

Győződjön meg arról, hogy a vezető célhoz való kapcsolódás érvényes. A partner Centerben a **Közzététel** lehetőségre kattintva elküldünk egy tesztelési érdeklődőt. Ha a tesztelési érdeklődőt látja, a rendszer érvényes lesz a kapcsolatban. Azt is tesztelheti az érdeklődői kapcsolatokat, hogy az előzetes verzióban próbálja megkeresni az ajánlat előzetes verzióját. Válassza a **Letöltés most**, **Kapcsolatfelvétel**vagy **ingyenes próbaverzió** lehetőséget a kereskedelmi piactéren.

Győződjön meg arról is, hogy a megfelelő adathalmazokat keresi. Ennek a cikknek a [megértési vezető adatai](#understand-lead-data) című szakaszában található tartalom a vezető célhelyre küldött érdeklődői adatokat ismerteti.

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Az Azure Blob Storage-t az érdeklődői célként konfiguráltam, de miért nem látom az érdeklődőt?

Az Azure Blob Storage már nem támogatott vezető célhelyként, így az ajánlat által létrehozott összes ügyfél-érdeklődő hiányzik. Váltson át a többi [érdeklődő célhelyére](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Kaptam egy e-mailt a kereskedelmi piactéren, de miért nem találom a vezetőt a CRM-ben?

Lehetséges, hogy a végfelhasználó e-mail-tartománya az. edu címről származik. Adatvédelmi okokból nem adjuk át a személyazonosításra alkalmas adatokat az. edu tartományból. Támogatási jegy beküldése a [partner Center Súgó és támogatás](https://partner.microsoft.com/support/v2/?stage=1)szolgáltatásán keresztül.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Egy Azure-táblázatot konfiguráltam vezető célként. Hogyan tekinthetem meg az érdeklődőket?

Az Azure-táblában tárolt érdeklődői adatok a Azure Portalból érhetők el. Az Azure Storage-fiók táblázatos adatai megtekintéséhez letöltheti és telepítheti az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ingyen is.

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Egy Azure-táblázatot konfiguráltam vezető célként. Kaphatok értesítést, amikor új kereskedelmi Piactéri érdeklődőt küldenek?

Igen. Kövesse a következő témakör utasításait: az [érdeklődők felügyeletének konfigurálása Azure-táblázat segítségével](./commercial-marketplace-lead-management-instructions-azure-table.md) , amely egy olyan Microsoft flow-t állít be, amely e-mailt küld, ha az Azure-táblához érdeklődőt adnak hozzá.

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Konfiguráltam Salesforce, de miért nem találom az érdeklődőket?

Ellenőrizze, hogy a webes és a vezető űrlap kötelező mező-e a kivételezési lista alapján. Ha igen, váltson a mezőre egy nem kötelező szövegmezőbe.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Probléma merült fel a vezető célhelyen, és néhány érdeklődőt kihagytam. Küldhetek nekem egy e-mailt?

A személyazonosításra alkalmas adatszabályzatok miatt a nem biztonságos e-mailen keresztül nem oszthatjuk meg az érdeklődői adatokat.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Egy Azure-táblázatot konfiguráltam vezető célként. Mennyibe kerül?

Az ólom létrehozási adatkészlete alacsony. Majdnem minden közzétevőnél kevesebb, mint 1 GB. A díj a kapott érdeklődők számától függ. Ha például az 1 000-es érdeklődők egy hónapban érkeznek, a díjak körülbelül 50 centet kapnak. További információ a Storage díjszabásáról: az [Azure Storage áttekintésének díjszabása](https://azure.microsoft.com/pricing/details/storage/).

Ha a kérdés nem válaszol, lépjen kapcsolatba Microsoft ügyfélszolgálata a [partner Center Súgó és támogatás](https://aka.ms/marketplacepublishersupport)szolgáltatásán keresztül. Ezután válassza az **ajánlat létrehozása** > lehetőséget**az ajánlati** > **érdeklődők felügyeletének beállításához**.

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Értesítő e-mailt kapok az új ügyfelek fogadásakor. Hogyan állíthatom be valaki másnak az e-mailek fogadását?

Nyissa meg az ajánlatát a partner Centerben, és nyissa meg az **ajánlat beállítása** lapot > az **érdeklődői felügyelet** > **szerkesztése**lehetőséget. Frissítse a **kapcsolattartási e-mail** mezőben szereplő e-mail-címeket.

## <a name="next-steps"></a>További lépések

A technikai beállítás bevezetését követően az aktuális értékesítési és marketing stratégia és működési folyamatok részeként építse be ezeket az érdeklődőket. Fontos, hogy jobban megértse a teljes értékesítési folyamatot, és szeretne szorosan együttműködni Önnel, hogy kiváló minőségű érdeklődőket és elegendő adatmennyiséget biztosítson a siker érdekében. Örömmel vesszük visszajelzését arról, hogy miként optimalizálható és növelhető az érdeklődők számára elérhető további információk, amelyek segítenek az ügyfelek sikerességében. Tudassa velünk, ha szeretne [visszajelzést](mailto:AzureMarketOnboard@microsoft.com) küldeni, és javaslatokat tesz arra, hogy az értékesítési csapat sikeresebb legyen a kereskedelmi piactéren.
