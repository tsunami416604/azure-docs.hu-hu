---
title: Microsoft AppSource és Azure Marketplace-termékismertető engedélyezése az Azure Active Directory használatával | Azure
description: Engedélyezze a hirdetéstípust az Azure Active Directory használatával az Azure Marketplace-en és az AppSource alkalmazás- és szolgáltatásközzétevők számára.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: dsindona
ms.openlocfilehash: 45855038e60dcdc3be4f98cfdceed69df5e8c946
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286319"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>AppSource- és Marketplace-termékoldal engedélyezése az Azure Active Directory használatával

 Az Azure Active Directory (Azure AD) egy felhőalapú identitásszolgáltatás, amely lehetővé teszi a Microsoft-fiókkal való hitelesítést. Az Azure AD iparági szabványnak megfelelő keretrendszereket használ. [További információ az Azure Active Directoryról.](https://azure.microsoft.com/services/active-directory)

## <a name="azure-ad-benefits"></a>Az Azure AD előnyei

A Microsoft AppSource és az Azure Marketplace-en lévő ügyfelek terméken belüli élményben használatosak a termékkatalógusokban való kereséshez. Ezek a műveletek megkövetelik az ügyfelektől, hogy jelentkezzenek be a termékbe. Az Azure AD-integráció a következő előnyökkel jár:

- Gyorsabb elköteleződés és optimalizált ügyfélélmény
- Egyszeri bejelentkezés (SSO) több millió vállalati felhasználó számára
- Konzisztens, bejelentkezési élmény a különböző partnerek által közzétett alkalmazások között
- Méretezhető, platformfüggetlen hitelesítés mobil- és felhőalapú alkalmazásokhoz

## <a name="offers-that-require-azure-ad"></a>Az Azure AD-t igénylő ajánlatok

Az AppSource és az Azure Marketplace különböző [beállítási lehetőségei és ajánlattípusai](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) eltérő követelményekkel rendelkeznek az Azure AD-implementációhoz. A részleteket lásd az alábbi táblázatban:

| **Ajánlat típusa**    | **Az Azure AD-sSO szükséges?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kapcsolat | Próbaverzió | Tesztverzió | Transact |
| Virtuális gép | N/A | Nem | Nem | Nem |
| Azure Apps (megoldássablon)  | N/A | N/A | N/A | N/A |
| Felügyelt alkalmazások  | N/A | N/A | N/A | Nem |
| SaaS  | Nem | Igen | Igen | Igen |
| Containers  | N/A | N/A | N/A | Nem |
| Tanácsadási szolgáltatások  | Nem | N/A | N/A | N/A |

A SaaS műszaki követelményeiről további információt a [SaaS-alkalmazások közzétételi útmutatójában talál.](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)

## <a name="azure-ad-integration"></a>Azure AD-integráció

- Az Azure AD-nek a termékismertetőbe való integrálásával történő egyszeri bejelentkezés engedélyezéséről az [Azure Active Directory fejlesztőknek című]( https://aka.ms/aaddev)témakörben talál tájékoztatást.
- Az Azure AD egyszeri bejelentkezéssel kapcsolatos részletekért olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="enable-a-trial-listing"></a>Próbalista engedélyezése

Az automatikus ügyfélbeállítás növelheti a konverzió valószínűségét. Amikor az ügyfél kiválasztja a próbaverziós adatlapot, és átirányítja a próbakörnyezetbe, közvetlenül is beállíthatja az ügyfelet anélkül, hogy további bejelentkezési lépéseket kellene megkövetelnie.

A hitelesítés során az Azure AD egy jogkivonatot küld az alkalmazásnak vagy az ajánlatnak. A jogkivonat által biztosított felhasználói adatok lehetővé teszik egy felhasználói fiók létrehozását az alkalmazásban vagy az ajánlatban. További információ: [Mintatokenek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Ha az Azure AD segítségével engedélyezi az egykattintásos hitelesítést az alkalmazásban vagy a próbaverzióban, a következőket teszi:

- Egyszerűsítse az ügyfélélményt a Piactérről a próbaverziós adatlapra.
- A terméken belüli élmény érzete akkor is megőrizheti, ha a felhasználót a Piactérről a tartományba vagy a próbakörnyezetbe irányítja át.
- Csökkentse az elhagyás valószínűségét, amikor a felhasználók átirányításra kerülnek, mert nincsenek további bejelentkezési lépések.
- Csökkentse a telepítési akadályokat az Azure AD-felhasználók nagy számú számára.

## <a name="verify-azure-ad-integration"></a>Az Azure AD-integráció ellenőrzése

### <a name="multitenant-solutions"></a>Több-bérlős megoldások

Az Azure AD segítségével támogassa a következő műveleteket:

- Regisztrálja alkalmazását a Marketplace egyik kirakatában. További információkért tekintse meg [az alkalmazásregisztrációt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) vagy az [AppSource-minősítést.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)
- Engedélyezze a többbérlős támogatási funkciót az Azure AD-ben, hogy egy kattintással elérhető próbaverziós élményt kapjon.

Ha most használja az Azure AD összevont egyszeri bejelentkezés, az alábbi lépésekkel:

1. Regisztrálja alkalmazását a Piactéren.
1. Az SSO fejlesztése az Azure AD-vel az [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) vagy [az OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)használatával.
1. Engedélyezze a többbérlős támogatási funkciót az Azure AD-ben, hogy egy kattintással elérhető próbaverziós élményt nyújtson.

### <a name="single-tenant-solutions"></a>Egybérlős megoldások

Az Azure AD használatával az alábbi műveletek egyikét támogatva:

- Vendégfelhasználók hozzáadása a címtárhoz az [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)használatával.
- Manuálisan állíthat be próbaverziókat az ügyfelek számára a **Kapcsolat** közzététele beállítással.
- Ügyfélenkénti tesztvezetés kidolgozása.
- Hozzon létre egy több-bérlős minta bemutató alkalmazást, amely egyszeri használatú.

## <a name="next-steps"></a>További lépések

- Győződjön meg arról, hogy [regisztrált az Azure Marketplace-en.](https://azuremarketplace.microsoft.com/sell)
- Az ajánlat létrehozásáról és teljesítéséről további információt a [Partnerközpont-fiók létrehozásáról](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) talál.
