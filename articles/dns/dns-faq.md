---
title: Az Azure DNS – gyakori kérdések |} A Microsoft Docs
description: Az Azure DNS – gyakran ismételt kérdések
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: victorh
ms.openlocfilehash: 747b2e2499a9bafcf7a7b03bc2ce144828c55c75
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172500"
---
# <a name="azure-dns-faq"></a>Az Azure DNS – gyakori kérdések

## <a name="about-azure-dns"></a>Az Azure DNS szolgáltatással kapcsolatos

### <a name="what-is-azure-dns"></a>Mi az Azure DNS?

A Domain Name System, vagy a DNS-beli felelős fordítása (vagy feloldása) az IP-címét a webhely vagy szolgáltatás nevét. Az Azure DNS a DNS-tartományok, a üzemeltetési szolgáltatás biztosítani a névfeloldást a Microsoft Azure infrastruktúráját használja. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

Az Azure DNS DNS-tartományokat az Azure DNS-névkiszolgálók globális hálózata van közzétéve. Ez a csomópont-választásos hálózat úgy, hogy minden DNS-lekérdezést a legközelebbi elérhető DNS-kiszolgáló válaszolt használja. Az Azure DNS gyors teljesítményt és magas rendelkezésre állást a tartomány is biztosít.

Az Azure DNS szolgáltatást az Azure Resource Manager-alapú. Mint ilyen akkor számos előnyt biztosít az erőforrás-kezelő szolgáltatásait, például a szerepköralapú hozzáférés-vezérlés, a vizsgálati naplók és a erőforrás zárolását. A tartományok és -rekordok az Azure Portalon, az Azure PowerShell-parancsmagok és a platformfüggetlen Azure CLI-n keresztül kezelhetők. Automatikus DNS-kezelést igénylő alkalmazásokhoz integrálható a szolgáltatás a REST API-t és az SDK-k használatával.

### <a name="how-much-does-azure-dns-cost"></a>Az Azure DNS mennyibe?

Az Azure DNS számlázási modellt az Azure DNS-ben és a kapott DNS-lekérdezések száma üzemeltetett DNS-zónák száma alapján. Kedvezmények biztosított használat alapján.

