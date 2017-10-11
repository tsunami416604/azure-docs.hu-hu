---
title: "Jogkivonat-alapú (HTTP/2) hitelesítési apns az Azure Notification hubs használatával |} Microsoft Docs"
description: "Ez a témakör azt ismerteti, hogyan használhatók ki az új jogkivonat hitelesítési apns"
services: notification-hubs
documentationcenter: .net
author: kpiteira
manager: erikre
editor: 
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 05/17/2017
ms.author: kapiteir
ms.openlocfilehash: 5a21bcd9f12fc3f96b17a556ba15526c35ababe2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="token-based-http2-authentication-for-apns"></a>Az APNS-jogkivonatalapú (HTTP/2) hitelesítés
## <a name="overview"></a>Áttekintés
Ez a cikk részletezi az új APNS HTTP/2 protokoll használata hitelesítési token módszer.

Az új protokollal főbb előnyei a következők:
-   Jogkivonatok létrehozásához viszonylag teljesen szabad (tanúsítványok képest)
-   Nincs több lejárati dátumokat – jogkörrel rendelkezik arra a hitelesítési tokenek és a visszavont tanúsítványok ellenőrzése
-   Legfeljebb 4 KB-os mostantól lehet hasznos adat található
- Szinkron visszajelzés
-   A legújabb protokoll Apple – tanúsítványok továbbra is használhatják a bináris protokoll, amely meg van jelölve érvénytelenítése

Ez új mechanizmus használatával is végrehajtható két lépésben néhány perc múlva:
1.  A szükséges adatok lekérését az Apple Developer-fiók portálon
2.  Az új információkkal az értesítési központ konfigurálása

A Notification Hubs mostantól az új hitelesítési rendszere az apns-sel használni minden beállítás. 

Vegye figyelembe, hogy a beállítást, ha áttelepítette az APNS tanúsítvány hitelesítő adatait:
- a token tulajdonságok felülírni a tanúsítványt a rendszerben,
- de az alkalmazás továbbra is problémamentesen értesítéseket.

## <a name="obtaining-authentication-information-from-apple"></a>Hitelesítési adatok beszerzése az Apple-től
Jogkivonat-alapú hitelesítés engedélyezéséhez a következő tulajdonságok Apple Developer fiókjából kell:
### <a name="key-identifier"></a>Kulcsazonosító
A kulcsazonosító érhető el az Apple Developer-fiók "Kulcsok" oldaláról

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Alkalmazásazonosító & alkalmazás nevét
Az alkalmazás nevét a fejlesztői fiók App IDs oldalon keresztül érhető el. 
![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

Az alkalmazásazonosító a tagság részleteit megjelenítő oldalon a fejlesztői fiókban keresztül érhető el.
![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)


### <a name="authentication-token"></a>Hitelesítési jogkivonata
A hitelesítési jogkivonat letölthető az alkalmazás jogkivonat létrehozása után. Hogyan hozza létre a jogkivonatot a részletekért tekintse meg a [az Apple fejlesztői dokumentációját](http://help.apple.com/xcode/mac/current/#/dev11b059073?sub=dev1eb5dfe65).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Jogkivonat-alapú hitelesítés használata az értesítési központ konfigurálása
### <a name="configure-via-the-azure-portal"></a>Az Azure-portál konfigurálása
Ahhoz, hogy a jogkivonat alapú hitelesítés a portálon, jelentkezzen be az Azure-portálon, majd nyissa meg az értesítési központba > értesítési szolgáltatások > APNS panel. 

Új tulajdonság – *hitelesítési mód*. Token kijelölésével a központ frissítése az összes releváns token tulajdonság.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

- Adja meg az Apple fejlesztői fiókját lekért tulajdonságait 
- Válassza ki az alkalmazás módot (éles vagy védőfal) 
- Kattintson a Mentés az APN szolgáltatás hitelesítő adatait. 

### <a name="configure-via-management-api-rest"></a>(REST) API Management szolgáltatáson keresztül konfigurálása

Használhatja a [felügyeleti API-k](https://msdn.microsoft.com/library/azure/dn495827.aspx) jogkivonat-alapú hitelesítés használata az értesítési központ frissítése.
Attól függően, hogy az alkalmazás, konfigurálja a védőfal vagy üzemi alkalmazások (Apple Developer fiókjához megadott) használja a megfelelő végpont közül:

- A védőfal végpont: [https://api.development.push.apple.com:443/3/eszköz](https://api.development.push.apple.com:443/3/device)
- Éles végpont: [https://api.push.apple.com:443/3/eszköz](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Jogkivonat-alapú hitelesítéshez, az API-verziót: **2017-04 vagy újabb**.
> 
> 

Íme egy példa a központ frissítése a jogkivonat-alapú hitelesítéssel PUT kérelmet:


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
        

### <a name="configure-via-the-net-sdk"></a>Konfigurálja a .NET SDK használatával
A központ használandó jogkivonat-alapú hitelesítés használatával konfigurálhatja a [legújabb ügyfél SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8). 

Íme egy helyes használatát bemutató példát:


        NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
        string token = "YOUR TOKEN HERE";
        string keyId = "YOUR KEY ID HERE";
        string appName = "YOUR APP NAME HERE";
        string appId = "YOUR APP ID HERE";
        NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
        desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
        desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
        nm.UpdateNotificationHubAsync(desc);

## <a name="reverting-to-using-certificate-based-authentication"></a>Visszatérés a Tanúsítványalapú hitelesítés használatával
Bármikor visszatérhet előző módszerrel, és átadja a tanúsítványt a jogkivonat tulajdonságok helyett a Tanúsítványalapú hitelesítés használatával. A művelet felülírja a korábban tárolt hitelesítő adatokat.
