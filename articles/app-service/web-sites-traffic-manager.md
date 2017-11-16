---
title: "Azure-webalkalmazás-forgalom vezérlése az Azure Traffic Managerrel"
description: "A cikkben összefoglaló információkat az Azure Traffic Manager, az Azure web apps vonatkozik."
services: app-service\web
documentationcenter: 
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.openlocfilehash: 5bf687afa8f42292a3b21b19a572c76926fef1cd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Azure-webalkalmazás-forgalom vezérlése az Azure Traffic Managerrel
> [!NOTE]
> Ez a cikk nyújt összefoglaló információkat a Microsoft Azure Traffic Manager, Azure Web Apps vonatkozik. További információ a Azure Traffic Manager maga érhetők el a hivatkozásokat, ez a cikk végén található.
> 
> 

## <a name="introduction"></a>Bevezetés
Azure Traffic Manager segítségével szabályozhatja, hogyan webes ügyfelektől érkező kérelmeket küld az Azure App Service web apps. Amikor web app végpontok egy Azure Traffic Manager-profil, Azure Traffic Manager nyomon követi az állapota (fut, leállítva vagy törölt.) a webalkalmazások így eldöntheti, amelyek ezekre a végpontokra kell kapnia a forgalmat.

## <a name="routing-methods"></a>útválasztási metódusait
Az Azure Traffic Manager három különböző útválasztási módszert használja. Ezeket a módszereket az alábbi lista ismerteti a, Azure-webalkalmazásokban eseménykódok.

* **[Prioritás](#priority):** minden forgalomhoz elsődleges webes alkalmazás használja, és adja meg a biztonsági mentések, abban az esetben, ha az elsődleges vagy a biztonsági mentési webalkalmazások nem érhetők el.
* **[Súlyozott](#weighted):** forgalom szét azokból a webalkalmazásokból, amelynek a egyenletesen vagy megfelelően súlyok, amelyet kell megadni.
* **[Teljesítmény](#performance):** Ha a webalkalmazások különböző földrajzi helyeken található, használja a "legközelebbi" webalkalmazás tekintetében a legkisebb hálózati késést.
* **[Földrajzi](#geographic):** meghatározott webes alkalmazásokra mutató közvetlen felhasználók alapján mely földrajzi helyen található, a DNS-lekérdezés származik. 

További információkért lásd: [Traffic Manager útválasztási módjai](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="web-apps-and-traffic-manager-profiles"></a>Webalkalmazások és a Traffic Manager-profilok
A vezérlő web app forgalom konfigurálásához profil létrehozása az Azure Traffic Managerben, hogy használja a három közül betölteni a fentiekben ismertetett terheléselosztási módszert, és adja meg az a végpontok (ebben az esetben az webalkalmazások), amelyekhez forgalmat a profilhoz. A webes alkalmazás állapota (fut, leállítva, vagy törölve), hogy az Azure Traffic Manager forgalom ennek megfelelően utasíthatja a profil rendszeresen tájékoztatják.

Az Azure-ral Azure Traffic Manager használatakor vegye figyelembe a következő szempontokat:

* A webes alkalmazás csak telepítésekhez belül ugyanabban a régióban Web Apps is biztosít feladatátvételi és ciklikus multiplexelés funkció függetlenül web app módban.
* Ugyanabban a régióban használó telepítések Web Apps egy másik Azure-felhőszolgáltatásban együtt kombinálhatja a mindkét típusú végpontok engedélyezése hibrid környezetekben.
* Egy webes alkalmazás végpontjának régiónként csak egy profilt adhat meg. A webes alkalmazás kiválasztható végpont egy régió tartozik, a fennmaradó webalkalmazások az adott régióban nem lehet kiválasztani, hogy válnak.
* A webes alkalmazás végpontok, melyet az Azure Traffic Manager-profil megjelenik a **tartománynevek** a konfigurálás oldalon szakaszban a webalkalmazást a profil, de nem konfigurálható a.
* Miután hozzáadta a webes alkalmazás-profilhoz, a **webhely URL-címe** az irányítópulton a Web portal alkalmazás lapját jeleníti meg az egyéni tartomány a webalkalmazás URL-címe Ha beállítása egy. Ellenkező esetben megjeleníti a Traffic Manager-profil URL-CÍMÉT (például `contoso.trafficmanager.net`). Mindkét közvetlen tartomány nevét a web app és a Traffic Manager URL-cím jelennek meg a webalkalmazás konfigurációs lapján alatt a **tartománynevek** szakasz.
* Az egyéni tartománynevek működnek megfelelően, de a webes alkalmazásokhoz való hozzáadásán, úgy kell konfigurálnia a DNS-leképezés a Traffic Manager URL-címre mutasson. Egy Azure webalkalmazás számára az egyéni tartománynév beállítása a további információkért lásd: [egy egyéni tartománynevet egy Azure-webhely konfigurálása](app-service-web-tutorial-custom-domain.md).
* Csak a standard vagy prémium szintű Azure Traffic Manager-profil üzemmódban működő webalkalmazások adhat hozzá.

## <a name="next-steps"></a>Következő lépések
A fogalmi és technikai áttekintés az Azure Traffic Manager: [Traffic Manager – áttekintés](../traffic-manager/traffic-manager-overview.md).

Traffic Manager a Web Apps használatával kapcsolatos további információkért lásd: a blogbejegyzések [Azure Traffic Manager használata az Azure-webhelyek](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) és [Azure Traffic Manager most már integrálható az Azure-webhelyek](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

