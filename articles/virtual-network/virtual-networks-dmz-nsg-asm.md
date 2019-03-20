---
title: Azure DMZ-példa – hozhat létre egy egyszerű Szegélyhálózat NSG-k |} A Microsoft Docs
description: Semleges zóna kialakítása hálózati biztonsági csoportokkal (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: f8622b1d-c07d-4ea6-b41c-4ae98d998fff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 115a459c6a9e4ea96931c89272a49396f0656258
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993343"
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-classic-powershell"></a>1 – példa hozhat létre egy egyszerű Szegélyhálózat NSG-k használata a klasszikus PowerShell-lel
[Térjen vissza a biztonsági határ ajánlott eljárások lap][HOME]

> [!div class="op_single_selector"]
> * [Resource Manager-sablon](virtual-networks-dmz-nsg.md)
> * [Klasszikus – PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Ebben a példában egy egyszerű szegélyhálózat (DMZ) hoz létre négy Windows-kiszolgálók és hálózati biztonsági csoportok. Ebben a példában a megfelelő PowerShell-parancsokat biztosít az egyes lépések jobban megértheti ismerteti. Emellett van egy forgalom forgatókönyv részt, hogy egy részletes részletes hogyan halad a forgalom a rétege a DMZ-n keresztül. Végül az hivatkozások szakaszban, a teljes kódját és utasítás összeállításához, teszteléséhez, és kísérletezzen a különböző forgatókönyvekben ebben a környezetben. 

![NSG bejövő Szegélyhálózat][1]

## <a name="environment-description"></a>Környezet leírása
Ebben a példában egy előfizetés a következőket tartalmazza:

* Cloud services két: "FrontEnd001" és "BackEnd001"
* Egy virtuális hálózatot, a "CorpNetwork", két alhálózattal "Előtér" és "Háttér"
* Mindkét alhálózat alkalmazott hálózati biztonsági csoport
* A Windows Server, amely egy webalkalmazás-kiszolgáló ("IIS01") jelöli.
* Két windows-kiszolgálók, amelyek az alkalmazás háttér-kiszolgálók ("AppVM01", "AppVM02")
* A Windows server DNS-kiszolgáló ("DNS01") jelölő

A hivatkozások szakaszban van egy PowerShell-parancsprogram, amely összeállítja a környezetben, az ebben a példában leírt többsége. A virtuális gépek és virtuális hálózatok létrehozásához kell elvégeznie a példaszkript, bár nem ismerteti a jelen dokumentum részletesen. 

A környezet; hozhat létre

1. Mentse a hálózati konfigurációs xml-fájlt a references szakaszában szereplő (frissített nevét, helyét és IP-címeket az adott esethez)
2. Frissítse a felhasználói változók a parancsfájl felel meg a környezetben, a parancsfájl az, hogy futtatni (előfizetés, szolgáltatásnevek, stb.)
3. Hajtsa végre a szkriptet a PowerShellben

>[!Note]
>A régió, a PowerShell-parancsfájl esetében meg kell egyeznie a hálózati konfigurációs xml-fájl esetében.
>
>

Miután a szkript futtatása sikeresen további nem kötelező lépések lehet venni, a hivatkozások szakaszban két parancsfájlok állíthatja be a webkiszolgáló és a egy egyszerű webalkalmazást, hogy a tesztelés a DMZ-konfigurációval rendelkező alkalmazások kiszolgálói.

A következő szakaszok a hálózati biztonsági csoportok és működésének ebben a példában a PowerShell-parancsfájlt a kulcsfontosságú sorokat, ajánljuk figyelmébe a részletes leírását.

## <a name="network-security-groups-nsg"></a>Hálózati biztonsági csoportok (NSG)
Ebben a példában egy NSG-csoport beépített, és aztán betölti a hat szabályokkal. 

> [!TIP]
> Általánosan fogalmazva az "Engedélyezés" szabályainak először hozzon létre, és ezután az "Elutasítás" általánosabb érvényűvé szabályok utolsó. A hozzárendelt prioritásokkal azt határozza meg szabályokat, amelyek az első értékeli ki. Forgalom kiderül, hogy egy adott szabály vonatkozik, ha nincsenek további szabályok értékeli ki. Az NSG-szabályok vagy (az alhálózat szempontjából) a bejövő vagy kimenő irányban is érvényesek.
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

Nincs olyan alapértelmezett kimenő szabály, amely lehetővé teszi, hogy az internetre irányuló forgalom. Ebben a példában folyamatban van lehetővé teszi a kimenő forgalom és nem módosítja a kimenő szabályok. Forgalom mindkét irányba zárolhat, felhasználói meghatározott útválasztás szükséges, és a"3" van megvizsgálta a a [biztonsági határ ajánlott eljárásokat tartalmazó lapon][HOME].

Minden egyes szabály a következő cikkben részletesen módon (**Megjegyzés**: tetszőleges elemre a következő lista kiadásától kezdve egy dollárjelet (például: $NSGName) egy felhasználó által definiált változó a hivatkozási szakaszban Ez a dokumentum a parancsfájlból):

1. Először a hálózati biztonsági csoport kell elkészíteni, amely tárolja a szabályokat:

    ```PowerShell
    New-AzureNetworkSecurityGroup -Name $NSGName `
        -Location $DeploymentLocation `
        -Label "Security group for $VNetName subnets in $DeploymentLocation"
    ```

2. Ebben a példában az első szabály lehetővé teszi, hogy a DNS-forgalmat a háttérbeli alhálózat DNS-kiszolgálóra az összes belső hálózatok között. A szabály néhány fontos paraméterekkel rendelkezik:
   
   * "Type" azt jelzi, hogy a forgalom iránya a szabály lép érvénybe. Irányát az alhálózatot, vagy a virtuális gép szempontjából (attól függően, ahol ez az NSG van kötve) van. Így ha típus: "Inbound" és a forgalom lépjen be az alhálózat, a szabály csak akkor vonatkozik és az alhálózatot elhagyó forgalomra nem érinti ez a szabály.
   * "Prioritás" állítja be, hogy a sorrendet, amelyben az adott ki lesz értékelve. Minél kisebb számra annál magasabb a prioritás. A szabály vonatkozik egy adott adatforgalmat, ha nincsenek további szabályok feldolgozása. Így ha egy 1-es prioritású szabály engedélyezi a forgalmat, és 2-es prioritású szabály megtagadja a forgalmat, és a forgalmi szabályokat is vonatkoznak majd melyeken engedélyezett a forgalom áramlását (mivel az 1. számú szabály kellett egy magasabb prioritású ideig tartott-e érvénybe, és nincsenek további szabályok alkalmazott).
   * "Action" azt jelenti, ha ez a szabály által érintett forgalom blokkolva van, vagy engedélyezett.

     ```PowerShell    
     Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
        -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[4] `
        -DestinationPortRange '53' `
        -Protocol *
     ```

3. Ez a szabály lehetővé teszi, hogy az RDP-forgalom áramlását az internetről érkező RDP-portjára kötött az alhálózaton bármelyik kiszolgálón. Ez a szabály címelőtagok; speciális kétféle használ. "VIRTUAL_NETWORK" és "INTERNET". Ezek a címkék olyan egyszerűen címelőtag egy nagyobb kategóriáját.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
         -Type Inbound -Priority 110 -Action Allow `
         -SourceAddressPrefix INTERNET -SourcePortRange '*' `
         -DestinationAddressPrefix VIRTUAL_NETWORK `
         -DestinationPortRange '3389' `
         -Protocol *
    ```

4. Ez a szabály lehetővé teszi, hogy a bejövő internetes forgalom eléri a webalkalmazás-kiszolgáló. Ez a szabály az útválasztási viselkedés nem változik. A szabály csak IIS01 át tartó forgalmat engedélyezi. Így ha az internetről forgalmat volt-e a célként, ez a szabály engedélyezi-e és további szabályok feldolgozásának leállítása a webkiszolgálón. (A szabályban prioritással 140 összes egyéb bejövő internetes forgalom blokkolva van). Ha Ön már csak dolgoz fel HTTP-forgalom, ez a szabály sikerült további korlátozva a kizárólag a 80-as Port cél.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
         -Type Inbound -Priority 120 -Action Allow `
         -SourceAddressPrefix Internet -SourcePortRange '*' `
         -DestinationAddressPrefix $VMIP[0] `
         -DestinationPortRange '*' `
         -Protocol *
    ```

5. Ez a szabály lehetővé teszi, hogy a forgalom áthaladását a IIS01 kiszolgálóról a AppVM01 kiszolgálóra, egy újabb szabály blokkolja a háttérrendszer forgalom minden más előtér. Ez a szabály javításához, ha a port ismert, hogy hozzá kell adni. Például ha az IIS-kiszolgálón futó AppVM01 csak SQL Server eléri, Célport-tartományt kell módosítani a "*" (tetszőleges), ezzel lehetővé téve a kisebb méretű bejövő támadási felületét AppVM01 kell a webalkalmazás minden eddiginél sérülhet 1433 (az SQL-port).

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
        -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
    ```

6. Ez a szabály az internetről érkező forgalom megtagadásához kiszolgálók a hálózaton. A 110-es és 120 prioritással a szabályokat milyen hatása, hogy csak a bejövő internetes forgalmat a tűzfal és a kiszolgálókon és blokkok RDP-portok minden mást. Ez a szabály minden nem várt folyamatok blokkolása "hibamentes" szabály.
    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $VNetName VNet from the Internet" `
        -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *
    ```
7. A végső szabályát a felhasználói réteg alhálózatáról forgalmat a háttérbeli alhálózathoz megtagadja. Mivel ez a szabály egy bejövő egyetlen szabályt, fordított irányú forgalom van engedélyezve (az előtér-háttérrendszerrel).

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
        -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix `
        -DestinationPortRange '*' `
        -Protocol * 
    ```

## <a name="traffic-scenarios"></a>Forgalom forgatókönyvek
#### <a name="allowed-internet-to-web-server"></a>(*Engedélyezett*) internetes webkiszolgálóra
1. Az internetes felhasználó egy HTTP-lap kér FrontEnd001.CloudApp.Net (Internet Facing Felhőszolgáltatás)
2. Cloud service pass forgalmat a 80-as porton IIS01 felé nyitott végpontok (webkiszolgáló)
3. Előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
   1. NSG-szabály 1 (DNS) nem vonatkoznak, helyezze át a következő szabály
   2. NSG-szabály 2 (RDP) nem vonatkoznak, helyezze át a következő szabály
   3. NSG 3. szabály (IIS01 interneten) a alkalmazni, a forgalom engedélyezett, állítsa le a szabály feldolgozása
4. Forgalom eléri a belső IP-címét a webkiszolgáló IIS01 (10.0.1.5)
5. IIS01 figyeli a webes forgalmat, ezt a kérelmet kap, és elindítja a kérés feldolgozása
6. IIS01 kéri az SQL Server a AppVM01 információk
7. Nincsenek kimenő szabályok az előtér-alhálózatot, mert a forgalom engedélyezve van-e
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
12. Az IIS-kiszolgálón az SQL-válasz fogadása és befejezése a HTTP-válasz és küld a kérelmezőnek
13. Mivel nincsenek kimenő szabályok a válasz az előtérben levő alhálózathoz van engedélyezve, és az interneten felhasználó kapja meg a kért weblap.

#### <a name="allowed-rdp-to-backend"></a>(*Engedélyezett*) háttérrendszer RDP-vel
1. Kiszolgáló-rendszergazdai interneten kérelmek AppVM01 BackEnd001.CloudApp.Net:xxxxx, ahol xxxxx-e az RDP-vel (a hozzárendelt port találhatók az Azure Portalon vagy a Powershellen keresztül) AppVM01 véletlenszerűen hozzárendelt portszámot az RDP-munkamenetet
2. Háttérbeli alhálózatot bejövő szabály feldolgozása kezdődik:
   1. NSG-szabály 1 (DNS) nem vonatkoznak, helyezze át a következő szabály
   2. NSG-szabály 2 (RDP) a alkalmazni, a forgalom engedélyezett, állítsa le a szabály feldolgozása
3. Kimenő szabályok, az alapértelmezett szabályok a alkalmazni, és a visszatérő forgalom engedélyezve van
4. RDP-munkamenet engedélyezve van
5. AppVM01 kérni fogja a felhasználónevet és jelszót

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

#### <a name="denied-web-to-backend-server"></a>(*Megtagadva*) a háttérkiszolgáló webes
1. Az internetes felhasználó próbál meg hozzáférni a AppVM01 egy fájlt a BackEnd001.CloudApp.Net szolgáltatáson keresztül
2. Mivel ebben az esetben végpontok nyissa meg a fájlmegosztás, ezt a forgalmat a Felhőszolgáltatás nem kellene átadnia, és a kiszolgáló nem érhető el.
3. Ha valamilyen okból a végpontok volt megnyitva, NSG-szabály (Internet, virtuális hálózatok közötti) 5 le fog állni a forgalmat

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Megtagadva*) a DNS-kiszolgáló a webhely DNS szolgáltatásban
1. An internet user tries to look up an internal DNS record on DNS01 through the BackEnd001.CloudApp.Net service
2. Nincsenek nyissa meg a DNS-végpontok, mivel ezt a forgalmat a Felhőszolgáltatás nem kellene átadnia, és a kiszolgáló nem érhető el.
3. Ha valamilyen okból a végpontok volt megnyitva, NSG-szabály (Internet, virtuális hálózatok közötti) 5 le fog állni a forgalmat (Megjegyzés: a szabály 1 (DNS) két okból nem alkalmazhatók, először a forrás címe az interneten, ez a szabály csak a helyi virtuális hálózat, mint a forrás vonatkozik is ez a szabály-e egy olyan engedélyezési szabály, ezért soha nem lenne megtagadják a forgalmat)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Megtagadva*) webalkalmazás, SQL-hozzáférés tűzfalon keresztül
1. Az internetes felhasználó SQL adatokat kér FrontEnd001.CloudApp.Net (Internet Facing Felhőszolgáltatás)
2. Végpontleképzőben nincs nyitott meg SQL esetén, mivel ezt a forgalmat a Felhőszolgáltatás nem kellene átadnia, és a tűzfal nem érhető el.
3. Ha valamilyen okból végpontok volt megnyitva, az előtérben levő alhálózathoz megkezdi a bejövő szabály feldolgozása:
   1. NSG-szabály 1 (DNS) nem vonatkoznak, helyezze át a következő szabály
   2. NSG-szabály 2 (RDP) nem vonatkoznak, helyezze át a következő szabály
   3. NSG 3. szabály (IIS01 interneten) a alkalmazni, a forgalom engedélyezett, állítsa le a szabály feldolgozása
