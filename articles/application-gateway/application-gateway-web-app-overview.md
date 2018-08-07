---
title: A több-bérlős áttekintése az Azure Application Gateway végződik.
description: Ez az oldal áttekintést nyújt az Application Gateway több-bérlős háttérrendszerekhez elérhető támogatásáról.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/1/2018
ms.author: victorh
ms.openlocfilehash: c0084580a2e4860f24aecd37232f38da2e55ccc8
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578432"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>Az Application Gateway támogatása több-bérlős háttérrendszerekhez

Az Azure Application Gateway támogatja a virtuálisgép-méretezési csoportok, hálózati adapterek, nyilvános vagy magánhálózati IP- vagy teljes tartománynév (FQDN) nevet a háttérkészlet részeként. Alapértelmezés szerint az Application Gateway nem módosítja az ügyféltől származó bejövő HTTP-állomásfejlécet, és így küldi tovább a háttérrendszernek. Számos olyan szolgáltatás létezik, mint az [Azure Web Apps](../app-service/app-service-web-overview.md), amely eredendően több-bérlős szolgáltatás, és a megfelelő végpontra való feloldáshoz egy adott állomásfejlécet vagy SNI-bővítményt használ. Application Gateway mostantól támogatja a felhasználók felülírja a bejövő HTTP-állomásfejlécet a háttér-HTTP-beállításai alapján. Ez a képesség teszi lehetővé a több-bérlős háttérrendszerek Azure Web Apps és API Management szolgáltatásának támogatását. Ez a képesség a standard és a WAF termékváltozat esetében is elérhető. Több-bérlős háttérszolgáltatások támogatása SSL-lezárások és teljes körű SSL-forgatókönyvek esetében is működik.

> [!NOTE]
> Hitelesítési tanúsítvány beállítása, nem szükséges, például az Azure Web Apps megbízható Azure-szolgáltatásokhoz.

![webalkalmazás-forgatókönyv](./media/application-gateway-web-app-overview/scenario.png)

A gazdagép-felülbírálás megadásának lehetőségét a HTTP-Settings van definiálva, és minden olyan háttérkészlet-szabály létrehozása során alkalmazhatók. A több-bérlős háttérrendszerek az állomásfejlécek és az SNI-bővítmények alábbi két felülbírálási módját támogatják.

1. A gazdanév rögzített értékre történő beállítása a HTTP-beállítások megadása során. Ez a képesség biztosítja, hogy az állomásfejléc az összes forgalmat a háttérbeli készletet, ahol a HTTP-beállítások alkalmazása erre az értékre legyen felülírva. Végpontok közötti SSL alkalmazása esetén az SNI-bővítményben a rendszer a felülírt gazdanevet használja. Ez a funkció lehetővé teszi, hogy a forgatókönyvek, ahol a háttérkészlet-farmban a bejövő ügyfél állomásfejlécétől eltérő állomásfejlécet vár.

2. Lehetővé teszi az IP vagy FQDN a háttérkészlet-tagok az állomásnév származtassa. HTTP-beállítások megadása során az állomás neve választhatok egy háttér-háttérkészlettag teljes tartománynevéből, ha az állomásnév származtassa egy egyéni háttér-készlettag beállítással konfigurált. Végpontok közötti SSL alkalmazása esetén az SNI-bővítményben a rendszer a teljes tartománynévből származtatott gazdanevet használja. Ez a funkció lehetővé teszi, hogy a forgatókönyvek, ahol a háttérkészlet rendelkezhet kettő vagy több több-bérlős PaaS szolgáltatásokhoz hasonlóan az Azure web apps és a kérelem állomásfejléc minden egyes tagjára tartalmazza a teljes tartománynévből származtatott.

> [!NOTE]
> Mindkét előző esetben a beállítások csak az élő adatforgalomra vannak hatással, az állapotminta viselkedésére nem. Az egyéni minták már korábban is támogatták az állomásfejléc megadásának lehetőségét a mintavételi konfigurációban. Az egyéni minták mostantól támogatják az állomásfejléc viselkedésének az aktuálisan konfigurált HTTP-beállításokból történő származtatását is. Ezt a konfigurációt a mintavételi konfigurációban a `PickHostNameFromBackendHttpSettings` paraméterrel is megadhatjuk. A végpontok közötti funkciók működéséhez a mintavételi és a HTTP-beállításokat is úgy kell módosítani, hogy azok a megfelelő konfigurációt tükrözzék.

Ezzel a képességgel az ügyfelek megadhatják a HTTP-beállítások és az egyéni mintavételek beállítási lehetőségeit a megfelelő konfigurációhoz. Ez a beállítás majd vannak kötve egy figyelőt, és a egy háttérkészlet-szabály segítségével.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan állítsa be egy webes alkalmazást az application gateway háttérkészlet tagjaként funkcionáló: [az App Service web apps Application Gatewayjel](application-gateway-web-app-powershell.md)
