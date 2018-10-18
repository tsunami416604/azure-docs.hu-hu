---
title: IPsec átviteli módhoz konfigurálása Windows gazdagépekre – az Azure ExpressRoute privát társviszony-létesítés |} A Microsoft Docs
description: Hogyan lehet engedélyezni az Azure Windows virtuális gépek és a helyszíni Windows gazdagépekre keresztül ExpressRoute privát társviszony-létesítés csoportházirend-objektumok és szervezeti egységek közötti IPsec átviteli módhoz.
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.openlocfilehash: 1b228f0238c678c0cea4a6be2a6c3e0b929ed4d6
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394329"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Konfigurálja az ExpressRoute privát társviszony-létesítés IPsec átviteli módhoz

Ez a cikk segítségével létrehozhatja az IPsec-alagutak átviteli módhoz az expressroute-on keresztül privát társviszony-létesítés Windows rendszert futtató Azure virtuális gépek között, és a helyszíni Windows gazdagépekre. A jelen cikkben ismertetett lépések ezt a konfigurációt a csoportházirend-objektumok létrehozásához. Bár ezt a konfigurációt létrehozhatja a szervezeti egység (OU-k) használata nélkül, és a csoportházirend-objektumok (GPO), a szervezeti egységek és a csoportházirend-objektumok együttes használata leegyszerűsíti a biztonsági házirendek felügyelete, és lehetővé teszi, hogy gyorsan a méretezési csoport mentése. A jelen cikkben ismertetett lépések feltételezik, hogy már rendelkezik egy Active Directory-konfiguráció és, hogy Ön ismeri a szervezeti egységek és a csoportházirend-objektumok használatával.

## <a name="about-this-configuration"></a>A konfiguráció bemutatása

A konfiguráció a következő lépésekben egyetlen Azure virtuális hálózaton (VNet) használata az ExpressRoute privát társviszony-létesítés. Ez a konfiguráció azonban több Azure virtuális hálózatok és a helyszíni hálózatok is kiterjedhetnek. Ez a cikk segítséget nyújt egy IPSec-titkosítás házirendjének és a egy Azure virtuális gépek és gazdagépek helyszíni egyező szervezeti Egységben részét képező csoportjára alkalmazhatja azt. Titkosítás az Azure virtuális gépek (vm1 és vm2) és a helyszíni host1 csak a HTTP-forgalom célport 8080-as konfigurálnia. IPsec-szabályzat hozható létre különböző típusú igényei alapján.

### <a name="working-with-ous"></a>Szervezeti egységek használata 

A biztonsági házirend egy szervezeti Egységhez társított csoportházirend-objektumokon keresztül a számítógépeket a rendszer továbbítja. Néhány előnye az, hogy a szervezeti egységek használata helyett szabályzatok alkalmazása az egyetlen gazdagépre, a következők:

* Egy házirend társítása egy szervezeti Egységet garantálja, hogy az azonos szervezeti egységbe tartozó számítógépek minél több mint.
* Módosítja a szervezeti Egységhez társított biztonsági házirend érvényes lesz a szervezeti Egységben lévő minden gazdagép a módosításokat.

### <a name="diagrams"></a>Diagramok

Az alábbi ábrán látható, az összekapcsolás és a hozzárendelt IP-címtér. Az Azure virtuális gépek és a helyi gazdagépen futnak a Windows 2016. Az Azure virtuális gépek és a helyszíni host1 ugyanazon tartomány részét képezik. Az Azure virtuális gépek és a helyi állomások fel tudja oldani a megfelelő DNS-sel nevét.

[![1]][1]

Ez az ábra az átvitel során az IPsec-alagutak bemutatja az ExpressRoute privát társviszony-létesítés.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>IPsec-házirend használata

Windows, a titkosítás az IPSec-házirend társítva. IPsec-házirend határozza meg, melyik IP-forgalom védett és a biztonsági mechanizmust alkalmazza az IP-csomagokat.
**IPSec-házirendek** állnak a következő elemek: **listáit**, **szűrési műveletek**, és **biztonsági szabályok**.