4. Forgalom eléri a IIS01 belső IP-címét (10.0.1.5)
5. IIS01 1433-as porton, így nincs válasz a kérésre nem figyel.

## <a name="conclusion"></a>Összegzés
Ebben a példában egy viszonylag egyszerű, és nagyon egyszerű mód a bejövő forgalmat a háttérbeli alhálózat elkülönítése.

További példákat és a hálózati biztonsági határok áttekintését találja [Itt][HOME].

## <a name="references"></a>Referencia
### <a name="main-script-and-network-config"></a>Fő parancsfájlt és a hálózati konfiguráció
Mentse a teljes szkriptet egy PowerShell-parancsfájlt. A hálózati konfiguráció mentése fájlba "NetworkConf1.xml."
Módosítsa a felhasználó által definiált változókat szükséges, és futtassa a szkriptet.

#### <a name="full-script"></a>Teljes szkript
Ez a szkript lesz, a felhasználó által definiált változókat; alapján

1. Csatlakozás Azure-előfizetéshez
2. Tárfiók létrehozása
3. Hozzon létre egy Vnetet és két alhálózatot a hálózati konfigurációs fájlban meghatározott
4. Négy windows server rendszerű virtuális gépek létrehozása
5. Konfigurálja az NSG-t többek között:
   * Hálózati biztonsági csoportok létrehozása
   * Ez a szabályoknak feltöltése
   * Az NSG-t a megfelelő alhálózatokat kötése

