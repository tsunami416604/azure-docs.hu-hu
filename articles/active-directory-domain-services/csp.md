---
title: Azure AD tartományi szolgáltatások felhőalapú megoldásszolgáltatókszámára | Microsoft dokumentumok
description: Megtudhatja, hogy miként engedélyezheti és kezelheti az Azure Active Directory tartományi szolgáltatások által felügyelt tartományokat az Azure felhőszolgáltatók számára
services: active-directory-ds
author: iainfoulds
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: e7276dcfca6ba033942d62f347ac3a799524cac4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519088"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Az Azure Active Directory tartományi szolgáltatások üzembe helyezése és kezelése az Azure felhőalapú megoldásszolgáltatók számára

Az Azure Cloud Solution Providers (CSP) a Microsoft Partners egyik programja, amely licenccsatornát biztosít a különböző Microsoft-felhőszolgáltatásokhoz. Az Azure CSP lehetővé teszi a partnerek számára, hogy kezeljék az értékesítéseket, birtokolják a számlázási kapcsolatot, technikai és számlázási támogatást nyújtsanak, és az ügyfél egyetlen kapcsolattartója legyenek. Emellett az Azure CsP az eszközök teljes készletét biztosítja, beleértve az önkiszolgáló portált és a kísérő API-kat. Ezek az eszközök lehetővé teszik a csp-partnerek számára, hogy egyszerűen kiépítsék és kezeljék az Azure-erőforrásokat, és számlázást biztosítsanak az ügyfelek és előfizetéseik számára.

A [Partnerközpont portál](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) az összes Azure CSP-partner belépési pontja, és gazdag ügyfélkezelési lehetőségeket, automatizált feldolgozást és egyebeket biztosít. Az Azure CSP-partnerek használhatják a Partner-központ képességeit egy webalapú felhasználói felület használatával vagy a PowerShell és a különböző API-hívások használatával.

Az alábbi ábra bemutatja, hogyan működik a kripta-modell magas szinten. Itt a Contoso rendelkezik egy Azure Active Directory (Azure AD) bérlővel. Partnerséget kötöttek egy kripta-szolgáltatóval, aki az Azure-kp-előfizetésükben üzembe helyezi és kezeli az erőforrásokat. A Contoso rendszeres (közvetlen) Azure-előfizetésekkel is rendelkezhet, amelyeket közvetlenül a Contoso számláz.

![A kriptamodell áttekintése](./media/csp/csp_model_overview.png)

A csp-partner bérlője három speciális ügynökcsoporttal rendelkezik : *rendszergazdai* ügynökök, *helpdesk* ügynökök és *értékesítési* ügynökök.

A *rendszergazdai* ügynökök csoport van rendelve a bérlői rendszergazdai szerepkör contoso az Azure AD-bérlő. Ennek eredményeképpen a CSP-partner felügyeleti ügynökcsoportjához tartozó felhasználó bérlői rendszergazdai jogosultságokkal rendelkezik a Contoso Azure AD-bérlőjében.

Amikor a CSP-partner egy Azure CSP-előfizetést rendel a Contoso számára, a rendszergazdai ügynökök csoportja hozzá van rendelve az adott előfizetés tulajdonosi szerepköréhez. Ennek eredményeképpen a csp-partner rendszergazdai ügynökei rendelkeznek a szükséges jogosultságokkal az Azure-erőforrások, például a virtuális gépek, a virtuális hálózatok és az Azure AD tartományi szolgáltatások contoso nevében történő kiépítéséhez.