IPsec-házirendjének konfigurálásakor fontos tudni, hogy a következő IPsec-házirend kifejezésekkel:

* **IPsec-házirend:** szabályok gyűjteménye. A házirend csak egy lehet aktív ("") bármikor rendelt adott. Az egyes házirendek rendelkezhet egy vagy több szabályt, amelyek mindegyike lehet aktív egy időben. Egy számítógépre csak egy aktív IPSec-házirend lehet hozzárendelni a megadott idő. Azonban az IPSec-házirend megadhatja több művelet, amely lehet venni különféle helyzetekben. Minden szabálykészletet IPsec társítva, amely hatással van a hálózati forgalom típusának megfelelő, amelyekre a szabály vonatkozik.

* **Lista szűrése:** szűrőlista csomag egy vagy több szűrők. Egy lista több szűrőt is tartalmaz. Szűrő határozza meg, ha a kommunikáció engedélyezett, biztonságos, vagy zárolva, az IP-címtartományok, protokollok vagy akár adott portok megfelelően. Minden szűrő megfelel egy meghatározott feltételek; Ha például küldött csomagokat egy adott alhálózatról egy adott számítógép egy adott célport. Ha hálózati körülmények egyeznek egy vagy több ezeket a szűrőket, akkor aktiválódik, a lista. Minden szűrő belül egy adott lista van meghatározva. Szűrők szűrőlista között nem lehet megosztani. Azonban egy adott szűrő lista több IPsec-házirendek beépíthető. 

* **Szűrési műveletek:** biztonsági metódus határozza meg azon algoritmusok, protokollok, és a kulcs egy számítógép kínál IKE egyeztetés során. Szűrő a műveletek olyan biztonsági módszerek, a kívánt sorrendben rangsorolt listáját.  Egy számítógép egyezteti az IPsec-munkameneteket, fogad vagy küld a műveletek szűrőlista tárolja a biztonsági beállítás alapján javaslatokat.

* **Biztonsági szabályok:** szabályok határozzák meg hogyan és mikor az IPSec-házirend védi-e a kommunikációt. Használ **szűrőlista** és **szűrési műveletek** egy IPsec-szabályt hozhat létre az IPsec-kapcsolat létrehozásához. Az egyes házirendek rendelkezhet egy vagy több szabályt, amelyek mindegyike lehet aktív egy időben. Minden egyes szabály egy IP-szűrők és a egy gyűjteményt, amely egy egyezik azzal, hogy szűrőlista alapján történik a biztonsági műveletek listáját tartalmazza:
  * IP-szűrési műveletek
  * Hitelesítési módszerek
  * IP-alagút beállítások
  * Kapcsolattípusok

[![5]][5]

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy megfelel-e a következő előfeltételek vonatkoznak:

* Rendelkeznie kell egy működő Active Directory-konfiguráció, amely segítségével a csoportházirend-beállítások megvalósításához. További információ a csoportházirend-objektumok: [csoportházirend-objektumok](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx).

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
  * ExpressRoute-kapcsolatcsoport létrehozásával kapcsolatos információkért lásd: [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md). 
  * Győződjön meg arról, hogy a kapcsolatcsoport a kapcsolatszolgáltató által engedélyezve van-e. 
  * Ellenőrizze, hogy az Azure privát társviszony-létesítést a kapcsolatcsoporthoz konfigurálva. Tekintse meg a [konfigurálja az útválasztást](expressroute-howto-routing-arm.md) cikk vonatkozó útválasztási utasításokat. 
  * Ellenőrizze, hogy egy virtuális hálózat és a egy virtuális hálózati átjáró létrehozása, és teljesen kiépítve. Kövesse az utasításokat [az ExpressRoute virtuális hálózati átjáró létrehozása](expressroute-howto-add-gateway-resource-manager.md). Az ExpressRoute virtuális hálózati átjáró a "ExpressRoute", nem gatewaytype VPN típust használja.

* Az ExpressRoute virtuális hálózati átjáró az ExpressRoute-kapcsolatcsoport csatlakoztatva kell lennie. További információkért lásd: [csatlakoztassa egy virtuális hálózatot ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md).

