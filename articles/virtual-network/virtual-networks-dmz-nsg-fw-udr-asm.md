---
title: "DMZ példa – Build hálózatokat és a tűzfalon, UDR és NSG védelméhez DMZ |} Microsoft Docs"
description: "Build tűzfallal DMZ, felhasználó által definiált útválasztási (UDR) és a hálózati biztonsági csoportokkal (NSG)"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: fdb3c5cbd3acee90386352c6f180a71aa81f54fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>3 – példa hálózatokat és a tűzfalon, UDR és NSG védelméhez DMZ összeállítása
[A biztonsági határ bevált gyakorlatok laphoz való visszatéréshez][HOME]

Ebben a példában az tűzfal, négy windows Server kiszolgálókon, felhasználói definiált útválasztó, IP-továbbítást, és hálózati biztonsági csoportok hoz létre a Szegélyhálózaton. Azt is haladhat végig a megfelelő parancsok bemutatják az egyes lépések, adja meg. Is van a forgalom forgatókönyv részt, hogy egy részletesebb lépésenkénti hogyan forgalom halad keresztül a Szegélyhálózaton lévő védelmi réteget. Végezetül a hivatkozások szakasz: a teljes kód látható, míg utasítás ebben a környezetben, teszteléséhez, és a különböző forgatókönyvekben kísérletezhet létrehozásához. 

![Kétirányú DMZ NVA, NSG és UDR][1]

## <a name="environment-setup"></a>Környezet beállítása
Ebben a példában nincs olyan előfizetést, amelyet a következőket tartalmazza:

* A felhőalapú szolgáltatások három: "SecSvc001", "FrontEnd001" és "BackEnd001"
* A virtuális hálózati "CorpNetwork", három alhálózatokon: "SecNet", "Előtér" és "Háttér"
* Ebben a példában a tűzfal, a SecNet alhálózathoz csatlakozik a hálózathoz virtuális készülék
* A Windows Server egy webalkalmazás-kiszolgáló ("IIS01") jelölő
* Két windows Server kiszolgálókon, amelyek megfelelnek az alkalmazás biztonsági end-kiszolgálók ("AppVM01", "AppVM02")
* A Windows server DNS-kiszolgáló ("DNS01") jelölő

Az alábbi hivatkozások részben van egy PowerShell-parancsfájlt, amely a fent leírt környezetben a legtöbb fog létrehozni. A virtuális gépek és virtuális hálózatok, bár a példaként megadott parancsfájlt, által végzett dokumentum nem ismerteti a jelen dokumentum részletesen.

A környezet létrehozásához:

1. Mentse a hálózati konfigurációs XML-fájlt a references szakaszában szereplő (frissítődik nevét, helyét és IP-címeket az adott forgatókönyv esetén)
2. A felhasználói változók a parancsfájl felel meg a parancsfájl-hoz (előfizetések, service nevét stb.) kell futtatni a környezet frissítése
3. A parancsfájl végrehajtása a PowerShellben

**Megjegyzés:**: A régió, a PowerShell-parancsfájl esetében meg kell egyeznie a hálózati konfigurációs XML-fájl esetében.

Miután a parancsfájl sikeresen fut a következő utáni parancsfájl lépések tehetők:

1. Állítsa be a tűzfalszabályok, ezzel kapcsolatban lásd: című részt az alábbi: tűzfal szabály leírása.
2. Opcionálisan a references szakaszában vannak két parancsfájlok állíthat be a webkiszolgáló és egy egyszerű webes alkalmazás tesztelése a DMZ-konfiguráció engedélyezése az alkalmazások kiszolgálói.

Miután a parancsfájl sikeresen fut a tűzfal-szabályokat kell végrehajtani, ez című részre kapcsolatban lásd: tűzfalszabályokat.

## <a name="user-defined-routing-udr"></a>Felhasználó által definiált útválasztási (UDR)
Alapértelmezés szerint a következő rendszerútvonalak is meg van adva:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

A VNETLocal mindig a megadott cím előtag(ok) (ie azt fogja módosítani a virtuális hálózat virtuális hálózat attól függően, hogy hogyan minden egyes megadott virtuális hálózat van meghatározva) adott hálózat vnet. A fennmaradó rendszerútvonalak statikusak, és a fenti alapértelmezett.

Prioritás, mint útvonalak dolgoznak fel a leghosszabb előtag-megfeleltetés (LPM) módszerrel, így a legjobban megfelelő útvonal a táblázatban megadott címre alkalmazni.

Ezért forgalmat (például DNS01 server 10.0.2.4) a helyi hálózati (10.0.0.0/16) szánt lesz átirányítva, a cél a 10.0.0.0/16 útvonal miatt a virtuális hálózat között. Más szóval 10.0.2.4, a 10.0.0.0/16 útvonal esetén a legjobban megfelel útvonal, annak ellenére, hogy a 10.0.0.0/8 és 0.0.0.0/0 is beállíthat, de mivel kevesebb adott nincsenek hatással a forgalmat. Így 10.0.2.4 forgalmat lenne egy következő ugrás a helyi vnet rendelkezik, és a cél egyszerűen továbbítani.

Ha a forgalom történő készült 10.1.1.1 például 10.0.0.0/16 útvonal nem érvényes, de a 10.0.0.0/8 lenne a legjobban megfelel és a forgalom lenne eldobott ("fekete furatos"), mert a következő ugrás értéke Null. 

Ha a cél egyik Null előtagjai vagy a VNETLocal előtagok nem lett alkalmazva, akkor a legkevésbé egyedi végrehajtania továbbítani, 0.0.0.0/0 és kell az internetre átirányított következő ugrásként, így Azure internet peremhálózati ki.

Ha két azonos előtagok az útválasztási táblázatban, a következő egy útvonalak "forrás" attribútumok alapján a sorrendben:

1. "VirtualAppliance" manuálisan adja hozzá a táblázathoz felhasználó definiált útvonal =
2. "A(z)" (BGP hibrid hálózatok használata esetén), dinamikus útvonal = dinamikus hálózati protokoll által hozzáadott, ezeket az útvonalakat idővel megváltozhat, a dinamikus protokoll automatikusan módosításoknak társviszonyban hálózat
3. "Alapértelmezett" = a Rendszerútvonalak, a helyi virtuális hálózat és a statikus bejegyzések az útvonal a fenti táblázatban látható módon.

> [!NOTE]
> Ezután már használhatja felhasználói definiált útválasztás (UDR) az ExpressRoute és kényszerítheti a kimenő és bejövő VPN-átjárók létesítmények közötti forgalom átirányítását a hálózati virtuális készülék (NVA).
> 
> 

#### <a name="creating-the-local-routes"></a>A helyi útvonalak létrehozása
Ebben a példában két útvonaltáblát van szükség, parancsikonja az előtér- és háttérszolgáltatások alhálózatok. Minden tábla az adott alhálózat megfelelő statikus útvonalakkal be van töltve. Jelen példában minden egyes táblához három útvonalakat:

1. A következő ugrás a helyi alhálózat forgalmának definiált helyi alhálózat forgalmának megkerüljék a tűzfal engedélyezi
2. Virtuális hálózati forgalmat a következő ugrásaként definiálva, tűzfal, az alapértelmezett szabály, amely lehetővé teszi, hogy a helyi VNet forgalmát közvetlenül irányítja a rendszer felülírja
3. Az összes többi forgalom (0/0) a következő ugrásaként definiálva, a tűzfal

Az útválasztási táblák létrehozása után azokat ezek alhálózatok vannak kötve. A Frontend alhálózathoz tartozó útvonaltábla, egyszer létre, és az alhálózati kötve kell kinéznie:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


Ehhez a példához a következő parancsok segítségével útvonaltábla létrehozása, adjon hozzá egy felhasználó által megadott útvonalat, és majd kötést létrehozni a útválasztási táblázatot egy alhálózat (Megjegyzés; dollárjelet kezdődő alatti elemek (pl.: $BESubnet) vannak felhasználó által definiált változóknak jelen dokumentum hivatkozás szakaszában a parancsfájlból):

