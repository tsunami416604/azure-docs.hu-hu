---
title: "DNS-zóna létrehozása a parancssori felület (CLI) használatával| Microsoft Docs"
description: "Részletes ismertető arról, hogyan hozhat létre DNS-zónákat az Azure DNS számára a parancssori felületen, és indíthatja el a saját DNS-tartománya üzemeltetését"
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e2b93d8788f8f36ff6bc04a33d1f35a86cd49973


---
# <a name="create-an-azure-dns-zone-using-cli"></a>Azure DNS-zóna létrehozása a parancssori felület használatával
> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)
> 
> 

Ez a cikk végigvezeti a DNS-zónák parancssori felület használatával történő létrehozásához szükséges lépéseken. A DNS-zónákat a PowerShell használatával, illetve az Azure portálon is létrehozhatja.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="before-you-begin"></a>Előkészületek
Az itt szereplő utasítások a Microsoft Azure CLI használatára vonatkoznak. Az Azure DNS-parancsainak használatához frissítsen az Azure CLI legújabb (0.9.8-as vagy újabb) verziójára. Az `azure -v` parancs futtatásával ellenőrizheti, hogy az Azure CLI melyik verziója van telepítve a számítógépen.

## <a name="step-1-set-up-azure-cli"></a>1. lépés: Az Azure CLI beállítása
### <a name="1-install-azure-cli"></a>1. Telepítse az Azure CLI-t
Az Azure CLI Windows, Linux és Mac rendszerre telepíthető. Az alábbi lépéseket kell elvégezni ahhoz, hogy az Azure DNS szolgáltatást az Azure CLI segítségével kezelhesse. További információt az [Azure parancssori felület (CLI) telepítése](../xplat-cli-install.md) című cikkben olvashat. A DNS-parancsok használatához az Azure CLI 0.9.8-as vagy újabb verziója szükséges.

A parancssori felület összes hálózati szolgáltatói parancsa megtekinthető a következő paranccsal:

    azure network

### <a name="2-switch-cli-mode"></a>2. Kapcsolja át a parancssori felület működési módját
Az Azure DNS az Azure Resource Managert használja. Váltson át a parancssori felületet megfelelő módjára az ARM-parancsok használatához.

    azure config mode arm

### <a name="3-sign-in-to-your-azure-account"></a>3. Jelentkezzen be az Azure-fiókjába
A rendszer arra kéri, hogy végezzen hitelesítést a hitelesítő adataival. Ne feledje, hogy csak ORGID-fiókokat használhat.

    azure login -u "username"

### <a name="4-select-the-subscription"></a>4. Válassza ki az előfizetést
Válassza ki, hogy melyek Azure-előfizetését használja.

    azure account set "subscription name"

### <a name="5-create-a-resource-group"></a>5. Hozzon létre egy erőforráscsoportot
Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Mivel azonban minden DNS-erőforrás globális, nem pedig regionális, az erőforráscsoport kiválasztott helye nincs hatással az Azure DNS szolgáltatásra.

Ezt a lépést kihagyhatja, ha egy meglévő erőforráscsoportot használ.

    azure group create -n myresourcegroup --location "West US"


### <a name="6-register"></a>6. Regisztráljon
Az Azure DNS szolgáltatást a Microsoft.Network erőforrás-szolgáltató kezeli. Az Azure DNS használatbavétele előtt az Azure-előfizetést regisztrálni kell ennek az erőforrás-szolgáltatónak a használatához. Ez a műveletet minden egyes előfizetés esetén csak egyszer kell elvégezni.

    azure provider register --namespace Microsoft.Network


## <a name="step-2-create-a-dns-zone"></a>2. lépés: A DNS-zóna létrehozása
A DNS-zóna az `azure network dns zone create` parancs használatával hozható létre. Szükség esetén a DNS-zónát címkékkel együtt is létrehozhatja. A címkék olyan, név-érték párokból álló listák, amelyeket az Azure Resource Manager használ az erőforrások számlázási vagy csoportosítási célú megcímkézéséhez. A címkékkel kapcsolatos további információért tekintse meg [Az Azure-erőforrások rendszerezése címkék használatával](../resource-group-using-tags.md) című cikket.

Az Azure DNS szolgáltatásban a zóna nevét a záró **„.”** nélkül kell megadni. Például a „**contoso.com.**” helyett a „**contoso.com**” formát kell használni.

### <a name="to-create-a-dns-zone"></a>DNS-zóna létrehozása
Az alábbi példaparancs a *MyResourceGroup* erőforráscsoportban létrehozza a *contoso.com* DNS-zónát.

