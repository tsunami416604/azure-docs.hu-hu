---
title: Több-bérlős háttérrendszerek, például az Azure App service-ben az Azure Application Gateway áttekintése
description: Ez az oldal áttekintést nyújt az Application Gateway több-bérlős háttérrendszerekhez elérhető támogatásáról.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 8434340bb7ed95cc36115c05048b2b67682b5796
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224498"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Application Gateway támogatása több-bérlős ér véget, például az App Service-ben

A több-bérlős architektúra tervek a webkiszolgálók több webhelyet is futtat a web server példányt. Állomásnevek megkülönböztetni a különböző alkalmazások futnak, amelyek használhatók. Alapértelmezés szerint az Application Gateway nem módosítja az ügyféltől származó bejövő HTTP-állomásfejlécet, és így küldi tovább a háttérrendszernek. A háttérkészlet érintett tagjai például a hálózati adapterek esetében megfelelően működik, virtuális gép méretezési csoportok, nyilvános IP-címek, belső IP-címek és a teljes tartománynév, ezek ne támaszkodjon kizárólag egy adott állomásfejlécet vagy SNI-bővítményt a megfelelő végpontra való feloldáshoz. Vannak azonban, például az Azure App service web apps szolgáltatásban, és az Azure API management számos olyan szolgáltatás, amely több-bérlős és a egy adott állomásfejlécet vagy SNI-bővítményt a megfelelő végpontra való feloldáshoz támaszkodnak. Általában az alkalmazás, amely pedig a társított az application gateway DNS-név, DNS-neve eltér a a háttérszolgáltatás tartomány nevét. Ezért az eredeti kérést, az application gateway által fogadott szereplő állomás fejlécével nem ugyanaz, mint a háttérszolgáltatás állomásneve. Éppen ezért, ha a háttérszolgáltatáshoz állomásneve változott a kérelmet az alkalmazásátjáróról, a háttérkiszolgáló szereplő állomás fejlécével a több-bérlős háttérrendszerek kihasználására nem képes a kérést a megfelelő végpontra való feloldáshoz. 

Application gateway egy olyan funkció, amely lehetővé teszi a felhasználóknak a kérelem alapján a háttér-állomás nevét a HTTP állomásfejléc felülbírálása biztosít. Ez a funkció lehetővé teszi, hogy például az Azure App service web apps szolgáltatásban, és az API management több-bérlős háttérrendszerek támogatása. Ez a funkció a v1 és v2 standard és a WAF termékváltozatban érhető el. 

