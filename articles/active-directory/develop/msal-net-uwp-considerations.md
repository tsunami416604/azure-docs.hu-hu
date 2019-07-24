---
title: Univerzális Windows-platform megfontolások (Microsoft Authentication Library for .NET) | Azure
description: Ismerkedjen meg az Univerzális Windows-platform és a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatára vonatkozó szempontokkal.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d78a64ee41e37fe53eba20eab6753c0b6eb8389
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277917"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Univerzális Windows-platform-specifikus megfontolások a MSAL.NET
A UWP-on több szempontot is figyelembe kell vennie a MSAL.NET használatakor.

## <a name="the-usecorporatenetwork-property"></a>A UseCorporateNetwork tulajdonság
A WinRT platformon `PublicClientApplication` a következő logikai tulajdonság ``UseCorporateNetwork``szerepel. Ez a tulajdonság lehetővé teszi a Win 8.1-és UWP-alkalmazások számára az integrált Windows-hitelesítés előnyeit (és így az operációs rendszerbe bejelentkezett felhasználóval való egyszeri bejelentkezést), ha a felhasználó egy összevont Azure AD-bérlőben lévő fiókkal van bejelentkezve. A tulajdonság beállításakor a MSAL.NET a WAB (web Authentication Broker) szolgáltatást használja.

> [!IMPORTANT]
> Ha a tulajdonságot igaz értékre állítja, feltételezi, hogy az alkalmazás fejlesztője engedélyezte az integrált Windows-hitelesítést (IWA) az alkalmazásban. Ehhez:
> - A UWP alkalmazásban a képességek lapon engedélyezze a következő képességeket:  ``Package.appxmanifest``
>   - Vállalati hitelesítés
>   - Magánhálózatok (ügyfél & kiszolgáló)
>   - Megosztott felhasználói tanúsítvány

A IWA alapértelmezés szerint nincs engedélyezve, mert a vállalati hitelesítést vagy a megosztott felhasználói tanúsítványokat kérő alkalmazások esetében magasabb szintű ellenőrzésre van szükség a Windows áruházban, és nem minden fejlesztőnek érdemes elvégeznie a nagyobb ellenőrzési szint.

A UWP platform (WAB) mögöttes implementációja nem működik megfelelően olyan nagyvállalati forgatókönyvekben, ahol a feltételes hozzáférés engedélyezve volt. A hibajelenség azt eredményezi, hogy a felhasználó megpróbál bejelentkezni a Windows Hello szolgáltatásba, és azt javasoljuk, hogy válasszon ki egy tanúsítványt, de:

- a PIN-kód tanúsítványa nem található,
- vagy a felhasználó úgy dönt, hogy nem kéri a PIN-kód megadását.

A megkerülő megoldás egy alternatív módszer használata (felhasználónév/jelszó + telefonos hitelesítés), de a felület nem jó.

## <a name="troubleshooting"></a>Hibaelhárítás

Néhány ügyfél jelezte, hogy bizonyos vállalati környezetekben a következő bejelentkezési hiba történt:

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

mivel tudják, hogy rendelkeznek internetkapcsolattal, és amelyek nyilvános hálózattal működnek.

A megkerülő megoldással gondoskodhat arról, hogy a WAB (a mögöttes Windows-összetevő) engedélyezze a magánhálózati hálózatot. Ezt a beállításkulcs beállításával teheti meg:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Részletekért lásd: [web Authentication Broker-Hegedűs](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>További lépések
További részleteket az alábbi példákban talál:

Sample | Platform | Leírás 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Egy Univerzális Windows-platform ügyfélalkalmazás a msal.net használatával, amely az Azure AD v 2.0-végponttal hitelesítő felhasználó Microsoft Graphhoz fér hozzá. <br>![Topológia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Egy egyszerű Xamarin űrlapos alkalmazás, amely bemutatja, hogyan használhatja az MSAL-t a MSA és az Azure AD hitelesítésére a HRE 2.0-s végponton keresztül, és az eredményül kapott jogkivonattal fér hozzá a Microsoft Graphhoz. <br>![Topológia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
