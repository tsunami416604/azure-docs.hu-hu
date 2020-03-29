---
title: Jogkivonatalapú (HTTP/2) hitelesítés az APNS-hez az Azure Értesítési központokban | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja az új jogkivonat-hitelesítést az APNS-hez.
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
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 448b5c38371024c2eae900f4f87b343ee0a3b36a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263812"
---
# <a name="token-based-http2-authentication-for-apns"></a>Jogkivonatalapú (HTTP/2) hitelesítés az APNS-hez

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan használhatja az új APNS HTTP/2 protokollt jogkivonatalapú hitelesítéssel.

Az új protokoll használatának legfontosabb előnyei a következők:

* A tokenek létrehozása viszonylag egyszerű (a tanúsítványokhoz képest)
* Nincs több lejárati dátum – ön irányítja a hitelesítési tokeneket és azok visszavonását
* A hasznos teher mostantól akár 4 KB is lehet
* Szinkron visszajelzés
* Ön az Apple legújabb protokollján van – a tanúsítványok továbbra is a bináris protokollt használják, amely evehetetlenségre van megjelölve

Ennek az új mechanizmusnak a használata két lépésben hajtható végre:

* Szerezze be a szükséges információkat az Apple Developer-fiók portáljáról.
* Konfigurálja az értesítési központot az új információkkal.

Az értesítési központok most úgy vannak beállítva, hogy az új hitelesítési rendszert használják az APNS-sel.

Vegye figyelembe, hogy ha áttelepített a tanúsítvány hitelesítő adatok at APNS, a jogkivonat tulajdonságai felülírják a tanúsítványt a rendszerünkben, de az alkalmazás továbbra is zökkenőmentesen kap értesítéseket.

## <a name="obtaining-authentication-information-from-apple"></a>Hitelesítési adatok beszerzése az Apple-től

A tokenalapú hitelesítés engedélyezéséhez az Apple Developer-fiókkövetkező tulajdonságaira van szükség:

### <a name="key-identifier"></a>Kulcsazonosító

A kulcsazonosító **az** Apple Developer-fiók **Tanúsítványok, azonosítók & profilok**lapján szerezhető be:

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Alkalmazásazonosító és alkalmazásnév

Az alkalmazás neve és azonosítója a fejlesztői fiók **Tanúsítványok, azonosítók & profilok** lapján is elérhető:

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Konfigurálás a .NET SDK-n vagy az Azure portalon keresztül

Beállíthatja a hub, hogy a legújabb [ügyfél SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)használatával, vagy az Azure Portalon használja a token-alapú hitelesítést. A jogkivonat-alapú hitelesítés engedélyezéséhez a portálon jelentkezzen be az Azure Portalon, és nyissa meg az értesítési központ **Beállítások > Apple (APNS)** panel. Válassza a **Token** a **Hitelesítési mód** tulajdonságból a hub frissítéséhez az összes vonatkozó jogkivonat-tulajdonsággal.

![Token konfigurálása](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Adja meg az Apple Developer-fiókjából beolvasott tulajdonságokat.
* Válassza ki az alkalmazás módot (**Éles** vagy **homokozó**).
* Kattintson a **Mentés** gombra az APNS-hitelesítő adatok frissítéséhez.

A jogkivonat-alapú hitelesítő adatok a következő mezőkből állnak:

* **Kulcsazonosító**: Az Apple Developer portálon létrehozott személyes kulcs azonosítója; például `2USFGKSKLT`.
* **Csapatazonosító**: Más néven "előtag" vagy "Alkalmazáselőtag". Ez a szervezet azonosítója az Apple Fejlesztői portálon; például `S4V3D7CHJR`.
* **Bundle ID**: Más néven az "App ID." Ez az alkalmazás csomagazonosítója; például `com.microsoft.nhubsample2019`. Ne feledje, hogy számos alkalmazáshoz használhat egy kulcsot. Ez az érték `apns-topic` leképezi a HTTP-fejléc, amikor értesítést küld, és az adott alkalmazás megcélzására szolgál.
* **Token**: Más néven a "kulcs" vagy "titkos kulcs.". Ez az Apple Developer portálon létrehozott .p8 fájlból származik. A kulcsnak engedélyeznie kell az APNS-t (amely a kulcs létrehozásakor az Apple Developer portalon van kiválasztva). Az értéknek rendelkeznie kell a PEM fejléc/lábléc lecsupaszítva, amikor az NH Portal/API-nak adja meg.
* **Végpont**: Ez egy váltás az értesítési központok portál panelen, és egy karakterlánc-mező az API-ban. Az érvényes `https://api.push.apple.com` `https://api.sandbox.push.apple.com`értékek vagy . Az Értesítési központok ezt az értéket használja az éles vagy a sandbox környezetben, az értesítések küldéséhez. Ennek meg `aps-environment` kell egyeznie az alkalmazásban lévő jogosultsággal, ellenkező esetben a létrehozott APNS-eszköztokenek nem felelnek meg a környezetnek, és az értesítések nem küldhetők el.

Íme egy kódminta, amely a helyes használatot szemlélteti:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>További lépések

* [Azure-értesítési központ létrehozása az Azure Portalon](create-notification-hub-portal.md)
* [Értesítési központ konfigurálása az Azure Portalon](create-notification-hub-portal.md)
