---
title: Azure DMZ-példa – hozhat létre egy egyszerű Szegélyhálózat NSG-k |} A Microsoft Docs
description: Semleges zóna kialakítása hálózati biztonsági csoportokkal (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 2f399b5084ab65736adfebb5cf0a77ccfbc972e8
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457289"
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>1 – példa egy egyszerű Szegélyhálózat NSG-k használata az Azure Resource Manager-sablon létrehozása
[Térjen vissza a biztonsági határ ajánlott eljárások lap][HOME]

> [!div class="op_single_selector"]
> * [Resource Manager-sablon](virtual-networks-dmz-nsg.md)
> * [Klasszikus – PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Ebben a példában egy egyszerű szegélyhálózat (DMZ) hoz létre négy Windows-kiszolgálók és hálózati biztonsági csoportok. Ebben a példában adja meg az egyes lépések jobban megértheti a megfelelő sablont szakaszokat ismerteti. Emellett van egy forgalom forgatókönyv szakaszban adja meg a részletes szűrőtípusok hogyan halad a forgalom a rétege a DMZ-n keresztül. Végül az hivatkozások szakaszban a teljes sablont kód és a vonatkozó útmutatást, ebben a környezetben tesztelheti, és kísérletezzen a különböző forgatókönyvekben. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![NSG bejövő Szegélyhálózat][1]

## <a name="environment-description"></a>Környezet leírása
Ebben a példában egy előfizetés a következőket tartalmazza:

* Egyetlen erőforráscsoportra
* Egy virtuális hálózatot két alhálózattal "Előtér" és "Háttér"
* Mindkét alhálózat alkalmazott hálózati biztonsági csoport
* A Windows Server, amely egy webalkalmazás-kiszolgáló ("IIS01") jelöli.
* Két windows-kiszolgálók, amelyek az alkalmazás háttér-kiszolgálók ("AppVM01", "AppVM02")
* A Windows server DNS-kiszolgáló ("DNS01") jelölő
* Az alkalmazás webes kiszolgálóhoz társított nyilvános IP-cím

A hivatkozások szakaszban az Azure Resource Manager-sablon, amely összeállítja a környezetben, az ebben a példában leírt kapcsolat van. A virtuális gépek és virtuális hálózatok, bár a példa sablon által végzett nem ismerteti a jelen dokumentum részletesen. 

**Ebben a környezetben hozhat létre** (részletes utasításokat is ez a dokumentum a hivatkozások szakaszban);

1. Az Azure Resource Manager-sablon, üzembe helyezése: [Azure gyorsindítási sablonok][Template]
2. A mintaalkalmazás telepítése: [minta alkalmazás-parancsfájl][SampleApp]

>[!NOTE]
>Ebben a példányban háttér-kiszolgálók távoli asztali eléréséhez az IIS-kiszolgálón szolgál egy "jump boxon." Az IIS-kiszolgálón, majd az IIS kiszolgáló RDP-vel a háttér-kiszolgáló az első RDP. Alternatív megoldásként egy nyilvános IP-címet minden egyes kiszolgáló egyszerűbb RDP hálózati Adapteréhez társítva lehet.
> 
>

A következő szakaszok a hálózati biztonsági csoportot, és hogyan működik-e ehhez a példához az Azure Resource Manager-sablon a kulcsfontosságú sorokat, ajánljuk figyelmébe a részletes leírását.

## <a name="network-security-groups-nsg"></a>Hálózati biztonsági csoportok (NSG)
Ebben a példában egy NSG-csoport beépített, és aztán betölti a hat szabályokkal. 

>[!TIP]
>Általánosan fogalmazva az "Engedélyezés" szabályainak először hozzon létre, és ezután az "Elutasítás" általánosabb érvényűvé szabályok utolsó. A hozzárendelt prioritásokkal azt határozza meg szabályokat, amelyek az első értékeli ki. Forgalom kiderül, hogy egy adott szabály vonatkozik, ha nincsenek további szabályok értékeli ki. Az NSG-szabályok vagy (az alhálózat szempontjából) a bejövő vagy kimenő irányban is érvényesek.
>
>

Deklaratív a következő szabályok beépített folyamatban van a bejövő forgalmat:

1. Belső DNS-forgalmat (53-as port)
2. RDP-forgalmat (3389-es port) az internetről bármely virtuális géphez
3. HTTP-forgalmat (80-as port) az internetről web Server (IIS01)
4. Minden újabb forgalmát (minden porthoz) IIS01 AppVM1 engedélyezett
5. Virtuális hálózat (mindkét alhálózat) bármely (minden porthoz) az internetről forgalmat a teljes megtagadva
6. Az összes bejövő forgalom (minden porthoz) a felhasználói réteg alhálózatáról a háttérrendszer alhálózatának megtagadva

A következő szabályok kötött mindegyik olyan alhálózatban, ha HTTP-kérést a webkiszolgáló, 3 szabályokat is az internetről bejövő (engedélyezése) és 5 (nem engedélyezi a) a alkalmazni szeretne, de mivel a szabály 3 egy magasabb prioritással bír, csak a alkalmazni, és szabály 5 lenne nem jut. Így a HTTP-kérelem szeretné engedélyezni a webkiszolgáló. Ha ugyanaz a forgalom próbál elérni az DNS01 kiszolgálót, 5 (Megtagadás) szabály lenne a alkalmazni az első, és szeretne átadni a kiszolgálónak nem engedélyezi a forgalmat. 6 (Megtagadás) szabály blokkolja az előtér-alhálózatot a beszélni a Backend alhálózathoz (kivéve az 1. és 4 szabályok engedélyezett forgalom), a szabálykészlet abban az esetben, ha egy támadó feltörések előtér, a támadó a webalkalmazás lenne korlátozott védelmet biztosít a háttérrendszer-hálózat a Háttérrendszeri hozzáférést "védett" hálózati (csak az erőforrások elérhetővé tett a AppVM01 kiszolgálón).

Nincs olyan alapértelmezett kimenő szabály, amely lehetővé teszi, hogy az internetre irányuló forgalom. Ebben a példában folyamatban van lehetővé teszi a kimenő forgalom és nem módosítja a kimenő szabályok. A alkalmazni a biztonsági szabályzatot forgalom mindkét irányba, felhasználói meghatározott Útválasztás megadása kötelező, és a"3" van megvizsgálta a a [biztonsági határ ajánlott eljárásokat tartalmazó lapon][HOME].

Minden egyes szabály a következő tárgyalja részletesen:

1. Egy hálózati biztonsági csoport erőforrást kell példányosítani, amely tárolja a szabályokat:

    ```JSON
    "resources": [
      {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/networkSecurityGroups",
        "name": "[variables('NSGName')]",
        "location": "[resourceGroup().location]",
        "properties": { }
      }
    ]
    ``` 

2. Ebben a példában az első szabály lehetővé teszi, hogy a DNS-forgalmat a háttérbeli alhálózat DNS-kiszolgálóra az összes belső hálózatok között. A szabály néhány fontos paraméterekkel rendelkezik:
  * "destinationAddressPrefix" - célcím-előtagjában értéke "10.0.2.4 cím", hogy a DNS-forgalom engedélyezett, a DNS-kiszolgáló eléréséhez.
  * "Iránya" azt jelzi, hogy a forgalom iránya a szabály lép érvénybe. Irányát az alhálózatot, vagy a virtuális gép szempontjából (attól függően, ahol ez az NSG van kötve) van. Így ha iránya "Inbound" és a forgalom lépjen be az alhálózat, a szabály csak akkor vonatkozik és az alhálózatot elhagyó forgalomra nem érinti ez a szabály.
  * "Prioritás" állítja be, hogy a sorrendet, amelyben az adott ki lesz értékelve. Minél kisebb számra annál magasabb a prioritás. A szabály vonatkozik egy adott adatforgalmat, ha nincsenek további szabályok feldolgozása. Így ha egy 1-es prioritású szabály engedélyezi a forgalmat, és 2-es prioritású szabály megtagadja a forgalmat, és a forgalmi szabályokat is vonatkoznak majd melyeken engedélyezett a forgalom áramlását (mivel az 1. számú szabály kellett egy magasabb prioritású ideig tartott-e érvénybe, és nincsenek további szabályok alkalmazott).
  * "Hozzáférés" jelzi, ha ez a szabály által érintett forgalom lesz-e a letiltott ("Elutasítás") vagy az engedélyezett ("Engedélyezés").

    ```JSON
    "properties": {
    "securityRules": [
      {
        "name": "enable_dns_rule",
        "properties": {
          "description": "Enable Internal DNS",
          "protocol": "*",
          "sourcePortRange": "*",
          "destinationPortRange": "53",
          "sourceAddressPrefix": "VirtualNetwork",
          "destinationAddressPrefix": "10.0.2.4",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
    ```

3. Ez a szabály lehetővé teszi, hogy az RDP-forgalom áramlását az internetről érkező RDP-portjára kötött az alhálózaton bármelyik kiszolgálón. 

    ```JSON
    {
      "name": "enable_rdp_rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 110,
        "direction": "Inbound"
      }
    },
    ```

4. Ez a szabály lehetővé teszi, hogy a bejövő internetes forgalom eléri a webalkalmazás-kiszolgáló. Ez a szabály az útválasztási viselkedés nem változik. A szabály csak IIS01 át tartó forgalmat engedélyezi. Így ha az internetről forgalmat volt-e a célként, ez a szabály engedélyezi-e és további szabályok feldolgozásának leállítása a webkiszolgálón. (A szabályban prioritással 140 összes egyéb bejövő internetes forgalom blokkolva van). Ha Ön már csak dolgoz fel HTTP-forgalom, ez a szabály sikerült további korlátozva a kizárólag a 80-as Port cél.

    ```JSON
    {
      "name": "enable_web_rule",
      "properties": {
        "description": "Enable Internet to [variables('VM01Name')]",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "10.0.1.5",
        "access": "Allow",
        "priority": 120,
        "direction": "Inbound"
        }
      },
    ```

5. Ez a szabály lehetővé teszi, hogy a forgalom áthaladását a IIS01 kiszolgálóról a AppVM01 kiszolgálóra, egy újabb szabály blokkolja a háttérrendszer forgalom minden más előtér. Ez a szabály javításához, ha a port ismert, hogy hozzá kell adni. Például ha az IIS-kiszolgálón futó AppVM01 csak SQL Server eléri, Célport-tartományt kell módosítani a "*" (tetszőleges), ezzel lehetővé téve a kisebb méretű bejövő támadási felületét AppVM01 kell a webalkalmazás minden eddiginél sérülhet 1433 (az SQL-port).

    ```JSON
    {
      "name": "enable_app_rule",
      "properties": {
        "description": "Enable [variables('VM01Name')] to [variables('VM02Name')]",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "10.0.1.5",
        "destinationAddressPrefix": "10.0.2.5",
        "access": "Allow",
        "priority": 130,
        "direction": "Inbound"
      }
    },
     ```

6. Szabályok segítségével egy speciális típusa az "alapértelmezett címke" nevű cím előtagja, ezek a címkék olyan rendszer által biztosított azonosítók, amelyek lehetővé teszik egy egyszerű módja annak címelőtag egy nagyobb kategóriáját. Ez a szabály az alapértelmezett címke "VirtualNetwork" célcím-előtag használ a virtuális hálózaton belül bármilyen cím jelölésére. Más előtag feliratai internetes és az AzureLoadBalancer. Ez a szabály az internetről érkező forgalom megtagadásához kiszolgálók a hálózaton. A 110-es és 120 prioritással a szabályokat milyen hatása, hogy csak a bejövő internetes forgalmat a tűzfal és a kiszolgálókon és blokkok RDP-portok minden mást. Ez a szabály minden nem várt folyamatok blokkolása "hibamentes" szabály.

    ```JSON
    {
      "name": "deny_internet_rule",
      "properties": {
        "description": "Isolate the [variables('VNetName')] VNet from the Internet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Deny",
        "priority": 140,
        "direction": "Inbound"
      }
    },
     ```

7. A végső szabályát a felhasználói réteg alhálózatáról forgalmat a háttérbeli alhálózathoz megtagadja. Mivel ez a szabály egy bejövő egyetlen szabályt, fordított irányú forgalom van engedélyezve (az előtér-háttérrendszerrel).

    ```JSON
    {
      "name": "deny_frontend_rule",
      "properties": {
        "description": "Isolate the [variables('Subnet1Name')] subnet from the [variables('Subnet2Name')] subnet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "[variables('Subnet1Prefix')]",
        "destinationAddressPrefix": "[variables('Subnet2Prefix')]",
        "access": "Deny",
        "priority": 150,
        "direction": "Inbound"
      }
    }
    ```

## <a name="traffic-scenarios"></a>Forgalom forgatókönyvek
#### <a name="allowed-internet-to-web-server"></a>(*Engedélyezett*) internetes webkiszolgálóra
1. Az internetes felhasználó egy HTTP-lap kér a hálózati Adaptert a IIS01 hálózati Adapterhez társított nyilvános IP-címe
2. A nyilvános IP-cím forgalmat továbbítja a VNet felé IIS01 (webkiszolgáló)
3. Előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
  1. NSG-szabály 1 (DNS) nem vonatkoznak, helyezze át a következő szabály
  2. NSG-szabály 2 (RDP) nem vonatkoznak, helyezze át a következő szabály
  3. NSG 3. szabály (IIS01 interneten) a alkalmazni, a forgalom engedélyezett, állítsa le a szabály feldolgozása
4. Forgalom eléri a belső IP-címét a webkiszolgáló IIS01 (10.0.1.5)
5. IIS01 figyeli a webes forgalmat, ezt a kérelmet kap, és elindítja a kérés feldolgozása
6. IIS01 kéri az SQL Server a AppVM01 információk
7. Nincs kimenő szabályok előtérbeli alhálózatán, forgalom engedélyezve van
8. A háttérrendszer alhálózatának bejövő szabály feldolgozása kezdődik:
  1. NSG-szabály 1 (DNS) nem vonatkoznak, helyezze át a következő szabály
  2. NSG-szabály 2 (RDP) nem vonatkoznak, helyezze át a következő szabály
  3. NSG 3. szabály (Internet tűzfalhoz) nem vonatkoznak, helyezze át a következő szabály
  4. NSG-szabály 4 (a AppVM01 IIS01) vonatkozik, a forgalom engedélyezve van, állítsa le a szabály feldolgozása
9. AppVM01 az SQL-lekérdezést kap, és válaszol
10. Nincsenek kimenő szabályok a háttérbeli alhálózat, mivel engedélyezve van-e a válasz
11. Előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
  1. Nincs NSG szabály, amelyre vonatkozik a bejövő forgalmat a háttérbeli alhálózat felől az előtérben levő alhálózathoz, így egyike sem az NSG-szabályok alkalmazása
  2. A alapértelmezett rendszerszabály, amely lehetővé teszi az alhálózatok közötti adatforgalom lehetővé tenné a forgalmat, így a forgalom engedélyezve van.
12. Az IIS-kiszolgálón az SQL-válasz fogadása és befejezése a HTTP-válasz és küld a kérelmező
13. Mivel nincsenek kimenő szabályok az előtérbeli alhálózat, a válasz engedélyezve van, és az internetes felhasználó kapja meg a kért weblap.

#### <a name="allowed-rdp-to-iis-server"></a>(*Engedélyezett*) az IIS-kiszolgálón RDP-vel
1. Egy kiszolgáló-rendszergazdai interneten kér egy RDP-munkamenetet a IIS01 a nyilvános IP-címét a hálózati Adapterhez megadott IIS01 hálózati adapter (a nyilvános IP-cím található a portál vagy a PowerShell használatával)
2. A nyilvános IP-cím forgalmat továbbítja a VNet felé IIS01 (webkiszolgáló)
3. Előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
  1. NSG-szabály 1 (DNS) nem vonatkoznak, helyezze át a következő szabály
  2. NSG-szabály 2 (RDP) a alkalmazni, a forgalom engedélyezett, állítsa le a szabály feldolgozása
4. Kimenő szabályok, az alapértelmezett szabályok a alkalmazni, és a visszatérő forgalom engedélyezve van
5. RDP-munkamenet engedélyezve van
6. IIS01 kérni fogja a felhasználónevet és jelszót

>[!NOTE]
>Ebben a példányban háttér-kiszolgálók távoli asztali eléréséhez az IIS-kiszolgálón szolgál egy "jump boxon." Az IIS-kiszolgálón, majd az IIS kiszolgáló RDP-vel a háttér-kiszolgáló az első RDP.
>
>

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Engedélyezett*) a DNS-kiszolgáló Web server a DNS szolgáltatásban
1. A Web Server, IIS01, egy adatcsatorna www.data.gov, igényeinek megfelelően, de igények címének feloldására.
2. A hálózati konfigurációt a virtuális hálózatok közötti listák DNS01 (a háttérbeli alhálózat 10.0.2.4 cím), az elsődleges DNS-kiszolgáló, IIS01 küld a DNS-kérelem DNS01
3. Nincs kimenő szabályok előtérbeli alhálózatán, forgalom engedélyezve van
4. Háttérbeli alhálózatot bejövő szabály feldolgozása kezdődik:
  * NSG-szabály 1 (DNS) a alkalmazni, a forgalom engedélyezett, állítsa le a szabály feldolgozása
5. DNS-kiszolgáló a kérést kap
6. DNS-kiszolgáló nem rendelkezik a címmel a gyorsítótárba, és az interneten egy DNS-gyökérkiszolgáló kéri
7. Nincs kimenő szabályok a háttérbeli alhálózat forgalom engedélyezve van
8. Internetes DNS-kiszolgáló válaszol, mivel ez a munkamenet indított belső, a válasz engedélyezett
9. DNS-kiszolgáló gyorsítótárazza a választ, és reagáljon a biztonsági IIS01 irányuló első kérelem
10. Nincs kimenő szabályok a háttérbeli alhálózat forgalom engedélyezve van
11. Előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
  1. Nincs NSG szabály, amelyre vonatkozik a bejövő forgalmat a háttérbeli alhálózat felől az előtérben levő alhálózathoz, így egyike sem az NSG-szabályok alkalmazása
  2. A alapértelmezett rendszerszabály, amely lehetővé teszi az alhálózatok közötti adatforgalom lehetővé tenné a forgalmat, így a forgalom engedélyezve van
12. IIS01 DNS01 kap a válasz

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Engedélyezett*) kiszolgáló-hozzáférés fájlt AppVM01
1. IIS01 AppVM01 fájlt kéri
2. Nincs kimenő szabályok előtérbeli alhálózatán, forgalom engedélyezve van
3. A háttérrendszer alhálózatának bejövő szabály feldolgozása kezdődik:
  1. NSG-szabály 1 (DNS) nem vonatkoznak, helyezze át a következő szabály
  2. NSG-szabály 2 (RDP) nem vonatkoznak, helyezze át a következő szabály
  3. NSG 3. szabály (az internethez való IIS01) nem vonatkoznak, helyezze át a következő szabály
  4. NSG-szabály 4 (a AppVM01 IIS01) vonatkozik, a forgalom engedélyezve van, állítsa le a szabály feldolgozása
