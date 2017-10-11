---
title: "Az Azure DMZ példa – Build az NSG-ket egy egyszerű DMZ |} Microsoft Docs"
description: "A hálózati biztonsági csoportokkal (NSG) DMZ összeállítása"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ec29e6b250f927a3a4a94ffdf83d6c7c0e325722
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>1 – például egy egyszerű DMZ NSG-k használata az Azure Resource Manager-sablon létrehozása
[A biztonsági határ bevált gyakorlatok laphoz való visszatéréshez][HOME]

> [!div class="op_single_selector"]
> * [Resource Manager-sablon](virtual-networks-dmz-nsg.md)
> * [Klasszikus - PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Ebben a példában egy egyszerű DMZ négy Windows-kiszolgálók és hálózati biztonsági csoportokat hoz létre. Ez a példa bemutatja az egyes bemutatják az egyes lépések, így a megfelelő template szakaszokban. Adjon meg egy hogyan forgalom halad keresztül a Szegélyhálózaton lévő védelmi réteget részletesebb lépésenkénti tekintse meg a forgalom forgatókönyv szakasz is van. Végezetül az hivatkozások szakaszban a teljes sablon kódot és az ebben a környezetben, teszteléséhez, és a különböző forgatókönyvekben kísérletezhet összeállítására vonatkozó útmutatást. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![Az NSG bejövő DMZ][1]

## <a name="environment-description"></a>Környezet leírása
Ebben a példában az előfizetés a következőket tartalmazza:

* Egyetlen erőforráscsoportként működnek
* Egy virtuális hálózatot, két alhálózattal; "Előtér" és "Háttér"
* Mindkét alhálózat alkalmazott hálózati biztonsági csoport
* A Windows Server egy webalkalmazás-kiszolgáló ("IIS01") jelölő
* Két windows Server kiszolgálókon, amelyek megfelelnek az alkalmazás háttérkiszolgálók ("AppVM01", "AppVM02")
* A Windows server DNS-kiszolgáló ("DNS01") jelölő
* A webalkalmazás-kiszolgáló egy nyilvános IP-cím

A references szakaszában a környezet ebben a példában leírt épülő Azure Resource Manager sablont kapcsolat van. A virtuális gépek és virtuális hálózatok, bár végezhető el a példa sablon dokumentum nem ismerteti a jelen dokumentum részletesen. 

**Ebben a környezetben összeállításához** (részletes utasításokra van szüksége van a references szakaszában, a jelen dokumentum);

1. Az Azure Resource Manager sablon telepítése: [Azure gyors üzembe helyezés sablonok][Template]
2. A következő mintaalkalmazás telepítése: [minta alkalmazás-parancsfájl][SampleApp]

>[!NOTE]
>Távoli asztali ezt a példányt a háttér-kiszolgálók az IIS-kiszolgálón használt egy "ugrást mező." Az IIS-kiszolgálót, majd az IIS kiszolgáló RDP a háttér-kiszolgálóhoz az első RDP. Másik lehetőség egy nyilvános IP-cím társítható minden kiszolgáló egyszerűbb az RDP a hálózati Adapternek.
> 
>

A következő szakaszokban a hálózati biztonsági csoport, és hogyan működik az ebben a példában az Azure Resource Manager sablon sorok keresztül érdekében részletes leírását.

## <a name="network-security-groups-nsg"></a>Hálózati biztonsági csoportok (NSG)
Az ebben a példában egy NSG-csoport összeállítása és hat szabályokkal majd betölteni. 

>[!TIP]
>Általánosságban véve az "Engedélyezés" speciális szabályok először hozzon létre, és majd a általánosabbá "Deny" szabályok utolsó. A prioritási megkövetel amelyek szabályokat első értékeli ki. Forgalom kiderül, hogy egy adott szabály vonatkozik, ha nincsenek további szabályok kiértékelése. NSG-szabályok egyaránt (az alhálózati szempontjából) a bejövő vagy kimenő irányban is alkalmazhat.
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

Nincs olyan alapértelmezett kimenő szabály, amely lehetővé teszi, hogy a kimenő forgalom az internethez. Ebben a példában a Microsoft most átengedi a kimenő forgalmat, és nem módosítja az egyetlen kimenő szabályok. Szeretné alkalmazni a forgalom mindkét irányban biztonsági szabályzatot, felhasználói definiált útválasztási szükséges, és a"3" a felfedezte van a [biztonsági határ bevált gyakorlatok lap][HOME].

Minden egyes szabály az alábbiak szerint tárgyalja részletesen:

1. A hálózati biztonsági csoport erőforrás kell példányosítani ahhoz, hogy a szabályok:

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

2. Ebben a példában az első szabály lehetővé teszi, hogy a DNS-forgalom a DNS-kiszolgálót a backend alhálózathoz az összes belső hálózatok között. A szabály néhány fontos paraméterekkel rendelkezik:
  * "destinationAddressPrefix" - szabályok használhatja az "alapértelmezett címke" nevű címelőtag különleges típusú, és ezekkel a címkékkel rendszer által biztosított azonosítók, amelyek lehetővé teszik a címelőtag egy nagyobb kategóriáját egyszerűen. Ez a szabály az alapértelmezett címke "Internet" használja a virtuális hálózaton kívül bármely cím jelölésére. Más előtag feliratai VirtualNetwork és AzureLoadBalancer.
  * "Irány" azt jelzi, hogy a forgalom iránya a szabály lép érvénybe. Irányát (attól függően, ahol az NSG kötött) az alhálózatra vagy a virtuális gép szempontjából van. Így ha irány "Bejövő" és a forgalom írja be az alhálózat, a szabályt alkalmazni és az alhálózatot elhagyó forgalomra ne befolyásolja az e szabály által.
  * "Prioritás" állítja be, amelyben a forgalom áramlását kiértékelésének. Minél kisebb számot annál magasabb a prioritás. A szabály vonatkozik egy adott adatforgalmat, ha nincsenek további szabályok feldolgozása. Így ha 1 prioritású szabály lehetővé teszi, hogy a forgalmat, és 2 prioritású szabály megtagadja a forgalmat, és szabályokat is vonatkoznak majd a forgalmat szeretné engedélyezni szeretné a flow (mivel szabály 1 kellett magasabb prioritású hatás tartott, és nincsenek további szabályok alkalmazása megtörtént).
  * "Access" jelzi, hogy a szabály által érintett forgalom-e letiltott ("Deny") vagy engedélyezett ("Engedélyezés").

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

3. Ez a szabály lehetővé teszi, hogy az internetről érkező RDP-portjára kötött alhálózaton egyetlen kiszolgálón történő flow RDP-forgalmát. 

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

4. Ez a szabály lehetővé teszi, hogy a webalkalmazás-kiszolgáló elérte a bejövő forgalmat. Ez a szabály nem változtatja meg az útválasztási viselkedés. A szabály csak lehetővé teszi, hogy a IIS01 továbbítani adatforgalmat. Így ha az internetről érkező forgalom volt-e a célként, ez a szabály akkor engedélyezi-e, és további szabályok feldolgozásának leállítása a webkiszolgálón. (A szabályban prioritással 140 összes egyéb bejövő forgalmat le van tiltva). Ha csak a HTTP-forgalom éppen feldolgozás, ez a szabály sikertelen további korlátozva csak engedélyezi a 80-as Port cél.

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

5. Ez a szabály lehetővé teszi, hogy a forgalom a IIS01 kiszolgáló számára a AppVM01 kiszolgáló egy újabb szabály blokkolja a háttér-forgalom minden más előtér. Ez a szabály akkor javíthatja, ha a port ismert, hogy hozzá kell adni. Például, ha az IIS-kiszolgálón van elérte-e a AppVM01 csak SQL Server, a Célporttartomány módosítani kell a "*" (minden) 1433 (az SQL-port), így a kisebb bejövő támadási felületét AppVM01 kell a webalkalmazás legalább egyszer utaló jeleket.

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

6. Ez a szabály az internetről érkező forgalom megtagadja a hálózaton lévő kiszolgálók számára. A 110-es és 120 prioritással szabályokat a hatás hoz csak a bejövő internet-kezelési forgalom engedélyezése a tűzfal és a kiszolgálókon és blokkok RDP-portok minden más. Ez a szabály szerepel egy "hibamentes" szabályt, amely minden váratlan forgalom blokkolása.

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

7. A végső szabály pedig megtagadja forgalom a Frontend alhálózatból való a Backend alhálózathoz. Mivel ez a szabály egy bejövő forgalomra vonatkozó csak szabály, akkor (érkező a Háttéralkalmazását az az előtérbeli) egy fordított forgalom nem engedélyezett.

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
#### <a name="allowed-internet-to-web-server"></a>(*Engedélyezett*) webkiszolgálón Internet
1. Az internetes felhasználó egy HTTP-lap kér a nyilvános IP-címet a hálózati adapter IIS01 társított hálózati adapter
2. A nyilvános IP-cím forgalmat továbbítja a VNet IIS01 felé (webkiszolgáló)
3. Frontend alhálózathoz bejövő szabály feldolgozása kezdődik:
  1. NSG szabály 1 (DNS) nem teljesül, a következő szabály áthelyezése
  2. NSG szabály 2 (RDP) nem teljesül, a következő szabály áthelyezése
  3. NSG 3. szabály (IIS01 interneten) alkalmazza, akkor engedélyezett, befejezése szabály feldolgozása
4. Forgalom találatok száma a belső kiszolgáló IP-címét a webes IIS01 (10.0.1.5)
5. IIS01 figyeli a webes forgalom, ezt a kérelmet kap, és elindítja a kérés feldolgozása
6. IIS01 az SQL Server a AppVM01 adatokat kéri
7. Nincs kimenő szabályok Frontend alhálózaton, forgalom engedélyezve van
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
13. Nincsenek kimenő szabályok Frontend alhálózaton található, mert a választ kap, és az internetes felhasználói kap, a kért weblap.

#### <a name="allowed-rdp-to-iis-server"></a>(*Engedélyezett*) RDP IIS-kiszolgálóra
1. Egy kiszolgálói rendszergazda interneten igényel a nyilvános IP-címhez a IIS01 hálózati adapter (a nyilvános IP-cím található a portál vagy a PowerShell használatával) társított hálózati adapter IIS01 az RDP-munkamenetet
2. A nyilvános IP-cím forgalmat továbbítja a VNet IIS01 felé (webkiszolgáló)
3. Frontend alhálózathoz bejövő szabály feldolgozása kezdődik:
  1. NSG szabály 1 (DNS) nem teljesül, a következő szabály áthelyezése
  2. NSG szabály 2 (RDP) alkalmazza, akkor engedélyezett, befejezése szabály feldolgozása
4. A nem kimenő szabályokat alapértelmezett szabályokat alkalmazni, és a forgalom engedélyezve van
5. RDP-munkamenetbe engedélyezve van
6. A felhasználónevet és jelszót kér IIS01

>[!NOTE]
>Távoli asztali ezt a példányt a háttér-kiszolgálók az IIS-kiszolgálón használt egy "ugrást mező." Az IIS-kiszolgálót, majd az IIS kiszolgáló RDP a háttér-kiszolgálóhoz az első RDP.
>
>

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

#### <a name="denied-rdp-to-backend"></a>(*Megtagadva*) RDP háttérrendszeréhez
1. Az internetes felhasználó megpróbálja távoli asztali kiszolgáló AppVM01
2. Mivel nincs a kiszolgálók hálózati társított nyilvános IP-címek, ez az adatforgalom soha ne adjon meg a virtuális hálózat, és így elérni a kiszolgáló
3. Azonban valamilyen okból engedélyezve volt a nyilvános IP-címnek, 2 (RDP) NSG-szabály lehetővé teszi az ilyen típusú adatforgalom

>[!NOTE]
>Távoli asztali ezt a példányt a háttér-kiszolgálók az IIS-kiszolgálón használt egy "ugrást mező." Az IIS-kiszolgálót, majd az IIS kiszolgáló RDP a háttér-kiszolgálóhoz az első RDP.
>
>

#### <a name="denied-web-to-backend-server"></a>(*Megtagadva*) háttérkiszolgálóra webes
1. Az internetes felhasználó megpróbál hozzáférni az AppVM01 fájlba
2. Mivel nincs a kiszolgálók hálózati társított nyilvános IP-címek, ez az adatforgalom soha ne adjon meg a virtuális hálózat, és így elérni a kiszolgáló
3. Valamilyen okból engedélyezve volt a nyilvános IP-címnek, NSG szabály 5 (Internet virtuális hálózatba) blokkolná-e a forgalom

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Megtagadva*) DNS-kiszolgálón a webes DNS szolgáltatásban
1. Az internetes felhasználó megpróbálja kereshet meg egy belső DNS-rekordot a DNS01
2. Mivel nincs a kiszolgálók hálózati társított nyilvános IP-címek, ez az adatforgalom soha ne adjon meg a virtuális hálózat, és így elérni a kiszolgáló
3. Valamilyen okból engedélyezve volt a nyilvános IP-címnek, NSG szabály 5 (Internet virtuális hálózatba) blokkolná-e a forgalom (Megjegyzés:, hogy szabály 1 (DNS) szeretné alkalmazni, mert a kérelem forráscím az internethez, és 1. szabály csak a helyi virtuális hálózat, mint a forrás vonatkozik)

