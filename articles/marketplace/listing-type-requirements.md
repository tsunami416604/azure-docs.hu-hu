---
title: Követelmények listázási típus szerint | Azure
description: Ez a cikk ismerteti a támogathatósági feltételeket és a közzétételi követelményekkel foglalkozó partnereket, amelyekkel megismerheti, hogyan tehet közzé alkalmazásokat az Azure piactéren.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: b9a2e8b37f746eb86b2e0fe6a7304cd888a293e1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823034"
---
# <a name="requirements-by-listing-type"></a>Követelmények listázási típus szerint  
A technikai és a marketing tartalomra vonatkozó követelmények a kirakat, az ajánlat típusa és a listaelem típusa szerint változnak. A megfelelőség ellenőrzéséhez tekintse át az alábbi specifikációkat.  
1. Kirakati követelmények:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. A lista típusa és az ajánlat típusára vonatkozó követelmények:  
    *   A típusokról és az ajánlati típusokról további információt a [docs.microsoft.com/Azure/Marketplace/Determine-Your-Listing-Type](./determine-your-listing-type.md)címen található megoldáshoz tartozó listaelem meghatározása című oldalon találhat.  

## <a name="storefront-requirements-appsource"></a>Kirakati követelmények: AppSource  
A következő táblázat ismerteti a AppSource való közzététel előfeltételeinek követelményeit.  

| Követelmény | Részletek | Kötelező vagy ajánlott |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Az alkalmazásnak engedélyeznie kell Azure Active Directory összevont egyszeri bejelentkezést (Azure AD összevont SSO) a beleegyezett engedéllyel.<ul> <li>Az Azure AD összevont egyszeri bejelentkezés engedélyezésével kapcsolatos további információkért tekintse meg az egyszeri bejelentkezés konfigurálása olyan alkalmazásokhoz, amelyek nem szerepelnek a Azure Active Directory [docs.microsoft.com/Azure/Active-Directory/Active-Directory-SaaS-Custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)címen található alkalmazás-katalógus lapon.</li> </ul> | Kötelező |   
| ***Integráció Microsoft Cloud szolgáltatásokkal*** | Az alkalmazásnak integrálnia kell más Microsoft Cloud szolgáltatásokkal, például a Microsoft Power BIokkal, a Cortana Intelligenceekkel vagy a Microsoft Azure szolgáltatásokkal.<ul> <li>Microsoft Cloud szolgáltatás például eszközök internetes hálózata.</li> </ul> | Ajánlott |  
| ***Célközönség*** | Az alkalmazásnak az üzletági felhasználók és a vállalkozások tulajdonosainak kell lennie. | Kötelező | 
| ***Szoftveres szolgáltatás (SaaS) alkalmazás vállalatoknak*** | Az alkalmazásnak az alábbi követelményeknek kell megfelelnie.<ul> <li>Üzletági SaaS-alkalmazás</li> <li>Üzleti folyamatok – fókuszált</li> <li>Üzleti ügyfeleknek</li> <li>Annak engedélyezése, hogy a felhasználók a munkahelyi hitelesítő adataikat használják a bejelentkezéshez, például a felhasználónevet és a jelszót</li> </ul> | Kötelező |  
| ***Ingyenes próbaidőszak és próbaidőszak*** | Az alkalmazásnak tartalmaznia kell egyet az alábbi lehetőségek közül, hogy az ügyfél korlátozott ideig ingyenesen használhassa az alkalmazást.<ul> <li>Adjon meg egy `try` metódust, így az ügyfelek a AppSource-n belül elindíthatják az alkalmazás próbaverzióját</li> <li>Adjon meg egy `request trial` lehetőséget a AppSource, így az ügyfelek az alkalmazás próbaverzióját kérhetik</li> </ul>Az Ön által megadott ingyenes próbaverziónak előre meghatározott időtartamot kell biztosítania az ügyfél számára, hogy további költség nélkül kipróbálja az alkalmazást. | Kötelező |  
| ***Könnyen konfigurálható, használatra kész megoldás*** | Az alkalmazásnak könnyen és gyorsan be kell állítania és be kell állítania a testreszabást, és nem kell beállítania. | Kötelező |  
| ***Érdeklődők kezelése*** | Engedélyezze a CRM-nek, hogy fogadja az érdeklődői adatait, mielőtt a kirakattól kapott érdeklődőket kapja.<ul> <li>CRM például a Marketo, a Microsoft Dynamics vagy a Salesforce</li> </ul> | Kötelező |  
| ***Adatvédelmi szabályzat és használati feltételek*** | Az alkalmazásnak egy nyilvános URL-cím használatával meg kell adnia egy hivatkozást az adatvédelmi szabályzat oldalára. Az Ön használati feltételeit szövegként való közzététel során kell megadni. | Kötelező |  
| ***Támogatás*** | Az alkalmazásnak egy nyilvános URL-cím használatával kell megadnia az ügyfélszolgálati oldal hivatkozását. Ha az alkalmazás próbaverziós, akkor a próbaidőszak alatt további költség nélkül kell támogatást biztosítania. | Kötelező |  

