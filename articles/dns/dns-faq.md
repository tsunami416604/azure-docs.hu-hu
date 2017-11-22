---
title: "Az Azure DNS – gyakori kérdések |} Microsoft Docs"
description: "Az Azure DNS kapcsolatos gyakori kérdések"
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: kumud
ms.openlocfilehash: a8cff450730d05970398989ceb3e8585aefd6771
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2017
---
# <a name="azure-dns-faq"></a>Az Azure DNS – gyakori kérdések

## <a name="about-azure-dns"></a>Az Azure DNS szolgáltatással kapcsolatos

### <a name="what-is-azure-dns"></a>Mi az Azure DNS?

A tartománynévrendszer, vagy a DNS-, felelős fordítása (vagy feloldása) az IP-címét egy webhely vagy szolgáltatás neve. Az Azure DNS egy olyan üzemeltetési szolgáltatás DNS-tartományok, biztosítani a névfeloldást a Microsoft Azure-infrastruktúra használatával. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

Az Azure DNS-DNS-tartományok Azure DNS névkiszolgálóit globális hálózata üzemelnek. Nem egyedi, hogy minden DNS-lekérdezés válaszolt a legközelebbi elérhető DNS-kiszolgáló hálózati is használjuk. Gyors teljesítmény és a magas rendelkezésre állást biztosít a tartományhoz.

Az Azure DNS szolgáltatást az Azure Resource Manager alapul. Ilyen előnyöket az erőforrás-kezelő szolgáltatásait, például a szerepköralapú hozzáférés-vezérlés, a vizsgálati naplók és a erőforrás zárolását. A tartományok és a rekordok az Azure portál, Azure PowerShell-parancsmagok és a platformok közötti Azure CLI segítségével is kezelhető. Automatikus DNS-kezelési igénylő alkalmazásokhoz integrálható a REST API-t és az SDK szolgáltatás.

### <a name="how-much-does-azure-dns-cost"></a>Milyen mértékű nem költségeket az Azure DNS?

Az Azure DNS számlázási modellt a DNS-zóna Azure DNS-ben és a nekik küldött, DNS-lekérdezések száma tárolt számát alapul. Kedvezményeket biztosított-használata alapján.

