---
title: A több-bérlős áttekintése az Azure Application Gateway végződik.
description: Ez az oldal áttekintést nyújt az Application Gateway több-bérlős háttérrendszerekhez elérhető támogatásáról.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/1/2018
ms.author: victorh
ms.openlocfilehash: 8e5f48e42a2a677622dae0b733f9d5af484d1c51
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605806"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>Az Application Gateway támogatása több-bérlős háttérrendszerekhez

Az Azure Application Gateway támogatja a virtuálisgép-méretezési csoportok, hálózati adapterek, nyilvános vagy magánhálózati IP- vagy teljes tartománynév (FQDN) nevet a háttérkészlet részeként. Alapértelmezés szerint az Application Gateway nem módosítja az ügyféltől származó bejövő HTTP-állomásfejlécet, és így küldi tovább a háttérrendszernek. Például számos szolgáltatást [Azure App Service](../app-service/overview.md) , amely több-bérlős és egy adott állomásfejlécet vagy SNI-bővítményt a megfelelő végpontra való feloldáshoz támaszkodnak. Application Gateway mostantól támogatja a felhasználók felülírja a bejövő HTTP-állomásfejlécet a háttér-HTTP-beállításai alapján. Ez a képesség teszi lehetővé a több-bérlős háttérrendszerek Azure Web Apps és API Management szolgáltatásának támogatását. Ez a képesség a standard és a WAF termékváltozat esetében is elérhető. Több-bérlős háttérszolgáltatások támogatása SSL-lezárások és teljes körű SSL-forgatókönyvek esetében is működik.

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

Ismerje meg, hogyan állítható be egy webes alkalmazást az application gateway háttérkészlet tagjaként által felkeresése: [Az App Service web Apps-alkalmazások konfigurálása az Application Gatewayen](application-gateway-web-app-powershell.md)
