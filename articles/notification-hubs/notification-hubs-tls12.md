---
title: Értesítési központok TLS-frissítései
description: Ismerje meg a TLS támogatását az Azure Értesítési központokban.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 4da96df50e961f4291029a37e883fdcf88c6c87f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885752"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

A magasabb szintű biztonság érdekében az értesítési központok 2020. Ezek a régebbi protokollok gyenge kriptográfia, és ki vannak téve a BEAST és uszkár támadások. Az 5-ös vagy újabb verziójú Android-verziót vagy iOS 5-ös vagy újabb verziót futtató eszközökre telepített alkalmazásokat ez a változás nem érinti, mivel ezek az operációs rendszerek támogatják a TLS 1.2-t, és az ügyfél és a kiszolgáló a kapcsolatra a protokoll legmagasabb, kölcsönösen támogatott verzióját egyezteti.

Azt javasoljuk, hogy tekintse át az összes, az Azure Notification Hubs-ot használó alkalmazást, hogy a tls 1.2-es részét támogató tls-t és TLS-veremeket használják.

## <a name="update-apps"></a>Alkalmazások frissítése

Az Apple alkalmazásközlekedés biztonsága (APP Transport Security, ATS) nevű hálózati biztonsági funkciójával biztosíthatja, hogy az iOS-alkalmazások a TLS 1.2-t használják. Az ATS nem használható az iOS 9.0-nál vagy a macOS 10.11-nél régebbi SDK-knál, és erről bővebben olvashat az [Apple dokumentációjában](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

Az SSLSocket-példányokat használó Android-alkalmazások esetében állítsa be az engedélyezett protokollokat minden SSLSocket példányon a [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

A [TLS protokollkompatibilitási](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) támogatási lapon található táblázat segít a kompatibilis TLS-verziókkal rendelkező operációs rendszerek leképezésében.

További információt a [Windows TLS protokolljainak támogatásáról](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)szóló áttekintésben talál.

## <a name="next-steps"></a>További lépések

- [Értesítési központok – áttekintés](notification-hubs-push-notification-overview.md)