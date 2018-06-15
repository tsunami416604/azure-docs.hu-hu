---
title: Az Azure DNS – gyakori kérdések |} Microsoft Docs
description: Az Azure DNS kapcsolatos gyakori kérdések
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: kumud
ms.openlocfilehash: e0eb39ced1d88d2e0b6128493304f112f9c685fa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31395230"
---
# <a name="azure-dns-faq"></a>Az Azure DNS – gyakori kérdések

## <a name="about-azure-dns"></a>Az Azure DNS szolgáltatással kapcsolatos

### <a name="what-is-azure-dns"></a>Mi az Azure DNS?

A tartománynévrendszer, vagy a DNS-, felelős fordítása (vagy feloldása) az IP-címét egy webhely vagy szolgáltatás neve. Az Azure DNS egy olyan üzemeltetési szolgáltatás DNS-tartományok, biztosítani a névfeloldást a Microsoft Azure-infrastruktúra használatával. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

Az Azure DNS-DNS-tartományok Azure DNS névkiszolgálóit globális hálózata üzemelnek. Nem egyedi, hogy minden DNS-lekérdezés válaszolt a legközelebbi elérhető DNS-kiszolgáló hálózati használja. Az Azure DNS biztosít a nagy teljesítményt és a magas rendelkezésre állást a tartományra.

Az Azure DNS szolgáltatást az Azure Resource Manager alapul. Ilyen előnyöket az erőforrás-kezelő szolgáltatásait, például a szerepköralapú hozzáférés-vezérlés, a vizsgálati naplók és a erőforrás zárolását. A tartományok és a rekordok az Azure portál, Azure PowerShell-parancsmagok és a platformok közötti Azure CLI segítségével is kezelhető. Automatikus DNS-kezelési igénylő alkalmazásokhoz integrálható a REST API-t és az SDK szolgáltatás.

### <a name="how-much-does-azure-dns-cost"></a>Milyen mértékű nem költségeket az Azure DNS?

Az Azure DNS számlázási modellt a DNS-zóna Azure DNS-ben és a nekik küldött, DNS-lekérdezések száma tárolt számát alapul. Kedvezményeket biztosított-használata alapján.

