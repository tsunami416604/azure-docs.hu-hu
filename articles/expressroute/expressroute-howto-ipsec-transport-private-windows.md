---
title: 'Azure ExpressRoute privát társviszony-létesítés: Az IPsec átviteli módjának konfigurálása – Windows-állomások'
description: Az IPsec átviteli mód engedélyezése az Azure Windows virtuális gépek és a helyszíni Windows-állomások között az ExpressRoute-alapú magántársviszony-létesítésen keresztül, csoportházirend-azonosítók és operációs rendszerekkel.
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: 1bc33047d31262af443cddc418853fbacd88aec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022008"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>IPsec átviteli mód konfigurálása az ExpressRoute privát társviszony-létesítéshez

Ez a cikk segít az IPsec-alagutak létrehozásában átviteli módban expressRoute-alapú privát társviszony-létesítés en a Windows-t futtató Azure virtuális gépek és a helyszíni Windows-állomások között. A cikkben ismertetett lépések csoportházirend-objektumok használatával hozzák létre ezt a konfigurációt. Bár ez a konfiguráció szervezeti egységek és csoportházirend-objektumok (GRo-k) használata nélkül is létrehozható, az ousok és a csoportházirend-objektumok kombinációja megkönnyíti a biztonsági házirendek vezérlését, és lehetővé teszi a gyors skálázást. A cikkben ismertetett lépések feltételezik, hogy már rendelkezik Active Directory-konfigurációval, és ismeri a számítógép-üzemeltetők és a csoportházirend-üzemeltetők használatát.

## <a name="about-this-configuration"></a>A konfiguráció bemutatása

A konfiguráció a következő lépésekben egyetlen Azure virtuális hálózat (VNet) ExpressRoute privát társviszony-létesítéssel használja. Ez a konfiguráció azonban több Azure virtuális hálózaton és helyszíni hálózatokon is átívelhet. Ez a cikk segít az IPsec-titkosítási szabályzat meghatározásában, és alkalmazza azt az Azure virtuális gépek egy csoportjára, és a helyszíni, ugyanazon szervezeti egység részét képezik. Az Azure virtuális gépek (vm1 és vm2) és a helyszíni host1 csak a 8080-as célporttal rendelkező HTTP-forgalom titkosítását konfigurálja. Az IPsec-házirendek különböző típusai hozhatók létre a követelmények alapján.

### <a name="working-with-ous"></a>A korgyártók kal való együttműködés 

A szervezeti egységhez társított biztonsági házirend a rendszerházirend-házirenden keresztül kerül a számítógépekre. A számítógépgyártók használatának néhány előnye, ahelyett, hogy házirendeket alkalmazna egyetlen állomásra, a következő:

* A házirend szervezeti egységhez való társítása garantálja, hogy az azonos szervezeti egységhez tartozó számítógépek ugyanazokat a házirendeket kapják.
* A szervezeti egységhez társított biztonsági házirend módosítása a módosításokat a szervezeti egység összes állomására alkalmazza.

### <a name="diagrams"></a>Diagramok

Az alábbi ábra az összekapcsolást és a hozzárendelt IP-címterületet mutatja be. Az Azure virtuális gépek és a helyszíni gazdagép Windows 2016-ot futtat. Az Azure virtuális gépek és a helyszíni host1 ugyanannak a tartománynak a része. Az Azure virtuális gépek és a helyszíni állomások a DNS használatával megfelelően feloldhatják a neveket.

[![1]][1.]

Ez az ábra az ExpressRoute privát társviszony-létesítésben áthaladó IPsec-alagutakat mutatja be.

[![4]][4.]

### <a name="working-with-ipsec-policy"></a>Az IPsec-házirend kezelése

A Windows rendszerben a titkosítás az IPsec-házirendhez van társítva. Az IPsec-házirend határozza meg, hogy mely IP-forgalom van biztosítva, és hogy melyik biztonsági mechanizmus vonatkozik az IP-csomagokra.
**Az IPSec-házirendek** a következő elemekből állnak: **Szűrőlisták**, **Szűrőműveletek**és **Biztonsági szabályok**.

