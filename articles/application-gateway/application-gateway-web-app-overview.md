---
title: Több-bérlős háttér – például Azure app Service – áttekintése az Azure Application Gateway
description: Ez az oldal áttekintést nyújt az Application Gateway több-bérlős háttérrendszerekhez elérhető támogatásáról.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 66e4a578e3f443f4cbc3f6e5467cf9a86adf05fe
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297047"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Application Gateway támogatja a több-bérlős háttérrendszer, például az App Service használatát

A webkiszolgálók több-bérlős architektúrájának kialakításában több webhely fut ugyanazon a webkiszolgáló-példányon. Az állomásnevek a különböző üzemeltetett alkalmazások megkülönböztetésére szolgálnak. Alapértelmezés szerint az Application Gateway nem módosítja az ügyféltől származó bejövő HTTP-állomásfejlécet, és így küldi tovább a háttérrendszernek. Ez jól működik a háttér-készletek tagjai, például a hálózati adapterek, a virtuálisgép-méretezési csoportok, a nyilvános IP-címek, a belső IP-címek és a teljes tartománynevek esetében, mivel ezek nem függnek egy adott állomásfejléc-vagy SNI-bővítménnyel a megfelelő végpontra Számos olyan szolgáltatás, mint például az Azure app Service Web Apps és az Azure API Management, amelyek több-bérlős jellegűek, és egy adott állomásfejléc-vagy SNI-bővítményt használnak a megfelelő végpontra való feloldáshoz. Általában az alkalmazás DNS-neve, amely viszont az Application gatewayhez társított DNS-név, eltér a háttér-szolgáltatás tartománynevétől. Ezért az Application Gateway által fogadott eredeti kérelemben szereplő állomásfejléc nem egyezik meg a háttér-szolgáltatás állomásneve nevével. Emiatt, ha az Application Gateway-ből a háttérbe irányuló kérelemben szereplő állomásfejléc nem változik a háttérrendszer állomásneve, a több-bérlős háttérrendszer nem tudja feloldani a kérést a megfelelő végpontra. 

Az Application Gatewayben elérhető egy olyan funkció, amely lehetővé teszi a felhasználóknak a HTTP-kiszolgálófejléc felülbírálását a kérelmekben a háttérrendszer gazdaneve alapján. Ez biztosítja a több-bérlős háttérrendszerek, mint az Azure App Service Web Apps és az API Management támogatását. A funkció a v1 és v2 standard, valamint a WAF termékváltozat esetében is elérhető. 

