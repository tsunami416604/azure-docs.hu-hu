---
title: Azure DNS FAQ
description: Az Azure DNS – gyakran ismételt kérdések
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/15/2019
ms.author: victorh
ms.openlocfilehash: bb5c4d508344f391d610aeaa7e0be54a93c997dc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080029"
---
# <a name="azure-dns-faq"></a>Azure DNS FAQ

## <a name="about-azure-dns"></a>Az Azure DNS szolgáltatással kapcsolatos

### <a name="what-is-azure-dns"></a>Mi az Azure DNS?

A tartománynévrendszer (DNS) a rendszer lefordítja, vagy az IP-címét a webhely vagy szolgáltatás nevének feloldása. Az Azure DNS a DNS-tartományok egy üzemeltetési szolgáltatás. Névfeloldás biztosít a Microsoft Azure-infrastruktúra használatával. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

DNS-tartományok Azure DNS-ben üzemeltetett DNS-kiszolgálók neve az Azure globális hálózatán. A rendszer használja, a csomópontválasztásos hálózat úgy, hogy minden DNS-lekérdezést a legközelebbi elérhető DNS-kiszolgáló válaszolt. Az Azure DNS által nyújtott, gyors teljesítményt és magas rendelkezésre állást a tartomány.

Az Azure DNS az Azure Resource Manager-alapú. Az Azure DNS számos előnyt biztosít, az erőforrás-kezelő szolgáltatásait, például a szerepköralapú hozzáférés-vezérlés, a vizsgálati naplók és a erőforrás zárolását. Tartományok és -rekordok az Azure Portalon, az Azure PowerShell-parancsmagok és a platformfüggetlen Azure CLI-n keresztül kezelheti. Automatikus DNS-kezelést igénylő alkalmazások integrálható a szolgáltatás a REST API-t és az SDK-k használatával.

### <a name="how-much-does-azure-dns-cost"></a>Az Azure DNS mennyibe?

Az Azure DNS számlázási modellt az Azure DNS Szolgáltatásban üzemeltetett DNS-zónák száma alapján. Azt is kapnak DNS-lekérdezések számán alapul. Kedvezmények biztosított használat alapján.