Az IPsec-házirend konfigurálásakor fontos a következő IPsec-házirend-terminológiát ismerni:

* **IPsec-házirend:** Szabályok gyűjteménye. Egy adott időpontban csak egy házirend lehet aktív ("hozzárendelve"),. Minden házirendnek lehet egy vagy több szabálya, amelyek mindegyike egyszerre is aktív lehet. Egy számítógéphez adott időpontban csak egy aktív IPsec-házirend rendelhető hozzá. Az IPsec-házirenden belül azonban több olyan műveletet is definiálhat, amelyek különböző helyzetekben is elvégezhetők. Az IPsec-szabályok minden egyes készlete egy szűrőlistához van társítva, amely befolyásolja a szabály hatálya szerinti hálózati forgalom típusát.

* **Szűrőlisták:** A szűrőlisták egy vagy több szűrőből álló kötegek. Egy lista több szűrőt is tartalmazhat. A szűrő határozza meg, hogy a kommunikáció engedélyezett, biztonságos vagy levan-e tiltva az IP-címtartományok, protokollok vagy akár adott protokollportok szerint. Minden szűrő egy adott feltételkészletnek felel meg; például egy adott alhálózatról egy adott célporton lévő számítógépre küldött csomagok. Ha a hálózati feltételek egy vagy több ilyen szűrőnek felelnek meg, a szűrőlista aktiválódik. Minden szűrő egy adott szűrőlistán belül van definiálva. A szűrők nem oszthatók meg a szűrőlisták között. Egy adott szűrőlista azonban több IPsec-házirendbe is beépíthető. 

* **Szűrőműveletek:** A biztonsági módszer határozza meg a biztonsági algoritmusok, protokollok és kulcsak készletét, amelyet a számítógép az ike-egyeztetések során kínál. A szűrőműveletek a biztonsági módszerek listái, preferencia szerint rangsorolva.  Amikor egy számítógép egyeztet egy IPsec-munkamenetet, a szűrőműveletek listájában tárolt biztonsági beállítás alapján fogadja el vagy küldi a javaslatokat.

* **Biztonsági szabályok:** Az IPsec-házirend kommunikációt védő és hogyan szabályozza a szabályokat. **Szűrőlista** és **szűrőműveletek segítségével** hoz létre egy IPsec-szabályt az IPsec-kapcsolat létrehozásához. Minden házirendnek lehet egy vagy több szabálya, amelyek mindegyike egyszerre is aktív lehet. Minden szabály tartalmazza az IP-szűrők listáját és az adott szűrőlistával való egyezéskor végrehajtott biztonsági műveletek gyűjteményét:
  * IP-szűrőműveletek
  * Hitelesítési módszerek
  * IP-alagút beállításai
  * Kapcsolattípusok

[![5]][5]

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy megfelel az alábbi előfeltételeknek:

* Az Active Directory-beállítások megvalósításához használható active Directory-konfigurációval kell rendelkeznie. A csoportházirend-objektumokról a Csoportházirend objektumok című [témakörben](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx)talál további információt.

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
  * Az ExpressRoute-kapcsolat létrehozásáról az [ExpressRoute-kapcsolat létrehozása](expressroute-howto-circuit-arm.md)című témakörben talál további információt. 
  * Ellenőrizze, hogy a kapcsolatszolgáltató engedélyezte-e a kapcsolatot. 
  * Ellenőrizze, hogy van-e azure-beli privát társviszony-létesítés konfigurálva a kapcsolatcsoporthoz. Az útválasztási utasításokat az útválasztási útmutató [konfigurálása](expressroute-howto-routing-arm.md) című cikkben találja. 
  * Ellenőrizze, hogy rendelkezik-e virtuális hálózattal és virtuális hálózati átjáróval, amelyet létrehozott és teljesen kiépített. Az utasításokat követve [hozzon létre egy virtuális hálózati átjárót az ExpressRoute számára.](expressroute-howto-add-gateway-resource-manager.md) Az ExpressRoute virtuális hálózati átjárója a GatewayType "ExpressRoute" típust használja, nem a VPN-t.

