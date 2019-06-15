---
title: Xamarin Android szempontok (Microsoft-hitelesítési tár .NET) |} Az Azure
description: Figyelembe kell venni bizonyos ismerje meg a Xamarin Android használata a Microsoft-hitelesítési tár .NET (MSAL.NET) esetén.
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
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57feb33967732481d78e0ddaba5e90f4f82f327
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544425"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Xamarin Android-specifikus MSAL.NET megfontolások
Ez a cikk ismerteti a figyelembe kell venni bizonyos, amikor a rendszer böngésző használatával Xamarin Android-a Microsoft-hitelesítési tár .NET (MSAL.NET).

MSAL.NET 2.4.0-preview kezdve MSAL.NET Chrome nem támogatja, és többé nem kell a Chrome-hitelesítéshez az Android-eszközön telepíteni.

Azt javasoljuk, használjon egyéni lapok, például a következő támogatja:

| Egyéni lapok támogató böngészők | Csomag neve |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kivitermesztő | com.kiwibrowser.browser|
|Brave | com.Brave.Browser|

Egyéni lapok támogatással, a tesztelés alapján böngészők mellett néhány egyéni lapok nem támogató böngészők is működnek a hitelesítéshez: Opera, Opera Mini, InBrowser és Maxthon. További információkért olvassa el [tábla a vizsgálati eredmények](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Ismert problémák

- Ha a felhasználónak nincs böngészőt az eszközön engedélyezve van, az MSAL.NET váltja egy `AndroidActivityNotFound` kivétel. 
  - **Kockázatcsökkentési**: Tájékoztatja a felhasználót, hogy azok engedélyezze (lehetőleg egy egyéni lapok támogatással) egy böngészőt az eszközön.

- Ha a hitelesítés sikertelen (például) hitelesítés indul el DuckDuckGo), MSAL.NET adja vissza egy `AuthenticationCanceled MsalClientException`. 
  - **A probléma kiváltó**: Egyéni lapok támogató böngészőt az eszközön nincs engedélyezve. Hitelesítési elindítása egy másik böngészővel, és nem tudja elvégezni a hitelesítést. 
  - **Kockázatcsökkentési**: Tájékoztatja a felhasználót, hogy kell telepíteniük (lehetőleg egy egyéni lap támogatással) böngészőt az eszközön.

## <a name="devices-and-browsers-tested"></a>Eszközök és böngészők tesztelése
A következő táblázat felsorolja az eszközök és böngészők tesztelt.

| | Böngésző&ast;     |  Eredmény  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome&ast; | Fázis|
| Huawei/One+ | Edge&ast; | Fázis|
| Huawei/One+ | Firefox&ast; | Fázis|
| Huawei/One+ | Brave&ast; | Fázis|
| Egy + | Ecosia&ast; | Fázis|
| Egy + | Kivitermesztő&ast; | Fázis|
| Huawei/One+ | Opera | Fázis|
| Huawei | OperaMini | Fázis|
| Huawei/One+ | InBrowser | Fázis|
| Egy + | Maxthon | Fázis|
| Huawei/One+ | DuckDuckGo | Hitelesítés a felhasználó által megszakítva|
| Huawei/One+ | UC böngésző | Hitelesítés a felhasználó által megszakítva|
| Egy + | Delfinvédelmi | Hitelesítés a felhasználó által megszakítva|
| Egy + | CM-böngésző | Hitelesítés a felhasználó által megszakítva|
| Huawei/One+ | nincs telepítve | AndroidActivityNotFound ex|

&ast; Támogatja az egyéni lap

## <a name="next-steps"></a>További lépések
Kód kódrészletek és a további tájékoztatást system böngésző Xamarin Android-, olvassa el ezt [útmutató](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  