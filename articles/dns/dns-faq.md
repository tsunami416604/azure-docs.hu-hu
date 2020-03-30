---
title: Gyakori kérdések – Azure DNS
description: Ebből a cikkből megismerésaz Azure DNS-sel kapcsolatos gyakori kérdésekről
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 6/15/2019
ms.author: rohink
ms.openlocfilehash: 76b19cfb3c00a26d81eab81f67d8e156a520f377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77121724"
---
# <a name="azure-dns-faq"></a>Azure DNS – gyakori kérdések

## <a name="about-azure-dns"></a>Az Azure DNS-ről

### <a name="what-is-azure-dns"></a>Mi az Azure DNS?

A DNS lefordítja vagy feloldja a webhely vagy szolgáltatás nevét az IP-címére. Az Azure DNS a DNS-tartományokhoz használható üzemeltetési szolgáltatás. A Microsoft Azure-infrastruktúra használatával névfeloldást biztosít. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

Az Azure DNS-ben található DNS-tartományok az Azure globális DNS-névkiszolgálói nak hálózatán találhatók. Ez a rendszer az Anycast hálózatot használja, így minden DNS-lekérdezésre a legközelebbi elérhető DNS-kiszolgáló válaszol. Az Azure DNS gyors teljesítményt és magas rendelkezésre állást biztosít a tartományszámára.

Az Azure DNS az Azure Resource Manager en alapul. Az Azure DNS-t az Erőforrás-kezelő olyan funkciói élvezhetik, mint a szerepköralapú hozzáférés-vezérlés, a naplónaplók és az erőforrás-zárolás. A tartományok és rekordok kezelése az Azure Portalon, az Azure PowerShell-parancsmagokon és a platformfüggetlen Azure CLI-n keresztül kezelhető. Az automatikus DNS-kezelést igénylő alkalmazások integrálhatók a szolgáltatással a REST API-n és az SDK-n keresztül.

### <a name="how-much-does-azure-dns-cost"></a>Mennyibe kerül az Azure DNS költsége?

Az Azure DNS számlázási modell az Azure DNS-ben tárolt DNS-zónák számától függ. A kapott DNS-lekérdezések számán is alapul. A kedvezmények a használat alapján állnak rendelkezésre.