További információkért lásd: a [árképzést ismertető oldalra Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Mi az Azure DNS garantált szolgáltatási szintje?

Garantáljuk, hogy az idő legalább 99,99%-ában válasz érkezik az érvényes DNS-lekérdezésekre legalább egy Azure DNS-névkiszolgálótól.

További információkért lásd: a [Azure DNS szolgáltatásiszint-szerződés lapon](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Újdonságok a DNS-zónát? Ugyanaz, mint a DNS-tartomány? 

A tartomány egy egyedi nevet a tartománynévrendszerben, például "contoso.com".

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A "contoso.com" tartomány például számos DNS-rekordokat, például "mail.contoso.com" (levelezési kiszolgálóhoz) és "www.contoso.com" (webhelyhez) is tartalmazhat. Ezek a támadó által a DNS "contoso.com" zóna.

A tartománynév *csak egy nevet*, mivel a DNS-zónát egy adatforrás, melyhez a tartománynév DNS-rekordjait tartalmazó. Az Azure DNS használatával DNS-zónákat üzemeltethet, és kezelheti a tartomány DNS-rekordjait az Azure felületén. A DNS-lekérdezéseket válaszolja meg az internetes DNS-névkiszolgálók is tartalmazza.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Kell DNS-tartománynevet vásárolnom az Azure DNS használatához? 

Nem feltétlenül.

Nem kell tartományt vásárolnia ahhoz, hogy DNS-zónát üzemeltessen az Azure DNS-ben. Bármikor létrehozhat egy DNS-zónát anélkül, hogy tartománynévvel rendelkezne. A zóna a DNS-lekérdezések csak oldható fel, ha azok a rendszer átirányítja az Azure DNS névkiszolgálóit, a zóna.

Kell a tartománynév vásárolhat, ha a DNS-zónát a DNS globális hierarchiába csatolni kívánt – Ez a DNS-lekérdezések bárhonnan lehetővé teszi a világ keresse meg a DNS-zónát, és a DNS-rekordokat a válaszolni.

## <a name="azure-dns-features"></a>Az Azure DNS-szolgáltatások

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Támogatja az Azure DNS DNS-alapú forgalom-útválasztási vagy végpont feladatátvevő?

DNS-alapú forgalom útválasztási és -végpont feladatátvételi Azure Traffic Manager által biztosított. Ez az Azure DNS együtt használható külön Azure szolgáltatás. További információkért lásd: a [Traffic Manager – áttekintés](../traffic-manager/traffic-manager-overview.md).

Az Azure DNS csak akkor támogatja a "statikus" DNS-tartomány, ahol minden egyes megadott DNS-rekord a DNS-lekérdezés mindig kapja az azonos DNS-válasz üzemeltetéséhez.

### <a name="does-azure-dns-support-domain-name-registration"></a>Támogatja az Azure DNS tartománynév-regisztrációs?

Nem. Az Azure DNS jelenleg nem támogatja tartománynevek megvásárlását. Tartományok beszerezni kívánt, ha meg szeretné használni, a külső tartományregisztráló nevét. A regisztráló általában egy kis éves díj költségek. A tartomány DNS-rekordok Management majd az Azure DNS-lehet üzemeltetni. Lásd: [az Azure DNS-tartomány delegálása az](dns-domain-delegation.md) részleteiről.

Ez a tulajdonság azt a várakozó fájlok számát a nyomon követett. Használhatja a visszajelzési webhelyet, hogy [regisztrálja ezt a szolgáltatást a támogatása](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-private-domains"></a>Támogatja az Azure DNS "private" tartományok?
"Private" tartomány támogatása a saját DNS-zónák segítségével van megvalósítva.  Ez a funkció jelenleg tartozik által megtekinthető villámnézetként.  Saját DNS-zónák felügyelt eszközök használatával az internet felé néző Azure DNS-zónák, de csak a megadott virtuális hálózatokon feloldható.  Tekintse meg a [áttekintése](private-dns-overview.md) részleteiről.

A más belső DNS-beállítások az Azure-ban információkért lásd: [névfeloldás virtuális gépek és a Szerepkörpéldányok](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS-ben támogatja a DNSSEC?

Nem. Az Azure DNS jelenleg nem támogatja a DNSSEC.

Ez a tulajdonság azt a várakozó fájlok számát a nyomon követett. Használhatja a visszajelzési webhelyet, hogy [regisztrálja ezt a szolgáltatást a támogatása](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Támogatja az Azure DNS zónaletöltés (AXFR/IXFR)?

Nem. Az Azure DNS jelenleg nem támogatja a zónaátvitelek. DNS-zónák lehet [importálja az Azure DNS az Azure parancssori felület használatával](dns-import-export.md). DNS-rekordok majd szolgáltatással kezelendő a [Azure DNS-kezelési portál](dns-operations-recordsets-portal.md), a [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell-parancsmagok](dns-operations-recordsets.md), vagy [CLI eszköz](dns-operations-recordsets-cli.md).

Ez a tulajdonság azt a várakozó fájlok számát a nyomon követett. Használhatja a visszajelzési webhelyet, hogy [regisztrálja ezt a szolgáltatást a támogatása](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Támogatja az Azure DNS URL-cím átirányítások?

Nem. URL-cím átirányítási szolgáltatások nincsenek ténylegesen egy DNS-szolgáltatás, mert a DNS-szintjét, hanem a HTTP szinten működnek. Egyes DNS-szolgáltatók szeretné a URL-címet átirányítja a szolgáltatás a teljes ajánlat részeként. Ez nem jelenleg támogatott Azure DNS által.

Ez a funkció a várakozó fájlok számát a kulcsban követhető nyomon. Használhatja a visszajelzési webhelyet, hogy [regisztrálja ezt a szolgáltatást a támogatása](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

## <a name="using-azure-dns"></a>Az Azure DNS-sel

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Telepíthetek üzemeltetni használata az Azure DNS- és egy másik DNS-szolgáltatónál tartományhoz?

Igen. Az Azure DNS támogatja a párhuzamos üzemeltetési tartományok más DNS-szolgáltatásokkal.

Ehhez módosítania kell a Névkiszolgálói rekordokat, a tartomány (a tartomány lekérdezések mely vezérlő, mely szolgáltatók kapjanak DNS), hogy mindkét szolgáltatót névkiszolgálókat mutasson. Ezeket a Névkiszolgálói rekordokat kell 3 helyen lehet módosítani: az Azure DNS, a másik szolgáltató és a szülőzónában (általában a regisztrációs beállítani). A DNS-delegálás további információkért lásd: [DNS-tartomány delegálás](dns-domain-delegation.md).

Továbbá szüksége arról, hogy a tartomány DNS-rekordjait mindkét DNS-szolgáltató közötti szinkronizálásához. Az Azure DNS jelenleg nem támogatja a DNS zónaletöltés. DNS-rekordok segítségével szinkronizálnia kell a [Azure DNS-kezelési portál](dns-operations-recordsets-portal.md), a [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell-parancsmagok](dns-operations-recordsets.md), vagy [CLI eszköz](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-4-azure-dns-name-servers"></a>Rendelkezik a tartomány összes 4 Azure DNS névkiszolgálóit delegálása?

Igen. Az Azure DNS névkiszolgálóit 4 minden DNS-zóna, a tartalék elkülönítési és nagyobb rugalmasság rendeli hozzá. Ahhoz, hogy az Azure DNS SLA, tartomány delegálása az összes 4 névkiszolgálók kell.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Az Azure DNS-használati korlátai

A következő alapértelmezett korlátokat alkalmazza, ha Azure DNS-sel:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Áthelyezhető egy Azure DNS-zóna, erőforráscsoport-sablonok vagy előfizetések között?

Igen. DNS-zónák akkor helyezheti át, erőforráscsoport-sablonok vagy előfizetések között.

Nincs hatással a DNS-lekérdezéseket a DNS-zónák áthelyezésekor. A zónájához rendelt névkiszolgálókat változatlan marad, és DNS-lekérdezések teljes szokásos módon dolgozza fel.

További információkat és útmutatást helyezze át a DNS-zónák [erőforrások áthelyezése egy új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Mennyi időt vesz igénybe DNS módosítások érvénybe lépéséhez?

Új DNS-zónák és a DNS-rekordok általában megjelennek az Azure DNS névkiszolgálóit gyorsan, néhány másodpercen belül.

Meglévő DNS-rekordok módosítása egy kicsit tovább tarthat, de kell még mindig tükrözi az Azure DNS névkiszolgálóit 60 másodpercen belül. Ebben az esetben DNS-gyorsítótárazás kívül Azure DNS-ben (amelyet a DNS-ügyfelek és a DNS rekurzív feloldókat) is hatással lehet a DNS módosításának érvénybe idő. A gyorsítótárazás időtartama az idő-Élettartam (TTL) tulajdonsága minden rekordhalmaz használatával lehet irányítani.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Hogyan védheti meg a DNS-zónák véletlen törlése ellen?

Az Azure DNS felügyelt Azure Resource Manager használatával, és számos előnyt biztosít a hozzáférés-vezérlés az adott Azure Resource Manager funkciókat biztosít. Szerepköralapú hozzáférés-vezérlés segítségével szabályozhatja, mely felhasználók olvasási vagy írási hozzáférést a DNS-zónák és rekordkészletek. Erőforrás zárolások véletlen módosításának és törlésének a DNS-zónák és rekordkészletek érdekében alkalmazhatók.

További információkért lásd: [védelme DNS-zónák és rekordok](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Hogyan állíthatom be SPF-rekordokat az Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6-"></a>Megoldásához tegye a Azure DNS Nameservers IPv6-kapcsolaton keresztül? 

Igen. Az Azure DNS Nameservers rendszer kettős verem (IPv4 és IPv6-címmel rendelkezzen). Az IPv6-cím keresése a rendelt a DNS-zóna Azure DNS nameservers, egy eszköz, például az nslookup is használhat (például `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Hogyan állíthatom be egy nemzetközi tartományneveket (IDN) az Azure DNS?

Nemzetközi tartományneveket (IDN) működnek-e kódolását minden DNS neve "[punycode](https://en.wikipedia.org/wiki/Punycode)". DNS-lekérdezéseket tesz a punycode titkosítású nevek használatával.

Konfigurálhatja nemzetközi tartományneveket (IDN) az Azure DNS-alakítja át, a zóna nevét vagy rekordhalmaz neveként a punycode. Az Azure DNS jelenleg nem támogatja a punycode és a beépített átalakítását.

## <a name="next-steps"></a>Következő lépések

[További tudnivalók az Azure DNS-ben](dns-overview.md)
<br>
[További tudnivalók a saját tartományok Azure DNS-sel](private-dns-overview.md)
<br>
[További információ a DNS-zónák és rekordok](dns-zones-records.md)
<br>
[Ismerkedés az Azure DNS-ben](dns-getstarted-portal.md)

