---
title: "A webalkalmazás egyéni tartománynév beállítása az Azure App Service által használt Traffic Manager terheléselosztás."
description: "Adjon nevet az egyéni tartomány egy egy webalkalmazást az Azure App Service szolgáltatásban, amely tartalmazza a Traffic Manager a(z) terheléselosztást."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.openlocfilehash: c78fb7883559e46ebaa1d8dab59a15c55fb76fdf
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>A webalkalmazáshoz tartozó egyéni tartománynév konfigurálása az Azure App Service szolgáltatásban a Traffic Managerrel
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Ez a cikk ismerteti az egyéni tartománynév általános utasításokat egy [App Service](app-service-web-overview.md) alkalmazást, amely integrálva van a [Traffic Manager](../traffic-manager/traffic-manager-overview.md) a(z) terheléselosztást.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>DNS-rekordok ismertetése
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>A szabványos mód a webalkalmazások konfigurálása
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Az egyéni tartomány DNS-rekord hozzáadása
> [!NOTE]
> Ha tartományi Azure App Service Web Apps keresztül vásárolt, majd ugorjon a következő lépéseket és az utolsó lépésében hivatkozik [tartomány vásárlása Web Apps](custom-dns-web-site-buydomains-web-app.md) cikk.
> 
> 

Az egyéni tartomány társítandó egy webalkalmazást az Azure App Service-ben, hozzá kell adnia egy új bejegyzést a DNS-tábla az egyéni tartományhoz. Ez a felügyeleti eszközök tartomány szolgáltatótól használatával teheti meg.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Minden tartomány-szolgáltató a mintaadatokról eltérők lehetnek, amíg leképez *a* az egyéni tartománynevet (például **contoso.com**) *való* a Traffic Manager szolgáltatásbeli tartománynevére ( **contoso.trafficmanager.NET**), amely integrálva van a webes alkalmazást.
   
> [!NOTE]
> Ha egy olyan rekordot már használatban van, és megelőző jelleggel kötni az alkalmazások azt szeretné, egy további CNAME-rekordot is létrehozhat. Ahhoz például, hogy megelőző jelleggel kötési **www.contoso.com** a webes alkalmazás, hozzon létre egy CNAME rekordot a **awverify.www** való **contoso.trafficmanager.net**. A webalkalmazás a "www" CNAME rekord módosítása nélkül Ezután felvehet "www.contoso.com". További információkért lásd: [hozzon létre DNS-rekordok az egyéni tartomány webalkalmazáshoz][CREATEDNS].
> 
> 

Miután befejezte a hozzáadása vagy módosítása a tartomány DNS-szolgáltató DNS-rekordokat, a módosítások mentéséhez.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>A Traffic Manager engedélyezése
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>További lépések
További információk: [Node.js fejlesztői központ](/develop/nodejs/).

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
