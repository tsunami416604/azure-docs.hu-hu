---
title: Forgalom szabályozása Traffic Manager-Azure App Service
description: Ez a cikk az Azure-Traffic Manager összefoglaló információit tartalmazza, mivel Azure App Servicehoz kapcsolódik.
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
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2cd53c3c0b3e9acbb8a5a88c7696e8b3089c50c9
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483378"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Azure App Service forgalom szabályozása az Azure-Traffic Manager
> [!NOTE]
> Ez a cikk összefoglalja a Microsoft Azure Traffic Manager kapcsolatos összefoglaló információkat, mivel azok Azure App Servicehoz kapcsolódnak. A jelen cikk végén található hivatkozásokra kattintva további információt találhat az Azure Traffic Managerról.
> 
> 

## <a name="introduction"></a>Bevezetés
Az Azure Traffic Manager segítségével szabályozhatja, hogy a webügyfelektől érkező kérések hogyan legyenek kiosztva a Azure App Serviceban lévő alkalmazásokhoz. Amikor App Service-végpontokat ad egy Azure Traffic Manager-profilhoz, az Azure Traffic Manager nyomon követi az App Service-alkalmazások állapotát (fut, leállítva vagy törölve), így eldöntheti, hogy melyik végpont kapja a forgalmat.

## <a name="routing-methods"></a>Útválasztási módszerek
Az Azure Traffic Manager négy különböző útválasztási módszert használ. Ezek a módszerek a következő listában vannak felsorolva, mivel azok Azure App Servicera vonatkoznak.

* **[Prioritás](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method):** használjon egy elsődleges alkalmazást az összes forgalomhoz, és adja meg a biztonsági mentéseket abban az esetben, ha az elsődleges vagy a biztonsági mentési alkalmazások nem érhetők el.
* **[Súlyozott](../traffic-manager/traffic-manager-routing-methods.md#weighted):** a forgalom elosztása az alkalmazások egy csoportján belül, akár egyenletesen, akár súlyok szerint, amelyet Ön határoz meg.
* **[Teljesítmény](../traffic-manager/traffic-manager-routing-methods.md#performance):** ha különböző földrajzi helyekről származó alkalmazásokkal rendelkezik, a legalacsonyabb hálózati késés szempontjából használja a "legközelebbi" alkalmazást.
* **[Földrajzi](../traffic-manager/traffic-manager-routing-methods.md#geographic):** azok a felhasználók, amelyek alapján a DNS-lekérdezésből származnak, adott alkalmazásokhoz irányítják a felhasználókat. 

További információ: [Traffic Manager útválasztási módszerek](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>App Service és Traffic Manager profilok
App Service-alkalmazás forgalmának szabályozásához létre kell hoznia egy profilt az Azure Traffic Managerban, amely a korábban ismertetett négy terheléselosztási módszer egyikét használja, majd hozzáadja a végpontokat (ebben az esetben App Service), amelynek a forgalmát szabályozni szeretné Profil. Az alkalmazás állapota (futó, leállított vagy törölt) rendszeresen kommunikál a profillal, hogy az Azure Traffic Manager ennek megfelelően irányítsa a forgalmat.

Az Azure Traffic Manager az Azure-ban való használatakor vegye figyelembe a következő szempontokat:

* Az alkalmazások csak az adott régión belül üzemelő példányok esetében App Service a feladatátvételt és a ciklikus multiplexelés funkciót az alkalmazás módjától függetlenül.
* Ha ugyanabban a régióban üzemelő példányok, amelyek egy másik Azure Cloud Service-sel együtt használják App Service, akkor mindkét típusú végpontot kombinálhatja a hibrid forgatókönyvek lehetővé tételéhez.
* A profilokban csak egy App Service végpont adható meg régiónként. Ha kijelöl egy alkalmazást az egyik régióhoz tartozó végpontként, akkor az adott régióban található többi alkalmazás nem lesz elérhető a profil kiválasztásához.
* Az Azure Traffic Manager profilban megadott App Service-végpontok a profilban az alkalmazás konfigurálás lapjának **tartománynevek** szakaszában jelennek meg, de itt nem konfigurálható.
* Miután hozzáadta az alkalmazást egy profilhoz, a **webhely URL-** címe az alkalmazás portáljának irányítópultján az alkalmazás egyéni tartományának URL-címe jelenik meg, ha be van állítva. Ellenkező esetben a Traffic Manager profil URL-címét (például `contoso.trafficmanager.net`) jeleníti meg. Mind az alkalmazás közvetlen tartományneve, mind a Traffic Manager URL-cím látható az alkalmazás konfigurálás lapján a **tartománynevek** szakaszban.
* Az egyéni tartománynevek a várt módon működnek, de az alkalmazásokhoz való hozzáadásuk mellett konfigurálnia kell a DNS-térképet is, hogy az Traffic Manager URL-re mutasson. Egy App Service alkalmazás egyéni tartományának beállításával kapcsolatos információkért lásd: [meglévő egyéni DNS-név leképezése Azure app Service](app-service-web-tutorial-custom-domain.md).
* Csak standard vagy prémium módban lévő alkalmazásokat adhat hozzá egy Azure Traffic Manager-profilhoz.

## <a name="next-steps"></a>További lépések
Az Azure Traffic Manager fogalmi és technikai áttekintését lásd: [Traffic Manager áttekintése](../traffic-manager/traffic-manager-overview.md).