* Győződjön meg arról, hogy az Azure Windows virtuális gépek telepítve vannak-e a virtuális hálózathoz.

* Győződjön meg arról, hogy nincs-e a helyszíni gazdagépekre és az Azure virtuális gépek közötti kapcsolatot.

* Ellenőrizze, hogy az Azure Windows virtuális gépek és a helyi állomások használhatják a DNS megfelelően a nevek feloldásához.

### <a name="workflow"></a>Munkafolyamat

1. Hozzon létre egy csoportházirend-Objektumot, és társítsa azt a szervezeti Egységet.
2. Adja meg az IPsec **szűrőművelet**.
3. Adja meg az IPsec **listájának szűrése**.
4. Az IPSec-házirend létrehozása **biztonsági szabályok**.
5. Az IPsec csoportházirend-objektum hozzárendelése a szervezeti Egységet.

### <a name="example-values"></a>Példaértékek

* **Tartománynév:** ipsectest.com

* **Szervezeti egység:** IPSecOU

* **A helyi Windows-számítógép:** kiszolgalo1

* **Az Azure Windows virtuális gépek:** vm1, vm2

## <a name="creategpo"></a>1. Csoportházirend-objektum létrehozása

1. Hozzon létre egy új csoportházirend-objektum egy szervezeti egységhez társított, a Csoportházirend kezelése beépülő modul megnyitásához, és keresse meg a szervezeti Egységet, amelyhez a csoportházirend-objektum lesz csatolva. A példában a szervezeti egység neve **IPSecOU**. 

  [![9]][9]
2. A Csoportházirend kezelése beépülő modulban jelölje be a szervezeti Egységet, és kattintson a jobb gombbal. A legördülő listában, kattintson a "**csoportházirend-objektum létrehozása ebben a tartományban, és hivatkozás létrehozása itt...** ".

  [![10]][10]
3. Név a csoportházirend-objektum egy intuitív, így később könnyen elérhetők. Kattintson a **OK** hozhat létre, és kapcsolja a csoportházirend-Objektumot.

  [![11]][11]

## <a name="enablelink"></a>2. Engedélyezze a csoportházirendobjektum-hivatkozás

A alkalmazni a csoportházirend-Objektumot a szervezeti Egységhez, a csoportházirend-Objektumot kell nem csak kell csatolni a szervezeti egység, de a hivatkozás is engedélyezve kell lennie.

1. Keresse meg a létrehozott csoportházirend-objektum, kattintson a jobb gombbal, és válassza **szerkesztése** a legördülő listából.
2. Válassza ki a alkalmazni a csoportházirend-Objektumot a szervezeti Egységhez, **hivatkozás engedélyezése**.

  [![12]][12]

## <a name="filteraction"></a>3. Az IP-szűrési művelet megadása

1. A legördülő listából kattintson a jobb gombbal **IP-biztonsági házirendet az Active Directory**, és kattintson a **kezelése IP szűrőlista, és a műveletek szűrése...** .

  [![15]][15]
2. Az a "**kezelés szűrési műveletek**" fülre, majd **Hozzáadás**.

  [![16]][16]

3. Az a **IP-biztonsági szűrőműveletet varázsló**, kattintson a **tovább**.

  [![17]][17]
4. Név a szűrési művelet egy intuitív, hogy később meg is megtalálhatják azt. Ebben a példában a szűrőművelet nevű **myEncryption**. Hozzáadhat egy leírást is. Ezután kattintson a **Tovább** gombra.

  [![18]][18]
5. **Biztonsági egyeztetés** viselkedésének megadása, ha az IPsec nem jön egy másik számítógéppel teszi lehetővé. Válassza ki **biztonsági egyeztetés**, majd kattintson a **tovább**.

  [![19]][19]
6. Az a **Communicating számítógépekkel, amelyek nem támogatják az IPsec** lapon jelölje be **nem biztonságos kommunikáció engedélyezése**, majd kattintson a **tovább**.

  [![20]][20]