![gazdagép felülbírálása](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Ez nem alkalmazható az Azure app Service Environment (bemutató) szolgáltatásra, mivel a szolgáltató egy dedikált erőforrás az Azure app Service-től, amely több-bérlős erőforrás.

## <a name="override-host-header-in-the-request"></a>A kérelemben szereplő állomásfejléc felülbírálása

A gazdagép felülbírálásának megadásának lehetősége a http- [beállításokban](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) van meghatározva, és a szabályok létrehozása során bármely háttér-készletre alkalmazható. A több-bérlős háttérrel rendelkező állomásfejléc-és SNI-bővítmények felülbírálása a következő két módon támogatott:

- Az állomásnév beállítása a HTTP-beállításokban explicit módon megadott rögzített értékre. Ez a funkció biztosítja, hogy a gazdagép fejléce felülbírálva legyen az értékre az adott HTTP-beállításokat alkalmazó háttér-készletre irányuló összes forgalom esetében. Végpontok közötti SSL alkalmazása esetén az SNI-bővítményben a rendszer a felülírt gazdanevet használja. Ez a funkció olyan forgatókönyveket tesz lehetővé, amelyekben a háttérbeli készlet farmja olyan állomásfejléc-t vár, amely eltér a bejövő ügyfél-állomásfejléc-fejléctől.

- A gazdagép nevének származtatása a háttérbeli készlet tagjainak IP-címéről vagy teljes tartománynevéről. A HTTP-beállítások azt is lehetővé teszik, hogy a rendszer dinamikusan kiválassza az állomásnevet a háttérbeli készlet tagjainak teljes tartománynevéről, ha úgy van konfigurálva, hogy az adott háttérbeli készletből származó állomásnév származtatása beállítást használja. Végpontok közötti SSL alkalmazása esetén az SNI-bővítményben a rendszer a teljes tartománynévből származtatott gazdanevet használja. Ez a funkció lehetővé teszi olyan forgatókönyvek használatát, amelyekben egy háttér-készlet két vagy több több-bérlős, például Azure Web Apps-szolgáltatással rendelkezhet, és a kérelem állomásneve az egyes tagok számára a teljes tartománynévből származtatott állomásnevet tartalmazza. Ennek a forgatókönyvnek a megvalósításához a [pick hostname](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) nevű http-beállításokat használjuk a háttér-címről, amely dinamikusan felülbírálja az eredeti kérelemben szereplő állomásfejléc-fejlécet a háttér-készletben említettek szerint.  Ha például a háttérbeli készlet teljes tartományneve a "contoso11.azurewebsites.net" és a "contoso22.azurewebsites.net" értéket tartalmazza, akkor az eredeti kérelem állomásneve, amely a contoso.com lesz felülbírálva a contoso11.azurewebsites.net vagy a contoso22.azurewebsites.net a kérésnek a megfelelő háttér-kiszolgálóra történő küldésekor. 

  ![webalkalmazás-forgatókönyv](./media/application-gateway-web-app-overview/scenario.png)

Ezzel a képességgel az ügyfelek megadhatják a HTTP-beállítások és az egyéni mintavételek beállítási lehetőségeit a megfelelő konfigurációhoz. Ez a beállítás egy figyelőhöz és egy háttér-készlethez van kötve egy szabály használatával.

## <a name="special-considerations"></a>Különleges szempontok

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>SSL-lezárás és végpontok közötti SSL a több-bérlős szolgáltatásokkal

A több-bérlős szolgáltatások esetében az SSL-lezárás és a végpontok közötti SSL-titkosítás is támogatott. Az Application Gateway SSL-lezárása esetén az SSL-tanúsítvány továbbra is szükséges az Application Gateway-figyelőhöz való hozzáadáshoz. A végpontok közötti SSL esetében azonban a megbízható Azure-szolgáltatások, például az Azure app Service Web Apps nem igénylik a háttérrendszer engedélyezését az Application gatewayben. Ezért nincs szükség hitelesítési tanúsítványok hozzáadására. 

![végpontok közötti SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Figyelje meg, hogy a fenti képen nincs szükség hitelesítési tanúsítványok hozzáadására, ha az App Service-t háttérként választja.

### <a name="health-probe"></a>Állapotadat-mintavétel

A gazdagép fejlécének felülbírálása a **http** -beállításokban csak a kérést és annak útválasztását érinti. nem befolyásolja az állapot mintavételi viselkedését. A végpontok közötti funkciók működéséhez a mintavételi és a HTTP-beállításokat is úgy kell módosítani, hogy azok a megfelelő konfigurációt tükrözzék. Amellett, hogy lehetővé teszi a gazdagép fejlécének megadását a mintavételi konfigurációban, az egyéni mintavételek is támogatják a gazdagép fejlécének a jelenleg konfigurált HTTP-beállításokból való származtatása lehetőségét. Ezt a konfigurációt a mintavételi konfigurációban a `PickHostNameFromBackendHttpSettings` paraméterrel is megadhatjuk.

### <a name="redirection-to-app-services-url-scenario"></a>Átirányítás App Service URL-címére

Előfordulhatnak olyan forgatókönyvek, amelyekben az App Service-válaszban szereplő állomásnév a Application Gatewayhoz társított tartomány helyett a *. azurewebsites.net állomásnévre irányíthatja a végfelhasználói böngészőt. Ez a probléma a következő esetekben fordulhat elő:

- Az átirányítás konfigurálva van a App Serviceon. Az átirányítás olyan egyszerű lehet, mint egy záró perjel hozzáadása a kéréshez.
- Rendelkezik Azure AD-hitelesítéssel, amely az átirányítás okát okozza.

Az ilyen esetek megoldásához lásd: [az App Service URL-címére irányuló átirányítás hibaelhárítása](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan állíthat be egy olyan Application Gateway-t egy több-bérlős alkalmazással, mint például az Azure app Service web app a háttérbeli készlet tagjaként, ha meglátogatja [app Service webalkalmazások konfigurálását Application Gateway](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)
