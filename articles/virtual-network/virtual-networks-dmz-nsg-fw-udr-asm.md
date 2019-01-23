---
title: Szegélyhálózat (DMZ) Példa – semleges egy tűzfal, az udr-t és az NSG-t a hálózatok védelme |} A Microsoft Docs
description: Semleges zóna kialakítása tűzfallal, felhasználó által definiált útválasztás (UDR) és a hálózati biztonsági csoportok (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: 36d6733ddc73ace2026ea838cf8f701db95469e6
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448466"
---
# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Például: 3 – semleges egy tűzfal, az udr-t és az NSG-t a hálózatok védelme
[Térjen vissza a biztonsági határ ajánlott eljárások lap][HOME]

Ebben a példában egy DMZ-t hoz létre tűzfalat, négy windows-kiszolgálók, felhasználói meghatározott útválasztást, az IP-továbbítás és hálózati biztonsági csoportok. Azt is végigvezetik az adja meg az egyes lépések jobban megértheti a megfelelő parancsokat. Emellett van egy forgalom forgatókönyv részt, hogy egy részletes részletes hogyan halad a forgalom a rétege a DMZ-n keresztül. Végül az hivatkozások szakaszban, a teljes kódját és utasítás összeállításához, teszteléséhez, és kísérletezzen a különböző forgatókönyvekben ebben a környezetben. 

![Kétirányú Szegélyhálózat nva-t, az NSG-t és az udr-t][1]

## <a name="environment-setup"></a>Környezet beállítása
Ebben a példában van egy előfizetést, amely a következőket tartalmazza:

* Három felhőszolgáltatások: "SecSvc001", "FrontEnd001" és "BackEnd001"
* Virtuális hálózat "CorpNetwork", és három alhálózatot: "SecNet", "Előtér" és "Háttér"
* Ebben a példában a tűzfal egy hálózati virtuális készüléket a SecNet alhálózathoz csatlakoztatott
* A Windows Server, amely egy webalkalmazás-kiszolgáló ("IIS01") jelöli.
* Vissza az alkalmazást képviselő két windows-kiszolgálók záró kiszolgálók ("AppVM01", "AppVM02")
* A Windows server DNS-kiszolgáló ("DNS01") jelölő

Az alábbi references szakaszában van egy PowerShell-parancsprogram, amely a fent leírt környezetben a legtöbb fog létrehozni. A virtuális gépek és virtuális hálózatok létrehozásához kell elvégeznie a példaszkript, bár nem ismerteti a jelen dokumentum részletesen.

A környezet létrehozásához:

1. Mentse a hálózati konfigurációs xml-fájlt a references szakaszában szereplő (frissített nevét, helyét és IP-címeket az adott esethez)
2. Frissítse a felhasználói változók a parancsfájl felel meg a környezetben, a parancsfájl az, hogy futtatni (előfizetés, szolgáltatásnevek stb.)
3. Hajtsa végre a szkriptet a PowerShellben

**Megjegyzés**: A régió, a PowerShell-parancsfájl esetében meg kell egyeznie a hálózati konfigurációs xml-fájl esetében.

A szkript sikeres futtatása után a következő utáni lépéseket lehet tenni:

1. A tűzfalszabályok beállítása, ez foglalkozik című szakaszt az alábbi: Tűzfal szabály leírása.
2. Igény szerint a hivatkozások szakaszban két parancsfájlt a webkiszolgáló és a egy egyszerű webalkalmazást, hogy a tesztelés a DMZ-konfigurációval rendelkező alkalmazáskiszolgáló beállításához vannak.

Miután a szkript sikeresen lefutott a tűzfal-szabályokat kell végrehajtani, ez című szakasz tárgyalja: Tűzfalszabály.

## <a name="user-defined-routing-udr"></a>Felhasználó által definiált útválasztás (UDR)
Alapértelmezés szerint a következő rendszerútvonalak vannak meghatározva:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

A megadott cím prefix(es) (például a befejezést vnet VNet attól függően, hogyan mindegyik megadott virtuális hálózat van meghatározva) adott hálózat virtuális hálózat a VNETLocal mindig. A fennmaradó rendszerútvonalak statikus, és a fenti alapértelmezett.

Prioritás, mint az útvonal a leghosszabb előtag Match (LPM) metódus használatával dolgozza fel, így a nejvíce specifické útvonal a táblázatban megadott címre alkalmazni.

Forgalom (például hogy DNS01 kiszolgáló 10.0.2.4 cím) a helyi hálózat (10.0.0.0/16) számára készült, ezért a 10.0.0.0/16 útvonal miatt a cél virtuális hálózat között lesz irányítva. Más szóval a 10.0.2.4 cím, a 10.0.0.0/16 útvonal a nejvíce specifické útvonal, annak ellenére, hogy a 10.0.0.0/8, és a 0.0.0.0/0 is lehetett alkalmazni, de mivel kisebb adott nincs hatással a forgalmat. Így a forgalom 10.0.2.4 cím lenne egy következő ugrási típusú a helyi virtuális hálózat rendelkezik, és egyszerűen átirányítása a cél.

Ha a forgalom történő készült 10.1.1.1 például a 10.0.0.0/16 útvonal nem lenne a alkalmazni, azonban a 10.0.0.0/8 nejvíce specifické lenne, és ez a forgalom lenne eldobott ("fekete furatos"), mert a következő ugrás má hodnotu Null. 

Ha a cél a VNETLocal előtagok vagy a Null-előtagok valamelyik nem lett alkalmazva, majd követnie a legkevésbé egyedi irányíthatja a 0.0.0.0/0, és ilyenkor úgy irányítja az interneten a következő ugrás és így az Azure internetes peremhálózatra ki.

Ha az útvonaltáblában lévő két azonos előtagokat, az alábbiakban látható a sorrend figyelembe vételével az útvonalak "forrás" attribútum alapján:

1. "VirtualAppliance" = egy felhasználó által definiált útvonal manuálisan adja hozzá a táblázathoz
2. "VPN" = egy dinamikus útválasztási (hibrid hálózatok együtt használva BGP-), a dinamikus hálózati protokoll által hozzáadott, ezeket az útvonalakat idővel változhat, a dinamikus protokoll automatikusan megjelennek-e módosítások társhálózatban
3. "Alapértelmezett" = a Rendszerútvonalak, a helyi virtuális hálózat és a statikus bejegyzések az útvonal a fenti táblázatban látható módon.

> [!NOTE]
> Felhasználó által megadott útválasztás (UDR) mostantól használhatja az expressroute-tal és kényszerítheti a kimenő és bejövő VPN-átjárók közötti forgalom átirányítását egy hálózati virtuális készüléket (NVA).
> 
> 

#### <a name="creating-the-local-routes"></a>A helyi útvonalak létrehozása
Ebben a példában két útválasztási táblázat van szükség, egyet az előtér- és Háttérportokat alhálózatok. Minden tábla be vannak töltve az adott alhálózat megfelelő statikus útvonalakat. Jelen példában mindegyik tábla rendelkezik három útvonalakat:

1. A következő ugrás a helyi alhálózat forgalmának meghatározva, amely engedélyezi a helyi alhálózat forgalmának, hogy a tűzfal megkerülését
2. A következő ugrás definiálva, a tűzfal a virtuális hálózati forgalom felülírja az alapértelmezett szabályt, amely lehetővé teszi a helyi virtuális hálózatok közötti forgalom közvetlenül továbbítani
3. A következő ugrás definiálva, a tűzfal az összes fennmaradó forgalmat (0/0)

Ha az útválasztási táblázatok jönnek létre, azok alhálózatok vannak kötve. Az előtérbeli alhálózat útválasztási táblázatához, miután létrehozott és van kötve az alhálózat kell kinéznie:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


Ebben a példában a következő parancsokat használja az útválasztási táblázat létrehozása, és adjon hozzá egy felhasználó által megadott útvonalat, majd társítanak az útválasztási táblázatot egy alhálózathoz (Megjegyzés; alatti kiadásától kezdve egy dollárjelet elemek (pl.: $BESubnet) vannak a felhasználó által definiált változókat a parancsfájl a referencia a dokumentum szakaszát):

1. Először az útválasztási alaptábla kell létrehozni. Ez a kódrészlet bemutatja a tábla a háttérrendszer alhálózatának. A parancsfájl egy kapcsolódó tábla is létrejön az előtér-alhálózatot.
   
     New-AzureRouteTable -Name $BERouteTableName `
   
         -Location $DeploymentLocation `
         -Label "Route table for $BESubnet subnet"
2. Az útvonaltábla létrehozása után a megadott felhasználó által megadott útvonalakat is hozzáadhatók. A jelen snipped a virtuális berendezés (egy változót, a [0], $VMIP használt adja át a virtuális készülék a parancsfájl a korábbi létrehozásakor rendelt IP-cím) keresztül minden forgalmat (0.0.0.0/0) lesznek irányítva. A parancsfájl egy megfelelő szabályt is létrejön az előtér-táblázatban.
   
     Get-AzureRouteTable $BERouteTableName | `
   
         Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
         -NextHopType VirtualAppliance `
         -NextHopIpAddress $VMIP[0]
3. A fenti útvonal bejegyzés felülírják az alapértelmezett "0.0.0.0/0" útvonalat, de továbbra is meglévő alapértelmezett 10.0.0.0/16 szabály, amely lehetővé tenné, hogy a forgalom közvetlenül a rendeltetési helyre és nem a hálózati virtuális berendezés továbbítani a Vneten belül. Helyes-e ezt a viselkedést az alábbi szabály hozzá kell adni.
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
4. Ezen a ponton választási lehetőség van kell tenni. Az a fenti két útvonalakat az értékelés, még akkor is, forgalmat egy egyetlen alhálózaton belül a tűzfal az összes forgalmat irányítja át. Ez előfordulhat, hogy lehet megfelelő, de ahhoz, hogy a forgalom egy helyileg, a tűzfal bevonása nélkül irányíthatja a harmadik alhálózatban, nagyon adott szabályokat is hozzáadhatók. Ezt az útvonalat, amely bármilyen címet a helyi alhálózaton rendkívül egyszerűen destine állapotok közvetlenül útvonal létezik (NextHopType = VNETLocal).
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
5. Végül az útválasztási tábla létrehozott és feltöltött egy felhasználó által megadott útvonalakat az a tábla most kell kötni egy alhálózathoz. A parancsfájl az előtér-útválasztási tábla is van kötve az előtér-alhálózatot. Íme a kötés parancsfájlt a háttér-alhálózat.
   
     Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
   
        -SubnetName $BESubnet `
        -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>IP-továbbítás
Egy kiegészítő funkció, amellyel udr-t, az IP-továbbítást. Ez az egy-egy virtuális berendezés, amely lehetővé teszi, hogy a készülék nem kifejezetten címzett forgalom fogadása és majd továbbítsa a forgalmat a végső rendeltetési beállítást.

Tegyük fel AppVM01 érkező kérést küld a DNS01 kiszolgálóra, ha udr-t kellene átirányítása Ez a tűzfal. Az IP-továbbítás engedélyezve van a forgalom a DNS01 cél (10.0.2.4 cím) fog fogadja el a készülék (10.0.0.4) és a végső rendeltetési (10.0.2.4 cím), majd továbbítja. Anélkül, hogy az IP-továbbítás engedélyezve van a tűzfalon forgalom lenne nem fogadja el a készülék annak ellenére, hogy az útvonaltáblát tartalmaz a következő ugrás a tűzfalon. 

> [!IMPORTANT]
> Rendkívül fontos, hogy az IP-továbbítás engedélyezése a felhasználó meghatározott útválasztás együtt.
> 
> 

Az IP-továbbítás beállításához egyetlen paranccsal és virtuális gépek létrehozáskor teheti meg. Az ebben a példában a folyamat a kódtöredék a szkript végén, és az UDR-parancsokkal csoportosítva:

1. A Virtuálisgép-példány, amely a virtuális berendezéshez, a tűzfal ebben az esetben nevezik, és az IP-továbbítás engedélyezése (Megjegyzés; piros kiadásától kezdve egy dollárjelet tetszőleges elemre (pl.: $VMName[0]) a referencia szakasz ebben a dokumentumban szereplő parancsfájl egy felhasználó által definiált változó. Zárójelek között, a [0], a nulla képviseli az első virtuális gép, virtuális gépek, a módosítás nélkül használható példa parancsfájl a tömbben, az első virtuális gép (VM-0) kell lennie a tűzfalon):
   
     Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
   
        Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Hálózati biztonsági csoportok (NSG)
Ebben a példában egy NSG-csoport beépített, és aztán betölti a egyetlen szabállyal. Ez a csoport majd kötött csak az előtérbeli és háttérbeli alhálózatok (nem a SecNet). Deklaratív a következő szabály létrehozása folyamatban van:

1. Bármely (minden porthoz) az internetről forgalmat a teljes virtuális hálózathoz (az összes alhálózat) megtagadva

Bár ebben a példában az NSG-ket használja, azt a fő célja manuális kiszolgálóhiba elleni védelmet a másodlagos összetevővel. Szeretnénk vagy az internetről érkező összes bejövő forgalom blokkolása az előtérbeli vagy háttérbeli alhálózatokkal, a forgalom csak áthaladhat a tűzfalhoz a SecNet alhálózat (és a majd ha megfelelő be az előtérbeli vagy a háttérbeli alhálózatok). Ezenkívül az UDR szabályoknak helyen, tette, hogy az előtérbeli és háttérbeli alhálózatokra adatforgalmat lenne átirányítja ki (UDR) nak köszönhetően a tűzfalon. A tűzfal itt jelennének meg ezt az aszimmetrikus folyamatként, és a kimenő forgalom volna eldobni. Ezért az alábbi három réteg védelmét az előtér- és Háttérportokat alhálózatok; biztonsági 1.) a FrontEnd001 és BackEnd001 nyitott végpontok felhőbeli szolgáltatásokat, 2) az NSG-k tagadja meg a forgalom az internetről, 3) a tűzfal eldobása aszimmetrikus forgalmat.

Ebben a példában a hálózati biztonsági csoport kapcsolatos egy érdekes pont, hogy csak egy szabályt, az alább látható benne: Ez a teljes virtuális hálózatot, amely hozzáadja a biztonság – alhálózati internetes forgalom. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

Azonban az NSG-t csak az előtérbeli és háttérbeli alhálózataihoz van kötve, mivel a szabály nem feldolgozott forgalom a bejövő biztonsági az alhálózathoz. Ennek eredményeképpen annak ellenére, hogy a Hálózatibiztonságicsoport-szabály nincs internetes forgalmat bármilyen cím felirat látható a virtuális hálózaton, mivel az NSG-t soha nem lett kötve a biztonság – alhálózati, forgalom biztonsági az alhálózathoz.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Tűzfalszabályok
A tűzfalon továbbítási szabályokat kell létrehozni. Mivel a tűzfal blokkolja-e vagy az összes bejövő, kimenő továbbítás és belüli virtuális hálózatok közötti forgalom számos tűzfalszabályok van szükség. Minden bejövő forgalom is, kattintson a biztonsági szolgáltatás nyilvános IP-cím (a különböző portok), a tűzfal feldolgozásra. Ajánlott eljárás, hogy a logikai folyamatokban az alhálózatok beállítása előtt a diagramon, és később újragyártási tűzfalszabályok elkerülése érdekében. Az alábbi ábra az ebben a példában a tűzfalszabályok logikai nézetében:

![A tűzfalszabályok logikai nézetében][2]

> [!NOTE]
> A használt hálózati virtuális berendezésen alapján, a felügyeleti portokat függ. Ebben a példában a Barracuda NextGen tűzfal hivatkozott amely 801, 807 és a 22-es portot használ. Tekintse meg a berendezés szállítói dokumentációban találja a pontos portokat használja az eszköz felügyeletére használható.
> 
> 

### <a name="logical-rule-description"></a>Logikai szabály leírása
A fenti logikai ábrán a biztonság – alhálózati nem látható, mivel a tűzfalat, hogy az egyetlen erőforrás az alhálózaton, és a tűzfalszabályokat, és hogyan azok logikailag engedélyezik vagy megtagadják a forgalom adatfolyamait, és nem a tényleges irányított elérési ezen az ábrán látható. Is, a kiválasztott magasabbak, az RDP-forgalmat a külső portok előre portok (8014 – 8026), és némileg igazodnak a helyi IP-cím az egyszerűbb olvashatóság érdekében a két utolsó bájt kijelölve (pl. helyi kiszolgáló címe 10.0.1.4 cím – ez kapcsolódik a külső portra 8014), viszont magasabb nem ütköző portokat használható.

Ebben a példában 7 típusú szabályok van szükségünk, ezek a szabályok típusai a következők leírtak szerint:

* Külső szabályok (bejövő forgalom) számára:
  1. Felügyeleti tűzfalszabályt: Az alkalmazás átirányítási szabály lehetővé teszi, hogy a forgalom számára a felügyeleti portokat a hálózati virtuális berendezéshez.
  2. RDP-szabályok (a minden windows server): Ezek a négy szabályok (kiszolgálónként egyet) lehetővé teszi az egyes kiszolgálók RDP-n keresztül. Ez sikerült is lehet egyszerre kezelni, a használt hálózati virtuális berendezésen függően egy szabályba.
  3. Alkalmazás-forgalomra vonatkozó szabályokat: Két alkalmazás forgalmi szabályok vannak, az első az az előtérbeli webes forgalom számára, és a második a háttér-forgalom (például web server adatrétegbeli). Ezek a szabályok konfigurációja lesz a hálózati architektúra (ahol a kiszolgálók kerülnek) függenek adatforgalom és a folyamatok (melyik irányba halad a forgalom és az azoknak a portoknak).
     * Az első szabály lehetővé teszi az alkalmazás tényleges forgalom elérhesse a kiszolgáló. A többi szabály engedélyezi a biztonsági, felügyeleti, stb., míg az alkalmazás szabályok, a külső felhasználók vagy a szolgáltatások eléréséhez (ok) lehetővé teszi. Ebben a példában a 80-as porton van egy webkiszolgálón, így egyetlen tűzfalszabályt alkalmazás átirányítja a bejövő forgalmat a külső IP-cím, a webes kiszolgálók belső IP-címre. Az átirányított forgalom munkamenet lenne NAT lenni a belső kiszolgálót.
     * A második alkalmazás forgalmi szabály a háttérben szabály lehetővé teszi a webkiszolgáló kommunikáljon a AppVM01 kiszolgáló (de nem AppVM02) bármely porton keresztül.
* Belső szabályokat (belüli virtuális hálózatok közötti forgalom)
  1. Kimenő Internet-szabályhoz: Ez a szabály lehetővé teszi a kiválasztott hálózatok átadása olyan hálózati forgalmát. Ez a szabály általában az alapértelmezett szabály már a tűzfalon, de letiltott állapotban. Ebben a példában ez a szabály engedélyezni kell.
  2. DNS Rule: Ez a szabály lehetővé teszi, hogy csak (53-as port) a DNS-forgalom számára a DNS-kiszolgáló. Ebben a környezetben, a háttérkiszolgáló a Frontend alhálózatból legtöbb forgalmat blokkol Ez a szabály kifejezetten engedélyezi az DNS bármely helyi alhálózatról.
  3. Az alhálózat alhálózati szabály: Ez a szabály az összes olyan kiszolgálónak engedélyezi a háttérrendszer alhálózat bármely az előtér-alhálózat (de nem fordítva) a kiszolgálóhoz való csatlakozáshoz.
* Hibamentes (forgalomra vonatkozó szabály, amely a fentiek valamelyike nem felel meg):
  1. Minden forgalmi szabály megtagadása: A végső szabályát (tekintetében prioritás) kell lennie, és mint ilyen, ha a forgalom nem sikerül megfelelően a fenti szabály, a rendszer eldobja a szabály által. Ez az alapértelmezett szabály, és általában aktiválva, nem módosított előfizetésekben általában szükség van.

> [!TIP]
> A második alkalmazás forgalmának szabályát bármely porton engedélyezve van a egyszerűen, az ebben a példában a valós forgatókönyvekben a legtöbb adott port és címtartomány használandó csökkenteni a támadási felületet, ez a szabály.
> 
> 

<br />

> [!IMPORTANT]
> Miután az összes fenti szabályt hoz létre, fontos tekintse át a forgalom lesz engedélyezett vagy letiltott igény szerint győződjön meg arról, hogy minden szabály prioritását. Ebben a példában a szabályok prioritási sorrendben vannak. Rosszul rendezett szabályok miatt a tűzfalon kívüli zárolni könnyebbé vált. Minimális magát a tűzfalat felügyeleti mindig legyen a abszolút legmagasabb prioritású szabályt.
> 
> 

### <a name="rule-prerequisites"></a>A szabály Előfeltételek
A virtuális gép fut, a tűzfal egy előfeltétel olyan nyilvános végpontok. A forgalom feldolgozása a tűzfalon a megfelelő nyilvános végpontokat nyitva kell lennie. Három típusú forgalom ebben a példában; 1.) a felügyeleti forgalom vezérlő a tűzfal és tűzfalszabályok, 2) RDP-forgalmat a windows-kiszolgálók és a 3.) alkalmazás forgalom szabályozására. Ezek a forgalom típusainak, a felső sarokban a három oszlopot fele a tűzfalszabályok a fenti logikai nézetében.

> [!IMPORTANT]
> A kulcs takeway, hogy ne feledje, hogy **összes** adatforgalom a tűzfalon keresztül fog érkezni. Ezért a IIS01 kiszolgálóhoz a távoli asztal annak ellenére, hogy a végfelhasználók az előtérbeli felhőszolgáltatás és az előtérbeli alhálózat a kiszolgálóhoz való azt fogja kell az RDP-hez 8014 porton a tűzfal, és engedje, hogy a tűzfal a IIS01 RDP Por irányíthatja a cégen belül az RDP-kérelem t. Az Azure portal "Csatlakozás" gomb nem fog működni, mert nincs közvetlen RDP-elérési útját IIS01 (szerint a portálon látható). Ez azt jelenti, hogy az internetről érkező összes kapcsolat lesz a szolgáltatás és a egy Port, pl. secscv001.cloudapp.net:xxxx (referencia a fenti ábra külső Port és a belső IP-cím és Port, a hozzárendelés).
> 
> 

A végpont segítségével lehet megnyitni, vagy a virtuális gép létrehozásakor, vagy build közzététele a példa parancsfájl kész, és ez a kódrészlet az alább látható módon (Megjegyzés; dollárjelet bármely elem kezdődő (pl.: $VMName[$i]) a referencia-rész a parancsfájl a felhasználó által definiált változó Ez a dokumentum n. A "$i" zárójelben [$i], a virtuális tömböt adott virtuális gép tömb számát jelenti):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Bár nem jelenik meg világosan itt oka az, hogy vannak-e a változókat, de a végpontok használata **csak** nyit meg a biztonsági Felhőszolgáltatás. Ez azért szükséges, hogy az összes bejövő forgalom kezelése (irányítva, NAT rendelkezett, kihagyva) a tűzfal.

A tűzfal kezelése és a szükséges konfigurációk létrehozásához a számítógépen telepíteni kell egy kezelési ügyfél. Tekintse át a dokumentáció a tűzfalon (vagy más NVA) szállító az eszköz kezelésére. Ez a szakasz és a következő szakaszban, a tűzfal-szabályok létrehozását, további része, a tűzfal a szállító felügyeleti ügyfél (tehát nem az Azure Portalon vagy a PowerShell) keresztül lesz konfigurálását.

Ügyfél letöltése és csatlakozik a Barracuda, ebben a példában használt vonatkozó utasítások itt található: [Barracuda NG rendszergazda](https://techlib.barracuda.com/NG61/NGAdmin)

Miután bejelentkezett, a tűzfal az alakzatot, de a tűzfal-szabályok létrehozása előtt, vannak-e két előfeltétel objektum osztály, amely megkönnyíti a szabályok könnyebb; létrehozása Szolgáltatás & hálózati objektumok.

Ebben a példában három nevű hálózati objektumhoz definiált (egyet az előtér-alhálózatot és a háttérrendszer alhálózatát, a DNS-kiszolgáló IP-címét is egy hálózathoz objektum) kell lennie. Nevű hálózat létrehozása kezdve a Barracuda NG felügyeleti ügyfél irányítópult, a konfiguráció lapjának megjelenítéséhez, a működési konfigurációs szakaszban kattintson a szabálykészletben, majd a Tűzfalobjektumokat menüpont alatt kattintson a "Hálózat", majd a Szerkesztés hálózatok menüben kattintson az új. A hálózati objektum már hozható létre, a nevét és az előtag hozzáadása:

![Hozzon létre egy előtér-hálózati objektumot][3]

Ezzel létrehoz egy névvel ellátott hálózati az előtérbeli alhálózat, egy hasonló objektumot kell létrehozni, valamint a háttérrendszer alhálózatának. Most már az alhálózatok egyszerűbben hivatkozhat neve az a tűzfalszabályokat.

A DNS-kiszolgáló objektum:

![Hozzon létre egy DNS-kiszolgáló objektumot][4]

A egyetlen IP-cím hivatkozás fog használhatók fel a dokumentum későbbi szakaszában egy DNS-szabályban.

A második előfeltételként szükséges objektumok olyan szolgáltatások objektumok. Ezek az RDP csatlakozási porttal kiszolgálónként képviseli. Mivel a meglévő RDP service objektum az alapértelmezett RDP-portra van kötve, 3389-es, új szolgáltatások hozható létre a külső portok (8014-8026) érkező adatforgalom engedélyezéséhez. Az új portok is hozzáadni a meglévő RDP-szolgáltatás, de a könnyű bemutató, az egyes szabályok kiszolgálónként hozható létre. Az RDP-kiszolgáló; új szabály létrehozása kezdve a Barracuda NG felügyeleti ügyfél irányítópult, a konfiguráció lapjának megjelenítéséhez, a működési konfigurációs szakaszban kattintson a szabálykészletben, majd a Tűzfalobjektumokat menüpont alatt kattintson a "Szolgáltatások", görgessen lefelé a szolgáltatások listájában, és válassza ki az "RDP". Kattintson a jobb gombbal, és válassza a másolás, majd kattintson a jobb gombbal, és kiválasztása, beillesztés. Most már rendelkezésre áll egy RDP-Copy1 Service objektum, amely szerkeszthető. Kattintson a jobb gombbal az RDP-Copy1, és válassza ki a Szerkesztés, a Szerkesztés Service objektum akár, ahogy az itt látható ablak jelenik:

![RDP-szabályban alapértelmezett példánya][5]

Az értékeket szerkesztheti, amelyek az RDP-szolgáltatás egy adott kiszolgálóhoz. AppVM01 az alapértelmezett szabályok fenti RDP módosítani kell, hogy egy új szolgáltatás neve, leírása és a 8. ábra diagramon használt külső RDP-Port (Megjegyzés: a portok úgy módosul, a 3389-es RDP alapértelmezett ehhez a kiszolgálóhoz használt külső port AppVM01 esetén a külső portra 8025) a módosított szolgáltatást az alábbiakban látható:

![AppVM01 Rule][6]

Ezt a folyamatot meg kell ismételni, a többi kiszolgálón; RDP-szolgáltatások létrehozásához AppVM02 DNS01 és IIS01. Ezek a szolgáltatások létrehozásának fogja elérhetővé tenni a szabály létrehozása egyszerűbb, és viszonylag kézenfekvő a következő szakaszban.

> [!NOTE]
> Egy RDP-szolgáltatás, a tűzfal nincs szükség két okból; 1.) először a virtuális gép tűzfal egy Linux-alapú lemezkép, ezért SSH használni kívánt 22-es port helyett RDP és (2) port 22-es virtuális gépek felügyelete, és a két felügyeleti portokat az alább ismertetett lehetővé teszik a csatlakozást felügyeleti első felügyeleti szabály engedélyezett.
> 
> 

### <a name="firewall-rules-creation"></a>Tűzfal-szabályok létrehozása
Ebben a példában használt tűzfalszabályok három típusa van, hogy, különböző ikonokkal rendelkeznek:

Az alkalmazás átirányítási szabály: ![Application Redirect Icon][7]

A cél NAT-szabály: ![Cél NAT ikon][8]

A Pass-szabály: ![Pass ikon][9]

További információ a szabályokról a Barracuda web helyen található.

A következő szabályokat hozhat létre (vagy ellenőrizze a meglévő alapértelmezett szabályok), a Barracuda NG rendszergazdai ügyfél irányítópultján, kezdve a konfiguráció lapjának megjelenítéséhez, az üzemeltetési konfiguráció szakaszban kattintson a Ruleset. A rács nevű, "Main szabályok" jelennek meg a meglévő aktív és inaktív szabályok a tűzfalon. A jobb felső sarkában található a rács van egy kis méretű, zöld "+" gombra kattint, ide kattintva hozzon létre egy új szabályt (Megjegyzés: a tűzfal "zárolva" a módosításokat, ha egy gombot "Zárolási" jelölésű, és nem tudja létrehozni vagy szabályok szerkesztése, kattintson erre a gombra kattintva a "feloldásához" a szabálykészlet és  Szerkesztés engedélyezése). Ha szeretné szerkeszteni a meglévő szabályokat, válassza ki azt a szabályt, jobb gombbal, majd válassza a szabály szerkesztése.

Miután a szabályok vannak létrehozott, illetve a módosított, azok leküldte a tűzfal és kell majd aktiválása, ha ez nem történik, ha a szabály módosításokat nem lépnek érvénybe. A leküldéses és az aktiválási folyamat leírása alább a részleteket a szabály leírását.

Ebben a példában végrehajtásához szükséges minden egyes szabály tulajdonságairól az alábbiak szerint:

* **Felügyeleti tűzfalszabály**: Az alkalmazás átirányítási szabály lehetővé teszi, hogy a forgalom számára a felügyeleti portokat a hálózati virtuális berendezéshez, ebben a példában a Barracuda NextGen tűzfal. A felügyeleti portokat: 801, a 807 és igény szerint 22. A belső és külső portok megegyeznek (azaz nincs port fordítás). Ez szabály, hozzáférés-MGMT-beállítása, egy alapértelmezett szabály, amely alapértelmezés szerint engedélyezett (a Barracuda NextGen tűzfal 6.1-es verzió).
  
    ![Tűzfalszabályok kezelése][10]

> [!TIP]
> Ez a szabály a forrás-címtér ilyen, a felügyeleti IP-címtartományok ismertek, ha csökkenti a hatókör lenne is csökkenteni a támadási felületet a felügyeleti portokhoz.
> 
> 

* **RDP-szabályok**:  A cél NAT-szabályok lehetővé teszi az RDP-n keresztül az egyes kiszolgálók felügyeletét.
  Van ez a szabály létrehozásához szükséges négy kritikus mezők:
  
  1. Forrás – a érkező RDP engedélyezése bárhol, a hivatkozás "Bármely" szerepel a forrás mező.
  2. Szolgáltatás – a megfelelő, korábban létrehozott, ebben az esetben "AppVM01 RDP" Service objektum használja, a külső portok átirányítani a kiszolgálók helyi IP-cím és port 3386 (az alapértelmezett RDP-port). Ez a szabály az RDP-hozzáférésének AppVM01 szól.
  3. Cél-kell lennie a *helyi portot a tűzfalon*, "Választógombot 1 helyi IP-címe" vagy eth0, ha statikus IP-címek használatával. Lehet, hogy a sorszámmal (eth0, eth1 stb.) más, ha a hálózati berendezéshez több helyi adapterrel rendelkezik. Ez a port a tűzfalon a küldi ki az (lehet ugyanaz, mint a fogadó portot), a tényleges irányított cél az a cél lista mezőben.
  4. Átirányítás – Ez a szakasz ismerteti, a virtuális készülék hol végső soron átirányítja a forgalmat. A legegyszerűbb átirányítás helyezi el az IP és Port (nem kötelező) a cél lista mezőben. Nincs port használata esetén a bejövő kérelem a célport lesz (vagyis nincsenek fordítási), használja, ha a port ki van jelölve, hogy a portot is NAT lenne együtt az IP-cím.
     
     ![RDP-tűzfalszabály][11]
     
     Összesen négy RDP-szabályokat kell létrehozni: 
     
     | Szabály neve | Kiszolgáló | Szolgáltatás | Cél lista |
     | --- | --- | --- | --- |
     | RDP-to-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
     | RDP-to-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
     | RDP-to-AppVM01 |AppVM01 |AppVM01 RDP |10.0.2.5:3389 |
     | RDP-to-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

> [!TIP]
> Szűkíteni a forrás- és szolgáltatás mezők körét csökkenti a támadási felületet. A legtöbb korlátozott hatókör, amely lehetővé teszi a funkció kell használni.
> 
> 

* **Alkalmazás-forgalomra vonatkozó szabályokat**: Két alkalmazás forgalmi szabályok vannak, az első az az előtérbeli webes forgalom számára, és a második a háttér-forgalom (például web server adatrétegbeli). Ezek a szabályok lesz a hálózati architektúra (ahol a kiszolgálók kerülnek) függenek adatforgalom és a folyamatok (melyik irányba halad a forgalom és az azoknak a portoknak).
  
    Az előtér szabály, a webes forgalom előbb tárgyalt van:
  
    ![Webes tűzfalszabály][12]
  
    A cél NAT-szabály lehetővé teszi, hogy az alkalmazás tényleges forgalom elérhesse a kiszolgáló. A többi szabály engedélyezi a biztonsági, felügyeleti, stb., míg az alkalmazás szabályok, a külső felhasználók vagy a szolgáltatások eléréséhez (ok) lehetővé teszi. Ebben a példában a 80-as porton van egy webkiszolgálón, így az alkalmazás egyetlen tűzfalszabályt átirányítja a bejövő forgalmat a külső IP-cím, a webes kiszolgálók belső IP-címre.
  
    **Megjegyzés:**:, hogy nincs hozzárendelve a cél lista mezőben port, így a bejövő port a 80-as (vagy a kiválasztott szolgáltatás a 443-as) használni kívánt az átirányítás, a webkiszolgáló. A webalkalmazás-kiszolgáló egy másik porton figyel, például a 8080-as portra, a cél lista mező frissítése sikerült, 10.0.1.4:8080, valamint a Port átirányítás engedélyezéséhez.
  
    A következő alkalmazások forgalmi szabály a háttérben szabály lehetővé teszi a webkiszolgáló kommunikáljon a AppVM01 kiszolgáló (de nem AppVM02) bármely szolgáltatáson keresztül:
  
    ![Tűzfalszabály AppVM01][13]
  
    A Pass-szabály lehetővé teszi, hogy a bármely az IIS-kiszolgálón az előtérbeli alhálózat elérni a AppVM01 (IP-cím 10.0.2.5) bármely porton bármely protokoll, a webes alkalmazás számára szükséges adatok elérését használatával.
  
    A képernyőfelvételen egy "\<explicit-dest\>" jelölésére 10.0.2.5 célhelyként szolgál a cél mezőben. Ez lehet, vagy explicit módon, vagy nevű objektum (ahogyan az az előfeltételeket a DNS-kiszolgáló). Ez a csak az a tűzfal feltárhatja, hogy mely metódus lesz használva. Egy létrehozását Desitnation 10.0.2.5 hozzáadni, kattintson duplán az első üres sor alatt \<explicit-dest\> és a címet írja be a felugró ablakban.
  
    Ez a fázis szabály, és nem NAT van szükség, mivel ez a belső forgalom, így "No SNAT" értékre lehet beállítani a kapcsolódási módszert.
  
    **Megjegyzés**: A szabály a Forráshálózat bármely erőforrás az előtérbeli alhálózat csak lesz egy, vagy egy ismert bizonyos számú webkiszolgálók, ha egy objektum erőforrás sikerült létrehozni részletesebb ezek pontos IP-címek helyett a teljes előtér-alhálózatot.

> [!TIP]
> Ez a szabály használ a szolgáltatás "Bármely", hogy a mintaalkalmazás beállítania és használnia az könnyebben, is engedélyezi ICMPv4 (ping), egy egyetlen szabályban. Azonban ez nem ajánlott. A portok és protokollok ("szolgáltatások") kell leszűkül a lehető legkisebb lehetséges, amely lehetővé teszi a támadási felület csökkentése a határán alkalmazás művelet.
> 
> 

<br />

> [!TIP]
> Bár ez a szabály azt mutatja, hogy egy explicit-dest hivatkozást használja, a tűzfal konfigurálása során minden egységes megközelítést kell használni. Javasoljuk, hogy a nevesített hálózati objektum használható egész kereslettől és a könnyebb olvashatóság érdekében. Az explicit-dest Itt csak egy alternatív módszer megjeleníthető szolgál, és általában nem ajánlott (különösen a összetett konfigurációk).
> 
> 

* **Internet szabály kimenő**: A Pass-szabály lehetővé teszi a kiválasztott cél hálózatok átadása bármely forrás hálózati forgalmát. Ez a szabály egy alapértelmezett szabály már általában a Barracuda NextGen tűzfal, de egy letiltott állapotban van. Ez a szabály a jobb gombbal érheti el a szabály aktiválásához parancsot. A szabály itt látható a létrehozott két helyi alhálózatok megtanulásához, ez a dokumentum az Előfeltételek szakaszban hozzáadása a forrásattribútum, ez a szabály módosult.
  
    ![Kimenő tűzfalszabály][14]
* **DNS-szabály**: A Pass-szabály lehetővé teszi, hogy csak (53-as port) a DNS-forgalom számára a DNS-kiszolgáló. Ebben a környezetben, a háttérkiszolgáló a FrontEnd alhálózatból legtöbb forgalmat blokkol Ez a szabály DNS kifejezetten engedélyezi.
  
    ![DNS-tűzfalszabály][15]
  
    **Megjegyzés**: Ezen a képernyőn a képernyőkép-készítés a kapcsolódási módszert részét képezi. Mivel ez a szabály belső IP-cím forgalmat a belső IP-címhez, nincs NATing nem szükséges, a kapcsolódási beállított érték "No SNAT" a hozzáférési szabályhoz.
* **Az alhálózat alhálózati szabály**: A Pass-szabály egy alapértelmezett szabályt, amely lett aktiválva és módosítani kell az összes olyan kiszolgálónak engedélyezi az előtér-alhálózat bármely kiszolgálóhoz való csatlakozáshoz a háttérrendszer alhálózaton. Ez a szabály az összes belső forgalom, így a nem SNAT értékre lehet beállítani a kapcsolódási módszert.
  
    ![Belüli virtuális hálózatok közötti tűzfalszabály][16]
  
    **Megjegyzés**: A kétirányú jelölőnégyzet nincs bejelölve (se nem a legtöbb szabályokban be van jelölve), ez jelentős ehhez a szabályhoz, hogy ez a szabály az "egy egyirányú" lehetővé teszi, kapcsolat indíthatók el a háttér-alhálózatból az előtérhálózat, de nem a fordított. Ha a jelölőnégyzet be lett jelölve, ez a szabály lehetővé tenné kétirányú forgalmat, amely a logikai diagramja a nem megfelelő.
* **Minden forgalmi szabály megtagadja**: A végső szabályát (tekintetében prioritás) kell lennie, és mint ilyen, ha a forgalom nem sikerül megfelelően a fenti szabály, a rendszer eldobja a szabály által. Ez az alapértelmezett szabály, és általában aktiválva, nem módosított előfizetésekben általában szükség van. 
  
    ![Tűzfal megtagadó szabály][17]

> [!IMPORTANT]
> Miután az összes fenti szabályt hoz létre, fontos tekintse át a forgalom lesz engedélyezett vagy letiltott igény szerint győződjön meg arról, hogy minden szabály prioritását. Ebben a példában a szabályokat kell szerepelniük kell a fő rácshoz, a Barracuda felügyeleti ügyfél szabályzatok továbbításához sorrendben.
> 
> 

## <a name="rule-activation"></a>A szabály az aktiválás
A szabálykészletben módosítani kell a logikai diagramja annak a meghatározását, az a szabálykészletben kell feltölteni a tűzfalat, és majd aktiválva.

![Tűzfal szabály aktiválás][18]

A jobb felső sarokban a felügyeleti ügyfél gombok fürtben vannak. A "Módosítások küldése" gombra kattintva a módosított szabályok küldeni a tűzfal, majd kattintson az "Aktiválás" gombra.

Az aktiválás, a tűzfal szabálykészletben Ez a példa környezet build elkészült.

## <a name="traffic-scenarios"></a>Forgalom forgatókönyvek
> [!IMPORTANT]
> A kulcs takeway, hogy ne feledje, hogy **összes** adatforgalom a tűzfalon keresztül fog érkezni. Ezért a IIS01 kiszolgálóhoz a távoli asztal annak ellenére, hogy a végfelhasználók az előtérbeli felhőszolgáltatás és az előtérbeli alhálózat a kiszolgálóhoz való azt fogja kell az RDP-hez 8014 porton a tűzfal, és engedje, hogy a tűzfal a IIS01 RDP Por irányíthatja a cégen belül az RDP-kérelem t. Az Azure portal "Csatlakozás" gomb nem fog működni, mert nincs közvetlen RDP-elérési útját IIS01 (szerint a portálon látható). Ez azt jelenti, hogy az összes az internetről érkező kapcsolatokat a Security Service és a egy Port, pl. secscv001.cloudapp.net:xxxx lesz.
> 
> 

Ebben az esetben a következő tűzfalszabályokat helyén kell lennie:

1. Tűzfalkezelés
2. IIS01 RDP-vel
3. DNS01 RDP-vel
4. AppVM01 RDP-vel
5. RDP to AppVM02
6. A Web App-forgalom
7. App-forgalom AppVM01
8. Kimenő Internet
9. Előtérkiszolgáló DNS01
10. Belüli alhálózatok közötti forgalmat (csak előtér, háttér)
11. Összes elutasítása

A tényleges tűzfal szabálykészletben valószínűleg fog rendelkezni ezen kívül számos más szabály, a szabályok bármely adott tűzfalon is megkapják az itt felsorolt rétegében eltérő prioritás számok. A lista és a hozzájuk társított számokat is biztosít a relevancia alapján végzett e tizenegy szabályok és a relatív prioritást közülük között. Más szóval; a tényleges tűzfalon a az "RDP való IIS01" lehet, hogy a szabály szám 5, de mindaddig, amíg a "Tűzfal-kezelési" szabály alatt legyen, és az "RDP DNS01" szabály felett platformösszetevő ebben a listában lenne igazítása. A lista fogja is ezzel elősegítve az az alábbi forgatókönyvek áttekinthetőség; így például: “FW Rule 9 (DNS)”. Is kivonatosan RDP négyet együttesen hívása, "az RDP-szabályok" Ha a forgalmat a forgatókönyv nem kapcsolódik az RDP.

Is az, hogy a hálózati biztonsági csoportok-e a helyi visszahívása az előtérbeli és háttérbeli alhálózatok bejövő internetes forgalom számára.

#### <a name="allowed-internet-to-web-server"></a>(Allowed) Internet to Web Server
1. Internetes felhasználói kérések HTTP lapot SecSvc001.CloudApp.Net (Internet Facing Felhőszolgáltatás)
2. Cloud service pass forgalmat a 80-as porton a tűzfal megfelelő felületéről 10.0.0.4:80 nyitott végpontok
3. Nincs biztonsági alhálózathoz rendelt hálózati biztonsági csoport, a rendszer tehát az NSG-szabályok tűzfal forgalom engedélyezésére
4. Forgalom eléri a tűzfal (10.0.1.4 cím – ez) belső IP-címe
5. Tűzfal szabály feldolgozása kezdődik:
   1. Keretrendszer 1. szabály (Keretrendszer Mgmt) nem vonatkoznak, helyezze át a következő szabály
   2. 2 – 5 (RDP-szabályok) Keretrendszer szabályok nem alkalmazni, helyezze át a következő szabály
   3. Keretrendszer szabály 6 (alkalmazás: Webes) a alkalmazni, a forgalom engedélyezve van, tűzfal NAT, hogy a 10.0.1.4 cím – ez (IIS01)
6. Az előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
   1. NSG-t 1. szabály (blokk Internet) nem vonatkozik (ezt a forgalmat a NAT lett volna a tűzfal, így a forrás-cím már a tűzfalon, amely biztonsági az alhálózaton látható a előtér-alhálózat NSG-forgalom "local", és van, és így), áthelyezése a következő szabály
   2. Alapértelmezett NSG-szabályok az alhálózat alhálózati forgalom engedélyezéséhez forgalom engedélyezett, állítsa le az NSG-szabály feldolgozása
7. IIS01 figyeli a webes forgalmat, ezt a kérelmet kap, és elindítja a kérés feldolgozása
8. IIS01 tett kísérletek kezdeményezi egy FTP-munkamenet AppVM01 a háttérbeli alhálózat
9. Az előtérben levő alhálózathoz UDR útvonalon lehetővé teszi a tűzfala a következő ugrás
10. Nincs kimenő szabályok előtérbeli alhálózatán, forgalom engedélyezve van
11. Tűzfal szabály feldolgozása kezdődik:
    1. Keretrendszer 1. szabály (Keretrendszer Mgmt) nem vonatkoznak, helyezze át a következő szabály
    2. 2 – 5 (RDP-szabályok) Keretrendszer szabály nem teljesül, a áthelyezése a következő szabály
    3. Keretrendszer szabály 6 (alkalmazás: Webes) nem vonatkoznak, helyezze át a következő szabály
    4. Keretrendszer szabály 7 (alkalmazás: Háttérrendszer) a alkalmazni, a forgalom engedélyezve van, a tűzfal továbbítja a forgalmat a 10.0.2.5 (AppVM01)
12. A háttérrendszer alhálózatának bejövő szabály feldolgozása kezdődik:
    1. NSG-t 1. szabály (blokk Internet) nem vonatkoznak, helyezze át a következő szabály
    2. Alapértelmezett NSG-szabályok az alhálózat alhálózati forgalom engedélyezéséhez forgalom engedélyezett, állítsa le az NSG-szabály feldolgozása
13. AppVM01 fogadja a kéréseket, és kezdeményezi a munkamenetet, és válaszol
14. Az UDR-útvonal a háttérbeli alhálózat lehetővé teszi a tűzfala a következő ugrás
15. Mivel ebben az esetben nincs kimenő NSG-szabályok a háttérbeli alhálózat a válasz engedélyezett-e.
16. Mivel ez adja vissza forgalmat a munkamenetet létesítenek a tűzfal átadja a választ a webkiszolgáló (IIS01)
17. Előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
    1. NSG-t 1. szabály (blokk Internet) nem vonatkoznak, helyezze át a következő szabály
    2. Alapértelmezett NSG-szabályok az alhálózat alhálózati forgalom engedélyezéséhez forgalom engedélyezett, állítsa le az NSG-szabály feldolgozása
18. Az IIS-kiszolgáló megkapja a választ, a tranzakció AppVM01, és befejezi a HTTP-válasz létrehozásához, a HTTP-válasz a kérelmezőnek küldött van
19. Mivel ebben az esetben nem az előtérben levő alhálózathoz a választ a kimenő NSG-szabályok engedélyezve van
20. A HTTP-válasz eléri a tűzfalat, és mivel ez a válasz NAT munkamenetet létesítenek elfogadta a tűzfal
21. A tűzfal után a rendszer átirányítja a választ az internetes felhasználói
22. Mivel ebben az esetben nem kimenő NSG-szabályok vagy a válasz engedélyezett Frontend alhálózaton található UDR ugrások, és az internetes felhasználó megkapja a kért weblap.

#### <a name="allowed-internet-rdp-to-backend"></a>(Engedélyezett) Internetes háttérrendszer RDP-vel
1. Kiszolgálói rendszergazda interneten keresztül SecSvc001.CloudApp.Net:8025, ahol 8025-e a felhasználó által hozzárendelt portszámot az "RDP AppVM01" tűzfalszabály AppVM01 RDP-munkamenetet kér
2. A felhőszolgáltatás továbbítja a nyitott végpontok forgalmát a porton 8025 10.0.0.4:8025 a tűzfal megfelelő felületéről
3. Nincs biztonsági alhálózathoz rendelt hálózati biztonsági csoport, a rendszer tehát az NSG-szabályok tűzfal forgalom engedélyezésére
4. Tűzfal szabály feldolgozása kezdődik:
   1. Keretrendszer 1. szabály (Keretrendszer Mgmt) nem vonatkoznak, helyezze át a következő szabály
   2. Keretrendszer 2. szabály (RDP IIS) nem vonatkoznak, helyezze át a következő szabály
   3. Keretrendszer 3. szabály (RDP DNS01) nem vonatkoznak, helyezze át a következő szabály
   4. Keretrendszer 4. szabály (RDP AppVM01) a alkalmazni, a forgalom engedélyezve van, tűzfal NAT, hogy 10.0.2.5:3386 (RDP-port a AppVM01)
5. A háttérrendszer alhálózatának bejövő szabály feldolgozása kezdődik:
   1. NSG-t 1. szabály (blokk Internet) nem vonatkozik (ezt a forgalmat a NAT volt a tűzfal lenne, így a forrás-cím már a tűzfalon, amely biztonsági az alhálózaton, és a háttérbeli alhálózat NSG-forgalom "local", van, és így), helyezze át a következő szabály
   2. Alapértelmezett NSG-szabályok az alhálózat alhálózati forgalom engedélyezéséhez forgalom engedélyezett, állítsa le az NSG-szabály feldolgozása
6. AppVM01 a RDP-forgalmat figyeli és reagál
7. Nincs kimenő NSG-szabályok az alapértelmezett szabályok a alkalmazni, és a visszatérő forgalom engedélyezve van
8. A következő ugrás UDR irányítja az a tűzfalon a kimenő forgalom
9. Mivel ez adja vissza forgalmat a munkamenetet létesítenek a tűzfal átadja a választ az internetes felhasználói
10. RDP-munkamenet engedélyezve van
11. AppVM01 felkéri a felhasználónév jelszó

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Engedélyezett) A DNS-kiszolgáló kiszolgálói DNS-címkeresés webes
1. A Web Server, IIS01, egy adatcsatorna www.data.gov, igényeinek megfelelően, de igények címének feloldására.
2. A hálózati konfigurációt a virtuális hálózatok közötti listák DNS01 (a háttérbeli alhálózat 10.0.2.4 cím), az elsődleges DNS-kiszolgáló, IIS01 küld a DNS-kérelem DNS01
3. A következő ugrás UDR irányítja az a tűzfalon a kimenő forgalom
4. Nincs kimenő NSG-szabályok az előtérben levő alhálózathoz vannak kötve, forgalom engedélyezve van
5. Tűzfal szabály feldolgozása kezdődik:
   1. Keretrendszer 1. szabály (Keretrendszer Mgmt) nem vonatkoznak, helyezze át a következő szabály
   2. 2 – 5 (RDP-szabályok) Keretrendszer szabály nem teljesül, a áthelyezése a következő szabály
   3. Keretrendszer szabályok 6 és 7 (Alkalmazásszabályok) nem vonatkoznak, helyezze át a következő szabály
   4. Keretrendszer szabály 8 (az Internet) nem vonatkoznak, helyezze át a következő szabály
   5. Keretrendszer szabály 9 (DNS) a alkalmazni, a forgalom engedélyezve van, a tűzfal továbbítja a forgalmat 10.0.2.4 cím (DNS01)
6. A háttérrendszer alhálózatának bejövő szabály feldolgozása kezdődik:
   1. NSG-t 1. szabály (blokk Internet) nem vonatkoznak, helyezze át a következő szabály
   2. Alapértelmezett NSG-szabályok az alhálózat alhálózati forgalom engedélyezéséhez forgalom engedélyezett, állítsa le az NSG-szabály feldolgozása
7. DNS-kiszolgáló a kérést kap
8. DNS-kiszolgáló nem rendelkezik a címmel a gyorsítótárba, és az interneten egy DNS-gyökérkiszolgáló kéri
9. A következő ugrás UDR irányítja az a tűzfalon a kimenő forgalom
10. Nincs kimenő NSG-szabályok a háttérbeli alhálózat, forgalom engedélyezve van
11. Tűzfal szabály feldolgozása kezdődik:
    1. Keretrendszer 1. szabály (Keretrendszer Mgmt) nem vonatkoznak, helyezze át a következő szabály
    2. 2 – 5 (RDP-szabályok) Keretrendszer szabály nem teljesül, a áthelyezése a következő szabály
    3. Keretrendszer szabályok 6 és 7 (Alkalmazásszabályok) nem vonatkoznak, helyezze át a következő szabály
    4. Keretrendszer szabály 8 (az Internet) a alkalmazni, a forgalom engedélyezve van, a munkamenet SNAT ki, hogy az interneten lévő DNS-kiszolgáló legfelső szintű
12. Internetes DNS-kiszolgáló válaszol, mivel ez a munkamenet kezdeményezték a tűzfal, a válasz elfogadta a tűzfal
13. Mivel ez egy munkamenetet létesítenek, a tűzfal továbbítja a kezdeményező kiszolgáló DNS01 adott válasz
14. A háttérrendszer alhálózatának bejövő szabály feldolgozása kezdődik:
    1. NSG-t 1. szabály (blokk Internet) nem vonatkoznak, helyezze át a következő szabály
    2. Alapértelmezett NSG-szabályok az alhálózat alhálózati forgalom engedélyezéséhez forgalom engedélyezett, állítsa le az NSG-szabály feldolgozása
15. A DNS-kiszolgáló fogadja és gyorsítótárazza a választ, és a kezdeti kérelmet vissza a IIS01 válaszol
16. Az UDR-útvonal a háttérbeli alhálózat lehetővé teszi a tűzfala a következő ugrás
17. Nincs kimenő NSG-szabályok a háttérbeli alhálózat létezik, forgalom engedélyezve van
18. Ez a munkamenetet létesítenek a tűzfalon, a válasz továbbítja a tűzfalon az IIS-kiszolgálón
19. Előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
    1. Nincs NSG szabály, amelyre vonatkozik a bejövő forgalmat a háttérbeli alhálózat felől az előtérben levő alhálózathoz, így egyike sem az NSG-szabályok alkalmazása
    2. A alapértelmezett rendszerszabály, amely lehetővé teszi az alhálózatok közötti adatforgalom lehetővé tenné a forgalmat, így a forgalom engedélyezve van
20. IIS01 DNS01 kap a válasz

#### <a name="allowed-backend-server-to-frontend-server"></a>(Engedélyezett) Háttérkiszolgáló előtér-kiszolgáló
1. Egy rendszergazda bejelentkezett AppVM02 RDP-Kapcsolaton keresztül közvetlenül a kiszolgálóról IIS01 keresztül a windows Fájlkezelőben olyan fájlt kér
2. Az UDR-útvonal a háttérbeli alhálózat lehetővé teszi a tűzfala a következő ugrás
3. Mivel ebben az esetben nincs kimenő NSG-szabályok a háttérbeli alhálózat a válasz engedélyezett-e.
4. Tűzfal szabály feldolgozása kezdődik:
   1. Keretrendszer 1. szabály (Keretrendszer Mgmt) nem vonatkoznak, helyezze át a következő szabály
   2. 2 – 5 (RDP-szabályok) Keretrendszer szabály nem teljesül, a áthelyezése a következő szabály
   3. Keretrendszer szabályok 6 és 7 (Alkalmazásszabályok) nem vonatkoznak, helyezze át a következő szabály
   4. Keretrendszer szabály 8 (az Internet) nem vonatkoznak, helyezze át a következő szabály
   5. Keretrendszer szabály 9 (DNS) nem vonatkoznak, helyezze át a következő szabály
   6. Keretrendszer szabály 10 (Intra-alhálózat) a alkalmazni, a forgalom engedélyezve van, a tűzfal továbbítja a forgalmat 10.0.1.4 cím – ez (IIS01)
5. Előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
   1. NSG-t 1. szabály (blokk Internet) nem vonatkoznak, helyezze át a következő szabály
   2. Alapértelmezett NSG-szabályok az alhálózat alhálózati forgalom engedélyezéséhez forgalom engedélyezett, állítsa le az NSG-szabály feldolgozása
6. Ha a megfelelő hitelesítési és engedélyezési, IIS01 fogadja a kéréseket és válaszol
7. Az előtérben levő alhálózathoz UDR útvonalon lehetővé teszi a tűzfala a következő ugrás
8. Mivel ebben az esetben nem az előtérben levő alhálózathoz a választ a kimenő NSG-szabályok engedélyezve van
9. Mivel ez egy meglévő munkamenetben a tűzfalon Ez a válasz engedélyezve van, és a tűzfal AppVM02 a választ adja vissza.
10. Háttérbeli alhálózatot bejövő szabály feldolgozása kezdődik:
    1. NSG-t 1. szabály (blokk Internet) nem vonatkoznak, helyezze át a következő szabály
    2. Alapértelmezett NSG-szabályok az alhálózat alhálózati forgalom engedélyezéséhez forgalom engedélyezett, állítsa le az NSG-szabály feldolgozása
11. AppVM02 a válasz fogadása

#### <a name="denied-internet-direct-to-web-server"></a>(Denied) Internet direct to Web Server
1. Az Internet felhasználó megpróbál hozzáférni a webkiszolgáló IIS01, a FrontEnd001.CloudApp.Net szolgáltatáson keresztül
2. Végpontleképzőben nincs nyissa meg a HTTP-forgalom, mivel ez lenne halad át a felhőalapú szolgáltatás, és a kiszolgáló nem érhető el.
3. Ha valamilyen okból a végpontok nyitva, az NSG-t (Internet letiltása) az előtérbeli alhálózat le fog állni a forgalmat
4. Végül az előtérbeli alhálózat UDR útvonal szeretné elküldeni a minden kimenő forgalom IIS01 a következő ugrás a tűzfalat, és a tűzfal tenné meg ezt az aszimmetrikus forgalom, és dobja el a kimenő válasz nincsenek között legalább három független rétege Thus az internet és IIS01 jogosulatlan vagy illetéktelen hozzáférés megakadályozása a felhő-szolgáltatáson keresztül.

#### <a name="denied-internet-to-backend-server"></a>(Tiltott) A háttérkiszolgáló az Internet
1. Az Internet felhasználó megpróbál hozzáférni egy fájlt a AppVM01 a BackEnd001.CloudApp.Net szolgáltatáson keresztül
2. Mivel ebben az esetben végpontok nyissa meg a fájlmegosztás, ez a Felhőszolgáltatás nem kellene átadnia, és a kiszolgáló nem érhető el.
3. Ha valamilyen okból a végpontok nyitva, az NSG-t (Internet letiltása) le fog állni a forgalmat
4. Végül az UDR-útvonal szeretné elküldeni a minden kimenő forgalom AppVM01 a következő ugrás a tűzfalat, és a tűzfal meg ezt az aszimmetrikus forgalom és a kimenő választ, így nincsenek az internet között legalább három független rétege dobja el és AppVM01 jogosulatlan vagy illetéktelen hozzáférés megakadályozása a felhő-szolgáltatáson keresztül.

#### <a name="denied-frontend-server-to-backend-server"></a>(Tiltott) Háttérkiszolgáló előtér-kiszolgáló
1. Tegyük fel, IIS01 feltörték, és fut-e a háttérkiszolgálók alhálózati tett kísérlet rosszindulatú kódot.
2. Az előtérbeli alhálózat UDR útvonal lenne minden kimenő forgalmat a küldjön IIS01 a tűzfalhoz következő ugrásként. Ez nem hiba, amely a sérült biztonságú virtuális gép által is módosítható.
3. A tűzfal szeretné feldolgozni a forgalomra, kérelmező: AppVM01, illetve a DNS-kiszolgáló DNS-keresések, amely a forgalom esetlegesen engedélyezhető a tűzfal (miatt Keretrendszer szabályok 7. és 9). Az összes többi forgalom Keretrendszer szabály 11 (az összes elutasítása) lenne blokkolja.
4. Ha a fenyegetésészlelés engedélyezése a tűzfalon (amely Ez a dokumentum nem terjed ki, a gyártó dokumentációjában az advanced threat képességek bizonyos hálózati berendezés), akkor is a forgalmat, melyeken engedélyezett az alapszintű továbbítási szabályok a jelen tárgyalt dokumentum sikerült előzhető meg, ha a forgalmat tartalmazta ismert aláírások vagy minták, hogy ez a jelző azt egy komplex veszélyforrások elleni szabályt.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Tiltott) A DNS-kiszolgáló internetes DNS-címkeresés
1. Internet user tries to lookup an internal DNS record on DNS01 through BackEnd001.CloudApp.Net service 
2. Végpontleképzőben nincs megnyitva a DNS-forgalom, mivel ez lenne halad át a felhőalapú szolgáltatás, és a kiszolgáló nem érhető el.
3. Ha valamilyen okból a végpontok volt megnyitva, a Hálózatibiztonságicsoport-szabály (blokk Internet) az előtérbeli alhálózat le fog állni a forgalmat
4. Végül a háttérbeli alhálózat UDR útvonal szeretné elküldeni a minden kimenő forgalom DNS01 a következő ugrás a tűzfalat, és a tűzfal meg ezt az aszimmetrikus forgalom és a kimenő válasz nincsenek között legalább három független rétege Thus dobja el a az Internet és DNS01 jogosulatlan vagy illetéktelen hozzáférés megakadályozása a felhő-szolgáltatáson keresztül.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Tiltott) Az SQL-hozzáférés tűzfalon keresztüli Internet
1. Internet user requests SQL data from SecSvc001.CloudApp.Net (Internet Facing Cloud Service)
2. Végpontleképzőben nincs nyitott meg SQL esetén, mivel ez a Felhőszolgáltatás nem kellene átadnia, és a tűzfal nem érhető el.
3. Ha valamilyen okból SQL végpontok volt megnyitva, a tűzfal szabály feldolgozása lenne kezdete:
   1. Keretrendszer 1. szabály (Keretrendszer Mgmt) nem vonatkoznak, helyezze át a következő szabály
   2. 2 – 5 (RDP-szabályok) Keretrendszer szabályok nem alkalmazni, helyezze át a következő szabály
   3. Keretrendszer szabály 6 és 7 (alkalmazás szabályok) nem vonatkoznak, helyezze át a következő szabály
   4. Keretrendszer szabály 8 (az Internet) nem vonatkoznak, helyezze át a következő szabály
   5. Keretrendszer szabály 9 (DNS) nem vonatkoznak, helyezze át a következő szabály
   6. Keretrendszer szabály 10 (Intra-alhálózat) nem vonatkoznak, helyezze át a következő szabály
   7. Keretrendszer szabály 11 (az összes elutasítása) a alkalmazni, a forgalom letiltott, állítsa le a szabály feldolgozása

## <a name="references"></a>Referencia
### <a name="main-script-and-network-config"></a>Fő parancsfájlba, és a hálózati konfiguráció
Mentse a teljes szkriptet egy PowerShell-parancsfájlt. Mentse a hálózati konfiguráció "NetworkConf2.xml" nevű fájl.
Szükség szerint módosítsa a felhasználó által definiált változókat. Futtassa a parancsfájlt, majd az útmutatást követve tűzfal szabály beállítása a fenti.

#### <a name="full-script"></a>Teljes szkript
Ez a szkript lesz, a felhasználó által definiált változóknak alapján:

1. Csatlakozás Azure-előfizetéshez
2. Új tárfiók létrehozása
3. Hozzon létre egy új virtuális hálózatot és három alhálózatot a hálózati konfigurációs fájlban meghatározott módon
4. Öt virtuális gépek létrehozása 1 tűzfal és a 4 a windows server virtuális gépek
5. Konfigurálja az udr-t többek között:
   1. Két új útválasztási táblázat létrehozása
   2. Útvonalak hozzáadása a táblákhoz
   3. Táblák megfelelő alhálózatokat kötése
6. Az nva IP-továbbítás engedélyezése
7. Konfigurálja az NSG-t többek között:
   1. Létrehozni egy NSG-t
   2. Szabály hozzáadása
   3. Az NSG-t a megfelelő alhálózatokat kötése

Ez a PowerShell-szkript kell futtatni a egy internethez csatlakoztatott számítógépen vagy kiszolgálón.

> [!IMPORTANT]
> Ez a szkript futtatásakor a lehetséges, hogy figyelmeztetéseket vagy más tájékoztató üzeneteket, amely a pop a PowerShellben. Csak hibaüzenetek vörös színnel adhatnak okot.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.

      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }

    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan

      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"

      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal

      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName

     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>Hálózati konfigurációs fájl
Mentse az xml-fájlt a hely frissítve, és adja hozzá a hivatkozás ehhez a fájlhoz, a szkriptben $NetworkConfigFile változó.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Mintaszkriptek alkalmazás
Ha szeretne egy mintaalkalmazás telepítése ezzel és más DMZ példák, egy, a következő hivatkozás lett megadva: [A Példaszkript alkalmazás][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Kétirányú Szegélyhálózat nva-t, az NSG-t és az udr-t"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "A tűzfalszabályok logikai nézetében"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Hozzon létre egy előtér-hálózati objektumot"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Hozzon létre egy DNS-kiszolgáló objektumot"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "RDP-szabályban alapértelmezett példánya"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "AppVM01 Rule"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Application Redirect Icon"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Cél NAT ikon"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Pass ikon"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Tűzfalszabályok kezelése"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "RDP-tűzfalszabály"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Webes tűzfalszabály"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Tűzfalszabály AppVM01"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Kimenő tűzfalszabály"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "DNS-tűzfalszabály"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Belüli virtuális hálózatok közötti tűzfalszabály"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Tűzfal megtagadó szabály"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Tűzfal szabály aktiválás"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