#### <a name="denied-sql-access-on-the-web-server"></a>(*Megtagadva*) SQL-hozzáférés a webkiszolgálón
1. Az internetes felhasználó SQL adatokat kér IIS01
2. Mivel nincs a kiszolgálók hálózati társított nyilvános IP-címek, ez az adatforgalom soha ne adjon meg a virtuális hálózat, és így elérni a kiszolgáló
3. Ha egy nyilvános IP-cím valamilyen okból engedélyezve lett, a Frontend alhálózathoz kezdődik bejövő szabály feldolgozása:
  1. NSG szabály 1 (DNS) nem teljesül, a következő szabály áthelyezése
  2. NSG szabály 2 (RDP) nem teljesül, a következő szabály áthelyezése
  3. NSG 3. szabály (IIS01 interneten) alkalmazza, akkor engedélyezett, befejezése szabály feldolgozása
4. Forgalom találatok a IIS01 belső IP-címét (10.0.1.5)
5. IIS01 1433-as portot, ezért nem kérelemre adott válasz nem figyel.

## <a name="conclusion"></a>Összegzés
Ez a példa egy viszonylag egyszerű és egyszerű mód a a háttér-alhálózathoz, a bejövő forgalom elkülönítésére.

További példákat és a hálózati biztonsági határokat egy áttekintést talál [Itt][HOME].

