---
title: Az Azure Active Directory tartományi szolgáltatások az Azure Cloud Solution Providers |} A Microsoft Docs
description: Az Azure Active Directory tartományi szolgáltatások az Azure Cloud Solution Providers.
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: maheshu
ms.openlocfilehash: 63244a8f767f71d0f9be80aad09f06f14c4d3acf
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503971"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Az Azure Active Directory (AD) Domain Services, Azure Felhőszolgáltatók (CSP) számára
Ez a cikk bemutatja, hogyan használhatja az Azure AD tartományi szolgáltatásokat az Azure CSP-előfizetésben.

## <a name="overview-of-azure-csp"></a>Az Azure CSP áttekintése
Az Azure CSP for Microsoft Partners programot, és egy licenc csatornát biztosít a Microsoft különböző felhőszolgáltatásai számára. Az Azure CSP lehetővé teszi, hogy a partnerek számára, értékesítési, kezelhetik a számlázást, technikai és számlázási támogatást és az ügyfél egyetlen kapcsolattartási pontja lesz. Emellett az Azure CSP eszközök, teljes körű biztosít, többek között az önkiszolgáló portál, valamint a hozzájuk kapcsolódó API-k. Ezek az eszközök lehetővé teszik a CSP-partnerek egyszerű üzembe helyezése és kezelése az Azure-erőforrások, és adja meg a számlázás az ügyfelek és az előfizetések.

A [Partnerközpont portál](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) minden Azure CSP-partnerek számára belépési pontként szolgál. Gazdag ügyfél-felügyeleti képességeket, automatizált feldolgozását és egyéb biztosít. Az Azure CSP-partnerek Partnerközpont képességeket használhatja, egy webes felhasználói felületén vagy a PowerShell és a különböző API-hívás használatával.

A következő ábra szemlélteti a CSP-modell működése magas szinten. Contoso rendelkezik egy Azure AD az Active Directory. Egy CSP-hez, akik üzembe helyezi és kezeli az erőforrások az Azure CSP-előfizetésének partneri rendelkeznek. Contoso rendszeres (közvetlen) Azure-előfizetések, amelyek közvetlenül a Contoso díja is rendelkezhetnek.

![A CSP modell áttekintése](./media/csp/csp_model_overview.png)

A CSP-partner bérlő rendelkezik három speciális ügynök csoportok – felügyeleti ügynökök, a segélyszolgálat ügynökök és az értékesítési ügynökök. A felügyeleti ügynökök csoport a Contoso Azure AD-címtárban a bérlői rendszergazda szerepkör van hozzárendelve. Ennek eredményeképpen a CSP-partner felügyeleti ügynökök csoporthoz tartozó felhasználók bérlői rendszergazdai jogosultságokkal rendelkezik a Contoso Azure AD-címtárban. Ha a CSP-partner rendelkezések contoso, felügyeleti ügynökök csoportfelügyelet az Azure CSP-előfizetéssel van hozzárendelve az adott előfizetéshez tartozó tulajdonosi szerepkör. Ennek eredményeképpen a CSP-partner felügyeleti ügynökök rendelkezik a szükséges jogosultságokkal, például a virtuális gépek, virtuális hálózatok és nevében a Contoso Azure AD tartományi szolgáltatások Azure-erőforrások kiépítéséhez.