* Az ExpressRoute virtuális hálózati átjárót az ExpressRoute-kapcsolathoz kell csatlakoztatni. További információ: [Virtuális hálózat csatlakoztatása ExpressRoute-kapcsolathoz.](expressroute-howto-linkvnet-arm.md)

* Ellenőrizze, hogy az Azure Windows virtuális gépek telepítve vannak-e a virtuális hálózatra.

* Ellenőrizze, hogy van-e kapcsolat a helyszíni gazdagépek és az Azure virtuális gépek között.

* Ellenőrizze, hogy az Azure Windows virtuális gépek és a helyszíni állomások képesek-e a DNS használatával megfelelően feloldani a neveket.

### <a name="workflow"></a>Munkafolyamat

1. Hozzon létre egy csoportházirend-ormányt, és társítsa azt a szervezeti egységhez.
2. **IPsec-szűrőművelet**megadása .
3. **IPsec-szűrőlista definiálása**.
4. Hozzon létre egy Biztonsági **szabályokkal**rendelkező IPsec-házirendet.
5. Rendelje hozzá az IPsec-gpo-t a szervezeti egységhez.

### <a name="example-values"></a>Példaértékek

* **Domain név:** ipsectest.com

* **Ou:** IPSecOU

* **Helyszíni Windows-számítógép:** állomás1

* **Azure Windows virtuális gépek:** vm1, vm2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. Csoportházirend-csoport házirend-csoport létrehozásához

1. Szervezeti egységhez kapcsolódó új csoportházirend-kezelő létrehozásához nyissa meg a Csoportházirend kezelése beépülő modult, és keresse meg azt a szervezeti egységet, amelyhez a csoportházirend-kezelő kapcsolódik. A példában a szervezeti egység neve **IPSecOU**. 

   [![9]][9.]
2. A Csoportházirend kezelése beépülő modulban jelölje ki a szervezeti egységet, és kattintson a jobb gombbal. A legördülő menüben kattintson a **"Csoportházirend-csoportházirend-csoport házirendjének létrehozása ebben a tartományban, és itt...**

   [![10]][10]
3. Nevezze el a gpo-t egy intuitív névnek, így később könnyen megtalálhatja. A csoportházirend-csoport létrehozásához és csatolásához kattintson az **OK** gombra.

   [![11]][11.]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. A felhasználói házirend-kiszolgáló hivatkozásának engedélyezése

A szervezeti egységre való alkalmazáshoz a gpo-t nem csak a szervezeti egységhez kell kapcsolni, hanem a kapcsolatot is engedélyezni kell.

1. Keresse meg a létrehozott csoportházirend-jáminót, kattintson a jobb gombbal, és válassza a Legördülő menü **Szerkesztés** gombjára.
2. Ha a csoportházirend-ellevelet a szervezeti egységre szeretné alkalmazni, válassza az **Engedélyezve hivatkozás lehetőséget.**

   [![12]][12.]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. Az IP-szűrőművelet meghatározása

1. A legördülő menüben kattintson a jobb gombbal **az Active Directory IP-biztonsági házirendje**elemre, majd kattintson **az IP-szűrőlisták és szűrőműveletek kezelése parancsra.**

   [![15]][15]
2. A **"Szűrőműveletek kezelése"** lapon kattintson a **Hozzáadás**gombra.

   [![16]][16.]

3. Az **IP-biztonság szűrőművelet varázslóján**kattintson a **Tovább**gombra.

   [![17]][17.]
4. Nevezze el a szűrőműveletet egy intuitív névnek, hogy később megtalálhassa. Ebben a példában a szűrőművelet neve **myEncryption**. Leírást is hozzáadhat. Ezután kattintson a **Tovább gombra.**

   [![18]][18.]
5. **A biztonsági egyeztetés** lehetővé teszi a viselkedés meghatározását, ha az IPsec nem állapítható meg másik számítógéppel. Válassza **a Biztonsági egyeztetés lehetőséget,** majd kattintson a **Tovább**gombra.

   [![19]][19.]
