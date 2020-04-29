---
title: TLS-frissítések Notification Hubs
description: Ismerkedjen meg a TLS támogatásával az Azure Notification Hubsban.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885752"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

A magasabb szintű biztonság érdekében Notification Hubs letilthatja a 1,0-es és 1,1-es TLS-verziók támogatását 2020 (2020. április 30-ig). Ezek a régebbi protokollok gyenge titkosítást biztosítanak, és ki vannak téve a VADÁLLATok és az USZKÁR elleni támadásoknak. Az Android 5-ös vagy újabb verzióját, vagy az iOS 5-ös vagy újabb verzióját futtató eszközökre telepített alkalmazások nem érintik ezt a változást, mivel ezek az operációs rendszerek támogatják a TLS 1,2-et, és az ügyfél és a kiszolgáló a kapcsolaton keresztül egyezteti a protokoll legmagasabb kölcsönösen támogatott verzióját.

Javasoljuk, hogy tekintse át az Azure Notification Hubst használó összes alkalmazást, és győződjön meg arról, hogy a legalkalmasabb kódtárak és TLS-csomagok a TLS 1,2-et támogatják.

## <a name="update-apps"></a>Alkalmazások frissítése

Gondoskodhat arról, hogy az iOS-alkalmazások a TLS 1,2-et használják az Apple hálózatkezelési biztonsági funkciója, az App Transport Security (ATS) használatával. Az ATS nem használható iOS 9,0 vagy macOS 10,11 rendszernél régebbi SDK-k esetében, és az [Apple dokumentációjában](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)olvashat bővebben.

SSLSocket-példányokat használó Android-alkalmazások esetén az összes SSLSocket-példányon állítsa be az engedélyezett protokollokat a [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D))feljegyzett módon.

A [TLS protokoll kompatibilitási](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) támogatás lapján található táblázat a kompatibilis TLS-verziókkal rendelkező operációs rendszerek feltérképezését segíti.

További információ: a [Windowson futó TLS protokollok támogatásának](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)áttekintése.

## <a name="next-steps"></a>További lépések

- [Notification Hubs áttekintése](notification-hubs-push-notification-overview.md)