7. Az a **IP-forgalom és a biztonsági** lapon jelölje be **egyéni**, majd kattintson a **beállítások...** .

  [![21]][21]
8. Az a **egyéni biztonsági mód beállításai** lapon jelölje be **adatintegritás és a titkosítás (ESP): SHA1, a 3DES**. Kattintson a **OK**.

  [![22-es]][22-es]
9. Az a **szűrési műveletek kezelése** lapon, láthatja, hogy a **myEncryption** szűrő hozzáadása sikerült. Kattintson a **Bezárás** gombra.

  [![23]][23]

## <a name="filterlist1"></a>4. Adja meg egy IP-szűrők listája

Hozzon létre egy lista, amely meghatározza a titkosított HTTP-forgalmat a 8080-as portra.

1. Ahhoz, hogy milyen típusú adatforgalom titkosítva kell lennie, használjon egy **IP-szűrők listája**. Az a **kezelése IP listáit** lapra, majd **Hozzáadás** hozzáadása egy új IP-szűrők listája.

  [![24]][24]
2. Az a **Name:** mezőben adjon meg egy nevet az IP-szűrők listája. Ha például **azure-onpremises-HTTP8080**. Kattintson a **Hozzáadás**.

  [![25]][25]
3. Az a **IP-szűrő leírása és a tükrözött tulajdonság** lapon jelölje be **tükrözött**. A tükrözött beállítás mindkét irányban csomagok illeszkedik, amely kétirányú kommunikációt tesz lehetővé. Ezután kattintson a **Next** (Tovább) gombra.

  [![26]][26]
4. A a **IP-forgalom forrása** lapon a a **forráscím:** legördülő menüben válassza a **egy adott IP-cím vagy alhálózat**. 

  [![27]][27]
5. Adja meg a forrás címe **IP-címet vagy alhálózatot:** az IP-forgalom, majd kattintson **tovább**.

  [![28]][28]
6. Adja meg a **célcím:** IP-címet vagy alhálózatot. Ezután kattintson a **Tovább** gombra.

  [![29]][29]
7. Az a **IP protokoll típusa** lapon jelölje be **TCP**. Ezután kattintson a **Tovább** gombra.

  [![30]][30]
8. Az a **IP protokoll Port** lapon jelölje be **bármely portról** és **erre a portra:**. Típus **8080-as** a szövegmezőben. Ezek a beállítások határozzák meg, csak a HTTP-forgalmat a 8080-as portra lesznek titkosítva. Ezután kattintson a **Tovább** gombra.

  [![31]][31]
9. Tekintse meg az IP-szűrők listája.  Az IP-szűrők listája konfigurációjának **azure-onpremises-HTTP8080** elindítja a következő feltételeknek megfelelő összes forgalom titkosítása:

  * Az (Azure Subnet2) 10.0.1.0/24 bármely forrás címe
  * Bármely 10.2.27.0/25 (a helyi alhálózat) a cél címe
  * A TCP protokoll
  * Cél 8080-as porton

  [![32]][32]

## <a name="filterlist2"></a>5. Az IP-szűrőlista szerkesztése

Azonos típusú (a gazdagépről a helyszínen az Azure virtuális gépekhez) ellenkező irányban forgalom titkosításához szüksége van egy második IP-szűrő. Az új szűrő beállításának folyamatán, amellyel az első IP-szűrő beállítása ugyanezzel a folyamattal történik. Az egyetlen különbség a forrásoldali alhálózat és a cél alhálózat.

1. Az IP-szűrők listája egy új IP-szűrő hozzáadásához válassza **szerkesztése**.

  [![33]][33]
2. Az a **IP-szűrők listája** kattintson **Hozzáadás**.

  [![34]][34]
3. Hozzon létre egy második IP-szűrő, az alábbi példában szereplő beállításokkal:

  [![35]][35]
4. Miután létrehozta a második IP-szűrő, az IP-szűrők listája a következőképpen jelenik meg:

  [![36]][36]