6. Az **IPsec-et nem támogató számítógépekkel való kommunikáció** lapon jelölje be a **Nem biztonságos kommunikáció engedélyezése**jelölőnégyzetet, majd kattintson a **Tovább**gombra.

   [![20]][20]
7. Az **IP-forgalom és biztonság** lapon válassza az **Egyéni**lehetőséget, majd kattintson a **Beállítások...** gombra.

   [![21]][21]
8. Az **Egyéni biztonsági módszer beállításai** lapon válassza az **Adatintegritás és titkosítás (ESP): SHA1, 3DES**lehetőséget. Ezután kattintson **az OK gombra.**

   [![22]][22]
9. A **Műveletek kezelése** lapon láthatja, hogy a **myEncryption** szűrő hozzáadása sikeresen megtörtént. Kattintson a **Bezárás** gombra.

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. IP-szűrőlista definiálása

Hozzon létre egy szűrőlistát, amely megadja a titkosított HTTP-forgalmat a 8080-as célporttal.

1. Annak minősítéséhez, hogy milyen típusú forgalmat kell titkosítani, használjon **IP-szűrőlistát.** Az **IP-szűrőlisták kezelése** lapon kattintson a **Hozzáadás** gombra új IP-szűrőlista hozzáadásához.

   [![24]][24.]
2. A **Név:** mezőbe írja be az IP-szűrőlista nevét. Például **az azure-onpremises-HTTP8080**. Ezután kattintson a **Hozzáadás gombra.**

   [![25]][25]
3. Az **IP-szűrő leírása és tükrözött tulajdonságlapján** válassza **a Tükrözött**lehetőséget. A tükrözött beállítás megegyezik a mindkét irányban haladó csomagoknak, ami lehetővé teszi a kétirányú kommunikációt. Kattintson a **Tovább** gombra.

   [![26]][26]
4. Az **IP-forgalom forrása** lapon a **Forrás címe:** legördülő menüben válassza az **Adott IP-cím vagy Alhálózat**lehetőséget. 

   [![27]][27]
5. Adja meg az **IP-forgalom forráscímét, IP-címét vagy alhálózatát,** majd kattintson a **Tovább**gombra.

   [![28]][28]
6. Adja meg a **célcímet:** IP-cím vagy alhálózat. Ezután kattintson a **Tovább gombra.**

   [![29]][29.]
7. Az **IP-protokoll típusa** lapon válassza a **TCP**lehetőséget. Ezután kattintson a **Tovább gombra.**

   [![30]][30]
8. Az **IP-protokoll portja** lapon válassza **bármelyik portról** és **erre a portra lehetőséget:**. Írja be a **8080-as** beírást a szövegmezőbe. Ezek a beállítások csak a 8080-as célport HTTP-forgalmát határozzák meg. Ezután kattintson a **Tovább gombra.**

   [![31]][31]
9. Az IP-szűrőlista megtekintése.  Az **azure-onpremises-HTTP8080 IP-szűrőlista** konfigurációja az alábbi feltételeknek megfelelő összes forgalom titkosítását váltja ki:

   * A 10.0.1.0/24 bármely forráscíme (Azure Subnet2)
   * Bármely célcím a 10.2.27.0/25-ben (helyszíni alhálózat)
   * TCP protokoll
   * Célállomás-port 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. Az IP-szűrőlista szerkesztése

Az azonos típusú forgalom ellenkező irányú titkosításához (a helyszíni gazdagépről az Azure virtuális gépre) egy második IP-szűrőre van szükség. Az új szűrő beállításának folyamata megegyezik az első IP-szűrő beállításának folyamatával. Az egyetlen különbség a forrásalhálózat és a célalhálózat.

1. Ha új IP-szűrőt szeretne hozzáadni az IP-szűrőlistához, válassza a **Szerkesztés**lehetőséget.

   [![33]][33]
2. Az **IP-szűrőlista** lapon kattintson a **Hozzáadás**gombra.

   [![34]][34]
3. Hozzon létre egy második IP-szűrőt a következő példában megadott beállításokkal:

   [![35]][35]
4. A második IP-szűrő létrehozása után az IP-szűrőlista a következőkkel fog kinézni:

   [![36]][36]

