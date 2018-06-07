---
title: Azure AD használata ingyenes próbaverzió engedélyezése |} Azure
description: Próbaverzió listaelem típus az Azure Active Directoryval (Azure AD) engedélyezése az Azure piactér és AppSource az alkalmazás és szolgáltatás-közzétevők
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
ms.openlocfilehash: 4140ba98c0c65c22674c61dc7266818af904e777
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825148"
---
# <a name="enable-trial-using-azure-ad"></a>Engedélyezze az Azure AD használata ingyenes próba  
Azure Active Directory (Azure AD) egy identitás felhőszolgáltatás, amely lehetővé teszi a hitelesítést a Microsoft munkahelyi vagy iskolai fiók szabványos keretrendszerek használatával: OAuth és az OpenID Connect.  
*   Az Azure AD kapcsolatos további információkért látogasson el az Azure Active Directory lapon található [azure.microsoft.com/services/active-directory](https://azure.microsoft.com/services/active-directory).  

Ön és ügyfelei az alábbiakra hitelesítése az Azure AD marketplace a. Miután az ügyfél választja ki, hogy a próbaidőszak listázása a piactéren, a rendszer átirányítja az ügyfél a kísérleti környezetben.  A kísérleti környezetben előfordulhat, hogy beállította az ügyfél közvetlenül a további bejelentkezési lépések nélkül. Az alkalmazás vagy az ajánlat megkapja a jogkivonatot az Azure AD a felhasználói fiók létrehozása az app vagy ajánlat értékes felhasználói adatokat tartalmazó hitelesítés során. Előfordulhat, hogy a telepítés automatizálása és átalakítás érdekében.  
*   Az Azure AD-hitelesítés során küldött jogkivonat kapcsolatos további információkért látogasson el a minta jogkivonatok szakaszban található [docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)

Használja az Azure AD az alkalmazásban és a próbaidőszak egy kattintással hitelesítést.  
*   A felhasználói élmény a próba a piactér teheti gördülékennyé.  
*   Karbantartása során a terméken belüli élményt, akkor is, ha a felhasználó a tartomány vagy kísérleti környezetben átirányítja a piactéren.  
*   Csökkenti az átirányítás, a lemondás valószínűségét, mert nincs további bejelentkezési lépéseket.  
*   Csökkentse a központi telepítés használatával az Azure Active Directory-felhasználók nagy sokaságát.  

## <a name="verify-your-azure-ad-integration-on-the-marketplace-multitenant-apps"></a>Ellenőrizze az Azure AD-integrációs a piactéren: több-Bérlős alkalmazásokhoz  
Az alábbi lehetőségek támogatja az Azure AD megoldást.  
*   Regisztrálja az alkalmazást a kirakatokkal a piactéren.  
*   A több-bérlős támogatással funkció engedélyezése az Azure AD egy egyetlen kattintással próbaverziója eléréséhez.  
    *   Alkalmazás regisztrálása kapcsolatos további információkért látogasson el a integrálása alkalmazások az Azure Active Directoryban található lap [docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Ha most ismerkedik az Azure AD összevont egyszeri bejelentkezést (SSO), majd kövesse az alábbi lépéseket.  
1.  Regisztrálja az alkalmazást a piactéren. 
2.  Egyszeri bejelentkezés az Azure ad-val fejlesztése az OAuth 2.0 vagy az OpenID Connect.  
    *   OAuth 2.0 kapcsolatos további információkért látogasson el a OAuth 2.0-s lapon található [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).  
    *   Open ID Connect kapcsolatos további információkért látogasson el a lapon található OpenID Connect [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  
3.  A több-bérlős támogatással funkció engedélyezése az Azure AD-egykattintásos próba felületet tudjon biztosítani.  
    *   AppSource hitelesítő kapcsolatos további információkért látogasson el a hitelesítésszolgáltató lapon található AppSource [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

## <a name="verify-your-azure-ad-integration-on-the-marketplace-single-tenant-apps"></a>Ellenőrizze az Azure AD-integrációs a piactéren: Single-bérlői alkalmazások  
Támogatja a következő lehetőségek egyikét a single-bérlős megoldást kínál.  
*   Felhasználók hozzáadása a könyvtárhoz vendégként Azure AD B2B használatával.  
    *   Azure AD B2B kapcsolatos további információkért látogasson el a Mi az Azure AD B2B együttműködés lapon található [docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Manuálisan állítson be kísérletek forduljon Me felhasználói számára  
*   Ügyfél próbát fejlesztéséhez.  
*   Hozzon létre egy több-bérlős bemutató mintaalkalmazás egyszeri Bejelentkezést.  

## <a name="next-steps"></a>További lépések
*   Látogasson el a [AppSource Publisher útmutató és az Azure piactér](./marketplace-publishers-guide.md) lap.  
 
---  

