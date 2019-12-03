---
title: DNS-nevek konfigurálása Traffic Manager
description: Megtudhatja, hogyan konfigurálhat egyéni tartományt egy olyan Azure App Service alkalmazáshoz, amely a terheléselosztáshoz Traffic Manager integrálódik.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 08/17/2016
ms.custom: seodec18
ms.openlocfilehash: 9139b83f1f2920da47b4a0d440f622626d41c938
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689281"
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
> Ha Azure App Service Web Apps megvásárolta a tartományt, akkor hagyja ki a következő lépéseket, és tekintse meg a [tartomány megvásárlásának](manage-custom-dns-buy-domain.md) utolsó lépését Web Apps cikkhez.
> 
> 

Ha az egyéni tartományt a Azure App Service webalkalmazásával szeretné társítani, új bejegyzést kell felvennie az egyéni tartomány DNS-táblájába. Ezt a tartományi szolgáltató felügyeleti eszközeinek használatával végezheti el.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Míg az egyes tartományi szolgáltatók sajátosságai eltérőek, az egyéni tartománynévtől (például **contoso.com** *) a* webalkalmazáshoz integrált Traffic Manager tartománynévre (**contoso.trafficmanager.net**) van *leképezve* .

> [!NOTE]
> Ha egy rekord már használatban van, és megelőző jelleggel kell kötnie az alkalmazásokat, létrehozhat egy további CNAME rekordot is. Ahhoz például, hogy megelőző jelleggel a kötések **www\.contoso.com** a webalkalmazáshoz, hozzon létre egy CNAME rekordot a **awverify. www** és a **contoso.trafficmanager.net**között. Ezután hozzáadhat "www\.contoso.com" a webalkalmazáshoz a "www" CNAME rekord módosítása nélkül. További információ: [DNS-rekordok létrehozása egy webalkalmazáshoz egyéni tartományban][CREATEDNS].

Miután befejezte a DNS-rekordok hozzáadását vagy módosítását a tartományi szolgáltatónál, mentse a módosításokat.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Traffic Manager engedélyezése
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Következő lépések
További információk: [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/).

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