További információt az [Azure CSP áttekintése című témakörben talál.](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Az Azure AD DS azure-beli CSP-előfizetésben való használatának előnyei

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM-hitelesítés, amelyek teljes mértékben kompatibilisek a Windows Server Active Directory tartományi szolgáltatásokkal. Az évtizedek során számos alkalmazás épült, hogy az AD ellen működjön ezekkel a képességekkel. Számos független szoftvergyártó (ISV) épített és telepített alkalmazásokat ügyfelei telephelyén. Ezeket az alkalmazásokat nehéz támogatni, mivel gyakran van szükség a különböző környezetekhez való hozzáférésre, ahol az alkalmazások telepítve vannak. Az Azure CSP-előfizetések, van egy egyszerűbb alternatíva az Azure skálázatával és rugalmasságával.

Az Azure AD DS támogatja az Azure CSP-előfizetéseket. Az alkalmazást az ügyfél Azure AD-bérlőjéhez kötött Azure CSP-előfizetésben telepítheti. Ennek eredményeképpen az alkalmazottak (támogatási személyzet) kezelhetik, felügyelhetik és kiszolgálhatják azokat a virtuális gépeket, amelyeken az alkalmazás telepítve van a szervezet vállalati hitelesítő adataival.

Az Azure AD DS által felügyelt tartományt is üzembe helyezheti az ügyfél Azure AD-bérlőjében. Az alkalmazás ezután csatlakozik az ügyfél felügyelt tartományához. Az alkalmazáson belüli, a Kerberos / NTLM, Az LDAP vagy a [System.DirectoryServices API-ra](/dotnet/api/system.directoryservices) támaszkodó képességek zökkenőmentesen működnek az ügyfél tartományával szemben. A végfelhasználók számára előnyös az alkalmazás szolgáltatásként való igénybevételek, anélkül, hogy aggódniuk kellene az alkalmazás által telepített infrastruktúra karbantartása miatt.

Az adott előfizetésben felhasznált Azure-erőforrások összes számlázása, beleértve az Azure AD DS-t is, visszakerül Önre. Ön fenntartja a teljes ellenőrzést a kapcsolatot az ügyféllel, amikor az értékesítés, számlázás, technikai támogatás stb. Az Azure CSP-platform rugalmasságával a támogatási ügynökök egy kis csoportja számos ilyen ügyfelet kiszolgálhat, akik az alkalmazás példányait telepítették.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Az Azure AD DS-hez való csp-telepítési modellek

Az Azure AD DS-t kétféleképpen használhatja egy Azure CSP-előfizetéssel. Válassza ki a megfelelőt az ügyfelek biztonsági és egyszerűségi szempontjai alapján.

### <a name="direct-deployment-model"></a>Közvetlen üzembe helyezési modell

Ebben a telepítési modellben az Azure AD DS engedélyezve van egy virtuális hálózaton belül, amely az Azure CSP-előfizetéshez tartozik. A kriptapartner rendszergazdai ügynökei a következő jogosultságokkal rendelkeznek:

* *Globális rendszergazdai* jogosultságok az ügyfél Azure AD-bérlőben.
* *Előfizetés-tulajdonosi* jogosultságok az Azure CSP-előfizetésben.

![Közvetlen üzembe helyezési modell](./media/csp/csp_direct_deployment_model.png)

Ebben a telepítési modellben a kripta-szolgáltató rendszergazdai ügynökei felügyelhetik az identitásokat az ügyfél számára. Ezek a rendszergazdai ügynökök feladatokat hajthatnak végre, például új felhasználókat vagy csoportokat, vagy alkalmazásokat adhatnak hozzá az ügyfél Azure AD-bérlőn belül.

Ez a telepítési modell alkalmas lehet a kisebb szervezetek, amelyek nem rendelkeznek dedikált identitás-rendszergazda, vagy inkább a kriptográfiai partner felügyelete identitások a nevükben.

### <a name="peered-deployment-model"></a>Társviszony-alapú központi telepítési modell

Ebben a telepítési modellben az Azure AD DS engedélyezve van az ügyfélhez tartozó virtuális hálózaton belül – az ügyfél által fizetett közvetlen Azure-előfizetésen belül. A csp-partner alkalmazásokat telepíthet az ügyfél KP-előfizetéséhez tartozó virtuális hálózaton belül. A virtuális hálózatok ezután az Azure virtuális hálózati társviszony-létesítésével csatlakoztathatók.

Ezzel a telepítéssel a CSP-partner által az Azure CSP-előfizetésben üzembe helyezett számítási feladatok vagy alkalmazások csatlakozhatnak az ügyfél közvetlen Azure-előfizetésében kiépített felügyelt tartományához.

![Társviszony-alapú központi telepítési modell](./media/csp/csp_peered_deployment_model.png)

Ez a telepítési modell a jogosultságok elkülönítését biztosítja, és lehetővé teszi, hogy a csp-partner ügyfélszolgálati ügynökei felügyelhessék az Azure-előfizetést, és üzembe helyezhessék és kezelhessék az erőforrásokat. A csp-partner ügyfélszolgálati ügynökeinek azonban nem kell globális rendszergazdai jogosultságokkal rendelkezniük az ügyfél Azure AD-címtárában. Az ügyfél identitás-rendszergazdák továbbra is kezelheti identitások a szervezet számára.

This deployment model may be suited to scenarios where an ISV provides a hosted version of their on-premises application, which also needs to connect to the customer's Azure AD.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Azure AD DS felügyelete a CSP-előfizetésekben

A következő fontos szempontok vonatkoznak egy felügyelt tartomány azure-beli CSP-előfizetésben történő felügyeletekor:

* **A csp-rendszergazdai ügynökök a hitelesítő adataikkal létesíthetnek felügyelt tartományt:** Az Azure AD DS támogatja az Azure CSP-előfizetéseket. A csp-partner felügyeleti ügynöki csoportjához tartozó felhasználók új Azure AD DS felügyelt tartományt hozhatnak üzembe.

* **A kript.ps-ek a PowerShell használatával új felügyelt tartományokat hozhatnak létre ügyfeleik számára:** [Tekintse meg, hogyan engedélyezheti az Azure AD DS powershell használatával](powershell-create-instance.md) a részleteket.

* **A csp-rendszergazdai ügynökök hitelesítő adataikkal nem hajthatnak végre folyamatos felügyeleti feladatokat a felügyelt tartományban:** A CSP-rendszergazdai felhasználók hitelesítő adataikkal nem hajthatnak végre rutinfelügyeleti feladatokat a felügyelt tartományon belül. Ezek a felhasználók az ügyfél Azure AD-bérlőn kívül vannak, és hitelesítő adataik nem érhetők el az ügyfél Azure AD-bérlőn belül. Az Azure AD DS nem fér hozzá a Kerberos és NTLM jelszó kivéti ezekhez a felhasználókhoz, így a felhasználók nem hitelesíthetők az Azure AD DS felügyelt tartományaiban.

  > [!WARNING]
  > A felügyelt tartomány folyamatban lévő felügyeleti feladatainak végrehajtásához létre kell hoznia egy felhasználói fiókot az ügyfél címtárában.
  >
  > A felügyelt tartományba nem jelentkezhet be a hitelesítésszolgáltató rendszergazdájának hitelesítő adataival. Ehhez használja az ügyfél Azure AD-bérlőhöz tartozó felhasználói fiók hitelesítő adatait. Ezekre a hitelesítő adatokra olyan feladatokhoz van szüksége, mint például a virtuális gépek nek a felügyelt tartományhoz való csatlakozása, a DNS felügyelete vagy a csoportházirend felügyelete.

* **A folyamatos felügyelethez létrehozott felhasználói fiókot hozzá kell adni az *AAD tartományvezérlő rendszergazdák* csoportjához:** Az *AAD TARTOMÁNYVEZÉRLŐ-rendszergazdák* csoport jogosultságokkal rendelkezik bizonyos delegált felügyeleti feladatok elvégzéséhez a felügyelt tartományban. Ezek a feladatok közé tartozik a DNS konfigurálása, szervezeti egységek létrehozása és a csoportházirend felügyelete.
    
    Ahhoz, hogy egy csp-partner elvégezhesse ezeket a feladatokat egy felügyelt tartományban, létre kell hozniegy felhasználói fiókot az ügyfél Azure AD-bérlőjén belül. A fiók hitelesítő adatait meg kell osztani a kriptapartner rendszergazdai ügyintézői vel. Ezenkívül ezt a felhasználói fiókot hozzá kell adni az *AAD tartományvezérlő rendszergazdái* csoportjához, hogy a felügyelt tartomány konfigurációs feladatait ezzel a felhasználói fiókkal lehessen végrehajtani.

## <a name="next-steps"></a>További lépések

Első lépésekhez [regisztráljon az Azure CSP-programban.](/partner-center/enrolling-in-the-csp-program) Ezután engedélyezheti az Azure AD tartományi szolgáltatásokat [az Azure Portalon](tutorial-create-instance.md) vagy az [Azure PowerShellen](powershell-create-instance.md)keresztül.
