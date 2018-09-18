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
ms.openlocfilehash: d7fd09928c0a687755d216e7f10f7eac23677c63
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986424"
---
# <a name="enable-a-microsoft-appsource-and-azure-marketplace-listing-by-using-azure-active-directory"></a>A Microsoft AppSource és az Azure Marketplace-en listaelem engedélyezése az Azure Active Directoryval

A Microsoft Azure Active Directory (Azure AD) az olyan felhőalapú identitásszolgáltatás, amely lehetővé teszi a Microsoft-fiókkal hitelesítést iparági szabványnak megfelelő keretrendszer használatával.  Az Azure AD kapcsolatos további információkért lásd: [Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="benefits-of-using-azure-active-directory"></a>Azure Active Directory használatának előnyei

A Microsoft AppSource és az Azure Marketplace ügyfeleknek terméken belüli funkciókat a tőzsdei katalógusokat, ezért a jelentkezzen be a termék kereséséhez használni.  Az alkalmazás integrálja az Azure AD-vel, gyorsítása engagement, és optimalizálhatja a felhasználói élményt. Azure ad-ben:

- Lehetővé teszi, hogy egyszeri bejelentkezési (SSO) több millió vállalati felhasználó számára.
- Lehetővé teszi, hogy egységes felhasználói bejelentkezési élmény különböző partnerek által közzétett alkalmazásokban.
- Méretezhető, platformfüggetlen hitelesítés mobil és a felhőalapú alkalmazásokat.

Az alábbi szakaszban részletezett egyes ajánlatai piactéren való közzétételéhez az Azure AD megvalósításához szükséges.

## <a name="azure-active-directory-requirements"></a>Az Azure Active Directory követelményei

Különböző [lehetőségeket felsoroló, illetve az típusok](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) a Microsoft AppSource és az Azure Marketplace-en.  Azure AD-követelmények ezen könyvtárlista beállításai és ajánlattípusokról alább láthatók:

| **Csomag típusa**    | **AAD egyszeri bejelentkezés szükséges?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kapcsolatfelvétel | Próbaverzió | Kipróbálás | Tranzakció |
| Virtuális gép | – | Nem | Nem | Nem |
| Az Azure Apps (sablon)  | – | N/A | N/A | – |
| Felügyelt alkalmazások  | – | N/A | – | Nem |
| SaaS  | Nem | Igen | Igen | Igen |
| Containers  | – | N/A | – | Nem |
| Tanácsadási szolgáltatások  | Nem | – | N/A | – |

Technikai SaaS-követelményeivel kapcsolatos további információkért lásd: [SaaS-alkalmazások közzétételi útmutató ajánlat](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="integration-with-azure-active-directory"></a>Az Azure Active Directory-integráció

Hogyan integrálható az Azure AD egyszeri bejelentkezés engedélyezése a további információért látogasson el https://aka.ms/aaddev.

Az Azure AD egyszeri Bejelentkezéssel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)?

## <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>A próbaverzió listázása az Azure Active Directoryval engedélyezése

Miután az ügyfél választja ki a próbaidőszakot, listázás, a piactéren, a rendszer átirányítja az ügyfél a próbakörnyezet. A kísérleti környezetben állíthat be az ügyfelek közvetlenül a további bejelentkezési lépések nélkül. Az alkalmazás vagy az ajánlat kap egy tokent az Azure AD-hitelesítés során. A jogkivonatot, amellyel a felhasználói fiók létrehozása az alkalmazás vagy az ajánlat értékes felhasználói adatokat tartalmazza. Ügyfél telepítő automatizálhatja és átalakítás valószínűségének növelése.

Az Azure AD-hitelesítés során küldött jogkivonatban kapcsolatos további információkért lásd: [jogkivonatok minta](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Azure AD használata az alkalmazásban vagy a próbaverzió egykattintásos hitelesítés engedélyezéséhez. Azure ad-ben a következő előnyöket biztosítja: 
*   Egyszerűsítse le a felhasználói élmény a próbaverzió a Marketplace-ről.
*   Karbantartása során a terméken belüli felületet, akkor is, ha a felhasználó a tartomány vagy kísérleti környezet átirányítja a Marketplace-ről.
*   Csökkenti a lemondás esetén az átirányítási, valószínűségét, mert további bejelentkezési lépést nem.
*   Csökkentheti a nagy feltöltése az Azure AD-felhasználók számára üzembe helyezési korlátok.

### <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Ellenőrizze a Marketplace-en az Azure AD-integrációs: több-Bérlős alkalmazások
Az Azure AD használatával a megoldás a következő beállításokat támogatja:
*   Az alkalmazás regisztrálása az kirakattípus a Marketplace-en.
*   A több-bérlős támogatásának engedélyezése az Azure AD-egykattintásos kipróbálására.

Alkalmazásregisztráció kapcsolatos további információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

Ha most ismerkedik az Azure AD összevont egyszeri bejelentkezést (SSO), a lépések végrehajtása:
1.  Az alkalmazás regisztrálása a Marketplace-en. 
2.  Az Azure AD SSO fejlesztése az OAuth 2.0 vagy OpenID Connect használatával.
    *   OAuth 2.0 kapcsolatos további információkért lásd: [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
    *   Open ID Connect kapcsolatos további információkért lásd: [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
3.  A több-bérlős támogatás funkció engedélyezése az Azure ad-ben a egy próbaverziós érdekében.
    
    AppSource-tanúsítvány kapcsolatos további információkért lásd: [AppSource-tanúsítvány](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

### <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Ellenőrizze a Marketplace-en az Azure AD-integrációs: egybérlős alkalmazások
Azure AD használata az egybérlős megoldás támogatja a következő lehetőségek közül: 
*   Felhasználók hozzáadása a címtárhoz, vendég felhasználók Azure Active Directory B2B használatával (Azure AD B2B). Az Azure AD B2B kapcsolatos további információkért lásd: [Mi az Azure AD B2B együttműködés](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Kézi beállítása kísérletek az ügyfelek az ügyfél használatával Me közzététel lehetőséget.
*   Próbálja ki az ügyfél fejleszthet.
*   Hozzon létre egy több-bérlős bemutató mintaalkalmazást, amely egyszeri Bejelentkezést használ.

## <a name="next-steps"></a>További lépések

Ha ezt még nem tette meg, 
- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a Marketplace-en.

Ha regisztrálva van és a egy új ajánlat létrehozása vagy egy meglévő, dolgozik
- [Jelentkezzen be a Cloud Partner portálra](https://cloudpartner.azure.com/) létrehozni, vagy végezze el az ajánlatot.