Ez a PowerShell-szkript kell futtatni a egy internethez csatlakoztatott számítógépen vagy kiszolgálón.

> [!IMPORTANT]
> Ez a szkript futtatásakor a lehetséges, hogy figyelmeztetéseket vagy más tájékoztató üzeneteket, amely a pop a PowerShellben. Csak hibaüzenetek vörös színnel adhatnak okot.
> 
>

```PowerShell
<# 
 .SYNOPSIS
  Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)

 .DESCRIPTION
  This script will build out a sample DMZ setup containing:
   - A default storage account for VM disks
   - Two new cloud services
   - Two Subnets (FrontEnd and BackEnd subnets)
   - One server on the FrontEnd Subnet
   - Three Servers on the BackEnd Subnet
   - Network Security Groups to allow/deny traffic patterns as declared

  Before running script, ensure the network configuration file is created in
  the directory referenced by $NetworkConfigFile variable (or update the
  variable to reflect the path and file name of the config file being used).

 .Notes
  Security requirements are different for each use case and can be addressed in a
  myriad of ways. Please be sure that any sensitive data or applications are behind
  the appropriate layer(s) of protection. This script serves as an example of some
  of the techniques that can be used, but should not be used for all scenarios. You
  are responsible to assess your security needs and the appropriate protections
  needed, and then effectively implement those protections.

  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
   IIS01      - 10.0.1.5

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

# User-Defined Global Variables
  # These should be changes to reflect your subscription and services
  # Invalid options will fail in the validation section

  # Subscription Access Details
    $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

  # VM Account, Location, and Storage Details
    $LocalAdmin = "theAdmin"
    $DeploymentLocation = "Central US"
    $StorageAccountName = "vmstore02"

  # Service Details
    $FrontEndService = "FrontEnd001"
    $BackEndService = "BackEnd001"

  # Network Details
    $VNetName = "CorpNetwork"
    $FESubnet = "FrontEnd"
    $FEPrefix = "10.0.1.0/24"
    $BESubnet = "BackEnd"
    $BEPrefix = "10.0.2.0/24"
    $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"

  # VM Base Disk Image Details
    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

  # NSG Details
    $NSGName = "MyVNetSG"

# User-Defined VM Specific Configuration
    # Note: To ensure proper NSG Rule creation later in this script:
    #       - The Web Server must be VM 0
    #       - The AppVM1 Server must be VM 1
    #       - The DNS server must be VM 3
    #
    #       Otherwise the NSG rules in the last section of this
    #       script will need to be changed to match the modified
    #       VM array numbers ($i) so the NSG Rule IP addresses
    #       are aligned to the associated VM IP addresses.

    # VM 0 - The Web Server
      $VMName += "IIS01"
      $ServiceName += $FrontEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $FESubnet
      $VMIP += "10.0.1.5"

    # VM 1 - The First Application Server
      $VMName += "AppVM01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.5"

    # VM 2 - The Second Application Server
      $VMName += "AppVM02"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.6"

    # VM 3 - The DNS Server
      $VMName += "DNS01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.4"

# ----------------------------- #
# No User-Defined Variables or  #
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

If (Test-AzureName -Service -Name $FrontEndService) { 
    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

If (Test-AzureName -Service -Name $BackEndService) { 
    Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

If (-Not (Test-Path $NetworkConfigFile)) { 
    Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The network configuration file was found" -ForegroundColor Green
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
    New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
    New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

# Build VMs
    $i=0
    $VMName | Foreach {
        Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
        New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
            Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
            Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
            Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
            Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
            Remove-AzureEndpoint -Name "PowerShell" | `
            New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
            # Note: A Remote Desktop endpoint is automatically created when each VM is created.
        $i++
    }
    # Add HTTP Endpoint for IIS01
    Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM

