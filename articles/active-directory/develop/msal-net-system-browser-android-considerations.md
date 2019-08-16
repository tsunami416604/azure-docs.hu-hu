---
title: Xamarin Android-megfontolások (Microsoft Authentication Library for .NET) | Azure
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
ms.openlocfilehash: 1b0c810097913e896027245b15600ed75aabcd25
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532573"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Xamarin Android-specifikus megfontolások a MSAL.NET
Ez a cikk a .NET-hez készült Microsoft Authentication Library (MSAL.NET) Xamarin Android rendszerhez készült rendszerböngészővel kapcsolatos szempontokat ismerteti.

A MSAL.NET 2.4.0 – előzetes verziótól kezdődően a MSAL.NET a Chrome-tól eltérő böngészőket is támogat, és a továbbiakban nincs szükség a Chrome telepítésére az Android-eszközön a hitelesítéshez.

Javasoljuk, hogy használjon olyan böngészőket, amelyek támogatják az egyéni lapokat, például a következőket:

| Egyéni lapokat támogató böngészők | Csomag neve |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com. kiwibrowser. Browser|
|Bátor | com. bátor. böngésző|

Az egyéni lapokat támogató böngészőkön kívül a tesztelésen alapuló, néhány böngésző, amely nem támogatja az egyéni lapokat, a hitelesítéshez is működni fog: Opera, Opera Mini, inBrowser és Maxthon. További információért olvassa el [a táblázat a tesztek eredményeihez](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested)című témakört.

## <a name="known-issues"></a>Ismert problémák

- Ha a felhasználónak nincs böngészője engedélyezve az eszközön, a MSAL.net `AndroidActivityNotFound` kivételt fog kiváltani. 
  - **Enyhítés**: Tájékoztassa a felhasználót, hogy engedélyezzen egy böngészőt (lehetőleg egy egyéni lapok támogatásával) az eszközön.

- Ha a hitelesítés sikertelen (pl. a hitelesítés a DuckDuckGo-sel együtt elindul, a `AuthenticationCanceled MsalClientException`MSAL.net egy értéket ad vissza. 
  - **Gyökérszintű probléma**: Egy egyéni lapokat támogató böngésző nem volt engedélyezve az eszközön. A hitelesítés egy másik böngészővel lett elindítva, amely nem tudta befejezni a hitelesítést. 
  - **Enyhítés**: Tájékoztassa a felhasználót, hogy telepítsen egy böngészőt (lehetőleg egy egyéni lapon támogatással) az eszközön.

## <a name="devices-and-browsers-tested"></a>Tesztelt eszközök és böngészők
A következő táblázat a tesztelt eszközöket és böngészőket sorolja fel.

| | Böngésző&ast;     |  Eredmény  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome&ast; | Sikeres|
| Huawei/One+ | Edge&ast; | Sikeres|
| Huawei/One+ | Firefox&ast; | Sikeres|
| Huawei/One+ | Bátor&ast; | Sikeres|
| Egy + | Ecosia&ast; | Sikeres|
| Egy + | Kiwi&ast; | Sikeres|
| Huawei/One+ | Opera | Sikeres|
| Huawei | OperaMini | Sikeres|
| Huawei/One+ | Inböngésző | Sikeres|
| Egy + | Maxthon | Sikeres|
| Huawei/One+ | DuckDuckGo | Felhasználó által megszakított hitelesítés|
| Huawei/One+ | UC böngésző | Felhasználó által megszakított hitelesítés|
| Egy + | Delfin | Felhasználó által megszakított hitelesítés|
| Egy + | CM böngésző | Felhasználó által megszakított hitelesítés|
| Huawei/One+ | nincs telepítve | AndroidActivityNotFound ex|

&ast;Egyéni lapok támogatása

## <a name="next-steps"></a>További lépések
A kódrészletek és a Xamarin Android rendszerű rendszerböngésző használatával kapcsolatos további információkért olvassa el ezt az [útmutatót](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  