![Gazdagép felülbírálása](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Ez a nem alkalmazható az Azure App service Environment-környezet (ASE) óta az ASE nem egy dedikált erőforrás eltérően az Azure App Service-ben, amely egy több-bérlős erőforrás.

## <a name="override-host-header-in-the-request"></a>A kérelemben szereplő állomás fejlécével felülbírálása

A gazdagép-felülbírálás megadásának lehetőségét van definiálva a [HTTP-beállítások](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) és minden olyan háttérkészlet-szabály létrehozása során alkalmazhatók. Az alábbi két módon állomásfejléc és SNI-bővítményt a több-bérlős háttérrendszerek elsőrendű támogatják:

- A gazdagép nevét a HTTP-beállítások explicit módon megadott rögzített értékre állítva lehetővé teszi. Ez a képesség biztosítja, hogy az állomásfejléc minden forgalmat a háttérbeli készletet, amelyen az adott HTTP-beállítások érvényesek a következő értékre legyen felülírva. Végpontok közötti SSL alkalmazása esetén az SNI-bővítményben a rendszer a felülírt gazdanevet használja. Ez a funkció lehetővé teszi, hogy a forgatókönyvek, ahol a háttérkészlet-farmban a bejövő ügyfél állomásfejlécétől eltérő állomásfejlécet vár.

- Lehetővé teszi az IP vagy FQDN a háttérkészlet-tagok az állomásnév származtassa. HTTP-beállítások megadása során dinamikusan választhatok a gazdagép nevét egy háttér-háttérkészlettag teljes tartománynevéből Ha állomásnév célosztályából származik az egyes háttérkészlet egyik tagja lehetőséggel konfigurált. Végpontok közötti SSL alkalmazása esetén az SNI-bővítményben a rendszer a teljes tartománynévből származtatott gazdanevet használja. Ez a funkció lehetővé teszi, hogy a forgatókönyvek, ahol a háttérkészlet rendelkezhet kettő vagy több több-bérlős PaaS szolgáltatásokhoz hasonlóan az Azure web apps és a kérelem állomásfejléc minden egyes tagjára tartalmazza a teljes tartománynévből származtatott. Ez a forgatókönyv végrehajtásához egy kapcsoló nevű HTTP-beállítások a használjuk [válasszon címet gazdanévre](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-backend-address) dinamikusan felülírják, amely az eredeti kérést egy háttérkészlet említett szereplő állomás fejlécével.  Például ha a háttérkészlet FQDN "contoso11.azurewebsites.net" és "contoso22.azurewebsites.net", az eredeti kérést állomásfejlécet, amelyek a contoso.com felül lesz bírálva contoso11.azurewebsites.net vagy contoso22.azurewebsites.net Ha a kérelem érkezik a megfelelő háttérkiszolgálóra. 

  ![webalkalmazás-forgatókönyv](./media/application-gateway-web-app-overview/scenario.png)

Ezzel a képességgel az ügyfelek megadhatják a HTTP-beállítások és az egyéni mintavételek beállítási lehetőségeit a megfelelő konfigurációhoz. Ez a beállítás majd vannak kötve egy figyelőt, és a egy háttérkészlet-szabály segítségével.

## <a name="special-considerations"></a>Különleges szempontok

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>SSL-lezárások és teljes körű SSL több-bérlős szolgáltatások

Több-bérlős szolgáltatások SSL-lezárások és teljes körű SSL-titkosítást is támogatja. Az SSL-lezárást az application gatewayben SSL-tanúsítvány továbbra is hozzá kell adni az application gateway figyelővel. Azonban teljes körű SSL-t, esetén megbízható Azure-szolgáltatások, mint például az Azure App service web apps szolgáltatásban nem igénylik a háttérrendszereket az Alkalmazásátjáró engedélyezési. Így hiba esetén nem kell minden olyan hitelesítési tanúsítványok hozzáadása. 

![teljes körű SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Figyelje meg, hogy a fenti képen, nem szükséges, adja hozzá a hitelesítési tanúsítványok, ha az App service háttérrendszer van kiválasztva.

### <a name="health-probe"></a>Állapotadat-mintavétel

Szereplő állomás fejlécével felülbírálása a **HTTP-beállítások** csak hatással van a kérelmet, és az útválasztást. Ez nincs hatással az állapotminta viselkedésére. A végpontok közötti funkciók működéséhez a mintavételi és a HTTP-beállításokat is úgy kell módosítani, hogy azok a megfelelő konfigurációt tükrözzék. Amellett, hogy a mintavételi konfigurációban állomásfejléc megadásának lehetőségét, az egyéni minták a jelenleg konfigurált HTTP-beállítások az állomásfejléc származtassa képességét is támogassa. Ezt a konfigurációt a mintavételi konfigurációban a `PickHostNameFromBackendHttpSettings` paraméterrel is megadhatjuk.

### <a name="redirection-to-app-services-url-scenario"></a>Átirányítási URL-cím-forgatókönyvet az App Service

Forgatókönyvek, ahol az állomásnév az App service-ből a válaszban utasíthatja a végfelhasználói böngészőben is lehet a *. azurewebsites.net állomásnév helyett az Application Gateway társított tartomány. Ez a hiba fordulhat elő, ha:

- Az App Service-ben konfigurált átirányítás rendelkezik. Lehet, hogy átirányítás szúrni egy záró perjellel a kérelemre.
- Azure AD-hitelesítés, amely azt eredményezi, az átirányítás rendelkezik.

Ezekben az esetekben megoldásához olvassa el [átirányítás, az App service URL-címet a probléma elhárításához](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan állítsa be például az Azure App service webalkalmazás egy több-bérlős alkalmazást az application gateway háttérkészlet tagjaként funkcionáló [az App Service web apps Application Gatewayjel](https://docs.microsoft.com/azure/application-gateway/create-web-app)
