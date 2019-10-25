---
title: Xamarin Android-megfontolások (Microsoft Authentication Library for .NET)
titleSuffix: Microsoft identity platform
description: Ismerje meg a Xamarin Android és a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatára vonatkozó szempontokat.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5caad4b136c9ef2686cc4befc70e6720e27855
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802747"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Xamarin Android-specifikus megfontolások a MSAL.NET
Ez a cikk a .NET-hez készült Microsoft Authentication Library (MSAL.NET) Xamarin Android rendszerhez készült rendszerböngészővel kapcsolatos szempontokat ismerteti.

A MSAL.NET 2.4.0 – előzetes verziótól kezdődően a MSAL.NET a Chrome-tól eltérő böngészőket is támogat, és a továbbiakban nincs szükség a Chrome telepítésére az Android-eszközön a hitelesítéshez.

Javasoljuk, hogy használjon olyan böngészőket, amelyek támogatják az egyéni lapokat, például a következőket:

| Egyéni lapokat támogató böngészők | Csomag neve |
|------| ------- |
|Chrome | com. Android. Chrome|
|Microsoft Edge | com. microsoft. emmx|
|Firefox | org. mozilla. Firefox|
|Ecosia | com. ecosia. Android|
|Kiwi | com. kiwibrowser. Browser|
|Bátor | com. bátor. böngésző|

A tesztelésen alapuló egyéni lapokat támogató böngészők mellett néhány böngésző, amely nem támogatja az egyéni lapokat, a hitelesítéshez is használható: Opera, Opera Mini, inBrowser és Maxthon. További információért olvassa el [a táblázat a tesztek eredményeihez](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested)című témakört.

## <a name="known-issues"></a>Ismert problémák

- Ha a felhasználónak nincs böngészője engedélyezve az eszközön, a MSAL.NET `AndroidActivityNotFound` kivételt fog kiváltani. 
  - Megoldás: tájékoztassa a felhasználót, hogy engedélyezzen egy böngészőt (lehetőleg egy egyéni fülek támogatásával) az eszközön.

- Ha a hitelesítés sikertelen (pl. a hitelesítés a DuckDuckGo-mel kezdődik, a MSAL.NET egy `AuthenticationCanceled MsalClientException`ad vissza. 
  - **Gyökérszintű probléma**: egy egyéni lapokat támogató böngésző nem volt engedélyezve az eszközön. A hitelesítés egy másik böngészővel lett elindítva, amely nem tudta befejezni a hitelesítést. 
  - Megoldás: tájékoztassa a felhasználót, hogy telepítsen egy böngészőt (lehetőleg egy egyéni lap-támogatással) az eszközön.

## <a name="devices-and-browsers-tested"></a>Tesztelt eszközök és böngészők
A következő táblázat a tesztelt eszközöket és böngészőket sorolja fel.

| | Böngésző&ast;     |  Eredmény  | 
| ------------- |:-------------:|:-----:|
| Huawei/egy + | Chrome&ast; | Pass|
| Huawei/egy + | Edge&ast; | Pass|
| Huawei/egy + | Firefox&ast; | Pass|
| Huawei/egy + | Bátor&ast; | Pass|
| Egy + | Ecosia&ast; | Pass|
| Egy + | Kiwi&ast; | Pass|
| Huawei/egy + | Operát | Pass|
| Huawei | OperaMini | Pass|
| Huawei/egy + | Inböngésző | Pass|
| Egy + | Maxthon | Pass|
| Huawei/egy + | DuckDuckGo | Felhasználó által megszakított hitelesítés|
| Huawei/egy + | UC böngésző | Felhasználó által megszakított hitelesítés|
| Egy + | Delfin | Felhasználó által megszakított hitelesítés|
| Egy + | CM böngésző | Felhasználó által megszakított hitelesítés|
| Huawei/egy + | nincs telepítve | AndroidActivityNotFound ex|

&ast; támogatja az egyéni lapokat

## <a name="next-steps"></a>Következő lépések
A kódrészletek és a Xamarin Android rendszerű rendszerböngésző használatával kapcsolatos további információkért olvassa el ezt az [útmutatót](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  