További információkért lásd: a [Azure DNS díjszabási oldalán](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Mi az Azure DNS garantált szolgáltatási szintje?

Az Azure garantálja, hogy érvényes DNS-lekérdezésekre fog egy válasz érkezik az Azure DNS-neve legalább egy kiszolgáló legalább 99,99 %-ában.

További információkért lásd: a [Azure DNS SZOLGÁLTATÁSSZINT-szerződésünk](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Mi a DNS-zóna? Ugyanaz, mint a DNS-tartomány? 

A tartomány egy egyedi nevet a tartománynévrendszerben, például "contoso.com".


Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A "contoso.com" tartomány például számos DNS-rekordot, például "mail.contoso.com" (levelezési kiszolgálóhoz) és "www.contoso.com" (webhelyhez) is tartalmazhatja. Ezeket a rekordokat a DNS "contoso.com" zónában szeretné üzemeltetni.

Egy adott tartománynév *csak egy nevét*, mivel a DNS-zónát a DNS-rekordok a tartomány nevét tartalmazó adatok erőforrás. Az Azure DNS használatával DNS-zónákat üzemeltethet, és kezelheti a tartomány DNS-rekordjait az Azure felületén. DNS névkiszolgálókat a DNS-lekérdezések választ az internetről is tartalmazza.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Kell DNS-tartománynevet vásárolnom az Azure DNS használatához? 

Nem feltétlenül.

Nem kell tartományt vásárolnia ahhoz, hogy DNS-zónát üzemeltessen az Azure DNS-ben. Bármikor létrehozhat egy DNS-zónát anélkül, hogy tartománynévvel rendelkezne. A zóna DNS-lekérdezések csak feloldja, ha a rendszer átirányítja az Azure DNS névkiszolgálóit, a zóna.

Kell vásárolnia a tartománynevet, ha szeretne kapcsolni a DNS-zónát az globális DNS hierarchiává – a csatolás lehetővé teszi a DNS-lekérdezések bárhol a világon, keresse meg a DNS-zónát, és válaszul megkapják a DNS-rekordok az.

## <a name="azure-dns-features"></a>Az Azure DNS-szolgáltatások

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Támogatja az Azure DNS DNS-alapú forgalom-útválasztást vagy a végpont feladatátvételi?

DNS-alapú forgalom útválasztási és -végpont feladatátvétel az Azure Traffic Manager által biztosított. Az Azure Traffic Manager egy külön Azure-szolgáltatás, amely az Azure DNS együtt is használhatók. További információkért lásd: a [a Traffic Manager áttekintése](../traffic-manager/traffic-manager-overview.md).

Az Azure DNS csak "statikus" DNS-tartományok, ahol egy adott DNS-rekord minden DNS-lekérdezés mindig megkapja ugyanazt a DNS-választ üzemeltetését is támogatja.

### <a name="does-azure-dns-support-domain-name-registration"></a>Támogatja az Azure DNS tartománynév-regisztrációval?

Nem. Az Azure DNS jelenleg nem támogatja a tartománynév-vásárlást. Ha meg szeretné tartományokat vásárolhatnak, szeretné használni egy külső tartománynév regisztrálójánál. A regisztráló általában csekély éves díjért költséget. A tartomány DNS-rekordok kezelését majd az Azure DNS-ben lehet üzemeltetni. Lásd: [tartomány delegálása az Azure DNS](dns-domain-delegation.md) részleteiről.

Tartomány beszerzési funkciója nyomon követi a Azure mappájában várakozó fájlok számát. Használhatja a visszajelzés hely [ennek a funkciónak a támogatási regisztrálása](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Az Azure DNS támogatja a DNSSEC?

Nem. Az Azure DNS jelenleg nem támogatja a DNSSEC.

A DNSSEC lehetővé teszi az Azure DNS mappájában várakozó fájlok számát a nyomon követett. Használhatja a visszajelzés hely [ennek a funkciónak a támogatási regisztrálása](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Az Azure DNS támogatja a zónaletöltések (AXFR és IXFR)?

Nem. Az Azure DNS jelenleg nem támogatja a zónaletöltések. DNS-zónák lehet [importálja az Azure DNS az Azure CLI-vel](dns-import-export.md). DNS-rekordok majd keresztül kezelhetők a [DNS az Azure felügyeleti portálján](dns-operations-recordsets-portal.md), a [REST API-val](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell-parancsmagok](dns-operations-recordsets.md), vagy [Parancssori eszköz](dns-operations-recordsets-cli.md).

Zónaletöltés funkciója nyomon követi az Azure DNS mappájában várakozó fájlok számát. Használhatja a visszajelzés hely [ennek a funkciónak a támogatási regisztrálása](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Az Azure DNS támogatja a URL-cím átirányítások?

Nem. URL-cím átirányítási szolgáltatások nem kerülnek valójában egy DNS-szolgáltatás – a DNS-szint, hanem a HTTP-szinten működnek. Néhány kötegeléséhez egy URL-cím DNS-szolgáltatók irányítsa át a szolgáltatás általános ajánlat részeként. Jelenleg nem támogatott az Azure DNS által ez.

A szolgáltatás URL-cím átirányítási rendszer nyomon követi az Azure DNS mappájában várakozó fájlok számát. Használhatja a visszajelzés hely [ennek a funkciónak a támogatási regisztrálása](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-extended-ascii-encoding-8-bit-set-for-txt-recordset-"></a>Az Azure DNS támogatja a bővített ASCII (8 bites) kódolást a txt típusú rekordhalmaz?

Igen. Az Azure DNS támogatja a kiterjesztett kódolási ASCII-készlet txt típusú rekordhalmazok, ha az Azure REST API-k, SDK-k, PowerShell és CLI (2017-10-01-nél régebbi verziók vagy nem támogatja a kiterjesztett ASCII készletet SDK 2.1 do) legújabb verzióját használja. Például, ha a felhasználó megadja egy karakterlánc értéket egy txt típusú rekordot, amely rendelkezik a kiterjesztett ASCII karaktert \128 (pl.: "abcd\128efgh"), az Azure DNS belső ábrázolás fogja használni a bájtérték ezt a karaktert (ami 128). A DNS-feloldás időpontjában, valamint a bájt értéket kap eredménynek a válaszban. Azt is vegye figyelembe, hogy "abc" és "\097\098\099" felcserélhetők feloldási illeti. 

Követjük [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) zóna a fájl fő formátum escape txt típusú rekordok szabályait. Például "\" most ténylegesen lehet kilépni mindent az RFC száma. Ha "A\B" txt típusú rekord értékeként adja meg, jelenik meg, és oldja meg, csak a "AB". Ha a TXT-rekord "A\B" megoldás, hogy valóban szeretné karaktert kell a "\" újra, azaz Adja meg a "A\\B". 

Vegye figyelembe, hogy ez a támogatás jelenleg nem érhető el a txt típusú rekordok létrehozása az Azure Portalról. 

## <a name="using-azure-dns"></a>Az Azure DNS használatával

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Helyezhetem el a közös üzemeltetésű egy tartomány Azure DNS-ben és a egy másik DNS-szolgáltató használatával?

Igen. Az Azure DNS támogatja a közös üzemeltetési tartományok más DNS-szolgáltatásokkal.

Ehhez módosítania a Névkiszolgálói rekordokat a tartomány (a tartomány melyik vezérlőelem, mely szolgáltatók kapjanak DNS lekérdezések), hogy mindkét szolgáltatót névkiszolgálókat mutasson. Módosíthatja ezeket a Névkiszolgálói rekordokat három helyen: az Azure DNS-ben, a másik szolgáltató és a szülőzóna rekordhalmazának (általában be van állítva a tartomány tartománynév-n keresztül). A DNS-delegálás további információkért lásd: [tartomány DNS-delegálás](dns-domain-delegation.md).

Emellett szüksége annak érdekében, hogy a tartomány DNS-rekordok mindkét DNS-szolgáltatók közötti szinkronizálásához. Az Azure DNS jelenleg nem támogatja a DNS zónaletöltés. DNS-rekordok segítségével szinkronizálni kell a [DNS az Azure felügyeleti portálján](dns-operations-recordsets-portal.md), [REST API-val](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell-parancsmagok](dns-operations-recordsets.md), vagy [Parancssori eszköz](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Rendelkezik az összes négy Azure DNS névkiszolgálóit saját tartomány delegálása?

Igen. Az Azure DNS minden DNS-zóna, a hibák elszigetelését és nagyobb rugalmasság a négy névkiszolgálói nevet rendeli. Ahhoz, hogy az Azure DNS – SLA, tartomány delegálása az összes négy névkiszolgálói nevet kell.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Milyen használati korlátok vonatkoznak az Azure DNS?

Az alábbi alapértelmezett korlátok vonatkoznak az Azure DNS használata esetén:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Áthelyezhető az Azure DNS-zóna, erőforráscsoportok vagy előfizetések között?

Igen. DNS-zónák is áthelyezhetők, erőforráscsoportok vagy előfizetések között.

Nincs nincs hatással a DNS-lekérdezéseket a DNS-zóna áthelyezésekor. A zónájához rendelt névkiszolgálókat változatlanok maradnak, és a DNS-lekérdezések feldolgozása során szokásos módon.

További információk és útmutatás a DNS-zónák áthelyezése, lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Mennyi ideig tart a DNS-módosítások érvénybe léptetéséhez?

Új DNS-zónák és a DNS-rekordok általában megjelennek az Azure DNS névkiszolgálóit, néhány másodpercen belül.

Meglévő DNS-rekordok módosításai egy kicsit több időt vesz igénybe, de érdemes még mindig tükrözi az Azure DNS névkiszolgálóit 60 másodpercen belül. Ebben az esetben DNS-gyorsítótárazást az Azure DNS-en kívül (a DNS-ügyfelek és a DNS-rekurzív feloldók) is hatással lehet egy DNS-módosítás érvénybe az idő. A gyorsítótárazás időtartama minden rekordhalmaz idő – Élettartam (TTL) tulajdonsága használatával lehet irányítani.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Hogyan segítenek a saját DNS-zónák a véletlen törlés elleni védelmében?

Az Azure DNS az Azure Resource Manager felügyelt és a hozzáférés-vezérlést előnyeit, hogy az Azure Resource Manager funkciót biztosít. Szerepköralapú hozzáférés-vezérlés segítségével szabályozhatja, hogy mely felhasználók olvasási vagy írási hozzáféréssel a DNS-zónák és -rekordhalmazok. Erőforrás-zárolások megakadályozza a véletlen módosításának és törlésének DNS-zónák és -rekordhalmazok is alkalmazható.

További információkért lásd: [védelme DNS-zónák és rekordok](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Hogyan állíthatok be SPF-rekordokat Azure DNS-ben?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>Az Azure DNS delegálásához megoldja IPv6 protokollon keresztül? 

Igen. Az Azure DNS delegálásához rendszer kettős vermű (IPv4 és IPv6-címeket is kell). Az IPv6-cím keresése az Azure DNS delegálásához a DNS-zónához hozzárendelt, megfelelő eszköz, például az nslookup használható (például `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Hogyan állíthatok be egy nemzetközi tartomány nevét (IDN) az Azure DNS?

Nemzetközi tartományneveket (IDN) által az egyes DNS név használatával működnek "[punycode](https://en.wikipedia.org/wiki/Punycode)". DNS-lekérdezések történnek a punycode kódolású nevek használatával.

Konfigurálhat nemzetközi tartományneveket (IDN) az Azure DNS-ben való visszaváltás, a zóna nevét, vagy a rekordhalmaz-névvel a punycode. Az Azure DNS jelenleg nem támogatja a punycode és a beépített átalakítás.

## <a name="private-dns"></a>Privát DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Az Azure DNS támogatja a "saját" tartományok?
"Private" tartomány támogatása a saját zónák funkció segítségével van megvalósítva.  Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.  Privát zónák felügyelt ugyanazokkal az eszközökkel, az Azure DNS-zónák internetkapcsolattal rendelkező, de csak a megadott virtuális hálózaton belül feloldható.  Tekintse meg a [áttekintése](private-dns-overview.md) részleteiről.

Jelenleg privát zónák nem támogatottak az Azure Portalon. 

Információk az egyéb belső DNS-beállítások az Azure-ban: [virtuális gépek és Szerepkörpéldányok névfeloldása](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Mi a különbség, regisztrációs virtuális hálózatok és a feloldási virtuális hálózat között a privát zónák kontextusában? 
Virtuális hálózatok privát DNS-zóna kétféle módon – egy regisztrációs virtuális hálózatként vagy feloldási virtuális hálózati kapcsolat. Mindkét esetben a virtuális hálózatban lévő virtuális gépek fogja tudni sikeresen fel kell oldania a saját zóna rekordjait ellen. Azonban ha megad egy virtuális hálózatot egy regisztrációs virtuális hálózatként, az Azure automatikusan regisztrálnak (dinamikus regisztráció) DNS-rekordok a virtuális hálózatban a virtuális gépek a zónához. Továbbá, ha egy virtuális géphez a virtuális hálózat törlése egy regisztrációt, az Azure is automatikusan eltávolítja a megfelelő DNS-rekord a csatolt titkos zónából. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Működni fog az Azure DNS Private Zones Azure-régióban?
Igen. Saját zónák DNS-feloldás közötti virtuális hálózatok az Azure-régióban, explicit módon a virtuális társhálózatok, mindaddig, amíg a virtuális hálózatok az összes megadott feloldási virtuális hálózatok számára a privát zónák nélkül is támogatott. Az ügyfeleknek kell előfordulhat, hogy a virtuális hálózatok társviszonyba állíthatók, a TCP/HTTP-adatforgalom megvalósulását egyik régióból egy másikba. 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Az Internet kapcsolat van a virtuális hálózatok privát zónák esetében szükséges?
Nem. Saját zónák működnek együtt a virtuális hálózatok, és lehetőség biztosítása az ügyfeleknek a tartományok kezelése virtuális gépek és egyéb erőforrások belül és azok a virtuális hálózatok között. Nincs internetkapcsolat nem szükséges a névfeloldáshoz. 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>A megoldás több virtuális hálózat használható a azonos Private Zone? 
Igen. Ügyfelek társíthat legfeljebb 10 feloldási virtuális hálózatok csak egy privát zónához.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>Egy másik előfizetéshez tartozó virtuális hálózat lehet hozzáadni a feloldási virtuális hálózatot, egy privát zónához? 
Igen, mindaddig, amíg a felhasználó rendelkezik írási művelet engedélyt mind a virtuális hálózatok, valamint a privát DNS-zónát. Vegye figyelembe, hogy az írási engedély lehet osztani több RBAC-szerepkört. Például a klasszikus hálózati közreműködő RBAC szerepkör rendelkezik írási engedéllyel a virtuális hálózatokhoz. További információ az RBAC-szerepkörök: [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>A privát zónák automatikusan regisztrált virtuális gép DNS-rekordok automatikusan törölve lesznek az ügyfél által a virtuális gépek törlése esetén?
Igen. Ha törli a regisztrációs virtuális hálózaton belüli virtuális gépek, miatt, mivel ez egy regisztrációs virtuális hálózatnak a zónába regisztrált DNS-rekordok automatikusan töröljük. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>A privát zónák (a regisztrációs virtuális hálózatnak) egy automatikus regisztrált virtuális gép bejegyzéséhez törölhetők manuálisan? 
Nem. Jelenleg a virtuális gép DNS-rekordokat, amelyek automatikusan regisztrálva lettek a regisztrációs virtuális hálózatról privát zónák amelyek nem látható vagy ügyfél által szerkeszthető. Ugyanakkor cserélje le (felülírása) az ilyen automatikusan regisztrált DNS-rekordok a manuálisan létrehozott DNS jegyezze fel a zónában. Tekintse meg a következő kérdést és választ, amely rendelkezik ezzel a funkcióval.

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>Mi történik, amikor azt próbálja meg manuálisan hozzon létre új DNS-rekord egy privát zónához, a regisztrációs virtuális hálózatban (automatikusan regisztrált) meglévő virtuális gépként az azonos állomásnévvel rendelkező? 
Ha megpróbálja manuálisan hozzon létre új DNS-rekord egy privát zónához, a regisztrációs virtuális hálózat meglévő (automatikusan regisztrált) virtuális gépként az azonos állomásnévvel rendelkező, hogy lehetővé teszi regisztrált automatikusan felülírja az új DNS-rekord virtuális gép rekord. Továbbá, ha később törli ezt a manuálisan létrehozott DNS-rekord a zóna, a törlés sikeres lesz, és az automatikus regisztráció újra fog zajlani (a DNS-rekord lesz újra létre automatikusan a zónában) mindaddig, a virtuális gép továbbra is létezik, és a egy magánhálózati IP-cím van csatolva. 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone--would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>Mi történik, ha azt a privát zónák regisztrációs virtuális hálózat leválasztása? A virtuális hálózat automatikusan regisztrált virtuális gép rekordját megszűnik a zónából?
Igen. A regisztrációs virtuális hálózatnak (frissítés a DNS-zóna eltávolítása a kapcsolódó regisztrációs virtuális hálózatnak) megszünteti egy privát zónához, ha az Azure eltávolítja automatikusan regisztrált virtuális gép rekordokat a zónában. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone--do-we-have-to-manually-update-the-private-zone-to-un-link-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>Mi történik, ha azt a privát zónához kapcsolódó regisztrációs (vagy feloldási) virtuális hálózat törlése? Tegye azt manuálisan frissítenie kell a saját zóna törlésekor-hivatkozás a regisztráció (vagy feloldása) virtuális hálózat virtuális hálózati a zónából?
Igen. Ha töröl egy regisztrációs (vagy feloldási) virtuális hálózati nélkül leválasztása azt a privát zónák először, az Azure lehetővé teszi a törlési művelet sikeres, de a virtuális hálózat nem áll automatikusan leválasztása a privát zónák, ha van ilyen. Manuálisan leválasztása a virtuális hálózattal a privát zónák kell. Ebből kifolyólag javasolt először leválasztása a virtuális hálózaton lévő a saját zóna törlése előtt.

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>DNS-feloldás az alapértelmezett teljes Tartományneve (internal.cloudapp.net) továbbra is működik, akkor is, ha a privát zónát kellene (például: contoso.local) van csatolva a virtuális hálózat? 
Igen. Saját zónák funkció nem helyettesíti az alapértelmezett DNS megoldások használata az Azure által biztosított internal.cloudapp.net zóna, és a egy további lehetőség, vagy a fejlesztés érhető el. Mindkét esetben az (akár a függő Azure által biztosított internal.cloudapp.net, illetve a saját privát zónához) használja a teljes Tartománynevet a feloldás szeretné a zóna javasolt. 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>A DNS-utótagot a társított virtuális hálózaton belüli virtuális gépek megváltozik, valamint a Private Zone? 
Nem. Jelenleg a virtuális gépek, a társított virtuális hálózat DNS-utótag marad az alapértelmezett Azure által biztosított utótagként ("*. internal.cloudapp.net"). Azonban manuálisan módosíthatja a DNS-utótagot a virtuális gépeken, valamint a saját zóna. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Vonatkozik valamilyen korlátozás privát zónák esetében ez az előzetes verzió használata során?
Igen. A nyilvános előzetes verzióban létezik a következő korlátozások vonatkoznak:
* 1 a regisztrációs virtuális hálózatok privát zónánkénti
* Legfeljebb 10 feloldási virtuális hálózatok privát zónánkénti
* Egy adott virtuális hálózathoz csak kapcsolható egy privát zónához a regisztrációs virtuális hálózatként
* Egy adott virtuális hálózaton kapcsolható akár 10 privát zónák feloldási virtuális hálózatként
* Ha meg van adva a regisztrációs virtuális hálózat, a virtuális gépek a virtuális hálózatról a Private Zone regisztrált DNS-rekordjait nem lesz megtekinthető vagy a Powershell vagy parancssori felület vagy API-kon keresztül lekérdezhető, de a virtuális gép rekordok valóban regisztrálva van, és feloldja sikeresen megtörtént
* Fordított DNS csak fog működni a regisztrációs virtuális hálózat privát IP-címtér
* Egy magánhálózati IP-címet, amely nincs regisztrálva a Private Zone a címfeloldási DNS (pl.: egy virtuális hálózatot, amely a feloldási virtuális hálózattal egy privát zónához van csatolva a virtuális gép magánhálózati IP) adja vissza "internal.cloudapp.net" DNS-utótagként, azonban Ennek az utótagnak nem oldható fel.   
* Hálózati virtuális üresnek kell lennie (vagyis) nincsenek a csatlakoztatott hálózati Adapterrel rendelkező virtuális gépek) kezdeti (vagyis) az első alkalommal) csatolása a regisztrációs vagy feloldási virtuális hálózat privát zónához. Azonban a virtuális hálózat majd lehet nem üres jövőbeli kapcsolásának egy regisztrációs vagy feloldási virtuális hálózattal, az egyéb privát zónák. 
* Jelenleg feltételes továbbítás nem támogatott, például engedélyezésével a megoldás az Azure és a rendszert hálózatok között. Hogyan ügyfelek is valósíthat meg ebben a forgatókönyvben keresztül más mechanizmusok dokumentációért tekintse meg [virtuális gépek és Szerepkörpéldányok névfeloldása](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Bármely kvóták vagy zónák vagy saját zónák rekordjait vonatkozó korlátozások vannak-e?
A zónák előfizetésenként engedélyezett számát vagy a privát zónák zónánként rekordhalmazok számának nincs külön korlátozva van. Nyilvános és privát zónák mind beleszámítanak az összesített DNS-korlátok dokumentált [Itt](../azure-subscription-service-limits.md#dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>Saját zónák támogatása portál van?
Külső mechanizmusok (API/PowerShell/CLI/SDK-k) használatával már létrehozott privát zónák az Azure Portalon látható lesz, de az ügyfelek fog nem sikerült létrehozni új privát zónák, vagy a társítások a virtuális hálózatok kezelése. Emellett kapcsolódó regisztrációs virtuális hálózatok, virtuális hálózatok, a virtuális gép automatikusan regisztrált rekordokat nem lesznek láthatók a portálon. 

## <a name="next-steps"></a>További lépések

[További információ az Azure DNS-ben](dns-overview.md)
<br>
[További információ az Azure DNS használata saját tartományok esetében](private-dns-overview.md)
<br>
[További információ a DNS-zónák és rekordok](dns-zones-records.md)
<br>
[Ismerkedés az Azure DNS-ben](dns-getstarted-portal.md)

