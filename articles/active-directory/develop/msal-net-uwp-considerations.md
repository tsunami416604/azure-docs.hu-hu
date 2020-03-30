---
title: UWP szempontok (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Az univerzális Windows-platform (UWP) és a Microsoft Authentication Library for .NET (MSAL.NET) használatával kapcsolatos szempontokról.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeec28569cf31af4542d6cd7aca1fb27d77b1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132527"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Az univerzális Windows-platform MSAL.NET használatának szempontjai
Az univerzális Windows-platformot (UWP) használó alkalmazások fejlesztőinek MSAL.NET figyelembe kell venniük a cikkben szereplő fogalmakat.

## <a name="the-usecorporatenetwork-property"></a>A UseCorporateNetwork tulajdonság
A Windows Futásidejű (WinRT) `PublicClientApplication` platformon rendelkezik a Logikai tulajdonsággal. `UseCorporateNetwork` Ez a tulajdonság lehetővé teszi, hogy a Windows 8.1-alkalmazások és az UWP-alkalmazások kihasználják az integrált Windows-hitelesítés (IWA) előnyeit, ha a felhasználó be van jelentkezve egy összevont Azure Active Directory (Azure AD) bérlővel rendelkező fiókba. Az operációs rendszerbe bejelentkezett felhasználók is használhatják az egyszeri bejelentkezést (SSO). A `UseCorporateNetwork` tulajdonság beállításakor MSAL.NET webhitelesítési brókert (WAB) használ.

> [!IMPORTANT]
> Ha `UseCorporateNetwork` a tulajdonságot igaz értékre állítja, az alkalmazás fejlesztője engedélyezte az IWA szolgáltatást az alkalmazásban. Az IWA engedélyezése:
> - Az UWP-alkalmazás `Package.appxmanifest` **Képességek** lapján engedélyezze a következő funkciókat:
>   - **Vállalati hitelesítés**
>   - **Privát hálózatok (ügyfél& kiszolgáló)**
>   - **Megosztott felhasználói tanúsítvány**

Az IWA alapértelmezés szerint nincs engedélyezve, mert a Microsoft Store magas szintű ellenőrzést igényel, mielőtt elfogadná a vállalati hitelesítési lehetőségeket vagy megosztott felhasználói tanúsítványokat kérő alkalmazásokat. Nem minden fejlesztő szeretné ezt a szintet az ellenőrzés.

Az UWP platformon az alapul szolgáló WAB-megvalósítás nem működik megfelelően olyan vállalati forgatókönyvekben, ahol a feltételes hozzáférés engedélyezve van. A felhasználók a probléma tüneteit látják, amikor a Windows Hello használatával próbálnak bejelentkezni. Ha a felhasználónak tanúsítványt kell választania:

- A PIN-kód tanúsítványa nem található.
- Miután a felhasználó kiválasztotta a tanúsítványt, a rendszer nem kéri a PIN-kódot.

Megpróbálhatja elkerülni ezt a problémát egy másik módszerrel, például a felhasználónév-jelszó és a telefon hitelesítésével, de a tapasztalat nem jó.

## <a name="troubleshooting"></a>Hibaelhárítás

Egyes ügyfelek a következő bejelentkezési hibát jelentették olyan vállalati környezetekben, ahol tudják, hogy rendelkeznek internetkapcsolattal, és hogy a kapcsolat nyilvános hálózattal működik.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

Ezt a problémát elkerülheti, ha gondoskodik arról, hogy a WAB (az alapul szolgáló Windows-összetevő) lehetővé tegye a magánhálózatot. Ezt a beállításkulcs beállításával teheti meg:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

További információ: [Web authentication broker - Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>További lépések
A következő minták további információkat nyújtanak.

Sample | Platform | Leírás 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | A MSAL.NET használó UWP-ügyfélalkalmazás. A Microsoft Graph egy olyan felhasználó számára érhető el, aki egy Azure AD 2.0-végpont használatával hitelesíti magát. <br>![Topológia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[active-directory-xamarin-natív-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Egy egyszerű Xamarin Forms alkalmazás, amely bemutatja, hogyan használhatja az MSAL-t a Microsoft személyes fiókok és az Azure AD hitelesítéséhez az Azure AD 2.0-végponton keresztül. Azt is bemutatja, hogyan érheti el a Microsoft Graph, és megmutatja az eredményül kapott jogkivonatot. <br>![Topológia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