Ha egy helyszíni hely és egy Azure-alhálózat között titkosításra van szükség egy alkalmazás védelme érdekében, a meglévő IP-szűrőlista módosítása helyett hozzáadhat egy új IP-szűrőlistát. A 2 IP-szűrőlista ugyanazon IPsec-házirendhez való társítása nagyobb rugalmasságot biztosít, mivel egy adott IP-szűrőlista bármikor módosítható vagy eltávolítható anélkül, hogy befolyásolnák a többi IP-szűrőlistát.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. IPsec biztonsági házirend létrehozása 

Hozzon létre egy IPsec-házirendet biztonsági szabályokkal.

1. Válassza ki az **Active Directory IP-biztonsági házirendjeit,** amelyek a szervezeti egységhez vannak társítva. Kattintson a jobb gombbal, és válassza **az IP-biztonsági házirend létrehozása parancsot.**

   [![37]][37]
2. Nevezze meg a biztonsági házirendet. Például **a policy-azure-onpremises**. Ezután kattintson a **Tovább gombra.**

   [![38]][38]
3. A jelölőnégyzet bejelölése nélkül kattintson a **Tovább** gombra.

   [![39]][39]
4. Ellenőrizze, hogy a **Tulajdonságok szerkesztése** jelölőnégyzet be van-e jelölve, majd kattintson a **Befejezés gombra.**

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. Az IPsec biztonsági házirendjének szerkesztése

Adja hozzá az IPsec-házirendhez a korábban konfigurált **IP-szűrőlistát** és **szűrőműveletet.**

1. A HTTP-házirend **tulajdonságainak szabályai** lapon kattintson a **Hozzáadás**gombra.

   [![41]][41]
2. Az Üdvözöljük lapon kattintson a **Tovább** gombra.

   [![42]][42]
3. A szabály lehetővé teszi az IPsec mód: alagút vagy szállítási mód definiálására.

   * Bújtatási módban az eredeti csomagot IP-fejlécek készlete foglalja be. A bújtatási mód az eredeti csomag IP-fejlécének titkosításával védi a belső útválasztási adatokat. A bújtatási mód széles körben elterjedt az átjárók között a helyek közötti VPN-forgatókönyvekben. A bújtatási mód a legtöbb esetben végpontok közötti titkosításra szolgál az állomások között.

   * A szállítási mód csak a hasznos terhet és az ESP pótkocsit titkosítja; az eredeti csomag IP-fejléce nincs titkosítva. Szállítási módban a csomagok IP-forrása és IP-címe változatlan marad.

   Jelölje **be: Ez a szabály nem ad meg bújtatást,** majd kattintson a **Tovább**gombra.

   [![43]][43]
4. **A Hálózattípusa** határozza meg, hogy melyik hálózati kapcsolat társítva van a biztonsági házirendhez. Jelölje be **az Összes hálózati kapcsolat**lehetőséget, majd kattintson a **Tovább**gombra.

   [![44]][44]
5. Jelölje ki a korábban létrehozott **IP-szűrőlistát, az azure-onpremises-HTTP8080**protokollt, majd kattintson a **Tovább**gombra.

   [![45]][45]
6. Jelölje ki a korábban létrehozott **myEncryption** szűrőműveletet.

   [![46]][46]
7. A Windows négy különböző típusú hitelesítést támogat: Kerberos, tanúsítványok, NTLMv2 és előmegosztott kulcs. Mivel tartományhoz csatlakozott állomásokkal dolgozunk, válassza az **Active Directory alapértelmezett (Kerberos V5 protokoll)** lehetőséget, majd kattintson a **Tovább**gombra.

   [![47]][47]
8. Az új házirend létrehozza a biztonsági szabályt: **azure-onpremises-HTTP8080**. Kattintson az **OK** gombra.

   [![48]][48]