Között egy helyszíni helyhez és a egy Azure-alhálózaton megvédeni az alkalmazásokat, ahelyett, hogy módosítja a meglévő IP-szűrők listája, kötelező titkosítás esetén inkább adhat hozzá egy új IP-szűrők listája. 2 IP társítása az ugyanazon IPSec-házirendjének szűrőlista jobb rugalmasságot biztosít, mivel egy adott IP-szűrő lista módosíthatják vagy a más IP-címszűrők listáit befolyásolása nélkül bármikor eltávolítva.

## <a name="ipsecpolicy"></a>6. Az IPSec-biztonsági házirend létrehozása 

Hozzon létre egy IPSec-házirend biztonsági szabályokat.

1. Válassza ki a **IPSecurity házirendek az Active directory** társítva a szervezeti Egységet. Kattintson a jobb gombbal, és válassza ki **IP-biztonsági házirend létrehozása**.

  [![37]][37]
2. A biztonsági szabályzat neve. Ha például **házirend-azure-onpremises**. Ezután kattintson a **Tovább** gombra.

  [![38]][38]
3. Kattintson a **tovább** anélkül, hogy a jelölőnégyzetet.

  [![39]][39]
4. Ellenőrizze, hogy a **tulajdonságainak szerkesztése** jelölőnégyzet van kiválasztva, és kattintson **Befejezés**.

  [![40]][40]

## <a name="editipsec"></a>7. Az IPSec-biztonsági házirend szerkesztése

Az IPSec-házirend hozzáadása a **IP-szűrők listája** és **szűrőművelet** korábban beállított.

1. A HTTP-házirend tulajdonságainak **szabályok** lapra, majd **Hozzáadás**.

  [![41]][41]
2. Az üdvözlőoldalon kattintson **tovább**.

  [![42]][42]
3. A szabály lehetővé teszi, hogy az IPSec-mód meghatározása: alagúton vagy átviteli üzemmódban.

  * Bújtatás módban az eredeti csomaggal egy IP-fejlécei készlete által van beágyazva. Bújtatási mód az eredeti csomag IP-fejlécének titkosításával védelmet biztosít a belső információkat. Bújtatási mód közötti átjárók a helyek közötti VPN-forgatókönyvek széles körben valósítja meg. A legtöbb esetben a gazdagép között teljes körű titkosítást használja bújtatás módban van.

  * Átvitelt titkosítja, csak a tartalom és az ESP bemutató; az eredeti csomaggal az IP-fejléc nem titkosított. Átviteli módban IP forrás- és IP-csomagok nem változnak.

  Válassza ki **Ez a szabály nem ad meg egy alagutat**, és kattintson a **tovább**.

  [![43]][43]
4. **Hálózattípus** határozza meg, amely a hálózati kapcsolat hozzárendeli a biztonsági házirendnek. Válassza ki **minden hálózati kapcsolat**, és kattintson a **tovább**.

  [![44]][44]
5. Válassza ki a korábban létrehozott IP-szűrők listája **azure-onpremises-HTTP8080**, és kattintson a **tovább**.

  [![45]][45]
6. Válassza ki a meglévő szűrőművelet **myEncryption** , amelyet korábban hozott létre.

  [![46]][46]
7. Windows hitelesítések négy különböző típust támogat: a Kerberos, tanúsítványok, NTLMv2, és előre megosztott kulcs. Mivel jelenleg is dolgozunk, a tartományhoz csatlakoztatott gazdagépek, jelölje ki **(Kerberos V5 protokoll) az Active Directory alapértelmezett**, és kattintson a **tovább**.

  [![47]][47]
8. Az új szabályzat a biztonsági szabályt hoz létre: **azure-onpremises-HTTP8080**. Kattintson az **OK** gombra.

  [![48]][48]

Az IPSec-házirend a cél IPsec átviteli módhoz használja a 8080-as porton az összes HTTP-ügyfélkapcsolatokat igényel. Mivel a HTTP protokoll titkosítatlan szövegként, biztosítja a biztonsági házirend engedélyezve van, adat titkosítva van, az ExpressRoute privát társviszony-létesítésen keresztül átvitele esetén. IP-biztonsági házirendet az Active Directory bonyolultabb, mint a fokozott biztonságú Windows tűzfal konfigurálásához, de a további testre szabhatja az IPsec-kapcsolat lehetővé teszi.

