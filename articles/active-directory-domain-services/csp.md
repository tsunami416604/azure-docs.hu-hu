---
title: Azure AD Domain Services a Cloud Solution Providers szolgáltatáshoz | Microsoft Docs
description: Azure Active Directory Domain Services Azure Cloud Solution-szolgáltatók számára.
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
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754443"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Az Azure Cloud Solution Providers (CSP) Azure Active Directory (AD) tartományi szolgáltatásai
Ez a cikk azt ismerteti, hogyan használható a Azure AD Domain Services egy Azure CSP-előfizetésben.

## <a name="overview-of-azure-csp"></a>Az Azure CSP áttekintése
Az Azure CSP egy Microsoft-partneri program, amely a különböző Microsoft Cloud Services-szolgáltatásokhoz biztosít licenc-csatornát. Az Azure CSP lehetővé teszi a partnerek számára az értékesítések kezelését, a számlázási kapcsolat megadását, a technikai és számlázási támogatás biztosítását, valamint az ügyfél egyetlen kapcsolattartási pontját. Emellett az Azure CSP teljes körű eszközkészletet biztosít, beleértve az önkiszolgáló portált és a kísérő API-kat. Ezek az eszközök lehetővé teszik a CSP-partnerek számára az Azure-erőforrások egyszerű üzembe helyezését és kezelését, valamint az ügyfelek és előfizetésük számlázását.

A [partner Center portál](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) az összes Azure CSP-partner belépési pontként működik. Az ügyfél-felügyeleti funkciókat, automatizált feldolgozást és egyebeket biztosít. Az Azure CSP-partnerek webalapú felhasználói felületen vagy a PowerShell és különféle API-hívások használatával használhatják a partner Center-képességeket.

A következő ábra azt szemlélteti, hogy a CSP-modell hogyan működik magas szinten. A contoso Azure AD-Active Directory rendelkezik. Rendelkeznek egy olyan CSP-vel, amely az Azure CSP-előfizetésében üzembe helyezi és kezeli az erőforrásokat. A contoso rendszeres (közvetlen) Azure-előfizetésekkel is rendelkezhet, amelyek számlázása közvetlenül a contoso számára történik.

![A CSP-modell áttekintése](./media/csp/csp_model_overview.png)

A CSP-partner bérlője három speciális ügynök csoporttal rendelkezik – a rendszergazdai ügynökökkel, az ügyfélszolgálati ügynökökkel és az értékesítési ügynökökkel. A felügyeleti ügynökök csoport a contoso Azure AD-címtárában van hozzárendelve a bérlői rendszergazda szerepkörhöz. Ennek eredményeképpen a CSP-partner rendszergazdai ügynökök csoportjába tartozó felhasználónak van bérlői rendszergazdai jogosultsága a contoso Azure AD-címtárában. Ha a CSP-partner kiépíti az Azure CSP-előfizetést a contoso számára, akkor az adott előfizetéshez tartozó tulajdonosi szerepkörhöz hozzá van rendelve a rendszergazdai ügynökök csoport. Ennek eredményeképpen a CSP-partner rendszergazdai ügynökei rendelkeznek a szükséges jogosultságokkal az Azure-erőforrások, például virtuális gépek, virtuális hálózatok és Azure AD Domain Services a contoso nevében történő kiépítéséhez.