További információkért lásd: a [Azure DNS díjszabási oldalán](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Mi az Azure DNS garantált szolgáltatási szintje?

Az Azure garantálja, hogy DNS-lekérdezésekre érvényes válasz érkezik legalább egy Azure DNS-névkiszolgálótól 100 %-ában.

További információkért lásd: a [Azure DNS SZOLGÁLTATÁSSZINT-szerződésünk](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Mi a DNS-zóna? Ugyanaz, mint a DNS-tartomány? 

A tartomány egy egyedi nevet a tartománynévrendszerben. Például: contoso.com.

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A contoso.com tartomány például számos DNS-rekordot tartalmazhatnak. A rekordok tartalmazhat egy levelezési kiszolgáló, illetve www mail.contoso.com\.contoso.com webhelyhez. These records are hosted in the DNS zone contoso.com.

Egy adott tartománynév *csak egy nevét*. A DNS zone is a data resource that contains the DNS records for a domain name. Az Azure DNS használatával DNS-zónákat üzemeltethet, és kezelheti a tartomány DNS-rekordjait az Azure felületén. DNS névkiszolgálókat a DNS-lekérdezések választ az internetről is tartalmazza.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Kell vásárolni az Azure DNS használatával DNS-tartománynevet? 

Nem feltétlenül.

Nem kell vásárolni egy tartományhoz, hogy a DNS-zónát az Azure DNS-ben. Bármikor létrehozhat egy DNS-zónát anélkül, hogy tartománynévvel rendelkezne. A zóna DNS-lekérdezések csak feloldása, ha azok még átirányítja az Azure DNS névkiszolgálóit, a zóna.

A DNS-zóna összekapcsolása az globális DNS hierarchiává, meg kell vásárolnia a tartomány nevét. Ezt követően DNS-lekérdezések bárhol a világon keresse meg a DNS-zóna és a válasz a DNS-rekordjait.

## <a name="azure-dns-features"></a>Az Azure DNS-szolgáltatások

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Vonatkozik valamilyen korlátozás egy tartomány nevét a Traffic Managerrel apex alias rekordok használatakor?

Igen. Az Azure Traffic Managerrel statikus nyilvános IP-címeket kell használnia. Konfigurálja a **külső végpont** célként megadott statikus IP-cím használatával. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Támogatja az Azure DNS DNS-alapú forgalom-útválasztást vagy a végpont feladatátvételi?

A Traffic Manager által biztosított DNS-alapú forgalom útválasztási és -végpont feladatátvételi. A TRAFFIC Manager egy külön Azure-szolgáltatás, amely használható az Azure DNS használata. További információkért lásd: a [a Traffic Manager áttekintése](../traffic-manager/traffic-manager-overview.md).

Az Azure DNS csak statikus DNS-tartományok, ahol egy adott DNS-rekord minden DNS-lekérdezés mindig megkapja ugyanazt a DNS-választ üzemeltetését is támogatja.

### <a name="does-azure-dns-support-domain-name-registration"></a>Támogatja az Azure DNS tartománynév-regisztrációval?

Nem. Az Azure DNS jelenleg nem támogatja a tartománynév megvásárlása lehetőséget. Tartományok vásárlása egy külső tartományregisztrálóhoz kell használnia. A regisztráló általában csekély éves díjért költséget. A tartományok majd lehet üzemeltetni az Azure DNS DNS-rekordok kezelését. További információ: [Delegate a domain to Azure DNS](dns-domain-delegation.md) (Tartomány delegálása az Azure DNS-be).

A szolgáltatás tartományneveket megvásárlása nyomon van az Azure várakozó fájlok számát. Használja a visszajelzés hely [ennek a funkciónak a támogatási regisztrálása](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Az Azure DNS támogatja a DNSSEC?

Nem. Az Azure DNS jelenleg nem támogatja a tartomány nevét rendszer biztonsági kiterjesztéseit (DNSSEC).

A DNSSEC szolgáltatást az Azure DNS várakozó fájlok számát a követi. Használja a visszajelzés hely [ennek a funkciónak a támogatási regisztrálása](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Does Azure DNS support zone transfers (AXFR/IXFR)?

Nem. Az Azure DNS jelenleg nem támogatja a zónaletöltések. DNS-zónák lehet [importált az Azure DNS az Azure CLI-vel](dns-import-export.md). DNS-rekordok segítségével történik a [DNS az Azure felügyeleti portálján](dns-operations-recordsets-portal.md), [REST API-val](https://docs.microsoft.com/powershell/module/az.dns), [SDK](dns-sdk.md), [PowerShell-parancsmagok](dns-operations-recordsets.md), vagy a [ Parancssori eszköz](dns-operations-recordsets-cli.md).

A zónák adatátviteli funkciója nyomon van az Azure DNS várakozó fájlok számát. Használja a visszajelzés hely [ennek a funkciónak a támogatási regisztrálása](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Az Azure DNS támogatja a URL-cím átirányítások?

Nem. URL-cím átirányítási szolgáltatások nem egy DNS-szolgáltatás. A DNS-szint, hanem a HTTP-szinten működnek. DNS-szolgáltatók néhány csomagot egy URL-cím átirányítási szolgáltatás általános ajánlat részeként. Ez a szolgáltatás jelenleg az Azure DNS által nem támogatott.

Az URL-cím átirányítási funkció nyomon van az Azure DNS várakozó fájlok számát a. Használja a visszajelzés hely [ennek a funkciónak a támogatási regisztrálása](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Az Azure DNS támogatja a bővített ASCII-kódolás (8 bites) set txt típusú rekordhalmazok?

Igen. Az Azure DNS támogatja a kiterjesztett ASCII-set txt típusú rekordhalmazok kódolást. De az Azure REST API-k, SDK-k, PowerShell és a CLI legújabb verzióját kell használnia. 2017\. október 1-nél régebbi verziók vagy SDK 2.1-es nem támogatják a kiterjesztett ASCII készletet. 

Például, előfordulhat, hogy adjon meg egy karakterláncot értékeként egy txt típusú rekordot, amely rendelkezik a kiterjesztett ASCII karaktert \128. Ilyen például, "abcd\128efgh." Az Azure DNS belső ábrázolás ezt a karaktert, amely 128 bájt értékét használja. A DNS-feloldás időpontjában a bájtérték a választ adja vissza. Azt is vegye figyelembe, hogy "abc" és "\097\098\099" felcserélhetők feloldási illeti. 

Követjük [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) zóna a fájl fő formátum escape txt típusú rekordok szabályait. Ha például `\` most ténylegesen lehet kilépni mindent az RFC száma. Ha megad `A\B` txt típusú rekord értékét jelöli, és egyszerűen megoldott `AB`. Ha a TXT-rekordot, hogy valóban szeretné `A\B` karaktert kell felbontású, a `\` újra. Tegyük fel, adja meg a `A\\B`.

Ez a támogatás jelenleg nem érhető el az Azure portálon létrehozott txt típusú rekordok.

## <a name="alias-records"></a>Aliasrekordok

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Mik az egyes forgatókönyvekben, ahol alias rekordok hasznosak?

Lásd a szakasz a forgatókönyveket a [Azure DNS-alias a rekordok áttekintése](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Milyen rekordtípusokat alias rekordhalmazok támogatottak?

A következő rekordtípusokhoz, az Azure DNS-zóna alias rekordhalmazok támogatottak:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Alias rekordhalmazok célként által támogatott?

- **A DNS A vagy AAAA típusú rekordhalmaz mutasson a nyilvános IP-erőforrást.** You can create an A/AAAA record set and make it an alias record set to point to a public IP resource.
- **A DNS A/AAAA/CNAME-rekordhalmazok átirányítása a Traffic Manager-profil.** A CNAME REKORDOT a Traffic Manager-profil egy DNS CNAME rekord készletből is mutasson. Ez például akkor contoso.trafficmanager.net. Most is mutathat, amely rendelkezik egy DNS-zónáját A vagy AAAA típusú rekordot a külső végpontok Traffic Manager-profilhoz.
- **Az Azure Content Delivery Network (CDN) végpontjára mutató**. Ez akkor hasznos, amikor hoz létre az Azure storage és az Azure CDN használatával statikus webhelyek kiszolgálására.
- **Egy másik DNS rekordhalmaz ugyanabban a zónában lévő mutasson.** Alias rekordok hivatkozhat, más azonos típusú rekordhalmazok. Például rendelkezhet egy DNS CNAME-rekordhalmazzal, amely egy ugyanolyan típusú másik CNAME-rekordhalmaz aliasa. Ezzel az elrendezéssel fokozott akkor hasznos, ha azt szeretné, hogy néhány rekordhalmazt kell aliasok és az egyes nem alias.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Létrehozhat és frissíthet alias rekordokat az Azure Portalon?

Igen. Hozzon létre, vagy az Azure Portalon az Azure REST API-k, PowerShell, CLI és SDK-k alias-rekordok kezelése.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Alias rekordok segítségével, győződjön meg arról, hogy a saját DNS-rekordhalmaz törölték az alapul szolgáló nyilvános IP-cím törlése?

Igen. Ez a funkció egyike az alapképességek alias rekordok. Ez segít a felhasználók számára az alkalmazás az esetleges üzemkimaradások elkerülése érdekében.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Alias rögzíti lesz a segítségére ahhoz, hogy ellenőrizze, hogy a saját DNS-rekordhalmaz frissül, és a megfelelő IP-címet az alapul szolgáló nyilvános IP-cím változásakor?

Igen. Ez a funkció egyike az alapképességek alias rekordok. Ez segít a lehetséges valamilyen okból kimaradás lép vagy az alkalmazás biztonsági kockázatok elkerülése érdekében.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Vonatkozik valamilyen korlátozás aliasrekordot használatával állít be, a vagy AAAA típusú rögzíti, mutasson a Traffic Manager?

Igen. Mutasson a Traffic Manager-profil-alias a vagy AAAA típusú rekordhalmaz, a Traffic Manager a profil csak külső végpontokkal kell használnia. Amikor a Traffic Manager hoz létre a külső végpontokat, adja meg a tényleges IP-címek a végpontok.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Van-e használni alias rekordok külön díj?

Alias rekordok egy bizonyságot adhatnak meg egy érvényes DNS-rekordhalmaz. Nincs semmilyen további használati díjának felszámolása alias rekordok.

## <a name="use-azure-dns"></a>Az Azure DNS használata

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Az Azure DNS és a egy másik DNS-szolgáltató használatával közös üzemeltetésű tartomány lehetőségeket?

Igen. Az Azure DNS támogatja a közös üzemeltetési tartományok más DNS-szolgáltatásokkal.

Állítsa be a közös üzemeltetési, módosítsa a Névkiszolgálói rekordokat átirányítása mindkét szolgáltatót a névkiszolgálókat a tartományhoz. A névkiszolgáló (NS) vezérlőelem, mely szolgáltatók kapjanak a tartomány DNS-lekérdezések rögzíti. Ezeket a Névkiszolgálói rekordokat Azure DNS-ben, a másik szolgáltató és a szülőzóna rekordhalmazának módosíthatja. A szülőzóna rekordhalmazának általában van konfigurálva a tartomány tartománynév-n keresztül. A DNS-delegálás további információkért lásd: [tartomány DNS-delegálás](dns-domain-delegation.md).

Bizonyosodjon meg róla, hogy mindkét DNS-szolgáltatók között a szinkronban-e a tartomány DNS-rekordjait. Az Azure DNS jelenleg nem támogatja a DNS zónaletöltés. DNS-rekordok segítségével szinkronizálva kell lenniük a [DNS az Azure felügyeleti portálján](dns-operations-recordsets-portal.md), [REST API-val](https://docs.microsoft.com/powershell/module/az.dns), [SDK](dns-sdk.md), [PowerShell-parancsmagok](dns-operations-recordsets.md), vagy a [Parancssori eszköz](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Rendelkezik az összes négy Azure DNS névkiszolgálóit saját tartomány delegálása?

Igen. Azure DNS assigns four name servers to each DNS zone. Ezzel az elrendezéssel fokozott olyan hibák elszigetelését és nagyobb rugalmasság. Ahhoz, hogy az Azure DNS – SLA, tartomány delegálása az összes négy névkiszolgálói nevet.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Milyen használati korlátok vonatkoznak az Azure DNS?

Az alábbi alapértelmezett korlátok vonatkoznak az Azure DNS használata esetén.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Áthelyezhető az Azure DNS-zóna, erőforráscsoportok vagy előfizetések között?

Igen. DNS-zónák is áthelyezhetők, erőforráscsoportok vagy előfizetések között.

Nincsenek hatással a DNS-lekérdezések van, amikor helyezi át, hogy a DNS-zónát. A zónájához rendelt névkiszolgálókat ugyanaz maradjon. DNS-lekérdezések feldolgozása során szokásos módon történik.

További információk és útmutatás a DNS-zónák áthelyezése, lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Mennyi ideig tart a DNS-módosítások érvénybe léptetéséhez?

Új DNS-zónák és a DNS-rekordok általában jelennek meg az Azure DNS névkiszolgálóit gyorsan. Az időzítési néhány másodpercig.

Meglévő DNS-rekordok módosításai egy kicsit tovább is igénybe vehet. Általában megjelennek az Azure DNS névkiszolgálóit 60 másodpercen belül. DNS-gyorsítótárazást a DNS-ügyfelek és a rekurzív DNS feloldók Azure DNS-en kívül is hatással lehet a ütemezését. A gyorsítótárazás időtartama szabályozásához használja minden rekordhalmaz idő – Élettartam (TTL) tulajdonságát.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Hogyan segítenek a saját DNS-zónák a véletlen törlés elleni védelmében?

Az Azure DNS Azure Resource Manager használatával felügyeli. Az Azure DNS számos előnyt biztosít az Azure Resource Manager biztosít hozzáférést vezérlő funkciókat. Szerepköralapú hozzáférés-vezérlés szabályozza, hogy mely felhasználók olvasási vagy írási hozzáféréssel a DNS-zónák és -rekordhalmazok. Erőforrás-zárolások elkerülése véletlen módosításának és törlésének DNS-zónák és -rekordhalmazok.

További információkért lásd: [védelme DNS-zónák és rekordok](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Hogyan állíthatok be SPF-rekordokat Azure DNS-ben?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Azure DNS névkiszolgálóit megoldja IPv6 protokollon keresztül? 

Igen. Az Azure DNS névkiszolgálóit kettős verem. Kettős verem azt jelenti, hogy a ezeknek az IPv4 és IPv6-címeket. Az Azure DNS névkiszolgálóit, a DNS-zónához hozzárendelt IPv6-cím megkereséséhez használja a olyan eszköz, például az nslookup. Például: `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Hogyan állíthatok be IDN formátumú az Azure DNS?

IDN formátumú tartománynevek (IDN) kódolása a DNS-név használatával [punycode](https://en.wikipedia.org/wiki/Punycode). DNS-lekérdezések a punycode kódolású nevek használatával történik.

Konfigurálja az IDN formátumú tartománynevek az Azure DNS-ben, konvertálja a zóna nevét vagy a rekordhalmaz nevének a punycode. Az Azure DNS jelenleg nem támogatja a beépített átalakítás, illetve a punycode.

## <a name="next-steps"></a>További lépések

- [További információ az Azure DNS](dns-overview.md).

- [További információ az Azure DNS használata saját tartományok](private-dns-overview.md).

- [További információ a DNS-zónák és rekordok](dns-zones-records.md).

- [Ismerkedés az Azure DNS](dns-getstarted-portal.md).
