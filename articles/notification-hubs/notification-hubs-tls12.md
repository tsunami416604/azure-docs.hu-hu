---
title: Értesítési központok TLS-frissítései
description: Ismerje meg a TLS támogatását az Azure Értesítési központokban.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87309e20efd9d6f8bd1a659451e5a603e6b95bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908526"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

A magasabb szintű biztonság érdekében az értesítési központok 2020. Ezek a régebbi protokollok gyenge kriptográfia, és ki vannak téve a BEAST és uszkár támadások. Az 5-ös vagy újabb verziójú Android-verziót vagy iOS 5-ös vagy nagyobb verziót futtató eszközökre telepített alkalmazásokat ez a változás nem érinti, mivel ezek az operációs rendszerek támogatják a TLS 1.2-t, és az ügyfél és a kiszolgáló a rendszer legmagasabb, kölcsönösen támogatott verzióját egyezteti. protokollt a csatlakozáskor.

Azt javasoljuk, hogy tekintse át az összes, az Azure Notification Hubs-ot használó alkalmazást, hogy a tls 1.2-es részét támogató tls-t és TLS-veremeket használják.

## <a name="update-apps"></a>Alkalmazások frissítése

Az Apple alkalmazásközlekedés biztonsága (APP Transport Security, ATS) nevű hálózati biztonsági funkciójával biztosíthatja, hogy az iOS-alkalmazások a TLS 1.2-t használják. Az ATS nem használható az iOS 9.0-nál vagy a macOS 10.11-nél régebbi SDK-knál, és erről bővebben olvashat az [Apple dokumentációjában](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

Az SSLSocket-példányokat használó Android-alkalmazások esetében állítsa be az engedélyezett protokollokat minden SSLSocket példányon a [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

A [TLS protokollkompatibilitási](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) támogatási lapon található táblázat segít a kompatibilis TLS-verziókkal rendelkező operációs rendszerek leképezésében.

További információt a [Windows TLS protokolljainak támogatásáról](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)szóló áttekintésben talál.

## <a name="next-steps"></a>További lépések

- [Értesítési központok – áttekintés](notification-hubs-push-notification-overview.md)