További információkért lásd: a [Azure CSP áttekintése](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Az Azure CSP-előfizetés az Azure AD Domain Services használatának előnyei
Az Azure AD tartományi szolgáltatásokat biztosít a Windows Server ad-vel kompatibilis szolgáltatások az Azure-ban például az LDAP, Kerberos/NTLM hitelesítés, a tartományhoz való csatlakozás, a csoportházirend és DNS. A évtizedes keresztül sok alkalmazás készített AD esetében működik, használja ezeket a képességeket. Számos, független szoftverszállítók (ISV-k) beépített és alkalmazások ügyfelek telephelyein üzembe helyezve. Ezek az alkalmazások olyan hátrányos támogatásához, mivel, amelyek gyakran hozzáférést igényel a a különböző környezetekhez, amelyekben ezek az alkalmazások vannak telepítve. Az Azure CSP-előfizetésekben van egy egyszerűbb alternatív és az Azure rugalmasságát.

Az Azure AD Domain Services már támogatja az Azure CSP-előfizetésekben. Most már telepítheti az alkalmazást egy Azure CSP-előfizetésben, az ügyfél az Azure AD-címtárhoz kötni. Az alkalmazottak (támogatási csapattal) eredményeképpen kezelése, felügyelheti és szolgáltatás a virtuális gépek, amelyre az alkalmazás telepítve van a szervezet vállalati hitelesítő adatok használatával. Helyezhet további, az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz, az ügyfél az Azure AD-címtárhoz. Az alkalmazás csatlakoztatva van az ügyfél által felügyelt tartományokhoz. Az alkalmazáson belül, ezért a Kerberos/NTLM, az LDAP, a használó funkciók vagy a [System.DirectoryServices API](https://msdn.microsoft.com/library/system.directoryservices) az ügyfél-címtár alapján zökkenőmentesen munkahelyi. A végfelhasználókat nagy mértékben az alkalmazás, szolgáltatás, anélkül, hogy az alkalmazás telepítve van az infrastruktúra karbantartásával foglalkoznia eméssze előnyeit.

Összes Azure-előfizetést, beleértve az Azure AD tartományi szolgáltatásokat, díjat számítunk fel visszatérhet ahhoz, hogy felhasznált erőforrások számlázását. A kapcsolat teljes körű irányítását karbantartása az ügyfélnél esetén, értékesítési, számlázási, műszaki támogatási stb. Az Azure CSP-platform rugalmasságával, egy kis csoportja támogatja az olyan ügynököket sok az ilyen ügyfelek, akik a példányok tud kiszolgálni az üzembe helyezett alkalmazás.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Az Azure AD tartományi szolgáltatásokhoz CSP üzembe helyezési modellek
Két módon, amelyben használhatja az Azure AD tartományi szolgáltatások Azure CSP-előfizetéssel. Válassza ki a megfelelőt, az ügyfelek a biztonság és az egyszerűség kedvéért szempontok alapján.

### <a name="direct-deployment-model"></a>Közvetlen telepítési modell
E-alapú üzemi modellben az Azure AD tartományi szolgáltatások engedélyezve van az Azure CSP-előfizetések alá tartozó, egy virtuális hálózatban. A CSP-partner felügyeleti ügynökök a következő jogosultságokkal rendelkezik:
* Az ügyfél Azure AD-címtár globális rendszergazdai jogokkal.
* Előfizetés tulajdonosi jogosultságokkal az Azure CSP-előfizetésekben.

![Közvetlen telepítési modell](./media/csp/csp_direct_deployment_model.png)

E-alapú üzemi modellben a CSP-szolgáltató felügyeleti ügynökök identitások felügyelheti az ügyfél számára. Felügyeleti ügynökök jogosultak az új felhasználók, csoportok, adja hozzá az alkalmazásokhoz a felhasználó Azure AD-címtár stb. Ez a telepítési modell előfordulhat, hogy lehet olyan kisebb szervezetek, amelyek nem egy dedikált identitásának kezelőjével vagy a CSP-partner felügyelheti a felhasználók nevében identitások igény szerint.


### <a name="peered-deployment-model"></a>Társított központi telepítési modell
E-alapú üzemi modellben az Azure AD tartományi szolgáltatások engedélyezve van egy az ügyfél fizeti közvetlen Azure-előfizetés alá tartozó, az ügyfél - egy virtuális hálózatban. A CSP-partner telepítheti az ügyfél CSP-előfizetések alá tartozó virtuális hálózaton belüli alkalmazások. Majd csatlakoztathatók a virtuális hálózatok az Azure virtuális hálózatok közötti társviszony segítségével. Ennek eredményeképpen a CSP-partnerek az Azure CSP-előfizetés által üzembe helyezett számítási feladatok/alkalmazások az ügyfél által felügyelt tartományokhoz, az ügyfél közvetlen Azure-előfizetésben kiépített csatlakozhat.

![Társított központi telepítési modell](./media/csp/csp_peered_deployment_model.png)

Az üzembe helyezési modell egy elválasztó jogosultságokat biztosít, és lehetővé teszi, hogy a CSP-partner segélyszolgálat ügynökök felügyelheti az Azure-előfizetés és telepítheti és kezelheti az erőforrásokat. A CSP-partner segélyszolgálat ügynökök azonban nem kell az ügyfél Azure AD-címtár globális rendszergazdai jogosultsággal rendelkezik. Az ügyfél identitáskezelő adminisztrátorokat továbbra is a szervezet identitásainak kezelése.

Előfordulhat, hogy az üzembe helyezési modell igazodó forgatókönyvek, ahol az ISV (független szoftverszállító) biztosít egy üzemeltetett helyszíni alkalmazását, amely is kell csatlakoznia az ügyfél verziója AD meg.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Felügyelete az Azure AD Domain Services által felügyelt tartományok CSP-előfizetésekben
Az alábbi megfontolásokat a alkalmazni: az Azure CSP-előfizetésének a felügyelt tartomány felügyeletéhez:

* **CSP felügyeleti ügynökök építhető ki egy felügyelt tartományi hitelesítő adataik használatával:** az Azure AD Domain Services támogatja az Azure CSP-előfizetésekben. Ezért egy CSP-partner felügyeleti ügynökök csoporthoz tartozó felhasználók helyezhet üzembe egy új Azure AD Domain Services felügyelt tartomány.

* **CSP-k parancsfájlt az új felügyelt tartományok létrehozása PowerShell használatával az ügyfelek számára:** lásd [engedélyezése az Azure AD Domain Services a PowerShell-lel](active-directory-ds-enable-using-powershell.md) részleteiről.

* **CSP felügyeleti ügynökök folyamatos fájlkezelési feladatok a felügyelt tartományi hitelesítő adataik használatával nem hajtható végre:** CSP rendszergazdai jogosultságú felhasználókhoz nem hajtható végre a szokásos felügyeleti feladatok a hitelesítő adataik használatával felügyelt tartományon belül. Ezek a felhasználók az ügyfél az Azure AD-címtárhoz, és a hitelesítő adatai nem érhetők el az ügyfél Azure AD-címtár belül. Ezért az Azure AD Domain Services nem rendelkezik a Kerberos és NTLM-jelszókivonatok ezeknek a felhasználóknak a hozzáférést. Ennek eredményeképpen ezek a felhasználók Azure AD tartományi szolgáltatások által felügyelt tartományokban nem lehet hitelesíteni.

  > [!WARNING]
  > **Egy felhasználói fiókot, az ügyfél directory folyamatban lévő felügyeleti feladatok a felügyelt tartományon belül kell létrehoznia.**
  > A felügyelt tartományra a CSP rendszergazdai felhasználói hitelesítő adatokkal nem tud bejelentkezni. Ehhez használja a egy felhasználói fiókot, az ügyfél az Azure AD-címtárhoz tartozó hitelesítő adatait. Ezeket a hitelesítő adatokat kell olyan feladatokhoz, mint a virtuális gépek csatlakoztatása a felügyelt tartományhoz, a DNS felügyelete, a felügyelet a csoport házirend stb.
  >

* **A felhasználói fiókhoz létrehozott a folyamatban lévő felügyeleti hozzá kell adni az "AAD DC rendszergazdák" csoportba:** az "AAD DC rendszergazdák" csoport rendelkezik bizonyos delegált felügyeleti feladatok végrehajtására a felügyelt tartományon. Az ilyen feladatokba beletartoznak a DNS, a szervezeti egységek, stb. Csoportházirend felügyelete létrehozásának konfigurálása. Az olyan feladatokat hajthat végre egy felügyelt tartományon CSP-partner egy felhasználói fiókot kell létrehozni az ügyfél Azure AD-címtár belül. Ez a fiók hitelesítő adatait kell osztani a CSP-partner felügyeleti ügynök. Ez a felhasználói fiók is, a konfigurációs feladatokat kell elvégezni, a felhasználói fiókkal a felügyelt tartományon engedélyezése "AAD DC rendszergazdák" csoportba kell adni.


## <a name="next-steps"></a>További lépések
* [Az Azure CSP programhoz](https://partnercenter.microsoft.com/partner/programs) és kezdjen el az Azure CSP teheti vállalkozását.
* Tekintse át a [Azure services elérhető az Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Az Azure AD Domain Services engedélyezése a PowerShell-lel](active-directory-ds-enable-using-powershell.md)
* [Az Azure AD Domain Services használatának első lépései](active-directory-ds-getting-started.md)