## <a name="storefront-requirements-azure-marketplace"></a>A kirakatra vonatkozó követelmények: Azure Marketplace  
Az Azure Marketplace-en az alábbi előfeltételekre vonatkozó követelmények vonatkoznak a típusok listázására.  

| Követelmény | Részletek | Listaelem típusa |  
|:--- |:--- |:--- |  
| ***Részvételi szabályzatok*** | Az alkalmazásnak követnie kell az Azure Marketplace részvételi szabályzatait.<ul> <li>A részvételi szabályzatokkal kapcsolatos további információkért tekintse meg az Azure Marketplace részvételi szabályzatait ismertető oldalt, amely a következő címen található: [Azure.microsoft.com/support/Legal/Marketplace/Participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | lista<br />Transact<br />trial |  
| ***Integráció a Microsofttal*** | Ajánlatának olyan Microsoft Azure-szolgáltatásokat kell használnia vagy kiterjesztenie, mint a számítás, a hálózatkezelés vagy a tárterület. Ajánlatának egy meglévő Azure Marketplace-kategóriához kell igazodnia, például adatbázisokhoz, biztonsághoz vagy hálózatkezeléshez.<ul> <li>A Piactéri ajánlatokkal kapcsolatos további információkért látogasson el a piactér alkalmazások oldalára, amely a következő címen található: [azuremarketplace.microsoft.com/Marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | lista<br />Transact<br />trial |  
| ***Célközönség*** | Ajánlatának informatikai szakembereknek, felhőalapú fejlesztőknek vagy más technikai felhasználói szerepköröknek kell lennie. | lista<br />Transact<br />trial |  
| ***Érdeklődők kezelése*** | Lehetővé teheti a CRM (Marketo, Microsoft Dynamics vagy Salesforce) számára, hogy fogadja az érdeklődőket, mielőtt az érdeklődőket megkapja a kirakatból. | lista<br />Transact<br />trial |  
| ***Adatvédelmi szabályzat és használati feltételek*** | Az alkalmazásnak egy nyilvános URL-cím használatával meg kell adnia egy hivatkozást az adatvédelmi szabályzat oldalára. Az Ön használati feltételeit szövegként való közzététel során kell megadni. | lista<br />Transact<br />trial |  
| ***Támogatás*** | Az ajánlatnak egy nyilvános URL-cím használatával meg kell adnia egy hivatkozást az ügyfélszolgálati oldalára. Ha az ajánlat próbaverziós, akkor a próbaidőszak alatt díjmentesen kell támogatást nyújtania. | Transact<br />trial |    

## <a name="non-transact-listings"></a>Nem Transact-listák  
Ez a szakasz azokat az ajánlatokat ismerteti, amelyek nem használják a Transact listaelem típusát. 

### <a name="list"></a>Lista  
A lista listázási típusa a piactéren található kirakatok következő ajánlati típusait tartalmazza.  

| Csomag típusa | Storefront | Részletek |  
|:---        |:---        |:---     |  
| Tanácsadási szolgáltatások | AppSource | Követelmények: AppSource: List: tanácsadási szolgáltatások |  
| Tanácsadási szolgáltatások | Azure Piactér | Követelmények: Azure Marketplace: List: tanácsadási szolgáltatások |  
| Kapcsolatfelvétel | AppSource | [](#) |  
| Kapcsolatfelvétel | Azure Piactér | Követelmények: AppSource: List: Kapcsolatfelvétel |  

#### <a name="requirements-appsource-list-consulting-service"></a>Követelmények: AppSource: List: tanácsadási szolgáltatás  

| Követelmények | Részletek |  
|:--- |:--- |  
| A szolgáltatás ajánlatának jellemzői | A tanácsadási szolgáltatásnak az alábbi feltételeknek kell megfelelnie.<ul> <li>Rögzített hatókörű, rögzített időtartamú, rögzített árú (vagy ingyenes) engagement.</li> <li>Elsősorban az értékesítés előtti tájolás.</li> <li>Egyetlen ügyfélre korlátozható.</li> <li>Viselkedés a webhelyen.</li> </ul> |  
| Partneri követelmények a tanácsadási szolgáltatásokhoz | Megfelel a szolgáltatáshoz kapcsolódó területeken feltételnek.<table><tr><th>Megoldási térség</th><th>Feltételek</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Ezüst vagy arany Felhőbeli ügyfélkapcsolat-kezelési kompetenciával rendelkezik.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Enterprise Edition</td><td>Az ezüst vagy arany vállalati erőforrás-tervezési kompetenciával és a Felhőbeli műveletekből származó bevételsel akár 12 hónapig $25 000 vagy még többet is nyerhet.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Business Edition</td><td>Egy vagy több ügyfélhez Cloud Services szolgáltatóként (CSP) vagy a Record (DPOR) digitális partnereként szolgál.</td></tr><tr><td>Power BI</td><td>Ismerje meg a megoldás partneri feltételeit.</td></tr><tr><td>PowerApps</td><td>A partner Showcase-megoldással rendelkezik.</td></tr></table><ul> <li>Az Ügyfélkapcsolat-kezeléssel kapcsolatos további információkért látogasson el a felhőalapú ügyfélkapcsolat-kezelési oldalra, amely a [partner.microsoft.com/Membership/Cloud-Customer-Relationship-Management-Competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency)címen található.</li> <li>Az erőforrás-tervezéssel kapcsolatos további információkért látogasson el a vállalati erőforrás-tervezési lapra, amely a következő címen található: [partner.microsoft.com/Membership/Enterprise-Resource-Planning-Competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>A CSP-vel kapcsolatos további információkért látogasson el a Cloud Services Provider oldalára, amely a következő címen található: [partner.microsoft.com/Cloud-Solution-Provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>A DPOR kapcsolatos további információkért tekintse meg a rekord-és partneri társítások digitális partnerét a [partner.microsoft.com/Membership/Digital-Partner-of-Record](https://partner.microsoft.com/membership/digital-partner-of-record)címen.</li> <li>A megoldási partneri feltételekkel kapcsolatos további információkért tekintse meg a megoldás partner áttekintése és a [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf)címen található ösztönzők című dokumentumot.</li> <li>A partner Showcase szolgáltatással kapcsolatos további információkért látogasson el a [powerapps.microsoft.com/partner-Showcase](https://powerapps.microsoft.com/partner-showcase)címen található partner Showcase-oldalra.</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Követelmények: Azure Marketplace: List: tanácsadási szolgáltatás  

| Követelmények | Részletek |  
|:--- |:--- |  
| A szolgáltatás ajánlatának jellemzői | A tanácsadási szolgáltatásnak az alábbi feltételeknek kell megfelelnie.<ul> <li>Rögzített hatókörű, rögzített időtartamú, rögzített árú (vagy ingyenes) engagement.</li> <li>Elsősorban az értékesítés előtti tájolás.</li> <li>Egyetlen ügyfélre korlátozható.</li> <li>Viselkedés a webhelyen.</li> </ul> |  
| Partneri követelmények a tanácsadási szolgáltatásokhoz | A szolgáltatáshoz tartozó érintett területeken a következő kompetenciák egyikében Silver vagy Gold típusúnak kell lennie: <table><tr><th>Megoldási térség</th><th>Kompetencia</th></tr><td>Felhőalapú platform és infrastruktúra</td><td>Felhőalapú platform<br />Adatközpont</td><tr><td>Alkalmazásfejlesztés és ISV</td><td>Alkalmazásfejlesztés<br />Alkalmazásintegrálás<br />DevOps</td></tr><tr><td>adatkezelés és elemzés</td><td>Adatelemzés<br />Data Platform</td></tr></table><ul> <li>A kompetenciákkal kapcsolatos további információkért tekintse meg Microsoft Partner Network oldalon található kompetenciákat a következő címen: [partner.microsoft.com/Membership/Competencies](https://partner.microsoft.com/membership/competencies).</li> <li>A listával kapcsolatos további információkért tekintse meg az Azure Marketplace-tanácsadói szolgáltatások oldalt, amely a következő címen található: [docs.microsoft.com/Azure/Marketplace/Consulting-Services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Próbaverzió  

| Csomag típusa | Storefront | Részletek |  
|:---        |:---        |:---     |  
| Ingyenes/SaaS-próbaverzió | AppSource | Listázási típusra vonatkozó követelmények: próbaverzió |  
| Ingyenes/SaaS-próbaverzió | Azure Piactér | Követelmények: Azure Marketplace: próbaverzió: ingyenes próbaverzió/SaaS próbaverzió |  
| Interaktív bemutató | AppSource | Listázási típusra vonatkozó követelmények: próbaverzió |  
| Interaktív bemutató | Azure Piactér | [Követelmények: Azure Marketplace: próbaverzió: Interaktív bemutató](#requirements-azure-marketplace-trial-interactive-demo) |  
| Próbaüzem | AppSource | Listázási típusra vonatkozó követelmények: próbaverzió |  
| Próbaüzem | Azure Piactér | [Követelmények: Azure Marketplace: próbaverzió: Test Drive](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Követelmények: Azure Marketplace: próbaverzió  

| Követelmény | Részletek |  
|:--- |:--- |  
| Ingyenes próbaidőszak és próbaidőszak | Az ügyfél korlátozott ideig ingyenesen használhatja az alkalmazást.<br /><br />Az ügyfélnek nem kell fizetnie az ajánlathoz vagy alkalmazáshoz tartozó licenc-vagy előfizetési díjakért. Az ügyfelet nem kell fizetnie az alapul szolgáló Microsoft-termék vagy-szolgáltatásért. Az Azure-előfizetéshez minden próbaverziós beállítás telepítve van. A költség optimalizálása és a felügyelet egyetlen vezérléssel rendelkezik.<br /><br />Dönthet úgy, hogy ingyenes próbaverziót, interaktív bemutatót vagy tesztelési meghajtót választ. Függetlenül attól, hogy mit választ, az ingyenes próbaverziónak előre meghatározott időt kell biztosítania az ügyfélnek az alkalmazás további költség nélkül történő kipróbálásához.<ul> <li>A tesztvezetés létrehozási folyamatának megkezdéséhez küldjön egy e-mailt a [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com).</li> </ul>Megjegyzés: az Azure Marketplace SaaS próbaverzióinak lehetővé kell tenniük, hogy az ügyfelek munkahelyi hitelesítő adatokat használjanak a bejelentkezéshez.<ul> <li>További információért látogasson el a AppSource Trial experiences szakaszra, amely a következő címen található: [docs.microsoft.com/Azure/Active-Directory/Develop/Active-Directory-devhowto-appsource-Certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Könnyen konfigurálható, használatra kész megoldás | Az alkalmazásnak egyszerűen és gyorsan kell konfigurálnia és beállítania. |  
| Rendelkezésre állás/üzemidő | Az SaaS-alkalmazásnak vagy-platformnak legalább 99,9%-os üzemidővel kell rendelkeznie. |  
| Azure Active Directory | Az ajánlatnak engedélyeznie kell a Azure Active Directory (Azure AD) összevont egyszeri bejelentkezést (SSO) (Azure AD összevont SSO) a beleegyezett engedéllyel. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Követelmények: Azure Marketplace: próbaverzió: ingyenes próbaverzió/SaaS próbaverzió  

| Előny | Követelmény |  
|:--- |:--- |  
| Lehetővé teszi az ügyfél számára, hogy kipróbálja a terméket, mielőtt automatikus módszerrel szeretne vásárolni a fizetős használatra való áttéréshez. Emellett lehetővé teszi az ügyfél és a Microsoft értékesítési csapatával való közös részvétel koncepciójának igazolását. | A megoldás egy virtuális gép vagy megoldás sablonja.<br /><br />A megoldás egy SaaS-ajánlat, és több-bérlős SaaS-terméket is kínál.<br /><br />A rendszer első alkalommal futtatja az ügyfelek gyors üzembe helyezését.<br /><br />Egyetlen Bérlővel rendelkezik, de az ügyfeleket vendég felhasználóként adja hozzá. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Követelmények: Azure Marketplace: próbaverzió: Interaktív bemutató  

| Előny | Követelmény |  
|:--- |:--- |  
| Lehetővé teszi az ügyfelek számára, hogy a beállítás bonyolultsága nélkül lássák a megoldást működés közben. | A megoldáshoz olyan összetett beállítások szükségesek, amelyeket a próbaidőszak alatt nehéz lenne elérni. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Követelmények: Azure Marketplace: próbaverzió: Test Drive  

| Előny | Követelmény |  
|:--- |:--- |  
| Lehetővé teszi, hogy az ügyfél a vásárlás előtt próbálja ki a terméket.<br /><br />Előre konfigurált beállításokkal biztosítja a megoldás interaktív élményét.<br /><br />A tesztelési meghajtó használatakor a következő további előnyökkel jár.<ul> <li>a felhasználók a piactéren végzett keresések 27%-át úgy finomítják, hogy csak tesztelési meghajtókat tartalmazó ajánlatokat jelenítenek meg.</li> <li>Az ajánlatok tesztelési meghajtókon 38%-kal nagyobb számú érdeklődőt hoznak, mint az ajánlatok nélkül.</li> <li>a piactéren az új ügyfelek beszerzésének 36%-a a test Drive-t használó ügyfelektől származik.</li> <li>A tesztelési meghajtók lehetővé teszik a Microsoft mező-értékesítők számára, hogy jobban megértsék a terméket a közös értékesítéssel kapcsolatos erőfeszítésekhez.</li> </ul> | A megoldás egy olyan virtuális gép, megoldás-sablon vagy SaaS-alkalmazás, amely egyetlen Bérlővel rendelkezik, vagy bonyolult a kiépítés. <br /><br />Nem rendelkezik olyan módszerrel, amellyel a próbaverziót fizetős ajánlatra alakíthatja át. |  

---

## <a name="transact-specific-listings"></a>Transact-specifikus Listázás

### <a name="transact"></a>Tranzakció  

| Csomag típusa | Storefront | Részletek |   
|:---        |:---        | :--- |  
| Azure-alkalmazások: felügyelt alkalmazás | Azure Piactér | Követelmények: Azure Marketplace: Transact: Azure apps: felügyelt alkalmazás |  
| Azure-alkalmazások: megoldás sablonja | Azure Piactér | Követelmények: Azure Marketplace: Transact: Azure apps: megoldás sablonja |  
| Tárolók | Azure Piactér | [Követelmények: Azure Marketplace: Transact: Container](#requirements-azure-marketplace-transact-container) |  
| SaaS-alkalmazás  | Azure Piactér | [Követelmények: Azure Marketplace: Transact: SaaS-alkalmazás](#requirements-azure-marketplace-transact-saas-app) |  
| Virtuális gép | Azure Piactér | [Követelmények: Azure Marketplace: Transact: virtuális gép](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Követelmények: Azure Marketplace: Transact: Container  

| Követelmény | Részletek |  
|:--- |:--- |  
| Számlázás és mérés | Az ingyenes vagy a BYOL számlázási modellt is támogatja. |  
| Docker-alapú rendszerkép | A tároló rendszerképének a Docker-rendszerkép formátumán kell alapulnia, és az Azure Container-nyilvántartásokból kell kihúzni. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Követelmények: Azure Marketplace: Transact: SaaS-alkalmazás  

| Követelmény | Részletek |  
|:--- |:--- |  
| Számlázás és mérés | Ajánlatunk havi átalánydíjas díjszabással érhető el. A használaton alapuló díjszabás és a használaton alapuló *valós* idejű beállítások jelenleg nem támogatottak. |  
| Lemondás | Ajánlatát bármikor megszakíthatja az ügyfél. |  
| Tranzakció kezdőlapja | Azure-beli közösen márkás tranzakció kezdőlapjának üzemeltetése. A Kezdőlap lehetővé teszi, hogy ügyfelei az SaaS-szolgáltatásfiókot hozzanak létre és kezeljenek. |  
| SaaS-előfizetés API | Adjon meg egy olyan szolgáltatást, amely együttműködik az SaaS-előfizetéssel egy felhasználói fiók és egy szolgáltatási csomag létrehozásához, frissítéséhez és törléséhez. A kritikus API-módosításokat 24 órán belül támogatni kell. A nem kritikus API-változások rendszeresen frissülnek. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Követelmények: Azure Marketplace: Transact: virtuális gép  

| Követelmény | Részletek |  
|:--- |:--- | 
| Számlázás és mérés | A virtuális gépnek BYOL vagy utólagos elszámolású havi számlázást kell támogatnia. |  
| Azure-kompatibilis virtuális merevlemez (VHD) | A virtuális gépeket Windows vagy Linux rendszerre kell építeni.<ul> <li>A linuxos virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: [Az Azure-ban támogatott Linux-disztribúciók](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>A Windows virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: [Azure-kompatibilis virtuális merevlemez létrehozása](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>További lépések
*   Látogasson el az [Azure Marketplace és a AppSource kiadói útmutató](./marketplace-publishers-guide.md) oldalára.  