4. AppVM01 dokumentálásáért fájllal (feltéve, hogy a hozzáférés engedélyezett), és a kérést kap
5. Nincsenek kimenő szabályok a háttérbeli alhálózat, mivel engedélyezve van-e a válasz
6. Előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
  1. Nincs NSG szabály, amelyre vonatkozik a bejövő forgalmat a háttérbeli alhálózat felől az előtérben levő alhálózathoz, így egyike sem az NSG-szabályok alkalmazása
  2. A alapértelmezett rendszerszabály, amely lehetővé teszi az alhálózatok közötti adatforgalom lehetővé tenné a forgalmat, így a forgalom engedélyezve van.
7. Az IIS-kiszolgáló fogadja a fájl

#### <a name="denied-rdp-to-backend"></a>(*Megtagadva*) háttérrendszer RDP-vel
1. Az internetes felhasználó RDP próbál AppVM01 kiszolgáló
2. Mivel ebben az esetben nem a hálózati adapter kiszolgálókhoz társított nyilvános IP-címek, a forgalmat soha ne adjon meg a virtuális hálózathoz, és a kiszolgáló nem érhető el.
3. Azonban ha valamilyen okból engedélyezve lett a nyilvános IP-cím, NSG-szabály (RDP) 2 lehetővé tenné, hogy ez a forgalom

