---
title: A Microsoft kereskedelmi Marketplace-ajánlatának integrálása a Azure Active Directory
description: A Azure Active Directory használatával hitelesítheti Microsoft AppSource és az Azure Marketplace ajánlatait.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/24/2020
ms.openlocfilehash: a6e304e5ffeab8f0a44cbdfe1566465f2b9bf34a
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607422"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>A kereskedelmi Marketplace-lista integrálása a Azure Active Directory

 Ez a cikk a kereskedelmi piactérek Azure Active Directory (Azure AD) szolgáltatással való integrálásának követelményeit ismerteti. Az Azure AD egy felhőalapú identitás-szolgáltatás, amely az iparági szabványnak megfelelő keretrendszereket használ a Microsoft-fiók való hitelesítés engedélyezéséhez. [További információ a Azure Active Directoryról](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Az Azure AD előnyei

A Microsoft AppSource és az Azure Marketplace ügyfelei a terméken belüli tapasztalatokat használják az online áruházbeli katalógusok kereséséhez. Ezeknek a műveleteknek a használatához a felhasználóknak be kell jelentkezniük a termékbe. Az Azure AD-integráció a következő előnyöket biztosítja:

- Gyorsabb engagement és optimalizált felhasználói élmény
- Egyszeri bejelentkezés (SSO) több millió vállalati felhasználó számára
- Konzisztens, bejelentkezési élmény különböző partnerek által közzétett alkalmazások között
- Méretezhető, többplatformos hitelesítés mobil-és felhőalapú alkalmazásokhoz

## <a name="offers-that-require-azure-ad"></a>Az Azure AD-t igénylő ajánlatok

A különböző kereskedelmi Piactéri [lehetőségek és az ajánlattételi típusok](determine-your-listing-type.md) különböző követelményekkel rendelkeznek az Azure ad megvalósításához. További részleteket a következő táblázatban talál.

| Csomag típusa    | Meg kell adnia az Azure AD SSO-t a kapcsolatfelvételhez?  | Azure AD SSO szükséges a próbaverzióhoz? | Azure AD SSO szükséges a test Drive-hoz?  | Azure AD SSO szükséges a Transacthoz |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
| Virtuális gép | n.a. | Nem | Nem | Nem |
| Azure-alkalmazások (megoldás-sablon)  | N.A. | N.A. | N.A. | N.A. |
| Felügyelt alkalmazások  | N.A. | N.A. | N.A. | No |
| SaaS  | Nem | Igen | Igen | Igen |
| Tárolók  | N.A. | N.A. | N.A. | No |
| Tanácsadási szolgáltatások  | No | N.A. | N.A. | N.A. |

A SaaS műszaki követelményeivel kapcsolatos további információkért lásd: [Az Azure ad és a transactd SaaS-ajánlatok a kereskedelmi piactéren](./azure-ad-saas.md).

## <a name="azure-ad-integration"></a>Azure AD-integráció

- Az Azure AD szolgáltatáshoz szolgáltatott szoftveres (SaaS) ajánlatokhoz való integrálásával kapcsolatos további információkért lásd: [Az Azure ad és a transactd SaaS-ajánlatok a kereskedelmi piactéren](./azure-ad-saas.md).
- További információ az egyszeri bejelentkezés engedélyezéséről az Azure AD-nek a listához való integrálásával: [Azure Active Directory for Developers](../active-directory/develop/index.yml).
- Az Azure AD egyszeri bejelentkezéssel kapcsolatos részletekért tekintse meg [a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory?](../active-directory/manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="enable-a-trial-listing"></a>Próbaverziók listájának engedélyezése

Az automatizált ügyfelek telepítése növelheti a konverzió valószínűségét. Ha az ügyfél kiválasztja a próbaidőszakot, és a rendszer átirányítja a próbaverziós környezetbe, az ügyfelet közvetlenül a további bejelentkezési lépések megkövetelése nélkül is beállíthatja.

A hitelesítés során az Azure AD jogkivonatot küld az alkalmazásnak vagy az ajánlatnak. A jogkivonat által megadott felhasználói adatok lehetővé teszik felhasználói fiók létrehozását az alkalmazásban vagy az ajánlatban. További információ: [minta tokenek](../active-directory/develop/id-tokens.md).

Ha az Azure AD-t használja az egykattintásos hitelesítés engedélyezéséhez az alkalmazásban vagy a próbaverzióban, a következőt kell tennie:

- Egyszerűsítse a kereskedelmi piactér felhasználói élményét a próbaverziók listájára.
- Ha a felhasználót a kereskedelmi piactérről a tartományba vagy a próbaverziós környezetbe irányítják, akkor is tapasztalhatja a terméken belüli élményt.
- Csökkentse annak a valószínűségét, hogy a rendszer elutasítja a felhasználókat, mert nincsenek további bejelentkezési lépések.
- Csökkentse az Azure AD-felhasználók nagy sokaságának üzembe helyezési korlátait.

## <a name="verify-azure-ad-integration"></a>Az Azure AD-integráció ellenőrzése

### <a name="multitenant-solutions"></a>Több-bérlős megoldások

Az Azure AD használata a következő műveletek támogatásához:

- Az alkalmazás regisztrálása az egyik kereskedelmi piactér online áruházban. További információért tekintse meg az [alkalmazás regisztrációjának](../active-directory/develop/quickstart-register-app.md) vagy [AppSource minősítését](../active-directory/azuread-dev/howto-get-appsource-certified.md) .
- Az Azure AD bérlős-támogatás funkciójának engedélyezésével egyetlen kattintással elsajátíthatja a próbaverziót.

Ha most ismerkedik az Azure AD összevont egyszeri bejelentkezéssel, hajtsa végre a következő lépéseket:

1. Regisztráljon az alkalmazást a kereskedelmi piactéren.
1. Az SSO-t az Azure AD-vel [OAuth 2,0](../active-directory/azuread-dev/v1-protocols-oauth-code.md) vagy [OpenID Connect](../active-directory/azuread-dev/v1-protocols-openid-connect-code.md)használatával fejlesztheti.
1. Az Azure AD bérlős-támogatás funkciójának engedélyezése egy kattintással elérhető próbaverziót biztosít.

### <a name="single-tenant-solutions"></a>Egybérlős megoldások

Az Azure AD használata a következő műveletek egyikének támogatásához használható:

- Adja hozzá a vendég felhasználókat a címtárhoz az [Azure ad B2B](../active-directory/b2b/what-is-b2b.md)használatával.
- Manuálisan állítson be próbaverziókat az ügyfelek számára a **kapcsolat** küldése közzétételi lehetőség használatával.
- Felhasználónkénti tesztelési meghajtó fejlesztése.
- Hozzon létre egy több-bérlős minta bemutató alkalmazást, amely egyszeri bejelentkezést használ.

## <a name="next-steps"></a>Következő lépések

Ha még nem tette meg, 

- [További](https://azuremarketplace.microsoft.com/sell) információ a kereskedelmi Piactérről.

A partner Centerben való regisztrációhoz hozzon létre egy új ajánlatot, vagy dolgozzon egy meglévőn:

- Az ajánlat létrehozásához vagy befejezéséhez [Jelentkezzen be a partner Centerben](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .
