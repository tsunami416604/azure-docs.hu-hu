---
title: DNS-nevek konfigurálása Traffic Manager-Azure App Servicet használó alkalmazásokhoz
description: Egyéni tartománynevet használhat olyan Azure App Service webalkalmazáshoz, amely a terheléselosztáshoz Traffic Manager tartalmaz.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e6a59d519ae8bb515ab16632bd39509682959f50
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074032"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Egyéni tartománynév konfigurálása webalkalmazáshoz Azure App Service használatával Traffic Manager
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Ez a cikk általános útmutatást nyújt az egyéni tartománynevek használatáról [app Service](overview.md) alkalmazással, amely a terheléselosztáshoz [Traffic Manager](../traffic-manager/traffic-manager-overview.md) integrált.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>DNS-rekordok ismertetése
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Webalkalmazások konfigurálása normál üzemmódra
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>DNS-rekord hozzáadása az egyéni tartományhoz
> [!NOTE]
> Ha Azure App Service Web Apps megvásárolta a tartományt, akkor hagyja ki a következő lépéseket, és tekintse meg a tartomány megvásárlásának utolsó lépését [Web Apps](manage-custom-dns-buy-domain.md) cikkhez.
> 
> 

Ha az egyéni tartományt a Azure App Service webalkalmazásával szeretné társítani, új bejegyzést kell felvennie az egyéni tartomány DNS-táblájába. Ezt a tartományi szolgáltató felügyeleti eszközeinek használatával végezheti el.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Míg az egyes tartományi szolgáltatók sajátosságai eltérőek, az egyéni tartománynévtől (például **contoso.com**) a webalkalmazáshoz integrált Traffic Manager tartománynévre (**contoso.trafficmanager.net**) van leképezve.

> [!NOTE]
> Ha egy rekord már használatban van, és megelőző jelleggel kell kötnie az alkalmazásokat, létrehozhat egy további CNAME rekordot is. Ha például a megelőző jelleggel szeretné kötni a webalkalmazáshoz a kötést **\.** , hozzon létre egy CNAME rekordot a **awverify. www** contoso.com a **contoso.trafficmanager.net**. Ezután a "www" CNAME\.rekord módosítása nélkül is hozzáadhat "www contoso.com" a webalkalmazáshoz. További információ: [DNS-rekordok létrehozása egy webalkalmazáshoz egyéni tartományban][CREATEDNS].

Miután befejezte a DNS-rekordok hozzáadását vagy módosítását a tartományi szolgáltatónál, mentse a módosításokat.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Traffic Manager engedélyezése
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>További lépések
További információk: [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/).

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
