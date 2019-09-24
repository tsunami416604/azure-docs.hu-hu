---
title: Jogkivonat-alapú (HTTP/2) hitelesítés a APNS az Azure Notification Hubsban | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan használható az új jogkivonat-hitelesítés a APNS
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: a7fdaae33e28bd543b44c54868324339d1269bc2
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213122"
---
# <a name="token-based-http2-authentication-for-apns"></a>Jogkivonat-alapú (HTTP/2) hitelesítés a APNS

## <a name="overview"></a>Áttekintés

Ez a cikk részletesen ismerteti, hogyan használható az új APNS HTTP/2 protokoll a jogkivonat-alapú hitelesítéssel.

Az új protokoll használatának legfontosabb előnyei a következők:

* A jogkivonat-létrehozás viszonylag ingyenes (a tanúsítványokhoz képest)
* Nincs több lejárati dátum – a hitelesítési jogkivonatok és azok visszavonása felett van
* A hasznos adatok akár 4 KB-os is lehetnek
* Szinkron visszajelzés
* Az Apple legújabb protokollját használja – a tanúsítványok továbbra is a bináris protokollt használják, amely elavultként van megjelölve.

Az új mechanizmus használata két lépésben végezhető el néhány perc alatt:

1. Szerezze be a szükséges információkat az Apple fejlesztői fiók portálján
2. Az értesítési központ konfigurálása az új adatokkal

A Notification Hubs mostantól az új hitelesítési rendszer használatára van beállítva a APNS használatával.

Vegye figyelembe, hogy ha a APNS tanúsítvány-hitelesítő adatainak használatával migrálta a tanúsítványt:

* a jogkivonat tulajdonságai felülírják a tanúsítványt a rendszeren,
* az alkalmazás azonban továbbra is zökkenőmentesen fogadja az értesítéseket.

## <a name="obtaining-authentication-information-from-apple"></a>Hitelesítési adatok beszerzése az Apple-től

A jogkivonat-alapú hitelesítés engedélyezéséhez a következő tulajdonságok szükségesek az Apple Developer-fiókjából:

### <a name="key-identifier"></a>Kulcs azonosítója

A kulcs azonosítóját az Apple fejlesztői fiókjának Keys (kulcsok) lapjáról lehet beszerezni

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Alkalmazás azonosítója & alkalmazás neve

Az alkalmazás neve a fejlesztői fiók alkalmazás-azonosítók lapján érhető el.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

Az alkalmazás azonosítója a fejlesztői fiók tagság részletei lapján érhető el.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>Hitelesítési jogkivonat

A hitelesítési jogkivonat letölthető, miután létrehozta az alkalmazáshoz tartozó jogkivonatot. A jogkivonat létrehozásával kapcsolatos részletekért tekintse meg az [Apple fejlesztői dokumentációját](https://help.apple.com/xcode/mac/current/#/devdfd3d04a1).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Az értesítési központ konfigurálása jogkivonat-alapú hitelesítés használatára

### <a name="configure-via-the-azure-portal"></a>Konfigurálás a Azure Portal használatával

Ha engedélyezni szeretné a jogkivonat-alapú hitelesítést a portálon, jelentkezzen be a Azure Portalba, és lépjen az értesítési központ > Notification Services > APNS panelre.

Új tulajdonság – *hitelesítési mód*. A jogkivonat kiválasztásával frissítheti a hubot az összes vonatkozó jogkivonat-tulajdonsággal.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Adja meg az Apple Developer-fiókból beolvasott tulajdonságokat
* Válassza ki az alkalmazási módot (éles vagy homokozó)
* Kattintson a **Save (Mentés** ) gombra a APNS hitelesítő adatainak frissítéséhez

### <a name="configure-via-management-api-rest"></a>Konfigurálás felügyeleti API-n keresztül (REST)

A [felügyeleti API](https://msdn.microsoft.com/library/azure/dn495827.aspx) -k segítségével a jogkivonat-alapú hitelesítés használatára frissítheti az értesítési központot.
Attól függően, hogy az Ön által konfigurált alkalmazás egy sandbox vagy éles alkalmazás (az Apple fejlesztői fiókjában van megadva), használja a megfelelő végpontok egyikét:

* Homokozó végpontja:[https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* Üzemi végpont:[https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> A jogkivonat-alapú hitelesítéshez a (z) API-verziója szükséges: **2017-04 vagy újabb**.

Az alábbi példa egy PUT-kérést mutat be, amely jogkivonat-alapú hitelesítéssel frissíti a hubot:

    ```text
    PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
      "Properties": {
        "ApnsCredential": {
          "Properties": {
            "KeyId": "<Your Key Id>",
            "Token": "<Your Authentication Token>",
            "AppName": "<Your Application Name>",
            "AppId": "<Your Application Id>",
            "Endpoint":"<Sandbox/Production Endpoint>"
          }
        }
      }
    ```

### <a name="configure-via-the-net-sdk"></a>Konfigurálás a .NET SDK használatával

A hub-t beállíthatja a jogkivonat-alapú hitelesítés használatára a [legújabb ÜGYFÉLOLDALI SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8)-val.

A helyes használatot bemutató kód minta:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR TOKEN HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="reverting-to-using-certificate-based-authentication"></a>Visszaállítása tanúsítványalapú hitelesítés használatára

Bármikor visszaállíthatja a tanúsítványalapú hitelesítést a fenti metódusok használatával, és a jogkivonat tulajdonságai helyett átadhatja a tanúsítványt. Ez a művelet felülírja a korábban tárolt hitelesítő adatokat.
