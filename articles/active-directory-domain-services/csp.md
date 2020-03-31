---
title: Azure AD tartományi szolgáltatások felhőalapú megoldásszolgáltatókszámára | Microsoft dokumentumok
description: Azure Active Directory tartományi szolgáltatások az Azure felhőbeli megoldásszolgáltatókhoz.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: iainfou
ms.openlocfilehash: 1134c078ee36a146cb1e1cbf8ca46f6cd9f8d775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754443"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Azure Active Directory (AD) tartományi szolgáltatások Azure felhőalapú megoldásszolgáltatókhoz (CSP)
Ez a cikk bemutatja, hogyan használhatja az Azure AD tartományi szolgáltatásokat egy Azure CSP-előfizetésben.

## <a name="overview-of-azure-csp"></a>Az Azure kriptanevének áttekintése
Az Azure CSP a Microsoft Partners egyik programja, és licenccsatornát biztosít a különböző Microsoft-felhőszolgáltatásokhoz. Az Azure CSP lehetővé teszi a partnerek számára, hogy kezeljék az értékesítéseket, birtokolják a számlázási kapcsolatot, technikai és számlázási támogatást nyújtsanak, és az ügyfél egyetlen kapcsolattartója legyenek. Emellett az Azure CsP az eszközök teljes készletét biztosítja, beleértve az önkiszolgáló portált és a kísérő API-kat. Ezek az eszközök lehetővé teszik a csp-partnerek számára, hogy egyszerűen kiépítsék és kezeljék az Azure-erőforrásokat, és számlázást biztosítsanak az ügyfelek és előfizetéseik számára.

A [Partnerközpont portál](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) belépési pontként működik az összes Azure CSP-partner számára. Gazdag ügyfélkezelési lehetőségeket, automatizált feldolgozást és egyebeket biztosít. Az Azure CSP-partnerek használhatják a Partner-központ képességeit egy webalapú felhasználói felület használatával vagy a PowerShell és a különböző API-hívások használatával.

Az alábbi ábra bemutatja, hogyan működik a kripta-modell magas szinten. A Contoso rendelkezik egy Azure AD Active Directoryval. Partnerséget kötöttek egy kripta-szolgáltatóval, aki az Azure-kp-előfizetésükben üzembe helyezi és kezeli az erőforrásokat. A Contoso rendszeres (közvetlen) Azure-előfizetésekkel is rendelkezhet, amelyeket közvetlenül a Contoso számláz.

![A kriptamodell áttekintése](./media/csp/csp_model_overview.png)

A csp-partner bérlője három speciális ügynökcsoporttal rendelkezik : rendszergazdai ügynökök, helpdesk ügynökök és értékesítési ügynökök. A rendszergazdai ügynökök csoport van rendelve a bérlői rendszergazdai szerepkör a Contoso Azure AD-címtárban. Ennek eredményeképpen a CSP-partner felügyeleti ügynökcsoportjához tartozó felhasználó bérlői rendszergazdai jogosultságokkal rendelkezik a Contoso Azure AD-címtárában. Amikor a CSP-partner egy Azure CSP-előfizetést rendel a Contoso számára, a rendszergazdai ügynökök csoportja hozzá van rendelve az adott előfizetés tulajdonosi szerepköréhez. Ennek eredményeképpen a csp-partner rendszergazdai ügynökei rendelkeznek a szükséges jogosultságokkal az Azure-erőforrások, például a virtuális gépek, a virtuális hálózatok és az Azure AD tartományi szolgáltatások contoso nevében történő kiépítéséhez.

