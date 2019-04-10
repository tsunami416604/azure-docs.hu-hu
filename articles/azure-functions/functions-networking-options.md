---
title: Az Azure Functions hálózati beállításai
description: Az Azure Functions szolgáltatásban elérhető összes hálózati lehetőségeinek áttekintése
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: conceptual
ms.date: 1/14/2019
ms.author: alkarche
ms.openlocfilehash: 10d7daa6da45c56e20c622fcbca9ee288e737dab
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358154"
---
# <a name="azure-functions-networking-options"></a>Az Azure Functions hálózati beállításai

Ez a dokumentum ismerteti a csomag hálózatkezelési funkcióit az Azure Functions, szolgáltatási beállítások között. Minden, a következő hálózati beállításokat adja meg a bizonyos erőforrások eléréséhez az internetes irányítható címek használata nélkül, vagy internet-hozzáférés korlátozása egy függvényalkalmazáshoz lehetősége. Az összes, a üzemeltetési modellek rendelkezik különböző szintű hálózati elkülönítés érhető el, és megfelelő kiválasztása lehetővé teszi, hogy a hálózati elkülönítés követelményeinek.

Függvényalkalmazások számos különböző módon lehet üzemeltetni.

* Nincsenek több különböző szintjeit VNET-kapcsolatot és méretezési lehetőségeket több-bérlős infrastruktúrában futó tervet beállítás.
    1. A Használatalapú csomag, amely betölteni a válaszban dinamikusan méretezhető, és minimális hálózati elkülönítési lehetőséget kínál az.
    1. A prémium szintű tervezze meg, amely átfogóbb hálózatelkülönítés garantál dinamikusan is méretezhető.
    1. Az App Service-csomagot, amely a rögzített méretű, működik, és a prémium szintű csomaggal hasonló hálózati elkülönítés kínálja.
* Funkciók is futtathatja a egy App Service Environment (ASE) üzembe helyezi a függvény azokat a virtuális hálózat és a teljes hálózati vezérlő és elszigeteltséget biztosít.

## <a name="networking-feature-matrix"></a>Hálózati szolgáltatás mátrix

