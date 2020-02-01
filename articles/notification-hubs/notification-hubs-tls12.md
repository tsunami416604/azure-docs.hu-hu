---
title: TLS-frissítések Notification Hubs
description: Ismerkedjen meg a TLS támogatásával az Azure Notification Hubsban.
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908526"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

A magasabb szintű biztonság érdekében Notification Hubs letiltja a 1,0-es és 1,1-es TLS-verziók támogatását 2020. április 30-án. Ezek a régebbi protokollok gyenge titkosítást biztosítanak, és ki vannak téve a VADÁLLATok és az USZKÁR elleni támadásoknak. Az Android 5-ös vagy újabb verzióját, vagy az iOS 5-ös vagy újabb verzióját futtató eszközökre telepített alkalmazások nem érintik ezt a változást, mivel ezek az operációs rendszerek támogatják a TLS 1,2-et, és az ügyfél és a kiszolgáló egyeztetni fogja a legmagasabb, kölcsönösen támogatott verzióját protokoll kapcsolat esetén.

Javasoljuk, hogy tekintse át az Azure Notification Hubst használó összes alkalmazást, és győződjön meg arról, hogy a legalkalmasabb kódtárak és TLS-csomagok a TLS 1,2-et támogatják.

## <a name="update-apps"></a>Alkalmazások frissítése

Gondoskodhat arról, hogy az iOS-alkalmazások a TLS 1,2-et használják az Apple hálózatkezelési biztonsági funkciója, az App Transport Security (ATS) használatával. Az ATS nem használható iOS 9,0 vagy macOS 10,11 rendszernél régebbi SDK-k esetében, és az [Apple dokumentációjában](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)olvashat bővebben.

SSLSocket-példányokat használó Android-alkalmazások esetén az összes SSLSocket-példányon állítsa be az engedélyezett protokollokat a [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D))feljegyzett módon.

A [TLS protokoll kompatibilitási](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) támogatás lapján található táblázat a kompatibilis TLS-verziókkal rendelkező operációs rendszerek feltérképezését segíti.

További információ: a [Windowson futó TLS protokollok támogatásának](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)áttekintése.

## <a name="next-steps"></a>Következő lépések

- [Notification Hubs áttekintése](notification-hubs-push-notification-overview.md)