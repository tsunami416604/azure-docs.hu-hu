---
title: Az Azure DMZ példa – Build az NSG-ket egy egyszerű DMZ |} Microsoft Docs
description: A hálózati biztonsági csoportokkal (NSG) DMZ összeállítása
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
ms.openlocfilehash: ed172d552e1e4c9ee27c58abcd7ad2d98df21579
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
ms.locfileid: "23928887"
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-classic-powershell"></a>1 – például egy egyszerű DMZ NSG-k használata a klasszikus PowerShell összeállítása
[A biztonsági határ bevált gyakorlatok laphoz való visszatéréshez][HOME]

> [!div class="op_single_selector"]
> * [Resource Manager-sablon](virtual-networks-dmz-nsg.md)
> * [Klasszikus - PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Ebben a példában egy egyszerű DMZ négy Windows-kiszolgálók és hálózati biztonsági csoportokat hoz létre. Ez a példa bemutatja az egyes bemutatják az egyes lépések, így a megfelelő PowerShell-parancsokat. Is van a forgalom forgatókönyv részt, hogy egy részletesebb lépésenkénti hogyan forgalom halad keresztül a Szegélyhálózaton lévő védelmi réteget. Végezetül a hivatkozások szakasz: a teljes kód látható, míg utasítás ebben a környezetben, teszteléséhez, és a különböző forgatókönyvekben kísérletezhet létrehozásához. 

![Az NSG bejövő DMZ][1]

## <a name="environment-description"></a>Környezet leírása
Ebben a példában az előfizetés a következőket tartalmazza:

* A felhőalapú szolgáltatások két: "FrontEnd001" és "BackEnd001"
* A virtuális hálózat, a "CorpNetwork", a két alhálózat; "Előtér" és "Háttér"
* Mindkét alhálózat alkalmazott hálózati biztonsági csoport
* A Windows Server egy webalkalmazás-kiszolgáló ("IIS01") jelölő
* Két windows Server kiszolgálókon, amelyek megfelelnek az alkalmazás háttérkiszolgálók ("AppVM01", "AppVM02")
* A Windows server DNS-kiszolgáló ("DNS01") jelölő

A references szakaszában egy PowerShell-parancsfájlt az ebben a példában leírt környezetben a legtöbb épülő van. A virtuális gépek és virtuális hálózatok, bár a példaként megadott parancsfájlt, által végzett dokumentum nem ismerteti a jelen dokumentum részletesen. 

A környezet; létrehozásához.

1. Mentse a hálózati konfigurációs XML-fájlt a references szakaszában szereplő (frissítődik nevét, helyét és IP-címeket az adott forgatókönyv esetén)
2. A felhasználói változók a parancsfájl felel meg a parancsfájl-hoz (előfizetések, service nevét stb.) kell futtatni a környezet frissítése
3. A parancsfájl végrehajtása a PowerShellben

>[!Note]
>A régió, a PowerShell-parancsfájl esetében meg kell egyeznie a hálózati konfigurációs XML-fájl esetében.
>
>

Miután a parancsfájl futtatása sikeresen további választható lépéseket lehet tenni, és a references szakaszában két parancsfájlok állíthat be a webkiszolgáló és egy egyszerű webes alkalmazás tesztelése a DMZ-konfiguráció engedélyezése az alkalmazások kiszolgálói.

A következő szakaszokban részletes leírása a hálózati biztonsági csoportok és működésének ehhez a példához a PowerShell parancsfájl sorok keresztül érdekében.

## <a name="network-security-groups-nsg"></a>Hálózati biztonsági csoportok (NSG)
Az ebben a példában egy NSG-csoport összeállítása és hat szabályokkal majd betölteni. 

> [!TIP]
> Általánosságban véve az "Engedélyezés" speciális szabályok először hozzon létre, és majd a általánosabbá "Deny" szabályok utolsó. A prioritási megkövetel amelyek szabályokat első értékeli ki. Forgalom kiderül, hogy egy adott szabály vonatkozik, ha nincsenek további szabályok kiértékelése. NSG-szabályok egyaránt (az alhálózati szempontjából) a bejövő vagy kimenő irányban is alkalmazhat.
> 
> 

Deklaratív módon a bejövő forgalom beépített folyamatban a következő szabályokat:

1. Belső DNS-forgalom (53-as port) engedélyezve van
2. RDP-forgalmát (3389-es port) az internetről bármely virtuális géphez engedélyezett
3. HTTP-forgalom (80-as port) az internetről (IIS01) webkiszolgálón engedélyezett
4. Minden forgalmat (minden porthoz) IIS01 AppVM1 engedélyezett
5. Az összes bejövő forgalom (minden porthoz) az internetről a teljes virtuális hálózatot (alhálózatok mindkét) megtagadva
6. Az összes bejövő forgalom (minden porthoz) a Frontend alhálózatból a Backend alhálózathoz megtagadva

A következő szabályok kötve alhálózatok, ha a webkiszolgálónak, a szabályokat is 3 az internetről bejövő HTTP-kérelem (engedélyezése) és 5 (megtagadni) szeretné alkalmazni, de mivel a szabály 3 egy magasabb prioritással bír, csak azt csak akkor vonatkozik, és 5 szabály nem lesz play kerülhet. Így a HTTP-kérelem szeretné engedélyezni kell a webkiszolgálón. Ha ugyanaz a forgalom próbál elérni az DNS01 kiszolgálót, szabály 5 (Megtagadás) lenne az első alkalmazni, és szeretné, hogy a kiszolgáló nem engedélyezett a forgalmat. 6 (Megtagadás) szabály blokkolja a Frontend alhálózathoz van szó, a Backend alhálózathoz (kivéve a engedélyezett forgalom szabályokban 1 és 4) a, a szabálykészlet abban az esetben, ha egy támadó biztonság sérüléseinek előtér, a támadó a webalkalmazás volna korlátozott védelmet nyújt a háttér hálózathoz a háttérrendszer elérésére "védett" hálózaton (csak a AppVM01 kiszolgálón kitett erőforrások).

Nincs olyan alapértelmezett kimenő szabály, amely lehetővé teszi, hogy a kimenő forgalom az internethez. Ebben a példában a Microsoft most átengedi a kimenő forgalmat, és nem módosítja az egyetlen kimenő szabályok. Forgalom mindkét irányban zárolását, felhasználói definiált útválasztási szükség, és a"3" a felfedezte van a [biztonsági határ bevált gyakorlatok lap][HOME].

Minden egyes szabály tárgyalt részletesebben az alábbiak szerint (**Megjegyzés**: valamely elemére a következő lista-tól kezdődően dollárjelet (például: $NSGName) egy felhasználó által definiált változó a hivatkozás szakaszban, a jelen dokumentum a parancsfájlból):

1. Hálózati biztonsági csoport először kell kialakítani, hogy a szabályok tárolására:

    ```PowerShell
    New-AzureNetworkSecurityGroup -Name $NSGName `
        -Location $DeploymentLocation `
        -Label "Security group for $VNetName subnets in $DeploymentLocation"
    ```

2. Ebben a példában az első szabály lehetővé teszi, hogy a DNS-forgalom a DNS-kiszolgálót a backend alhálózathoz az összes belső hálózatok között. A szabály néhány fontos paraméterekkel rendelkezik:
   
   * "Típus" azt jelzi, hogy a forgalom iránya a szabály lép érvénybe. Irányát (attól függően, ahol az NSG kötött) az alhálózatra vagy a virtuális gép szempontjából van. Így ha típus: "Bejövő" és a forgalom írja be az alhálózat, a szabályt alkalmazni és az alhálózatot elhagyó forgalomra ne befolyásolja az e szabály által.
   * "Prioritás" állítja be, amelyben a forgalom áramlását kiértékelésének. Minél kisebb számot annál magasabb a prioritás. A szabály vonatkozik egy adott adatforgalmat, ha nincsenek további szabályok feldolgozása. Így ha 1 prioritású szabály lehetővé teszi, hogy a forgalmat, és 2 prioritású szabály megtagadja a forgalmat, és szabályokat is vonatkoznak majd a forgalmat szeretné engedélyezni szeretné a flow (mivel szabály 1 kellett magasabb prioritású hatás tartott, és nincsenek további szabályok alkalmazása megtörtént).
   * "Action" azt jelzi, hogy ha a szabály által érintett forgalom vagy tiltott.

    ```PowerShell    
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
        -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[4] `
        -DestinationPortRange '53' `
        -Protocol *
    ```

3. Ez a szabály lehetővé teszi, hogy az internetről érkező RDP-portjára kötött alhálózaton egyetlen kiszolgálón történő flow RDP-forgalmát. Ez a szabály címelőtagokat; különleges kétféle használ. "VIRTUAL_NETWORK" és "INTERNET". Ezekkel a címkékkel egyszerűen egy nagyobb kategóriáját címelőtag.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
         -Type Inbound -Priority 110 -Action Allow `
         -SourceAddressPrefix INTERNET -SourcePortRange '*' `
         -DestinationAddressPrefix VIRTUAL_NETWORK `
         -DestinationPortRange '3389' `
         -Protocol *
    ```

4. Ez a szabály lehetővé teszi, hogy a webalkalmazás-kiszolgáló elérte a bejövő forgalmat. Ez a szabály nem változtatja meg az útválasztási viselkedés. A szabály csak lehetővé teszi, hogy a IIS01 továbbítani adatforgalmat. Így ha az internetről érkező forgalom volt-e a célként, ez a szabály akkor engedélyezi-e, és további szabályok feldolgozásának leállítása a webkiszolgálón. (A szabályban prioritással 140 összes egyéb bejövő forgalmat le van tiltva). Ha csak a HTTP-forgalom éppen feldolgozás, ez a szabály sikertelen további korlátozva csak engedélyezi a 80-as Port cél.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
         -Type Inbound -Priority 120 -Action Allow `
         -SourceAddressPrefix Internet -SourcePortRange '*' `
         -DestinationAddressPrefix $VMIP[0] `
         -DestinationPortRange '*' `
         -Protocol *
    ```

5. Ez a szabály lehetővé teszi, hogy a forgalom a IIS01 kiszolgáló számára a AppVM01 kiszolgáló egy újabb szabály blokkolja a háttér-forgalom minden más előtér. Ez a szabály akkor javíthatja, ha a port ismert, hogy hozzá kell adni. Például, ha az IIS-kiszolgálón van elérte-e a AppVM01 csak SQL Server, a Célporttartomány módosítani kell a "*" (minden) 1433 (az SQL-port), így a kisebb bejövő támadási felületét AppVM01 kell a webalkalmazás legalább egyszer utaló jeleket.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
        -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
    ```

6. Ez a szabály az internetről érkező forgalom megtagadja a hálózaton lévő kiszolgálók számára. A 110-es és 120 prioritással szabályokat a hatás hoz csak a bejövő internet-kezelési forgalom engedélyezése a tűzfal és a kiszolgálókon és blokkok RDP-portok minden más. Ez a szabály szerepel egy "hibamentes" szabályt, amely minden váratlan forgalom blokkolása.
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
7. A végső szabály pedig megtagadja forgalom a Frontend alhálózatból való a Backend alhálózathoz. Mivel ez a szabály egy bejövő forgalomra vonatkozó csak szabály, akkor (érkező a Háttéralkalmazását az az előtérbeli) egy fordított forgalom nem engedélyezett.

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
#### <a name="allowed-internet-to-web-server"></a>(*Engedélyezett*) webkiszolgálón Internet
1. Az internetes felhasználó egy HTTP-lap kér FrontEnd001.CloudApp.Net (Internet Facing Felhőszolgáltatás)
2. Cloud service fázisok forgalom nyitott végpontok felé IIS01 80-as porton keresztül (webkiszolgáló)
3. Frontend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. NSG szabály 1 (DNS) nem teljesül, a következő szabály áthelyezése
   2. NSG szabály 2 (RDP) nem teljesül, a következő szabály áthelyezése
   3. NSG 3. szabály (IIS01 interneten) alkalmazza, akkor engedélyezett, befejezése szabály feldolgozása
4. Forgalom találatok száma a belső kiszolgáló IP-címét a webes IIS01 (10.0.1.5)
5. IIS01 figyeli a webes forgalom, ezt a kérelmet kap, és elindítja a kérés feldolgozása
6. IIS01 az SQL Server a AppVM01 adatokat kéri
7. Nincsenek kimenő szabályok a Frontend alhálózathoz, mert a forgalom engedélyezve van-e
8. A Backend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. NSG szabály 1 (DNS) nem teljesül, a következő szabály áthelyezése
   2. NSG szabály 2 (RDP) nem teljesül, a következő szabály áthelyezése
   3. NSG 3. szabály (Internet tűzfalhoz) nem teljesül, a közvetkező szabályának áthelyezése
   4. NSG 4. szabály (a AppVM01 IIS01) teljesül, a forgalom engedélyezve van, akkor állítsa le a szabály feldolgozása
9. AppVM01 dokumentálásáért és az SQL-lekérdezést kap
10. Mivel a Backend alhálózathoz nem kimenő szabályok, engedélyezett-e a válasz
11. Frontend alhálózathoz bejövő szabály feldolgozása kezdődik:
    1. Nincs érvényes bejövő szabály NSG a Frontend alhálózathoz, így nincs az NSG-szabályok vonatkoznak a Backend alhálózathoz-forgalom
    2. A alapértelmezett rendszerszintű szabály, amely lehetővé teszi az alhálózatok közötti forgalmat lehetővé tenné a forgalmat, a forgalom engedélyezve van.
12. Az IIS-kiszolgálót az SQL-válasz fogadása és a HTTP-válasz befejezése és a kérelmező küld
13. Mivel nincsenek kimenő szabályok a válasz a Frontend alhálózathoz van engedélyezve, és az interneten a felhasználó kap a kért weblap.

#### <a name="allowed-rdp-to-backend"></a>(*Engedélyezett*) RDP háttérrendszeréhez
1. Server Admin interneten kérelmek AppVM01 BackEnd001.CloudApp.Net:xxxxx, ahol xxxxx az RDP számára (a hozzárendelt port található az Azure portálon vagy a PowerShell segítségével) AppVM01 véletlenszerűen hozzárendelt portszámot az RDP-munkamenetet
2. Backend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. NSG szabály 1 (DNS) nem teljesül, a következő szabály áthelyezése
   2. NSG szabály 2 (RDP) alkalmazza, akkor engedélyezett, befejezése szabály feldolgozása
3. A nem kimenő szabályokat alapértelmezett szabályokat alkalmazni, és a forgalom engedélyezve van
4. RDP-munkamenetbe engedélyezve van
5. A felhasználónevet és jelszót kér AppVM01

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Engedélyezett*) a DNS-kiszolgáló Web server a DNS szolgáltatásban
1. Webalkalmazás-kiszolgálón, IIS01, egy adatcsatorna www.data.gov: igényeinek, de a címek feloldására igényeinek.
2. A hálózati konfigurációt a VNet listák DNS01 (a háttér alhálózaton 10.0.2.4) elsődleges DNS-kiszolgálóként, IIS01 küld a DNS-kérelem DNS01
3. Nincs kimenő szabályok Frontend alhálózaton, forgalom engedélyezve van
4. Backend alhálózathoz bejövő szabály feldolgozása kezdődik:
   * NSG szabály 1 (DNS) alkalmazza, akkor engedélyezett, befejezése szabály feldolgozása
5. DNS-kiszolgáló a kérelmet kap.
6. DNS-kiszolgáló nem rendelkezik a gyorsítótárazott címmel és egy legfelső szintű DNS-kiszolgáló kéri az interneten
7. Nincs kimenő szabályok a Backend alhálózathoz forgalom engedélyezve van
8. Internetes DNS-kiszolgáló válaszol, mivel ehhez a munkamenethez belső kezdeményezett, a válasz engedélyezett
9. DNS-kiszolgáló gyorsítótárazza a választ, és a kezdeti kérés vissza IIS01 válaszol
10. Nincs kimenő szabályok a Backend alhálózathoz forgalom engedélyezve van
11. Frontend alhálózathoz bejövő szabály feldolgozása kezdődik:
    1. Nincs érvényes bejövő szabály NSG a Frontend alhálózathoz, így nincs az NSG-szabályok vonatkoznak a Backend alhálózathoz-forgalom
    2. A alapértelmezett rendszerszintű szabály, amely lehetővé teszi az alhálózatok közötti forgalmat lehetővé tenné a forgalmat, így a forgalom engedélyezve van
12. IIS01 megkapja válaszát DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Engedélyezett*) server-hozzáférés fájl AppVM01
1. IIS01 kér AppVM01 fájlba
2. Nincs kimenő szabályok Frontend alhálózaton, forgalom engedélyezve van
3. A Backend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. NSG szabály 1 (DNS) nem teljesül, a következő szabály áthelyezése
   2. NSG szabály 2 (RDP) nem teljesül, a következő szabály áthelyezése
   3. NSG 3. szabály (IIS01 interneten) nem teljesül, a következő szabály áthelyezése
   4. NSG 4. szabály (a AppVM01 IIS01) teljesül, a forgalom engedélyezve van, akkor állítsa le a szabály feldolgozása
4. AppVM01 a kérelmet kap, és válaszol, a fájl (feltéve, hogy van engedélyezve)
5. Mivel a Backend alhálózathoz nem kimenő szabályok, engedélyezett-e a válasz
6. Frontend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. Nincs érvényes bejövő szabály NSG a Frontend alhálózathoz, így nincs az NSG-szabályok vonatkoznak a Backend alhálózathoz-forgalom
   2. A alapértelmezett rendszerszintű szabály, amely lehetővé teszi az alhálózatok közötti forgalmat lehetővé tenné a forgalmat, a forgalom engedélyezve van.
7. Az IIS-kiszolgálót kap a fájl

#### <a name="denied-web-to-backend-server"></a>(*Megtagadva*) háttérkiszolgálóra webes
1. Az internetes felhasználó megpróbál hozzáférni az AppVM01 fájlba a BackEnd001.CloudApp.Net szolgáltatáson keresztül
2. Mivel nincsenek nyissa meg a fájlmegosztás nincsenek végpontok, a forgalom nem lenne továbbítja a felhőalapú szolgáltatás, és így elérni a kiszolgálót
3. Ha valamilyen okból a végpontok nyitott, NSG szabály 5 (Internet virtuális hálózatba) blokkolná-e a forgalom

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Megtagadva*) DNS-kiszolgálón a webes DNS szolgáltatásban
1. Az internetes felhasználó megpróbálja kereshet meg egy belső DNS-rekordot a DNS01 a BackEnd001.CloudApp.Net szolgáltatáson keresztül
2. Mivel nincsenek végpontok nyissa meg a DNS, a forgalom nem lenne továbbítja a felhőalapú szolgáltatás, és így elérni a kiszolgálót
3. Ha valamilyen okból a végpontok nyitott, NSG szabály 5 (Internet virtuális hálózatba) blokkolná-e a forgalom (Megjegyzés: csak akkor nem vonatkozik két okból szabály 1 (DNS), először a forrás címe az interneten, ez a szabály csak a helyi virtuális hálózat, mint a forrás vonatkozik Ez a szabály a rendszer egy olyan engedélyezési szabály, akkor soha nem letiltsa a forgalom)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Megtagadva*) webes SQL-hozzáférés tűzfalon keresztül
1. Az internetes felhasználó SQL adatokat kér FrontEnd001.CloudApp.Net (Internet Facing Felhőszolgáltatás)
2. Mivel nincsenek végpontok nyissa meg az SQL, a forgalom nem lenne továbbítja a felhőalapú szolgáltatás, és a tűzfal nem tudnák elérni
3. Ha valamilyen okból végpontok voltak nyitva, a Frontend alhálózathoz megkezdi a bejövő szabály feldolgozása:
   1. NSG szabály 1 (DNS) nem teljesül, a következő szabály áthelyezése
   2. NSG szabály 2 (RDP) nem teljesül, a következő szabály áthelyezése
   3. NSG 3. szabály (IIS01 interneten) alkalmazza, akkor engedélyezett, befejezése szabály feldolgozása
4. Forgalom találatok a IIS01 belső IP-címét (10.0.1.5)
5. IIS01 1433-as portot, ezért nem kérelemre adott válasz nem figyel.

## <a name="conclusion"></a>Összegzés
Ez a példa egy viszonylag egyszerű és egyszerű mód a a háttér-alhálózathoz, a bejövő forgalom elkülönítésére.

További példákat és a hálózati biztonsági határokat egy áttekintést talál [Itt][HOME].

## <a name="references"></a>Referencia
### <a name="main-script-and-network-config"></a>Fő parancsfájlt és a hálózati konfiguráció
A teljes parancsfájl menthető egy olyan PowerShell-parancsfájlt. A hálózati konfiguráció mentése fájlba "NetworkConf1.xml."
A felhasználói változók szükség szerinti módosításához, és futtassa a parancsfájlt.

#### <a name="full-script"></a>Teljes szkript
Ez a parancsfájl lesz, a felhasználó által definiált változókat; alapján

1. Csatlakozás Azure-előfizetéshez
2. Create a storage account
3. Hozzon létre egy virtuális hálózat és a hálózati konfigurációs fájljában definiált két alhálózat
4. Négy windows server virtuális gépek létrehozása
5. Adja meg, beleértve az NSG:
   * Az NSG létrehozása
   * Azt a szabályoknak feltöltése
   * Az NSG kötése a megfelelő alhálózatokat

A PowerShell parancsfájl fusson helyben egy internethez csatlakoztatott PC vagy a kiszolgáló.

> [!IMPORTANT]
> Ezt a parancsfájlt, amikor előfordulhat figyelmeztetések vagy a PowerShellben pop más tájékoztató üzeneteit. Piros csak hibaüzenetek aggodalomra.
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
Az XML-fájl mentése frissített hellyel rendelkező, és ehhez a fájlhoz, a fenti parancsfájlban a $NetworkConfigFile változóhoz a hivatkozás hozzáadása.

```XML
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

#### <a name="sample-application-scripts"></a>Alkalmazás mintaparancsfájlok
Szeretne telepíteni egy mintaalkalmazás ezzel és más DMZ példák, ha egy adtak meg, a következő hivatkozásra: [minta alkalmazás-parancsfájl][SampleApp]

## <a name="next-steps"></a>További lépések
* Frissítse és mentse az XML-fájl
* A környezet létrehozása a PowerShell-parancsprogram futtatása
* A mintaalkalmazás telepítése
* A DMZ keresztül különböző forgalom tesztelése

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Az NSG bejövő DMZ"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

