---
title: "Az Azure App Service IP-korlátozások |} Microsoft Docs"
description: "Az IP-korlátozásokat használata az Azure App Service"
author: btardif
manager: stefsch
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/23/2017
ms.author: byvinyal
ms.openlocfilehash: 551f34436dfeac14bb6e8676dd7c9b10d30a3af3
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="azure-app-service-static-ip-restrictions"></a>Az Azure App Service statikus IP-korlátozások #

Az IP-korlátozásokat engedélyezi annak meghatározását, amelyek jogosultak hozzáférni az alkalmazáshoz IP-címek listáját. Az engedélyezési listán lehetnek egyedi IP-címek vagy egy adott IP-címek határozzák meg egy alhálózati maszkot.

Amikor az alkalmazás kérelem ügyfélről történik, az IP-címet az engedélyezési listán képest értékeli ki. Ha az IP-cím nem szerepel a listában, az alkalmazás a válaszol-e egy [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) állapotkódot.

Az IP-korlátozásokat a Web.config fájl, amely az alkalmazás akkor futási időben vannak definiálva (pontosabban, korlátozások engedélyezett IP-címek az applicationHost.config fájlban egy sor szúrja be, ha is engedélyezett IP-címek egy készletének a web.config fájlban, akkor lépnek a sorrend). Bizonyos körülmények között néhány modul előfordulhat, hogy előtt kell végrehajtani a HTTP-feldolgozási folyamat IP-korlátozások logika. Ha ez történik, egy másik HTTP-hibakód: a kérés nem teljesíthető.

Az IP-korlátozásokat a rendszer a ugyanazon alkalmazás terv szolgáltatáspéldány be az alkalmazáshoz hozzárendelt alapján értékeli ki.

Az IP-korlátozási szabály hozzáadása az alkalmazáshoz, a menü használatával nyissa meg a **hálózati**>**IP-korlátozásokat** , majd kattintson a **IP-korlátozások konfigurálása**

![IP restrictions] (media/app-service-ip-restrictions/ip-restrictions.png)

Itt tekintheti meg az alkalmazás megadott IP-korlátozás szabályok listája.

![lista IP-korlátozások](media/app-service-ip-restrictions/browse-ip-restrictions.png)

Rákattinthat a **[+] Hozzáadás** egy új IP-korlátozási szabály hozzáadása.

![IP-korlátozás hozzáadása](media/app-service-ip-restrictions/add-ip-restrictions.png)
