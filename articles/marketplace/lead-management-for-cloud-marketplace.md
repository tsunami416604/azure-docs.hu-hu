---
title: Vezető menedzsment a kereskedelmi piacon | Azure Piactér és AppSource
description: Az Ajánlatok és műszaki összetevők Azure Piactéren és AppSource-ban való közzétételével kapcsolatos különböző témakörök áttekintése
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 09b02d043e970c68cdff0e3dc4f97328c9d74c84
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383776"
---
# <a name="lead-management-for-commercial-marketplace"></a>Vezető menedzsment a kereskedelmi piacon


Az ügyfelek minden jó üzlet központja. A mai termékfelvásárlások átalakítása során a marketingeseknek az ügyfelekkel való közvetlen kapcsolatra és a kapcsolat kiépítésére kell összpontosítaniuk. Ez az oka annak, hogy a kiváló minőségű érdeklődők létrehozása létfontosságú eszköz az értékesítési ciklushoz. Miután az ajánlatot a [Partner központban felsorolta,](https://partner.microsoft.com/)vannak olyan eszközök, amelyek lehetővé teszik, hogy programozott módon megkapja az ügyfél elérhetőségi adatait közvetlenül azután, hogy az ügyfél érdeklődést mutat, vagy telepíti a terméket a piacon. 



## <a name="what-are-leads-in-the-marketplace"></a>Mik azok az érdeklődők a piacon?

Az érdeklődők olyan ügyfelektől származnak, akik érdeklődnek a Piactérről, vagy üzembe helyezik a termékeit. Függetlenül attól, hogy a termék szerepel-e az Azure Marketplace-en vagy az AppSource-ban, akkor az ügyfelektől is fogadhat érdeklődőket, ha megfelelően beállította a CRM-et a Partnerközpontban található adatlap(ok)ra. 


## <a name="how-to-connect-your-crm-system-with-partner-center"></a>A CRM-rendszer csatlakoztatása a Partnerközponttal

Az érdeklődők megszerzéséhez a Partnercenter érdeklődőkezelési összekötőjét úgy tervezték, hogy könnyen csatlakoztatható legyen a CRM-adatokhoz a rendelkezésre álló CRM-rendszer listájához. Mostantól könnyedén kihasználhatja a piactér által generált érdeklődőket anélkül, hogy jelentős mérnöki erőfeszítéseket tene egy külső rendszerrel való integrációérdekében.

Az alábbiakban részletes en-step utasításokat olvashat alehetséges érdeklődői úti célok összekapcsolásához:

**Dynamics CRM Online** – Az [érdeklődőkezelés konfigurálása a Dynamics 365 for Customer Engagement alkalmazáshoz](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) című témakörben olvashat arról, hogyan konfigurálhatja a Dynamics CRM Online-t az érdeklődők levezetéséhez.

**Marketo** - [Lásd: A Marketo vezetőkezelésének konfigurálása](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) a Marketo lead configuration beállításával kapcsolatos utasításokért.

**Salesforce** – Az [érdeklődők kezelésének konfigurálása a Salesforce-hoz](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md) című témakörben található, amely a Salesforce-példány érdeklődők levezetéseként történő beállítására vonatkozó utasításokat tartalmaz.

**Azure Table** – Tekintse [meg az érdeklődőkezelés konfigurálása egy Azure-táblázat használatával](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) az Azure storage-fiók beállításával kapcsolatos utasításokat az Érdeklődők azure-táblában való levezetéséhez.

**Https Endpoint** – Tekintse meg [az érdeklődőkezelés konfigurálása HTTPS-végpont használatával](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) a Https-végpont beállítása az érdeklődők levezetése utasításokat.

Miután megfelelően konfigurálta az érdeklődő célállomását, és elérte a Közzététel az ajánlaton, érvényesítjük a kapcsolatot, és elküldjük Önnek a tesztérdeklődőt. Amikor az ajánlat megtekintése előtt éles, akkor is tesztelheti a vezető kapcsolat, ha megpróbálja megszerezni az ajánlatot magad az előnézeti környezetben. Fontos, hogy az érdeklődőbeállítások naprakészek maradjanak, hogy ne veszítsd el az érdeklődőket, ezért mindenképpen frissítsd ezeket a kapcsolatokat, ha valami megváltozott a végén.

### <a name="what-are-the-next-steps"></a>Mik a következő lépések?

Miután a technikai beállítás a helyén van, be kell építenie ezeket az érdeklődőket a jelenlegi értékesítési & marketing stratégiájába és működési folyamataiba. Szeretnénk jobban megérteni a teljes értékesítési folyamatot, és szorosan együtt kívánunk működni Önnel a kiváló minőségű érdeklődők és elegendő adat biztosításában ahhoz, hogy sikeres legyen. Örömmel fogadjuk visszajelzését arról, hogyan optimalizálhatjuk és javíthatjuk az általunk küldött érdeklődőket további adatokkal, hogy ezek az ügyfelek sikeresek legyenek. Tudassa velünk, ha visszajelzést és javaslatokat szeretne adni annak érdekében, hogy értékesítési csapata sikeresebb legyen a Marketplace-érdeklődőkkel.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a>Gyakori érdeklődőkonfigurációs hibák a Partnerközpontban való közzététel során

**Nem menthető az érdeklődő a Dynamics CRM rendszerbe. Ellenőrizze a Dynamics CRM-fiók beállításait. LastCRMError: Nem lehet bejelentkezni a Dynamics CRM, LastCRMException alkalmazásba:** 

> Ha az O365-hitelesítés lett kiválasztva, ellenőrizze, hogy a felhasználói fiók és a jelszó érvényes-e. Ha az AAD lett kiválasztva, ellenőrizze, hogy a bérlőazonosító, az alkalmazásazonosító és az alkalmazás titkos kulcsa megegyezik-e az AAD-n beállított beállítással. Kövesse [az](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)itt található utasításokat. Ha a fiók felhasználóneve/jelszava érvényes, ellenőrizze, hogy rendelkezik-e hozzáféréssel a Dynamics 365-höz, és rendelkezik licenccel (11–15. lépés, ha Az Azure Active Directoryt vagy a Biztonsági beállításokat használja, ha Office-felhasználót használ). 

**Nem menthető az érdeklődő a Dynamics CRM rendszerbe. A felhasználó nem rendelkezik létrehozási engedéllyel a leadsourcecode attribútumhoz az érdeklődő entitásban** 

> Az alkalmazás/felhasználó nem támogatja a Microsoft Piactér vezető írójának biztonsági szerepköreit. Ha office-felhasználót használ, kövesse a 11–15. [here](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)

**Nem menthető az érdeklődő a Dynamics CRM rendszerhez az AAD használatával. Kivétel:: A bérlő nem található. Ez a példány akkor fordulhat elő, ha nincsenek aktív előfizetések a bérlőhöz.**  

> Az érdeklődőkezelés szakaszban megadott címtárazonosító nem érvényes könyvtár. Kérjük, olvassa be a címtárazonosítót a [2.](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)

**Nem menthető az érdeklődő a Dynamics CRM rendszerbe. LastCRMError: SecLib::RetrievePrivilegeForUser nem sikerült - nincs szerepkör hozzárendelve a felhasználóhoz.**  

> Megoldás: Biztonsági szerepkör hozzárendelése a Microsoft Piactér vezető írójához. Kövesse az [itt](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) található utasításokat a Biztonsági beállítások csoportban.

**Nem menthető az érdeklődő a Dynamics CRM rendszerhez az AAD használatával. Kivétel:: Az azonosítóval rendelkező alkalmazás nem található a címtárban** 

> Az érdeklődőkezelés szakaszban megadott alkalmazásazonosító nem érvényes könyvtár. Kérjük, olvassa be a címtárazonosítót a 8. [here](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Nem menthető az érdeklődő a Dynamics CRM rendszerhez az AAD használatával. Kivétel:: A kért bérlői azonosító érvénytelen és nem érvényes külső tartományformátum.** 

> Az érdeklődőkezelés szakaszban megadott címtárazonosító nem érvényes könyvtár. Kérjük, olvassa be a címtárazonosítót a 2. [here](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Nem menthető az érdeklődő a Dynamics CRM rendszerhez az AAD használatával. Kivétel:: Hiba a hitelesítő adatok érvényesítése miatt.: Érvénytelen ügyféltitok van megadva.** 

> Megoldás: Jelentkezzen be az Azure Portalon, ellenőrizze, hogy az alkalmazáskulcs megegyezik-e a Partnerközpontban lévővel. Kérjük, hogy a [10.](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Nem menthető az érdeklődő a Dynamics CRM rendszerbe. LastCRMError: A kérelemcsatorna idővel több, mint várakozás a válasz után 00:02:00. Növelje a kérés hívásának átadott időtúltöltési értéket, vagy növelje a SendTimeout értékét a kötésen. A művelethez megadott idő lehet, hogy egy hosszabb időbevétel része volt.**  

> Megoldás: Jelentkezzen be a Partnerközpontba, ellenőrizze a kirakat részleteit, >> érdeklődő cél >> URL-cím, ellenőrizze, hogy érvényes dinamikus CRM-példány-e.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Mik azok az érdeklődők, és miért fontosak számomra kiadóként a Marketplace-en?** 

Az érdeklődők olyan ügyfelek, akik a Marketplace-en telepítik a termékeit. Függetlenül attól, hogy a termék szerepel-e az [Azure Marketplace-en](https://azuremarketplace.microsoft.com) vagy az [AppSource-ban,](https://appsource.microsoft.com/)akkor a termék iránt érdeklődő ügyfelek érdeklődőket kaphat, ha beállította az ajánlat vezető célhelyét.  

**Hol kaphatok segítséget az érdeklődő célhelyének beállításához?** 

Itt talál dokumentációt: [Szerezzen be ügyfélérdeklődőket,](./partner-center-portal/commercial-marketplace-get-customer-leads.md) vagy küldjön be támogatási jegyet aka.ms/marketplacepublishersupport ajánlat típusának és érdeklődőkezelésének kiválasztásával. 

**Be kell állítanom egy érdeklődő célállomását ahhoz, hogy ajánlatot tegyek közzé a Marketplace-en?**

Igen, ha a Kapcsolat i SaaS alkalmazást vagy a Tanácsadó szolgáltatásokat teszi közzé.  
 
**Hogyan győződhetek meg arról, hogy az érdeklődő konfigurációja helyes?**

Az ajánlat és az érdeklődő célállomásának beállítása után tegye közzé az ajánlatot. Az érdeklődőérvényesítési lépéssel a Marketplace egy tesztérdeklődőt küld az ajánlatban konfigurált érdeklődő célállomásához. 

**Hogyan találhatom meg a tesztvezetőt?**

Keressen rá a "MSFT_TEST" kifejezésre az érdeklődő célállomásán, itt egy mintateszt-érdeklődői nek adatai: 

vállalat = MSFT_TEST_636573304831318844 

ország = USA 

leírás = MSFT_TEST_636573304831318844 

e-mail =MSFT_TEST_636573304831318844@test.com

kódolás = UTF-8 

kódolás = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844| \<Ajánlat neve> 

oid = 00Do000000ZHog 

telefon = 1234567890 

title = MSFT_TEST_636573304831318844 

**Van egy élő ajánlatom, de nem látok semmi nyomot?**

Minden érdeklődő nek adatai lesznek átadva a kiválasztott érdeklődő célmezőiben, az érdeklődők a következő formátumban jelennek meg: **Forrás-Művelet| Ajánlat** 

  *Források:*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Műveletek:*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Kínál:*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Az ügyféladatok mintaadatai*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

További információ az [Érdeklődőink adatai](./partner-center-portal/commercial-marketplace-get-customer-leads.md)alatt. 

**Az Azure BLOB-ot állítottam be érdeklődőcélomként, miért nem látom az érdeklődőt?** 

Az érdeklődő csak akkor íródik, ha az Azure BLOB storage-t választja az érdeklődő célhelyeként. Váltson az Azure-táblára, hogy valós időben megkapja az érdeklődőt.

**Kaptam egy e-mailt a Marketplace-en, miért nem találom a vezető a CRM?**  

Lehetséges, hogy a végfelhasználó e-mail tartománya .edu-ból származik. Adatvédelmi okokból nem adunk át személyes azonosításra alkalmas adatokat .edu domainről. Támogatási [jegy](https://aka.ms/marketplacepublishersupport)beküldése .

**Az Azure Table/Azure BLOB-ot állítottam be vezető célként, hogyan tekinthetem meg az érdeklődőket?** 

A blobot vagy a táblát az Azure Portalról érheti el, vagy ingyenesen letöltheti és telepítheti [az Azure Storage Explorert](https://azure.microsoft.com/features/storage-explorer/) az Azure storage-fiók tábláinak/blobjainak megtekintéséhez. 

**Az Azure Table-t állítottam be érdeklődőcélként, értesítést kaphatok, ha a Marketplace új érdeklődőt küld?** 

Igen, kövesse az utasításokat az Azure Table + Function beállításához a dokumentációban [itt.](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) 

**A Salesforce-ot állítottam be érdeklődőcélként, miért nem találom az érdeklődőket?** 

Ellenőrizze, hogy az érdeklődőkeresési űrlap kötelező mező-e egy választólista alapján. Ha igen, váltson át a mezőre egy nem kötelező szövegmezőre.  
 
**Volt egy kis gond a vezető úti célommal, és lemaradtam néhány nyomról. Elküldhetem őket e-mailben?** 

Az adatvédelmi irányelvek miatt nem oszthatjuk meg a vezető adatokat nem biztonságos e-mailben. 

**Az Azure Storage-t (BLOB/Table) állítottam be érdeklődőcélként, mennyibe fog kerülni?** 

Az érdeklődőgen adatok alacsonyak (<1 GB szinte minden megjelenítő esetében). A költség a kapott érdeklődők számától függ, ha egy hónap alatt 1000 érdeklődő érkezik, az körülbelül 50 centbe kerül. 