## <a name="assigngpo"></a>8. Az IPsec csoportházirend-objektum hozzárendelése a szervezeti egység

1. Tekintse meg a szabályzatot. A biztonsági csoport házirend van megadva, de még nincs hozzárendelve.

  [![49]][49]
2. A biztonsági csoport házirend hozzárendelése a szervezeti egység **IPSecOU**, kattintson a jobb gombbal a biztonsági házirend és a választott **hozzárendelése**.
Minden számítógép munkamegosztást tartozik, a szervezeti egység fog rendelkezni a hozzárendelt biztonsági csoportházirend.

  [![50]][50]

## <a name="checktraffic"></a>Ellenőrizze a forgalom titkosításához

Tekintse meg a titkosítást a csoportházirend-Objektumot alkalmazza a szervezeti Egységben található, telepítse az IIS minden Azure virtuális gépeken, és a host1. Minden IIS van testre szabva, hogy a HTTP-kérelmekre a 8080-as porton válaszoljon.
Annak ellenőrzéséhez, titkosítás, egy hálózatelemző (például a Wireshark) a szervezeti egység összes számítógépének is telepíthető.
Egy powershell-parancsprogram készítése a HTTP-kérelmekre a 8080-as port HTTP-ügyfelek módon működik:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
A következő hálózati rögzítési mutatja be, a helyszíni host1 eredményei csak a titkosított forgalom megfelelő szűrő ESP megjelenítéséhez:

[![51]][51]

Ha futtatja a powershell parancsfájl a-premisies (HTTP-alapú), az Azure-beli virtuális gépen a hálózati rögzítés hasonló nyomot jeleníti meg.

## <a name="next-steps"></a>További lépések

További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "hálózati Diagram IPsec átviteli módhoz expressroute-on keresztül"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "érdekes forgalom IPsec"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows IPsec-házirend"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "a csoportházirendet a szervezeti egység"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "a szervezeti Egységhez társított csoportházirend-objektum létrehozása"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "név hozzárendelése az a szervezeti Egységhez társított csoportházirend-objektum"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "a csoportházirend-objektum szerkesztéséhez"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "kezelése IP-címszűrők listáit, és a műveletek szűrése"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "szűrési művelet hozzáadása"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "művelet varázsló"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "szűrési művelet neve"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "művelet szűrése"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "adja meg, hogy a viselkedését egy nem biztonságos kapcsolat létrejött"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "biztonsági mechanizmus"
[22-es]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "egyéni biztonsági módszer"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "szűrőlista művelet"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "adjon hozzá egy új IP-szűrők listája"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "hozzáadása HTTP-forgalmat, az IP-szűrő"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "egyezés csomag mindkét irányban"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "a forrás-alhálózat kiválasztása"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "hálózati forrás"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "célhálózat"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "protokoll"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "forrás port és a célport"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "szűrőlista"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "HTTP-forgalom az IP-szűrők listája"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "egy második IP-szűrő hozzáadása"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP-szűrő lista-második bejegyzés"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP-szűrő lista-második bejegyzés"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "az IP-biztonsági szabályzat létrehozása"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "az IPSec-házirend neve"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "IPsec-házirend varázsló"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "az IPSec-házirend szerkesztése"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "új szabály hozzáadása az IPSec-házirend"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "hozzon létre egy új biztonsági szabályt"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "átviteli mód"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "hálózati típusa"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "kiválasztása a meglévő IP-szűrők listája"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "meglévő szűrése művelet kiválasztása"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "a hitelesítési módszer kiválasztása"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "a biztonsági házirend létrehozási folyamat végén"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "IPSec-házirend a csoportházirend-objektumhoz kapcsolódó, de nincs hozzárendelve"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "a csoportházirend-objektumhoz rendelt IPSec-házirend"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "rögzítése az IPSec-titkosított forgalom"