Az IPsec-házirend használatához a 8080-as célport összes HTTP-kapcsolata az IPsec átviteli módot használja. Mivel a HTTP egy tiszta szöveges protokoll, a biztonsági házirend engedélyezése biztosítja az adatok titkosítását az ExpressRoute privát társviszony-létesítésén keresztül. Az Active Directory IP-biztonsági házirendje bonyolultabbkonfigurálás, mint a fokozott biztonságú Windows tűzfal, de lehetővé teszi az IPsec-kapcsolat további testreszabását.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. Az IPsec-házirend-azonosító hozzárendelése a szervezeti egységhez

1. Tekintse meg a házirendet. A biztonságicsoport-házirend definiálva van, de még nincs hozzárendelve.

   [![49]][49]
2. Ha a biztonságicsoport-házirendet hozzá szeretné rendelni a szervezeti egység **IPSecOU-jához,** kattintson a jobb gombbal a biztonsági házirendre, és válassza a **Hozzárendelés lehetőséget.**
   Minden számítógép, amely a szervezeti egységhez tartozik, a biztonságicsoport-házirendet hozzá rendeli.

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Forgalmi titkosítás ellenőrzése

A szervezeti egységen alkalmazott titkosítási gpo-ból való kivételhez telepítse az IIS-t az összes Azure-beli virtuális gépen és az állomáson1. Minden IIS testre szabott, hogy válaszoljon a HTTP-kérésekre a 8080-as porton.
A titkosítás ellenőrzéséhez telepíthet egy hálózati szimatolót (például a Wireshark-ot) a szervezeti egység összes számítógépére.
A PowerShell-parancsfájl HTTP-ügyfélként működik a 8080-as porton http-kérelmek létrehozásához:

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
A következő hálózati rögzítés a helyszíni állomás1 eredményeit jeleníti meg, és az ESP-szűrő csak a titkosított forgalomnak felel meg:

[![51]][51]

Ha a powershell-parancsfájl on-premisies (HTTP-ügyfél) futtatásához, a hálózati rögzítés az Azure virtuális gép mutat hasonló nyomkövetést.

## <a name="next-steps"></a>További lépések

Az ExpressRoute-ról további információt az [ExpressRoute gyakori kérdések című](expressroute-faqs.md)témakörben talál.

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "hálózati diagram IPsec átviteli mód expresszroute-on keresztül"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPsec érdekes forgalom"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows IPsec házirend"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "Szervezeti egység a csoportházirendben"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "hozzon létre egy csoportházirend-ormányt a szervezeti egységhez társítva"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "név hozzárendelése a szervezeti egységhez társított gpo-hoz"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "a gpo szerkesztése"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "IP-szűrőlisták és szűrőműveletek kezelése"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "add Szűrő akció"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Művelet varázsló"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "Szűrőművelet neve"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "Szűrő művelet"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "adja meg a viselkedés egy nem biztonságos kapcsolat jön létre"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "biztonsági mechanizmus"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "Egyéni biztonsági módszer"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "szűrő műveletlista"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Új IP-szűrőlista hozzáadása"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "HTTP-forgalom hozzáadása az IP-szűrőhöz"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "match csomag mindkét irányban"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "a Forrás alhálózat kiválasztása"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "Forráshálózat"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "Célhálózat"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "jegyzőkönyv"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "forrásport és célport"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "szűrőlista"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "IP-szűrőlista HTTP-forgalommal"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "Második IP-szűrő hozzáadása"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP-szűrőlista másodperces bejegyzése"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP-szűrő lista másodperces bejegyzés"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "hozza létre az IP-biztonsági házirendet"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "az IPsec-házirend neve"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "IPsec-házirend varázsló"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "szerkesztés az IPsec-házirendről"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "új biztonsági szabály hozzáadása az IPsec-házirendhez"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "új biztonsági szabály létrehozása"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "Átviteli mód"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "Hálózat típusa"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "a meglévő IP-szűrőlista kiválasztása"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "a meglévő szűrőművelet kiválasztása"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "a hitelesítési módszer kiválasztása"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "a biztonsági politika létrehozásának folyamatának vége"
[49,]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "a főházirend-jámorához kapcsolódó, de hozzá nem rendelt IPsec-politika"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "IPsec-házirend a főházirend-ár-jácasnához rendelve"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "Az IPsec titkosított forgalmának rögzítése"
