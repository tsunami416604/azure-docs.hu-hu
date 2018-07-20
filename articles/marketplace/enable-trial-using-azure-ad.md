---
title: Az Azure Marketplace-en próbaverzióra engedélyezése az Azure Active Directoryval |} Az Azure
description: Engedélyezze a próbaverzióra listaelem típusa, alkalmazás és szolgáltatás-kiadók az Azure Marketplace és appsource-ban az Azure Active Directory használatával.
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
ms.date: 06/04/2018
ms.author: ellacroi
ms.openlocfilehash: c5b7b4967c1acef733d366e651d50706db42aace
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160467"
---
# <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>A próbaverzió listázása az Azure Active Directoryval engedélyezése

Az Azure Active Directory (Azure AD) egy olyan felhőalapú identitásszolgáltatás, amely lehetővé teszi a hitelesítést a Microsoft a munkahelyi vagy iskolai fiók iparági szabványnak megfelelő keretrendszer használatával. Az Azure AD OAuth és OpenID Connect hitelesítést támogatja. A [Azure Marketplace-en](https://azuremarketplace.microsoft.com) az Azure AD segítségével, és az ügyfelek hitelesítéséhez.

Az Azure AD kapcsolatos további információkért lásd: [Azure Active Directory](https://azure.microsoft.com/services/active-directory).

Miután az ügyfél választja ki a próbaidőszakot, listázás, a piactéren, a rendszer átirányítja az ügyfél a próbakörnyezet. A kísérleti környezetben állíthat be az ügyfelek közvetlenül a további bejelentkezési lépések nélkül. Az alkalmazás vagy az ajánlat kap egy tokent az Azure AD-hitelesítés során. A jogkivonatot, amellyel a felhasználói fiók létrehozása az alkalmazás vagy az ajánlat értékes felhasználói adatokat tartalmazza. Ügyfél telepítő automatizálhatja és átalakítás valószínűségének növelése.

Az Azure AD-hitelesítés során küldött jogkivonatban kapcsolatos további információkért lásd: [jogkivonatok minta](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Azure AD használata az alkalmazásban vagy a próbaverzió egykattintásos hitelesítés engedélyezéséhez. Azure ad-ben a következő előnyöket biztosítja: 
*   Egyszerűsítse le a felhasználói élmény a próbaverzió a Marketplace-ről.
*   Karbantartása során a terméken belüli felületet, akkor is, ha a felhasználó a tartomány vagy kísérleti környezet átirányítja a Marketplace-ről.
*   Csökkenti a lemondás esetén az átirányítási, valószínűségét, mert további bejelentkezési lépést nem.
*   Csökkentheti a nagy feltöltése az Azure AD-felhasználók számára üzembe helyezési korlátok.

## <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Ellenőrizze a Marketplace-en az Azure AD-integrációs: több-Bérlős alkalmazások
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

## <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Ellenőrizze a Marketplace-en az Azure AD-integrációs: egybérlős alkalmazások
Azure AD használata az egybérlős megoldás támogatja a következő lehetőségek közül: 
*   Felhasználók hozzáadása a címtárhoz, vendég felhasználók Azure Active Directory B2B használatával (Azure AD B2B).
    
    Az Azure AD B2B kapcsolatos további információkért lásd: [Mi az Azure AD B2B együttműködés](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Kézi beállítása kísérletek az ügyfelek az ügyfél használatával Me közzététel lehetőséget.
*   Próbálja ki az ügyfél fejleszthet.
*   Hozzon létre egy több-bérlős bemutató mintaalkalmazást, amely egyszeri Bejelentkezést használ.

## <a name="next-steps"></a>További lépések
*   Tekintse át a [közzétételi útmutató az Azure Marketplace és AppSource](./marketplace-publishers-guide.md).
