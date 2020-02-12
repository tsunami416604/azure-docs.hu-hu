---
title: Xamarin Android rendszerbeli böngészővel kapcsolatos megfontolások (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Xamarin-alapú rendszerböngészők használatának szempontjait a .NET-hez készült Microsoft Authentication Library (MSAL.NET) szolgáltatással.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad26a4d619a7984f08a8decc87f9339adae47cdd
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132613"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>A Xamarin Android rendszerbeli böngészővel kapcsolatos szempontok a MSAL.NET használatához

Ez a cikk azt ismerteti, hogy milyen szempontokat kell figyelembe vennie, amikor az Android rendszerhez készült Microsoft Authentication Library (MSAL.NET) Xamarin használja a rendszerböngészőt.

A MSAL.NET 2.4.0 előzetes verziójától kezdődően a MSAL.NET a Chrome-tól eltérő böngészőket is támogat. A továbbiakban nincs szükség a Chrome telepítésére az Android-eszközön a hitelesítéshez.

Javasoljuk, hogy használjon egyéni lapokat támogató böngészőket. Íme néhány példa a böngészőkre:

| Egyéni lapokat támogató böngészők | Csomag neve |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com. kiwibrowser. Browser|
|Bátor | com. bátor. böngésző|

Az egyéni lapokat támogató böngészők azonosításán kívül a tesztelés azt jelzi, hogy néhány böngésző, amely nem támogatja az egyéni lapokat, a hitelesítéshez is működik. Ezen böngészők közé tartozik az Opera, az Opera Mini, a inBrowser és a Maxthon. 

## <a name="tested-devices-and-browsers"></a>Tesztelt eszközök és böngészők
A következő táblázat felsorolja a hitelesítési kompatibilitáshoz tesztelt eszközöket és böngészőket.

| Eszköz | Böngésző     |  Eredmény  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome\* | Pass|
| Huawei/One+ | Edge\* | Pass|
| Huawei/One+ | Firefox\* | Pass|
| Huawei/One+ | Bátor\* | Pass|
| Egy + | Ecosia\* | Pass|
| Egy + | Kiwi\* | Pass|
| Huawei/One+ | Opera | Pass|
| Huawei | OperaMini | Pass|
| Huawei/One+ | Inböngésző | Pass|
| Egy + | Maxthon | Pass|
| Huawei/One+ | DuckDuckGo | Felhasználó által megszakított hitelesítés|
| Huawei/One+ | UC böngésző | Felhasználó által megszakított hitelesítés|
| Egy + | Delfin | Felhasználó által megszakított hitelesítés|
| Egy + | CM böngésző | Felhasználó által megszakított hitelesítés|
| Huawei/One+ | nincs telepítve | AndroidActivityNotFound kivétel|

\* támogatja az egyéni lapokat

## <a name="known-issues"></a>Ismert problémák

Ha a felhasználónak nincs böngészője engedélyezve az eszközön, a MSAL.NET `AndroidActivityNotFound` kivételt fog kiváltani.  
  - **Enyhítés**: kérje meg a felhasználót, hogy engedélyezzen egy böngészőt az eszközön. Olyan böngészőt javasoljon, amely támogatja az egyéni lapokat.

Ha a hitelesítés sikertelen (például ha a hitelesítés a DuckDuckGo-val kezdődik), akkor a MSAL.NET `AuthenticationCanceled MsalClientException`fog visszaadni. 
  - **Gyökérszintű probléma**: az eszközön nincs engedélyezve az egyéni lapokat támogató böngésző. A hitelesítés olyan böngészővel lett elindítva, amely nem tudta befejezni a hitelesítést. 
  - **Enyhítés**: kérje meg a felhasználót, hogy engedélyezzen egy böngészőt az eszközön. Olyan böngészőt javasoljon, amely támogatja az egyéni lapokat.

## <a name="next-steps"></a>Következő lépések
További információkért és Példákért lásd: [kiválasztás egy beágyazott webböngésző és egy rendszerböngésző között a Xamarin Android és a](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) Embedded és a [rendszer webes felhasználói felületén](msal-net-web-browsers.md#embedded-vs-system-web-ui).  