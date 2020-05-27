---
title: Vezető felügyelet a kereskedelmi piactéren | Azure Marketplace és AppSource
description: Az Azure Marketplace-en és a AppSource-on közzétett ajánlatokkal és technikai összetevőkkel kapcsolatos különböző témakörök áttekintése
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: f8b466dca9f3af55e3c11b39b3fbdac315af3675
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798594"
---
# <a name="lead-management-for-commercial-marketplace"></a>Vezető felügyelet a kereskedelmi piactéren

Az ügyfelek a jó üzleti tevékenység központja. A mai termék beszerzésének átalakításakor a marketingeseknek az ügyfelek közvetlen csatlakoztatására és a kapcsolatok létrehozására kell összpontosítaniuk. A kiváló minőségű érdeklődők létrehozásának oka az értékesítési ciklus létfontosságú eszköze. Az ajánlatnak a [partner Centerben](https://partner.microsoft.com/)való listázása után olyan eszközök is elérhetők, amelyekkel programozott módon kaphatják meg az ügyfelek kapcsolattartási adatait, miután az ügyfél kifejezte érdeklődését, vagy üzembe helyezi a terméket a piactéren. 

## <a name="what-are-leads-in-the-marketplace"></a>Mik azok az érdeklődők a piactéren?

Az érdeklődők olyan ügyfelektől származnak, akik érdeklik, vagy üzembe helyezik termékeiket a piactéren. Függetlenül attól, hogy a termék szerepel-e az Azure Marketplace-en vagy a AppSource-on, akkor is fogadhat érdeklődőket az ügyfelektől, ha a CRM-ből a partner Centerben lévő listára (k) megfelelően beállította. 

## <a name="how-to-connect-your-crm-system-with-partner-center"></a>A CRM rendszer összekapcsolásának módja a partneri központtal

Az érdeklődők beszerzésének megkezdéséhez az ólom-felügyeleti összekötő a partner Centerben úgy lett kialakítva, hogy könnyen csatlakoztatható legyen a CRM-adatokhoz az elérhető CRM-rendszer listájához. Mostantól könnyedén kihasználhatja a piactér által generált érdeklődőket, és nem kell egy külső rendszerrel integrálni a jelentős mérnöki erőfeszítéseket.

Az alábbiakban részletes útmutatást talál az egyes lehetséges érdeklődői célok összekapcsolásáról:

**Dynamics CRM Online** – tekintse meg a Dynamics CRM Online-hoz való bevezetésének konfigurálására vonatkozó útmutatást a Dynamics [365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) .

**Marketo** – lásd: az [érdeklődők felügyeletének konfigurálása a Marketo-ben](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) az érdeklődők beolvasásához szükséges Marketo-alapú konfiguráció beállításához.

**Salesforce** – lásd: az [érdeklődők felügyeletének konfigurálása a Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md) számára, útmutatás a Salesforce-példány beállításához az érdeklődők beszerzéséhez.

**Azure-tábla** – lásd: az [érdeklődők felügyeletének beállítása egy Azure-táblázat segítségével](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) , amely útmutatást nyújt az Azure Storage-fiók létrehozásához az érdeklődők Azure-táblázatban való beszerzéséhez.

**Https-végpont** – lásd: az [érdeklődők felügyeletének konfigurálása HTTPS-végponttal](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) , útmutatás a https-végpont beállításához az érdeklődők beszerzéséhez.

Miután megfelelően konfigurálta az érdeklődői célhelyet, és az ajánlaton közzétette a találatot, a rendszer érvényesíti a kapcsolatokat, és elküld egy tesztelési érdeklődőt. Ha az ajánlat megtekintése előtt megtekinti az ajánlatot, akkor tesztelheti az érdeklődői kapcsolatokat is, ha az előnézeti környezetben próbálja meg saját maga is beszerezni az ajánlatot. Fontos, hogy az érdeklődői beállítások naprakészek maradjanak, hogy ne veszítse el az érdeklődőket, ezért ügyeljen arra, hogy frissítse ezeket a kapcsolatokat, ha valami módosult a végén.

### <a name="what-are-the-next-steps"></a>Mi a következő lépés?

A technikai beállítás bevezetését követően érdemes beépíteni ezeket az érdeklődőket az aktuális értékesítési & marketing stratégiába és működési folyamataiba. Fontos, hogy jobban megértsük a teljes értékesítési folyamatot, és szeretne szorosan együttműködni Önnel, hogy kiváló minőségű érdeklődőket és elegendő adatmennyiséget biztosítson a siker érdekében. Örömmel vesszük visszajelzését arról, hogy miként optimalizálható és növelhető az érdeklődők számára elérhető további információk, amelyek segítenek az ügyfelek sikerességében. Tudassa velünk, ha szeretne visszajelzést küldeni, és javaslatokat tesz arra, hogy az értékesítési csapata még nagyobb sikert biztosítson a piactéren.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a><a id="publishing-config-errors"></a>Gyakori vezető konfigurációs hibák a partner Centerben való közzététel során

**Nem lehetett menteni a vezetőt a Dynamics CRM-be. Keresse meg a Dynamics CRM-fiók beállításait. LastCRMError: nem lehet bejelentkezni a Dynamics CRM-be, LastCRMException:** 

> Ha a O365-hitelesítés be van jelölve, ellenőrizze, hogy érvényes-e a felhasználói fiók és a jelszó. Ha a HRE ki van jelölve, ellenőrizze, hogy a bérlő azonosítója, az alkalmazás azonosítója és az alkalmazás titkos kulcsa megegyezik-e a HRE beállított értékkel. Kövesse az [alábbi utasításokat.](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) Ha a fiók felhasználóneve/jelszava érvényes, ellenőrizze, hogy rendelkezik-e hozzáféréssel a Dynamics 365-hez, és van-e hozzárendelve licence (11-15. lépés, ha az Office-felhasználó használata esetén Azure Active Directory vagy biztonsági beállításokat használ). 

**Nem lehetett menteni a vezetőt a Dynamics CRM-be. A felhasználó nem rendelkezik a leadsourcecode attribútum létrehozásához szükséges engedélyekkel az érdeklődő entitásban.** 

> Az alkalmazás/felhasználó hiányzó biztonsági szerepkör (ek) Microsoft Marketplace vezető író számára. Kövesse az 11-15-es lépéseket, ha Azure Active Directory vagy biztonsági beállításokat használ, ha itt az Office-felhasználó [van](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)használatban.

**Nem lehetett menteni a vezetőt a Dynamics CRM-be a HRE használatával. Kivétel:: a bérlő nem található. Ez a példány akkor fordulhat elő, ha a bérlőhöz nem tartoznak aktív előfizetések.**  

> A vezető felügyeleti szakaszban megadott címtár-azonosító nem érvényes könyvtár. Szerezze be a címtár-azonosítót a 2. lépésben (Azure Active Directory alatt található utasítások [alapján).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)

**Nem lehetett menteni a vezetőt a Dynamics CRM-be. LastCRMError: SecLib:: a RetrievePrivilegeForUser sikertelen – egyetlen szerepkör sincs hozzárendelve a felhasználóhoz.**  

> Megoldás: rendeljen biztonsági szerepkört Microsoft Marketplace vezető írója számára. Kövesse az utasításokat [itt](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) : biztonsági beállítások.

**Nem lehetett menteni a vezetőt a Dynamics CRM-be a HRE használatával. Kivétel:: az azonosítójú alkalmazás nem található a címtárban.** 

> A vezető felügyeleti szakaszban megadott alkalmazás-azonosító nem érvényes könyvtár. Szerezze be a [címtár-azonosítót](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)a 8. lépés (Azure Active Directory alatt) utasításai alapján. 

**Nem lehetett menteni a vezetőt a Dynamics CRM-be a HRE használatával. Kivétel:: a kért bérlői azonosító érvénytelen, és nem érvényes külső tartomány formátuma** 

> A vezető felügyeleti szakaszban megadott címtár-azonosító nem érvényes könyvtár. Szerezze be a [címtár-azonosítót](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)a 2. lépés (Azure Active Directory alatt) utasításai alapján. 

**Nem lehetett menteni a vezetőt a Dynamics CRM-be a HRE használatával. Kivétel:: hiba történt a hitelesítő adatok érvényesítésekor.: Érvénytelen az ügyfél titkos kulcsa.** 

> Megoldás: Jelentkezzen be a Azure Portalba, és ellenőrizze, hogy az alkalmazás kulcsa megfelel-e a partneri központban. Adja meg a jelszót a 10. lépésben megadott utasítás alapján ( [Azure Active Directory).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Nem lehetett menteni a vezetőt a Dynamics CRM-be. LastCRMError: a kérelem csatornájának időkorlátja lejárt, miközben a 00:02:00 utáni választ várt. Növelje meg a híváshoz átadott időtúllépési értéket a kérelemhez, vagy növelje a SendTimeout értékét a kötésben. Előfordulhat, hogy a művelethez megadott idő egy hosszú időtúllépés részét képezi.**  

> Megoldás: Jelentkezzen be a partner központba, ellenőrizze a kirakat részleteit >>  >> URL-címet, és ellenőrizze, hogy érvényes-e a dinamikus CRM-példány.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Mik azok az érdeklődők, és miért fontosak a piactéren a kiadók számára?** 

Az érdeklődők olyan ügyfelek, akik a piactéren telepítik termékeiket. Függetlenül attól, hogy a termék az [Azure Marketplace](https://azuremarketplace.microsoft.com) -en vagy a [AppSource](https://appsource.microsoft.com/)van-e felsorolva, fogadhat olyan ügyfeleket, akik érdeklődnek a termék iránt, ha a vezető célhelyet az ajánlatában állította be.  

**Hol kaphatok segítséget az érdeklődői cél beállításakor?** 

A dokumentációt az [ügyfelek beszerzése](./partner-center-portal/commercial-marketplace-get-customer-leads.md) vagy támogatási jegy beküldése a [Súgó és támogatás](https://aka.ms/marketplacepublishersupport)szolgáltatásban című cikk tartalmazza. Válassza az ajánlat típusa és az érdeklődő kezelése lehetőséget. 

**Meg kell-e adni egy érdeklődői célhelyet, hogy közzé lehessen tenni egy ajánlatot a piactéren?**

Igen, ha egy Contact me SaaS-alkalmazást vagy tanácsadói szolgáltatást tesz közzé.  
 
**Hogyan ellenőrizhető, hogy helyes-e az érdeklődő konfigurációja?**

Az ajánlat beállítása és az érdeklődő céljának közzététele után tegye közzé ajánlatát. Az érdeklődő ellenőrzésének lépésein a piactér egy tesztet küld az ajánlatában konfigurált vezető célhelyre. 

**Hogyan találhatom meg a tesztelési érdeklődőt?**

Keressen rá a "MSFT_TEST" kifejezésre a vezető célhelyen, íme egy példa a teszt érdeklődői adataira: 

Vállalat = MSFT_TEST_636573304831318844 

ország = USA 

Leírás = MSFT_TEST_636573304831318844 

e-mail =MSFT_TEST_636573304831318844@test.com

kódolás = UTF-8 

kódolás = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844 | \< Ajánlat neve> 

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

    "Create" - This is inside Azure portal only and is whenever a customer purchases your offer to their account. 

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

További [információ a Lead info](./partner-center-portal/commercial-marketplace-get-customer-leads.md)alatt található. 

**Az Azure BLOBot vezető célként konfiguráltam, miért nem látom az érdeklődőt?** 

Az érdeklődő csak akkor lesz írva, ha az Azure BLOB Storage-t az érdeklődő célhelyként választja ki. Váltson át az Azure Table-re az érdeklődő valós idejű fogadásához.

**Kaptam egy e-mailt a piactérről, miért nem találom a vezetőt a CRM-ben?**  

Lehetséges, hogy a végfelhasználó e-mail-tartománya az. edu címről származik. Adatvédelmi okokból nem adunk át személyes azonosításra alkalmas adatokat az. edu tartományból. Támogatási jegy küldése a [Súgó és támogatás](https://aka.ms/marketplacepublishersupport)szolgáltatásban.

**Az Azure Table/Azure BLOBot az érdeklődői célként konfiguráltam, Hogyan tekinthetem meg az érdeklődőket?** 

A blobot vagy a táblázatot elérheti Azure Portalból, vagy ingyenesen letöltheti és telepítheti [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) az Azure Storage-fiók táblái/Blobok megtekintésére is. 

**Az Azure-táblázatot vezető célként konfiguráltam, értesítést kaphatok arról, ha új érdeklődőt küld a piactér?** 

Igen, kövesse az utasításokat az Azure Table + Function a dokumentációban történő beállításához [.](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) 

**Úgy konfiguráltam a Salesforce, hogy az érdeklődői célként miért nem találom az érdeklődőket?**

Ellenőrizze, hogy a web to Lead űrlap kötelező mező-e a lista alapján. Ha igen, váltson át a mezőre egy nem kötelező szövegmezőre.  
 
**Probléma merült fel a vezető célhelyen, és néhány érdeklődőt kihagytam. Küldhetek nekem egy e-mailt?** 

Adatvédelmi szabályzatok miatt a nem biztonságos e-mailen keresztül nem oszthatjuk meg az érdeklődői adatokat. 

**Beállítottam-e az Azure Storage-t (BLOB/Table) a vezető célként, Mennyibe kerül?** 

A Lead Gen-adat alacsony (<1 GB szinte minden közzétevőhöz). A költségek a kapott érdeklődők számától függenek, ha az 1 000-es érdeklődők egy hónapon belül érkeznek, a költségek körülbelül 50 centet vesznek igénybe. 