További információt az [Azure DNS-díjszabási lapján](https://azure.microsoft.com/pricing/details/dns/)talál.

### <a name="what-is-the-sla-for-azure-dns"></a>Mi az Azure DNS SLA-ja?

Az Azure garantálja, hogy az érvényes DNS-kérelmek az idő 100%-ában legalább egy Azure DNS-névkiszolgálótól kapválaszt.

További információt az [Azure DNS SLA (Dns-sLA) lapon talál.](https://azure.microsoft.com/support/legal/sla/dns)

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Mi az a DNS-zóna? Ez ugyanaz, mint a DNS-tartomány? 

A tartomány a tartománynév-rendszerben egyedi név. Például: contoso.com.

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartomány contoso.com például több DNS-rekordot is tartalmazhat. A rekordok tartalmazhatnak mail.contoso.com egy\.levelezési kiszolgálóhoz és www contoso.com egy webhelyhez. Ezek a rekordok a DNS-zónában contoso.com találhatók.

A domain név *csak egy név*. A DNS-zóna olyan adaterőforrás, amely egy tartománynév DNS-rekordjait tartalmazza. Az Azure DNS használatával DNS-zónákat üzemeltethet, és kezelheti a tartomány DNS-rekordjait az Azure felületén. Dns-névkiszolgálókat is biztosít az internetről érkező DNS-lekérdezések megválaszolásához.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Az Azure DNS használatához DNS-tartománynevet kell vásárolnom? 

Nem feltétlenül.

Nem kell tartományt vásárolnia ahhoz, hogy dns-zónát üzemeltetjen az Azure DNS-ben. Dns-zónát bármikor létrehozhat a tartománynév nélkül. A zóna DNS-lekérdezései csak akkor oldódnak fel, ha azok a zónához rendelt Azure DNS-névkiszolgálókra vannak irányítva.

Ahhoz, hogy a DNS-zónát a globális DNS-hierarchiához csatolja, meg kell vásárolnia a tartománynevet. Ezután a világ bármely pontjáról érkező DNS-lekérdezések megtalálják a DNS-zónát, és válaszolnak a DNS-rekordjaiddal.

## <a name="azure-dns-features"></a>Az Azure DNS-szolgáltatásai

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Vannak-e korlátozások, amikor alias rekordokat használ egy domain név csúcsa a Traffic Manager?

Igen. Statikus nyilvános IP-címeket kell használnia az Azure Traffic Manager rel. Konfigurálja a **külső végpontcélt** statikus IP-cím használatával. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Támogatja az Azure DNS-t a DNS-alapú forgalomútválasztás vagy végpontfeladat-átvétel?

A DNS-alapú forgalomútválasztás és végpontfeladat-átvétel a Traffic Manager biztosítja. A Traffic Manager egy különálló Azure-szolgáltatás, amely használható az Azure DNS-sel. További információt a [Forgalomkezelő áttekintésében talál.](../traffic-manager/traffic-manager-overview.md)

Az Azure DNS csak statikus DNS-tartományok üzemeltetését támogatja, ahol egy adott DNS-rekord minden DNS-lekérdezése mindig ugyanazt a DNS-választ kapja.

### <a name="does-azure-dns-support-domain-name-registration"></a>Támogatja az Azure DNS a tartománynév-regisztrációt?

Nem. Az Azure DNS jelenleg nem támogatja a tartománynevek megvásárlásának lehetőségét. Tartományok vásárlásához külső tartománynév-regisztrálót kell használnia. Az anyakönyvvezető általában egy kis éves díjat számít fel. A tartományok ezután az Azure DNS-ben üzemeltethetők a DNS-rekordok kezeléséhez. További információt a [Tartomány delegálása az Azure DNS-re](dns-domain-delegation.md)című témakörben talál.

A tartománynevek megvásárlására a szolgáltatás nyomon követhető az Azure hátralékában. A visszajelzési webhely segítségével [regisztrálhatja a funkció támogatását.](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar)

### <a name="does-azure-dns-support-dnssec"></a>Támogatja az Azure DNS-szolgáltatás a DNSSEC szolgáltatást?

Nem. Az Azure DNS jelenleg nem támogatja a tartománynévrendszer biztonsági bővítményeit (DNSSEC).

A DNSSEC szolgáltatás nyomon követhető az Azure DNS-hátralékban. A visszajelzési webhely segítségével [regisztrálhatja a funkció támogatását.](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support)

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Az Azure DNS-támogatás zóna (AXFR/IXFR)?

Nem. Az Azure DNS jelenleg nem támogatja a zónaletöltést. A DNS-zónák [az Azure CLI használatával importálhatók az Azure DNS-be.](dns-import-export.md) A DNS-rekordok kezelése az [Azure DNS-kezelési portálon](dns-operations-recordsets-portal.md), [REST API,](https://docs.microsoft.com/powershell/module/az.dns) [SDK](dns-sdk.md), [PowerShell-parancsmagokon](dns-operations-recordsets.md)vagy a [CLI-eszközön](dns-operations-recordsets-cli.md)keresztül történik.

A zónaletöltési szolgáltatás nyomon követhető az Azure DNS-hátralékban. A visszajelzési webhely segítségével [regisztrálhatja a funkció támogatását.](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c)

### <a name="does-azure-dns-support-url-redirects"></a>Támogatja az Azure DNS-t az URL-átirányítások?

Nem. Az URL-átirányítási szolgáltatások nem DNS-szolgáltatások. A DNS-szint helyett a HTTP szintjén működnek. Egyes DNS-szolgáltatók a teljes ajánlatuk részeként url-átirányítási szolgáltatást adnak. Ezt a szolgáltatást az Azure DNS jelenleg nem támogatja.

Az URL-átirányítási funkció nyomon követhető az Azure DNS-hátralékban. A visszajelzési webhely segítségével [regisztrálhatja a funkció támogatását.](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape)

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Támogatja az Azure DNS a TXT rekordkészletekhez beállított kiterjesztett ASCII kódolást (8 bites)

Igen. Az Azure DNS támogatja a TXT rekordkészletek kiterjesztett ASCII kódoláskészletét. De az Azure REST API-k, SDK-k, PowerShell és a CLI legújabb verzióját kell használnia. 2017. október 1-nél régebbi verziók, illetve az SDK 2.1 nem támogatják a bővített ASCII-készletet. 

Megadhat például egy karakterláncot egy olyan TXT rekord értékeként, amely a kiterjesztett ASCII karaktert (\128) rendelkezik. Erre példa az "abcd\128efgh" Az Azure DNS a belső ábrázolásban a karakter bájtértékét használja, amely 128. A DNS-feloldás időpontjában ez a bájtérték jelenik meg a válaszban. Vegye figyelembe azt is, hogy az "abc" és a "\097\098\099" felcserélhetők a megoldás tekintetében. 

Követjük [az RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) zóna fájl törzsformátumescape szabályait a TXT rekordokhoz. Például `\` most már valójában megszökik mindent egy RFC. Ha a `A\B` TXT rekord értékeként adja meg, az `AB`csak . Ha azt szeretné, hogy a `A\B` TXT rekord felbontásban `\` legyen, akkor újra el kell menekülnie. Például adja `A\\B`meg a értéket.

Ez a támogatás jelenleg nem érhető el az Azure Portalon létrehozott TXT-rekordokhoz.

## <a name="alias-records"></a>Aliasrekordok

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Milyen esetekben hasznosak az aliasrekordok?

Tekintse meg a forgatókönyvek szakaszt az [Azure DNS-aliasrekordok áttekintésében.](dns-alias.md)

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Milyen bejegyzéstípusok támogatottak az alias rekordhalmazok számára?

Az Alias rekordkészletek az Azure DNS-zónában lévő következő rekordtípusok esetében támogatottak:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Milyen erőforrásokat támogatnak az aliasrekord-készletek tárolóiként?

- **Mutasson egy nyilvános IP-erőforrásra egy DNS A/AAAA rekordkészletből.** Létrehozhat egy A/AAAA rekordkészletet, és aliasrekordkészletet állíthat be, amely nyilvános IP-erőforrásra mutat.
- **Mutasson egy Traffic Manager-profilra egy DNS A/AAAA/CNAME rekordkészletből.** A Traffic Manager-profil CNAME-profiljára DNS CNAME rekordkészletből mutathat. Egy példa a contoso.trafficmanager.net. Most is rámutathat egy Traffic Manager-profilra, amely külső végpontokat biztosít a DNS-zónában beállított A vagy AAAA rekordból.
- **Mutasson egy Azure Content Delivery Network (CDN) végpontra.** Ez akkor hasznos, ha statikus webhelyeket hoz létre az Azure Storage és az Azure CDN használatával.
- **Mutasson egy másik DNS-rekordra, amely ugyanabban a zónában található.** Az aliasrekordok más azonos típusú rekordhalmazokra is hivatkozhatnak. Például rendelkezhet egy DNS CNAME-rekordhalmazzal, amely egy ugyanolyan típusú másik CNAME-rekordhalmaz aliasa. Ez az elrendezés akkor hasznos, ha azt szeretné, hogy egyes rekordkészletek aliasok és egyes nem aliasok legyenek.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Létrehozhatok és frissíthetek aliasrekordokat az Azure Portalról?

Igen. Az Azure REST API-k, a PowerShell, a CLI és az SDK-k együtt aliasrekordokat hozhat létre és kezelhet az Azure PORTALon.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Az aliasrekordok segítenek annak biztosításában, hogy a DNS-rekordkészletem törlődjön az alapul szolgáló nyilvános IP törlésekor?

Igen. Ez a szolgáltatás az aliasrekordok egyik alapvető képessége. Ez segít elkerülni a lehetséges kimaradások a felhasználók számára az alkalmazás.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Az aliasrekordok segítenek abban, hogy a DNS-rekordkészletem a megfelelő IP-címre frissüljön, amikor az alapul szolgáló nyilvános IP-cím megváltozik?

Igen. Ez a szolgáltatás az aliasrekordok egyik alapvető képessége. Ez segít elkerülni a lehetséges kimaradások vagy biztonsági kockázatok az alkalmazás.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Vannak-e korlátozások, ha alias rekordhalmazokat használ az A vagy AAAA rekordokhoz, hogy a Traffic Managerre mutasson?

Igen. Ha egy A vagy AAAA rekordkészlet aliasaként szeretne rámutatni egy Traffic Manager-profilra, a Traffic Manager-profilnak csak külső végpontokat kell használnia. Amikor létrehozza a külső végpontok traffic manager, adja meg a tényleges IP-címek a végpontok.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Van-e további díj az aliasrekordok használatáért?

Az aliasrekordok egy érvényes DNS-rekordkészlet minősítései. Az aliasrekordokhoz nincs további számlázás.

## <a name="use-azure-dns"></a>Az Azure DNS használata

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Az Azure DNS és egy másik DNS-szolgáltató használatával társüzemeltethetek egy tartományt?

Igen. Az Azure DNS támogatja a tartományok más DNS-szolgáltatásokkal való közös üzemeltetését.

A társüzemeltetés beállításához módosítsa a tartomány NS-rekordjait úgy, hogy mindkét szolgáltató névkiszolgálóira mutassanak. A névkiszolgáló (NS) rögzíti, hogy mely szolgáltatók kapnak DNS-lekérdezéseket a tartományhoz. Ezeket az NS-rekordokat módosíthatja az Azure DNS-ben, a másik szolgáltatóban és a szülőzónában. A szülőzóna általában a tartománynév-regisztrálón keresztül van konfigurálva. A DNS-delegálásról további információt a [DNS-tartománydelegálás című](dns-domain-delegation.md)témakörben talál.

Győződjön meg arról is, hogy a tartomány DNS-rekordjai szinkronban vannak a két DNS-szolgáltató között. Az Azure DNS jelenleg nem támogatja a DNS-zónaletöltést. A DNS-rekordokat az [Azure DNS-felügyeleti portál,](dns-operations-recordsets-portal.md)a [REST API,](https://docs.microsoft.com/rest/api/dns/)az [SDK](dns-sdk.md), a [PowerShell-parancsmag](dns-operations-recordsets.md)vagy a [CLI eszköz](dns-operations-recordsets-cli.md)használatával kell szinkronizálni.

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Delegálnom kell a tartományomat mind a négy Azure DNS-névkiszolgálóra?

Igen. Az Azure DNS-kiszolgáló minden DNS-zónához négy névkiszolgálót rendel hozzá. Ez az elrendezés a hibaelkülönítésre és a fokozott rugalmasságra szól. Az Azure DNS SLA-ra való jogosultsághoz delegálja a tartományt mind a négy névkiszolgálóra.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Milyen használati korlátok vonatkoznak az Azure DNS-re?

Az Azure DNS használatakor a következő alapértelmezett korlátozások érvényesek.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Áthelyezhetek egy Azure DNS-zónát az erőforráscsoportok vagy az előfizetések között?

Igen. A DNS-zónák áthelyezhetők az erőforráscsoportok vagy az előfizetések között.

Dns-zóna áthelyezésekénél nincs hatással a DNS-lekérdezésekre. A zónához rendelt névkiszolgálók változatlanok maradnak. A DNS-lekérdezések feldolgozása a szokásos módon történt.

A DNS-zónák áthelyezéséről további információt és útmutatást az [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)című témakörben talál.

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Mennyi ideig tart a DNS-módosítások érvénybe léptetése?

Az új DNS-zónák és DNS-rekordok általában gyorsan megjelennek az Azure DNS-névkiszolgálóiban. Az időzítés pár másodperc.

A meglévő DNS-rekordok módosítása imeddig tovább tarthat. Általában 60 másodpercen belül megjelennek az Azure DNS-névkiszolgálókon. A DNS-ügyfelek és az Azure DNS-en kívüli DNS-rekurzív feloldók DNS-gyorsítótárazása szintén befolyásolhatja az időzítést. A gyorsítótár időtartamának szabályozásához használja az egyes rekordhalmazok Élettartam -hoz -t (TTL) tulajdonságát.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Hogyan védhetem meg a DNS-zónákat a véletlen törléssel szemben?

Az Azure DNS-t az Azure Resource Manager kezeli. Az Azure DNS előnyeit élvezi az Azure Resource Manager által biztosított hozzáférés-vezérlési funkciók. A szerepköralapú hozzáférés-vezérlés szabályozza, hogy a felhasználók mely felhasználók rendelkeznek olvasási vagy írási hozzáféréssel a DNS-zónákhoz és rekordkészletekhez. Az erőforrászárolások megakadályozzák a DNS-zónák és rekordkészletek véletlen módosítását vagy törlését.

További információt a [DNS-zónák és rekordok védelme című témakörben talál.](dns-protect-zones-recordsets.md)

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Hogyan állíthatom be az SPF-rekordokat az Azure DNS-ben?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Az Azure DNS-névkiszolgálói feloldódnak az IPv6-on keresztül? 

Igen. Az Azure DNS-névkiszolgálók kettős verem. A kettős verem azt jelenti, hogy IPv4 és IPv6-címekkel rendelkeznek. A DNS-zónához rendelt Azure DNS-névkiszolgálók IPv6-címének megkereséséhez használjon egy eszközt, például az nslookup eszközt. Például: `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Hogyan állíthatok be idn-t az Azure DNS-ben?

A nemzetközivé vált tartománynevek (IDN-ek) minden DNS-nevet [a punycode](https://en.wikipedia.org/wiki/Punycode)használatával kódolnak. A DNS-lekérdezések ezeka apró kódokkal kódolt nevek használatával történnek.

Az IDN-ek konfigurálásához az Azure DNS-ben konvertálja a zóna nevét vagy a rekordkészlet nevét aprókódra. Az Azure DNS jelenleg nem támogatja a beépített átalakítást a punycode-ra vagy a kis méretűről.

## <a name="next-steps"></a>További lépések

- [További információ az Azure DNS-ről.](dns-overview.md)

- [További információ az Azure DNS magántartományokhoz való használatáról.](private-dns-overview.md)

- [További információ a DNS-zónákról és -rekordokról.](dns-zones-records.md)

- [Ismerkedés az Azure DNS-sel](dns-getstarted-portal.md).
