---
title: DNS-neveket, amelyek használják a Traffic Manager – Azure App Service-alkalmazások konfigurálása
description: Egy egyéni tartománynevet használja az-webalkalmazás létrehozása az Azure App Service, amely a terheléselosztás a Traffic Manager tartalmazza.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b207d9e3123245e5cb7dddb625f7488886591ae5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089862"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Webes alkalmazás egy egyéni tartománynév konfigurálása az Azure App Service, Traffic Manager használatával
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Ez a cikk az egyéni tartománynév használatára vonatkozó általános útmutatást biztosít egy [App Service-ben](overview.md) alkalmazást, amely integrálva van a [Traffic Manager](../traffic-manager/traffic-manager-overview.md) terheléselosztásra.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>DNS-rekordok ismertetése
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>A web apps a normál üzemeltetési módra konfigurálni
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>A DNS-rekordot az egyéni tartomány hozzáadása
> [!NOTE]
> Ha tartományhoz az Azure App Service Web Apps keresztül vásárolt, akkor ugorjon a következő lépéseket és az utolsó lépésében tekintse meg [tartomány vásárlása a Web Apps](manage-custom-dns-buy-domain.md) cikk.
> 
> 

Az egyéni tartomány hozzárendelése egy webalkalmazást az Azure App Service-ben, hozzá kell adnia egy új bejegyzést a DNS-tábla az egyéni tartományhoz. Ehhez a tartományszolgáltatója felügyeleti eszközöket.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Minden tartomány szolgáltató tulajdonságairól eltérőek lehetnek, amíg leképezése *a* az egyéni tartomány nevét (például **contoso.com**) *való* a Traffic Manager szolgáltatásbeli tartománynévre ( **contoso.trafficmanager.NET**), amely integrálva van a webalkalmazás.

> [!NOTE]
> Ha a rekord már használatban van, és előrelátó módon kötni az alkalmazások azt szeretné, egy további CNAME-rekordot is létrehozhat. Például előrelátó módon kötési **www\.contoso.com** a webalkalmazást, hozzon létre egy CNAME rekordot a **awverify.www** való **contoso.trafficmanager.net**. Ezután hozzáadhatja "www\.contoso.com" a "www" CNAME-rekord módosítása nélkül a webalkalmazáshoz. További információkért lásd: [-webalkalmazás létrehozása az egyéni tartomány létrehozása DNS-rekordok][CREATEDNS].

Miután befejezte a hozzáadásával vagy módosításával a tartományszolgáltatója DNS-rekordokat, a módosítások mentéséhez.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>A Traffic Manager engedélyezése
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>További lépések
További információk: [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/).

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