## <a name="references"></a>Referencia
### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Ez a példa egy előre meghatározott Azure Resource Manager-sablon használja a Microsoft által kezelt GitHub-tárházban, és a Közösség. Ez a sablon telepített rögtön kívül a github webhelyen, vagy letölthető, és szükség szerint módosítva. 

A fő sablon megtalálható-e az "azuredeploy.json." nevű fájlt. Ez a sablon küldheti el PowerShell vagy a parancssori felületen keresztül (fájllal a társított "azuredeploy.parameters.json") a sablon telepítéséhez. A legegyszerűbb módja a használja a "Központi telepítése az Azure-bA" gombot a README.md lap a github webhelyen található.

A sablon épülő ebben a példában a Githubról, majd az Azure-portálon történő üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Egy böngészőből keresse meg a [sablon][Template]
2. Kattintson a "Központi telepítése az Azure-bA" (vagy a "Megjelenítés" gombra a sablon grafikus ábrázolása)
3. A Storage-fiók, felhasználónév és jelszó a (paraméterek) panelen adja meg, majd kattintson a **OK**
5. Hozzon létre egy erőforráscsoportot a központi telepítés (egy meglévőt is használhat, de egy újat a legjobb eredmények elérése érdekében javasolt)
6. Szükség esetén a virtuális hálózat az egyes előfizetésekhez és helyekhez beállításainak módosítása.
7. Kattintson a **tekintse át a jogi feltételeket**, olvassa el a feltételeket, és kattintson a **beszerzési** gombra kattintva elfogadja.
8. Kattintson a **létrehozása** a sablon telepítésének megkezdéséhez.
9. Miután a telepítés sikeresen befejeződött, nyissa meg az erőforráscsoport létrehozásánál ehhez a központi telepítéshez a konfigurált belüli erőforrások megjelenítéséhez.

>[!NOTE]
>Ez a sablon lehetővé teszi, hogy csak a IIS01 kiszolgálóra RDP (Keresés a nyilvános IP-cím a IIS01 a portálon). Távoli asztali ezt a példányt a háttér-kiszolgálók az IIS-kiszolgálón használt egy "ugrást mező." Az IIS-kiszolgálót, majd az IIS kiszolgáló RDP a háttér-kiszolgálóhoz az első RDP.
>
>

Távolítsa el a telepítést, törölje a csoportot, és minden gyermek-erőforrás is törlődik.

#### <a name="sample-application-scripts"></a>Alkalmazás mintaparancsfájlok
A sablon sikeres futtatása után állíthat be a webkiszolgáló és egy egyszerű webes alkalmazás tesztelése a DMZ-konfiguráció engedélyezése az alkalmazások kiszolgálói. Ez, és más DMZ példák mintaalkalmazás telepítéséhez egy adtak meg, az alábbi hivatkozásra: [minta alkalmazás-parancsfájl][SampleApp]

## <a name="next-steps"></a>Következő lépések

* Ez a példa központi telepítése
* A minta-alkalmazás létrehozása
* A DMZ keresztül különböző forgalom tesztelése

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "Az NSG bejövő DMZ"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md