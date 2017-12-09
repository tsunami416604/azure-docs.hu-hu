---
title: "Az Azure Active Directory tartományi szolgáltatások az Azure felhőalapú Megoldásszolgáltatók közül |} Microsoft Docs"
description: "Az Azure Active Directory tartományi szolgáltatások, Azure Cloud Solution szolgáltatók."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: maheshu
ms.openlocfilehash: 313c4853b0f585921739779bb764c50036c9ac8a
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Az Azure Active Directory (AD) tartományi szolgáltatásokban az Azure felhőalapú megoldás szolgáltatók (CSP)
Ez a cikk azt ismerteti, hogyan használható az Azure AD tartományi szolgáltatások egy Azure CSP előfizetésben.

## <a name="overview-of-azure-csp"></a>Az Azure CSP áttekintése
Azure CSP Microsoft Partners egy programot, és egy licenc csatornát biztosít a különböző Microsoft felhőszolgáltatásai számára. Az Azure CSP lehetővé teszi, hogy a partnerek számára, hogy az értékesítési kezelése, a számlázási kapcsolatban saját, számlázási és technikai segítséget és kell az ügyfél kapcsolódási pont. Emellett Azure Kriptográfiai szolgáltató eszközök teljes készletét, többek között az önkiszolgáló portál és API-k kísérő. Ezek az eszközök CSP partnerek számára, hogy könnyen telepíteni és kezelni a Azure-erőforrások engedélyezése, és adja meg a számlázási az ügyfelek és az előfizetések.

A [Partnerközpontjában portal](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) minden Azure CSP-partnerek úgy működik, mint a belépési pont. Gazdag ügyfél-felügyeleti képességeket, automatikus feldolgozás és további biztosít. Azure CSP partnerek Partnerközpontjában képességeket használhatja, egy webes felhasználói felület használatával vagy a PowerShell és a különböző API-hívások segítségével.

A következő ábra bemutatja, hogyan a CSP-modell magas szinten működik. A Contoso Azure AD Active Directory-alapú rendelkezik. Kapcsolatban áll egy CSP-hez, telepíti és kezeli az Azure CSP előfizetésének erőforrásokat, akik rendelkeznek. Contoso is rendszeres (közvetlen) Azure-előfizetések, amelyek közvetlenül a Contoso vannak számlázva.

![A kriptográfiai Szolgáltató modell áttekintése](./media/csp/csp_model_overview.png)

A kriptográfiai Szolgáltató partner által létrehozott bérlő van három különleges ügynök - felügyeleti ügynökök, a segélyszolgálat ügynökök és az értékesítési ügynökök. A felügyeleti ügynökök csoport a Contoso Azure AD-címtár a bérlői rendszergazda szerepkör van hozzárendelve. Ennek eredményeképpen a CSP partner által létrehozott felügyeleti ügynökök csoporthoz tartozó felhasználó Bérlői rendszergazda jogosultságokkal rendelkezik a Contoso Azure AD-címtár. Ha a CSP partner rendelkezések egy Azure CSP-előfizetést a Contoso, a felügyeleti ügynökök csoport van rendelve a tulajdonosi szerepkört, hogy az előfizetéshez. Ennek eredményeképpen a CSP partner által létrehozott felügyeleti ügynökök rendelkezik a szükséges jogosultságokkal, az Azure erőforrások, például virtuális gépek, virtuális hálózatok és a nevében a Contoso Azure AD tartományi szolgáltatások biztosításához.