>[!NOTE]
>Ebben a példányban háttér-kiszolgálók távoli asztali eléréséhez az IIS-kiszolgálón szolgál egy "jump boxon." Az IIS-kiszolgálón, majd az IIS kiszolgáló RDP-vel a háttér-kiszolgáló az első RDP.
>
>

#### <a name="denied-web-to-backend-server"></a>(*Megtagadva*) a háttérkiszolgáló webes
1. Az internetes felhasználó próbál meg hozzáférni egy fájlt a AppVM01
2. Mivel ebben az esetben nem a hálózati adapter kiszolgálókhoz társított nyilvános IP-címek, a forgalmat soha ne adjon meg a virtuális hálózathoz, és a kiszolgáló nem érhető el.
3. Ha valamilyen okból engedélyezve lett a nyilvános IP-cím, 5 (Internet, virtuális hálózatok közötti) NSG-szabályt le fog állni a forgalmat

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Megtagadva*) a DNS-kiszolgáló a webhely DNS szolgáltatásban
1. Az internetes felhasználó próbál egy belső DNS-rekord DNS01 keresése
2. Mivel ebben az esetben nem a hálózati adapter kiszolgálókhoz társított nyilvános IP-címek, a forgalmat soha ne adjon meg a virtuális hálózathoz, és a kiszolgáló nem érhető el.
3. Ha valamilyen okból engedélyezve lett a nyilvános IP-cím, 5 (Internet, virtuális hálózatok közötti) NSG-szabályt le fog állni a forgalmat (Megjegyzés: az internethez, hogy a szabály 1 (DNS) nem alkalmazhatók, mert a kérelmek forrás címe nem és az 1. szabály csak a helyi virtuális hálózat, mint a forrás vonatkozik)

