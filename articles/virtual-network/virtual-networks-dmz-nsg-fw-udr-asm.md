---
title: Szegélyhálózat-alapú hálózati például – a peremhálózaton, a tűzfal, udr-t és az NSG-k védelme hálózatok |} A Microsoft Docs
description: Szegélyhálózaton (más néven DMZ), a tűzfal, felhasználó által meghatározott útválasztás (UDR) és hálózati biztonsági csoportok (NSG-k) hozhat létre.
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
ms.openlocfilehash: 104eae7c8d18c32fd5b9b84147df2bf121345c22
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277415"
---
# <a name="example-3-build-a-perimeter-network-to-protect-networks-with-a-firewall-udr-and-nsgs"></a>3. példa: Hozhat létre egy szegélyhálózaton hálózatok egy tűzfal, udr-t és az NSG-k védelme

[Térjen vissza a biztonsági határ ajánlott eljárások lap][HOME]

Ebben a példában létrehozott egy szegélyhálózaton (DMZ-t, a demilitarizált zónában és a demilitarizált is ismert). A példában a tűzfal, négy Windows-kiszolgálók, felhasználó által meghatározott útválasztás (UDR), IP-továbbítás és hálózati biztonsági csoportok (NSG) valósítja meg. Ez a cikk végigvezeti az adja meg az egyes lépések jobban megértheti a megfelelő parancsokat. A forgalom forgatókönyv szakaszban is ismerteti a mélység hogyan forgalom halad keresztül a szegélyhálózaton a rétege. Végül a references szakaszában tartalmaz, a kód és a vonatkozó útmutatást ebben a környezetben, így tesztelése, és kísérletezzen a különböző forgatókönyvekben.

![Az nva-t, a NSG-t és az udr-t és a kétirányú szegélyhálózat][1]

## <a name="environment-setup"></a>A környezet beállítása

Ebben a példában egy előfizetést, amely a következő összetevőket tartalmazza:

* Három felhőszolgáltatások: SecSvc001 FrontEnd001 és BackEnd001
* Virtuális hálózat (CorpNetwork) és három alhálózatot: SecNet, FrontEnd és BackEnd
* Egy hálózati virtuális berendezések: a tűzfal az SecNet alhálózathoz csatlakozó
* Egy Windows-kiszolgáló, amely egy alkalmazás webkiszolgáló jelöli: IIS01
* Két Windows-kiszolgálók, amelyek a háttér-alkalmazáskiszolgálókat: AppVM01, AppVM02
* DNS-kiszolgálót képviselő Windows-kiszolgáló: DNS01