1. Először az útválasztási alaptábla létre kell hozni. A kódrészletben láthatja a Backend alhálózathoz a tábla létrehozásához. A parancsfájl egy kapcsolódó táblában is létrejön a Frontend alhálózathoz.
   
     Új AzureRouteTable-$BERouteTableName neve "
   
         -Location $DeploymentLocation `
         -Label "Route table for $BESubnet subnet"
2. Útvonaltábla létrehozása után a megadott felhasználó által megadott útvonalakat is hozzáadhatók. Ezen parancsmaghoz minden forgalmat (0.0.0.0/0) a virtuális készülék ($VMIP [0], a változó segítségével adjon át a virtuális készülék létrehozásakor, a parancsfájl a korábbi IP-cím) keresztül továbbítódik. A parancsfájl a megfelelő létrejön egy szabály is az előtér-táblázatban.
   
     Get-AzureRouteTable $BERouteTableName |} `
   
         Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
         -NextHopType VirtualAppliance `
         -NextHopIpAddress $VMIP[0]
3. A fenti útvonal bejegyzés felülírják az alapértelmezett "0.0.0.0/0" útvonal, de a meglévő alapértelmezett 10.0.0.0/16 szabály amely lehetővé teszi a forgalom továbbításához közvetlenül a cél és nem a virtuális hálózati berendezések a Vneten belül. A helyes-e ez a viselkedés a következő szabály hozzá kell adni.
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
4. Ezen a ponton választási lehetőség van el kell végezni. A fenti két felé mutató útvonalakat minden forgalom irányítja a tűzfalhoz a értékelésére, még akkor is, forgalmat egy egyetlen alhálózaton belül. Ez kívánatos lehet, de helyileg irányítja a tűzfaltól beavatkozás nélkül harmadik alhálózat forgalom engedélyezéséhez olyan speciális szabályt is hozzáadhatók. Ez az útvonal bármely cím destine a helyi alhálózaton is csak állapotok közvetlenül útvonal létezik (NextHopType = VNETLocal).
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
5. Végezetül az útvonaltábla létrehozása, és feltölti a felhasználó által megadott útvonalakat, az a táblázat most kell kötni alhálózathoz. A parancsfájl az előtér-útválasztási táblázatot is van kötve a Frontend alhálózathoz. Ez a kötés parancsfájl a háttér-alhálózathoz.
   
     Set-AzureSubnetRouteTable - VirtualNetworkName $VNetName "
   
        -SubnetName $BESubnet `
        -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>IP-továbbítás
Egy kiegészítő szolgáltatást UDR, az IP-továbbítást. Ez az egy-egy virtuális készüléken, amely lehetővé teszi a forgalom a készülék nem kifejezetten fogadását, és majd továbbítja a végső cél, hogy forgalom beállítása.

Tegyük fel ha AppVM01 érkező forgalmat egy kérést küld a DNS01 kiszolgáló UDR volna útvonal ezt a tűzfalhoz. Az IP-továbbítás engedélyezve van a forgalom DNS01 célhelyhez (10.0.2.4) fog fogadja el a készülék (10.0.0.4) és továbbítja a végső rendeltetési (10.0.2.4). IP-továbbítást, a tűzfal engedélyezve van, nélkül forgalom volna nem fogadja el a készülék annak ellenére, hogy az útvonaltáblát van a tűzfal következő ugrásként. 

> [!IMPORTANT]
> Nagyon fontos a IP-továbbítás engedélyezze a felhasználó definiált útválasztási együtt.
> 
> 

Az IP-továbbítás beállítása egy parancs, és a virtuális gép létrehozás időpontjában végezhető. Ebben a példában a folyamathoz a kódrészletet a parancsfájl vége felé van, és a UDR parancsok csoportosítva:

1. A Virtuálisgép-példány, amely a virtuális készülékre, a tűzfal ebben az esetben hívható, és az IP-továbbítás engedélyezése (Megjegyzés; valamely elemére dollárjelet piros kezdődő (pl.: $VMName[0]) egy felhasználó által definiált változó a parancsfájlból, ez a dokumentum hivatkozás szakaszában. Szögletes [0], a nulla képviseli az első virtuális gép virtuális gépek, a példában parancsfájl módosítás nélkül működik a tömbben, az első virtuális gép (VM 0) kell lennie a tűzfalon):
   
     Get-AzureVM-Name $VMName [0] - szolgáltatásnév $ServiceName [0] |} `
   
        Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Hálózati biztonsági csoportok (NSG)
Ebben a példában egy NSG-csoport összeállítása és kezelhető egyetlen szabállyal majd betölteni. Ez a csoport majd kötött csak az előtér- és háttérszolgáltatások alhálózatok (nem a SecNet). Deklaratív módon a következő szabály éppen készül:

1. Az összes bejövő forgalom (minden porthoz) az internetről a teljes virtuális hálózatot (az összes alhálózatot) megtagadva

Bár ebben a példában NSG-ket használ, annak fő célja azoknak a manuális helytelen konfiguráció másodlagos rétegként. Szeretnénk vagy az internetről érkező összes bejövő forgalom blokkolása a Frontend elemre, vagy háttér alhálózatok felderítéséhez, a forgalom csak áramlása a tűzfalhoz a SecNet alhálózati (és az előtér- vagy háttéradatbázis be megfelelő majd alhálózatok). Plus helyen, a UDR szabályokkal hajtott végre az előtér- vagy háttéradatbázis alhálózatokra adatforgalmat volna kéri ki (UDR) környezetnek köszönhetően a tűzfalon. A tűzfal jelenik meg ez az aszimmetrikus folyamatként, és a kimenő forgalom volna eldobni. Így az alábbi három réteg biztonsági védelmét az előtér- és háttérszolgáltatások alhálózatok; 1.) a FrontEnd001 és BackEnd001 nyitott végpontok felhőszolgáltatásokkal, 2) az NSG-k megtagadása a forgalom az internetről, 3.) a tűzfal figyelmen kívül hagyása aszimmetrikus forgalmat.

Egy érdekes pont vonatkozó ebben a példában a hálózati biztonsági csoport, hogy az tartalmazza csak egy szabály, az alábbi ábrán látható, vagyis a teljes virtuális hálózathoz, amely magában foglalja a biztonság – alhálózati internetforgalom megtagadása. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

Azonban az NSG-t csak az előtér- és háttérszolgáltatások alhálózatok van kötve, mert a szabály nem feldolgozott forgalom a bejövő biztonsági alhálózathoz. Ennek eredményeképpen annak ellenére, hogy az NSG-szabály szerint bármely cím nincs internetes forgalmat a virtuális hálózaton, mert az NSG soha nem volt kötve a biztonság – alhálózati, forgalom erdőtől áramolnak a biztonsági alhálózathoz.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Tűzfalszabályok
A tűzfalon szabályok továbbítási kell létrehozni. Mivel a tűzfal blokkolja vagy az összes bejövő, kimenő továbbítási és intra-VNet forgalom sok tűzfalszabályok van szükség. Minden bejövő forgalom is, kattintson a biztonsági szolgáltatás nyilvános IP-címét (különböző portok), a tűzfal feldolgozásra. Az ajánlott eljárás, hogy az alhálózatok beállítása előtt a logikai flow diagram és tűzfalszabályok elkerülése érdekében átdolgozási később. Az alábbi ábra az ebben a példában a tűzfalszabályok logikai nézetében:

![A tűzfalszabályok logikai ábrázolása][2]

> [!NOTE]
> A használt hálózati virtuális készülék alapján, a felügyeleti portokat függ. Ebben a példában a Barracuda NextGen tűzfal hivatkozott használó 22, 801 és 807 portok. További részleteket a készülék gyártó dokumentációjában, a pontos portokat használ az eszköz felügyeletére használható.
> 
> 

### <a name="logical-rule-description"></a>Logikai szabály leírása
Logikai a fenti ábrán a biztonság – alhálózati nem látható, mert a tűzfal be az adott alhálózat csak erőforrás, és ezen a diagramon láthatók a tűzfal-szabályok és hogyan azok logikailag engedélyezi vagy megtagadja a forgalmat, és nem tényleges irányított elérési. Is, a külső portok az RDP-forgalmát magasabbak kiválasztott címkiosztási (8014 – 8026) portok, és sincs kijelölve nagyjából megfelel-e az utolsó két oktett könnyebb olvashatóság érdekében a helyi IP-cím (pl. helyi kiszolgáló címe 10.0.1.4 társítva a külső portra 8014), azonban bármely magasabb nem ütköző portok is használható.

Az ebben a példában 7 típusú szabályokat kell, ezek a szabályok típusai a következők ismertetett:

* Külső szabályainak (bejövő forgalom):
  1. Felügyeleti tűzfalszabály: Az alkalmazás átirányítási szabály lehetővé teszi a forgalom a felügyeleti portokat a hálózati virtuális készülék számára.
  2. RDP-szabályok (az egyes windows-kiszolgálók): A négy szabályok (kiszolgálónként egyet) lehetővé teszi az egyes kiszolgálók RDP-kapcsolaton keresztül. Ezt követően sikerült kell is kötegelt, attól függően, hogy a használt hálózati virtuális készülék képességeinek egy szabály be.
  3. Alkalmazás forgalomra vonatkozó szabályok: Két alkalmazás forgalmi szabályok vonatkoznak, az első az előtérbeli webes forgalom, és a második a háttér-forgalom (pl. web server adatrétegbeli). Ezek a szabályok konfigurálása lesz a hálózati architektúra (ahol a kiszolgálók kerülnek) függ, és a forgalom adatfolyamok (a forgalmat, és azoknak a portoknak használt iránya).
     * Az első szabály lehetővé teszi a kiszolgáló eléréséhez a tényleges alkalmazás forgalmat. A többi szabály engedélyezése a biztonsági, felügyeleti stb, alkalmazás szabályokat is mi engedélyezése külső felhasználót vagy szolgáltatást, az alkalmazás eléréséhez. Ebben a példában egyetlen webkiszolgálón való üzemeltetésekor 80-as porton van, így egyetlen alkalmazás tűzfalszabály átirányítja a bejövő forgalom a külső IP-címhez, a webes kiszolgálók belső IP-címre. A forgalmat munkamenet volna NAT lenni a belső kiszolgáló.
     * A második alkalmazás forgalmi szabály a háttérben szabály lehetővé teszi a webkiszolgáló felvegye a AppVM01 kiszolgáló (de nem AppVM02) bármely porton keresztül.
* Belső szabályainak (intra-VNet-forgalom)
  1. Kimenő Internet szabály: Ez a szabály lehetővé teszi, hogy a kijelölt hálózat minden hálózati forgalmat. Ez a szabály az általában egy alapértelmezett szabály már a tűzfalon, de a letiltott állapotú. Ez a szabály ehhez a példához engedélyezni kell.
  2. DNS-szabály: Ez a szabály lehetővé teszi, hogy csak a DNS (53-as port) forgalom számára a DNS-kiszolgáló. Az ebben a környezetben a háttérrendszer az előtér-legtöbb forgalom le van tiltva ez a szabály kifejezetten engedélyezi DNS a helyi alhálózaton.
  3. Alhálózati az alhálózati szabály: Ez a szabály az, hogy az összes olyan kiszolgálónak engedélyezi a háttérben alhálózat az előtérben lévő alhálózat alhálózat (de nem fordítva) bármely kiszolgálóhoz való csatlakozáshoz.
* Hibamentes (forgalomra vonatkozó szabály, amely nem felel meg a fentiek):
  1. Minden forgalmi szabály megtagadása: Ez mindig kell a végső szabály (tekintetében prioritás), és használja, így ha a forgalom nem sikerül, a rendszer eldobja a szabály által a fenti szabályok egyike. Ez az alapértelmezett szabály, és általában aktív, módosítás nélkül általában szükség van.

> [!TIP]
> A második alkalmazás forgalmi szabályt bármely portra engedélyezett könnyen ebben a példában egy valós forgatókönyv a legtöbb adott port és címtartomány csökkenteni a támadási felületet, a szabály használatával.
> 
> 

<br />

> [!IMPORTANT]
> A fenti szabályok jönnek létre, ha fontos tekintse át az egyes szabályt, hogy forgalom lesz engedélyezett vagy letiltott tetszés szerint prioritását. Ebben a példában a szabályok prioritási sorrendben szerepelnek. Könnyen téves rendezett szabályok miatt a tűzfalon kívüli zárolva. Legalább a ellenőrizze magát a tűzfalat a felügyeleti mindig a legmagasabb prioritású abszolút szabály.
> 
> 

### <a name="rule-prerequisites"></a>A szabály Előfeltételek
A virtuális gép fut, a tűzfal előfeltétele egy olyan nyilvános végpontok. A tűzfal forgalom feldolgozni a megfelelő nyilvános végpontok nyitva kell lennie. Három típusú forgalom ebben a példában; 1.) a felügyeleti forgalom vezérlő a tűzfal és a tűzfalszabályokat, 2) RDP-forgalmát szabályozhatja a windows-kiszolgálók és a 3.) alkalmazás-forgalmat. Ezek a felső forgalomtípushoz három oszlopot fele a tűzfalszabályok fent logikai nézetében.

> [!IMPORTANT]
> A kulcs takeway, hogy ne feledje, hogy **minden** forgalom határozza meg a tűzfalon keresztül. Ezért a IIS01 kiszolgálóhoz távoli asztal akkor is, ha az első End felhőalapú szolgáltatás, valamint az előtérben lévő alhálózat alhálózat, a kiszolgálóhoz való rendszer távoli asztali 8014 portra a tűzfalon kell, és majd engedélyezi a tűzfalat, hogy a belső átirányíthatja az RDP-kérelem IIS01 RDP-portjára. Az Azure-portálon "Csatlakozás" gomb nem fog működni, mert nincs közvetlen RDP elérési út IIS01 (szerint a portál látható). Ez azt jelenti, hogy az összes az internetről érkező kapcsolatokat lesz a szolgáltatás és a Port, pl. secscv001.cloudapp.net:xxxx (hivatkozás külső Port és a belső IP-cím és a Port hozzárendelését a fenti ábrán).
> 
> 

A végpont használatával megnyitható vagy a virtuális gép létrehozásakor vagy post build szerint végezheti el a példaként megadott parancsfájlt, és a következő kódrészletet az alább látható (Megjegyzés; bármely dollárjelet elem kezdődő (pl.: $VMName[$i]) egy felhasználó által definiált változó a parancsfájlból, ez a dokumentum hivatkozás szakaszában. A "$i" szögletes [$i] pedig a tömb számát egy adott virtuális gép virtuális gépek tömbben):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Bár nem jelenik meg világosan itt oka az, hogy vannak-e a változókat, de a végpontok használatát **csak** biztonsági felhőalapú szolgáltatás megnyitva. Ez azért szükséges, hogy minden bejövő forgalom kezelése (továbbítani, NAT, csökkent) a tűzfal.

A kezelési ügynök a számítógépen a tűzfal felügyeletéhez, és a szükséges konfigurációk létrehozásához telepíteni kell. A dokumentáció a tűzfal (vagy más NVA) szállítójával tekintse meg az eszközök felügyelete. A következő szakaszban, a tűzfal-szabályok létrehozása, és ez a szakasz többi magát, a tűzfal a szállító felügyeleti ügyfél (azaz nem az Azure portál vagy PowerShell) keresztül lesz konfigurálását.

Ügyfél letöltése és az ebben a példában használt Barracuda kapcsolódás itt található: [Barracuda NG rendszergazda](https://techlib.barracuda.com/NG61/NGAdmin)

Ha a tűzfal, de tűzfalszabályok létrehozása előtt a rendszer naplózza, vannak-e két előfeltétel objektumosztályok, amelyekkel könnyebben; a szabályok létrehozása Hálózati & szolgáltatás objektumok.

Ebben a példában három elnevezett hálózati objektumok meghatározott (egy a Frontend alhálózathoz és a Backend alhálózathoz, a DNS-kiszolgáló IP-címét is egy hálózati objektum) kell elhelyezni. Elnevezett hálózat; létrehozása a Barracuda NG felügyeleti ügyfél irányítópult-től kezdődő, a konfiguráció lapjának megjelenítéséhez, a működési konfigurációs szakaszban kattintson a Ruleset, majd kattintson "Hálózat" a tűzfal objektumok menüjében, majd új a hálózatok Szerkesztés menü. A hálózati objektum most hozhatók létre, a neve és az előtag hozzáadásával:

![Hozzon létre egy előtér-hálózati objektumot][3]

Ezzel létrehoz egy nevesített hálózatot a FrontEnd alhálózathoz, egy hasonló objektum, valamint a BackEnd alhálózathoz kell létrehozni. Most már az alhálózatok könnyebben hivatkozhat be a tűzfalszabályok nevét.

A DNS-kiszolgáló objektum:

![Hozzon létre egy DNS-kiszolgáló objektum][4]

Az egyetlen IP-címre való hivatkozás a DNS-szabályban a dokumentum későbbi szakaszában fog történni.

A második előfeltétel objektumok olyan szolgáltatások objektumok. Ezek az RDP csatlakozási porttal kiszolgálónként jelöli. A meglévő RDP-objektum alapértelmezett RDP-portjára van kötve, mivel 3389-es, az új szolgáltatásokat is létrehozható külső portot (8014-8026) forgalom engedélyezésére. Az új portok is hozzáadhatók a meglévő RDP-szolgáltatáshoz, de a bemutató megkönnyítése érdekében, az egyes szabályok kiszolgálónként is létrehozható. Kiszolgáló; új RDP-szabály létrehozása a Barracuda NG felügyeleti ügyfél irányítópult-től kezdődő, a konfiguráció lapjának megjelenítéséhez, a működési konfigurációs szakaszban kattintson a Ruleset, majd a Tűzfalobjektumokat menüjében kattintson a "Szolgáltatás", görgessen le a szolgáltatások és válassza ki a "RDP" szolgáltatást. Kattintson a jobb gombbal és válassza a másolás, majd kattintson a jobb gombbal, és válassza ki a beillesztés. Már létezik egy RDP-Copy1 szolgáltatás objektum, amely szerkeszthető. Kattintson a jobb gombbal az RDP-Copy1, és válassza a Szerkesztés, akár, ahogy az itt látható ablak jelenik szerkesztése szolgáltatás objektum:

![Alapértelmezett RDP-szabály másolása][5]

Az értékek szerkeszthető képviseli az RDP szolgáltatást egy adott kiszolgálóra. A AppVM01 a fenti alapértelmezett RDP szabály módosítani kell egy új szolgáltatás neve, leírása és a 8. ábra diagramon használt külső RDP-portjára megfelelően (Megjegyzés: a portok a megadott kiszolgálóhoz használt külső port változnak az RDP alapértelmezett 3389-es, a AppVM01 esetén a külső portra 8025) alább láthatók a módosított szolgáltatás :

![AppVM01 szabály][6]

Ezt a folyamatot meg kell ismételni a többi kiszolgálóját; RDP-szolgáltatások létrehozásához AppVM02 DNS01 és IIS01. Ezek a szolgáltatások létrehozásának működésképtelenné teszi a szabályok létrehozása egyszerűbb és viszonylag kézenfekvő a következő szakaszban.

> [!NOTE]
> Az RDP a szolgáltatás a tűzfal nem szükséges két okból; 1.) a virtuális gép tűzfal a Linux-alapú lemezkép, ezért SSH 22-es portot a virtuális gép felügyeleti helyett RDP és a 2.) 22-es portot használják, és más felügyeleti két portok használata engedélyezett az első felügyeleti szabály felügyeleti kapcsolat lehetővé teszi az itt leírt első.
> 
> 

### <a name="firewall-rules-creation"></a>Tűzfal-szabályok létrehozása
Ebben a példában használt tűzfal-szabályok három különböző, azok összes rendelkeznek, különböző ikonok:

Az alkalmazás átirányítási szabály: ![alkalmazás átirányítási ikonja][7]

A cél NAT-szabály: ![cél NAT ikon][8]

A hozzáférési szabály: ![átadni ikon][9]

Ezek a szabályok további információt a Barracuda webhelyen található.

A következő szabályok létrehozásának (vagy ellenőrizze a meglévő alapértelmezett szabályok), a Barracuda NG felügyeleti ügyfél irányítópultról indítása a konfiguráció lapjának megjelenítéséhez, a működési konfigurációs szakaszban kattintson szabálykészletben. A rács nevű, "Main szabályok" jelennek meg a meglévő aktív és inaktív szabályok a tűzfalon. A rács jobb felső sarkában a kisméretű, zöld "+" gombra, ide kattintva hozzon létre egy új szabályt (Megjegyzés: a tűzfal "zárolva" a változásokat, ha megjelenik egy "Lock" gombra, és nem tudja létrehozni vagy szabályok szerkesztése, kattintson erre a gombra kattintva "új" a szabálykészlet elérése és szerkesztése). Meglévő szabály szerkesztése, válassza ki, hogy a szabály, kattintson a jobb gombbal és válassza ki a szabály szerkesztése.

Ha a szabályok létrehozása és/vagy módosítva, kell lennie a tűzfalhoz leküldött és majd aktiválása, ha ez nem történik a szabály módosítások nem lépnek érvénybe. A leküldéses és az aktiválási folyamat az alábbiakban a részletek szabály leírását.

Az ebben a példában befejezéséhez szükséges minden egyes szabály tulajdonságait az alábbiak szerint:

* **Tűzfal-kezelési szabály**: az alkalmazás átirányítási szabály lehetővé teszi, hogy a forgalom számára a hálózati virtuális készülék, ebben a példában a felügyeleti portokat Barracuda NextGen tűzfal. A felügyeleti portjait 801, a 807 és opcionálisan 22. A külső és belső portok megegyeznek (azaz nem port fordítási). A szabály, a telepítő-MGMT-hozzáférés alapértelmezett szabály, amely alapértelmezés szerint engedélyezett (a Barracuda NextGen tűzfal 6.1-es verzió).
  
    ![Felügyeleti tűzfalszabály][10]

> [!TIP]
> Ez a szabály a forrás-címtér ilyen, ha a felügyeleti IP-címtartományok ismert, csökkenti a hatókör volna is csökkenteni a támadási felületet a felügyeleti portjaihoz.
> 
> 

* **RDP-szabályok**: ezek cél NAT-szabályok lehetővé teszi az egyes kiszolgálók RDP-kapcsolaton keresztül.
  Ez a szabály létrehozásához szükséges négy kritikus mezők van:
  
  1. Forrás – engedélyezi az RDP helyről való munkavégzéséből és a hivatkozás "A" forrás mező szerepel.
  2. Szolgáltatás – használja a megfelelő szolgáltatás objektumot ebben az esetben "AppVM01 RDP" korábban létrehozott, a külső portok átirányítja a kiszolgálók helyi IP-cím és port 3386 (az alapértelmezett RDP-port). Ez a szabály AppVM01 RDP elérésére szolgál.
  3. Cél-érdemes lehet a *helyi portot a tűzfalon*, "DCHP 1 helyi IP-címe" vagy eth0 statikus IP-címek használata. A sorszámmal (eth0, eth1 stb.) Ha a hálózati berendezések több helyi kapcsolatok lehetnek. A tűzfal küldi ki az portja (lehet ugyanaz, mint a fogadó port), a tényleges irányított célja a cél lista mezőben.
  4. Átirányítás – Ez a szakasz ismerteti, a virtuális készülék hol végső soron átirányítja a forgalmat. A legegyszerűbb átirányítás az, hogy az IP- és a Port (nem kötelező) a cél lista mezőben. Ha nincs port használatban van a bejövő kérelem a célport lesz (azaz nem fordítási), használható, ha a port kapja meg a portot is NAT volna mellett az IP cím.
     
     ![RDP tűzfalszabály][11]
     
     Összesen négy RDP-szabályokat kell létrehozni: 
     
     | Szabály neve | Kiszolgáló | Szolgáltatás | Cél lista |
     | --- | --- | --- | --- |
     | RDP-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
     | RDP-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
     | RDP-AppVM01 |AppVM01 |AppVM01 RDP |10.0.2.5:3389 |
     | RDP-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

> [!TIP]
> A forrás- és a szolgáltatás mezők körét szűkítését csökkenti a támadási felületet. A legtöbb korlátozott hatókör, amely lehetővé teszi a funkció kell használni.
> 
> 

* **Alkalmazás forgalomra vonatkozó szabályok**: két alkalmazás forgalmi szabályok vonatkoznak, az első az előtérbeli webes forgalom, és a második a háttér-forgalom (pl. web server adatrétegbeli). Ezek a szabályok lesz a hálózati architektúra (ahol a kiszolgálók kerülnek) függ, és a forgalom adatfolyamok (a forgalmat, és azoknak a portoknak használt iránya).
  
    A webes forgalom előtér-szabálya előbb tárgyalt van:
  
    ![Webes tűzfalszabály][12]
  
    A cél NAT-szabály lehetővé teszi, hogy a kiszolgáló eléréséhez a tényleges alkalmazás forgalmat. A többi szabály engedélyezése a biztonsági, felügyeleti stb, alkalmazás szabályokat is mi engedélyezése külső felhasználót vagy szolgáltatást, az alkalmazás eléréséhez. Ebben a példában egyetlen webkiszolgálón való üzemeltetésekor 80-as porton van, így egyetlen tűzfalszabályt alkalmazás átirányítja a bejövő forgalom a külső IP-címhez, a webes kiszolgálók belső IP-címre.
  
    **Megjegyzés:**: nincs hozzárendelve a cél lista mezőben port, így a bejövő port a 80-as (vagy a kiválasztott szolgáltatás a 443-as) fogja használni az átirányítás, a webkiszolgáló. Ha a webalkalmazás-kiszolgáló egy másik portot figyeli, például a 8080-as portra, 10.0.1.4:8080 Port átirányításának, valamint hogy a cél lista mező frissítése sikerült.
  
    A következő alkalmazás forgalmi szabály a háttérben szabály engedélyezése a webkiszolgálón. kérdezze meg a AppVM01 kiszolgáló (de nem AppVM02) bármely szolgáltatáson keresztül:
  
    ![Tűzfalszabály AppVM01][13]
  
    A hozzáférési szabály lehetővé teszi, hogy a IIS-kiszolgálót a AppVM01 eléréséhez Frontend alhálózaton (IP-cím 10.0.2.5) bármely porton bármely protokoll a webalkalmazás számára szükséges hozzáférést adatokhoz használatával.
  
    Ezen a képernyőn megtekinthet egy "\<explicit cél\>" célhelyként 10.0.2.5 jelölésére a cél mező szerepel. Ennek oka lehet, vagy explicit látható módon, vagy nevű hálózati objektum (mint a DNS-kiszolgáló előfeltételeit végzett). Ez a tűzfal, hogy mely metódus lesz használva a rendszergazda feladata. Egy Explict Desitnation 10.0.2.5 hozzáadni, kattintson duplán az első üres sor alatt \<explicit cél\> és adja meg a címet az előugró ablakban.
  
    A átadni szabálynak nem NAT van szükség, mivel ez belső forgalmat, így a csatlakozási módszerhez "No SNAT" értékre állítható.
  
    **Megjegyzés:**: A forrás hálózati ebben a szabályban bármilyen olyan erőforrás a FrontEnd alhálózaton csak lesz egy, vagy egy ismert bizonyos számú webkiszolgálók, ha egy hálózati objektum erőforrás sikerült létrehozni részletesebb e pontos IP-címek helyett a teljes Frontend alhálózathoz.

> [!TIP]
> Ez a szabály alkalmazza a szolgáltatás "Any" egyszerűbbé teszik a mintaalkalmazás kell beállítania és használnia, ez lehetővé teszi a is ICMPv4 (ping) lévő egyetlen szabályt. Ez azonban nem ajánlott. A portok és protokollok ("szolgáltatások") kell szűkíthető minimálisra, amely lehetővé teszi az alkalmazás művelet csökkenteni a támadási felületet között ezt a határt.
> 
> 

<br />

> [!TIP]
> Bár ez a szabály azt mutatja, hogy egy explicit cél hivatkozást használja, a tűzfal konfigurálása során minden konzisztens megközelítést kell használni. Javasoljuk, hogy a nevesített hálózati objektum használható egész könnyebb olvashatóság és támogatási. Az explicit-cél használható a csak egy alternatív módszert megjelenítése, és általában nem ajánlott (különösen a összetett konfigurációk).
> 
> 

* **Kimenő Internet szabály**: ehhez adja át a szabály lehetővé teszi a kiválasztott cél hálózatok átadása bármely forrás hálózati forgalmát. Ez a szabály egy alapértelmezett szabály már általában a Barracuda NextGen tűzfalon, de a letiltott állapotú. Ez a szabály csomagot jobb gombbal a szabály aktiválása parancs férhetnek hozzá. Itt látható a szabály utalás, ez a dokumentum Előfeltételek szakaszában létrehozott két helyi alhálózatok hozzáadása a forrásattribútum a szabály módosult.
  
    ![Kimenő tűzfalszabály][14]
* **DNS-szabály**: ehhez adja át a szabály lehetővé teszi, hogy csak DNS (53-as port) forgalom számára a DNS-kiszolgáló. Ebben a környezetben a háttérrendszer az előtér-legtöbb forgalom le van tiltva ez a szabály kifejezetten lehetővé teszi a DNS.
  
    ![DNS tűzfalszabály][15]
  
    **Megjegyzés:**: Ez a képernyő hibaüzenetet a csatlakozási módszerhez szerepel. Mivel ez a szabály belső IP-cím forgalom a belső IP-címhez, nem NATing szükség, a csatlakozási mód értéke "No SNAT" a hozzáférési szabályhoz.
* **Alhálózati az alhálózati szabály**: ehhez adja át a szabály egy alapértelmezett szabály, amely aktiválva, és módosíthatja, hogy az összes olyan kiszolgálónak engedélyezi a háttérben alhálózat előtér alhálózaton található minden kiszolgálóhoz való csatlakozáshoz. Ez a szabály az összes belső forgalmat, így a csatlakozási módszerhez SNAT nem állítható be.
  
    ![Tűzfal Intra-VNet szabály][16]
  
    **Megjegyzés:**: kétirányú jelölőnégyzet nincs bejelölve (se nem be van jelölve, a legtöbb szabályok), ez a szabály jelentős abban, hogy ez a szabály az "egy irányított" teszi, a kapcsolatot a számítógép a háttérrendszer alhálózatból az előtérhálózat, de nem a névkeresési. Ha a jelölőnégyzet be lett jelölve, ez a szabály lehetővé tenné kétirányú forgalmat, amely az a logikai diagram nem kívánatos.
* **Minden forgalmi szabály megtagadása**: a végső szabály (tekintetében prioritás) mindig legyen, és használja, így ha a forgalom nem felel meg az előző szabályok rendszer eldobja a szabály által. Ez az alapértelmezett szabály, és általában aktív, módosítás nélkül általában szükség van. 
  
    ![Tűzfal megtagadási szabály][17]

> [!IMPORTANT]
> A fenti szabályok jönnek létre, ha fontos tekintse át az egyes szabályt, hogy forgalom lesz engedélyezett vagy letiltott tetszés szerint prioritását. Ehhez a példához szabályokat kell szerepelniük kell a fő rácsvonalak továbbítása a Barracuda felügyeleti ügyfélben szabályok sorrendben.
> 
> 

## <a name="rule-activation"></a>A szabály aktiválás
Az a ruleset módosíthatja, hogy a logikai diagramja a megadását a ruleset kell feltölthetők a tűzfalhoz és majd aktiválva.

![Tűzfal szabály aktiválás][18]

A felügyeleti ügyfél jobb felső sarkában található egy fürt gombok vannak. A módosított szabályok és a tűzfalon küldendő a "Küldési módosítások" gombra, majd kattintson az "Aktiválás" gombra.

Az az aktiválás, a tűzfal szabálykészletben ez példa környezet build befejeződött.

## <a name="traffic-scenarios"></a>Forgalom forgatókönyvek
> [!IMPORTANT]
> A kulcs takeway, hogy ne feledje, hogy **minden** forgalom határozza meg a tűzfalon keresztül. Ezért a IIS01 kiszolgálóhoz távoli asztal akkor is, ha az első End felhőalapú szolgáltatás, valamint az előtérben lévő alhálózat alhálózat, a kiszolgálóhoz való rendszer távoli asztali 8014 portra a tűzfalon kell, és majd engedélyezi a tűzfalat, hogy a belső átirányíthatja az RDP-kérelem IIS01 RDP-portjára. Az Azure-portálon "Csatlakozás" gomb nem fog működni, mert nincs közvetlen RDP elérési út IIS01 (szerint a portál látható). Ez azt jelenti, hogy az összes az internetről érkező kapcsolatokat lesz a szolgáltatás és a Port, pl. secscv001.cloudapp.net:xxxx.
> 
> 

Ezek a forgatókönyvek a következő tűzfalszabályokat kell figyelembe:

1. Tűzfalkezelés
2. Az RDP IIS01
3. Az RDP DNS01
4. Az RDP AppVM01
5. Az RDP AppVM02
6. A webes alkalmazás forgalom
7. Alkalmazás-forgalom AppVM01
8. Kimenő Internet
9. Előtérkiszolgáló DNS01
10. Helyen belüli forgalmat (előtér csak a háttér)
11. Az összes megtagadása

A tényleges tűzfal szabálykészletben valószínűleg kell tanulmányozása mellett sok más szabály, bármely adott tűzfal szabályok is különböző prioritási számok, mint az alább felsorolt itt. A lista és a hozzájuk társított számokat arra, hogy csak a 11 szabályok és a relatív prioritását közülük közötti relevanciájának. Más szóval; a tényleges tűzfalon a "RDP való IIS01" Előfordulhat, hogy a szabály szám 5, de mindaddig, amíg azt nem éri el a "Tűzfal kezelése" szabály és a "RDP DNS01" szabály felett azzal a lista volna igazítása. A lista a rendszer is segítséget nyújtanak az alábbi kivonatosan mutatja; így forgatókönyvek például "FW szabály 9 (DNS)". Is kivonatosan mutatja a négy RDP-szabályok együttesen hívja meg a rendszer, "az RDP-szabályok" RDP független a forgalom forgatókönyv esetén.

Is az, hogy a hálózati biztonsági csoportok helyben előhívja a az előtér- és háttérszolgáltatások alhálózatok bejövő internetes forgalmat.

#### <a name="allowed-internet-to-web-server"></a>(Engedélyezett) Internet webkiszolgálón
1. Internetes felhasználói kérelmek HTTP oldal SecSvc001.CloudApp.Net (Internet Facing Felhőszolgáltatás)
2. Felhőalapú szolgáltatás fázisok forgalmat a 80-as port a tűzfal megfelelő felületéről 10.0.0.4:80 nyitott végpontok keresztül
3. Nincs NSG biztonság – alhálózati rendelt, a rendszer igen NSG-szabályok tűzfalhoz forgalom engedélyezése
4. Forgalom találatok belső IP-címet a tűzfal (10.0.1.4)
5. Tűzfal szabály feldolgozása kezdődik:
   1. FW 1. szabály (FW Mgmt) nem teljesül, a következő szabály áthelyezése
   2. FW szabályok 2 – 5 (RDP szabályok) nem teljesül, a következő szabály áthelyezése
   3. FW 6. szabály (App: webes) alkalmazásához forgalom engedélyezve van, tűzfal NAT úgy, hogy 10.0.1.4 (IIS01)
6. A Frontend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. NSG 1. szabály (elérésű internetes) nem vonatkozik (a forgalom NAT lett volna a tűzfal, így a forrás-cím már a tűzfalon, amelyek a biztonság – alhálózati a és a Frontend alhálózathoz NSG-t kell lennie a "helyi" forgalom láthatók, és így engedélyezve van), áthelyezése a következő szabály
   2. Alapértelmezett NSG-szabályok alhálózati az alhálózati forgalom engedélyezéséhez forgalom engedélyezve van, akkor állítsa le az NSG-szabály feldolgozása
7. IIS01 figyeli a webes forgalom, ezt a kérelmet kap, és elindítja a kérés feldolgozása
8. IIS01 megpróbálja indít el az FTP-munkameneteket a AppVM01 a Backend alhálózathoz
9. A UDR útvonal Frontend alhálózaton lehetővé teszi a tűzfal, a következő ugrás
10. Nincs kimenő szabályok Frontend alhálózaton, forgalom engedélyezve van
11. Tűzfal szabály feldolgozása kezdődik:
    1. FW 1. szabály (FW Mgmt) nem teljesül, a következő szabály áthelyezése
    2. 2 – 5 (RDP szabályok) FW szabály nem teljesül, a áthelyezése a következő szabály
    3. FW 6. szabály (App: webes) nem teljesül, a következő szabály áthelyezése
    4. FW szabály 7 (App: háttér) alkalmazásához forgalom engedélyezve van, a tűzfal 10.0.2.5 (AppVM01) forgalmat továbbítja.
12. A Backend alhálózathoz bejövő szabály feldolgozása kezdődik:
    1. NSG 1. szabály (elérésű internetes) nem teljesül, a közvetkező szabályának áthelyezése
    2. Alapértelmezett NSG-szabályok alhálózati az alhálózati forgalom engedélyezéséhez forgalom engedélyezve van, akkor állítsa le az NSG-szabály feldolgozása
13. AppVM01 a kérelem fogadása és a munkamenet indítása és válaszol
14. Backend alhálózathoz UDR útvonalon lehetővé teszi a tűzfal, a következő ugrás
15. Mivel nincsenek engedélyezve van-e a nem kimenő NSG-szabályok a Backend alhálózathoz válasz
16. Mivel ez adott vissza forgalom a munkamenetet a tűzfal továbbítja a választ a webkiszolgáló (IIS01)
17. Frontend alhálózathoz bejövő szabály feldolgozása kezdődik:
    1. NSG 1. szabály (elérésű internetes) nem teljesül, a közvetkező szabályának áthelyezése
    2. Alapértelmezett NSG-szabályok alhálózati az alhálózati forgalom engedélyezéséhez forgalom engedélyezve van, akkor állítsa le az NSG-szabály feldolgozása
18. Az IIS-kiszolgálót a választ kap, a tranzakció AppVM01, és ha igen összeállítása a HTTP-válasz, a kérelmező kap a HTTP-válasz
19. Mivel nincsenek engedélyezve van-e a Frontend alhálózaton a válasz nem kimenő NSG-szabályok
20. A HTTP-válasz találatok a tűzfalat, és mivel ez a válasz NAT munkamenetet elfogadta a tűzfal
21. A tűzfal majd átirányítja a felhasználókat a választ az internetes felhasználói
22. Mivel nincsenek nem kimenő NSG-szabályok vagy UDR útválasztók ugrásainak a válasz engedélyezett Frontend alhálózaton található, és az internetes felhasználó megkapja a kért weblap.

#### <a name="allowed-internet-rdp-to-backend"></a>(Engedélyezett) Internet RDP háttérrendszeréhez
1. Kiszolgálói rendszergazda interneten keresztül SecSvc001.CloudApp.Net:8025, ahol 8025-e a "RDP AppVM01" tűzfalszabály felhasználó lehet hozzárendelve portszáma AppVM01 RDP-munkamenetet kérelmek
2. A felhőalapú szolgáltatás továbbítja a nyitott végpontok forgalmát 8025 porton 10.0.0.4:8025 a tűzfal megfelelő felületéről
3. Nincs NSG biztonság – alhálózati rendelt, a rendszer igen NSG-szabályok tűzfalhoz forgalom engedélyezése
4. Tűzfal szabály feldolgozása kezdődik:
   1. FW 1. szabály (FW Mgmt) nem teljesül, a következő szabály áthelyezése
   2. FW 2. szabály (RDP IIS) nem teljesül, a következő szabály áthelyezése
   3. FW 3. szabály (RDP DNS01) nem teljesül, a következő szabály áthelyezése
   4. FW 4. szabály (RDP AppVM01) teljesül, a forgalom engedélyezve van, tűzfal NAT úgy, hogy 10.0.2.5:3386 (RDP-portjához AppVM01)
5. A Backend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. NSG 1. szabály (elérésű internetes) nem vonatkozik (a forgalom NAT lett volna a tűzfal, így a forrás-cím már a tűzfalon, amelyek a biztonság – alhálózati a és a Backend alhálózathoz NSG-t kell lennie a "helyi" forgalom láthatók, és így engedélyezve van), áthelyezése a következő szabály
   2. Alapértelmezett NSG-szabályok alhálózati az alhálózati forgalom engedélyezéséhez forgalom engedélyezve van, akkor állítsa le az NSG-szabály feldolgozása
6. AppVM01 RDP-forgalmát a figyeli és reagál
7. Nincsenek kimenő NSG-szabályok, az alapértelmezett szabályok vonatkoznak, és a forgalom engedélyezve van
8. UDR útvonalak kimenő forgalmat a következő ugrásként tűzfal
9. Mivel ez adott vissza forgalom a munkamenetet a tűzfal továbbítja a választ az internetes felhasználói
10. RDP-munkamenetbe engedélyezve van
11. Felhasználói nevének jelszavának megadását kéri az AppVM01

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Engedélyezett) Web Server DNS-címkeresés DNS-kiszolgálón
1. Webalkalmazás-kiszolgálón, IIS01, egy adatcsatorna www.data.gov: igényeinek, de a címek feloldására igényeinek.
2. A hálózati konfigurációt a VNet listák DNS01 (a háttér alhálózaton 10.0.2.4) elsődleges DNS-kiszolgálóként, IIS01 küld a DNS-kérelem DNS01
3. UDR útvonalak kimenő forgalmat a következő ugrásként tűzfal
4. Nincsenek kimenő NSG-szabályok a Frontend alhálózathoz vannak kötve, forgalom engedélyezve van
5. Tűzfal szabály feldolgozása kezdődik:
   1. FW 1. szabály (FW Mgmt) nem teljesül, a következő szabály áthelyezése
   2. 2 – 5 (RDP szabályok) FW szabály nem teljesül, a áthelyezése a következő szabály
   3. FW szabályok 6 és 7 (Alkalmazásszabályok) nem vonatkoznak, folytassa a következő szabály
   4. FW szabály 8 (az internethez) nem teljesül, a közvetkező szabályának áthelyezése
   5. FW szabály 9 (DNS) alkalmazni, a forgalom engedélyezve van, a tűzfal 10.0.2.4 (DNS01) forgalmat továbbítja.
6. A Backend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. NSG 1. szabály (elérésű internetes) nem teljesül, a közvetkező szabályának áthelyezése
   2. Alapértelmezett NSG-szabályok alhálózati az alhálózati forgalom engedélyezéséhez forgalom engedélyezve van, akkor állítsa le az NSG-szabály feldolgozása
7. DNS-kiszolgáló a kérelmet kap.
8. DNS-kiszolgáló nem rendelkezik a gyorsítótárazott címmel és egy legfelső szintű DNS-kiszolgáló kéri az interneten
9. UDR útvonalak kimenő forgalmat a következő ugrásként tűzfal
10. Nincs kimenő NSG-szabályok a Backend alhálózathoz, forgalom engedélyezve van
11. Tűzfal szabály feldolgozása kezdődik:
    1. FW 1. szabály (FW Mgmt) nem teljesül, a következő szabály áthelyezése
    2. 2 – 5 (RDP szabályok) FW szabály nem teljesül, a áthelyezése a következő szabály
    3. FW szabályok 6 és 7 (Alkalmazásszabályok) nem vonatkoznak, folytassa a következő szabály
    4. FW szabály 8 (az internethez) alkalmazni, a forgalom engedélyezve van, a munkamenet SNAT ki, hogy gyökér DNS-kiszolgáló az interneten
12. Internetes DNS-kiszolgáló válaszol, mivel a munkamenet kezdeményezték a tűzfal, a válasz elfogadta a tűzfal
13. Ez ugyanis a munkamenetet, a tűzfal továbbítja a kezdeményező kiszolgáló DNS01 válasz
14. A Backend alhálózathoz bejövő szabály feldolgozása kezdődik:
    1. NSG 1. szabály (elérésű internetes) nem teljesül, a közvetkező szabályának áthelyezése
    2. Alapértelmezett NSG-szabályok alhálózati az alhálózati forgalom engedélyezéséhez forgalom engedélyezve van, akkor állítsa le az NSG-szabály feldolgozása
15. A DNS-kiszolgáló fogadja és gyorsítótárazza a választ, és ezután válaszol-e az eredeti kérelem vissza a IIS01
16. Backend alhálózathoz UDR útvonalon lehetővé teszi a tűzfal, a következő ugrás
17. Kimenő NSG-szabályok nem létezik az a Backend alhálózathoz, forgalom engedélyezve van
18. Ez a tűzfalon munkamenetet, a válasz továbbítja a tűzfal, az IIS-kiszolgálón
19. Frontend alhálózathoz bejövő szabály feldolgozása kezdődik:
    1. Nincs érvényes bejövő szabály NSG a Frontend alhálózathoz, így nincs az NSG-szabályok vonatkoznak a Backend alhálózathoz-forgalom
    2. A alapértelmezett rendszerszintű szabály, amely lehetővé teszi az alhálózatok közötti forgalmat lehetővé tenné a forgalmat, így a forgalom engedélyezve van
20. IIS01 megkapja válaszát DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Engedélyezett) A háttérkiszolgáló előtér-kiszolgálóhoz
1. RDP-kapcsolaton keresztül AppVM02 bejelentkezett rendszergazda olyan fájlt kér, közvetlenül a kiszolgálóról IIS01 windows Fájlkezelőben keresztül
2. Backend alhálózathoz UDR útvonalon lehetővé teszi a tűzfal, a következő ugrás
3. Mivel nincsenek engedélyezve van-e a nem kimenő NSG-szabályok a Backend alhálózathoz válasz
4. Tűzfal szabály feldolgozása kezdődik:
   1. FW 1. szabály (FW Mgmt) nem teljesül, a következő szabály áthelyezése
   2. 2 – 5 (RDP szabályok) FW szabály nem teljesül, a áthelyezése a következő szabály
   3. FW szabályok 6 és 7 (Alkalmazásszabályok) nem vonatkoznak, folytassa a következő szabály
   4. FW szabály 8 (az internethez) nem teljesül, a közvetkező szabályának áthelyezése
   5. FW szabály 9 (DNS) nem teljesül, a következő szabály áthelyezése
   6. FW szabály 10 (Intra-alhálózat) alkalmazza, a forgalom engedélyezve van, a tűzfal forgalom átadása 10.0.1.4 (IIS01)
5. Frontend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. NSG 1. szabály (elérésű internetes) nem teljesül, a közvetkező szabályának áthelyezése
   2. Alapértelmezett NSG-szabályok alhálózati az alhálózati forgalom engedélyezéséhez forgalom engedélyezve van, akkor állítsa le az NSG-szabály feldolgozása
6. Ha a megfelelő hitelesítési és engedélyezési, IIS01 elfogadja a kérelmet, és válaszol
7. A UDR útvonal Frontend alhálózaton lehetővé teszi a tűzfal, a következő ugrás
8. Mivel nincsenek engedélyezve van-e a Frontend alhálózaton a válasz nem kimenő NSG-szabályok
9. Ez ugyanis, hogy a tűzfalon a meglévő munkamenetekben ezt a választ kap, és a tűzfalon AppVM02 válasz adja vissza.
10. Backend alhálózathoz bejövő szabály feldolgozása kezdődik:
    1. NSG 1. szabály (elérésű internetes) nem teljesül, a közvetkező szabályának áthelyezése
    2. Alapértelmezett NSG-szabályok alhálózati az alhálózati forgalom engedélyezéséhez forgalom engedélyezve van, akkor állítsa le az NSG-szabály feldolgozása
11. AppVM02 a válasz fogadása

#### <a name="denied-internet-direct-to-web-server"></a>(Megtagadva) A webalkalmazás-kiszolgáló közvetlen Internet
1. Internetes felhasználó megpróbál hozzáférni a webkiszolgálóhoz, IIS01, a FrontEnd001.CloudApp.Net szolgáltatáson keresztül
2. Mivel nincsenek végpontok nyissa meg a HTTP-forgalom, ez nem lenne továbbítása a felhőalapú szolgáltatás, és így elérni a kiszolgálót
3. Ha valamilyen okból a végpontok nyitva, a (elérésű internetes) NSG a Frontend alhálózaton megakadályozza a forgalom
4. Végezetül a Frontend alhálózathoz UDR útvonal elküldése minden kimenő forgalom IIS01 a következő ugrásként a tűzfalhoz, és a tűzfalon volna megjelenik ez az aszimmetrikus adatforgalom és dobja el a kimenő válasz, így nincsenek a felhőalapú szolgáltatás nem engedélyezett/nem megfelelő elérésének megakadályozásához keresztül az internethez, és IIS01 közötti védelmi legalább három független rétegek.

#### <a name="denied-internet-to-backend-server"></a>(Megtagadva) Internet háttérkiszolgálóra
1. Internetes felhasználó megpróbál hozzáférni egy fájl AppVM01 a BackEnd001.CloudApp.Net szolgáltatáson keresztül
2. Mivel nincsenek nyissa meg a fájlmegosztás nincsenek végpontok, ez nem lenne továbbítja a felhőalapú szolgáltatás, és így elérni a kiszolgálót
3. Ha valamilyen okból a végpontok nyitott, az NSG-t (Internet blokk) megakadályozza a forgalom
4. Végezetül a UDR útvonal elküldése minden kimenő forgalom AppVM01 a következő ugrásként a tűzfalhoz, és a tűzfalon volna megjelenik ez az aszimmetrikus adatforgalom és dobja el a kimenő válasz, így nincsenek legalább három független rétegek használatával a felhőalapú szolgáltatás nem engedélyezett/nem megfelelő elérésének megakadályozásához, az internetes és AppVM01 közötti védelmi.

#### <a name="denied-frontend-server-to-backend-server"></a>(Megtagadva) Háttérkiszolgálóra Előtéri kiszolgáló
1. Tegyük fel, IIS01 biztonsági szempontból sérült és fut-e a háttérkiszolgálók alhálózati beolvasására tett kísérlet rosszindulatú kód.
2. A Frontend alhálózathoz UDR útvonal elküldése minden kimenő forgalom a IIS01 a tűzfalhoz következő ugrásként. Ez az, amelyet a sérült biztonságú VM lehet megváltoztatni.
3. A tűzfal volna feldolgozni a forgalmat, ha a kérelmező AppVM01, vagy a DNS-kiszolgáló DNS-keresések, amely a forgalom potenciálisan letiltotta a tűzfal (miatt FW szabályok 7 és 9). Az összes többi forgalom FW szabály 11 (az összes megtagadása) lenne blokkolja.
4. Ha speciális fenyegetésészlelés volt engedélyezve a tűzfal (a jelen dokumentum nem ismerteti, az adott hálózati berendezések advanced threat képességek gyártói dokumentációjában), még akkor is, volna a jelen dokumentumban tárgyalt alapvető továbbítási szabályok által engedélyezett forgalmat sikerült előzhető, ha a forgalom ismert aláírások vagy minta, egy az advanced threat szabály jelzőt.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Megtagadva) A DNS-kiszolgáló internetes DNS-címkeresés
1. Internetes felhasználó megpróbálja megkeresni az egy belső DNS-rekordot a DNS01 BackEnd001.CloudApp.Net szolgáltatáson keresztül 
2. Mivel nincsenek végpontok nyissa meg a DNS-forgalmat, ez nem lenne továbbítása a felhőalapú szolgáltatás, és így elérni a kiszolgálót
3. Ha valamilyen okból a végpontok nyitva, a Frontend alhálózaton NSG szabály (elérésű internetes) megakadályozza a a forgalom
4. Végezetül a Backend alhálózathoz UDR útvonal elküldése minden kimenő forgalom DNS01 a következő ugrásként a tűzfalhoz, és a tűzfalon volna megjelenik ez az aszimmetrikus adatforgalom és dobja el a kimenő válasz, így nincsenek a felhőalapú szolgáltatás nem engedélyezett/nem megfelelő elérésének megakadályozásához keresztül az internethez, és DNS01 közötti védelmi legalább három független rétegek.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Megtagadva) Az SQL-hozzáférés tűzfalon keresztül Internet
1. Internetes felhasználó SQL adatokat kér a SecSvc001.CloudApp.Net (Internet Facing Felhőszolgáltatás)
2. Mivel nincsenek végpontok nyissa meg az SQL, ez nem lenne továbbítja a felhőalapú szolgáltatás, és a tűzfal nem tudnák elérni
3. Ha valamilyen okból SQL végpontok nyitva, a tűzfal szabály feldolgozása volna megkezdéséhez:
   1. FW 1. szabály (FW Mgmt) nem teljesül, a következő szabály áthelyezése
   2. FW szabályok 2 – 5 (RDP szabályok) nem teljesül, a következő szabály áthelyezése
   3. FW szabály 6 és 7 (alkalmazás szabályok) nem vonatkoznak, folytassa a következő szabály
   4. FW szabály 8 (az internethez) nem teljesül, a közvetkező szabályának áthelyezése
   5. FW szabály 9 (DNS) nem teljesül, a következő szabály áthelyezése
   6. FW szabály 10 (Intra-alhálózat) nem teljesül, a közvetkező szabályának áthelyezése
   7. FW szabály 11 (az összes megtagadása) alkalmazza, a forgalom letiltott, befejezése szabály feldolgozása

## <a name="references"></a>Referencia
### <a name="main-script-and-network-config"></a>Fő parancsfájlt és a hálózati konfiguráció
A teljes parancsfájl menthető egy olyan PowerShell-parancsfájlt. A hálózati konfiguráció mentése "NetworkConf2.xml" fájlba.
Szükség szerint módosítsa a felhasználó által definiált változókat. Futtassa a parancsfájlt, majd kövesse a tűzfal szabály beállítása a fenti.

#### <a name="full-script"></a>Teljes körű parancsfájl
Ez a parancsfájl lesz, a felhasználó által definiált változóknak alapján:

1. Csatlakozás Azure-előfizetéshez
2. Új tárfiók létrehozása
3. Hozzon létre egy új virtuális hálózat és a három alhálózatot a hálózati konfigurációs fájlban meghatározottak szerint
4. Build öt virtuális gépek; 1 tűzfal és a 4 a windows server virtuális gépek
5. Adja meg UDR többek között:
   1. Két új útvonal-táblázatok létrehozása
   2. Útvonalak hozzáadása a táblák
   3. Táblák kötődni megfelelő alhálózatokat
6. Engedélyezze az NVA IP-továbbítás
7. Adja meg, beleértve az NSG:
   1. Egy NSG létrehozása
   2. Szabály hozzáadása
   3. Az NSG kötése a megfelelő alhálózatokat

A PowerShell parancsfájl fusson helyben egy internethez csatlakoztatott PC vagy a kiszolgáló.

> [!IMPORTANT]
> Ezt a parancsfájlt, amikor előfordulhat figyelmeztetések vagy a PowerShellben pop más tájékoztató üzeneteit. Piros csak hibaüzenetek aggodalomra.
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
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
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
Az XML-fájl mentése frissített hellyel rendelkező, és a hivatkozás hozzáadása ehhez a fájlhoz a $NetworkConfigFile változó a parancsfájlban.

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

#### <a name="sample-application-scripts"></a>Alkalmazás mintaparancsfájlok
Szeretne telepíteni egy mintaalkalmazás ezzel és más DMZ példák, ha egy adtak meg, a következő hivatkozásra: [minta alkalmazás-parancsfájl][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Kétirányú DMZ NVA, NSG és UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "A tűzfalszabályok logikai ábrázolása"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Hozzon létre egy előtér-hálózati objektumot"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Hozzon létre egy DNS-kiszolgáló objektum"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Alapértelmezett RDP-szabály másolása"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "AppVM01 szabály"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Alkalmazás átirányítási ikonja"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Cél NAT ikon"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Pass ikon"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Felügyeleti tűzfalszabály"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "RDP tűzfalszabály"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Webes tűzfalszabály"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Tűzfalszabály AppVM01"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Kimenő tűzfalszabály"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "DNS tűzfalszabály"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Tűzfal Intra-VNet szabály"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Tűzfal megtagadási szabály"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Tűzfal szabály aktiválás"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