#### <a name="denied-sql-access-on-the-web-server"></a>(*Megtagadva*) SQL-hozzáférését a webkiszolgálón
1. Az internetes felhasználó SQL adatokat kér IIS01
2. Mivel ebben az esetben nem a hálózati adapter kiszolgálókhoz társított nyilvános IP-címek, a forgalmat soha ne adjon meg a virtuális hálózathoz, és a kiszolgáló nem érhető el.
3. Ha valamilyen okból engedélyezve lett a nyilvános IP-cím, az előtér-alhálózatot a bejövő szabály feldolgozása kezdődik:
  1. NSG-szabály 1 (DNS) nem vonatkoznak, helyezze át a következő szabály
  2. NSG-szabály 2 (RDP) nem vonatkoznak, helyezze át a következő szabály
  3. NSG 3. szabály (IIS01 interneten) a alkalmazni, a forgalom engedélyezett, állítsa le a szabály feldolgozása
4. Forgalom eléri a IIS01 belső IP-címét (10.0.1.5)
5. IIS01 1433-as porton, így nincs válasz a kérésre nem figyel.

## <a name="conclusion"></a>Összegzés
Ebben a példában egy viszonylag egyszerű, és nagyon egyszerű mód a bejövő forgalmat a háttérbeli alhálózat elkülönítése.