További információ: [Azure CSP – áttekintés](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Az Azure CSP-előfizetések Azure AD Domain Services használatának előnyei
A Azure AD Domain Services Windows Server AD-kompatibilis szolgáltatásokat biztosít az Azure-ban, mint például az LDAP, a Kerberos/NTLM hitelesítés, a tartományhoz való csatlakozás, a csoportházirend és a DNS. Az évtizedek során számos alkalmazás lett létrehozva az AD-vel való együttműködésre ezen képességek használatával. Számos független szoftvergyártó (ISV) létrehozta és üzembe helyezte az alkalmazásokat az ügyfelek telephelyén. Ezek az alkalmazások megterhelik a támogatást, mivel ez gyakran hozzáférést igényel a különböző környezetekhez, ahol ezek az alkalmazások telepítve vannak. Az Azure CSP-előfizetésekkel egyszerűbb alternatíva az Azure méretezése és rugalmassága.

A Azure AD Domain Services mostantól támogatja az Azure CSP-előfizetéseket. Mostantól az ügyfél Azure AD-címtárához kötött Azure CSP-előfizetésben is üzembe helyezheti alkalmazását. Ennek eredményeképpen az alkalmazottak (a támogatási személyzet) kezelhetik, felügyelhetik és szervizelik azokat a virtuális gépeket, amelyeken az alkalmazás üzembe helyezése a szervezet vállalati hitelesítő adataival történik. Emellett Azure AD Domain Services felügyelt tartományt is kiépítheti az ügyfél Azure AD-címtárához. Az alkalmazás az ügyfél felügyelt tartományához csatlakozik. Ezért az alkalmazásban a Kerberos/NTLM, az LDAP vagy a [System. DIRECTORYSERVICES API](/dotnet/api/system.directoryservices) -ra támaszkodó képességek zökkenőmentesen működnek az ügyfél címtárában. A végfelhasználók nagy mértékben kihasználják az alkalmazás szolgáltatásként való felhasználását, anélkül, hogy aggódniuk kellene az alkalmazás üzembe helyezését biztosító infrastruktúra fenntartásával.

Az előfizetésben felhasznált Azure-erőforrásokért (beleértve a Azure AD Domain Servicest is) minden számlázás vissza lesz terhelve. Az ügyfelekkel való kapcsolat teljes körű vezérlést nyújt, amikor az értékesítés, a számlázás, a technikai támogatás stb. Az Azure CSP platform rugalmassága révén a támogatási ügynökök egy kis csoportja számos olyan ügyfelet képes kiszolgálni, akik az alkalmazás példányaival rendelkeznek.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>CSP üzembe helyezési modellek az Azure AD tartományi szolgáltatásokhoz
Az Azure AD Domain Servicest kétféleképpen használhatja Azure CSP-előfizetéssel. Válassza ki a megfelelőt a biztonság és az egyszerűség alapján, amely az ügyfelekkel kapcsolatos.

### <a name="direct-deployment-model"></a>Közvetlen üzembe helyezési modell
Ebben az üzembe helyezési modellben a Azure AD Domain Services az Azure CSP-előfizetéshez tartozó virtuális hálózaton belül engedélyezve van. A CSP-partner rendszergazdai ügynökei a következő jogosultságokkal rendelkeznek:
* Globális rendszergazdai jogosultságok az ügyfél Azure AD-címtárában.
* Az előfizetés tulajdonosának jogosultságai az Azure CSP-előfizetésben.

![Közvetlen üzembe helyezési modell](./media/csp/csp_direct_deployment_model.png)

Ebben az üzembe helyezési modellben a CSP-szolgáltató rendszergazdai ügynökei felügyelhetik az ügyfél identitásait. Ezek a rendszergazdai ügynökök új felhasználókat, csoportokat és alkalmazásokat adhatnak hozzá az ügyfél Azure AD-címtárában stb. Ez az üzembe helyezési modell olyan kisebb szervezetek számára is alkalmas lehet, amelyek nem rendelkeznek dedikált identitás-rendszergazdával, vagy inkább a CSP-partnernek használják az identitások felügyeletét a nevükben.


### <a name="peered-deployment-model"></a>Egyenrangú üzembe helyezési modell
Ebben az üzemi modellben a Azure AD Domain Services az ügyfélhez tartozó virtuális hálózaton belül van engedélyezve, azaz az ügyfél által fizetett közvetlen Azure-előfizetés. A CSP-partner ezután az ügyfél CSP-előfizetéséhez tartozó virtuális hálózaton belül telepítheti az alkalmazásokat. A virtuális hálózatok ezután csatlakozhatnak az Azure Virtual Network-társítás használatával. Ennek eredményeképpen a CSP-partner által az Azure CSP-előfizetésben üzembe helyezett munkaterhelések/alkalmazások kapcsolódhatnak az ügyfél közvetlen Azure-előfizetésében kiosztott felügyelt tartományhoz.

![Egyenrangú üzembe helyezési modell](./media/csp/csp_peered_deployment_model.png)

Ez a telepítési modell a jogosultságok elkülönítését biztosítja, és lehetővé teszi, hogy a CSP-partner segélyszolgálat-ügynökei felügyelik az Azure-előfizetést, és üzembe helyezzük és kezelhetik az erőforrásokat. A CSP-partner helpdesk-ügynökének azonban nem kell globális rendszergazdai jogosultságokkal rendelkeznie az ügyfél Azure AD-címtárában. Az ügyfél identitás-rendszergazdái továbbra is kezelhetik a szervezetük identitásait.

Ez az üzembe helyezési modell olyan forgatókönyvekhez használható, ahol az ISV-k (független szoftvergyártók) a helyszíni alkalmazás egy üzemeltetett verzióját biztosítják, amely szintén csatlakoznia kell az ügyfél AD-hez.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Azure AD Domain Services felügyelt tartományok felügyelete a CSP-előfizetésekben
A következő fontos szempontokat kell figyelembe venni egy felügyelt tartomány Azure CSP-előfizetésben való felügyeletekor:

* **A CSP felügyeleti ügynökök a hitelesítő adataik használatával létrehozhatnak egy felügyelt tartományt:** A Azure AD Domain Services támogatja az Azure CSP-előfizetéseket. Ezért a CSP-partner felügyeleti ügynökök csoportjához tartozó felhasználók létrehozhatnak egy új Azure AD Domain Services felügyelt tartományt.

* A **kriptográfiai szolgáltatók létrehozhatnak új felügyelt tartományokat az ügyfelek számára a PowerShell használatával:** A részletekért lásd: [Azure ad Domain Services engedélyezése a PowerShell használatával](powershell-create-instance.md) .

* **A CSP rendszergazdai ügynökei nem végezhetnek folyamatos felügyeleti feladatokat a felügyelt tartományon a hitelesítő adataik használatával:** A CSP rendszergazdai felhasználói a felügyelt tartományon belül nem hajthatnak végre rutin felügyeleti feladatokat a hitelesítő adataik használatával. Ezek a felhasználók kívül esik az ügyfél Azure AD-címtárán, és a hitelesítő adataik nem érhetők el az ügyfél Azure AD-címtárában. Ezért Azure AD Domain Services nem fér hozzá ezekhez a felhasználókhoz a Kerberos-és NTLM-jelszó-kivonatokhoz. Ennek eredményeképpen az ilyen felhasználók nem hitelesíthetők Azure AD Domain Services felügyelt tartományokon.

  > [!WARNING]
  > **A felügyelt tartományon folyamatban lévő felügyeleti feladatok végrehajtásához létre kell hoznia egy felhasználói fiókot az ügyfél címtárában.**
  > A felügyelt tartományhoz a CSP rendszergazda felhasználójának hitelesítő adatait használva nem tud bejelentkezni. Használja az ügyfél Azure AD-címtárához tartozó felhasználói fiók hitelesítő adatait. Ezekre a hitelesítő adatokra szüksége van olyan feladatokhoz, mint például a virtuális gépek csatlakoztatása a felügyelt tartományhoz, a DNS felügyelete, a Csoportházirend felügyelete stb.
  >

* **A folyamatos felügyelethez létrehozott felhasználói fiókot fel kell venni az "HRE DC rendszergazdák" csoportba:** Az "HRE DC rendszergazdák" csoport jogosultságokkal rendelkezik bizonyos delegált felügyeleti feladatok végrehajtásához a felügyelt tartományon. Ezek a feladatok közé tartoznak a DNS konfigurálása, a szervezeti egységek létrehozása, a Csoportházirend felügyelete stb. Ahhoz, hogy egy CSP-partner felügyelt tartományon hajtson végre ilyen feladatokat, felhasználói fiókot kell létrehoznia az ügyfél Azure AD-címtárában. A fiók hitelesítő adatait meg kell osztani a CSP-partner rendszergazdai ügynökével. Ezt a felhasználói fiókot is fel kell venni az "HRE DC-rendszergazdák" csoportba, hogy engedélyezze a konfigurációs feladatokat a felügyelt tartományon a felhasználói fiók használatával történő végrehajtáshoz.


## <a name="next-steps"></a>Következő lépések
* [Regisztráljon az Azure CSP programba](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) , és kezdje el létrehozni a vállalatot az Azure CSP-n keresztül.
* Tekintse át az [Azure CSP-ben elérhető Azure-szolgáltatások](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)listáját.
* [Az Azure AD Domain Services engedélyezése a PowerShell-lel](powershell-create-instance.md)
* [Ismerkedés a Azure AD Domain Services](tutorial-create-instance.md)
