---
title: Univerzális Windows Platform szempontok (Microsoft-hitelesítési tár .NET) |} Az Azure
description: Figyelembe kell venni bizonyos ismerje meg az univerzális Windows Platform használata a Microsoft-hitelesítési tár .NET (MSAL.NET) esetén.
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
ms.openlocfilehash: eb8076df5482c91942349e0a052794f3fe945a5f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076510"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Univerzális Windows Platform-specifikus megfontolások MSAL.NET
Xamarin IOS-eszközökön több szempontot figyelembe kell végrehajtania, amikor az MSAL.NET használatával.

## <a name="the-usecorporatenetwork-property"></a>A UseCorporateNetwork tulajdonság
A WinRT Platform `PublicClientApplication` rendelkezik a következő logikai tulajdonság ``UseCorporateNetwork``. Ez a tulajdonság lehetővé teszi, hogy a Win8.1 és UWP-alkalmazások számára, hogy integrált Windows-hitelesítés (és így az SSO a felhasználó bejelentkezett az operációs rendszer) Ha a felhasználó bejelentkezett egy olyan fiókkal, az összevont Azure AD-bérlővel. Ez a modul WAB (Webeshitelesítés-szervező). 

> [!IMPORTANT]
> Ez a tulajdonság beállítása igaz értékre azt feltételezi, hogy az alkalmazás fejlesztőjének engedélyezte-e az integrált Windows-hitelesítés (IWA), az alkalmazás. Ehhez:
> - Az a ``Package.appxmanifest`` az UWP-alkalmazás a a **képességek** fülre, engedélyezze a következő képességekkel:
>   - Vállalati hitelesítési
>   - Magánhálózatok (ügyfél és kiszolgáló)
>   - Megosztott felhasználói tanúsítvány

IWA nem alapértelmezés szerint engedélyezve van, mert a vállalati hitelesítési vagy megosztott felhasználói tanúsítványok képességeket igénylő alkalmazások szükséges magasabb szintű ellenőrzés a Windows Store, el kell fogadni, és nem minden fejlesztő Kezdésként érdemes lehet végrehajtani a magasabb ellenőrzési szintje. 

Az UWP platformon (WAB) az alapul szolgáló implementáció nem működik megfelelően nagyvállalati forgatókönyvekben, ahol a feltételes hozzáférés engedélyezve lett. A probléma tünete, hogy a felhasználó jelentkezzen be Windows hello próbál, és válassza ki egy tanúsítványt, de a PIN-kódot a tanúsítvány nem található, vagy a felhasználó úgy dönt, azt, de soha nem kér a PIN-kód javasolt. Egy megkerülő megoldás az, hogy használjon egy másik módszert (felhasználónév/jelszó + telefonos hitelesítés), de a felület nem helyes. 

## <a name="next-steps"></a>További lépések
További részletek találhatók a következő mintákat:

Sample | Platform | Leírás 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Egy univerzális Windows Platform-ügyfélalkalmazás msal.net, fér hozzá a Microsoft Graph egy felhasználó hitelesítéséhez az Azure AD v2.0-végpont használatával. <br>![Topológia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Egy egyszerű Xamarin Forms alkalmazás amely hogyan használható az MSAL MSA és az Azure AD-n keresztül az AAD v2.0-végpont hitelesítéséhez, és hozzáférhet a Microsoft Graph, az eredményül kapott jogkivonatokkal. <br>![Topológia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|