|                |[Használatalapú csomag](functions-scale.md#consumption-plan)|⚠ [Premium Plan](functions-scale.md##premium-plan-public-preview)|[App Service-csomag](functions-scale.md#app-service-plan)|[App Service-környezet](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[**Bejövő IP-korlátozások**](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[**Virtuálishálózat-integráció**](#vnet-integration)|❌No|⚠ igen|✅Yes|✅Yes|
|[**Előzetes verzió VNET-integráció (Express Route & Szolgáltatásvégpontok)**](#preview-vnet-integration)|❌No|⚠ igen|⚠ igen|✅Yes|
|[**Hibrid kapcsolatok**](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[**Private Site Access**](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ Előzetes verziójú funkció, nem éles környezetben való használatra

## <a name="inbound-ip-restrictions"></a>Bejövő IP-korlátozások

Az IP-korlátozások lehetővé teszik a rendezett lista engedélyezni vagy letiltani az IP-címek, amelyek jogosultak hozzáférni az alkalmazáshoz prioritást határozza meg. Az engedélyezési listán lehetnek IPv4 és IPv6 típusú címek. Ha egy vagy több bejegyzés, nincs, akkor egy implicit összes elutasítása, hogy a lista végén létezik. Az IP-korlátozások funkció minden, a üzemeltetési lehetőségeinek funkció működik.

> [!NOTE]
> Az, hogy az Azure portal szerkesztővel, a portálon közvetlenül elérni a futó függvényalkalmazást képesnek kell lennie, és az Ön által használt eszköz hozzáférhet a portálhoz rendelkeznie kell az IP-engedélyezési listán. A korlátozásait a hálózati helyen, továbbra is hozzáférhet a szolgáltatásokat a a **platformfunkciók** fülre.

[További információk itt](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)

## <a name="vnet-integration"></a>Virtuálishálózat-integráció

VNET-integráció lehetővé teszi, hogy a függvényalkalmazás, egy virtuális hálózaton belül erőforrások eléréséhez. VNET-integráció az prémium szintű csomag és az App Service-csomagban érhető el. Ha az alkalmazás az App Service-környezet, már van egy virtuális hálózathoz, és nincs szükség a VNet-integráció funkció az azonos virtuális hálózatban található erőforrások eléréséhez.

VNet-integráció a függvény alkalmazás hozzáférést biztosít a virtuális hálózatban lévő erőforrásokra irányuló, de nem biztosíthat [privát hozzáférést](#private-site-access) a virtuális hálózatról a függvényalkalmazáshoz.

VNet-integráció-adatbázisok az alkalmazások elérésének lehetővé tétele és a virtuális hálózatban futó webszolgáltatások gyakran használják. A VNet-integráció nem kell egy nyilvános végpontot elérhetővé az alkalmazások is, de a gép nem internetes irányítható Magáncímeket helyette használja.

A VNET-integráció általánosan elérhető verziója a Függvényalkalmazások kapcsolódni egy virtuális hálózat VPN-átjáró támaszkodik. App service-csomagban üzemeltetett funkciók érhető el. Ez a szolgáltatás konfigurálásával kapcsolatban lásd: a [ugyanazokat a funkciókat az App Service-ben a dokumentum](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-vnet-integration"></a>VNET-integráció előzetes verzió

Van egy új verziója, a VNet-integráció szolgáltatás, amelyet az előzetes verzióban érhető el. Ez nem függ a pont – hely VPN és erőforrások elérésére is támogatja az expressroute-on vagy a végpontok közötti. Ez a funkció a prémium szintű csomag, és az App Service-csomagok PremiumV2 skálázható érhető el.

VNet-integráció, amely jelenleg előzetes verzióban érhető el, az új verzió a következő előnyöket nyújtja:

* Nincs átjárót kell használnia az új virtuális hálózat integrációja
* Az ExpressRoute-kapcsolatok között hozzáférhetnek erőforrásokhoz, az ExpressRoute integrálása mellett további konfiguráció csatlakoztatott virtuális hálózat nélkül.
* Védett erőforrásokhoz függvények futtatását szolgáltatásvégpont használhatja fel. Ehhez engedélyezze a szolgáltatásvégpontokat a virtuális hálózatok közötti integráció használt alhálózat.
* Védett erőforrásokhoz az új VNet-integráció funkcióval szolgáltatásvégpont használandó eseményindítók nem lehet konfigurálni. 
* A függvényalkalmazás, mind a virtuális hálózat ugyanabban a régióban kell lennie.
* Az új szolgáltatáshoz a Resource Manager virtuális hálózatban található egy nem használt alhálózat.
* Éles számítási feladatok nem támogatottak a VNet-integráció új verziója, amíg a szolgáltatás előzetes verzióként.
* Útválasztási táblázatokat és globális társviszony-létesítés még nem érhető el az új VNet-integráció.
* Minden lehetséges függvény példányt egy cím használható. Alhálózat méretét a hozzárendelés után nem módosítható, mert könnyen által támogatott maximális skálázási méret alhálózatot használjon. Ha például egy prémium szintű csomag, amely a 80-példány támogatása érdekében javasoljuk, hogy egy `/25` alhálózatot, amelyet a gazdagép 126 címet biztosít.

VNET-integráció előzetes használatával kapcsolatos további tudnivalókért lásd: [függvényalkalmazás integrálása az Azure Virtual Network](functions-create-vnet.md).

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

[Hibrid kapcsolatok](../service-bus-relay/relay-hybrid-connections-protocol.md) egy olyan egyéb hálózatok az alkalmazás-erőforrások eléréséhez használható az Azure Relay szolgáltatás. Egy alkalmazás végpontnak az alkalmazásból való elérést biztosít. Az alkalmazás elérése csak akkor használható. Hibrid kapcsolatok futó függvények rendelkezésére áll egy [App Service-csomag](functions-scale.md#app-service-plan) és a egy [App Service Environment-környezet](../app-service/environment/intro.md).

A függvények használ, egyetlen TCP-állomás és port kombinációja utal. minden hibrid kapcsolat. Ez azt jelenti, hogy a hibrid kapcsolat végpontja is lehet bármely operációs rendszerben, és minden alkalmazás, feltéve, hogy érnek el egy figyelő TCP-portot. Hibrid kapcsolatok szolgáltatását nem ismert, és gondoskodik a protokoll van, vagy amit Ön hozzáfér. Egyszerűen csak a számítógép hálózati hozzáférést.

További tudnivalókért lásd a [App Service – dokumentáció a hibrid kapcsolatokhoz](../app-service/app-service-hybrid-connections.md), amely támogatja a funkciók és a Web Apps.

## <a name="private-site-access"></a>Private Site Access

Privát hozzáférést utal, hogy az alkalmazás csak elérhetőnek magánhálózaton például az Azure virtuális hálózatban. Privát hozzáférést csak akkor használható a konfigurált egy belső Load Balancer (ILB) rendelkező ASE. Az ILB ASE használatával kapcsolatos részletekért lásd: [létrehozása és a egy ILB ASE használatával](../app-service/environment/create-ilb-ase.md).

Számos módon más üzemeltetési lehetőségek a VNET-erőforrások eléréséhez, de az ASE az egyetlen módja, hogy az eseményindítók a egy függvényt, amely egy VNET-en keresztül történik.
