---
title: "Az Azure Traffic Manager Azure App Service-forgalom vezérlése"
description: "A cikkben összefoglaló információkat az Azure Traffic Manager, az Azure App Service vonatkozik."
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
ms.openlocfilehash: efb732e9be62313eb199cb2cfbb1fa4d2cde0282
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Az Azure Traffic Manager Azure App Service-forgalom vezérlése
> [!NOTE]
> A cikkben összefoglaló információkat a Microsoft Azure Traffic Manager, az Azure App Service vonatkozik. További információ a Azure Traffic Manager maga érhetők el a hivatkozásokat, ez a cikk végén található.
> 
> 

## <a name="introduction"></a>Bevezetés
Azure Traffic Manager segítségével szabályozhatja, hogyan továbbítja őket a az Azure App Service apps webes ügyfelektől érkező kérelmeket. Amikor App Service-végpontok egy Azure Traffic Manager-profilt, Azure Traffic Manager nyomon követi az állapota (fut, leállítva vagy törölt.) az App Service-alkalmazásokhoz, hogy eldöntheti, amelyek ezekre a végpontokra kell kapnia a forgalmat.

## <a name="routing-methods"></a>útválasztási metódusait
Az Azure Traffic Manager négy különböző útválasztási metódusait használja. Ezeket a módszereket az alábbi lista ismerteti a, az Azure App Service eseménykódok.

* **[Prioritás](#priority):** elsődleges alkalmazás használata az összes forgalom, és adja meg a biztonsági mentések, abban az esetben, ha az elsődleges vagy a biztonsági másolatot készítő alkalmazások nem érhetők el.
* **[Súlyozott](#weighted):** forgalom szét kapnia, egyenletesen vagy megfelelően súlyok, amelyet kell megadni.
* **[Teljesítmény](#performance):** Ha különböző földrajzi helyeken található alkalmazások, az "legközelebbi" alkalmazást a legkisebb hálózati késést tekintetében használja.
* **[Földrajzi](#geographic):** közvetlen felhasználók adott alkalmazásokra alapján mely földrajzi helyen található, a DNS-lekérdezés származik. 

További információkért lásd: [Traffic Manager útválasztási módjai](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>App Service és a Traffic Manager-profilok
Az App Service alkalmazás forgalom vezérlő megadásához hozza létre a profilt az Azure Traffic Managerben, hogy használja a három közül betölteni a fentiekben ismertetett terheléselosztási módszert, és adja hozzá a végpont (ebben az esetben az App Service), amelynek a forgalmát szabályozni szeretné a profil. Az alkalmazás állapota (fut, leállítva, vagy törölve), hogy az Azure Traffic Manager forgalom ennek megfelelően utasíthatja a profil rendszeresen tájékoztatják.

Az Azure-ral Azure Traffic Manager használatakor vegye figyelembe a következő szempontokat:

* Csak alkalmazástelepítések belül ugyanabban a régióban, az App Service is biztosít feladatátvételi és ciklikus multiplexelés funkció módú telefonalkalmazás függetlenül.
* Ugyanabban a régióban használó telepítések App Service egy másik Azure-felhőszolgáltatásban együtt kombinálhatja a mindkét típusú végpontok engedélyezése hibrid környezetekben.
* Egy App Service-végpont régiónként csak egy profilt adhat meg. Egy adott területre végpontjaként válasszon ki egy alkalmazást, az adott régióban fennmaradó alkalmazásokat nem lehet kiválasztani, hogy válnak.
* Az App Service végpontok, melyet az Azure Traffic Manager-profil megjelenik a **tartománynevek** szakasz a konfigurálás oldalon, az az alkalmazás a profil, de nem konfigurálható.
* Egy alkalmazás egy profil hozzáadása után a **webhely URL-címe** az irányítópulton a portál az alkalmazás lapját jeleníti meg az az egyéni tartomány URL-CÍMÉT, ha a beállított egyet. Ellenkező esetben megjeleníti a Traffic Manager-profil URL-CÍMÉT (például `contoso.trafficmanager.net`). Mindkét közvetlen tartomány nevét, valamint az alkalmazás a Traffic Manager URL-cím jelennek meg az alkalmazás konfigurációs lapján alatt a **tartománynevek** szakasz.
* Az egyéni tartománynevek működnek megfelelően, de az alkalmazásokhoz hozzáadásán, úgy kell konfigurálnia a DNS-leképezés a Traffic Manager URL-címre mutasson. Az App Service alkalmazás az egyéni tartománynév beállítása további információkért lásd: [egy meglévő egyéni DNS-névvel hozzárendelése az Azure Web Apps](app-service-web-tutorial-custom-domain.md).
* Csak a standard vagy prémium szintű Azure Traffic Manager-profil üzemmódban működő alkalmazások adhat hozzá.

## <a name="next-steps"></a>Következő lépések
A fogalmi és technikai áttekintés az Azure Traffic Manager: [Traffic Manager – áttekintés](../traffic-manager/traffic-manager-overview.md).

Az App Service Traffic Manager használatával kapcsolatos további információkért tekintse meg a blogbejegyzések [Azure Traffic Manager használata az Azure-webhelyek](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) és [Azure Traffic Manager most már integrálható az Azure-webhelyek](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