A [szakaszban hivatkozott](#references) tartalmaz egy PowerShell-parancsprogram, amely összeállítja a legtöbb a környezetben, az itt leírtak szerint. Ez a cikk más módon nem ad részletes útmutatást a virtuális gépek (VM) és a virtuális hálózatok létrehozásához.

A környezet létrehozásához:

1. Mentse a hálózati konfigurációs XML-fájl tartalmazza a [szakasz hivatkozhat](#references). Nevét, helyét és IP-címeket az adott esethez megfelelő frissíteni kell.
1. Frissítse a felhasználói változók (például az előfizetések, szolgáltatásnevek, és így tovább) az adott környezetnek megfelelő teljes szkript.
1. Hajtsa végre a szkriptet a PowerShellben.

> [!NOTE]
> A PowerShell-parancsfájl a megadott területet meg kell egyeznie a régió van megadva. a hálózati konfigurációs XML-fájlban.

A szkript sikeres futtatása után a következő lépéseket:

1. A tűzfalszabályok beállítása. Tekintse meg a [tűzfalszabályok](#firewall-rules) szakaszban.
1. Szükség esetén a hivatkozások szakaszban a két parancsfájlok használatával egy webalkalmazás, a webkiszolgáló és alkalmazás-kiszolgálón, hogy a tesztelés a DMZ-konfiguráció beállítása.

## <a name="user-defined-routing"></a>Felhasználó által meghatározott Útválasztás

Alapértelmezés szerint a következő rendszerútvonalak vannak meghatározva:

    Effective routes :
     Address Prefix    Next hop type    Next hop IP address Status   Source
     --------------    -------------    ------------------- ------   ------
     {10.0.0.0/16}     VNETLocal                            Active   Default
     {0.0.0.0/0}       internet                             Active   Default
     {10.0.0.0/8}      Null                                 Active   Default
     {100.64.0.0/10}   Null                                 Active   Default
     {172.16.0.0/12}   Null                                 Active   Default
     {192.168.0.0/16}  Null                                 Active   Default

VNETLocal, mindig a meghatározott cím prefix(es) az adott virtuális hálózaton. Például azt változik a virtuális hálózatról virtuális hálózat, attól függően, hogyan mindegyik megadott virtuális hálózat van meghatározva. A fennmaradó rendszerútvonalak statikusak, és az alapértelmezett látható módon.

Prioritás, mint az útvonalakat a leghosszabb előtag Match (LPM) metódus használatával dolgozza fel. Így a legtöbb adott útvonal a táblázatban megadott címre vonatkozik.

Ezért egy kiszolgálót, például DNS01 szánt adatforgalom (10.0.2.4 cím) a helyi hálózati (10.0.0.0/16) a virtuális hálózaton keresztül legyen irányítva a 10.0.0.0/16 útvonal miatt.  A 10.0.2.4 cím a 10.0.0.0/16 útvonal a nejvíce specifické útvonal. Ez a szabály vonatkozik, annak ellenére, hogy a 10.0.0.0/8, és a 0.0.0.0/0 is alkalmazható. Kevésbé specifikus, azonban, így azok nem befolyásolják a forgalmat. Forgalom 10.0.2.4 cím a következő ugrás, így a rendeltetési helyre irányítja a rendszer a helyi virtuális hálózat rendelkezik.

Például a 10.0.0.0/16 útvonal 10.1.1.1 szánt adatforgalomra nem vonatkozik. A 10.0.0.0/8 rendszerútvonal azért nejvíce specifické, eldobja a forgalmat, vagy a "fekete furatos" a következő ugrás má hodnotu Null.

Ha a cél nem vonatkozik a Null-előtagok vagy a VNETLocal előtagokat, forgalom a legkevésbé egyedi útvonalat (0.0.0.0/0) követi. Lesz irányítva az interneten a következő ugrás, és az Azure internetes peremhálózatra kívül.

Ha az útvonaltáblában lévő két azonos előtagokat, preferencia sorrendje alapján az útvonal forrásattribútum:

1. VirtualAppliance: Egy felhasználó által megadott útvonal, manuálisan adja hozzá a táblázathoz.
1. VPN-átjáró: Egy dinamikus hálózati protokoll által hozzáadott egy dinamikus útválasztási (BGP-hibrid hálózatok együtt használva). Ezeket az útvonalakat idővel változhat, a dinamikus protokoll automatikusan megjelennek-e módosítások társhálózatban.
1. Alapértelmezett: A rendszerútvonalak, a helyi virtuális hálózat és a statikus bejegyzések az útvonal a fenti táblázatban látható módon.

> [!NOTE]
> Mostantól használhatja felhasználó által meghatározott útválasztás (UDR) az ExpressRoute és VPN-átjárók kimenő és bejövő létesítmények közötti forgalom átirányítását egy hálózati virtuális készüléket (NVA) kényszerítése.

### <a name="create-local-routes"></a>Helyi útvonalak létrehozása

Ebben a példában két útválasztási táblázat, egyet-egyet az előtér- és alhálózatokat. Minden tábla be vannak töltve az adott alhálózat megfelelő statikus útvonalakat. Jelen példában mindegyik tábla rendelkezik három útvonalakat:

1. Nincs definiálva következő ugrás a helyi alhálózat forgalmának. Ez az útvonal lehetővé teszi, hogy a helyi alhálózat forgalmának, hogy a tűzfal megkerülését.
2. Virtuális hálózati forgalmat egy következő ugrási tűzfal definiálva. Ez az útvonal felülbírálja az alapértelmezett szabályt, amely lehetővé teszi a helyi virtuális hálózati forgalom közvetlenül továbbítani.
3. Az összes többi forgalom (0/0) egy következő ugrási definiálva, a tűzfal.

Miután az útválasztási táblázatok jönnek létre, azok az alhálózatok vannak kötve. Az előtérbeli alhálózat útválasztási táblázatához hasonlóan kell kinéznie:

    Effective routes :
     Address Prefix    Next hop type       Next hop IP address  Status   Source
     --------------    ------------------  -------------------  ------   ------
     {10.0.1.0/24}     VNETLocal                                Active
     {10.0.0.0/16}     VirtualAppliance    10.0.0.4             Active
     {0.0.0.0/0}       VirtualAppliance    10.0.0.4             Active

Ebben a példában a következő parancsokat használja az útvonaltábla létrehozása, és adjon hozzá egy felhasználó által megadott útvonalat, majd társítanak az útválasztási táblázatot egy alhálózathoz. Kezdődő nevű elemek `$`, mint például `$BESubnet`, felhasználó által definiált változók a hivatkozás a szakaszban a parancsfájlból.

1. Először hozza létre az alapszintű útválasztási táblázatban. A következő kódrészletet a táblázat tartalmazza a háttérbeli alhálózatot hoz létre. A teljes szkript is létrehoz egy megfelelő táblát az előtér-alhálózat.

   ```powershell
   New-AzureRouteTable -Name $BERouteTableName `
       -Location $DeploymentLocation `
       -Label "Route table for $BESubnet subnet"
   ```

1. Az útvonaltábla létrehozása után az adott felhasználó által megadott útvonalakat is hozzáadhat. Az alábbi kódrészlet Megadja, hogy a virtuális berendezésen keresztül irányítja-e a minden forgalmat (0.0.0.0/0). Egy változó `$VMIP[0]` be az IP-cím hozzárendelve, amikor a virtuális készülék a szkriptben korábban lett létrehozva. A teljes szkript is létrehoz egy megfelelő szabályt az előtér-táblázatban.

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. Az előzőt útvonal felülbírálja az alapértelmezett "0.0.0.0/0" útvonalat, de a 10.0.0.0/16 alapértelmezett szabály engedélyezi a forgalom közvetlenül a rendeltetési helyre és nem a hálózati virtuális berendezés továbbítani a virtuális hálózaton belül. Ez a viselkedés kijavítása érdekében hozzá kell a következő szabálynak:

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. Ezen a ponton rendelkezik egy döntés. A két előző szabályokat minden forgalmat irányítson a tűzfalhoz értékelés, többek között a forgalmat egy egyetlen alhálózaton belül. Érdemes ezt a viselkedést. Ha nem, akkor azonban is engedélyezi a forgalmat egy tűzfal bevonása nélkül irányíthatja a helyi alhálózaton belül. Adjon hozzá egy harmadik, az adott szabály, amely közvetlenül a helyi alhálózat felé irányuló minden cím irányítja (NextHopType = VNETLocal).

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
           -NextHopType VNETLocal
   ```

1. Végül az útválasztási táblázat létrehozása, és a felhasználó által megadott útvonalak feltöltve, után kell kötni a tábla egy alhálózathoz. A következő kódrészletet a tábla a háttérbeli alhálózat van kötve. A teljes szkript is van kötve az előtér-útválasztási táblázatot az előtérbeli alhálózat.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
       -SubnetName $BESubnet `
       -RouteTableName $BERouteTableName
   ```

## <a name="ip-forwarding"></a>IP-továbbítás

IP-továbbítás kiegészítő funkciója az udr-t. Ezt a beállítást egy olyan virtuális berendezésre lehetővé teszi, hogy a forgalom a készülék nem fogadását, és majd továbbítsa a forgalmat a végső rendeltetési helyükre.

Például ha AppVM01 érkező kérést küld a DNS01 kiszolgálóhoz, UDR irányítja a forgalmat a tűzfalhoz. Az IP-továbbítás engedélyezve van a forgalmat a DNS01 cél (10.0.2.4 cím) fogadja el a tűzfalat (10.0.0.4), majd továbbítja a végső rendeltetési helyükre (10.0.2.4 cím). IP-továbbítás engedélyezve van a tűzfalon, nélkül forgalom nem fogadja el a készülék annak ellenére, hogy az útvonaltáblát tartalmaz a következő ugrás a tűzfalon.

> [!IMPORTANT]
> Fontos, hogy a felhasználó által meghatározott útválasztás együtt IP-továbbítás engedélyezése.

IP-továbbítást a virtuális gépek létrehozáskor egyetlen paranccsal is engedélyezhetők. A Virtuálisgép-példány, amely a virtuális készülék tűzfal és az IP-továbbítás engedélyezése hívható meg. Ne feledje, hogy a piros kezdődő nevű elemek `$`, pl. `$VMName[0]`, felhasználó által definiált változók a hivatkozási szakaszban Ez a dokumentum a parancsfájlból. A nulla zárójelben `[0]`, az első virtuális gép a virtuális tömb jelöli. A példaszkript módosítás nélkül működik a tűzfal az első virtuális gép (VM-0) kell lennie. A teljes szkript a megfelelő kódrészletet az UDR-parancsok végén van csoportosítva.

```powershell
Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
    Set-AzureIPForwarding -Enable
```

## <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

Ebben a példában egy hálózati biztonsági csoport (NSG) hozhat létre, és ezután töltse be egyetlen szabállyal. A példa ezután az NSG-KET csak köti az előtérbeli és háttérbeli alhálózatok (nem a SecNet). A szabály, akkor betöltheti az NSG-t a következőképpen történik:

* A rendszer megtagadja az összes bejövő forgalom (minden porthoz) az internetről a teljes virtuális hálózathoz (az összes alhálózat)

Bár ebben a példában az NSG-ket használja, a fő célja manuális kiszolgálóhiba elleni védelmet a másodlagos összetevővel. Minden bejövő forgalom az internetről az előtérbeli vagy a háttérbeli alhálózatokhoz letiltani kívánt. Érdemes csak adatforgalom a SecNet alhálózat a tűzfalon keresztül után, amely csak a megfelelő forgalmat kell irányítva az előtér- vagy a háttérbeli alhálózat be. Emellett az UDR-szabályok irányíthatja a forgalmat, amely eléri az előtérbeli vagy a háttérbeli alhálózatok a tűzfalhoz. A tűzfal látja, mint egy aszimmetrikus folyamatnak, és a kimenő forgalom csökken.

Biztonsági három réteg védelmét az előtérbeli és háttérbeli alhálózatok:

1. Nincsenek nyitott végpontok FrontEnd001 és BackEnd001 cloud Services
1. NSG-k megtagadása a forgalom az internetről
1. A tűzfal megállítja az aszimmetrikus forgalom

Ebben a példában az NSG kapcsolatos egy érdekes pont, hogy csak egy szabályt, az alább látható tartalmazza. Ez a szabály megtagadja a teljes virtuális hálózathoz, például a biztonság – alhálózati internetes forgalmat.

```powershell
Get-AzureNetworkSecurityGroup -Name $NSGName | `
    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
    from the internet" `
    -Type Inbound -Priority 100 -Action Deny `
    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
    -DestinationAddressPrefix VIRTUAL_NETWORK `
    -DestinationPortRange '*' `
    -Protocol *
```

Az NSG-t csak az előtérbeli és háttérbeli alhálózataihoz van kötve, mert a szabály nem alkalmazza a biztonsági alhálózatra bejövő forgalmat. Ennek eredményeképpen a forgalom a biztonsági alhálózathoz.

```powershell
Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $FESubnet -VirtualNetworkName $VNetName

Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $BESubnet -VirtualNetworkName $VNetName
```

## <a name="firewall-rules"></a>Tűzfalszabályok

Létre kell hoznia továbbítási szabályok a tűzfalon. Mivel a tűzfal letiltja, vagy az összes bejövő kimenő és virtual network intra forgalmat továbbítja, számos tűzfalszabályokat kell. Emellett a tűzfal rendelkezik minden bejövő forgalom biztonsági szolgáltatás nyilvános IP-címet (a másik portot képes) feldolgozni. Átdolgozási később elkerülése érdekében kövesse az ajánlott eljárás szerint diagramkészítési az alhálózatok és a tűzfalszabályok beállítása előtt a logikai folyamatokban. Az alábbi ábra az ebben a példában a tűzfalszabályok logikai nézetében:

![A tűzfalszabályok logikai nézetében][2]

> [!NOTE]
> Felügyeleti portokat a hálózati virtuális berendezés függenek. Ez a példa bemutatja a Barracuda NextGen tűzfal, amely 801, 807 és a 22-es portot használja. Tekintse meg a berendezés szállítói dokumentációban találja a pontos portokat az eszköz kezelésére.

### <a name="logical-rule-description"></a>Logikai szabály leírása

A fenti logikai ábrán nem jelenik meg a biztonsági alhálózat, mert a tűzfalat, hogy az egyetlen erőforrás az alhálózaton. Ez a diagram bemutatja a tűzfalszabályok hogyan azok logikailag engedélyezik vagy megtagadják a forgalom adatfolyamait, de nem a tényleges irányítva elérési útja. A külső portok, a távoli asztal protokoll (RDP) forgalmat kiválasztott is magasabb előre portok (8014 – 8026), az egyszerűbb olvashatóság érdekében, amelyek a helyi IP-címek az utolsó két oktetben összhangban vannak kiválasztva. Például helyi kiszolgáló címe 10.0.1.4 cím – Ez a külső portra 8014 társul. Használhatja azonban magasabb nem ütköző portokat.

Ebben a példában a következő típusú szabályok lesz szüksége:

* A bejövő forgalmat a külső szabályok:
  1. Felügyeleti tűzfalszabály: lehetővé teszi, hogy a forgalom számára a felügyeleti portokat a hálózati virtuális berendezéshez.
  2. Minden windows Server RDP-szabályok: lehetővé teszi, hogy a felügyeleti kiszolgálók az RDP-n keresztül.  A hálózati virtuális berendezéshez, függően előfordulhat szeretné a szabályok egy.
  3. Alkalmazás-forgalomra vonatkozó szabályokat: egy előtérbeli webes forgalmat, egy, a háttér-forgalmat (például web server adatréteg). Ezek a szabályok konfigurálása a hálózati architektúra és a forgalom függ folyamatokat.

     * Az első szabály lehetővé teszi, hogy az alkalmazás tényleges forgalom elérhesse a kiszolgáló. A szabályok a biztonsági, felügyeleti és így tovább, eltérően alkalmazás szabályok lehetővé teszik a külső felhasználók vagy a szolgáltatások eléréséhez (ok). Ebben a példában egy webkiszolgálón 80,-as porton, amely lehetővé teszi, hogy egyetlen alkalmazás tűzfalszabályt átirányítja a forgalmat, amelynek célja ehelyett a webalkalmazás-kiszolgáló belső IP-címének átirányítása egy külső IP-címet tartalmaz. Az átirányított forgalom munkamenet NAT által újramegfeleltetése alatt a belső kiszolgálónak.
     * A második alkalmazás forgalmi szabály a háttér-szabály lehetővé teszi a webkiszolgáló, bármely portra irányítja a forgalmat a AppVM01 kiszolgálóhoz, de nem a AppVM02 server használandó.

* A virtual network intra forgalmat belső szabályok:
  1. Kimenő internet-szabályhoz: lehetővé teszi, hogy a kiválasztott hálózatok átadása olyan hálózati forgalmát. Ez a szabály általában alapértelmezés szerint a tűzfalon, de letiltott állapotban. Ebben a példában a szabály engedélyezése.
  2. DNS-szabály: csak lehetővé teszi a DNS (53-as port) forgalom számára a DNS-kiszolgáló. Ebben a környezetben a legtöbb forgalmat az előtértől a háttérbe le van tiltva. Ez a szabály a bármilyen helyi alhálózatról DNS kifejezetten engedélyezi.
  3. Alhálózat-alhálózat szabály: lehetővé teszi, hogy minden kiszolgáló a háttérbeli alhálózat bármely az előtérbeli alhálózat, de nem a fordított a kiszolgálóhoz való csatlakozáshoz.

* Ez a szabály hibamentes a forgalmat, amely nem felel meg a fenti feltételek bármelyike:
  1. Megtagadó minden forgalmi szabály: mindig prioritást tekintetében végső szabályát. Forgalom nem egyezik meg a fenti szabály, ha ez a szabály letiltja azt. Alapértelmezett szabály. Mert általában aktív, módosítás nélkül van szükség.

> [!TIP]
> Az alkalmazás második forgalmi szabály a bármely porton engedélyezve van, így az ebben a példában egyszerű. A valós forgatókönyvekben használjon az adott port és címtartomány a szabály a támadási felület csökkentése érdekében.


> [!IMPORTANT]
> Miután létrehozta a szabályokat, fontos, hogy tekintse át, győződjön meg arról, hogy engedélyezett vagy tiltott forgalmat igény szerint minden egyes szabály prioritását. Ebben a példában a szabályok prioritási sorrendben vannak. Ha a szabály rosszul rendezett zárja ki magát a a tűzfal könnyebbé vált a. Ellenőrizze, hogy a felügyeleti tűzfalszabály állítja be a abszolút legmagasabb prioritású.

### <a name="rule-prerequisites"></a>A szabály Előfeltételek

Nyilvános végpontok szükség a virtuális gép fut, a tűzfal. A nyilvános végpontok nyitva kell lennie, hogy a tűzfal forgalom tud feldolgozni. A forgalom ebben a példában három típusa van:

1. Felügyeleti forgalom szabályozására, a tűzfal és tűzfalszabályok
1. RDP-forgalom szabályozására, a windows-kiszolgálók
1. Alkalmazás-forgalom

Az adatforgalom típusainak tűzfal szabályok fenti logikai ábrán felső részén jelennek meg.

> [!IMPORTANT]
> Ne feledje, hogy *összes* adatforgalom a tűzfalon keresztül érhető el. Távoli asztal a IIS01 kiszolgálóhoz szüksége 8014 porton a tűzfal csatlakozhat, és ezután engedélyezi az RDP-kérelem belső irányíthatja a IIS01 RDP-portra a tűzfalon. Az Azure Portalon **Connect** gomb nem fog működni, mert nincs IIS01 való közvetlen RDP útvonal, amennyire a portálon látható. Az összes az internetről érkező kapcsolatokat is a szolgáltatás és a egy portot (például secscv001.cloudapp.net:xxxx). A fenti ábra külső port és a belső IP-cím és port, a hozzárendelés hivatkozhat.

Egy végpontot a virtuális gép létrehozásakor, illetve a build után megnyithatja. A példaszkript és a következő kódrészletet a virtuális gép létrehozása után nyissa meg a végpont.

Ne feledje, hogy az elemek, amelyek kezdődnek `$`, például `$VMName[$i]`, felhasználó által definiált változók a hivatkozás a szakaszban a parancsfájlból. A `[$i]` egy adott virtuális Gépet a virtuális tömb tömb számát jelenti.

```powershell
Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
    Update-AzureVM
```

Azt nem jól látható itt változók miatt, bár csak nyissa meg a biztonsági felhőszolgáltatás-végpontok. Az eszközeikről biztosíthatja, hogy a tűzfal az összes bejövő forgalmat kezeli, e irányítva, újramegfeleltetése alatt a NAT által, vagy hiánytalanul eldobva.

A felügyeleti ügyfél telepítése számítógépeken a tűzfal kezelése és a szükséges konfigurációk létrehozásához. Megtudhatja, hogyan kezelheti a tűzfal vagy más nva-t tekintse meg a gyártói dokumentációt. Ez a szakasz további része, valamint a **tűzfalszabályok létrehozása** a tűzfal konfigurálását ismertetik. A szállító felügyeleti ügyfél, nem az Azure Portalon vagy a Powershellt használja.

Lépjen a [Barracuda NG rendszergazdai](https://techlib.barracuda.com/NG61/NGAdmin) a management-ügyfél letöltéséhez, és ismerje meg, hogyan csatlakozhat a Barracuda tűzfal.

Miután jelentkezett be a tűzfal, adja meg a hálózat- és szolgáltatási objektumok a tűzfalszabályok létrehozása előtt. E két előfeltétel objektumosztályok teheti a szabályok könnyebb létrehozása.

Ebben a példában adja meg az egyes három nevű hálózati objektumok:

* Előtér-alhálózat
* Háttérbeli alhálózatot
* IP address of the DNS server

A Barracuda NG felügyeleti ügyfél irányítópult egy névvel ellátott hálózat létrehozása:

1. Nyissa meg a **konfiguráció lapon**.
1. Válassza ki **szabálykészletben** a a **működési konfigurációs** szakasz
1. Válassza ki **hálózatok** alatt a **Tűzfalobjektumokat** menü.
1. Válassza ki **új** a a **szerkesztése hálózatok** menü.
1. A hálózati objektum létrehozásához adja meg a nevét és az előtag:

   ![Hozzon létre egy előtér-ed hálózati objektumot][3]

A fenti lépéseket az előtér-alhálózat nevű hálózat létrehozása. Hozzon létre egy hasonló, valamint a háttérbeli alhálózat objektumot. Most már az alhálózatok egyszerűbben hivatkozhat neve az a tűzfalszabályokat.

A DNS-kiszolgáló objektum:

![Hozzon létre egy DNS-kiszolgáló objektumot][4]

A egyetlen IP-cím hivatkozás szerepel a dokumentum későbbi szakaszában egy DNS-szabály.

A többi objektum osztálya magában foglalja a szolgáltatások objektumokat, amely képviseli az RDP csatlakozási porttal kiszolgálónként. A meglévő RDP service objektum 3389-es van kötve az alapértelmezett RDP-portra. Így hozhat létre új szolgáltatás a külső portok (8014-8026) érkező adatforgalom engedélyezéséhez. Az új portok a meglévő RDP-szolgáltatásban is megteheti. Azonban bemutató megkönnyítése érdekében, hogy az egyes szabályok minden olyan kiszolgáló esetén. A Barracuda NG felügyeleti ügyfél irányítópult kiszolgáló új RDP szabály létrehozása:

1. Nyissa meg a **konfiguráció lapon**.
1. Válassza ki **szabálykészletben** a a **működési konfigurációs** szakaszban.
1. Válassza ki **szolgáltatások** alatt a **Tűzfalobjektumokat** menü.
1. Görgessen lefelé a szolgáltatások, és válassza ki a listában **RDP**.
1. Kattintson a jobb gombbal válassza ki a példányt, kattintson a jobb gombbal és válassza a beillesztés.
1. Most már rendelkezésre áll egy RDP-Copy1 service objektum, amely szerkeszthető. Kattintson a jobb gombbal **RDP-Copy1** válassza **szerkesztése**.
1. A **szerkesztése a Service objektum** ablak kerül, ahol akár itt látható módon:

   ![RDP-szabályban alapértelmezett példánya][5]

1. Módosítsa az értékeket, amelyek az RDP-szolgáltatás egy adott kiszolgálóhoz. A AppVM01, az alapértelmezett RDP-szabályban módosítani kell, hogy egy új szolgáltatás **neve**, **leírás**, és a 8. ábra diagramon használt külső RDP-portra. Ne feledje, hogy a portok a megadott kiszolgáló a külső portra a 3389-es RDP alapértelmezett változnak. Például a külső portra vonatkozó AppVM01 8025. A módosított szolgáltatás a szabály itt jelenik meg:

   ![AppVM01 Rule][6]

Ismételje meg a folyamatot, a többi kiszolgálón RDP-szolgáltatások létrehozásához: AppVM02, DNS01, and IIS01. Ezek a szolgáltatások egyszerűbben hozhat létre és viszonylag kézenfekvő a következő szakaszban győződjön meg a szabályokat.

> [!NOTE]
> Egy RDP-szolgáltatás, a tűzfal nincs szükség, mert a virtuális gép tűzfal egy linuxos rendszerképet, így SSH 22-es port szolgál a virtuális gép kezelése helyett az RDP. Emellett a 22-es port, és két port felügyeleti kapcsolat engedélyezett. Tekintse meg a **felügyeleti tűzfalszabály** a következő szakaszban.

### <a name="firewall-rules-creation"></a>Tűzfal-szabályok létrehozása

Ebben a példában az összes különböző ikonokkal tűzfalszabályok három típusa van:

Az alkalmazás átirányítási szabály: ![Application Redirect Icon][7]

A cél NAT-szabály: ![Cél NAT ikon][8]

A pass-szabály: ![Pass ikon][9]

További információ a szabályokról a Barracuda webhelyén található.

Hozza létre a következő szabályokat, vagy ellenőrizze a meglévő alapértelmezett szabályok:

1. A Barracuda NG rendszergazdai ügyfél irányítópultján, nyissa meg a **konfigurációs** fülre.
1. Az a **működési konfigurációs** szakaszban jelölje be **szabálykészletben**.
1. A **fő szabályok** rács megjelenítése a meglévő active és inaktiválása szabályok a tűzfalon. Válassza ki a zöld **+** hozzon létre egy új szabályt, a jobb felső sarkában. Ha a tűzfal zárolva van, a módosításokat, látja-e megjelölve gomb **zárolási** , és nem hozható létre vagy szerkessze a szabályok. Válassza ki a **zárolási** a szabálykészlet feltárását, és a Szerkesztés engedélyezése gombra. Kattintson a jobb gombbal egy szabály szerkesztése, és válassza ki a kívánt **szabály szerkesztése**.

Miután létrehozott vagy módosított összes szabály, azok leküldése a tűzfal, és aktiválhatja ezeket. Ellenkező esetben a szabály változások lépnek érvénybe. A leküldéses és az aktiválási folyamat nem ismertetett [aktiválási szabály](#rule-activation).

Az alábbiakban az egyes szabályok ebben a példában a végrehajtásához tulajdonságairól:

* **Felügyeleti tűzfalszabály**: Az alkalmazás átirányítási szabály lehetővé teszi, hogy a forgalom számára a felügyeleti portokat a hálózati virtuális berendezéshez, ebben a példában a Barracuda NextGen tűzfal. A felügyeleti portoknak 801 807 és igény szerint 22-es. A belső és külső portok megegyeznek, nincs port fordítási. Ez a szabály neve hozzáférés-MGMT-beállítása. Alapértelmezett szabály, és alapértelmezés szerint engedélyezett a Barracuda NextGen tűzfal, 6.1-es verzió.
  
    ![Tűzfalszabályok kezelése][10]

  > [!TIP]
  > Ez a szabály a forrás-címtér van **bármely**. A felügyeleti IP-címtartományok ismertek, ha ebben a hatókörben csökkentve a támadási felületet a felügyeleti portokhoz is csökkenti.

* **RDP-szabályok**:  E cél NAT-szabályok lehetővé teszik az RDP-n keresztül az egyes kiszolgálók felügyeletét. A kritikus fontosságú mezők esetében ezek a szabályok a következők:
  * A forrás. Ahhoz, hogy RDP bárhonnan, használja a rendszer **bármely** a forrás mezőben.
  * A szolgáltatás. Az RDP-szolgáltatás korábban létrehozott objektum használja: **AppVM01 RDP**. A külső portok a kiszolgáló helyi IP-címet, és az alapértelmezett RDP-portra 3386 irányítsa át. Ez a szabály az RDP-hozzáférésének AppVM01 szól.
  * Cél. Használja a helyi portra a tűzfalon: **DCHP 1 helyi IP-cím** vagy **eth0** statikus IP-címek használata. Lehet, hogy a megfelelő sorszámmal (eth0 eth1 és így tovább) eltérő, ha a hálózati berendezéshez több helyi adapterrel rendelkezik. A tűzfal küld el ezt a portot használja, és a fogadó port azonos lehet. A tényleges irányított célhely a **cél lista** mező.
  * Átirányítást. Konfigurálja, hogy a virtuális készülék helyét végső soron átirányítja a forgalmat. A legegyszerűbb átirányítás, hogy a IP-cím jelölje be a cél listában. Azt is megadhatja a portot, és a NAT reroutes a portot és az IP-címet is. Ha nem ad meg egy portot, a virtuális készülék célport használja a bejövő kérelem.

    ![RDP-tűzfalszabály][11]

    Hozzon létre négy RDP-szabályok:

    | Szabály neve | Kiszolgáló | Szolgáltatás | Cél lista |
    | --- | --- | --- | --- |
    | RDP-to-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
    | RDP-to-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
    | RDP-to-AppVM01 |AppVM01 |AppVM01 RDP |10.0.2.5:3389 |
    | RDP-to-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

  > [!TIP]
  > A forrás- és szolgáltatás mezők körét szűkítheti csökkentve a támadási felületet. A legtöbb korlátozott hatókör, amely lehetővé teszi a funkció használatához.

* **Alkalmazás-forgalomra vonatkozó szabályokat**: Két alkalmazás forgalmi szabályok vannak. Egyik az előtérbeli webes forgalom számára. Hasonlóan az adatréteg, és a háttér-forgalom a másik ismerteti. Ezek a szabályok adatforgalom folyamatokat, és a hálózati architektúra függenek.
  
  * A webes előtér-szabály:
  
    ![Firewall Web Rule][12]
  
    A cél NAT-szabály lehetővé teszi, hogy az alkalmazás tényleges forgalom elérhesse a kiszolgáló. Eltérően a szabályok a biztonsági, felügyeleti és etcetera alkalmazás-szabályok lehetővé teszik a külső felhasználók vagy a szolgáltatások eléréséhez (ok). Ebben a példában egy webkiszolgálón 80,-as porton, amely lehetővé teszi, hogy egyetlen alkalmazás tűzfalszabályt átirányítja a forgalmat, amelynek célja ehelyett a webalkalmazás-kiszolgáló belső IP-címének átirányítása egy külső IP-címet tartalmaz. Az átirányított forgalom munkamenet NAT által újramegfeleltetése alatt a belső kiszolgálónak.

    > [!NOTE]
    > Nincs hozzárendelve az port a **cél lista** mező. A bejövő port a 80-as (vagy a kiválasztott szolgáltatás a 443-as), az átirányítás, a webkiszolgáló használatos. A webalkalmazás-kiszolgáló egy másik, például a 8080-as portot figyeli, a cél lista mező 10.0.1.4:8080, hogy az a port átirányítás, valamint a segítségével frissítheti.
  
  * A háttér-szabály engedélyezi a AppVM01 kiszolgálón, de nem AppVM02 keresztül kommunikáljon a webkiszolgáló **bármely** szolgáltatás:
  
    ![Firewall AppVM01 Rule][13]
  
    A pass-szabály lehetővé teszi, hogy a bármely az IIS-kiszolgálón az előtér-alhálózat AppVM01 elérni (10.0.2.5) bármely porton bármely protokoll használatával, hogy az adatok is hozzáférhet a webalkalmazáshoz.
  
    Az ezen a képernyőfelvételen látható `<explicit-dest>` használatban van a **cél** mező céljaként 10.0.2.5 jelölésére. Az IP-cím explicit módon a képernyőképen látható módon is megadhat. Az előfeltételeket a DNS-kiszolgáló is használhatja például egy névvel ellátott hálózati objektumra. A tűzfal-rendszergazdák kiválaszthatják, hogy melyik módszert használja. Explicit célként 10.0.2.5 hozzáadásához kattintson duplán az első üres sor alatt `<explicit-dest>` és a megnyíló párbeszédpanelen írja be a címet.
  
    A pass-szabály, és nem NAT van szükség, mert belső forgalmat kezeli. Beállíthatja a **kapcsolódási módszert** való `No SNAT`.
  
    > [!NOTE]
    > A szabály a Forráshálózat az előtér-alhálózat bármely erőforrás esetén csak egy. Ha az architektúra egy ismert webkiszolgálók számát határozza meg, létrehozhat egy részletesebb, ezek helyett a teljes előtér-alhálózat pontos IP-címek hálózati objektum-erőforrásaikat.

    > [!TIP]
    > Ez a szabály a szolgáltatás használ **bármely** a mintaalkalmazás könnyebben beállítania és használnia. Ez lehetővé teszi, hogy a ICMPv4 (ping), egy egyetlen szabályban. Javasoljuk azonban, csökkentheti a támadási felület között ezt a határt, a portok és protokollok szolgáltatások korlátozása a lehető legkisebb lehetséges, amelyek lehetővé teszik az alkalmazás műveletet.

    > [!TIP]
    > Bár ez a példa szabály `<explicit-dest>` hivatkozást, a tűzfal konfigurálása során minden egy egységes megközelítést kell használnia. Azt javasoljuk, hogy egy nevesített objektum használata a kereslettől és a könnyebb olvashatóság érdekében. A `<explicit-dest>` látható itt van egy alternatív módszer csak megjeleníthető. Nem általánosan ajánlott, különösen a összetett konfigurációkkal.

* **Kimenő internet szabály**: A pass-szabály lehetővé teszi, hogy a kiválasztott cél hálózatok átadása bármely forrás hálózati forgalmát. A Barracuda NextGen tűzfal általában van ez a szabály "on" alapértelmezés szerint, de letiltott állapotban. Ez a szabály eléréséhez kattintson a jobb gombbal a **aktiválása szabály** parancsot. Módosítsa a szabály a hálózati objektumok a háttér- és előtér-alhálózat hozzáadása a forrásattribútum, ez a szabály a képernyőképen látható. Ez a cikk Előfeltételek szakaszában hozott létre ezeket a hálózati objektumok.
  
    ![Kimenő tűzfalszabály][14]

* **DNS-szabály**: A pass-szabály lehetővé teszi, hogy csak (53-as port) a DNS-forgalom számára a DNS-kiszolgáló. Ebben a környezetben a legtöbb forgalmat az előtértől a háttérbe, ez a szabály kifejezetten engedélyezi a DNS-forgalom blokkolva van.
  
    ![Firewall DNS Rule][15]
  
    > [!NOTE]
    > A **kapcsolódási módszert** értékre van állítva `No SNAT` , mert ez a szabály belső IP-cím forgalmat a belső IP-címhez, és nem NAT-n keresztül átirányítási nem szükséges.

* **Alhálózat-alhálózat szabály**: Ez az alapértelmezett hozzáférési szabály aktiválva, és a módosított az összes olyan kiszolgálónak engedélyezi a háttérbeli alhálózat az előtér-alhálózat bármely kiszolgálóhoz való csatlakozáshoz. Ez a szabály csak a belső forgalom coves tehát a **kapcsolódási módszert** állítható `No SNAT`.

    ![Belüli virtuális hálózatok közötti tűzfalszabály][16]
  
    > [!NOTE]
    > A **kétirányú** jelölőnégyzet nincs bejelölve itt, ezért csak egy irányban Ez a szabály vonatkozik. A kezdeményezett kapcsolat a háttérbeli alhálózat felől az előtér-hálózati, de nem a fordított. Ha a jelölőnégyzet be lett kiválasztva, ez a szabály lehetővé tenné kétirányú forgalmat, amely azt a logikai ábrán nemkívánatos, megadása.

* **Minden forgalmi szabály megtagadja**: Ez a szabály mindig kell lennie a végső szabály prioritása tekintetében. Ha a forgalom áramlását nem egyezik a fenti szabály, ez a szabály miatt a lehet elvetni. A szabály alapértelmezés szerint gyakran akkor aktiválódik, így módosítás nélkül van szükség.
  
    ![Tűzfal megtagadó szabály][17]

> [!IMPORTANT]
> Az előző szabályok létrehozása után tekintse át az egyes szabályt, hogy a forgalom engedélyezett vagy tiltott igény szerint prioritását. Ebben a példában a szabály szerepel a listában a sorrendben szerepelniük kell a Barracuda felügyeleti ügyfél fő rácshoz, a szabályzatok továbbításához.

## <a name="rule-activation"></a>A szabály az aktiválás

Miután módosította a szabálykészletet, a logikai diagramja előírásoknak megfelelnek, kell töltse fel a szabálykészletet a tűzfalat, és aktiválja.

![Tűzfal szabály aktiválás][18]

Keresse meg a felügyeleti ügyfél ablakának jobb felső sarokban, és válassza ki **módosítások küldése** tölthet fel a módosított szabályok a tűzfalon. Válassza ki **aktiválása**.

Aktiválja a tűzfal szabálykészlet, amikor ez a példa a környezet számára befejeződött.

## <a name="traffic-scenarios"></a>Forgalom forgatókönyvek

> [!IMPORTANT]
> Ne feledje, hogy *összes* adatforgalom a tűzfalon keresztül érhető el. Távoli asztal a IIS01 kiszolgálóhoz szüksége 8014 porton a tűzfal csatlakozhat, és ezután engedélyezi az RDP-kérelem belső irányíthatja a IIS01 RDP-portra a tűzfalon. Az Azure Portalon **Connect** gomb nem fog működni, mert nincs IIS01 való közvetlen RDP útvonal, amennyire a portálon látható. Az összes az internetről érkező kapcsolatokat is a szolgáltatás és a egy portot (például secscv001.cloudapp.net:xxxx). A fenti ábra külső port és a belső IP-cím és port, a hozzárendelés hivatkozhat.

Ebben az esetben a következő tűzfalszabályokat helyén kell lennie:

1. Tűzfalkezelés (Keretrendszer Mgmt)
2. IIS01 RDP-vel
3. DNS01 RDP-vel
4. AppVM01 RDP-vel
5. RDP to AppVM02
6. A Web App-forgalom
7. App-forgalom AppVM01
8. Kimenő Internet
9. A DNS01 előtér
10. Belüli alhálózatok közötti forgalmat (csak előtér, háttér)
11. Összes elutasítása

A tényleges tűzfal sadu pravidel nagy valószínűséggel lesz szükség, mint ebben a példában további szabályok. Ezek már valószínűleg különböző prioritású száma is. A lista és a hozzájuk társított számokat egymáshoz a relatív prioritását az hivatkozzon. Például az "RDP IIS01" szabály előfordulhat, hogy számot 5 a tényleges tűzfalon, de azt az alábbi "tűzfal felügyelet" lehető leghosszabbak szabály és fent az "RDP való DNS01" szabály, a készlet igazítja platformösszetevő ebben a listában. Ez a lista is egyszerűbbé teszi a forgatókönyvekhez, kövesse az utasításokat. Például "tűzfalszabályt 9 (DNS)." Vegye figyelembe, hogy az RDP négyet együttesen neve "az RDP-szabályok" Ha a forgalmat a forgatókönyv nem kapcsolódik az RDP.

Idézze is, hogy a hálózati biztonsági csoportok (NSG-k) vannak érvényben a bejövő internetes forgalmat az előtérbeli és háttérbeli alhálózatok.

### <a name="allowed-internet-to-web-server"></a>(Allowed) Internet to web server

1. Az internetes felhasználó kér (az internet felé néző felhőszolgáltatás) SecSvc001.CloudApp.Net HTTP lap.
1. A felhőszolgáltatás forgalom nyissa meg a végpont keresztül továbbítja a tűzfal megfelelő felületéről 10.0.0.4:80 a 80-as porton.
1. Nincs hálózati biztonsági csoport biztonsági alhálózat van hozzárendelve, így az NSG-szabályok a forgalmat a tűzfal engedélyezése.
1. A forgalom eléri a belső IP-cím a tűzfal (10.0.1.4 cím – ez).
1. A tűzfal szabály feldolgozása hajtja végre:
   1. Tűzfalszabály 1 (Keretrendszer Mgmt) nem vonatkozik. Ugrás a következő szabály.
   1. 2 – 5 (RDP-szabályok) tűzfal-szabályok nem érvényesek. Ugrás a következő szabály.
   1. Tűzfalszabály 6 (alkalmazás: Webes) a alkalmazni. Engedélyezi a forgalmat. Tűzfal reroutes 10.0.1.4 cím – ez (IIS01) NAT-n keresztül a forgalom.
1. Az előtérbeli alhálózat bejövő szabály feldolgozása hajtja végre:
   1. NSG-szabály 1 (blokk internet) nem vonatkozik. A tűzfal átirányítva a forgalmat, NAT-n keresztül, így a forrás-cím már a tűzfalon. A tűzfal biztonsági az alhálózaton, és így jelenik meg helyi az előtérbeli alhálózat NSG-forgalom, mivel engedélyezi a forgalmat. Ugrás a következő szabály.
   1. Alapértelmezett NSG-szabályok engedélyezik a alhálózati alhálózatok közötti forgalmat, így a forgalom engedélyezve van. Állítsa le az NSG-szabály feldolgozása.
1. A webes forgalom IIS01 figyeli. Ezt a kérelmet kap, és elindítja a kérelem feldolgozásakor.
1. IIS01 próbál egy FTP-munkamenet AppVM01, a háttérbeli alhálózat kezdeményezni.
1. Az UDR-útvonal az előtérbeli alhálózat lehetővé teszi a tűzfala a következő ugrás.
1. Nincsenek kimenő szabályok előtérbeli alhálózatán, engedélyezi a forgalmat.
1. Tűzfal szabály feldolgozása kezdődik:
   1. Tűzfalszabály 1 (Keretrendszer Mgmt) nem vonatkozik. Ugrás a következő szabály.
   1. 2 – 5 (RDP-szabályok) tűzfal-szabályok nem érvényesek. Ugrás a következő szabály.
   1. Tűzfalszabály 6 (alkalmazás: Webes) nem vonatkozik. Ugrás a következő szabály.
   1. Tűzfalszabály 7 (alkalmazás: háttérrendszer) a alkalmazni. Engedélyezi a forgalmat. A tűzfal továbbítja a forgalmat a 10.0.2.5 (AppVM01).
1. A háttérbeli alhálózat bejövő szabály feldolgozása hajtja végre:
    1. NSG-szabály 1 (blokk internet) nem vonatkozik. Ugrás a következő szabály.
    1. Alapértelmezett NSG-szabályok lehetővé teszik az alhálózat alhálózatok közötti forgalmat. Engedélyezi a forgalmat. Állítsa le az NSG-szabály feldolgozása.
1. AppVM01 a kérelmet kap, a munkamenet indítása és válaszol.
1. Az UDR-útvonal, a háttérbeli alhálózat lehetővé teszi a tűzfala a következő ugrás.
1. Nincsenek nem kimenő NSG-szabályok a háttérbeli alhálózat, a válasz használata engedélyezett.
1. Forgalom munkamenetet létesítenek a adja vissza, mert a tűzfal adja meg a választ a webkiszolgáló (IIS01).
1. Előtérbeli alhálózat bejövő szabály feldolgozása hajtja végre:
    1. NSG-szabály 1 (blokk internet) nem vonatkozik. Ugrás a következő szabály.
    1. Az alapértelmezett NSG-szabályok engedélyezik a alhálózati alhálózatok közötti forgalmat, így a forgalom engedélyezve van. Állítsa le az NSG-szabály feldolgozása.
1. Az IIS-kiszolgálón a válasz fogadása és a tranzakció AppVM01. A kiszolgáló majd a HTTP-válasz létrehozása befejeződik, és elküldi azokat a kérelmezőnek.
1. Nincsenek nem kimenő NSG-szabályok az előtér-alhálózat, a válasz használata engedélyezett.
1. A HTTP-válasz eléri a tűzfalon. Mivel a válasz a NAT munkamenetet létesítenek, a tűzfal elfogadja azt.
1. A tűzfal átirányítja az internetes felhasználói a választ.
1. Nincsenek kimenő NSG-szabályok vagy UDR ugrások az előtér-alhálózat, a válasz használata engedélyezett. Az internetes felhasználó kapja a kért weblap.

### <a name="allowed-internet-rdp-to-back-end"></a>(Engedélyezett) Internetes RDP-háttéralkalmazáshoz

1. A server admin on the internet requests an RDP session to AppVM01 via SecSvc001.CloudApp.Net:8025. 8025 a felhasználó által hozzárendelt portszámot a tűzfalszabály 4 (RDP AppVM01).
1. A felhőszolgáltatás továbbítja a nyitott végpontok forgalmát a porton 8025 10.0.0.4:8025 a tűzfal megfelelő felületéről.
1. Nincs hálózati biztonsági csoport biztonsági alhálózathoz van hozzárendelve, így a rendszer az NSG-szabályok lehetővé teszik a tűzfal-forgalom.
1. A tűzfal szabály feldolgozása hajtja végre:
   1. Tűzfalszabály 1 (Keretrendszer Mgmt) nem vonatkozik. Ugrás a következő szabály.
   1. Tűzfalszabály 2 (RDP IIS) nem vonatkozik. Ugrás a következő szabály.
   1. Tűzfalszabály 3 (RDP DNS01) nem vonatkozik. Ugrás a következő szabály.
   1. Tűzfalszabály 4 (RDP AppVM01) vonatkozik, így a forgalom engedélyezve van. A tűzfal reroutes, NAT-n keresztül történő 10.0.2.5:3386 (RDP-port a AppVM01).
1. A háttérbeli alhálózat bejövő szabály feldolgozása hajtja végre:
   1. NSG-szabály 1 (blokk internet) nem vonatkozik. A tűzfal átirányítva a forgalmat, NAT-n keresztül, így a forrás-cím már a tűzfal, a biztonsági alhálózaton található. Ez a háttérbeli alhálózat NSG-forgalom helyi látható, és engedélyezve van. Ugrás a következő szabály.
   1. Alapértelmezett NSG-szabályok engedélyezik a alhálózati alhálózatok közötti forgalmat, így a forgalom engedélyezve van. Állítsa le az NSG-szabály feldolgozása.
1. AppVM01 a RDP-forgalmat figyeli és reagál.
1. Így az alapértelmezett szabályok a alkalmazni, nincsenek NSG kimenő szabályok. Engedélyezett a visszatérő forgalmat.
1. Udr-t a következő ugrás kimenő forgalmat irányítja a tűzfalon.
1. Forgalom munkamenetet létesítenek a adja vissza, mert a tűzfal adja meg a választ az internetes felhasználói.
1. RDP-munkamenet engedélyezve van.
1. Felhasználói név jelszó AppVM01 kéri.

### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Engedélyezett) Web server a DNS-címkeresés DNS-kiszolgálón

1. Webkiszolgáló IIS01 egy adatcsatornát, http-kérelmek\:\/\/www.data.gov, de igények címének feloldására.
1. A hálózati konfigurációt a virtuális hálózat listák DNS01 (a háttérbeli alhálózat 10.0.2.4 cím), elsődleges DNS-kiszolgálót. IIS01 DNS01 a DNS-kérelmet küld.
1. Udr-t a következő ugrás kimenő forgalmat irányítja a tűzfalon.
1. Nincs kimenő NSG-szabályok az előtérbeli alhálózat felé vannak kötve. Engedélyezi a forgalmat.
1. Tűzfal szabály feldolgozása hajtja végre:
   1. Tűzfalszabály 1 (Keretrendszer Mgmt) nem vonatkozik. Ugrás a következő szabály.
   1. Tűzfalszabály 2 – 5 (RDP-szabályok) nem vonatkoznak. Ugrás a következő szabály.
   1. 6 és 7 (Alkalmazásszabályok) tűzfal-szabályok nem érvényesek. Ugrás a következő szabály.
   1. Tűzfalszabály 8 (internet) nem vonatkozik. Ugrás a következő szabály.
   1. Tűzfalszabály 9 (DNS) a alkalmazni. Engedélyezi a forgalmat. Tűzfal továbbítja a forgalmat a 10.0.2.4 cím (DNS01).
1. A háttérbeli alhálózat bejövő szabály feldolgozása hajtja végre:
   1. NSG-szabály 1 (blokk internet) nem vonatkozik. Ugrás a következő szabály.
   1. Alapértelmezett NSG-szabályok lehetővé teszik az alhálózat alhálózatok közötti forgalmat. Engedélyezi a forgalmat. Állítsa le az NSG-szabály feldolgozása.
1. A DNS-kiszolgáló fogadja a kérelmet.
1. A DNS-kiszolgáló nem rendelkezik a gyorsítótárazott címet, és gyökérszintű DNS-kiszolgáló kéri az interneten.
1. Udr-t a tűzfalon a kimenő forgalmat a következő ugrás irányítja.
1. Nincsenek nem kimenő NSG-szabályok a forgalmat a háttérbeli alhálózat használata engedélyezett.
1. Tűzfal szabály feldolgozása hajtja végre:
   1. Tűzfalszabály 1 (Keretrendszer Mgmt) nem vonatkozik. Ugrás a következő szabály.
   1. Tűzfalszabály 2 – 5 (RDP-szabályok) nem vonatkoznak. Ugrás a következő szabály.
   1. 6 és 7 (Alkalmazásszabályok) tűzfal-szabályok nem érvényesek. Ugrás a következő szabály.
   1. Tűzfalszabály 8 (internetes) a alkalmazni. Engedélyezi a forgalmat. A munkamenet van protokollon keresztül átirányítja az SNAT gyökér DNS-kiszolgálóra az interneten.
1. Az internetes DNS-kiszolgáló azt válaszolja. Ez a munkamenet kezdeményezték a tűzfal, a válasz elfogadta a tűzfalon.
1. Ez a munkamenet már létrejött, a tűzfal továbbítja a kezdeményező kiszolgáló DNS01 adott válasz.
1. A háttérbeli alhálózat bejövő szabály feldolgozása hajtja végre:
    1. NSG-szabály 1 (blokk internet) nem vonatkozik. Ugrás a következő szabály.
    1. Alapértelmezett NSG-szabályok lehetővé teszik a forgalmat az alhálózat alhálózatok közötti forgalom engedélyezve van. Állítsa le az NSG-szabály feldolgozása.
1. A DNS-kiszolgáló fogadja és gyorsítótárazza a választ, és a kezdeti kérelmet vissza a IIS01 válaszol.
1. Az UDR-útvonal, a háttérbeli alhálózat lehetővé teszi a tűzfala a következő ugrás.
1. Nincs kimenő NSG-szabályok a háttérbeli alhálózat létezik, így engedélyezi a forgalmat.
1. Ez a munkamenet már létrejött a tűzfalon, a tűzfal reroutes a választ az IIS-kiszolgálón.
1. Az előtérbeli alhálózat bejövő szabály feldolgozása hajtja végre:
    1. A bejövő forgalmat a háttérbeli alhálózat felől az előtérbeli alhálózat nincs NSG-szabály létezik nem így egyetlen sem az NSG-szabályokat a alkalmazni.
    1. Az alapértelmezett rendszerszintű szabály lehetővé teszi, hogy a forgalmat az alhálózatok között. Engedélyezi a forgalmat.
1. IIS01 DNS01 a választ kap.

### <a name="allowed-back-end-server-to-front-end-server"></a>(Engedélyezett) Háttér-kiszolgálót előtér-kiszolgáló

1. Egy rendszergazda bejelentkezett AppVM02 RDP-Kapcsolaton keresztül közvetlenül a kiszolgálóról IIS01 keresztül a windows Fájlkezelőben olyan fájlt kér.
1. Az UDR-útvonal, a háttérbeli alhálózat lehetővé teszi a tűzfala a következő ugrás.
1. Nincsenek nem kimenő NSG-szabályok a háttérbeli alhálózat, a válasz használata engedélyezett.
1. A tűzfal szabály feldolgozása hajtja végre:
   1. Tűzfalszabály 1 (Keretrendszer Mgmt) nem vonatkozik. Ugrás a következő szabály.
   1. Tűzfalszabály 2 – 5 (RDP-szabályok) nem vonatkoznak. Ugrás a következő szabály.
   1. 6 és 7 (alkalmazásszabályok) tűzfal-szabályok nem érvényesek. Ugrás a következő szabály.
   1. Tűzfalszabály 8 (internet) nem vonatkozik. Ugrás a következő szabály.
   1. Tűzfalszabály 9 (DNS) nem vonatkozik. Ugrás a következő szabály.
   1. Tűzfalszabály 10 (Intra-alhálózat) a alkalmazni. Engedélyezi a forgalmat. A tűzfal forgalom 10.0.1.4 cím – ez (IIS01) továbbítja.
1. Az előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
   1. NSG-t 1. szabály (blokk internet) nem vonatkoznak, helyezze át a következő szabály
   1. Az alapértelmezett NSG-szabályok engedélyezik a alhálózati alhálózatok közötti forgalmat, így a forgalom engedélyezve van. Állítsa le az NSG-szabály feldolgozása.
1. Ha a megfelelő hitelesítési és engedélyezési, IIS01 fogadja a kéréseket, és válaszol.
1. Az UDR-útvonal az előtér-alhálózat lehetővé teszi a tűzfala a következő ugrás.
1. Nincsenek nem kimenő NSG-szabályok az előtér-alhálózat, a válasz használata engedélyezett.
1. A munkamenet már létezik a tűzfalon, így ez a válasz használata engedélyezett. A tűzfal AppVM02 a választ adja vissza.
1. A háttérbeli alhálózat bejövő szabály feldolgozása hajtja végre:
    1. NSG-szabály 1 (blokk internet) nem vonatkozik. Ugrás a következő szabály.
    2. Az alapértelmezett NSG-szabályok engedélyezik a alhálózati alhálózatok közötti forgalmat, így a forgalom engedélyezve van. Állítsa le az NSG-szabály feldolgozása.
1. AppVM02 megkapja a választ.

### <a name="denied-internet-direct-to-web-server"></a>(Tiltott) A webalkalmazás-kiszolgáló közvetlen Internet

1. An internet user tries to access the IIS01 web server through the FrontEnd001.CloudApp.Net service.
1. Nincsenek végpontok nyissa meg a HTTP-forgalom, a felhőszolgáltatás nem továbbítja a forgalmat. A forgalom nem éri el a kiszolgálót.
1. Ha valamilyen okból a végpontok nyitva, az NSG-t (internet letiltása) az előtér-alhálózat blokkolja a forgalmat.
1. Végül az előtérbeli alhálózat UDR útvonal küld minden kimenő forgalmat a IIS01 a tűzfalon a következő ugrás. A tűzfal látja, aszimmetrikus adatforgalom, és a kimenő válasz csökken.

>Ebből kifolyólag nincsenek az internetes és IIS01 között legalább három független rétege. A felhőszolgáltatás megakadályozza a jogosulatlan vagy illetéktelen hozzáférést.

### <a name="denied-internet-to-back-end-server"></a>(Tiltott) A háttérkiszolgáló-Internet

1. Az internetes felhasználó megkísérel hozzáférni AppVM01 egy fájlt a BackEnd001.CloudApp.Net szolgáltatáson keresztül.
2. Nincsenek nyissa meg a fájlmegosztás, ezért a kérelem nem adja át a felhőszolgáltatás végpontok. A forgalom nem éri el a kiszolgálót.
3. Ha valamilyen okból a végpontok nyitva, az NSG-t (internet letiltása) blokkolja a forgalmat.
4. Végül az UDR-útvonal küld minden kimenő forgalmat a AppVM01 a tűzfalon a következő ugrás. A tűzfal látja, aszimmetrikus adatforgalom, és a kimenő válasz csökken.

> Ebből kifolyólag nincsenek az internetes és AppVM01 között legalább három független rétege. A felhőszolgáltatás megakadályozza a jogosulatlan vagy illetéktelen hozzáférést.

### <a name="denied-front-end-server-to-back-end-server"></a>(Tiltott) Háttér-előtér-kiszolgáló

1. IIS01 biztonsága sérül, és a háttérbeli alhálózat kiszolgálók tett kísérlet kártevő kódja fut-e.
1. Az előtérbeli alhálózat UDR útvonal küld minden kimenő forgalmat a IIS01 a tűzfalon a következő ugrás. A sérült biztonságú virtuális gép nem módosítható az útválasztást.
1. A tűzfal dolgozza fel a forgalmat. Ha a kérés AppVM01 vagy a DNS-kiszolgáló DNS-keresések, a tűzfal előfordulhat, hogy lehetővé teszi a forgalom miatt tűzfalszabályok 7. és 9. Az összes többi forgalom tűzfalszabály 11 (az összes elutasítása) le van tiltva.
1. Ha engedélyezi a fejlett fenyegetésészlelés, a tűzfalon, ismert aláírások vagy minták, hogy ez a jelző azt egy komplex veszélyforrások elleni szabályt tartalmazó forgalom nem sikerült történik meg. Ez a mérték is működik, akkor is, ha ebben a cikkben tárgyalt alapszintű továbbítási szabályainak megfelelően engedélyezi a forgalmat. Speciális fenyegetésészlelés Ez a dokumentum nem tárgyalja. A speciális hálózati berendezés advanced threat képességek a gyártói dokumentációjában talál.

### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Tiltott) A DNS-kiszolgáló internetes DNS-címkeresés

1. An internet user tries to look up an internal DNS record on DNS01 through BackEnd001.CloudApp.Net service.
1. Végpontleképzőben nincs megnyitva a DNS-forgalom, mivel ez a forgalom nem halad át a felhőalapú szolgáltatás. A kiszolgáló nem éri el.
1. Ha valamilyen okból a végpontok nyitva, az NSG-szabály (blokk internet) az előtér-alhálózat blokkolja a forgalmat.
1. Végül a háttérbeli alhálózat UDR útvonal küld minden kimenő forgalmat a DNS01 a tűzfalon a következő ugrás. A tűzfal látja ezt az aszimmetrikus adatforgalom, és a kimenő válasz csökken.

> Ebből kifolyólag nincsenek az internetes és DNS01 között legalább három független rétege. A felhőszolgáltatás megakadályozza a jogosulatlan vagy illetéktelen hozzáférést.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Tiltott) Az SQL-hozzáférés tűzfalon keresztüli Internet

1. Az internetes felhasználó SQL adatokat kér a SecSvc001.CloudApp.Net internetkapcsolattal rendelkező felhőalapú szolgáltatás.
1. Nincsenek végpontok nyissa meg az SQL, a forgalmat nem adja át a felhőalapú szolgáltatás. A tűzfal nem éri el.
1. Ha valamilyen okból SQL végpontok nyitva, a tűzfal szabály feldolgozása hajtja végre:
   1. Tűzfalszabály 1 (Keretrendszer Mgmt) nem vonatkozik. Ugrás a következő szabály.
   1. 2 – 5 (RDP-szabályok) tűzfal-szabályok nem érvényesek. Ugrás a következő szabály.
   1. 6 és 7 (alkalmazás szabályok) tűzfal-szabályok nem érvényesek. Ugrás a következő szabály.
   1. Tűzfalszabály 8 (internet) nem vonatkozik. Ugrás a következő szabály.
   1. Tűzfalszabály 9 (DNS) nem vonatkozik. Ugrás a következő szabály.
   1. Tűzfalszabály 10 (Intra-alhálózat) nem vonatkozik. Ugrás a következő szabály.
   1. Tűzfalszabály 11 (az összes elutasítása) a alkalmazni. Forgalom le van tiltva. Állítsa le a szabály feldolgozása.

## <a name="references"></a>Referencia

Ez a szakasz a következő elemeket tartalmazza:

* Teljes szkript. Mentse azt egy olyan PowerShell-parancsfájlt.
* Hálózati konfiguráció. Mentse a fájlt egy NetworkConf2.xml nevű fájlba.

A felhasználó által definiált változókat a fájlokban, szükség szerint módosítsa. Futtassa a szkriptet, és kövesse az ebben a cikkben ismertetett tűzfal szabály telepítési utasításokat.

### <a name="full-script"></a>Teljes szkript

Miután beállította a felhasználó által definiált változókat, futtassa ezt a szkriptet:

1. Csatlakozás Azure-előfizetéshez
1. Új tárfiók létrehozása
1. Hozzon létre egy új virtuális hálózatot és három alhálózatot a hálózati konfigurációs fájlban meghatározott
1. Öt virtuális gépeket hozhat létre: a tűzfal- és négy Windows Server rendszerű virtuális gépek
1. UDR konfigurálásához:
   1. Két új útválasztási táblázat létrehozása
   1. Útvonalak hozzáadása a táblákhoz
   1. Táblák megfelelő alhálózatokat kötése
1. Az nva IP-továbbítás engedélyezése
1. Az NSG konfigurálása:
   1. Hozzon létre egy NSG-t
   1. Szabály hozzáadása
   1. Az NSG-t a megfelelő alhálózatokat kötése

Futtatás a PowerShell szkriptet helyileg, az internet csatlakoztatott számítógépen vagy kiszolgálón.

> [!IMPORTANT]
> Ez a szkript futtatásakor a figyelmeztetések vagy más tájékoztató üzeneteket előfordulhat, hogy felugró a PowerShellben. Csak a piros hibaüzeneteket is adhatnak okot.

```powershell
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
       - IP Forwarding from the FireWall out to the internet
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

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
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
    # No User Defined Variables or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) {
            Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
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

      # Associate the Route Tables with the Subnets
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
        # since the NSG bound to the FrontEnd and BackEnd subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install BackEnd resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

### <a name="network-config-file"></a>Hálózati konfigurációs fájl

Mentse az XML-fájl hely frissítve. Módosítsa a `$NetworkConfigFile` mutató hivatkozást a mentett hálózati konfigurációs fájlban a fenti teljes szkript változóját.

```xml
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
```

## <a name="next-steps"></a>További lépések

Segítség a szegélyhálózat-alapú hálózati például egy mintaalkalmazást is telepítheti.

> [!div class="nextstepaction"]
> [A példaszkript alkalmazás](./virtual-networks-sample-app.md)

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