A példát követve, és az értékeket a sajátjaira cserélve hozza létre a saját DNS-zónáját.

    azure network dns zone create myresourcegroup contoso.com

### <a name="to-create-a-dns-zone-and-tags"></a>DNS-zóna és címkék létrehozása
Az Azure DNS parancssori felülete támogatja a DNS-zónák nem kötelező *-Tag* paraméterrel megadott címkéit. Az alábbi példa egy két paraméterrel (project = demo és env = test) rendelkező DNS-zóna létrehozását szemlélteti.

A példát követve, és az értékeket a sajátjaira cserélve hozza létre a saját DNS-zónáját és címkéit.

    azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## <a name="view-records"></a>A rekordok megtekintése
A DNS-zónák létrehozásával a következő DNS-rekordok is létrejönnek:

* A „mérvadó adatforrásrekord” (SOA-rekord). Ez minden DNS-zóna gyökerében megtalálható.
* A mérvadó névkiszolgálói (NS-) rekordok. Ezek a rekordok jelenítik meg, hogy mely névkiszolgálók üzemeltetik a zónát. Az Azure DNS névkiszolgálókból álló készletet használ, így a különböző névkiszolgálók különböző zónákhoz tartozhatnak az Azure DNS szolgáltatásban. További információért tekintse meg a [Tartomány delegálása az Azure DNS-be](dns-domain-delegation.md) című cikket.

A rekordok megtekintéséhez az `azure network dns-record-set show` parancsot használhatja.<BR>
*Használat: network dns record-set show <erőforráscsoport> <dns-zóna-neve><name><type>*

Ha az alábbi példában a parancsot a *myresourcegroup* erőforráscsoporttal, a rekordhalmaz-névvel *"@"*(gyökérrekordként) és a *SOA* típussal futtatja, akkor a kimenete a következő lesz:

    azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/SOA
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    SOA record:
    data:      Email                         : msnhst.microsoft.com
    data:      Expire time                   : 604800
    data:      Host                          : edge1.azuredns-cloud.net
    data:      Minimum TTL                   : 300
    data:      Refresh time                  : 900
    data:      Retry time                    : 300
    data:                                    :
<BR>
A zónával létrehozott mérvadó névkiszolgálói rekordok megtekintéséhez használja a következő parancsot:

    azure network dns record-set show myresourcegroup "contoso.com" "@" NS
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    NS records
    data:        Name server domain name     : ns1-05.azure-dns.com
    data:        Name server domain name     : ns2-05.azure-dns.net
    data:        Name server domain name     : ns3-05.azure-dns.org
    data:        Name server domain name     : ns4-05.azure-dns.info
    data:
    info:    network dns-record-set show command OK

> [!NOTE]
> A DNS-zóna gyökerében (más néven *legfelső pontján*) található rekordhalmazok az **@** karaktert használják a rekordhalmaz neveként.
> 
> 

## <a name="test"></a>Tesztelés
A DNS-zónát a DNS-eszközök – például az nslookup, a DIG vagy a `Resolve-DnsName` PowerShell-parancsmag – segítségével tesztelheti.

Ha még nem delegálta a tartományt az új zóna Azure DNS-beli használatához, akkor a DNS-lekérdezést közvetlenül a zóna egyik névkiszolgálójához kell irányítani. A zóna névkiszolgálói a mérvadó névkiszolgálói rekordokban vannak megadva, a fenti „azure network dns record-set show” paranccsal megjelenített módon. Ügyeljen arra, hogy az alábbi parancsba a saját zónája megfelelő értékeit helyettesítse be.

Az alábbi példa a DIG segítségével és a DNS-zónához hozzárendelt névkiszolgálók használatával kérdezi le a contoso.com tartományt. A lekérdezésnek olyan névkiszolgálóra kell mutatnia, amelyhez a DIG segítségével az *@<name server for the zone>* értéket, valamint a zónanevet használtuk.

     <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
     Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
     flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
     WARNING: recursion requested but not available

     OPT PSEUDOSECTION:
     EDNS: version: 0, flags:; udp: 4000
      QUESTION SECTION:
    contoso.com.                        IN      A

     AUTHORITY SECTION:
    contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
    msnhst.microsoft.com. 6 900 300 604800 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>Következő lépések
Miután létrehozta a DNS-zónát, az internetes tartományához tartozó nevek feloldásának megkezdéséhez hozzon létre [rekordhalmazokat és rekordokat](dns-getstarted-create-recordset-cli.md).




<!--HONumber=Nov16_HO2-->


