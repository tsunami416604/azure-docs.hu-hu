---
title: Az Azure Traffic Manager az Azure App Service-forgalom szabályozása
description: Ez a cikk nyújt összefoglaló információkat az Azure Traffic Manager kapcsolódik az Azure App Service-ben.
services: app-service\web
documentationcenter: ''
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
ms.openlocfilehash: 5e4dfec4bdc9deaf1a57413c1cba4ceefa5195d6
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246344"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Az Azure Traffic Manager az Azure App Service-forgalom szabályozása
> [!NOTE]
> Ez a cikk nyújt összefoglaló információkat a Microsoft Azure Traffic Manager kapcsolódik az Azure App Service-ben. Kövesse a hivatkozásokat, ez a cikk végén található további információ az Azure Traffic Manager magát.
> 
> 

## <a name="introduction"></a>Bevezetés
Az Azure Traffic Manager segítségével szabályozhatja, hogyan oszlanak meg az Azure App Service Apps webes ügyfelektől érkező kérelmek. Amikor App Service-végpontokat ad egy Azure Traffic Manager-profilhoz, az Azure Traffic Manager nyomon követi az App Service-alkalmazások állapotát (fut, leállítva vagy törölve), így eldöntheti, hogy melyik végpont kapja a forgalmat.

## <a name="routing-methods"></a>Útválasztási módszerek
Az Azure Traffic Manager használ a négy különböző útválasztási módszert. Az Azure App Service-megvalósításáért ezen módszerek az alábbi lista ismerteti.

* **[Prioritás](../traffic-manager/traffic-manager-routing-methods.md#priority):** minden forgalmat az elsődleges alkalmazás használata, és adja meg a biztonsági mentések, abban az esetben, ha az elsődleges vagy a biztonsági másolatot készítő alkalmazások nem érhető el.
* **[Súlyozott](../traffic-manager/traffic-manager-routing-methods.md#weighted):** elosztani a forgalmat alkalmazásokat, egyenletesen vagy megfelelően súlyok, amelyek azt határozzák meg.
* **[Teljesítmény](../traffic-manager/traffic-manager-routing-methods.md#performance):** , az alkalmazások a különböző földrajzi helyeken, ha használja a legalacsonyabb hálózati késéssel szempontjából a "legközelebbi" alkalmazást.
* **[Földrajzi](../traffic-manager/traffic-manager-routing-methods.md#geographic):** közvetlen felhasználók adott alkalmazásokra alapú mely földrajzi helye határozza meg a DNS-lekérdezés származik. 

További információkért lásd: [Traffic Manager útválasztási módszerei](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>App Service-ben és a Traffic Manager-profilok
Az App Service-alkalmazás forgalom konfigurálásához hozzon létre egy profilt az Azure Traffic Managerben, hogy használja a három közül betölteni a korábban leírt terheléselosztási módszert, és adja hozzá az a végpontok (ebben az esetben az App Service-ben), amelynek a forgalmát szabályozni szeretné a profil. Az alkalmazás állapotát (fut, leállítva vagy törölt) rendszeresen közlik a profilt, hogy az Azure Traffic Manager forgalom ennek megfelelően irányíthatók.

Ha az Azure Traffic Managerrel, az Azure-ral, vegye figyelembe a következőket:

* Csak alkalmazástelepítések az azonos régión belüli, az App Service-ben is biztosít feladatátvételi és ciklikus időszeletelési funkció során mód függetlenül.
* A környezeteknek ugyanabban a régióban, amelyek az App Service egy másik Azure-felhőszolgáltatásban együtt kombinálhatja mindkét típusú végpontjait a hibrid forgatókönyvek megvalósítását teszik lehetővé.
* Csak egy App Service-végpont régiónként adhat egy profilt. Amikor kiválaszt egy alkalmazást egy adott régióban-végpontként, a fennmaradó alkalmazásokat az adott régióban elérhetetlenné válik, hogy a profil kiválasztásához.
* Az App Service-ben végpontokat, amelyeket egy Azure Traffic Manager-profil megjelenik a **tartománynevek** szakaszban az alkalmazás a profil, de ez nem konfigurálható van a konfigurálás oldalon látható.
* Miután hozzáadta az alkalmazást a profilhoz a **webhely URL-címe** az irányítópulton, az alkalmazás portáloldalán jelenít meg az az egyéni tartomány URL-címét, ha az egyik beállítását. Ellenkező esetben a Traffic Manager-profil URL-CÍMÉT jeleníti meg (például `contoso.trafficmanager.net`). Mindkét közvetlen tartomány nevét az alkalmazás és a Traffic Manager URL-cím alatt az alkalmazás konfigurálása lapon láthatók a **tartománynevek** szakaszban.
* Az egyéni tartománynevek működik az elvárásoknak megfelelően, de az alkalmazásokhoz hozzáadásán, is konfigurálnia kell a DNS-térkép átirányítása a Traffic Manager URL-címe. App Service-alkalmazások egyéni tartomány beállítása a további információkért lásd: [meglévő egyéni DNS-név leképezése az Azure Web Apps](app-service-web-tutorial-custom-domain.md).
* Csak vehet fel alkalmazásokat, amelyeket a standard vagy prémium szintű mód használata egy Azure Traffic Manager-profilt.

## <a name="next-steps"></a>További lépések
Egy elméleti és technikai áttekintése az Azure Traffic Manager: [a Traffic Manager áttekintése](../traffic-manager/traffic-manager-overview.md).

Az App Service-ben a Traffic Manager használatával kapcsolatos további információkért tekintse meg a blogbejegyzések [Azure Traffic Manager használata az Azure-webhelyek](https://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) és [Azure Traffic Manager mostantól integrálhatja az Azure webhelyek szolgáltatással](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

