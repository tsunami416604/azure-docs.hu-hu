---
title: Jogkivonat-alapú (HTTP/2) hitelesítése az APN Szolgáltatáshoz az Azure Notification Hubs |} A Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan használható az APN Szolgáltatáshoz az új jogkivonat-hitelesítés
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 02/13/2019
ms.author: jowargo
ms.openlocfilehash: 890577c013a96fc06acf3b05881649ad8202a083
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56671641"
---
# <a name="token-based-http2-authentication-for-apns"></a>Jogkivonat-alapú (HTTP/2) hitelesítése az APN Szolgáltatáshoz

## <a name="overview"></a>Áttekintés

Ez a cikk részletesen bemutatja az új APNS HTTP/2 protokoll használatára a jogkivonat-alapú hitelesítéssel.

Az új protokollal főbb előnyei a következők:

* Tokenlétrehozási viszonylag gond nélkül az ingyenes (tanúsítványok képest)
* Nincsenek további lejárati dátumokat – biztosan megfeleljen az a hitelesítési tokenek és a visszavont tanúsítványok ellenőrzése
* Legfeljebb 4 KB-os is lehet is észleltünk adattartalmakat.
* Szinkron visszajelzés
* Az Apple legújabb protokollt használ – tanúsítványok továbbra is használhatja a bináris protokoll, amely meg van jelölve elavultként

Ez az új mechanizmus használatával teheti meg két lépésben néhány perc múlva:

1. Szerezze be a szükséges információkat az Apple Developer-fiók portálról
2. Az új adatokkal az értesítési központ konfigurálása

A Notification Hubs már az új hitelesítési rendszert használja az apns-sel minden beállítás.

Vegye figyelembe, hogy a beállítást, ha az APN szolgáltatás tanúsítványalapú át:

* a token tulajdonságait a tanúsítvány található a rendszerben, írja felül.
* de az alkalmazás továbbra is zökkenőmentesen értesítéseket kapni.

## <a name="obtaining-authentication-information-from-apple"></a>Hitelesítési adatok beszerzése az Apple-től

Jogkivonat-alapú hitelesítés engedélyezéséhez a következő tulajdonságokat az Apple fejlesztői fiók nyitása kell:

### <a name="key-identifier"></a>Kulcs azonosítója

A kulcs azonosítóját szerezhető be a "Kulcsok" lapot az Apple Developer-fiók

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Alkalmazás azonosítója & alkalmazás neve

Az alkalmazás neve a fejlesztői fiókban az Alkalmazásazonosítók lapján érhető el.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

Az alkalmazásazonosító a fejlesztői fiók a tagság részleteit megjelenítő oldalra keresztül érhető el.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>A hitelesítési jogkivonat

A hitelesítési jogkivonat egy tokent az alkalmazás létrehozása után lehet letölteni. Ez a token létrehozása a részletekért tekintse meg a [az Apple fejlesztői dokumentációjában](https://help.apple.com/xcode/mac/current/#/devdfd3d04a1).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Jogkivonat-alapú hitelesítés használatára az értesítési központ konfigurálása

### <a name="configure-via-the-azure-portal"></a>Az Azure Portalon keresztül konfigurálása

Ahhoz, hogy a jogkivonat-alapú hitelesítés a portálon, jelentkezzen be az Azure Portalon, és nyissa meg az értesítési központ > értesítési szolgáltatások > APNS panel.

Egy új tulajdonság – *hitelesítési mód*. Token kiválasztása lehetővé teszi, hogy a hub frissítse az összes releváns token tulajdonságait.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Adja meg a tulajdonságokat, az Apple developer-fiók lekért
* Válassza ki az alkalmazás módban (éles vagy tesztkörnyezet)
* Kattintson a **mentése** az APN szolgáltatás hitelesítő adatainak frissítése gomb

### <a name="configure-via-management-api-rest"></a>Felügyeleti API (REST) konfigurálása

Használhatja a [felügyeleti API-k](https://msdn.microsoft.com/library/azure/dn495827.aspx) frissíteni az értesítési központot a jogkivonat-alapú hitelesítés használatára.
Attól függően, hogy az alkalmazás konfigurálása esetén a védőfal vagy éles alkalmazás (az Apple Developer-fiók megadva) használja a megfelelő végpontok egyikét:

* A védőfal végpont: [https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* Éles végpont: [https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Jogkivonat-alapú hitelesítéshez szükséges API-verziót: **2017-04 vagy újabb**.

Íme egy példa egy PUT kérelem a hub frissíteni a jogkivonat-alapú hitelesítéssel:

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

### <a name="configure-via-the-net-sdk"></a>Konfigurálja a .NET SDK-n keresztül

A hub használata a jogkivonat-alapú hitelesítés használatával konfigurálhatja a [legújabb ügyféloldali SDK-val](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8).

Íme egy helyes használatát bemutató kódminta:

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

## <a name="reverting-to-using-certificate-based-authentication"></a>Visszatérés a Tanúsítványalapú hitelesítés használatával

Bármikor visszatérhet Tanúsítványalapú hitelesítés használatával bármilyen előző metódus használatával, és átadja a tanúsítvány helyett a token tulajdonságait. A művelet felülírja a korábban tárolt hitelesítő adatokat.