További példákat és a hálózati biztonsági határok áttekintését találja [Itt][HOME].

## <a name="references"></a>Referencia
### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Ebben a példában egy előre meghatározott Azure Resource Manager-sablont használ egy GitHub-tárházat, a Microsoft által karbantartott és a Közösség számára. Ez a sablon telepíthető közvetlenül a githubból, vagy letöltött és módosított saját igényei szerint. 

A fő sablon szerepel a "azuredeploy.json." nevű fájl Ez a sablon beküldhető PowerShell vagy parancssori felület (a társított "azuredeploy.parameters.json"-fájl) Ez a sablon üzembe helyezéséhez. A legegyszerűbben a "Üzembe helyezése az Azure-bA" gomb használatához a github webhelyen a README.md oldalon található.

A sablon olyan ebben a példában a Githubról, majd az Azure Portalon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Egy böngészőből keresse fel a [sablon][Template]
2. Kattintson a "Üzembe helyezése az Azure-bA" gomb (vagy a "Visualize" gombra a sablon grafikus megjelenítése)
3. Adja meg a Storage-fiók, felhasználónév és jelszó a (paraméterek) panelen, majd kattintson a **OK**
5. Hozzon létre egy erőforráscsoportot az üzembe helyezés (egy már meglévőt is használhatja, de egy új a legjobb eredmények elérése érdekében javasolt)
6. Szükség esetén módosítsa a virtuális hálózat az előfizetésben és helyen beállításait.
7. Kattintson a **jogi feltételek áttekintése**, olvassa el a feltételeket, majd kattintson **beszerzési** fogadjon el.
8. Kattintson a **létrehozás** , ez a sablon üzembe helyezésének megkezdése.
9. A telepítés sikeres befejeződése után keresse meg az erőforráscsoport létrehozásánál a központi telepítés belül konfigurált erőforrások megtekintéséhez.

