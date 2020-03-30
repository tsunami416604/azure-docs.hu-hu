---
title: Xamarin Android rendszer böngésző szempontok (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: A cikk ismerteti a rendszerböngészők Xamarin Android rendszeren és a Microsoft Authentication Library for .NET (MSAL.NET) használatával kapcsolatos szempontokat.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132613"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Xamarin Android rendszer böngésző szempontjai tabellán MSAL.NET

Ez a cikk azt ismerteti, hogy mit kell figyelembe venni, amikor a rendszerböngészőt a Xamarin Android rendszerböngészőjét használja a Microsoft Authentication Library for .NET (MSAL.NET) segítségével.

A MSAL.NET 2.4.0 Preview-tól kezdve MSAL.NET a Chrome-tól eltérő böngészőket is támogat. Már nincs szükség chrome kell telepíteni az Android készülék hitelesítéshez.

Javasoljuk, hogy egyéni lapokat támogató böngészőket használjon. Íme néhány példa a következő böngészőkre:

| Az egyéni lapokkal támogatott böngészők | Csomag neve |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia között | com.ecosia.android|
|Kiwi | com.kiwibrowser.böngésző|
|Bátor | com.brave.browser|

Az egyéni lapokat támogató böngészők azonosítása mellett a tesztelés azt is jelzi, hogy néhány olyan böngésző, amely nem támogatja az egyéni lapokat, szintén működik a hitelesítéshez. Ezek a böngészők közé opera, Opera Mini, InBrowser, és Maxthon. 

## <a name="tested-devices-and-browsers"></a>Tesztelt eszközök és böngészők
Az alábbi táblázat azokat az eszközöket és böngészőket sorolja fel, amelyekhitelesítési kompatibilitás szempontjából tesztelték őket.

| Eszköz | Böngésző     |  Eredmény  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome\* | Át|
| Huawei/One+ | Edge\* | Át|
| Huawei/One+ | Firefox\* | Át|
| Huawei/One+ | Bátor\* | Át|
| Egy+ | Ecosia között\* | Át|
| Egy+ | Kiwi\* | Át|
| Huawei/One+ | Opera | Át|
| Huawei | OperaMini | Át|
| Huawei/One+ | InBrowser | Át|
| Egy+ | Maxthon | Át|
| Huawei/One+ | DuckDuckGo | A felhasználó megszakította a hitelesítést|
| Huawei/One+ | UC böngésző | A felhasználó megszakította a hitelesítést|
| Egy+ | Delfin | A felhasználó megszakította a hitelesítést|
| Egy+ | CM böngésző | A felhasználó megszakította a hitelesítést|
| Huawei/One+ | Nincs telepítve | AndroidActivityNemFound kivétel|

\*Támogatja az egyéni lapokat

## <a name="known-issues"></a>Ismert problémák

Ha a felhasználónak nincs engedélyezve böngészője `AndroidActivityNotFound` az eszközön, MSAL.NET kivételt fog nak.  
  - **Kockázatcsökkentés**: Kérje meg a felhasználót, hogy engedélyezze a böngészőt az eszközén. Javasoljon olyan böngészőt, amely támogatja az egyéni lapokat.

Ha a hitelesítés sikertelen (például ha a hitelesítés elindul `AuthenticationCanceled MsalClientException`duckduckgo), MSAL.NET visszatér . 
  - **Gyökérprobléma:** Az egyéni lapokat támogató böngésző nem volt engedélyezve az eszközön. A hitelesítés olyan böngészővel indult, amely nem tudta befejezni a hitelesítést. 
  - **Kockázatcsökkentés**: Kérje meg a felhasználót, hogy engedélyezze a böngészőt az eszközén. Javasoljon olyan böngészőt, amely támogatja az egyéni lapokat.

## <a name="next-steps"></a>További lépések
További információ és kódpéldák: [Választás beágyazott webböngésző és rendszerböngésző között a Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) és Embedded versus system web felhasználói [felületén.](msal-net-web-browsers.md#embedded-vs-system-web-ui)  