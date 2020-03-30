---
title: Több-bérlős háttérrendszerek
titleSuffix: Azure Application Gateway
description: Ez az oldal áttekintést nyújt az Application Gateway több-bérlős háttérrendszerekhez elérhető támogatásáról.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: efa2885ce0534c5d78bb08bbf24da59850f6ea22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075178"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Az Application Gateway támogatása több-bérlős háttérrendszerek, például az App Service

A webkiszolgálók több-bérlős architekturális terveiben több webhely fut ugyanazon a webkiszolgáló-példányon. Az állomásnevek a különböző üzemeltetett alkalmazások megkülönböztetésére szolgálnak. Alapértelmezés szerint az Application Gateway nem módosítja az ügyféltől származó bejövő HTTP-állomásfejlécet, és így küldi tovább a háttérrendszernek. Ez jól működik a háttérkészlet-tagok, például hálózati adapterek, virtuálisgép-méretezési készletek, nyilvános IP-címek, belső IP-címek és FQDN esetén, mivel ezek nem támaszkodnak egy adott gazdagépfejlécre vagy SNI-bővítményre a megfelelő végpont feloldásához. Azonban számos olyan szolgáltatás létezik, például az Azure App service webalkalmazások és az Azure API-kezelés, amelyek több-bérlős jellegű, és támaszkodnak egy adott gazdagép fejléc vagy SNI-bővítmény a megfelelő végpont feloldásához. Általában az alkalmazás DNS-neve, amely viszont az alkalmazásátjáróhoz társított DNS-név, eltér a háttérszolgáltatás tartománynevétől. Ezért az alkalmazásátjáró által fogadott eredeti kérelem állomásfejléce nem egyezik meg a háttérszolgáltatás állomásnevével. Emiatt, kivéve, ha az alkalmazás átjáróból a háttérrendszerbe küldött kérelem gazdagépfejléce a háttérszolgáltatás gazdagépnevére változik, a több-bérlős háttérrendszerek nem tudják a kérést a megfelelő végpontra feloldani. 

Az Application Gatewayben elérhető egy olyan funkció, amely lehetővé teszi a felhasználóknak a HTTP-kiszolgálófejléc felülbírálását a kérelmekben a háttérrendszer gazdaneve alapján. Ez biztosítja a több-bérlős háttérrendszerek, mint az Azure App Service Web Apps és az API Management támogatását. A funkció a v1 és v2 standard, valamint a WAF termékváltozat esetében is elérhető. 

