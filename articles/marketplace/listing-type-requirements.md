---
title: Követelmények a jegyzék típusa szerint | Azure
description: Ez a cikk ismerteti a jogosultsági feltételek et és a közzétételi követelményeket, amelyek partnerei megpróbálják megérteni, hogyan tehetik közzé az alkalmazásokat az Azure Piactéren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: dsindona
ms.openlocfilehash: c9936e9c406e262c06d9016b88f8999e46dcb917
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684361"
---
# <a name="requirements-by-listing-type"></a>Az egyes termékoldal-típusok követelményei  
A technikai és marketingtartalomra vonatkozó követelmények a kirakattól, az ajánlat típusától és a hirdetés típusától függően változnak. Tekintse át az alábbi specifikációkat a megfelelőség ellenőrzéséhez.  
1. Kirakati követelmények:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Piactér](#storefront-requirements-azure-marketplace)  
2. A listatípusra és az ajánlattípusra vonatkozó követelmények:  
    *   A listaelemtípusokról és az ajánlattípusokról a megoldás listatípusának meghatározása lapon talál további információt [a docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Kirakati követelmények: AppSource  
Az alábbi táblázat az AppSource-on való közzététel előfeltételeit ismerteti.  

| Követelmény | Részletek | Kötelező vagy ajánlott |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Az alkalmazásnak engedélyeznie kell az Azure Active Directory összevont egyszeri bejelentkezést (Azure AD összevont egyszeri egyszeri egyszeri sso) a hozzájárulás engedélyezve van.<ul> <li>Az Azure AD összevont egyszeri egyszeri bejelentkezés engedélyezéséről az egyszeri bejelentkezés konfigurálása az azure Active Directory alkalmazásgalériájában [található, docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)</li> </ul> | Kötelező |   
| ***Integráció a Microsoft Cloud Services szolgáltatással*** | Az alkalmazásnak integrálnia kell más Microsoft Cloud-szolgáltatásokkal, például a Microsoft Power BI-val, a Cortana Intelligence-szel vagy a Microsoft Azure-szolgáltatásokkal.<ul> <li>A Microsoft Cloud szolgáltatás egyik példája a tárgyak internete.</li> </ul> | Ajánlott |  
| ***Célközönség*** | Az alkalmazásnak üzletviteli felhasználók és cégtulajdonosok számára kell lennie. | Kötelező | 
| ***Szoftver szolgáltatásként (SaaS) alkalmazás üzleti célokra*** | Az alkalmazásnak meg kell felelnie az alábbi követelményeknek.<ul> <li>Üzletági SaaS-alkalmazás</li> <li>Üzleti folyamatokra összpontosítva</li> <li>Üzleti ügyfeleknek célzott</li> <li>Annak engedélyezése, hogy a felhasználók munkahelyi hitelesítő adataikkal jelentkezzenek be, például a felhasználónév és a jelszó</li> </ul> | Kötelező |  
| ***Ingyenes próbaidőszak és próbaverzió*** | Az alkalmazásnak tartalmaznia kell az alábbi lehetőségeket annak érdekében, hogy az ügyfél korlátozott ideig ingyenesen használhassa az alkalmazást.<ul> <li>Adjon `try` meg egy módszert, hogy az ügyfelek megkezdhessék az alkalmazás kipróbálását az AppSource-on belül</li> <li>Adjon `request trial` meg egy lehetőséget az AppSource-ban, hogy az ügyfelek az alkalmazás próbaverzióját kérhessék</li> </ul>Az Ön által megadott ingyenes próbaverziónak előre meghatározott időt kell biztosítania az ügyfél számára, hogy további költségek nélkül kipróbálja az alkalmazást. | Kötelező |  
| ***Könnyen konfigurálható, használatra kész megoldás*** | Az alkalmazásnak könnyen és gyorsan konfigurálhatónak és konfigurálhatónak kell lennie testreszabás nélkül. | Kötelező |  
| ***Érdeklődők kezelése*** | Engedélyezze, hogy a CRM elfogadja az érdeklődőadatokat, mielőtt érdeklődőket kapna a kirakatból.<ul> <li>Crm-ek például a Marketo, a Microsoft Dynamics vagy a Salesforce</li> </ul> | Kötelező |  
| ***Adatvédelmi szabályzat és használati feltételek*** | Az alkalmazásnak nyilvános URL-cím használatával meg kell adnia egy hivatkozást az adatvédelmi irányelvoldalhoz. A használati feltételeket meg kell adni a szövegként történő közzététel során. | Kötelező |  
| ***Támogatás*** | Az alkalmazásnak nyilvános URL-cím használatával meg kell adnia egy hivatkozást az ügyfélszolgálati oldalra. Ha az alkalmazás próbaverzió, akkor a próbaidőszak alatt további költségek nélkül kell támogatnia. | Kötelező |  

## <a name="storefront-requirements-azure-marketplace"></a>Kirakati követelmények: Azure Piactér  
Az alábbiakban az Azure Marketplace-en a hirdetéstípusok hoz a következő előfeltételeket.  

| Követelmény | Részletek | Hirdetés típusa |  
|:--- |:--- |:--- |  
| ***Részvételi politikák*** | Az alkalmazásnak követnie kell az Azure Marketplace részvételi szabályzatait.<ul> <li>A részvételi szabályzatokkal kapcsolatos további információkért látogasson el az Azure Marketplace részvételi szabályzatai oldalra, amely [azure.microsoft.com/support/legal/marketplace/participation-policies.](https://azure.microsoft.com/support/legal/marketplace/participation-policies)</li></ul> | lista<br />Transact<br />trial |  
| ***Integráció a Microsofttal*** | Az ajánlatnak a Microsoft Azure-szolgáltatástípusokat, például a számítást, a hálózatépítést vagy a tárhelyet kell használnia vagy kiterjesztenie. Az ajánlatnak igazodnia kell egy meglévő Azure Marketplace-kategóriához, például adatbázisokhoz, biztonsághoz vagy hálózatkezeléshez.<ul> <li>A Marketplace-ajánlatokról a Marketplace-alkalmazások oldalon, [azuremarketplace.microsoft.com/marketplace/apps.](https://azuremarketplace.microsoft.com/marketplace/apps)</li> </ul> | lista<br />Transact<br />trial |  
| ***Célközönség*** | Az ajánlatnak informatikai szakembereknek, felhőfejlesztőknek vagy más technikai ügyfélszerepköröknek kell lennie. | lista<br />Transact<br />trial |  
| ***Érdeklődők kezelése*** | Engedélyezze, hogy crm-je (Marketo, Microsoft Dynamics vagy Salesforce) fogadja az érdeklődőadatokat, mielőtt érdeklődőket kapna a kirakatból. | lista<br />Transact<br />trial |  
| ***Adatvédelmi szabályzat és használati feltételek*** | Az alkalmazásnak nyilvános URL-cím használatával meg kell adnia egy hivatkozást az adatvédelmi irányelvoldalhoz. A használati feltételeket meg kell adni a szövegként történő közzététel során. | lista<br />Transact<br />trial |  
| ***Támogatás*** | Az ajánlatnak nyilvános URL-cím használatával meg kell adnia az ügyfélszolgálati oldalra mutató hivatkozást. Ha ajánlata próbaverzió, akkor a próbaidőszak alatt további költségek nélkül kell támogatnia. | Transact<br />trial |    

## <a name="non-transact-listings"></a>Nem tranzakciós listák  
Ez a szakasz az összes olyan ajánlattípust ismerteti, amely nem használja a Transact listatípusát. 

### <a name="list"></a>Lista  
A Listalista típusa a következő ajánlattípusokat tartalmazza a piactéren lévő kirakatokban.  

| Csomag típusa | Kirakat | Részletek |  
|:---        |:---        |:---     |  
| Tanácsadási szolgáltatások | AppSource | Követelmények: AppSource: Lista: Tanácsadási szolgáltatások |  
| Tanácsadási szolgáltatások | Azure Piactér | Követelmények: Azure Marketplace: Lista: Tanácsadási szolgáltatások |  
| Kapcsolat | AppSource | [](#) |  
| Kapcsolat | Azure Piactér | Követelmények: AppForrás: Lista: Kapcsolat |  

#### <a name="requirements-appsource-list-consulting-service"></a>Követelmények: AppForrás: Lista: Tanácsadási szolgáltatás  

| Követelmények | Részletek |  
|:--- |:--- |  
| A szolgáltatási ajánlat jellemzői | A tanácsadó szolgálatnak meg kell felelnie az alábbi feltételeknek.<ul> <li>Fix hatókörű, rögzített időtartamú, fix árú (vagy ingyenes) elköteleződést biztosíthat.</li> <li>Orient elsősorban a pre-sales.</li> <li>Korlát egyetlen ügyfélre.</li> <li>Magatartás a helyszínen.</li> </ul> |  
| A tanácsadási szolgáltatásokra vonatkozó partneri követelmények | Ön megfelel a szolgáltatás megfelelő területén megadott feltételeknek.<table><tr><th>Megoldási terület</th><th>Feltételek</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Silver vagy Gold Cloud ügyfélkapcsolat-kezelési kompetenciával rendelkezik.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Enterprise kiadás</td><td>Rendelkezik a Silver vagy Gold Enterprise Resource Planning kompetenciával és a felhőműveletekből származó bevételeket a követő 12 hónap 25 000 usd vagy annál nagyobb területen.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Üzleti kiadás</td><td>Felhőszolgáltatóként (CSP) vagy digitális rekordpartnerként (DPOR) szolgál egy vagy több ügyfél számára.</td></tr><tr><td>Power BI</td><td>Feleljen meg a Megoldáspartner kritériumainak.</td></tr><tr><td>PowerApps</td><td>Van egy Partner Showcase megoldás.</td></tr></table><ul> <li>Az ügyfélkapcsolat-kezeléssel kapcsolatos további információkért látogasson el a [felhőalapú](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency)ügyfélkapcsolat-kezelés oldalára, amely a partner.microsoft.com/membership/cloud-customer-relationship-management-competency .</li> <li>Az erőforrás-tervezésről a vállalati erőforrás-tervezés lapon talál [partner.microsoft.com/membership/enterprise-resource-planning-competency.](https://partner.microsoft.com/membership/enterprise-resource-planning-competency)</li> <li>A kriptap-szolgáltatással kapcsolatos további információkért látogasson el a [felhőszolgáltató partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>A DPOR-ról további információt a Digitális Nyilvántartási partner és partnerszövetség oldalon talál, amely [a partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>A megoldáspartneri feltételekkel kapcsolatos további információkért látogasson el [https://partner.microsoft.com/en-us/membership/partner-incentives](https://partner.microsoft.com/en-us/membership/partner-incentives)a Megoldáspartner áttekintése és az Ösztönzők dokumentumba, amely a helyen található.</li> <li>A partnerbemutatóval kapcsolatos további információkért látogasson el a Partner Showcase oldalra, amely [a powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Követelmények: Azure Marketplace: Lista: Consulting service  

| Követelmények | Részletek |  
|:--- |:--- |  
| A szolgáltatási ajánlat jellemzői | A tanácsadó szolgálatnak meg kell felelnie az alábbi feltételeknek.<ul> <li>Fix hatókörű, rögzített időtartamú, fix árú (vagy ingyenes) elköteleződést biztosíthat.</li> <li>Orient elsősorban a pre-sales.</li> <li>Korlát egyetlen ügyfélre.</li> <li>Magatartás a helyszínen.</li> </ul> |  
| A tanácsadási szolgáltatásokra vonatkozó partneri követelmények | A szolgáltatáshoz ezüsttel vagy arannyal kell rendelkeznie az alábbi kompetenciák egyikében az adott területen. <table><tr><th>Megoldási terület</th><th>Kompetencia</th></tr><td>Felhőplatform és infrastruktúra</td><td>Felhőplatform<br />Adatközpont</td><tr><td>Alkalmazásfejlesztés és ISV</td><td>Alkalmazásfejlesztés<br />Alkalmazásintegráció<br />DevOps</td></tr><tr><td>Adatkezelés és elemzés</td><td>Adatelemzés<br />Adatplatform</td></tr></table><ul> <li>A kompetenciákról a Kompetenciák a Microsoft Partner Network lapon talál, amely [a partner.microsoft.com/membership/competencies.](https://partner.microsoft.com/membership/competencies)</li> <li>A listázásról további információt az Azure Marketplace Consulting Services [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Próbaverzió  

| Csomag típusa | Kirakat | Részletek |  
|:---        |:---        |:---     |  
| Ingyenes / SaaS próba | AppSource | Listatípusra vonatkozó követelmények: Próbaverzió |  
| Ingyenes / SaaS próba | Azure Piactér | Követelmények: Azure Marketplace: Próbaverzió: Ingyenes próbaverzió / SaaS-próbaverzió |  
| Interaktív bemutató | AppSource | Listatípusra vonatkozó követelmények: Próbaverzió |  
| Interaktív bemutató | Azure Piactér | [Követelmények: Azure Marketplace: Próbaverzió: Interaktív bemutató](#requirements-azure-marketplace-trial-interactive-demo) |  
| Tesztvezetés | AppSource | Listatípusra vonatkozó követelmények: Próbaverzió |  
| Tesztvezetés | Azure Piactér | [Követelmények: Azure Marketplace: Próba: Tesztmeghajtó](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Követelmények: Azure Piactér: Próbaverzió  

| Követelmény | Részletek |  
|:--- |:--- |  
| Ingyenes próbaidőszak és próbaverzió | Az ügyfél korlátozott ideig ingyenesen használhatja az alkalmazást.<br /><br />Az ügyfélnek nem kell licenc- vagy előfizetési díjat fizetnie az ajánlatáért vagy alkalmazásáért. Az ügyfélnek nem kell fizetnie az alapul szolgáló Microsoft-termékért vagy -szolgáltatásért. Az összes próbaverziós lehetőség telepítve van az Azure-előfizetésében. Ön kizárólagos ellenőrzése próba a költségoptimalizálás és -kezelés.<br /><br />Választhat egy ingyenes próbaverziót, interaktív demót vagy tesztvezetést. Nem számít, hogy mit választ, az ingyenes próbaverziónak előre meghatározott időt kell kínálnia az ügyfélnek, hogy további költségek nélkül kipróbálja az alkalmazást.<ul> <li>A tesztvezetés létrehozásának megkezdéséhez küldjön [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com)egy e-mailt a rendszernek.</li> </ul>Megjegyzés: Az Azure Marketplace SaaS próbaverziós élményének lehetővé kell tennie az ügyfelek számára, hogy munkahelyi hitelesítő adatokat használjanak a bejelentkezéshez.<ul> <li>További információkért látogasson el az AppSource próbaélmények szakaszára, amely [a docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Könnyen konfigurálható, használatra kész megoldás | Az alkalmazásnak könnyen és gyorsan konfigurálhatónak és konfigurálhatónak kell lennie. |  
| Elérhetőség / üzemidő | A SaaS-alkalmazásnak vagy platformnak legalább 99,9%-os uptime-mal kell rendelkeznie. |  
| Azure Active Directory | Az ajánlatnak engedélyeznie kell az Azure Active Directory (Azure AD) összevont egyszeri bejelentkezés (SSO) (Azure AD összevont egyszeri egyszeri bejelentkezés) beleegyezésével. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Követelmények: Azure Marketplace: Próbaverzió: Ingyenes próbaverzió / SaaS-próbaverzió  

| Előny | Követelmény |  
|:--- |:--- |  
| Lehetővé teszi az ügyfél számára, hogy kipróbálja a terméket, mielőtt automatikus módszerrel vásárolna a fizetett használatra való áttéréshez. Lehetővé teszi az ügyfél koncepciójának igazolását és a Microsoft értékesítési csapataival való közös elköteleződést is. | A megoldás egy virtuális gép vagy megoldás sablon.<br /><br />A megoldás egy SaaS-ajánlat, és több-bérlős SaaS-terméket kínál.<br /><br />Van egy első futás tapasztalat, hogy egy ügyfél, és gyorsan fut.<br /><br />Egyetlen bérlővel rendelkezik, de ügyfeleket ad hozzá vendégfelhasználóként. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Követelmények: Azure Marketplace: Próbaverzió: Interaktív bemutató  

| Előny | Követelmény |  
|:--- |:--- |  
| Lehetővé teszi az ügyfelek számára, hogy a megoldás működés közben, a beállítás bonyolultsága nélkül. | A megoldás összetett beállításokat igényel, amelyeket a próbaidőszakon belül nehéz lenne elérni. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Követelmények: Azure Marketplace: Próba: Tesztmeghajtó  

| Előny | Követelmény |  
|:--- |:--- |  
| Lehetővé teszi, hogy az ügyfél kipróbálja a terméket, mielőtt megvásárolná.<br /><br />A megoldás irányított használatát biztosítja az előre konfigurált beállítások használatával.<br /><br />A tesztvezetés használata további előnyökkel jár.<ul> <li>A piacon végzett felhasználói keresések 27%-át a felhasználók finomítják, hogy csak tesztmeghajtókkal rendelkező ajánlatokat jelenítsenek meg.</li> <li>A tesztvezetésekkel rendelkező ajánlatok 38%-kal több érdeklődőt generálnak, mint az ajánlatok nélkül.</li> <li>A piacon az új ügyfélszerzések 36%-a olyan ügyfelektől származik, akik tesztvezetést hajtottak végre.</li> <li>A tesztmeghajtók lehetővé teszik a Microsoft helyszíni eladói számára, hogy jobban megértsék a terméket a közös értékesítési erőfeszítésekhez.</li> </ul> | A megoldás egy virtuális gép, megoldássablon vagy SaaS-alkalmazás egyetlen bérlővel, vagy bonyolult kiépítése. <br /><br />Nincs olyan módszere, amivel a próbaidőszakot fizetős ajánlattá alakíthatja át. |  

---

## <a name="transact-specific-listings"></a>Tranzakció-specifikus listák

### <a name="transact"></a>Transact  

| Csomag típusa | Kirakat | Részletek |   
|:---        |:---        | :--- |  
| Azure-alkalmazások: Felügyelt alkalmazás | Azure Piactér | Követelmények: Azure Marketplace: Transact: Azure-alkalmazások: Felügyelt alkalmazás |  
| Azure-alkalmazások: Megoldássablon | Azure Piactér | Követelmények: Azure Marketplace: Transact: Azure-alkalmazások: Megoldássablon |  
| Containers | Azure Piactér | [Követelmények: Azure Marketplace: Transact: Container](#requirements-azure-marketplace-transact-container) |  
| SaaS alkalmazás  | Azure Piactér | [Követelmények: Azure Marketplace: Transact: SaaS-alkalmazás](#requirements-azure-marketplace-transact-saas-app) |  
| Virtuális gép | Azure Piactér | [Követelmények: Azure Marketplace: Transact: Virtuális gép](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Követelmények: Azure Marketplace: Transact: Container  

| Követelmény | Részletek |  
|:--- |:--- |  
| Számlázás és mérés | Támogassa az ingyenes vagy byol számlázási modellt. |  
| Docker-alapú lemezkép | A tárolórendszerképnek a Docker-lemezkép formátumán kell alapulnia, és le kell állítani az Azure Container Registarendszerből. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Követelmények: Azure Marketplace: Transact: SaaS-alkalmazás  

| Követelmény | Részletek |  
|:--- |:--- |  
| Számlázás és mérés | Az ajánlat ára havi átalánydíjas. A használatalapú árképzési és használati alapú *true-up* beállítások jelenleg nem támogatottak. |  
| Lemondás | Ajánlatát az ügyfél bármikor lemondhatja. |  
| Tranzakció céloldala | Az Azure társmárkás tranzakciós céloldalának üzemeltetése. A céloldal lehetővé teszi az ügyfelek számára, hogy létrehozzák és kezeljék saas-szolgáltatási fiókját. |  
| SaaS-előfizetéses API | Olyan szolgáltatást nyújt, amely együttműködik az SaaS-előfizetéssel egy felhasználói fiók és szolgáltatáscsomag létrehozásához, frissítéséhez és törléséhez. Minden kritikus API-módosítást 24 órán belül támogatni kell. A nem kritikus API-módosítások rendszeres időközönként frissülnek. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Követelmények: Azure Marketplace: Transact: Virtuális gép  

| Követelmény | Részletek |  
|:--- |:--- | 
| Számlázás és mérés | A virtuális gépnek támogatnia kell a BYOL vagy a pay-as-you-go havi számlázást. |  
| Azure-kompatibilis virtuális merevlemez (VHD) | A virtuális gépeknek Windows vagy Linux rendszerre kell épülniük.<ul> <li>A Linux virtuális merevlemez ek létrehozásáról további információt az [Azure-ban jóváhagyott Linux-disztribúciók című](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)témakörben talál.</li> <li>A Windows virtuális merevlemez ek létrehozásáról további információt az [Azure-kompatibilis virtuális merevlemez létrehozása](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)című témakörben talál.</li> </ul> |  

## <a name="next-steps"></a>További lépések
*   Keresse fel az Azure Piactér és az [AppSource Publisher Guide](./marketplace-publishers-guide.md) lapot.  