>[!NOTE]
>Ez a sablon lehetővé teszi, hogy az RDP-vel a IIS01-kiszolgáló (Keresés a nyilvános IP-cím IIS01 a portál esetében). Ebben a példányban háttér-kiszolgálók távoli asztali eléréséhez az IIS-kiszolgálón szolgál egy "jump boxon." Az IIS-kiszolgálón, majd az IIS kiszolgáló RDP-vel a háttér-kiszolgáló az első RDP.
>
>

A központi telepítés eltávolításához törölje az erőforráscsoportot, és minden gyermek-erőforrás is törlődik.

#### <a name="sample-application-scripts"></a>Mintaszkriptek alkalmazás
A sablon sikeres futtatása után beállíthatja a webkiszolgáló és a egy egyszerű webalkalmazást, hogy a tesztelés a DMZ-konfigurációval rendelkező alkalmazások kiszolgálói. Egy mintaalkalmazás telepítése ezzel és más DMZ példák, egy lett megadva, a következő hivatkozásra: [minta alkalmazás-parancsfájl][SampleApp]

## <a name="next-steps"></a>További lépések

* Ebben a példában üzembe helyezése
* A mintaalkalmazás létrehozása
* A szegélyhálózat (DMZ) keresztül forgalmakat folyamatok tesztelése

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "NSG bejövő Szegélyhálózat"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md