![állomás felülbírálása](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Ez nem alkalmazható az Azure App service-környezet (ASE) számára, mivel az ASE egy dedikált erőforrás, ellentétben az Azure App szolgáltatással, amely egy több-bérlős erőforrás.

## <a name="override-host-header-in-the-request"></a>Állomásfejléc felülbírálása a kérelemben

Az állomásfelülbírálás megadása a [HTTP-beállításokban](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) van definiálva, és a szabály létrehozása során bármely háttérkészletre alkalmazható. A következő két módszer támogatja az állomásfejléc és az SNI-bővítmény több-bérlős háttérrendszerek esetén történő felülbírálását:

- Az állomásnév beállítása a HTTP-beállításokban kifejezetten megadott rögzített értékre. Ez a képesség biztosítja, hogy az állomásfejléc felüllegyen ezzel az értékkel a háttérkészletminden olyan forgalmában, ahol az adott HTTP-beállítások at alkalmazzák. Végpontok közötti SSL alkalmazása esetén az SNI-bővítményben a rendszer a felülírt gazdanevet használja. Ez a funkció olyan eseteket tesz lehetővé, amelyekben a háttérkészlet-farm a bejövő ügyfélállomás-fejléctől eltérő gazdagépfejlécet vár.

- Az állomásnév származtatása a háttérkészlet-tagok IP-címéből vagy teljes tartománynevéből. A HTTP-beállítások azt is lehetővé teszik, hogy dinamikusan válassza ki az állomásnevet egy háttérkészlet-tag teljes tartománynevéből, ha úgy van beállítva, hogy az állomásnevet egy adott háttérkészlet-tagból származtathassa. Végpontok közötti SSL alkalmazása esetén az SNI-bővítményben a rendszer a teljes tartománynévből származtatott gazdanevet használja. Ez a képesség lehetővé teszi, hogy egy háttérkészlet két vagy több több-bérlős PaaS-szolgáltatással, például az Azure-webalkalmazásokkal rendelkezzen, és a kérelem gazdagépfejléce az egyes tagoknak tartalmazza a teljes tartománynévből származtatott állomásnevet. Ebben a forgatókönyvben egy kapcsolót használunk a HTTP-beállítások nevű [Pick állomásnév a háttércímből,](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) amely dinamikusan felülbírálja a gazdagép fejlécét az eredeti kérelemben, hogy a háttérkészletben említett.  Ha például a háttérkészlet teljes tartományneve "contoso11.azurewebsites.net" és "contoso22.azurewebsites.net" azonosítót tartalmaz, az eredeti kérelem gazdagépfejléce, amely contoso.com, contoso11.azurewebsites.net vagy contoso22.azurewebsites.net amikor a kérést elküldi a megfelelő háttérkiszolgálóra. 

  ![webalkalmazás-forgatókönyv](./media/application-gateway-web-app-overview/scenario.png)

Ezzel a képességgel az ügyfelek megadhatják a HTTP-beállítások és az egyéni mintavételek beállítási lehetőségeit a megfelelő konfigurációhoz. Ez a beállítás ezután egy figyelőhöz és egy háttérkészlethez van kötve egy szabály használatával.

## <a name="special-considerations"></a>Különleges szempontok

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>SSL-végződés és végponttól végpontig ssl több-bérlős szolgáltatásokkal

Az SSL-végződtetés és a végpontok közötti SSL-titkosítás is támogatott a több-bérlős szolgáltatások. Az alkalmazásátjáró SSL-végződtetése esetén az SSL-tanúsítványt továbbra is hozzá kell adni az alkalmazásátjáró figyelőjéhez. Azonban a végpontok közötti SSL esetén a megbízható Azure-szolgáltatások, például az Azure App-szolgáltatás webalkalmazásai nem igénylik a háttérrendszerek engedélyezési listázását az alkalmazásátjáróban. Ezért nincs szükség hitelesítési tanúsítványok hozzáadására. 

![végponttól végpontig SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Figyelje meg, hogy a fenti képen nincs követelmény hitelesítési tanúsítványok hozzáadása, ha az App Service háttérrendszerként van kiválasztva.

### <a name="health-probe"></a>Állapotadat-mintavétel

A **HTTP-beállításokban** az állomásfejléc felülbírálása csak a kérelemre és annak útválasztására van hatással. ez nem befolyásolja az állapotminta viselkedését. A végpontok közötti funkciók működéséhez a mintavételi és a HTTP-beállításokat is úgy kell módosítani, hogy azok a megfelelő konfigurációt tükrözzék. Amellett, hogy lehetővé teszik egy állomásfejléc megadását a mintavételi konfigurációban, az egyéni mintavételek is támogatják az állomásfejléc levezetésének lehetőségét az aktuálisan konfigurált HTTP-beállításokból. Ezt a konfigurációt a mintavételi konfigurációban a `PickHostNameFromBackendHttpSettings` paraméterrel is megadhatjuk.

### <a name="redirection-to-app-services-url-scenario"></a>Átirányítás az App Service URL-címére

Előfordulhatnak olyan esetek, amikor az App service válaszában lévő állomásnév a végfelhasználói böngészőt az Application Gateway-hez társított tartomány helyett a *.azurewebsites.net állomásnévre irányíthatja. Ez a probléma akkor fordulhat elő, ha:

- Az átirányítás konfigurálva van az App Service-ben. Az átirányítás lehet olyan egyszerű, mint egy záró perjel hozzáadása a kérelemhez.
- Az Azure AD-hitelesítés, amely az átirányítást okozza.

Az ilyen esetek megoldásához [olvassa el Az Alkalmazásszolgáltatás URL-címére való átirányítás elhárítása című témakört.](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)

## <a name="next-steps"></a>További lépések

Megtudhatja, hogy miként állíthat be alkalmazásátjárót egy több-bérlős alkalmazással, például az Azure App Service webalkalmazással háttérkészlet-tagként, ha ellátogat [az App Service webalkalmazásainak konfigurálása az Application Gateway alkalmazásátjáróval című webhelyre.](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)
