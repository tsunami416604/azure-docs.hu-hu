---
title: A Microsoft AppSource és az Azure Marketplace-en listaelem engedélyezése az Azure Active Directoryval |} Az Azure
description: Egy lista típusának engedélyezése az alkalmazás és szolgáltatás a kiadók az Azure Marketplace és appsource-ban az Azure Active Directory használatával.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: 247a45a38d732ace0455c6ca2ebbd5c44c384004
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55732328"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Az appsource-ban és a Piactér listaelem engedélyezése az Azure Active Directoryval

 Az Azure Active Directory (Azure AD) egy olyan felhőalapú identitásszolgáltatás, amely lehetővé teszi a Microsoft-fiókkal hitelesítést. Azure ad-ben az iparági szabványnak megfelelő keretrendszer használ. [További információ az Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Az Azure AD előnyei

A Microsoft AppSource és az Azure Marketplace ügyfeleknek terméken belüli funkciókat a tőzsdei katalógusok kereséséhez használni. Ezek a műveletek az ügyfelek számára, hogy jelentkezzen be a termék szükséges. Az Azure AD-integráció az alábbi előnyöket nyújtja:

- Gyorsabb és a egy optimalizált felhasználói élmény
- Egyszeri bejelentkezés (SSO) több millió vállalati felhasználó számára
- Egységes, a bejelentkezési élmény a különböző partnerek által közzétett alkalmazások
- Méretezhető, platformfüggetlen hitelesítés mobile és a felhőalapú alkalmazások

## <a name="offers-that-require-azure-ad"></a>Az Azure AD igénylő ajánlatok

A különböző [lehetőségeket felsoroló, illetve az típusok](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) appsource-ban és az Azure Marketplace-en rendelkeznie az Azure AD-implementáció különböző követelmények vonatkoznak. Az alábbi táblázat a részletekért lásd:

| **Csomag típusa**    | **Az Azure AD egyszeri bejelentkezés szükséges?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kapcsolatfelvétel | Próbaverzió | Tesztverzió | Tranzakció |
| Virtuális gép | – | Nem | Nem | Nem |
| Az Azure Apps (sablon)  | – | N/A | N/A | – |
| Managed Apps  | – | N/A | – | Nem |
| SaaS  | Nem | Igen | Igen | Igen |
| Containers  | – | N/A | – | Nem |
| Tanácsadási szolgáltatások  | Nem | – | N/A | – |

Technikai SaaS-követelményeivel kapcsolatos további információkért lásd: [SaaS-alkalmazások közzétételi útmutató ajánlat](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Az Azure AD-integráció

- Azure AD integrálása az ajánlata által egyszeri bejelentkezés engedélyezésével kapcsolatos információkért lásd: [Azure Active Directory fejlesztők számára]( https://aka.ms/aaddev).
- Az Azure AD egyszeri bejelentkezéssel kapcsolatos információért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Engedélyezze a egy próbaverziós listaelem

Automatizált ügyfél telepítő növelheti annak esélyét, az átalakítás. Amikor a próbakörnyezet a rendszer átirányítja az ügyfél választja ki a próbaverziós listázása, állíthat be az ügyfél közvetlenül a további bejelentkezési lépések nélkül.

A hitelesítés során az Azure AD küld egy tokent az alkalmazásához vagy ajánlat. A felhasználói adatokat a token által biztosított lehetővé teszi az alkalmazás vagy az ajánlat lévő felhasználói fiók létrehozását. További tudnivalókért lásd: [jogkivonatok minta](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Ha Azure AD használata az alkalmazásban vagy a próbaverziós listázása, egyetlen kattintással hitelesítés engedélyezéséhez meg:

- A vásárlói élményt, a Marketplace-ről a próbaverziós tőzsdei leegyszerűsíthető.
- Karbantartása még akkor is, a terméken belüli felületet működését, amikor a felhasználó a tartomány vagy kísérleti környezet átirányítja a Marketplace-ről.
- Amikor a rendszer átirányítja a felhasználókat, mert további bejelentkezési lépést nem csökkentheti a lemondás valószínűségét.
- Csökkentheti a nagy feltöltése az Azure AD-felhasználók számára üzembe helyezési korlátok.

## <a name="verify-azure-ad-integration"></a>Ellenőrizze az Azure AD-integráció

### <a name="multitenant-solutions"></a>Több-bérlős megoldások

Az Azure AD használatával támogatja a következő műveleteket:

- Az alkalmazás regisztrálása a Marketplace-en kirakattípus egyikében. Nézet [alkalmazásregisztráció](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) vagy [AppSource-tanúsítvány](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) további információt.
- A több-bérlős támogatásának engedélyezése az Azure AD-egykattintásos kipróbálására.

Ha most ismerkedik az Azure AD összevont egyszeri bejelentkezést használ, ezeket a lépéseket:

1. Az alkalmazás regisztrálása a Marketplace-en.
1. Fejlesztés az Azure AD egyszeri bejelentkezés használatával [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) vagy [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. A több-bérlős támogatás funkció engedélyezése az Azure ad-ben a egy próbaverziós érdekében.

### <a name="single-tenant-solutions"></a>Egybérlős megoldások

Használja az Azure AD-támogatás az alábbi műveletek egyikét:

- Vendég felhasználók hozzáadása a címtárhoz használatával [az Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Manuálisan állítsa be az ügyfelek kísérletek használatával a **kapcsolatfelvételi** közzététel lehetőséget.
- Próbálja ki az ügyfél fejleszthet.
- Hozzon létre egy több-bérlős bemutató mintaalkalmazást, amely egyszeri Bejelentkezést használ.

## <a name="next-steps"></a>További lépések

- Győződjön meg arról, hogy [regisztrálva az Azure Marketplace-en](https://azuremarketplace.microsoft.com/sell).
- Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/) létrehozni, vagy végezze el az ajánlatot.
