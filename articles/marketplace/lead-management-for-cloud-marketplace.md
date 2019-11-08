---
title: A Felhőbeli piactér vezetése | Azure Marketplace és AppSource
description: Az Azure Marketplace-en és a AppSource-on közzétett ajánlatokkal és technikai összetevőkkel kapcsolatos különböző témakörök áttekintése
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/05/2018
ms.author: pabutler
ms.openlocfilehash: 047ffd983cecd9f9bd971ee3f97e15734d14ce66
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813277"
---
# <a name="lead-management-for-cloud-marketplace"></a>A Felhőbeli piactér vezető kezelése


Az ügyfelek a jó üzleti tevékenység központja. A mai termék beszerzésének átalakításakor a marketingeseknek az ügyfelek közvetlen csatlakoztatására és a kapcsolatok létrehozására kell összpontosítaniuk. A kiváló minőségű érdeklődők létrehozásának oka az értékesítési ciklus létfontosságú eszköze. Az ajánlatnak a [Cloud Partner Portal](https://cloudpartner.azure.com/)való listázása után olyan eszközök is elérhetők, amelyekkel programozott módon kaphatja meg az ügyfelek kapcsolattartási adatait, miután az ügyfél kifejezte érdeklődését, vagy üzembe helyezi a terméket a piactéren. 



## <a name="what-are-leads-in-the-marketplace"></a>Mik azok az érdeklődők a piactéren?

Az érdeklődők olyan ügyfelektől származnak, akik érdeklik, vagy üzembe helyezik termékeiket a piactéren. Függetlenül attól, hogy a termék szerepel-e az Azure Marketplace-en vagy a AppSource-on, akkor is fogadhat érdeklődőket az ügyfelektől, ha a CRM-ből a Cloud Partner Portal 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>A CRM-rendszer összekötése a felhőalapú partner portál használatával

Az érdeklődők beszerzésének megkezdéséhez az ólom-felügyeleti összekötő a Cloud Partner Portal úgy lett kialakítva, hogy könnyen csatlakoztatható legyen a CRM-adatokhoz az elérhető CRM-rendszer listájához. Mostantól könnyedén kihasználhatja a piactér által generált érdeklődőket, és nem kell egy külső rendszerrel integrálni a jelentős mérnöki erőfeszítéseket.

Az alábbiakban részletes útmutatást talál az egyes lehetséges érdeklődői célok összekapcsolásáról:

**Dynamics CRM online** - [ide kattintva](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) megtekintheti a Dynamics CRM Online konfigurálásához szükséges utasításokat az érdeklődők beszerzéséhez.

**Marketo** - [ide kattintva](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) megtudhatja, hogyan állíthatja be a Marketo érdeklődő konfigurációját az érdeklődők beolvasásához.

**Salesforce** - [ide kattintva](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) megtudhatja, hogyan állíthatja be a Salesforce-példányt az érdeklődők beszerzéséhez.

**Azure table** - [ide kattintva](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) megtudhatja, hogyan állíthatja be az Azure Storage-fiókját az érdeklődők Azure-táblázatba való beszerzéséhez.

**Https-végpont** - [ide kattintva](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) kérheti le a https-végpont beállításához szükséges utasításokat az érdeklődők beszerzéséhez.

Miután megfelelően konfigurálta az érdeklődői célhelyet, és az ajánlaton közzétette a találatot, a rendszer érvényesíti a kapcsolatokat, és elküld egy tesztelési érdeklődőt. Ha az ajánlat megtekintése előtt megtekinti az ajánlatot, akkor tesztelheti az érdeklődői kapcsolatokat is, ha az előnézeti környezetben próbálja meg saját maga is beszerezni az ajánlatot. Fontos, hogy az érdeklődői beállítások naprakészek maradjanak, hogy ne veszítse el az érdeklődőket, ezért ügyeljen arra, hogy frissítse ezeket a kapcsolatokat, ha valami módosult a végén.


### <a name="what-are-the-next-steps"></a>Mi a következő lépés?

Ha a technikai beállítás be van állítva, ezeket az érdeklődőket be kell építenie a jelenlegi értékesítési & Marketing stratégiába és működési folyamataiba. Fontos, hogy jobban megértsük a teljes értékesítési folyamatot, és szeretne szorosan együttműködni Önnel, hogy kiváló minőségű érdeklődőket és elegendő adatmennyiséget biztosítson a siker érdekében. Örömmel vesszük visszajelzését arról, hogy miként optimalizálható és növelhető az érdeklődők számára elérhető további információk, amelyek segítenek az ügyfelek sikerességében. Tudassa velünk, ha szeretne visszajelzést küldeni, és javaslatokat tesz arra, hogy az értékesítési csapata még nagyobb sikert biztosítson a piactéren.



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Gyakori vezető konfigurációs hibák a Cloud Partner portálon való közzététel során 

**Nem lehetett menteni a vezetőt a Dynamics CRM-be. Keresse meg a Dynamics CRM-fiók beállításait. LastCRMError: nem lehet bejelentkezni a Dynamics CRM-be, LastCRMException:** 

> Ha a O365-hitelesítés be van jelölve, ellenőrizze, hogy érvényes-e a felhasználói fiók és a jelszó. Ha a HRE ki van jelölve, ellenőrizze, hogy a bérlő azonosítója, az alkalmazás azonosítója és az alkalmazás titkos kulcsa megegyezik-e a HRE beállított értékkel. Kövesse az [alábbi utasításokat.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) Ha a fiók felhasználóneve/jelszava érvényes, ellenőrizze, hogy rendelkezik-e hozzáféréssel a Dynamics 365-hez, és van-e hozzárendelve licence (11-15. lépés, ha az Office-felhasználó használata esetén Azure Active Directory vagy biztonsági beállításokat használ). 

 
**Nem lehetett menteni a vezetőt a Dynamics CRM-be. A felhasználó nem rendelkezik a leadsourcecode attribútum létrehozásához szükséges engedélyekkel az érdeklődő entitásban.** 

> Az alkalmazás/felhasználó hiányzó biztonsági szerepkör (ek) Microsoft Marketplace vezető író számára. Kövesse az 11-15-es lépéseket, ha Azure Active Directory vagy biztonsági beállításokat használ, ha itt az Office-felhasználó [van](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)használatban.

**Nem lehetett menteni a vezetőt a Dynamics CRM-be a HRE használatával. Kivétel:: a bérlő nem található. Ez a példány akkor fordulhat elő, ha a bérlőhöz nem tartoznak aktív előfizetések.**  

> A vezető felügyeleti szakaszban megadott címtár-azonosító nem érvényes könyvtár. Szerezze be a címtár-azonosítót a 2. lépés (Azure Active Directory [alatt található)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) utasításai alapján 

**Nem lehetett menteni a vezetőt a Dynamics CRM-be. LastCRMError: SecLib:: a RetrievePrivilegeForUser sikertelen – egyetlen szerepkör sincs hozzárendelve a felhasználóhoz.**  

> Megoldás: rendeljen biztonsági szerepkört Microsoft Marketplace vezető írója számára. Kövesse az [itt](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) található utasításokat a biztonsági beállítások területen. 

**Nem lehetett menteni a vezetőt a Dynamics CRM-be a HRE használatával. Kivétel:: az azonosítójú alkalmazás nem található a címtárban.** 

> A vezető felügyeleti szakaszban megadott alkalmazás-azonosító nem érvényes könyvtár. Szerezze be a [címtár-azonosítót](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)a 8. lépés (Azure Active Directory alatt) utasításai alapján. 

**Nem lehetett menteni a vezetőt a Dynamics CRM-be a HRE használatával. Kivétel:: a kért bérlői azonosító érvénytelen, és nem érvényes külső tartomány formátuma** 

> A vezető felügyeleti szakaszban megadott címtár-azonosító nem érvényes könyvtár. Szerezze be a [címtár-azonosítót](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)a 2. lépés (Azure Active Directory alatt) utasításai alapján. 

**Nem lehetett menteni a vezetőt a Dynamics CRM-be a HRE használatával. Kivétel:: hiba történt a hitelesítő adatok érvényesítésekor.: Érvénytelen az ügyfél titkos kulcsa.** 

> Megoldás: Jelentkezzen be az Azure Portalra, és ellenőrizze, hogy az alkalmazás kulcsa egyezik-e a Cloud Partner Portal. Adja meg a jelszót a 10. lépésben megadott utasítás alapján ( [Azure Active Directory).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Nem lehetett menteni a vezetőt a Dynamics CRM-be. LastCRMError: a kérelem csatornájának időkorlátja lejárt, miközben a 00:02:00 utáni választ várt. Növelje meg a híváshoz átadott időtúllépési értéket a kérelemhez, vagy növelje a SendTimeout értékét a kötésben. Előfordulhat, hogy a művelethez megadott idő egy hosszú időtúllépés részét képezi.**  

> Megoldás: Jelentkezzen be Cloud Partner Portalba, ellenőrizze a kirakat részleteit > > érdeklődő célhelyét > > URL-címet, ellenőrizze, hogy érvényes-e a dinamikus CRM-példány

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Mik azok az érdeklődők, és miért fontosak a piactéren a kiadók számára?** 

Az érdeklődők olyan ügyfelek, akik a piactéren telepítik termékeiket. Függetlenül attól, hogy a termék az [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) -en vagy a [AppSource](https://appsource.microsoft.com/)van-e felsorolva, fogadhat olyan ügyfeleket, akik érdeklődnek a termék iránt, ha a vezető célhelyet az ajánlatában állította be.  


**Hol kaphatok segítséget az érdeklődői cél beállításakor?** 

A dokumentációt itt találja: [ügyfél-érdeklődők beszerzése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) vagy támogatási jegy beküldése a aka.MS/marketplacepublishersupport-on az ajánlat típusa és az érdeklődő kezelése lehetőségre. 



**Meg kell-e adni egy érdeklődői célhelyet, hogy közzé lehessen tenni egy ajánlatot a piactéren?**

Igen, ha egy Contact me SaaS-alkalmazást vagy tanácsadói szolgáltatást tesz közzé.  
 


**Hogyan ellenőrizhető, hogy helyes-e az érdeklődő konfigurációja?**

Az ajánlat beállítása és az érdeklődő céljának közzététele után tegye közzé ajánlatát. Az érdeklődő ellenőrzésének lépésein a piactér egy tesztet küld az ajánlatában konfigurált vezető célhelyre. 


**Hogyan találhatom meg a tesztelési érdeklődőt?**


Keressen rá a "MSFT_TEST" kifejezésre a vezető célhelyen, íme egy példa a teszt érdeklődői adataira: 

Vállalat = MSFT_TEST_636573304831318844 

ország = USA 

Leírás = MSFT_TEST_636573304831318844 

e-mail = MSFT_TEST_636573304831318844@test.com

kódolás = UTF-8 

kódolás = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844 |\<ajánlat neve > 

OID = 00Do0000000ZHog 

telefon = 1234567890 

title = MSFT_TEST_636573304831318844 

 

**Élő ajánlatom van, de nem látok semmilyen érdeklődőt?**

Az egyes érdeklődők a kiválasztott érdeklődői célhely mezőiben átadott adattal rendelkeznek, az érdeklődők ebben a formátumban fognak megjelenni: **forrásoldali művelet | Ajánlat** 

  *Adatforrások*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Műveletek*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Kínál*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Az ügyféladatok adatai*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

További [információ a Lead info](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)alatt található. 


**Az Azure BLOBot vezető célként konfiguráltam, miért nem látom az érdeklődőt?** 

Az érdeklődő csak akkor lesz írva, ha az Azure BLOB Storage-t az érdeklődő célhelyként választja ki. Váltson át az Azure Table-re az érdeklődő valós idejű fogadásához 


**Kaptam egy e-mailt a piactérről, miért nem találom a vezetőt a CRM-ben?**  

Lehetséges, hogy a végfelhasználó e-mail-tartománya az. edu címről származik. Adatvédelmi okokból nem adunk át személyes adatokat az. edu tartományból. Támogatási jegy beküldése a aka.ms/marketplacepublishersupport-on keresztül 


 **Az Azure Table/Azure BLOBot az érdeklődői célként konfiguráltam, Hogyan tekinthetem meg az érdeklődőket?** 

Az Azure Portalon keresztül elérheti a blobot vagy a táblázatot, vagy letöltheti és telepítheti [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ingyenesen, és megtekintheti az Azure Storage-fiók táblázatait/blobait. 


**Az Azure-táblázatot vezető célként konfiguráltam, értesítést kaphatok arról, ha új érdeklődőt küld a piactér?** 

Igen, kövesse az utasításokat az Azure Table + Function a dokumentációban történő beállításához [.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) 



**Úgy konfiguráltam a Salesforce, hogy az érdeklődői célként miért nem találom az érdeklődőket?** 

Ellenőrizze, hogy a web to Lead űrlap kötelező mező-e a lista alapján. Ha igen, váltson át a mezőre egy nem kötelező szövegmezőre.  
 

**Probléma merült fel a vezető célhelyen, és néhány érdeklődőt kihagytam. Küldhetek nekem egy e-mailt?** 

A személyes azonosításra alkalmas adatokkal kapcsolatos szabályzatok miatt a nem biztonságos e-mailen keresztül nem oszthatjuk meg az érdeklődői adatokat. 



**Beállítottam-e az Azure Storage-t (BLOB/Table) a vezető célként, Mennyibe kerül?** 

A Lead Gen-adat alacsony (< 1 GB szinte minden közzétevőhöz). A költségek a kapott érdeklődők számától függenek, ha az 1 000-es érdeklődők egy hónapon belül érkeznek, a költségek körülbelül 50 centet vesznek igénybe. 
