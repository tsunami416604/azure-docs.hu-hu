---
title: Xamarin IOS-es szempontok (Microsoft-hitelesítési tár .NET) |} Az Azure
description: Figyelembe kell venni bizonyos ismerje meg a Xamarin IOS-es használata a Microsoft-hitelesítési tár .NET (MSAL.NET) esetén.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7621b18f32a90b81dc075c534367981cf44f16e0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076330"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Xamarin iOS-specifikus MSAL.NET megfontolások
Xamarin IOS-eszközökön több szempontot figyelembe kell végrehajtania, amikor az MSAL.NET használatával

- [IOS 12- és hitelesítési szolgáltatással kapcsolatos ismert problémák](#known-issues-with-ios-12-and-authentication)
- [Bírálja felül, és implementálása a `OpenUrl` működni a `AppDelegate`](#implement-openurl)
- [A kulcslánccsoportok engedélyezése](#enable-keychain-groups)
- [Engedélyezze a jogkivonatok gyorsítótárának megosztása](#enable-token-cache-sharing-across-ios-applications)
- [Kulcslánc-hozzáférési engedélyezése](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>IOS 12- és hitelesítési szolgáltatással kapcsolatos ismert problémák
A Microsoft közzétette egy [biztonsági tanácsadó](https://github.com/aspnet/AspNetCore/issues/4647) iOS12 és bizonyos típusú hitelesítés között inkompatibilitás kapcsolatos adatok megadása. Az inkompatibilitás oldaltörések szociális, WSFed és OIDC bejelentkezések. A tanácsadó is útmutatást nyújt a fejlesztők mire képes lesz iOS12 kompatibilis alkalmazásokhoz az ASP.NET által hozzáadott aktuális biztonsági korlátozások eltávolítása.  

Xamarin iOS-en MSAL.NET alkalmazások fejlesztésekor végtelen ciklust láthatja, jelentkezzen be a webhelyek IOS 12 tett kísérlet során (ehhez hasonló [ADAL probléma](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Az ASP.NET Core OIDC hitelesítési IOS 12 szünet is megjelenhet ez leírtak szerint a Safari [WebKit probléma](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>OpenUrl megvalósítása

Először felül kell bírálnia az `OpenUrl` módszere a `FormsApplicationDelegate` származtatott osztály és hívás `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Emellett egy URL-séma határozza meg, az alkalmazás meghív egy másik alkalmazást, rendelkezik egy adott képernyő az átirányítási URL-cím és az átirányítási URL-címet a regisztrálásához engedély szükséges a [az Azure portal](https://portal.azure.com).

## <a name="enable-keychain-groups"></a>A Kulcslánccsoportok engedélyezése

Annak érdekében, hogy a jogkivonat munkahelyi gyorsítótárazza, és rendelkezik a `AcquireTokenSilentAsync` metódus munka, több lépést kell követnie:
1. A kulcslánc-hozzáférési engedélyezése a *`* Entitlements.plist* fájlt, és adja meg a **Kulcslánccsoportok** található a csomagazonosító.
2. Válassza ki *`*Entitlements.plist*`* fájlt a **egyéni jogosultságok** mezőt a beállítások ablakban iOS projekt **csomag aláírási nézet**.
3. Amikor aláíró tanúsítványt, ellenőrizze, hogy XCode használja ugyanazt az Apple ID azonosítójával.

## <a name="enable-token-cache-sharing-across-ios-applications"></a>Token gyorsítótár iOS-alkalmazások közötti megosztásának engedélyezése

Az MSAL kezdődően 2.x, megadhat egy biztonsági kulcslánccsoportot a token gyorsítótár megőrzése több alkalmazás használata. Ez lehetővé teszi, hogy ugyanazon biztonsági kulcslánccsoportot beleértve a-val fejlesztett kellene több alkalmazás között a jogkivonatok gyorsítótárának megosztása [ADAL.NET](https://aka.ms/adal-net), MSAL.NET Xamarin.iOS-alkalmazást, és fejlett natív iOS-alkalmazások a [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) vagy [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

A jogkivonatok gyorsítótárának megosztása lehetővé teszi, hogy egyszeri bejelentkezés (SSO) az összes biztonsági azonos kulcslánccsoportot használó alkalmazások között.

Egyszeri bejelentkezés engedélyezéséhez kell beállítani a `PublicClientApplication.iOSKeychainSecurityGroup` ugyanazt az értéket az összes alkalmazás tulajdonságát.

Például az MSAL használatával v3.x lenne:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Például az MSAL használatával v2.7.x lenne:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

> [!NOTE]
> A `KeychainSecurityGroup` tulajdonság elavult. Korábban a MSAL 2.x használatakor előtagot meg teamid értéknek kellett a fejlesztők a `KeychainSecurityGroup` tulajdonság. 
> 
> Most MSAL kezdődően 2.7.x, MSAL feloldja teamid értéknek előtagot futásidőben használatakor a `iOSKeychainSecurityGroup` tulajdonság. Ez a tulajdonság használata esetén az értékben nem szerepelhetnek a teamid értéknek előtagot. 
> 
> Az új `iOSKeychainSecurityGroup` tulajdonság, amely nem igényel a fejlesztők számára, hogy adja meg a teamid értéknek. A `KeychainSecurityGroup` tulajdonság már elavult. 

## <a name="enable-keychain-access"></a>Kulcslánc-hozzáférési engedélyezése

Az MSAL 2.x és ADAL 4.x a teamid értéknek használható a kulcsláncba, amely lehetővé teszi az egyszeri bejelentkezés (SSO) az ugyanattól alkalmazások között adja meg a hitelesítési tárak elérésére. 

Mi az a [TeamIdentifierPrefix](/xamarin/ios/deploy-test/provisioning/entitlements?tabs=vsmac) (csoportazonosító)? Az App Store egyedi azonosítóját (vállalati vagy személyes). Az alkalmazásazonosító egy egyedülálló megoldás, az alkalmazáshoz. Ha egynél több alkalmazás, az összes alkalmazásra vonatkozó TeamId ugyanaz lesz, de az AppId eltérő lesz. A kulcslánc-hozzáférési csoportot a következő előtaggal teamid értéknek által automatikusan az egyes csoportokhoz a rendszer. Hogyan kényszeríti az operációs rendszer, hogy a az ugyanazon közzétevőtől származó alkalmazások hozzáférhessenek-e a megosztott kulcslánc. 

Inicializálásakor a `PublicClientApplication`, ha egy `MsalClientException` üzenettel: `TeamId returned null from the iOS keychain...`, tegye a következőket a Xamarin iOS-alkalmazás az kell:

1. A VS-ben hibakeresési lapján, Ugrás nameOfMyApp.iOS tulajdonságai...
2. Folytassa a csomag aláírása iOS 
3. Egyéni jogosultságokat, kattintson a... Jelölje ki a Entitlements.plist fájlt az alkalmazásból
4. Az iOS-alkalmazás csproj-fájljában most már tartalmazza ezt a sort kell rendelkeznie: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. **Építse újra** a projektet.

Ez a *emellett* , amely lehetővé teszi a kulcslánc-hozzáférési a `Entitlements.plist` fájlt, vagy az alábbi hozzáférési csoportot vagy a saját:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
</plist>
```

## <a name="next-steps"></a>További lépések

További részletek szerepelnek a [iOS figyelembe kell venni bizonyos](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) bekezdés readme.md fájl a következő mintát:

Sample | Platform | Leírás 
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Egy egyszerű Xamarin Forms alkalmazás amely hogyan használható az MSAL MSA és az Azure AD-n keresztül az AAD V2.0-végpont hitelesítéséhez, és hozzáférhet a Microsoft Graph, az eredményül kapott jogkivonatokkal. <br>![Topológia](media/msal-net-xamarin-ios-considerations/topology.png)