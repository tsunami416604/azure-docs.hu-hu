---
title: "Az Azure Active Directory-Kódminták |} Microsoft Docs"
description: "Azure Active Directory mintakódok forgatókönyv szerint vannak rendszerezve index."
services: active-directory
documentationcenter: dev-center-name
author: msmbaldwin
manager: mtillman
editor: 
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2017
ms.author: mbaldwin
ms.custom: aaddev
ms.openlocfilehash: ba8356a018605331dc4b9cc9ff401929cd8793bf
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-code-samples"></a>Az Azure Active Directory-Kódminták
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Microsoft Azure Active Directory (Azure AD) segítségével vegye fel a hitelesítési és engedélyezési a webalkalmazások és webes API-k. Ez a szakasz hivatkozásokat biztosít mintákat, amelyek bemutatják, hogyan történik, és az alkalmazásokban használható kódrészleteket. A kód a minta oldalon találhat részletes olvasás-me témakörök, amelyek a követelmények, a telepítés és a telepítés számára. És megismerheti a fontos részek megjegyzésként a kódot.

Az egyes minta alapvető forgatókönyv ismertetése: az Azure Active Directory hitelesítési forgatókönyvei.

Hozzájárul az általunk biztosított mintákat a Githubon: [Microsoft Azure Active Directory-példák és dokumentáció](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="web-browser-to-web-application"></a>Webalkalmazás webböngészőben
Ezeket a mintákat egy webes alkalmazás, amely arra utasítja a felhasználó böngészőben való bejelentkezéshez őket az Azure AD írásával megjelenítése.

| Nyelvi/Platform | Sample | Leírás |
| --- | --- | --- |
| C# / .NET |[Webalkalmazás-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) |OpenID Connect (ASP.Net OpenID Connect OWIN köztes) segítségével az Azure AD-bérlő felhasználóit hitelesíti. |
| C# / .NET |[Webalkalmazás-több-Bérlős-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) |Egy több-bérlős .NET MVC használó webalkalmazások OpenID Connect (ASP.Net OpenID Connect OWIN köztes) a felhasználók hitelesítéséhez több Azure AD-bérlő. |
| C# / .NET |[Webalkalmazás-WSFederation-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) |WS-Federation (ASP.Net WS-Federation OWIN köztes) használja a felhasználók hitelesítéséhez az Azure AD-bérlő. |

## <a name="single-page-application-spa"></a>Egylapos alkalmazások (SPA)
Ez a példa bemutatja, hogyan az Azure ad-vel védett egylapos alkalmazások írásával.  

| Nyelvi/Platform | Sample | Leírás |
| --- | --- | --- |
| JavaScript, C# / .NET |[SinglePageApp-DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) |JavaScript és az Azure AD az ADAL használatával egy ASP.NET web API háttér megvalósítva AngularJS alapú egylapos alkalmazások biztonságos. |

## <a name="native-application-to-web-api"></a>Natív alkalmazás webes API-hoz
A Kódminták szemléltetik, amelyet a webes API-t az Azure AD által biztosított hív natív ügyfél alkalmazásokat hozhatnak létre. Használata [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) és [OAuth 2.0, az Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Nyelvi/Platform | Sample | Leírás |
| --- | --- | --- |
| Javascript |[NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) |Az ADAL Apache Cordova beépülő modul segítségével létrehozása egy Apache Cordova-alkalmazást, amely meghívja a webes API-k és az Azure AD használ a hitelesítéshez. |
| C# / .NET |[NativeClient-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) |Egy .NET WPF-alkalmazás, amely meghívja a webes API-k, amelyek az Azure AD használatával lett biztonságossá. |
| C# / .NET |[NativeClient-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) |Egy Windows Áruházbeli alkalmazás, amely meghívja a webes API-k, amelyek az Azure ad-vel védett. |
| C# / .NET |[NativeClient-WebAPI – több-Bérlős-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) |A Windows Áruházbeli alkalmazással egy több-bérlős webes az Azure ad-vel védett API hívása. |
| C# / .NET |[WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) |Egy natív ügyfélalkalmazást, amely behívja a webes API-k, amely lekérdezi az eredeti felhasználó nevében végezze a jogkivonatot, és ezután használja a jogkivonatot egy másik webes API hívásához. |
| C# / .NET |[NativeClient-WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) |A Windows Áruházbeli alkalmazás Windows Phone 8.1, amely behívja webes API-k, amelyek az Azure AD által védett. |
| ObjC |[NativeClient-iOS](https://github.com/Azure-Samples/active-directory-ios) |IOS-alkalmazás, amely behívja webes API-k, amelyek az Azure AD hitelesítési igényel. |
| C# / .NET |[WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) |A webes API-k, a JWT jogkivonat feldolgozni OWIN köztes használata helyett logikát tartalmaz egy natív ügyfélalkalmazás. |
| C# / Xamarin |[NativeClient Xamarin-Android](https://github.com/Azure-Samples/active-directory-xamarin-android) |Xamarin kötést a a natív Azure AD Authentication Library (ADAL) Android könyvtár. |
| C# / Xamarin |[NativeClient-Xamarin-iOS](https://github.com/Azure-Samples/active-directory-xamarin-ios) |Xamarin kötés a a natív Azure AD Authentication Library (ADAL) iOS-hez. |
| C# / Xamarin |[NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) |A Xamarin-projekthez, amely öt platformok célozza, és meghívja a webes API-k, amelyek az Azure AD által védett. |
| C# / .NET |[NativeClient távfelügyeleti DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) |Egy natív alkalmazás, amely nem interaktív hitelesítést hajt végre, és meghívja a webes API-k, amelyek az Azure AD által védett. |

## <a name="web-application-to-web-api"></a>Webalkalmazás, webes API-hoz
Megjelenítése hogyan használja a következő kód mintákat [OAuth 2.0, az Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) a hívás web API-t az Azure AD által biztosított webes alkalmazások készíthetők.

| Nyelvi/Platform | Sample | Leírás |
| --- | --- | --- |
| C# / .NET |[Webalkalmazás-WebAPI-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) |A bejelentkezett felhasználó engedélyeivel webes API-hívás. |
| C# / .NET |[Webalkalmazás-WebAPI-OAuth2-AppIdentity-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) |Az alkalmazás engedélyekkel webes API-hívás. |
| C# / .NET |[Webalkalmazás-WebAPI-OAuth2-UserIdentity-Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) |Adja hozzá az engedélyezési [OAuth 2.0, az Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) egy meglévő webes alkalmazás, akkor meghívhatja a webes API-k. |
| JavaScript |[WebAPI-Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) |Állítsa be az Azure ad-val API védelmi integrált REST API-szolgáltatás. A Node.js-kiszolgáló egy webes API-t tartalmaz. |
| C# / .NET |[Webalkalmazás-WebAPI – több-Bérlős-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) |Egy több-bérlős MVC webes használó alkalmazások OpenID Connect (ASP.Net OpenID Connect OWIN köztes) a felhasználók hitelesítéséhez az Azure AD-bérlő. Az engedélyezési kódot használja a Graph API meghívására. |

## <a name="server-or-daemon-application-to-web-api"></a>Kiszolgáló vagy démon alkalmazás webes API-hoz
A Kódminták bemutatják, hogyan hozható létre egy webes API-k használatával erőforrások lekérése démon vagy kiszolgáló-alkalmazás [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) és [OAuth 2.0, az Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Nyelvi/Platform | Sample | Leírás |
| --- | --- | --- |
| C# / .NET |[Démon-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) |A Konzolalkalmazás meghívja a webes API-k. Az ügyfél hitelesítő pedig a jelszót. |
| C# / .NET |[Démon-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) |Egy konzolalkalmazást, amely meghívja a webes API-k. Meg kell egy tanúsítványt az ügyfél hitelesítő adatait. |

## <a name="calling-azure-ad-graph-api"></a>Az Azure AD Graph API felület meghívásakor
Ezek kódminta alkalmazások összeállítását, amelyek az Azure AD Graph API írási és olvasási címtáradatok szemléltetik.

| Nyelvi/Platform | Sample | Leírás |
| --- | --- | --- |
| Java |[Webalkalmazás-GraphAPI-Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) |Egy webalkalmazás, amelyről a Graph API-t használja az Azure Active directory-adatokat. |
| PHP |[A PHP GraphAPI webalkalmazás](https://github.com/Azure-Samples/active-directory-php-graphapi-web) |Egy webalkalmazás, amelyről a Graph API-t használja az Azure Active directory-adatokat. |
| C# / .NET |[Webalkalmazás-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) |Egy webalkalmazás, amelyről a Graph API-t használja az Azure Active directory-adatokat. |
| C# / .NET |[ConsoleApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-console) |A Konzolalkalmazás azt mutatja be, a Graph API közös olvasási és írási hívások, és bemutatja, hogyan hajtható végre felhasználó licenc-hozzárendelést, és frissítse egy felhasználó miniatűr fényképre és hivatkozásokat. |
| C# / .NET |[ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) |Egy konzolalkalmazást, amely a Graph API-t a különbözeti lekérdezés segítségével rendszeres a felhasználói objektumok változásait az beszerzése az Azure AD-bérlő. |
| C# / .NET |[Webalkalmazás-GraphAPI-DirectoryExtensions-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) |Egy MVC alkalmazás használják a Graph API-lekérdezések egy egyszerű vállalati szervezeti diagram létrehozásához. |
| PHP |[Webalkalmazás-GraphAPI-DirectoryExtensions-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) |A PHP-alkalmazások, amelyek a Graph API-regisztrálhat egy kiterjesztést és olvassa el, frissítése és törlése a mellék attribútum értékeket. |

## <a name="authorization"></a>Engedélyezés
A Kódminták használatát mutatják be az Azure AD a hitelesítéshez.

| Nyelvi/Platform | Sample | Leírás |
| --- | --- | --- |
| C# / .NET |[Webalkalmazás-GroupClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) |Hajtsa végre a szerepköralapú hozzáférés-vezérlés (RBAC) Azure Active Directory csoport jogcímek használata az Azure ad-vel integrált alkalmazásban. |
| C# / .NET |[Webalkalmazás-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) |Hajtsa végre a szerepkör alapú hozzáférés-vezérlést (RBAC) integrálva van az Azure AD alkalmazás Azure Active Directory-alkalmazás szerepköröket használ. |

## <a name="legacy-walkthroughs"></a>A hagyományos forgatókönyvek
Ezek a forgatókönyvek némileg régebbi technológiát használ, de továbbra is érdemes lehet.

| Nyelvi/Platform | Sample | Leírás |
| --- | --- | --- |
| C# / .NET |[A Microsoft Azure AD-alkalmazást a szerepkör- és hozzáférés-vezérlési lista-alapú engedélyezési](http://go.microsoft.com/fwlink/?LinkId=331694) |Hajtsa végre az Azure ad-vel integrált alkalmazás szerepköralapú hitelesítés (RBAC) és az ACL-alapú engedélyezési. |
| C# / .NET |[AAL - REST-szolgáltatást a Windows Áruházbeli alkalmazással - hitelesítés](http://go.microsoft.com/fwlink/?LinkId=330605) |Használjon [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) (korábbi nevén AAL) a Windows áruház Beta felhasználói hitelesítési képességeket adhat a Windows áruház egy alkalmazásához. |
| C# / .NET |[ADAL - natív alkalmazásnak, hogy a többi szolgáltatás - hitelesítési keresztül a tallózási párbeszédpanelhez AAD-ben](http://go.microsoft.com/fwlink/?LinkId=259814) |Használjon [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) felhasználói hitelesítés-képességeket adhat a WPF-ügyfeleknek. |
| C# / .NET |[A tallózási párbeszédpanelhez keresztül ACS ADAL - natív alkalmazásnak, hogy a többi szolgáltatás - hitelesítés](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) |Használjon [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) és [Access Control Service 2.0 (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) felhasználói hitelesítés-képességeket adhat a WPF-ügyfeleknek. |
| C# / .NET |[ADAL - kiszolgáló hitelesítési](http://go.microsoft.com/fwlink/?LinkId=259816) |Használjon [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) szolgáltatás hívások egy MVC4 webes API REST-szolgáltatást a kiszolgáló oldalán folyamatának védelméhez. |
| C# / .NET |[A webalkalmazás az Azure AD bejelentkezés felvétele](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) |Beállíthat egy .NET-alkalmazást, webes egyszeri bejelentkezés az Azure AD vállalati címtár végrehajtásához. |
| C# / .NET |[Több-Bérlős webalkalmazások Azure AD-val létrehozása](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) |Az Azure AD használatával adhatja hozzá egyszeri bejelentkezést és a directory elérés egy .NET-alkalmazás több szervezet működéséhez. |
| JAVA |[Az Azure AD Graph API Java mintaalkalmazás](http://go.microsoft.com/fwlink/?LinkId=263969) |A Graph API segítségével hozzáférést directory adatokat az Azure AD. |
| PHP |[Az Azure AD Graph API PHP mintaalkalmazás](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) |A Graph API segítségével hozzáférést directory adatokat az Azure AD. |
| C# / .NET |[Az Azure AD Graph API mintaalkalmazás](http://go.microsoft.com/fwlink/?LinkID=262648) |A Graph API segítségével hozzáférést directory adatokat az Azure AD. |
| C# / .NET |[Az Azure AD Graph különbözeti lekérdezés mintaalkalmazás](http://go.microsoft.com/fwlink/?LinkId=275398) |A Graph API-t a különbözeti lekérdezés segítségével rendszeres a felhasználói objektumok változásait az beszerzése az Azure AD-bérlő. |
| C# / .NET |[Több-Bérlős felhőalapú alkalmazások az Azure Active Directory integrálásának mintaalkalmazás](http://go.microsoft.com/fwlink/?LinkId=275397) |A több-bérlős alkalmazások integrálása az Azure AD-be. |
| C# / .NET |[A Windows áruház-alkalmazás és az Azure AD REST webszolgáltatás biztonságossá tétele](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) |Hozzon létre egy egyszerű webes API-erőforrás és a Windows áruház ügyfélalkalmazás az Azure AD és a [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md). |
| C# / .NET |[A Graph API segítségével az Azure AD lekérdezése](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) |A Microsoft .NET-alkalmazásokat az Azure AD Graph API segítségével érheti el adatait az Azure AD-bérlő címtár konfigurálása. |

## <a name="see-also"></a>Lásd még:
##### <a name="other-resources"></a>Egyéb források
[Az Azure Active Directory fejlesztői útmutatója](active-directory-developers-guide.md)

[Az Azure AD Graph API fogalmi és referencia](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Az Azure AD Graph API Segédkódtárba helyezni](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
