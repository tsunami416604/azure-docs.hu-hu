---
title: Fogalmak – API Management
description: Ismerje meg, hogyan védi API Management az Azure VMware Solution (AVS) virtuális gépeken (VM) futó API-kat
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 62112bf3c0bf551232e09e5910e3eaae228dc202
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85306908"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-avs-based-vms"></a>Az AVS-alapú virtuális gépeken futó API-k közzétételének és védelemének API Management

Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) lehetővé teszi, hogy a fejlesztők és a DevOps-csapatok biztonságosan közzétegyék a belső vagy külső felhasználók számára.

Bár a több SKU-ban is elérhető, csak a fejlesztői és prémium SKU-k teszik lehetővé az Azure Virtual Network integrációját az Azure VMware Solution (AVS) munkaterheléseken futó API-k közzétételéhez. Ez a két SKU biztonságosan engedélyezi API Management szolgáltatás és a háttér közötti kapcsolatot. A fejlesztői SKU fejlesztésre és tesztelésre szolgál, míg a prémium SKU éles környezetben üzemel.

Az AVS-alapú virtuális gépeken (VM-EK) futó háttér-szolgáltatások esetében a API Management alapértelmezett konfigurációja alapértelmezés szerint megegyezik a helyszíni háttér-szolgáltatásokkal. A belső és külső központi telepítések esetében API Management konfigurálja a terheléselosztó virtuális IP-címét (VIP) a háttér-végpontként, ha a háttér-kiszolgáló egy NSX-Load Balancer mögé kerül az AVS oldalon.

## <a name="external-deployment"></a>Külső üzembe helyezés

Egy külső telepítés közzéteszi a külső felhasználók által a nyilvános végpont használatával felhasznált API-kat. A fejlesztők és a DevOps mérnökök a Azure Portal vagy a PowerShell segítségével kezelhetik az API-kat, valamint a API Management fejlesztői portált.

A külső telepítési diagramon a teljes folyamat és az érintett szereplők láthatók (fent láthatók). A szereplők a következők:

- **Rendszergazda (k):** A rendszergazda vagy a DevOps csapatot jelöli, amely az AVS-t az Azure Portal-és Automation-mechanizmusok, például a PowerShell vagy az Azure DevOps segítségével kezeli.

- **Felhasználók:**  A kitett API-k fogyasztóit jelöli, és az API-kat használó felhasználókat és szolgáltatásokat képviseli.

A forgalom áthalad API Management-példányon, amely elkerüli a háttér-szolgáltatásokat, és csatlakoztatva van a hub Virtual Network szolgáltatáshoz. A ExpressRoute-átjáró átirányítja a forgalmat a ExpressRoute Global Reach csatornára, és elér egy NSX Load Balancer a bejövő forgalmat a különböző háttér-szolgáltatási példányokra terjeszti.

API Management rendelkezik Azure nyilvános API-val, és az Azure DDOS Protection szolgáltatás aktiválása ajánlott. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Külső üzembe helyezés – AVS-API Management":::


## <a name="internal-deployment"></a>Belső telepítés

A belső telepítés a belső felhasználók vagy rendszerek által felhasznált API-kat teszi közzé. A DevOps-csapat és az API-fejlesztők ugyanazokat a felügyeleti eszközöket és fejlesztői portált használják, mint a külső telepítésben.

A belső telepítések az [Azure Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) segítségével létrehozhatnak egy nyilvános és biztonságos végpontot az API-hoz, amely lehetővé teszi a képességeinek kihasználását és a különböző forgatókönyveket használó hibrid központi telepítések létrehozását.  Az API kihasználja a képességeit, és létrehoz egy hibrid üzembe helyezést, amely különböző forgatókönyveket tesz lehetővé.

* Ugyanazt a API Management erőforrást használja a belső és külső felhasználók általi felhasználáshoz.

* Egyetlen API Management erőforrással rendelkezik, és a külső fogyasztók számára elérhető API-k egy részhalmazát kell megadni.

* A nyilvános internetről be-és kikapcsolhatja a API Management hozzáférésének egyszerű módját.

Az alábbi üzembe helyezési ábrán láthatók a belső és a külső felhasználók, valamint az azonos vagy a különböző API-khoz hozzáférő típusok.

Belső telepítés esetén az API-k ugyanazon API Management-példányon lesznek elérhetők. A API Management előtt Application Gateway az Azure webalkalmazási tűzfal (WAF) funkcióját aktiválta, valamint a HTTP-figyelő (k) készletét és a forgalom szűrésére szolgáló szabályokat, és csak az AVS-n futó háttér-szolgáltatások egy részhalmazát teszi elérhetővé.

* A belső forgalom a ExpressRoute-átjárón keresztül lesz átirányítva, hogy Azure Firewall, majd API Management, ha forgalmi szabályok vannak kialakítva, vagy közvetlenül API Management.  

* A külső forgalom az Azure-ba Application Gatewayon keresztül lép be, amely a API Management külső védelmi rétegét használja.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Belső üzembe helyezés – API Management az AVS-hez":::