További információt az [Azure CSP áttekintése című témakörben talál.](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Az Azure AD tartományi szolgáltatások Azure-csp-előfizetésben való használatának előnyei
Az Azure AD tartományi szolgáltatások Windows Server AD-kompatibilis szolgáltatásokat biztosít az Azure-ban, például az LDAP-t, a Kerberos/NTLM-hitelesítést, a tartományhoz való csatlakozást, a csoportházirendet és a DNS-t. Az évtizedek során számos alkalmazás épült, hogy az AD ellen működjön ezekkel a képességekkel. Számos független szoftvergyártó (ISV) épített és telepített alkalmazásokat ügyfelei telephelyén. Ezek az alkalmazások támasztják alá, mivel gyakran hozzáférést igényelnek a különböző környezetekben, amelyekben ezek az alkalmazások telepítve vannak. Az Azure CSP-előfizetések, van egy egyszerűbb alternatíva az Azure skálázatával és rugalmasságával.

Az Azure AD tartományi szolgáltatások mostantól támogatja az Azure CSP-előfizetéseket. Most már üzembe helyezheti az alkalmazást egy Azure CSP-előfizetésben, amely az ügyfél Azure AD-címtárához van kötve. Ennek eredményeképpen az alkalmazottak (támogatási személyzet) kezelhetik, felügyelhetik és kiszolgálhatják azokat a virtuális gépeket, amelyeken az alkalmazás telepítve van a szervezet vállalati hitelesítő adataival. Továbbá azure AD tartományi szolgáltatások felügyelt tartományt is kiépíthet az ügyfél Azure AD-címtárához. Az alkalmazás az ügyfél felügyelt tartományához kapcsolódik. Ezért az alkalmazáson belüli, a Kerberos/NTLM, az LDAP vagy a [System.DirectoryServices API-ra](/dotnet/api/system.directoryservices) támaszkodó képességek zökkenőmentesen működnek az ügyfél címtárával szemben. A végfelhasználók számára nagy mértékben előnyös az alkalmazás szolgáltatásként való fogyasztása, anélkül, hogy aggódnia kellene az alkalmazás üzembe helyezett infrastruktúrájának karbantartása miatt.

Az adott előfizetésben felhasznált Azure-erőforrások összes számlázása, beleértve az Azure AD tartományi szolgáltatásokat is, visszakerül Önre. Ön fenntartja a teljes ellenőrzést a kapcsolatot az ügyféllel, amikor az értékesítés, számlázás, technikai támogatás stb. Az Azure CSP-platform rugalmasságával a támogatási ügynökök egy kis csoportja számos ilyen ügyfelet kiszolgálhat, akik az alkalmazás példányait telepítették.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Az Azure AD tartományi szolgáltatások központi telepítésének modelljei
Az Azure AD tartományi szolgáltatások azure-beli CSP-előfizetéssel kétféleképpen használhatók. Válassza ki a megfelelőt az ügyfelek biztonsági és egyszerűségi szempontjai alapján.

### <a name="direct-deployment-model"></a>Közvetlen üzembe helyezési modell
Ebben a telepítési modellben az Azure AD tartományi szolgáltatások engedélyezve van egy virtuális hálózaton belül az Azure CSP-előfizetés. A kriptapartner rendszergazdai ügynökei a következő jogosultságokkal rendelkeznek:
* Globális rendszergazdai jogosultságok az ügyfél Azure AD-címtárában.
* Előfizetés-tulajdonosi jogosultságok az Azure CSP-előfizetésben.

![Közvetlen üzembe helyezési modell](./media/csp/csp_direct_deployment_model.png)

Ebben a telepítési modellben a kripta-szolgáltató rendszergazdai ügynökei felügyelhetik az identitásokat az ügyfél számára. Ezek a rendszergazdai ügynökök képesek új felhasználókat, csoportokat, alkalmazásokat hozzáadni az ügyfél Azure AD-címtárában stb. Ez a telepítési modell alkalmas lehet a kisebb szervezetek, amelyek nem rendelkeznek dedikált identitás-rendszergazda, vagy inkább a kriptográfiai partner felügyeli identitások a nevükben.


### <a name="peered-deployment-model"></a>Társviszony-alapú központi telepítési modell
Ebben a telepítési modellben az Azure AD tartományi szolgáltatások engedélyezve vannak az ügyfélhez tartozó virtuális hálózaton belül – azaz az ügyfél által fizetett közvetlen Azure-előfizetésen belül. A csp-partner ezután alkalmazásokat telepíthet az ügyfél CSP-előfizetéséhez tartozó virtuális hálózaton belül. A virtuális hálózatok ezután az Azure virtuális hálózati társviszony-létesítésével csatlakoztathatók. Ennek eredményeképpen a CSP-partner által az Azure CSP-előfizetésben üzembe helyezett számítási feladatok/alkalmazások csatlakozhatnak az ügyfél közvetlen Azure-előfizetésében kiépített felügyelt tartományához.

![Társviszony-alapú központi telepítési modell](./media/csp/csp_peered_deployment_model.png)

Ez a telepítési modell a jogosultságok elkülönítését biztosítja, és lehetővé teszi, hogy a csp-partner ügyfélszolgálati ügynökei felügyelhessék az Azure-előfizetést, és üzembe helyezhessék és kezelhessék az erőforrásokat. A csp-partner ügyfélszolgálati ügynökeinek azonban nem kell globális rendszergazdai jogosultságokkal rendelkezniük az ügyfél Azure AD-címtárában. Az ügyfél identitás-rendszergazdák továbbra is kezelheti identitások a szervezet számára.

Ez a központi telepítési modell alkalmas lehet olyan esetekben, amikor egy független szoftverszállító (független szoftverszállító) biztosítja a helyszíni alkalmazás üzemeltetett verzióját, amelynek csatlakoznia kell az ügyfél AD-jéhez is.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Az Azure AD tartományi szolgáltatások által felügyelt tartományok felügyelete a CSP-előfizetésekben
A következő fontos szempontok vonatkoznak egy felügyelt tartomány azure-beli CSP-előfizetésben történő felügyeletekor:

* **A csp-rendszergazdai ügynökök a hitelesítő adataikkal létesíthetnek felügyelt tartományt:** Az Azure AD tartományi szolgáltatások támogatja az Azure CSP-előfizetéseket. Ezért a CSP-partner felügyeleti ügynökcsoportjához tartozó felhasználók új Azure AD tartományi szolgáltatások felügyelt tartományt hozhatnak üzembe.

* **A kript.ps-ek a PowerShell használatával új felügyelt tartományokat hozhatnak létre ügyfeleik számára:** [Tekintse meg, hogyan engedélyezheti az Azure AD tartományi szolgáltatások powershell használatával](powershell-create-instance.md) a részletekért.

* **A csp-rendszergazdai ügynökök hitelesítő adataikkal nem tudnak folyamatos felügyeleti feladatokat végrehajtani a felügyelt tartományban:** A CSP-rendszergazdai felhasználók hitelesítő adataikkal nem hajthatnak végre rutinkezelési feladatokat a felügyelt tartományon belül. Ezek a felhasználók az ügyfél Azure AD-címtárán kívül vannak, és hitelesítő adataik nem érhetők el az ügyfél Azure AD-címtárában. Ezért az Azure AD tartományi szolgáltatások nem férnek hozzá a Kerberos és NTLM jelszó kivék ezekhez a felhasználókhoz. Ennek eredményeképpen ezek a felhasználók nem hitelesíthetők az Azure AD tartományi szolgáltatások által felügyelt tartományokban.

  > [!WARNING]
  > **A felügyelt tartomány folyamatban lévő felügyeleti feladatainak végrehajtásához létre kell hoznia egy felhasználói fiókot az ügyfél címtárában.**
  > A felügyelt tartományba nem jelentkezhet be a hitelesítésszolgáltató rendszergazdai hitelesítő adataival. Ehhez használja az ügyfél Azure AD-címtárához tartozó felhasználói fiók hitelesítő adatait. Ezekre a hitelesítő adatokra olyan feladatokhoz van szüksége, mint a virtuális gépek csatlakozása a felügyelt tartományhoz, a DNS felügyelete, a csoportházirend felügyelete stb.
  >

* **A folyamatos felügyelethez létrehozott felhasználói fiókot hozzá kell adni az "AAD DC rendszergazdák" csoporthoz:** Az "AAD TARTOMÁNYVEZÉRLŐ-rendszergazdák" csoport jogosultságokkal rendelkezik bizonyos delegált felügyeleti feladatok végrehajtására a felügyelt tartományban. Ezek a feladatok magukban foglalják a DNS konfigurálását, a szervezeti egységek létrehozását, a csoportházirend felügyeletét stb. Ahhoz, hogy egy csp-partner ilyen feladatokat hajtson végre egy felügyelt tartományban, létre kell hozniegy felhasználói fiókot az ügyfél Azure AD-címtárában. A fiók hitelesítő adatait meg kell osztani a kriptapartner rendszergazdai ügyintézői vel. Ezt a felhasználói fiókot hozzá kell adni az "AAD tartományvezérlő rendszergazdái" csoporthoz, hogy a felügyelt tartomány konfigurációs feladatait ezzel a felhasználói fiókkal lehessen végrehajtani.


## <a name="next-steps"></a>További lépések
* [Az Azure CSP-programban regisztrálva,](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) és az Azure CSP-n keresztül indíthat üzleti vállalkozást.
* Tekintse át az [Azure-ban elérhető Azure-szolgáltatások](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)listáját.
* [Az Azure AD Domain Services engedélyezése a PowerShell-lel](powershell-create-instance.md)
* [Ismerkedés az Azure AD tartományi szolgáltatásokkal](tutorial-create-instance.md)