További információkért lásd: a [Azure CSP áttekintése](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Egy Azure CSP-előfizetés az Azure AD tartományi szolgáltatások használatának előnyei
Azure AD tartományi szolgáltatások biztosít a Windows Server AD kompatibilis szolgáltatások, például az LDAP, Kerberos vagy NTLM hitelesítési, tartományhoz való csatlakozást, Csoportházirend és a DNS az Azure-ban. A évtizedben keresztül számos alkalmazás készített elleni AD működéséhez ezeket a képességeket használatával. Sok független szoftvergyártók (ISV-k) beépített és az ügyfelek telephelyein alkalmazások telepítését. Ezek az alkalmazások hátrányos támogatására, mert, amelyek gyakran kell elérnie a különböző környezetekben, amelyekben ezek az alkalmazások vannak telepítve. Azure CSP előfizetésekkel rendelkezik egy egyszerűbb alternatív a méretezési és az Azure rugalmasságát.

Azure AD tartományi szolgáltatások most már támogatja az Azure CSP előfizetések. Most már telepítheti az alkalmazást egy Azure CSP-előfizetést, az ügyfél az Azure AD-címtár kötve. Az alkalmazottak (segélyszolgálat) eredményeképpen kezelése, felügyelhetik és szolgáltatást a virtuális gépek, amelyeken az alkalmazás van telepítve a szervezet vállalati hitelesítő adatok használatával. További az Azure AD tartományi szolgáltatásokat Azure AD-címtár az ügyfél által felügyelt tartományokhoz létesíthet. Az alkalmazás csatlakozik-e az ügyfél által felügyelt tartományokhoz. Az alkalmazáson belül, ezért a Kerberos vagy NTLM, LDAP, támaszkodó képességek vagy a [System.DirectoryServices API](https://msdn.microsoft.com/library/system.directoryservices) munkahelyi zökkenőmentesen ügyfele directory ellen. Az end ügyfelek nagy mértékben igénybe vehesse az fel az alkalmazás szolgáltatásként, anélkül, hogy az alkalmazás központi telepítése a infrastruktúrájának foglalkoznia.

Az összes számlázási, az Azure-erőforrások abban, hogy előfizetését, beleértve az Azure AD tartományi szolgáltatások díjfizetéssel vissza szeretné használni. Teljes ellenőrzést a kapcsolat az ügyféllel karbantartása, amikor a értékesítési, számlázási, műszaki támogatás stb. Az Azure CSP platform rugalmasságot, a támogatási ügynökök kis csoport szolgáltatás is sok az ilyen ügyfelek, akik példány az alkalmazás telepítése.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Azure AD tartományi szolgáltatások üzembe helyezési modellel kriptográfiai Szolgáltató
Kétféleképpen, amelyben használható Azure AD tartományi szolgáltatások egy Azure CSP-előfizetést. Válassza ki a megfelelőt, a felhasználóknak a biztonsági és egyszerűségre szempontok alapján.

### <a name="direct-deployment-model"></a>Közvetlen telepítési modell
Az üzembe helyezési modellben a Azure AD tartományi szolgáltatások engedélyezve van a Azure CSP-előfizetések alá tartozó virtuális hálózaton belül. A CSP partner által létrehozott felügyeleti ügynök a következő jogosultságok szükségesek:
* Az ügyfél az Azure AD-címtár globális rendszergazdai jogosultsággal.
* Az Azure CSP előfizetés előfizetés tulajdonos jogosultsággal.

![Közvetlen telepítési modell](./media/csp/csp_direct_deployment_model.png)

Az üzembe helyezési modellel a CSP-szolgáltató admin ügynökök identitások felügyelhetik az ügyfél. Felügyeleti ügynökök képesek az új felhasználók, csoportok kiépíteni, vegyen fel alkalmazásokat belül az ügyfél az Azure AD-címtár stb. A telepítési modell is kell megfelelő kisebb szervezetek számára, amely nem rendelkezik egy dedikált identitásának kezelőjével, vagy inkább a CSP partner helyettük végzi az identitások felügyeletéhez.


### <a name="peered-deployment-model"></a>Nincsenek társviszonyban telepítési modell
Az üzembe helyezési modellben a Azure AD tartományi szolgáltatások engedélyezve van ez azt jelenti, hogy az ügyfél - egy az ügyfél által kifizette közvetlen Azure-előfizetéshez tartozó virtuális hálózaton belül. A kriptográfiai Szolgáltató partner Ezután telepítheti az alkalmazások az ügyfél CSP-előfizetések alá tartozó virtuális hálózaton belül. A virtuális hálózatok segítségével a Azure-beli virtuális hálózat társviszony-létesítés majd csatlakozik. Ennek eredményeképpen a munkaterhelések/Azure CSP az előfizetést a CSP partner által telepített kapcsolódhat az ügyfél közvetlen Azure-előfizetésben kiépített az ügyfél által felügyelt tartományokhoz.

![Nincsenek társviszonyban telepítési modell](./media/csp/csp_peered_deployment_model.png)

Ez a telepítési modell kizárás jogosultságokat biztosít, és lehetővé teszi, hogy a kriptográfiai Szolgáltató partner segélyszolgálat ügynökök felügyelete az Azure-előfizetés és üzembe helyezését, és az erőforrások kezelése. Azonban a CSP partner segélyszolgálat ügynökök nem kell az ügyfél az Azure AD-címtár globális rendszergazdai jogosultságokkal rendelkezik. A felhasználói identitás rendszergazdák továbbra is a szervezet identitásainak kezelése.

A telepítési modell forgatókönyvek, ahol az ISV (független szoftverszállító) biztosít egy üzemeltetett igazodó előfordulhat, hogy azok a helyszíni alkalmazás, amely is kapcsolódnia kell az ügyfél verziója AD meg.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Azure AD tartományi szolgáltatások felügyelete felügyelt tartományok CSP előfizetésekhez
A következő szempontokat alkalmazza, ha egy felügyelt tartomány egy Azure CSP előfizetésben felügyelete:

* **CSP felügyeleti ügynökök hozhat létre a hitelesítő adataik használatával felügyelt tartományhoz:** Azure AD tartományi szolgáltatások támogatja az Azure CSP előfizetések. Ezért egy CSP partner által létrehozott felügyeleti ügynökök csoporthoz tartozó felhasználók hozhat létre új Azure AD tartományi szolgáltatások felügyelt tartományhoz.

* **CSP lehet parancsprogramot futtatni a PowerShell használatával az ügyfelek új felügyelt tartományok létrehozását:** lásd: [engedélyezése az Azure AD tartományi szolgáltatások a PowerShell használatával hogyan](active-directory-ds-enable-using-powershell.md) részleteiről.

* **CSP felügyeleti ügynökök folyamatos fájlkezelési feladatok a felügyelt tartományra, a hitelesítő adataik használatával nem hajtható végre:** CSP rendszergazda felhasználók nem hajtható végre a szokásos felügyeleti feladatok a hitelesítő adataik használatával kezelt tartományban. Ezek a felhasználók számára az ügyfél az Azure AD-címtár külső és a hitelesítő adatok nem érhetők el az ügyfél az Azure Active Directoryban. Azure AD tartományi szolgáltatásokat, ezért nem rendelkezik a Kerberos és NTLM jelszókivonatait ezek a felhasználók hozzáférését. Ennek eredményeképpen ezek a felhasználók nem lehet hitelesíteni a Azure AD tartományi szolgáltatások által felügyelt tartományon.

  > [!WARNING]
  > **Egy felhasználói fiókot a felügyelt tartományra a folyamatban lévő felügyeleti feladatok elvégzéséhez az ügyfél címtáron belül kell létrehoznia.**
  > A felügyelt tartományra egy CSP rendszergazdai felhasználói hitelesítő adatokkal nem tud bejelentkezni. Az ügyfél az Azure AD-címtár tartozó felhasználói fiók hitelesítő adatait használja ehhez. Ezeket a hitelesítő adatokat kell olyan feladatokhoz, mint a virtuális gépek csatlakoztatása felügyelt tartományhoz, a DNS felügyelete, a felügyelet csoport házirend stb.
  >

* **A felhasználói fiókhoz létrehozott az adminisztrációra hozzá kell adni a "AAD DC rendszergazdák" csoportba:** a "AAD DC rendszergazdák" csoport nem rendelkezik jogosultsággal a bizonyos delegált felügyeleti feladatok végrehajtása a felügyelt tartományra. Ezek a feladatok közé tartoznak a szervezeti egységeket, stb. Csoportházirend felügyelete hoz létre, a DNS konfigurálása. Olyan feladatokat hajthat végre egy felügyelt tartomány CSP partner egy felhasználói fiókot kell létrehozni az ügyfél az Azure Active Directoryban. Ez a fiók hitelesítő adatait meg kell osztani a CSP partner által létrehozott felügyeleti ügynök. Ez a felhasználói fiók is, a "AAD DC rendszergazdák" csoportba ahhoz, hogy a feladatokat a felügyelt tartomány végezhető el ez a felhasználói fiók használatával kell adni.


## <a name="next-steps"></a>Következő lépések
* [Az Azure CSP programhoz való csatlakozás](https://partnercenter.microsoft.com/partner/programs) és Azure CSP vállalatra létrehozásának megkezdése.
* Tekintse át a listában, [Azure CSP elérhető Azure-szolgáltatások](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Az Azure AD Domain Services engedélyezése a PowerShell-lel](active-directory-ds-enable-using-powershell.md)
* [Ismerkedés az Azure AD tartományi szolgáltatások](active-directory-ds-getting-started.md)
