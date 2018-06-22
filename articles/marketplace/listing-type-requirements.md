---
title: Listaelem típusa követelményeit |} Azure
description: Ez a cikk ismerteti a jogosultsági feltételeket és a közzététel követelményeinek partnerek meg szeretné ismerni az alkalmazások közzététele az Azure piactéren.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: 07a62dfa2d7e1c71daf547c5aa7c8c7d15830bfd
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309316"
---
# <a name="requirements-by-listing-type"></a>Követelményeit listaelem típusa  
A műszaki és marketing content követelmények kirakat ajánlattípus és listaelem típus lehet. Tekintse át az alábbi előírások a megfelelőség ellenőrzése.  
1. Storefront követelmények:  
    *   [AppSource](#storefront-requirements-appSource)  
    *   [Az Azure piactéren](#storefront-requirements-azure-marketplace)  
2. Listaelem típusa és ajánlat szemben támasztott követelményeit:  
    *   Listázása és ajánlat típusainak kapcsolatos további információkért látogasson el a listázása típusát határozza meg a megoldás lap a mappában lévő [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Storefront követelmények: AppSource  
A következő táblázat ismerteti az előfeltételként szükséges közzététel AppSource a.  
| Követelmény | Részletek | Kötelező vagy ajánlott |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Az alkalmazás hozzájárulási engedélyezve van az Azure Active Directory összevont egyszeri bejelentkezést (az Azure AD összevont egyszeri bejelentkezés) engedélyeznie kell.<ul> <li>Az Azure AD engedélyezésével kapcsolatos további információk összevont egyszeri Bejelentkezést, látogasson el a konfigurálása egyszeri bejelentkezés alkalmazásokhoz, amelyek nincsenek rajta az Azure Active Directory application gallery lapon található [docs.microsoft.com/azure/active-directory/ Active-directory-szolgáltatottszoftver-egyéni-alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Szükséges |   
| ***Integráció a Microsoft Cloud Services csomag*** | Az alkalmazás egyéb Microsoft Cloud services csomag Microsoft Power BI, a Cortana Intelligence vagy a Microsoft Azure-szolgáltatásokhoz hasonlóan kell integrálható.<ul> <li>Egy példa a Microsoft Cloud Service szolgáltatásra, az eszközök internetes hálózatát.</li> </ul> | Ajánlott |  
| ***Célközönség*** | Az alkalmazás üzleti felhasználók és a vállalakozások tulajdonosai kell lennie. | Szükséges | 
| ***A vállalati szolgáltatott szoftverként (SaaS) alkalmazásként szoftver*** | Az alkalmazás az alábbi követelményeknek kell megfelelnie.<ul> <li>Egy üzleti SaaS-alkalmazás</li> <li>Arra irányul, hogy üzleti-folyamat</li> <li>Üzleti felhasználók irányuló</li> <li>Engedélyezze a felhasználók a munkahelyi hitelesítő adatokat a jelentkezzen be például a felhasználónév és jelszó</li> </ul> | Szükséges |  
| ***Ingyenes próbaverzió lejárta és próbaverziója*** | Az alkalmazás tartalmaznia kell egy a következő beállítások ahhoz, hogy az ügyfél korlátozott ideig szabad használhatja az alkalmazást.<ul> <li>Adjon meg egy `try` metódust, így az ügyfelek is kipróbálása egy AppSource belül az alkalmazás</li> <li>Adjon meg egy `request trial` a AppSource, így az ügyfelek kérheti valamelyik próbaverzióját futtatja, az alkalmazás beállítása</li> </ul>Az ingyenes próbaidőszakra, hogy az alkalmazás további költség nélkül kipróbálásához előre beállított időn kell nyújtania az ügyfél. | Szükséges |  
| ***Könnyen konfigurálható és használatra kész megoldást*** | Az alkalmazás könnyű és gyors konfigurálásához, és állítsa be a nem szükséges testreszabási kell lennie. | Szükséges |  
| ***Felügyeleti vezethet*** | Engedélyeznie kell a CRM ahhoz, hogy az érdeklődési adatok vezet a kirakat a fogadásához.<ul> <li>CRM példák Marketo, a Microsoft Dynamics vagy a Salesforce</li> </ul> | Szükséges |  
| ***Adatvédelmi szabályzatát, illetve a használati feltételek*** | Az alkalmazás meg kell adnia egy hivatkozást az adatvédelmi szabályzat oldalát egy nyilvános URL-cím használatával. A használati feltételek szövegként közzététele során meg kell adni. | Szükséges |  
| ***Támogatás*** | Az alkalmazás meg kell adnia egy hivatkozást az ügyfélszolgálat oldalának egy nyilvános URL-cím használatával. Ha az alkalmazás egy próbaverzióval, majd Ön támogatnia kell minden további költség nélkül a próbaidőszak alatt. | Szükséges |  

## <a name="storefront-requirements-azure-marketplace"></a>Storefront követelmények: Az Azure piactér  
Az Azure piactér típusok listázása előfeltételként szükséges követelményei a következők.  
| Követelmény | Részletek | Listaelem típusa |  
|:--- |:--- |:--- |  
| ***Részvétel házirendek*** | Hajtsa végre az alkalmazást az Azure piactér részvételét házirendeket.<ul> <li>A programban való részvétellel házirendekkel kapcsolatos további információkért látogasson el az Azure piactér részvételét házirendek lapon található [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | lista<br />Transact<br />Próbaverzió |  
| ***Integráció a Microsoft*** | Az ajánlatot kell használni, vagy számítási, hálózati vagy tárolási például a Microsoft Azure szolgáltatástípusok kiterjesztése. Az ajánlat kell igazítás adatbázisok, a biztonság és a hálózat meglévő Azure piactér kategória.<ul> <li>Piactér ajánlatok kapcsolatos további információkért látogasson el a piactér alkalmazások lapján található [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | lista<br />Transact<br />Próbaverzió |  
| ***Célközönség*** | Az ajánlat az informatikai szakembereknek, a felhő fejlesztők és az egyéb technikai felhasználói szerepkörök kell lennie. | lista<br />Transact<br />Próbaverzió |  
| ***Felügyeleti vezethet*** | Engedélyeznie kell a CRM (a Marketo, a Microsoft Dynamics vagy a Salesforce) ahhoz, hogy az érdeklődési adatok vezet a kirakat a fogadásához. | lista<br />Transact<br />Próbaverzió |  
| ***Adatvédelmi szabályzatát, illetve a használati feltételek*** | Az alkalmazás meg kell adnia egy hivatkozást az adatvédelmi szabályzat oldalát egy nyilvános URL-cím használatával. A használati feltételek szövegként közzététele során meg kell adni. | lista<br />Transact<br />Próbaverzió |  
| ***Támogatás*** | Az ajánlat meg kell adnia egy hivatkozást az ügyfélszolgálat oldalának egy nyilvános URL-cím használatával. Ha az ajánlatot a próbaverziót, majd Ön támogatnia kell minden további költség nélkül a próbaidőszak alatt. | Transact<br />Próbaverzió |    

## <a name="non-transact-listings"></a>A transact-listák  
Ez a szakasz ismerteti, amelyek nem használnak a listaelem típusa Transact minden típusú. 

### <a name="list"></a>Lista  
A lista típusú bejegyzés a következő típusú a kirakatokkal a tartalmazza a piactéren.  

| Csomag típusa | Storefront | Részletek |  
|:---        |:---        |:---     |  
| Tanácsadási szolgáltatásokat | AppSource | [Követelmények: AppSource: lista: tanácsadás](#requirements-appsource-list-consulting-services) |  
| Tanácsadási szolgáltatásokat | Azure Piactér | [Követelmények: Az Azure piactér: lista: tanácsadás](#requirements-azure-marketplace-list-consulting-services) |  
| Kapcsolatfelvétel | AppSource | [](#) |  
| Kapcsolatfelvétel | Azure Piactér | [Követelmények: AppSource: lista: megkereshet](#requirements-azure-marketplace-list-contact-me) |  

#### <a name="requirements-appsource-list-consulting-service"></a>Követelmények: AppSource: lista: tanácsadás szolgáltatás  

| Követelmények | Részletek |  
|:--- |:--- |  
| Szolgáltatási ajánlat tulajdonságok | A tanácsadási szolgáltatást a következő feltételeknek kell megfelelnie.<ul> <li>A rögzített hatókör, rögzített időtartamú, rögzített ár (vagy szabad) engagement biztosításához.</li> <li>Elhelyezés elsősorban az értékesítés előtti.</li> <li>Korlátozza az egyetlen ügyfél számára.</li> <li>Végezze el a helyen.</li> </ul> |  
| Consulting Services tanácsadó szolgálat partner követelményei | Ön a feltételeknek az érintett területen a szolgáltatás.<table><tr><th>Megoldás terület</th><th>Feltételek</th></tr><tr><td>Az ügyfél Engagement Dynamics 365</td><td>Ezüst vagy arany felhő Ügyfélkapcsolat-kezelés kompetencia rendelkezik.</td></tr><tr><td>Dynamics 365 pénzügyi, illetve műveletek, Enterprise edition</td><td>A záró 12 hónapon keresztül $ 25 000 vagy több rendelkezik ezüst vagy arany vállalati erőforrás tervezési kompetencia és a felhőbeli műveletek származó bevételt.</td></tr><tr><td>Dynamics 365 pénzügyi, illetve műveletek, Business edition</td><td>Egy vagy több ügyfél felhőalapú szolgáltatások szolgáltató (CSP) vagy digitális Partner a rekord (DPOR) szolgálják.</td></tr><tr><td>Power BI</td><td>A megoldás Partner feltételeknek.</td></tr><tr><td>PowerApps</td><td>Van egy Partner Showcase megoldás.</td></tr></table><ul> <li>Ügyfélkapcsolat-kezelés kapcsolatos további információkért látogasson el a felhő Ügyfélkapcsolat-kezelés lap helyen [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Erőforrás megtervezésével kapcsolatos további információkért látogasson el a vállalati erőforrás tervezési lapon található [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>CSP kapcsolatos további információkért látogasson el a felhő szolgáltatója lapon található [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>DPOR kapcsolatos további információkért látogasson el a digitális a következő bejegyzett Partner és a Partner társítás lapon található [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Megoldás partner szempontjaival kapcsolatos további információkért látogasson el a megoldás Partner áttekintése és ösztönzők dokumentum helyen [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx? SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Partner showcase kapcsolatos további információkért látogasson el a Partner Showcase lapon található [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Követelmények: Az Azure piactér: lista: tanácsadás szolgáltatás  

| Követelmények | Részletek |  
|:--- |:--- |  
| Szolgáltatási ajánlat tulajdonságok | A tanácsadási szolgáltatást a következő feltételeknek kell megfelelnie.<ul> <li>A rögzített hatókör, rögzített időtartamú, rögzített ár (vagy szabad) engagement biztosításához.</li> <li>Elhelyezés elsősorban az értékesítés előtti.</li> <li>Korlátozza az egyetlen ügyfél számára.</li> <li>Végezze el a helyen.</li> </ul> |  
| Consulting Services tanácsadó szolgálat partner követelményei | Rendelkeznie kell ezüst vagy arany egy, az érintett területen a következő szakértelem a szolgáltatás. <table><tr><th>Megoldás terület</th><th>Kompetencia</th></tr><td>Felhő Platform és infrastruktúra</td><td>Felhő Platform<br />Adatközpont</td><tr><td>Alkalmazások fejlesztése és ISV</td><td>Alkalmazásfejlesztés<br />Alkalmazásintegrálás<br />DevOps</td></tr><tr><td>Adatkezelés és elemzés</td><td>Adatelemzés<br />Data Platform</td></tr></table><ul> <li>Szakértelem kapcsolatos további információkért látogasson el a szakértelem keresztül Microsoft Partner Network lapon található [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Listaelem kapcsolatos további információkért látogasson el az Azure piactér Consulting Services tanácsadó szolgálat lapon található [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---   

### <a name="trial"></a>Próbaverzió  

| Csomag típusa | Storefront | Részletek |  
|:---        |:---        |:---     |  
| Szabad / SaaS próbaverzió | AppSource | [Szemben támasztott követelményeit listázása: próbaverzió](#listing-type-requirements-trial) |  
| Szabad / SaaS próbaverzió | Azure Piactér | [Követelmények: Az Azure piactér: próba: ingyenes próbaverzió / SaaS próbaverzió](#requirements-azure-marketplace-trial-free-trial-/-saas-trial) |  
| Interaktív bemutató | AppSource | [Szemben támasztott követelményeit listázása: próbaverzió](#listing-type-requirements-trial) |  
| Interaktív bemutató | Azure Piactér | [Követelmények: Az Azure piactér: próbaverzió: interaktív bemutató](#requirements-azure-marketplace-trial-interactive-demo) |  
| Próbaüzem | AppSource | [Szemben támasztott követelményeit listázása: próbaverzió](#listing-type-requirements-trial) |  
| Próbaüzem | Azure Piactér | [Követelmények: Az Azure piactér: próbaverzió: kipróbálása](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Követelmények: Az Azure piactér: próbaverzió  

| Követelmény | Részletek |  
|:--- |:--- |  
| Ingyenes próbaverzió lejárta és próbaverziója | Az ügyfél szabad korlátozott időre lehet használni az alkalmazást.<br /><br />Az ügyfél nem szükséges a licencek vagy előfizetések fizeti az ajánlat vagy alkalmazás. Az ügyfél nincs szükség az alapul szolgáló Microsoft-k termékhez vagy szolgáltatáshoz kell fizetnie. Mind a próbaverziós beállítást az Azure-előfizetéshez vannak telepítve. Az költség optimalizálása és a felügyeleti egyetlen vezérlő próbaverziója van.<br /><br />Válassza ki a próba-előfizetésre, az interaktív bemutató is, vagy kipróbálása. Beállításoktól függetlenül az ingyenes próbaidőszak kell nyújtania az ügyfél az alkalmazás minden további költség nélkül próbálja meg előre beállított időn.<ul> <li>E-mail küldése a kezdéshez próbát létrehozásának folyamatán [ amp-testdrive@microsoft.com ](mailto:amp-testdrive@microsoft.com).</li> </ul>Megjegyzés: Az Azure piactér SaaS próba lép lehetővé kell tenni az ügyfelek számára a munkahelyi hitelesítő adatai segítségével bejelentkezhetnek.<ul> <li>További információkért látogasson el a próba lép szakaszban található AppSource [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Könnyen konfigurálható és használatra kész megoldást | Az alkalmazás könnyű és gyors konfigurálásához és állítson be kell lennie. |  
| Rendelkezésre állás / hasznos üzemidő | A Szolgáltatottszoftver-alkalmazás vagy a platform legalább 99,9 %-os hasznos üzemidőt kell rendelkeznie. |  
| Azure Active Directory | Az ajánlat lehetővé kell tenni, Azure Active Directory (Azure AD) összevont egyszeri bejelentkezést (SSO) (az Azure AD összevont egyszeri bejelentkezés) hozzájárulási engedélyezve van. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Követelmények: Az Azure piactér: próba: ingyenes próbaverzió / SaaS próbaverzió  

| Előny | Követelmény |  
|:--- |:--- |  
| Lehetővé teszi, hogy egy ügyfél egy automatizált módszerrel az átalakításához vásárlás fizetős használata előtt, próbálja meg a termékhez. Emellett lehetővé teszi az igazolást, ügyfél és a Microsoft értékesítési csoportok közös engagement fogalom. | A megoldás egy virtuális géphez vagy sablonhoz megoldás.<br /><br />A megoldás egy SaaS kínál, és egy több-bérlős Szolgáltatottszoftver-termék ajánlatot tesz.<br /><br />A számítógép első indításakor az ügyfél megszerezni, akinek gyorsan rendelkezik.<br /><br />Egyetlen bérlővel rendelkezik, de vendégként ügyfelek ad hozzá. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Követelmények: Az Azure piactér: próbaverzió: interaktív bemutató  

| Előny | Követelmény |  
|:--- |:--- |  
| Lehetővé teszi, hogy az ügyfelek a megoldás beállításához összetettsége nélkül művelet megjelenítéséhez. | A megoldás bonyolult beállításokat, amelyek merevlemez eléréséhez a próbaidőszak belül lenne szükséges. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Követelmények: Az Azure piactér: próbaverzió: kipróbálása  

| Előny | Követelmény |  
|:--- |:--- |  
| Lehetővé teszi, hogy az ügyfél előtt vásárolnak, próbálja meg a termék.<br /><br />A megoldás, előre konfigurált beállításokkal interaktív élményt nyújt.<br /><br />Az alábbiakban további előnyökkel is jár teszt meghajtót használ.<ul> <li>27 %-a felhasználói kereséseket a piactéren csak megjelenítése ajánlatok teszt meghajtók felhasználók vannak is.</li> <li>Teszt meghajtók ajánlatok készítése 38 % további érdeklődők mint ajánlatok nélkül.</li> <li>Új ügyfél kérése a piactéren 36 %-át ügyfeleket, próbát tartott határozza meg.</li> <li>Teszt meghajtók engedélyezése a Microsoft mező eladók jobb megértése érdekében a termék közös értékesít erőfeszítéseket.</li> </ul> | A megoldás egy virtuális gép, megoldássablonban vagy egy bérlői SaaS-alkalmazás, vagy összetett kiépítését. <br /><br />A próbaverzió átalakítása fizetett ajánlat metódus nem rendelkeznek. |  

---

## <a name="transact-specific-listings"></a>Transact-specifikus listák

### <a name="transact"></a>Tranzakció  

| Csomag típusa | Storefront | Részletek |   
|:---        |:---        | :--- |  
| Az Azure apps: által kezelt alkalmazás | Azure Piactér | [Követelmények: Az Azure piactér: Transact: Azure alkalmazások: által kezelt alkalmazás](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  
| Az Azure apps: megoldás sablon | Azure Piactér | [Követelmények: Az Azure piactér: Transact: Azure alkalmazások: megoldás sablon](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  
| Containers | Azure Piactér | [Követelmények: Az Azure piactér: Transact: tároló](#requirements-azure-marketplace-transact-container) |  
| SaaS-alkalmazás  | Azure Piactér | [Követelmények: Az Azure piactér: Transact: SaaS-alkalmazás](#requirements-azure-marketplace-transact-saas-app) |  
| Virtuális gép | Azure Piactér | [Követelmények: Az Azure piactér: Transact: virtuális gép](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Követelmények: Az Azure piactér: Transact: tároló  

| Követelmény | Részletek |  
|:--- |:--- |  
| Számlázási és annak mérésére | Támogatja a vagy az ingyenes vagy BYOL számlázási modellt. |  
| Docker-alapú lemezkép | A tároló lemezkép a Docker képformátum kell alapulnia, és az Azure-tároló nyilvántartó kell húzni. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Követelmények: Az Azure piactér: Transact: SaaS-alkalmazás  

| Követelmény | Részletek |  
|:--- |:--- |  
| Számlázási és annak mérésére | Az ajánlat árképzéséről havi simán sebességgel. Használatalapú tarifacsomag és használat alapú *true felfelé* beállítások jelenleg nem támogatottak. |  
| Visszavonás | Az ajánlat bármikor törölhető az ügyfél. |  
| Tranzakció kezdőlapja | Egy Azure társmárkás tranzakció kezdőlapja üzemeltetésére. A kezdőlapja lehetővé teszi, hogy a felhasználók létrehozásához és kezeléséhez a Szolgáltatottszoftver-szolgáltatásfiók. |  
| SaaS-előfizetés API | Adjon meg egy olyan szolgáltatás, amely együttműködik a Szolgáltatottszoftver-előfizetés létrehozása, frissítése és a felhasználói fiók és a service-csomag törlése. Minden kritikus API módosítások 24 órán belül támogatnia kell. A nem kritikus API módosításokat a rendszer rendszeres időközönként frissíti. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Követelmények: Az Azure piactér: Transact: virtuális gép  

| Követelmény | Részletek |  
|:--- |:--- | 
| Számlázási és annak mérésére | A virtuális gép BYOL vagy a használatalapú fizetés havi számlázási támogatniuk kell. |  
| Azure-kompatibilis virtuális merevlemez (VHD) | A Windows vagy Linux rendszerű virtuális gépek kell épül.<ul> <li>Linux virtuális Merevlemezek létrehozásával kapcsolatos további információkért látogasson el a hozzon létre egy Azure-kompatibilis VHD-t (Linux-alapú) szakaszban található [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Windows virtuális Merevlemezek létrehozásával kapcsolatos további információkért látogasson el a hozzon létre egy Azure-kompatibilis VHD-t (Windows-alapú) szakaszban található [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>További lépések
*   Látogasson el a [AppSource Publisher útmutató és az Azure piactér](./marketplace-publishers-guide.md) lap.  
 
---  
