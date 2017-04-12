---
title: "Az Azure Active Directory fejlesztői útmutatója | Microsoft Docs"
description: "Ez a cikk az Azure Active Directory fejlesztőknek készített erőforrásairól nyújt átfogó képet."
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/09/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 45ff2ff08cb36f9a4bf49ff04de149accf157e0e
ms.lasthandoff: 03/31/2017


---
# <a name="azure-active-directory-developers-guide"></a>Az Azure Active Directory fejlesztői útmutatója
## <a name="overview"></a>Áttekintés
Mivel az Azure Active Directory (AD) egy identitáskezelést szolgáltatásként nyújtó (IDMaaS) platform, hatékony módot biztosít a fejlesztők számára, hogy az alkalmazásokba integrálhassák az identitáskezelést. A következő cikkek az Azure AD implementálásának és fő jellemzőinek áttekintését nyújtják. Ajánlott ezeket sorrendben elolvasni, vagy ha készen áll, folytassa az [Első lépések](#getting-started) című szakasszal.

1. [Az Azure AD integrációjának előnyei:](active-directory-how-to-integrate.md) Megtudhatja, miért kínálja a legjobb megoldást az Azure AD integrációja a biztonságos bejelentkezéshez és hitelesítéshez.
2. [Az Azure AD hitelesítési forgatókönyvei:](active-directory-authentication-scenarios.md) Kihasználhatja az Azure AD egyszerűsített hitelesítésének előnyeit az alkalmazásba történő bejelentkezéshez.
3. [Alkalmazások integrálása az Azure AD-vel:](active-directory-integrating-applications.md) Megtudhatja, hogyan adhat hozzá, frissíthet és távolíthat el alkalmazásokat az Azure AD-ből, és megismerheti az integrált alkalmazások arculati útmutatóját.
4. [Microsoft Graph](https://graph.microsoft.io/) és [Azure AD Graph API](active-directory-graph-api.md): programozott módon érheti el az Azure AD-t REST API-végpontokon keresztül. **Az Azure AD Graph API helyett ajánljuk a Microsoft Graph használatát az Azure Active Directory erőforrásainak eléréséhez.** A fejlesztési energiáinkat mostantól a Microsoft Graph-ra koncentráljuk, az Azure AD Graph API-hoz nem tervezünk további fejlesztéseket. Nagyon korlátozott azon forgatókönyvek száma, amelyeknél az Azure AD Graph API használata még elegendő. További információért tekintse meg a [Microsoft Graph vagy Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) blogbejegyzést az Office fejlesztői központjában.
5. [Az Azure AD hitelesítési kódtárai:](active-directory-authentication-libraries.md) A .NET, JavaScript, Objective-C, Android és további Azure AD-hitelesítési kódtárakkal könnyedén hitelesítheti a felhasználókat a hozzáférési tokenek beszerzése érdekében.

## <a name="getting-started"></a>Bevezetés
Ezek az oktatóanyagok több platformot is lefednek, és segíthetnek, hogy minél gyorsabban elkezdje az Azure Active Directoryval folytatott fejlesztést. Előfeltételként [be kell szereznie egy Azure Active Directory-bérlőt](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Mobil- és számítógépes alkalmazások első lépéseinek útmutatói
| [![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md) | [![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md) | [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md) | [![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md) | 
|:---:|:---:|:---:|:---:|:---:|
| [iOS](active-directory-devquickstarts-ios.md) |[Android](active-directory-devquickstarts-android.md) |[.NET](active-directory-devquickstarts-dotnet.md) |[Univerzális</br>Windows](active-directory-devquickstarts-windowsstore.md) |

|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md) | [![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md) | [![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md) |
|:---:|:---:|:---:|
|[Xamarin](active-directory-devquickstarts-xamarin.md) |[Cordova](active-directory-devquickstarts-cordova.md) |[Integráció közvetlenül</br>OAuth 2.0-val](active-directory-protocols-oauth-code.md) |

### <a name="web-application-quick-start-guides"></a>Webalkalmazások első lépéseinek útmutatói
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md) | [![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md) | [![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md) |
|:---:|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapp-dotnet.md) |[Java](active-directory-devquickstarts-webapp-java.md) |[AngularJS](active-directory-devquickstarts-angular.md) |[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[Integráció közvetlenül</br> OpenID Connecttel](active-directory-protocols-openid-connect-code.md) |

| [![Javascript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md) |
|:---:|:---:|:---:|
|[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[Integráció közvetlenül</br> OpenID Connecttel](active-directory-protocols-openid-connect-code.md) |

### <a name="web-api-quick-start-guides"></a>Webes API-k első lépéseinek útmutatói
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md) |
|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapi-dotnet.md) |[Node.js](active-directory-devquickstarts-webapi-nodejs.md) |

### <a name="microsoft-graph-and-azure-ad-graph-api-quick-start-guides"></a>A Microsoft Graph és az Azure AD Graph API első lépéseinek útmutatói
| [![Microsoft Graph](./media/active-directory-developers-guide/msgraph.png)](https://developer.microsoft.com/graph/quick-start) | [![Azure AD Graph API](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md) |
|:---:|:---:|
| [Microsoft Graph](https://developer.microsoft.com/graph/quick-start) | [Azure AD Graph API](active-directory-graph-api-quickstart.md) |

## <a name="how-tos"></a>Használati útmutatók
Ezek a cikkek ismertetik, hogyan végezhet el bizonyos feladatokat az Azure Active Directoryval:

* [Azure AD-bérlő beszerzése](active-directory-howto-tenant.md)
* [Azure AD-felhasználók bejelentkeztetése több-bérlős alkalmazásminta használatával](active-directory-devhowto-multi-tenant-overview.md)
* [Tanúsítvány használata titok helyett alkalmazásidentitás hitelesítéséhez](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/)
* Alkalmazások közötti SSO engedélyezése ADAL-kódtárral [Android](active-directory-sso-android.md) és [iOS](active-directory-sso-ios.md) rendszerű eszközökön
* [Alkalmazás AppSource Certified minősítése Azure AD-hez](active-directory-devhowto-appsource-certified.md)
* [Az alkalmazás szerepeltetése az Azure AD alkalmazáskatalógusában](active-directory-app-gallery-listing.md)
* [Webalkalmazások beküldése az Office 365 Értékesítői információ-központjába](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
* [Alkalmazás regisztrálása az Azure Active Directory szolgáltatásban az Azure Portal segítségével](../active-directory-app-registration.md)
* [Az Azure Active Directory alkalmazásjegyzékének megismerése](active-directory-application-manifest.md)
* [Az ügyfélalkalmazásban található bejelentkezési és alkalmazásbeszerzési gombok arculati irányelveinek megismerése](active-directory-branding-guidelines.md)
* [Előzetes verzió: Olyan alkalmazások fordítása, amelyekben a személyes és a munkahelyi vagy iskolai fiókokkal is bejelentkezhetnek a felhasználók](active-directory-appmodel-v2-overview.md)
* [Előzetes verzió: Olyan alkalmazások fordítása, amelyekben a végfelhasználók regisztrálhatnak és bejelentkezhetnek](../../active-directory-b2c/active-directory-b2c-overview.md)
* [Előzetes verzió: A jogkivonatok élettartamának konfigurálása az Azure AD-ban](../active-directory-configurable-token-lifetimes.md) a PowerShell használatával. Az Azure AD Graph API használatával történő konfigurációval kapcsolatos részletekért lásd: [Házirend műveletek](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) és [Házirend entitás](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity).

## <a name="reference"></a>Referencia
Ezek a cikkek a REST és hitelesítési kódtár API-k, protokollok, hibák, kódminták és végpontok alapvető referenciáit biztosítják  

### <a name="support"></a>Támogatás
* [Címkével ellátott kérdések:](http://stackoverflow.com/questions/tagged/azure-active-directory) Az [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) és [adal](http://stackoverflow.com/questions/tagged/adal) címkékkel ellátott Azure Active Directory-megoldások keresése a Stack Overflow oldalain.
* Az alkalmazásfejlesztéshez és az integrációhoz gyakran használt fogalmak meghatározásához tekintse meg az [Azure AD fejlesztői szószedetét](active-directory-dev-glossary.md).

### <a name="code"></a>Kód
* [Az Azure Active Directory nyílt forráskódú kódtárai:](http://github.com/AzureAD) A kódtárforrások megkeresésének legegyszerűbb módja a [kódtárlistánk](active-directory-authentication-libraries.md) használata.
* [Azure Active Directory-példák:](https://github.com/azure-samples?query=active-directory) A példák listájában történő böngészés legegyszerűbb módja a [kódpéldák tárgymutatójának](active-directory-code-samples.md) használata.
* [Active Directory Authentication Library (ADAL) for .NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) – A [legutóbbi főverzió](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) és az [előző főverzió](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory) esetében is elérhetők referenciadokumentációk.

### <a name="microsoft-graph-and-azure-ad-graph-api"></a>A Microsoft Graph és az Azure AD Graph API
> [!IMPORTANT]
> Az Azure AD Graph API helyett ajánljuk a [Microsoft Graph](https://graph.microsoft.io/) használatát az Azure Active Directory erőforrásainak eléréséhez. A fejlesztési energiáinkat mostantól a Microsoft Graph-ra koncentráljuk, az Azure AD Graph API-hoz nem tervezünk további fejlesztéseket. Nagyon korlátozott azon forgatókönyvek száma, amelyeknél az Azure AD Graph API használata még elegendő. További információért tekintse meg a [Microsoft Graph vagy Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) blogbejegyzést az Office fejlesztői központjában.
> 

* [Microsoft Graph](https://graph.microsoft.io/): Dokumentáció, referencia, minták és SDK-k a Microsoft Graph-hoz. 
* [Azure AD Graph API-referencia](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog): REST-referencia az Azure Active Directory Graph API-hoz. 
* [Azure AD Graph API-engedélyhatókörök](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): Olyan OAuth 2.0-engedélyhatókörök, amelyekkel az alkalmazások hozzáférése vezérelhető a bérlőkben lévő címtáradatokhoz.

### <a name="authentication-and-authorization-protocols"></a>Hitelesítési és engedélyezési protokollok
* [Aláírókulcs-váltás az Azure AD-ben:](active-directory-signing-key-rollover.md) További információ az Azure AD aláírókulcs-váltás gyakoriságáról és a leggyakoribb alkalmazás-forgatókönyvek kulcsának frissítéséről.
* [OAuth 2.0 protokoll: Engedélyezési kódmegadásának használata:](active-directory-protocols-oauth-code.md) Az OAuth 2.0 protokoll engedélyezési kódmegadásával a webalkalmazásokhoz és a webes API-khoz engedélyezheti a hozzáférést az Azure Active Directory-bérlőben.
* [OAuth 2.0 protokoll: Az implicit engedélyezés megismerése:](active-directory-dev-understanding-oauth2-implicit-grant.md) Tudjon meg többet az implicit engedélyezés megadásáról és arról, hogy ez a megfelelő-e az alkalmazásához.
* [OAuth 2.0 protokoll: Szolgáltatások közötti hívások ügyfél-hitelesítő adatok használatával:](active-directory-protocols-oauth-service-to-service.md) Az OAuth 2.0 ügyfél-hitelesítő adatok engedélyezi a webszolgáltatás (bizalmas ügyfél) számára a saját hitelesítő adatainak használatát egy másik webszolgáltatás hívásakor, a felhasználó megszemélyesítése helyett. Ebben a forgatókönyvben az ügyfél általában egy köztes rétegű webszolgáltatás, démonszolgáltatás vagy webhely.
* [OpenID Connect 1.0 protokoll: Bejelentkezés és hitelesítés:](active-directory-protocols-openid-connect-code.md) Az OpenID Connect 1.0 protokoll az OAuth 2.0 protokollt bővíti ki hitelesítési protokollként történő használat esetén. Az ügyfélalkalmazás fogadhat id_tokent a bejelentkezési folyamat kezeléséhez, vagy az engedélyezési kódfolyam bővítéséhez, hogy id_token és engedélyezési kód fogadása is lehetséges legyen.
* [A SAML 2.0 protokoll referenciája:](active-directory-saml-protocol-reference.md) A SAML 2.0 protokoll lehetővé teszi, hogy az alkalmazások egyszeri bejelentkezéses működést biztosítsanak a felhasználóiknak.
* [WS-Federation 1.2 protokoll:](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) Az Azure Active Directory támogatja a WS-Federation 1.2 protokoll a Web Services Federation Version 1.2 specifikációnak megfelelő használatát. További információk az összevonási metaadat-dokumentumokról: [Összevonási metaadatok](active-directory-federation-metadata.md).
* [Támogatott token- és jogcímtípusok:](active-directory-token-and-claims.md) Ennek az útmutatónak a segítségével megismerheti és kiértékelheti a SAML 2.0 és a JSON Web Tokens (JWT) tokenek jogcímeit.

## <a name="videos"></a>Videók
### <a name="build"></a>Felépítés
Ezek az áttekintő bemutatók az alkalmazásfejlesztést ismertetik Azure Active Directory kiemelt előadóinak közreműködésével, akik közvetlenül a mérnöki csapatban dolgoznak. A bemutatók alapvető témaköröket érintenek, beleértve az IDMaaS-t, a hitelesítést, az identitás-összevonást és az egyszeri bejelentkezést.

* [Microsoft Identity: Az unió állapota és a jövőbeli irány](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
* [Azure Active Directory: Identitáskezelés a modern alkalmazások szolgáltatásaként](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
* [Modern webalkalmazások fejlesztése az Azure Active Directoryval](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
* [Modern natív alkalmazások fejlesztése az Azure Active Directoryval](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure Friday
Az [Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) egy péntekenként ismétlődő videósorozat, amely rövid (10–15 perces), szakértőkkel készített interjúkat vonultat fel különböző Azure-témakörökkel kapcsolatban.  Az oldalon lévő Services (Szolgáltatások) szűrővel tekintheti meg az összes Azure Active Directory-videót.

* [Azure-identitás 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
* [Azure-identitás 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
* [Azure-identitás 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Közösségi tartalom
* [Az Active Directory csapatának blogja:](http://blogs.technet.com/b/ad/) Ismerje meg az Azure Active Directory világának legújabb fejlesztéseit.
* [Az Azure Active Directory Graph csapatának blogja:](https://blogs.msdn.microsoft.com/aadgraphteam) Ismerje meg a Graph API-val kapcsolatos Azure Active Directory-információkat.
* [Cloud Identity:](http://www.cloudidentity.net) Gondolatok az identitáskezelésről mint szolgáltatásról, az Azure Active Directory vezető projektmenedzserétől.  
* [Azure Active Directory a Twitteren:](https://twitter.com/azuread) Azure Active Directory-közlemények legfeljebb 140 karakterben.

## <a name="windows-server-on-premises-development"></a>Windows Server – helyszíni fejlesztés
A Windows Server és az Active Directory összevonási szolgáltatás (AD FS) fejlesztési célú használatával kapcsolatos útmutatóért olvassa el az alábbi témaköröket:

* [AD FS-forgatókönyvek fejlesztőknek](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): Áttekintést nyújt az AD FS összetevőiről és azok működéséről a támogatott hitelesítési/engedélyezési forgatókönyvek részleteinek ismertetésével együtt.
* [AD FS-bemutatók](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): Bemutató cikkek listája, amelyek lépésenkénti útmutatót kínálnak a kapcsolódó hitelesítési/engedélyezési folyamatok végrehajtásához.