# Configure NSG
    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

  # Build the NSG
    Write-Host "Building the NSG" -ForegroundColor Cyan
    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

  # Add NSG Rules
    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
        -SourceAddressPrefix Internet -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
        -Protocol *

    # Assign the NSG to the Subnets
        Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

# Optional Post-script Manual Configuration
  # Install Test Web App (Run Post-Build Script on the IIS Server)
  # Install Backend resource (Run Post-Build Script on the AppVM01)
  Write-Host
  Write-Host "Build Complete!" -ForegroundColor Green
  Write-Host
  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
  Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
  Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
  Write-Host
```

#### <a name="network-config-file"></a>Hálózati konfigurációs fájl
Mentse az xml-fájlt a hely frissítve, és adja hozzá ezt a fájlt az előző szkriptben $NetworkConfigFile változó hivatkozásra.

```XML
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

#### <a name="sample-application-scripts"></a>Mintaszkriptek alkalmazás
Ha szeretne egy mintaalkalmazás telepítése ezzel és más DMZ példák, egy, a következő hivatkozás lett megadva: [A Példaszkript alkalmazás][SampleApp]

## <a name="next-steps"></a>További lépések
* Frissítse és mentse az XML-fájl
* A környezet létrehozása a PowerShell-parancsprogram futtatásával
* A mintaalkalmazás telepítése
* A szegélyhálózat (DMZ) keresztül forgalmakat folyamatok tesztelése

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "NSG bejövő Szegélyhálózat"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