További információkért lásd: a [árképzést ismertető oldalra Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Mi az Azure DNS garantált szolgáltatási szintje?

Azure biztosítja, hogy érvényes DNS-kérelmek megkapja a válasz legalább egy Azure DNS-név kiszolgálóról legalább 99,99 %-ában.

További információkért lásd: a [Azure DNS szolgáltatásiszint-szerződés lapon](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Újdonságok a DNS-zónát? Ugyanaz, mint a DNS-tartomány? 

A tartomány egy egyedi nevet a tartománynévrendszerben, például "contoso.com".


Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A "contoso.com" tartomány például számos DNS-rekordokat, például "mail.contoso.com" (levelezési kiszolgálóhoz) és "www.contoso.com" (webhelyhez) is tartalmazhat. Ezeket a rekordokat a DNS "contoso.com" zóna a támadó által.

A tartománynév *csak egy nevet*, mivel a DNS-zónát egy adatforrás, melyhez a tartománynév DNS-rekordjait tartalmazó. Az Azure DNS használatával DNS-zónákat üzemeltethet, és kezelheti a tartomány DNS-rekordjait az Azure felületén. A DNS-lekérdezéseket válaszolja meg az internetes DNS-névkiszolgálók is tartalmazza.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Kell DNS-tartománynevet vásárolnom az Azure DNS használatához? 

Nem feltétlenül.

Nem kell tartományt vásárolnia ahhoz, hogy DNS-zónát üzemeltessen az Azure DNS-ben. Bármikor létrehozhat egy DNS-zónát anélkül, hogy tartománynévvel rendelkezne. A zóna a DNS-lekérdezések csak oldható fel, ha azok a rendszer átirányítja az Azure DNS névkiszolgálóit, a zóna.

Szeretné a tartománynév vásárolhat, ha a DNS-zónát a DNS globális hierarchiába csatolni kívánt – a csatolás lehetővé teszi, hogy a DNS-lekérdezések bárhonnan a világ keresse meg a DNS-zónát, és a DNS-rekordokat a válaszolni.

## <a name="azure-dns-features"></a>Az Azure DNS-szolgáltatások

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Támogatja az Azure DNS DNS-alapú forgalom-útválasztási vagy végpont feladatátvevő?

DNS-alapú forgalom útválasztási és -végpont feladatátvételi Azure Traffic Manager által biztosított. Az Azure Traffic Manager olyan külön Azure szolgáltatás Azure DNS együtt használható. További információkért lásd: a [Traffic Manager – áttekintés](../traffic-manager/traffic-manager-overview.md).

Az Azure DNS csak akkor támogatja a "statikus" DNS-tartomány, ahol minden egyes megadott DNS-rekord a DNS-lekérdezés mindig kapja az azonos DNS-válasz üzemeltetéséhez.

### <a name="does-azure-dns-support-domain-name-registration"></a>Támogatja az Azure DNS tartománynév-regisztrációs?

Nem. Az Azure DNS jelenleg nem támogatja tartománynevek megvásárlását. Tartományok beszerezni kívánt, ha meg szeretné használni, a külső tartományregisztráló nevét. A regisztráló általában egy kis éves díj költségek. A tartomány DNS-rekordok Management majd az Azure DNS-lehet üzemeltetni. Lásd: [az Azure DNS-tartomány delegálása az](dns-domain-delegation.md) részleteiről.

Tartomány beszerzési egy olyan szolgáltatás, nyomon követi a Azure várakozó fájlok számát. Használhatja a visszajelzési webhelyet, hogy [regisztrálja ezt a szolgáltatást a támogatása](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS-ben támogatja a DNSSEC?

Nem. Az Azure DNS jelenleg nem támogatja a DNSSEC.

A DNSSEC lehetővé teszi az Azure DNS várakozó fájlok számát a nyomon követett. Használhatja a visszajelzési webhelyet, hogy [regisztrálja ezt a szolgáltatást a támogatása](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Támogatja az Azure DNS zónaletöltés (AXFR/IXFR)?

Nem. Az Azure DNS jelenleg nem támogatja a zónaátvitelek. DNS-zónák lehet [importálja az Azure DNS az Azure parancssori felület használatával](dns-import-export.md). DNS-rekordok majd szolgáltatással kezelendő a [Azure DNS-kezelési portál](dns-operations-recordsets-portal.md), a [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell-parancsmagok](dns-operations-recordsets.md), vagy [CLI eszköz](dns-operations-recordsets-cli.md).

Zónaletöltés lehetővé teszi az Azure DNS várakozó fájlok számát a nyomon követett. Használhatja a visszajelzési webhelyet, hogy [regisztrálja ezt a szolgáltatást a támogatása](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Támogatja az Azure DNS URL-cím átirányítások?

Nem. URL-cím átirányítási szolgáltatások nincsenek ténylegesen egy DNS-szolgáltatás, mert a DNS-szintjét, hanem a HTTP szinten működnek. Egyes DNS-szolgáltatók szeretné a URL-címet átirányítja a szolgáltatás a teljes ajánlat részeként. Ez nem jelenleg támogatott Azure DNS által.

Átirányítási URL-cím funkció az Azure DNS várakozó kulcsban követhető nyomon. Használhatja a visszajelzési webhelyet, hogy [regisztrálja ezt a szolgáltatást a támogatása](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-extended-ascii-encoding-8-bit-set-for-txt-recordset-"></a>Támogatja az Azure DNS TXT rekordhalmazhoz (8 bites) kódolást kiterjesztett ASCII?

Igen. Az Azure DNS támogat a kiterjesztett ASCII kódolási a TXT-rekordhalmazok, ha az Azure REST API-k, a SDK-k, a PowerShell és a parancssori felület (2017-10-01-verziók vagy nem támogatja a bővített ASCII karakterkészlet SDK 2.1 tegye) legújabb verzióját használja. Például, ha a felhasználó megadja egy karakterlánc értékeként egy TXT-rekord, amelynek a kiterjesztett ASCII karaktert \128 (pl.: "abcd\128efgh"), Azure DNS belső ábrázolás fogja használni a bájtérték (amely 128) karaktert. A DNS-feloldás időpontjában, valamint a bájtérték visszatér a válaszban. Ne feledje, hogy "abc" és "\097\098\099" felcserélhető feloldási illetően. 

Hajtsa végre az azt [RFC 1035 szabványnak megfelelően](https://www.ietf.org/rfc/rfc1035.txt) fájl fő formátum escape szabályok TXT-rekord zóna. Például "\" most ténylegesen lehet kilépni minden, egy a RFC. "A\B" TXT rekord értékeként adja meg, ha ez fog megjelenni, és oldja meg, csak az "AB". Ha a TXT-rekord "A\B" megoldás, hogy valóban szeretné kell megadnia a "\" újra, azaz Adja meg az "A\\B". 

Vegye figyelembe, hogy ez a támogatás jelenleg nem érhető el az Azure-portálon létrehozott TXT rekord. 

## <a name="using-azure-dns"></a>Az Azure DNS-sel

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Telepíthetek üzemeltetni használata az Azure DNS- és egy másik DNS-szolgáltatónál tartományhoz?

Igen. Az Azure DNS támogatja a párhuzamos üzemeltetési tartományok más DNS-szolgáltatásokkal.

Ehhez módosítania kell a Névkiszolgálói rekordokat, a tartomány (a tartomány lekérdezések mely vezérlő, mely szolgáltatók kapjanak DNS), hogy mindkét szolgáltatót névkiszolgálókat mutasson. Ezeket a Névkiszolgálói rekordokat három helyen módosíthatja: az Azure DNS, a másik szolgáltató és a szülőzónában (általában a regisztrációs beállítani). A DNS-delegálás további információkért lásd: [DNS-tartomány delegálás](dns-domain-delegation.md).

Továbbá szüksége arról, hogy a tartomány DNS-rekordjait mindkét DNS-szolgáltató közötti szinkronizálásához. Az Azure DNS jelenleg nem támogatja a DNS zónaletöltés. Szükséges DNS-rekordok segítségével szinkronizálni a [Azure DNS-kezelési portál](dns-operations-recordsets-portal.md), [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell-parancsmagok](dns-operations-recordsets.md), vagy [CLI eszköz](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Rendelkezik a tartomány összes négy Azure DNS névkiszolgálóit delegálása?

Igen. Az Azure DNS névkiszolgálóit négy minden DNS-zóna, a tartalék elkülönítési és nagyobb rugalmasság rendeli hozzá. Ahhoz, hogy az Azure DNS SLA, tartomány delegálása az összes négy névkiszolgálók kell.

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

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>Megoldásához tegye a Azure DNS Nameservers IPv6-kapcsolaton keresztül? 

Igen. Az Azure DNS Nameservers rendszer kettős verem (IPv4 és IPv6-címmel rendelkezzen). Az IPv6-cím keresése a rendelt a DNS-zóna Azure DNS nameservers, egy eszköz, például az nslookup is használhat (például `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Hogyan állíthatom be egy nemzetközi tartományneveket (IDN) az Azure DNS?

Nemzetközi tartományneveket (IDN) működnek-e kódolását minden DNS neve "[punycode](https://en.wikipedia.org/wiki/Punycode)". DNS-lekérdezéseket tesz a punycode titkosítású nevek használatával.

Konfigurálhatja nemzetközi tartományneveket (IDN) az Azure DNS-alakítja át, a zóna nevét vagy rekordhalmaz neveként a punycode. Az Azure DNS jelenleg nem támogatja a punycode és a beépített átalakítását.

## <a name="private-dns"></a>Saját DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Támogatja az Azure DNS "private" tartományok?
"Private" tartomány támogatása a saját zónák funkció segítségével van megvalósítva.  Ez a funkció jelenleg tartozik nyilvános előzetes verziójában.  Személyes zónák felügyelt eszközök használatával az internet felé néző Azure DNS-zónák, de csak a megadott virtuális hálózatokon feloldható.  Tekintse meg a [áttekintése](private-dns-overview.md) részleteiről.

Jelenleg a saját zónák használata nem támogatott az Azure portálon. 

A más belső DNS-beállítások az Azure-ban információkért lásd: [névfeloldás virtuális gépek és a Szerepkörpéldányok](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Mi a különbség a regisztrációs virtuális hálózat és a megoldás virtuális hálózat között titkos zónák kontextusában? 
Virtuális hálózatok hozzákapcsolhatja egy DNS-titkos zóna kétféleképpen - regisztrációs virtuális hálózati vagy virtuális hálózati feloldás. Mindkét esetben a virtuális hálózatban lévő virtuális gépek fog tudni sikeresen fel kell oldania a titkos zónában ellen. Azonban ha regisztrációs virtuális hálózati adja meg a virtuális hálózat, Azure automatikusan regisztrálja (dinamikus regisztrációs) DNS-rekordokat a zónához a virtuális gépek virtuális hálózatban. Továbbá, ha egy virtuális gépről a virtuális hálózat törlése regisztráció, Azure is automatikusan eltávolítja a megfelelő DNS-rekord a csatolt titkos zónájából. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure saját DNS-zónák Azure-régiók közötti működnek?
Igen. Saját zónák DNS-feloldás különböző Azure-régiók, explicit módon-társviszony létesítése – a virtuális hálózatok, mindaddig, amíg a virtuális hálózatok vannak az összes megadott feloldási virtuális hálózatok a személyes zóna nélkül is virtuális hálózatok között támogatott. Előfordulhat, hogy az ügyfelek a virtuális hálózatok társítottak, az TCP/HTTP forgalmat egy adott területre a másik. 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>A saját zónák szükséges virtuális hálózatok van kapcsolat az internetre?
Nem. Saját zónák virtuális hálózatok összehangolva használhatók, és tudassa az ügyfelekkel, a virtuális gépek vagy más erőforrások belül, mind a virtuális hálózatok közötti tartományok kezelése. Internetkapcsolat nélküli szükség a névfeloldáshoz. 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>Az azonos zónába saját használható több virtuális hálózat a feloldásához? 
Igen. Az ügyfelek felbontása legfeljebb 10 virtuális hálózatok társíthatja egy titkos zóna.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>Egy virtuális hálózatot, amelyhez tartozik egy másik előfizetésben található felveheti feloldási virtuális hálózatként saját zónához? 
Igen, mindaddig, amíg a felhasználó rendelkezik-e írási művelet engedélye mind a virtuális hálózatok, valamint a saját DNS-zónát. Vegye figyelembe, hogy az írási engedély több RBAC-szerepkörök lehet osztani. Például a klasszikus hálózati közreműködői RBAC szerepkörhöz írási jogot a virtuális hálózatok. Az RBAC-szerepkörök további információkért lásd: [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>A privát zóna automatikusan regisztrált virtuális gép DNS-rekordok automatikusan törli a virtuális gépek esetén törli az ügyfél által?
Igen. Ha töröl egy virtuális gépet a regisztrációs virtuális hálózaton belül, a Microsoft automatikusan törli a DNS-rekordokat, mivel ez egy regisztrációs virtuális hálózati miatt a zónába regisztrált. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Az automatikusan regisztrált virtuális gép rekord (a regisztrációs virtuális hálózat) saját zónában törölhetők manuálisan? 
Nem. Jelenleg a virtuális gép DNS-rekordok regisztrációs virtuális hálózatról titkos zónában automatikusan regisztrált nem látható vagy a felhasználók. Ugyanakkor cseréje (felülírása) ilyen automatikusan regisztrált DNS-rekordokat a DNS-manuálisan létrehozott jegyezze fel a zónában. Tekintse meg a következő kérdést és választ, amely rendelkezik ezzel a funkcióval.

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>Mi történik, ha azt manuálisan hozzon létre egy új DNS-rekordot egy titkos zónába regisztrációs virtuális hálózat (automatikusan regisztrált) meglévő virtuális gépből, azonos állomásnévvel rendelkező megpróbálja? 
Manuálisan hozzon létre egy új DNS-rekordot egy titkos zónába regisztrációs virtuális hálózatot egy meglévő (automatikusan regisztrált) virtuális gépként azonos állomásnévvel rendelkező kísérli meg, hogy lehetővé teszi az regisztrált automatikusan felülírja az új DNS-rekord virtuális gép rekord. Ha ezt követően próbálja törli ezt a manuálisan létrehozott DNS-rekord eltávolítása a zónából, akkor sikeres, a delete, továbbá az automatikus regisztráció újra történik (a DNS-rekord hozza létre újra automatikusan a zónában lesz) mindaddig, a virtuális gép továbbra is létezik, és egy privát IP-cím van csatolva. 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone--would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>Mi történik, ha azt a személyes zónából regisztrációs virtuális hálózat leválasztása? A virtuális hálózati automatikusan regisztrált virtuális gép rekordját eltűnnek, valamint a zóna?
Igen. Ha megszünteti egy regisztrációs virtuális hálózat (frissítés a DNS-zónát eltávolítani a regisztrációs társított virtuális hálózati) a személyes zónából, Azure a zóna automatikusan regisztrált virtuális gép rekordok eltávolít. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone--do-we-have-to-manually-update-the-private-zone-to-un-link-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>Mi történik, ha azt egy regisztrációs (vagy a feloldás) a virtuális hálózat törlése, amely csatolva van egy titkos zónát? Tudunk manuálisan frissíteni a privát zóna un-hivatkozás a virtuális hálózat regisztrációs (vagy megoldás) virtuális hálózati eltávolítása a zónából?
Igen. Ha töröl egy regisztrációs (vagy a feloldás) virtuális hálózati nélkül egy olyan magánhálózat a forgatókönyvhöz való kapcsolásának zónát először, Azure lehetővé teszi, hogy a törlési művelet sikeres, de a virtuális hálózat már nem automatikusan a személyes zónából ha van ilyen. Manuálisan leválasztása a virtuális hálózat titkos zónából származó kell. Emiatt tanácsos először leválasztása a virtuális hálózaton lévő a titkos zóna törlése előtt.

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>DNS-feloldás az alapértelmezett teljes Tartományneve (internal.cloudapp.net) továbbra is működni, akkor is, ha egy titkos zónát kellene (például: contoso.local) csatolva van egy virtuális hálózatot? 
Igen. Saját zónák a szolgáltatás nem helyettesíti az alapértelmezett DNS-megoldások használata az Azure által biztosított internal.cloudapp.net zóna és érhető el egy további lehetőség vagy fejlesztésen esett. Mindkét esetben az (akár az Azure által biztosított internal.cloudapp.net vagy a saját privát zóna függő) tanácsos a teljes tartománynév, a zóna megoldandó ellen. 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>A DNS-utótag egy csatolt virtuális hálózaton belüli virtuális gépek a megváltozik-e a saját zóna? 
Nem. Jelenleg a virtuális gépeken a csatolt virtuális hálózat DNS-utótagját marad az alapértelmezett Azure által biztosított utótagot ("*. internal.cloudapp.net"). Azonban manuálisan módosíthatja a DNS-utótagot a virtuális gépeken, valamint a titkos zóna. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Vannak-e saját zónák korlátozások ezen előzetes?
Igen. Nyilvános előzetes a következő korlátozások vonatkoznak:
* 1 regisztrációs virtuális hálózatok saját zónánkénti
* Legfeljebb 10 feloldási virtuális hálózatok saját zónánkénti
* Egy adott virtuális hálózathoz csak kapcsolható egy saját zóna regisztrációs virtuális hálózati
* Egy adott virtuális hálózati lehet társítani legfeljebb 10 saját zónák virtuális hálózati feloldás
* Ha egy regisztrációs virtuális hálózathoz van megadva, a DNS-rekordokat, a virtuális gépek a virtuális hálózatban, a saját zónához regisztrált nem lesz megtekinthető vagy a Powershell vagy parancssori felület/API-lekérhető, de a virtuális gép rekordokat valóban regisztrálja, és feloldja sikeresen megtörtént
* Névkeresési DNS csak így fog működni az privát IP-címtér regisztrációs virtuális hálózatban
* Névkeresési DNS egy privát IP-címhez, amely nincs regisztrálva a saját zónában (például: privát IP-Címek egy virtuális gép virtuális hálózati feloldási titkos zónához kapcsolódó virtuális hálózatban) ad vissza "internal.cloudapp.net" DNS-utótagként, azonban Ez utótag csak akkor tudják feloldani.   
* Virtuális hálózati kell lennie az üres (azaz nem egy hálózati Adaptere csatlakoztatva a virtuális gépek) kezdeti (azaz az első alkalommal) regisztrációja vagy névfeloldási virtuális hálózatként saját zónához csatolása. Azonban a virtuális hálózati majd lehet nem üres jövőbeli kapcsolásához regisztrációs vagy névfeloldási virtuális hálózati, egyéb személyes zónákhoz. 
* Ilyenkor feltételes továbbítás nem támogatott, például Azure és a helyi üzemeltetésű hálózatok felbontást engedélyezéséhez. Hogyan ügyfelek is valósíthat meg ebben a forgatókönyvben keresztül más mechanizmusok dokumentációja, lásd: [virtuális gépek és a Szerepkörpéldányok névfeloldás](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Vannak-e a kvóták vagy zónák vagy titkos zónák rekordok vonatkozó korlátozások?
Nincsenek külön korlátozások zónák előfizetésenként engedélyezett száma, vagy az egyes zónák saját zónák rekordhalmazok száma. Nyilvános és a saját zónák száma a teljes DNS-korlátok felé dokumentált [Itt](../azure-subscription-service-limits.md#dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>Nincs titkos zónák támogatása portál?
Külső mechanizmusok (API-t vagy PowerShell vagy parancssori felület/SDK) keresztül már létrehozott magánfelhő zónák fognak megjelenni az Azure portálon, de az ügyfelek nem tud új titkos zónák létrehozása vagy kezelése virtuális hálózatokhoz való társítást lesz. Emellett regisztrációs virtuális hálózatokként társított virtuális hálózatok, a virtuális gép automatikusan regisztrált rekordok nem lesznek láthatók a portálon. 

## <a name="next-steps"></a>További lépések

[További tudnivalók az Azure DNS-ben](dns-overview.md)
<br>
[További tudnivalók a saját tartományok Azure DNS-sel](private-dns-overview.md)
<br>
[További információ a DNS-zónák és rekordok](dns-zones-records.md)
<br>
[Ismerkedés az Azure DNS-ben](dns-getstarted-portal.md)

