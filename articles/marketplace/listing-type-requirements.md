---
title: Típus listázásával követelményei |} Az Azure
description: Ez a cikk ismerteti a megfelelőségi feltételeket, és szeretné ismerni az alkalmazások közzététele az Azure piactér közzétételi követelmények partnerek.
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
ms.openlocfilehash: 1c850b16492f725114fb23a8503f6345e1509212
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868667"
---
# <a name="requirements-by-listing-type"></a>Típus listázásával követelmények  
A technikai és marketinganyagokat tartalmi követelmények eltérőek lehetnek, storefront, ajánlat és lista típusa. Tekintse át a következő előírásoknak, a megfelelőség ellenőrzése.  
1. Storefront-követelmények:  
    *   [Appsource-ban](#storefront-requirements-appSource)  
    *   [Az Azure Marketplace-en](#storefront-requirements-azure-marketplace)  
2. Lista típusa és az ajánlat adattípus-követelményekkel:  
    *   Listaelem és ajánlat típusainak kapcsolatos további információkért látogasson el állapítsa meg az ajánlati típus for Your Solution oldalon található [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Storefront-követelményeknek: appsource-ban  
A következő táblázat ismerteti a előfeltételeinek közzététele az appsource-ban.  

| Követelmény | Részletek | Kötelező vagy ajánlott |  
|:--- |:--- |:--- |  
| ***Az Azure Active Directory (Azure AD)*** | Az alkalmazás Azure Active Directory összevont egyszeri bejelentkezés (Azure AD összevont egyszeri bejelentkezés) az engedélyezett hozzájárulásával engedélyeznie kell.<ul> <li>További információ a engedélyezése az Azure AD összevont egyszeri Bejelentkezést, látogasson el a konfigurálása egyszeri bejelentkezéshez olyan alkalmazások, amelyek nem szerepelnek az Azure Active Directory application galériabeli oldalán található [docs.microsoft.com/azure/active-directory/ Active-directory-saas-egyéni-alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Szükséges |   
| ***Integráció a Microsoft Felhőszolgáltatásaival*** | Az alkalmazás egyéb Microsoft Cloud services, Microsoft Power BI, a Cortana Intelligence vagy a Microsoft Azure-szolgáltatásokhoz hasonlóan kell integrálható.<ul> <li>Egy példa a Microsoft Cloud service, az eszközök internetes hálózatát.</li> </ul> | Ajánlott |  
| ***Célközönség*** | Az alkalmazás üzleti – felhasználók és az üzleti tulajdonosai kell lennie. | Szükséges | 
| ***Szoftverek, mint a szoftverszolgáltatások (SaaS) alkalmazások vállalati*** | Az alkalmazás az alábbi követelményeknek kell megfelelnie.<ul> <li>A sor üzleti SaaS-alkalmazás</li> <li>A fókuszban lévő üzleti folyamat</li> <li>Az üzleti ügyfeleknek megcélzott</li> <li>Engedélyezze a felhasználók számára a munkahelyi hitelesítő adatokat – például felhasználónévvel és jelszóval bejelentkezni</li> </ul> | Szükséges |  
| ***Ingyenes próbaidőszak és próbaverziója*** | Az alkalmazás tartalmaznia kell egy a következő beállítások ahhoz, hogy egy ügyfél az alkalmazás korlátozott ideig ingyenes használatához.<ul> <li>Adjon meg egy `try` metódust, így az ügyfelek előfordulhat, hogy az alkalmazás az appsource-on belül próbaverzió indítása</li> <li>Adjon meg egy `request trial` lehetőség az az appsource-ban, így a felhasználók kérhetik az alkalmazás próbaverzióját</li> </ul>Az ingyenes próbaverzióra, hogy az ügyfél csak olyan előre megadott, és próbálja ki az alkalmazás további költség nélkül. | Szükséges |  
| ***Könnyen konfigurálható, használatra kész megoldás*** | Az alkalmazás egyszerű és gyors konfigurálását, és állítsa be a nem szükséges testreszabás kell lennie. | Szükséges |  
| ***Felügyeleti vezethet*** | Engedélyeznie kell a CRM, fogadja el az érdeklődők adatait ahhoz, hogy vezet a megjelennie.<ul> <li>CRM példák Marketo, a Microsoft Dynamics vagy a Salesforce-ban</li> </ul> | Szükséges |  
| ***Adatvédelmi szabályzat és a használati feltételeket*** | Az alkalmazás meg kell adnia egy hivatkozást az adatvédelmi szabályzat oldalát, egy nyilvános URL-cím használatával. A használati feltételeket tartalmazó fájl közzététele szövegként során meg kell adni. | Szükséges |  
| ***Támogatás*** | Az alkalmazás az ügyfél-támogatási oldalát, egy nyilvános URL-hivatkozást kell megadnia. Ha az alkalmazás a próbaverziót, majd, támogatnia kell további költségek nélkül a próbaidőszak alatt. | Szükséges |  

## <a name="storefront-requirements-azure-marketplace"></a>Storefront-követelmények: Az Azure Marketplace-en  
Az alábbiakban az Azure Marketplace-en típusok listázásához előfeltételeinek.  

| Követelmény | Részletek | Lista típusa |  
|:--- |:--- |:--- |  
| ***Részvételi szabályzata*** | Alkalmazást be kell tartaniuk az Azure piactér részvételi szabályzatának.<ul> <li>A részvételi szabályzatának kapcsolatos további információkért látogasson el az Azure Marketplace részvételi szabályzatában található oldal [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | lista<br />Transact<br />trial |  
| ***A Microsoft-integráció*** | Az ajánlat kell használni, vagy kiterjesztheti a Microsoft Azure-szolgáltatás típusok, például számítási, hálózati vagy tárolási. Az ajánlat meglévő Azure Marketplace-en kategóriákhoz például adatbázisokat, biztonsági és hálózatkezelési kell igazítani.<ul> <li>Marketplace-ajánlat kapcsolatos további információkért látogasson el a Marketplace-alkalmazások lapján található [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | lista<br />Transact<br />trial |  
| ***Célközönség*** | Az ajánlat az informatikai szakemberek, fejlesztők számára, vagy egyéb technikai ügyfélszerepkörök kell lennie. | lista<br />Transact<br />trial |  
| ***Felügyeleti vezethet*** | Engedélyeznie kell a CRM, fogadja el az érdeklődők adatait ahhoz, hogy vezet a megjelennie (a Marketo, a Microsoft Dynamics vagy a Salesforce). | lista<br />Transact<br />trial |  
| ***Adatvédelmi szabályzat és a használati feltételeket*** | Az alkalmazás meg kell adnia egy hivatkozást az adatvédelmi szabályzat oldalát, egy nyilvános URL-cím használatával. A használati feltételeket tartalmazó fájl közzététele szövegként során meg kell adni. | lista<br />Transact<br />trial |  
| ***Támogatás*** | Az ajánlat az ügyfél-támogatási oldalát, egy nyilvános URL-hivatkozást kell megadnia. Ha az ajánlatot a próbaverziót, majd, támogatnia kell további költségek nélkül a próbaidőszak alatt. | Transact<br />trial |    

## <a name="non-transact-listings"></a>A transact-listák  
Ez a szakasz ismerteti, amelyek nem használnak az ajánlati típus Transact minden típusú. 

### <a name="list"></a>Lista  
A listában, listázás, írja be a következő típusú a kirakattípus a tartalmazza a Marketplace-en.  

| Csomag típusa | Storefront | Részletek |  
|:---        |:---        |:---     |  
| Tanácsadási szolgáltatások | AppSource | [Követelmények: Az AppSource: lista: tanácsadási szolgáltatások](#requirements-appsource-list-consulting-services) |  
| Tanácsadási szolgáltatások | Azure Piactér | [Követelmények: Az Azure piactéren: lista: tanácsadási szolgáltatások](#requirements-azure-marketplace-list-consulting-services) |  
| Kapcsolatfelvétel | AppSource | [](#) |  
| Kapcsolatfelvétel | Azure Piactér | [Követelmények: Az AppSource: lista: velem a kapcsolatot](#requirements-azure-marketplace-list-contact-me) |  

#### <a name="requirements-appsource-list-consulting-service"></a>Követelmények: Az AppSource: lista: tanácsadási szolgáltatás  

| Követelmények | Részletek |  
|:--- |:--- |  
| Szolgáltatási ajánlat jellemzői | A tanácsadási szolgáltatást a következő feltételeknek kell megfelelnie.<ul> <li>Egy meghatározott hatókör, rögzített időtartama, rögzített ár (vagy ingyenes) engagement kézbesítéséhez.</li> <li>Elhelyezés elsősorban a értékesítés előtti.</li> <li>Korlátozza az egyetlen ügyfél számára.</li> <li>Végez a helyen.</li> </ul> |  
| Partner Consulting Services-követelményei | A szolgáltatás megfelel a feltételeknek megfelelő területén.<table><tr><th>Megoldási terület</th><th>Feltételek</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Silver vagy Gold fokozatú Cloud Ügyfélkapcsolat-kezelés kompetenciával rendelkezik.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Enterprise edition</td><td>A záró 12 hónapos $ 25 000 vagy több rendelkezik kompetencia Silver vagy Gold vállalati erőforrás-tervezés és a felhőbeli műveletek származó bevételt.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Business edition</td><td>Cloud Services-szolgáltató (CSP) vagy a digitális partnert a rekord (DPOR) szolgál egy vagy több ügyfelek számára.</td></tr><tr><td>Power BI</td><td>A Felhőszolgáltatói Partner feltételeknek.</td></tr><tr><td>PowerApps</td><td>Van olyan Partnerbemutatókhoz megoldás.</td></tr></table><ul> <li>Ügyfélkapcsolat-kezelés kapcsolatos további információkért látogasson el a lapon található Felhőbeli Ügyfélkapcsolat-kezelés [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Erőforrás tervezésével kapcsolatos további információkért látogasson el a vállalati erőforrás-tervezés lapon található [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>CSP kapcsolatos további információkért látogasson el a lapon található Felhőszolgáltatást nyújtó [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Dpor-partnerként választotta kapcsolatos további információkért látogasson el a digitális hivatalos közvetítő partneri és partneri társítás oldalon található [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Megoldás partner szempontjaival kapcsolatos további információkért látogasson el a megoldás Partneráttekintés és ösztönzők dokumentumban található [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx? SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Partner bemutatása kapcsolatos további információkért látogasson el a lapon található Partnerbemutatókhoz [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Követelmények: Az Azure piactéren: lista: tanácsadási szolgáltatás  

| Követelmények | Részletek |  
|:--- |:--- |  
| Szolgáltatási ajánlat jellemzői | A tanácsadási szolgáltatást a következő feltételeknek kell megfelelnie.<ul> <li>Egy meghatározott hatókör, rögzített időtartama, rögzített ár (vagy ingyenes) engagement kézbesítéséhez.</li> <li>Elhelyezés elsősorban a értékesítés előtti.</li> <li>Korlátozza az egyetlen ügyfél számára.</li> <li>Végez a helyen.</li> </ul> |  
| Partner Consulting Services-követelményei | Rendelkeznie kell silver vagy gold az érintett területen az alábbi kompetenciák egyikén a szolgáltatás. <table><tr><th>Megoldási terület</th><th>Kompetencia</th></tr><td>A felhőalapú Platform és infrastruktúra</td><td>Felhőalapú Platform<br />Adatközpont</td><tr><td>Az alkalmazások fejlesztése és ISV-k</td><td>Alkalmazásfejlesztés<br />Alkalmazásintegrálás<br />DevOps</td></tr><tr><td>Adatkezelési és -elemzés</td><td>Adatelemzés<br />Data Platform</td></tr></table><ul> <li>Kompetenciák kapcsolatos további információkért látogasson el a kompetenciák keresztül Microsoft Partner Network oldalon található [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Listaelem kapcsolatos további információkért látogasson el az Azure Marketplace tanácsadási szolgáltatások lapján található [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---   

### <a name="trial"></a>Próbaverzió  

| Csomag típusa | Storefront | Részletek |  
|:---        |:---        |:---     |  
| Ingyenes / SaaS próbalehetőség | AppSource | [Adattípus-követelményekkel listázása: próbaverzió](#listing-type-requirements-trial) |  
| Ingyenes / SaaS próbalehetőség | Azure Piactér | [Követelmények: Az Azure piactéren: próbaverziós: az ingyenes próbaidőszak / SaaS próbalehetőség](#requirements-azure-marketplace-trial-free-trial-/-saas-trial) |  
| Interaktív bemutató | AppSource | [Adattípus-követelményekkel listázása: próbaverzió](#listing-type-requirements-trial) |  
| Interaktív bemutató | Azure Piactér | [Követelmények: Az Azure piactéren: próbaverzió: interaktív bemutató](#requirements-azure-marketplace-trial-interactive-demo) |  
| Próbaüzem | AppSource | [Adattípus-követelményekkel listázása: próbaverzió](#listing-type-requirements-trial) |  
| Próbaüzem | Azure Piactér | [Követelmények: Az Azure piactéren: próbaverzió: kipróbálása](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Követelmények: Az Azure piactéren: próbaverzió  

| Követelmény | Részletek |  
|:--- |:--- |  
| Ingyenes próbaidőszak és próbaverziója | Az ügyfél ingyenes korlátozott ideig lehet használni az alkalmazást.<br /><br />Az ügyfél nem szükséges az ajánlat vagy licencek vagy előfizetések díjakat fizetni. Az ügyfél, nem szükséges az alapul szolgáló Microsoft belső termék vagy szolgáltatás díjat fizetni. Minden próbaverzió lehetőségeket vannak telepítve az Azure-előfizetéshez. A költségek optimalizálása és a felügyeleti egyetlen ellenőrzési próbaverzióját rendelkezik.<br /><br />Előfordulhat, hogy a Válasszon egy ingyenes próbaverzióra, interaktív demó, vagy próbálja ki. Függetlenül attól, hogy milyen választja az ingyenes próbaverzió csak olyan az ügyfél előre megadott, és próbálja ki az alkalmazást, további költségek nélkül.<ul> <li>E-mail küldése egy tesztverziós létrehozásának folyamatán a kezdéshez [ amp-testdrive@microsoft.com ](mailto:amp-testdrive@microsoft.com).</li> </ul>Megjegyzés: Az Azure Marketplace SaaS próbaverziós élményt engedélyeznie kell az ügyfelek számára a munkahelyi hitelesítő adatait használva a bejelentkezéshez.<ul> <li>További információért látogasson el az appsource-ban próbaverziós élményt szakaszban található [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Könnyen konfigurálható, használatra kész megoldás | Az alkalmazás könnyen és gyorsan konfigurálását, és állítsa be kell lennie. |  
| Rendelkezésre állás / üzemidő | Az SaaS-alkalmazás vagy a platform legalább 99,9 %-os hasznos üzemidővel kell rendelkeznie. |  
| Azure Active Directory | Az ajánlat engedélyeznie kell az Azure Active Directory (Azure AD) összevont egyszeri bejelentkezés (SSO) (Azure AD összevont egyszeri bejelentkezés) hozzájárult engedélyezve van. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Követelmények: Az Azure piactéren: próbaverziós: az ingyenes próbaidőszak / SaaS próbalehetőség  

| Előny | Követelmény |  
|:--- |:--- |  
| Lehetővé teszi egy próbálja meg a termék konvertálása egy olyan automatizált módszer a vásárló fizetős használata előtt. Emellett lehetővé teszi az ügyfél és a Microsoft értékesítői csapatok közös együttműködés a megvalósíthatósági próbák menete. | A megoldás egy virtuális gép vagy a megoldássablon.<br /><br />A megoldás egy SaaS-ajánlatok, és egy több-bérlős SaaS-terméket kínál.<br /><br />Egy ügyfél használatának első futtatási élménye és egyszerű munkakezdés rendelkezik.<br /><br />Egyetlen új bérlő rendelkezik, de nem ad hozzá ügyfeleket vendégként. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Követelmények: Az Azure piactéren: próbaverzió: interaktív bemutató  

| Előny | Követelmény |  
|:--- |:--- |  
| Lehetővé teszi az ügyfelek tekintheti meg működés közben összetettsége nélkül beállítása után a megoldás. | A megoldáshoz összetett beállítások eléréséhez a próbaidőszak alatt belül nehéz lenne szükséges. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Követelmények: Az Azure piactéren: próbaverzió: kipróbálása  

| Előny | Követelmény |  
|:--- |:--- |  
| Lehetővé teszi egy előtt vásárolnak, próbálja ki a terméket.<br /><br />A beállításokkal előre konfigurált megoldás az interaktív élményt nyújt.<br /><br />A következő további előnyökkel egy tesztverziós használatakor.<ul> <li>27 %-a felhasználói keresések a marketplace-en vannak alakítva show kínál tesztelheti a felhasználók által.</li> <li>Tesztelheti, amelyek felkeltésére 38 %-kal több, mint az ajánlatok nélkül.</li> <li>az új ügyfél felvásárlás a marketplace-en 36 % igénybe vett próbálja ki az ügyfelek származnak.</li> <li>Tesztverziók engedélyezze a Microsoft-értékesítőkkel közösen mező jobb megértése érdekében a termék közös értékesítési munkája alapján.</li> </ul> | A megoldás egy virtuális gép, megoldássablon vagy SaaS-alkalmazás egy egybérlős és, vagy bonyolult üzembe helyezni. <br /><br />A próbaidőszak átalakítása fizetős ajánlatra metódus nem rendelkezik. |  

---

## <a name="transact-specific-listings"></a>A Transact-specifikus listaelemek

### <a name="transact"></a>Tranzakció  

| Csomag típusa | Storefront | Részletek |   
|:---        |:---        | :--- |  
| Azure-alkalmazások: a felügyelt alkalmazás | Azure Piactér | [Követelmények: Az Azure Marketplace-en: Transact: Azure-alkalmazások: a felügyelt alkalmazás](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  
| Az Azure apps: megoldássablon | Azure Piactér | [Követelmények: Az Azure piactéren: Transact: Azure-alkalmazások: megoldássablon](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  
| Containers | Azure Piactér | [Követelmények: Az Azure piactéren: Transact: tároló](#requirements-azure-marketplace-transact-container) |  
| SaaS-alkalmazás  | Azure Piactér | [Követelmények: Az Azure piactéren: Transact: SaaS-alkalmazás](#requirements-azure-marketplace-transact-saas-app) |  
| Virtuális gép | Azure Piactér | [Követelmények: Az Azure piactéren: Transact: virtuális gép](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Követelmények: Az Azure piactéren: Transact: tároló  

| Követelmény | Részletek |  
|:--- |:--- |  
| Számlázással és méréssel | Támogatja a vagy az ingyenes vagy BYOL számlázási modellt. |  
| Docker-alapú lemezkép | A tároló rendszerképét a Docker rendszerképformátuma kell alapulnia, és kell Azure Container Registry szolgáltatásból kell lekérni. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Követelmények: Az Azure piactéren: Transact: SaaS-alkalmazás  

| Követelmény | Részletek |  
|:--- |:--- |  
| Számlázással és méréssel | Az ajánlat havonta átalánydíjat díjszabása. Használatalapú díjszabás és a használat alapú *igaz felfelé* beállítások jelenleg nem támogatottak. |  
| Visszavonás | Az ajánlat bármikor visszavonható az ügyfélnek. |  
| Tranzakció kezdőlapja | A gazdagép egy Azure társmárkás tranzakció kezdőlapja. A kezdőlapja lehetővé teszi, hogy az ügyfelek hozhat létre és kezelhet az SaaS-szolgáltatás fiókjából. |  
| SaaS-előfizetés API | Adjon meg egy olyan szolgáltatás, amely együttműködik a SaaS-előfizetés létrehozása, frissítése és egy felhasználói fiókot és a service-csomag törlése. Az összes kritikus fontosságú API-módosítás támogatnia kell a 24 órán belül. Minden nem kritikus fontosságú API-módosítás rendszeresen frissül. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Követelmények: Az Azure piactéren: Transact: virtuális gép  

| Követelmény | Részletek |  
|:--- |:--- | 
| Számlázással és méréssel | A virtuális gép vagy BYOL, vagy használatalapú havi számlázási támogatniuk kell. |  
| Azure-kompatibilis virtuális merevlemez (VHD) | Windows vagy Linux rendszerű virtuális gépek kell épül.<ul> <li>Linux rendszerű virtuális Merevlemezek létrehozásával kapcsolatos további információkért látogasson el az Azure-kompatibilis VHD-t (Linux-alapú) szakaszban található létrehozása [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Windows virtuális Merevlemezek létrehozásával kapcsolatos további információkért látogasson el az Azure-kompatibilis VHD-t (Windows-alapú) szakaszban található létrehozása [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>További lépések
*   Látogasson el a [Azure Marketplace és AppSource útmutató Közzétevőknek](./marketplace-publishers-guide.md) lapot.  
 
---  
