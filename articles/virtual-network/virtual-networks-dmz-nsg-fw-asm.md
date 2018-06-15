---
title: DMZ példa – összeállítása a tűzfal és az NSG-alkalmazások védelmét DMZ |} Microsoft Docs
description: A tűzfal és a hálózati biztonsági csoportokkal (NSG) DMZ összeállítása
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: cc0e8a3fa749eb2e6f65ef92c2d3cb404cfc8bc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23885125"
---
# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>2 – példa egy tűzfal és az NSG-alkalmazások védelmét DMZ összeállítása
[A biztonsági határ bevált gyakorlatok laphoz való visszatéréshez][HOME]

Ebben a példában a DMZ hozzon létre egy tűzfal, négy windows Server-kiszolgálók és hálózati biztonsági csoportok. Azt is haladhat végig a megfelelő parancsok bemutatják az egyes lépések, adja meg. Is van a forgalom forgatókönyv részt, hogy egy részletesebb lépésenkénti hogyan forgalom halad keresztül a Szegélyhálózaton lévő védelmi réteget. Végezetül a hivatkozások szakasz: a teljes kód látható, míg utasítás ebben a környezetben, teszteléséhez, és a különböző forgatókönyvekben kísérletezhet létrehozásához. 

![NVA és NSG bejövő DMZ][1]

## <a name="environment-description"></a>Környezet leírása
Ebben a példában nincs olyan előfizetést, amelyet a következőket tartalmazza:

* A felhőalapú szolgáltatások két: "FrontEnd001" és "BackEnd001"
* A virtuális hálózati "CorpNetwork", két alhálózattal rendelkező: "Előtér" és "Háttér"
* Mindkét alhálózat alkalmazott egyetlen hálózati biztonsági csoport
* Ebben a példában a Barracuda NextGen tűzfal, a hálózati virtuális készülék a Frontend alhálózathoz csatlakozik.
* A Windows Server egy webalkalmazás-kiszolgáló ("IIS01") jelölő
* Két windows Server kiszolgálókon, amelyek megfelelnek az alkalmazás biztonsági end-kiszolgálók ("AppVM01", "AppVM02")
* A Windows server DNS-kiszolgáló ("DNS01") jelölő

> [!NOTE]
> Bár ebben a példában Barracuda NextGen tűzfalat használ, a másik virtuális hálózati berendezések számos ehhez a példához is használható.
> 
> 

Az alábbi hivatkozások részben van egy PowerShell-parancsfájlt, amely a fent leírt környezetben a legtöbb fog létrehozni. A virtuális gépek és virtuális hálózatok, bár a példaként megadott parancsfájlt, által végzett dokumentum nem ismerteti a jelen dokumentum részletesen.

A környezet létrehozásához:

1. Mentse a hálózati konfigurációs XML-fájlt a references szakaszában szereplő (frissítődik nevét, helyét és IP-címeket az adott forgatókönyv esetén)
2. A felhasználói változók a parancsfájl felel meg a parancsfájl-hoz (előfizetések, service nevét stb.) kell futtatni a környezet frissítése
3. A parancsfájl végrehajtása a PowerShellben

**Megjegyzés:**: A régió, a PowerShell-parancsfájl esetében meg kell egyeznie a hálózati konfigurációs XML-fájl esetében.

Miután a parancsfájl sikeresen fut a következő utáni parancsfájl lépések tehetők:

1. Állítsa be a tűzfalszabályok, ezzel kapcsolatban lásd: című részt az alábbi: tűzfalszabályokat.
2. Opcionálisan a references szakaszában vannak két parancsfájlok állíthat be a webkiszolgáló és egy egyszerű webes alkalmazás tesztelése a DMZ-konfiguráció engedélyezése az alkalmazások kiszolgálói.

Az alábbi szakasz ismerteti a legtöbb hálózati biztonsági csoportok viszonyítva parancsfájlok utasítás.

## <a name="network-security-groups-nsg"></a>Hálózati biztonsági csoportok (NSG)
Az ebben a példában egy NSG-csoport összeállítása és hat szabályokkal majd betölteni. 

> [!TIP]
> Általánosságban véve az "Engedélyezés" speciális szabályok először hozzon létre, és majd a általánosabbá "Deny" szabályok utolsó. A prioritási megkövetel amelyek szabályokat első értékeli ki. Forgalom kiderül, hogy egy adott szabály vonatkozik, ha nincsenek további szabályok kiértékelése. NSG-szabályok egyaránt (az alhálózati szempontjából) a bejövő vagy kimenő irányban is alkalmazhat.
> 
> 

Deklaratív módon a bejövő forgalom beépített folyamatban a következő szabályokat:

1. Belső DNS-forgalom (53-as port) engedélyezve van
2. RDP-forgalmát (3389-es port) az internetről bármely virtuális géphez engedélyezett
3. Engedélyezett HTTP-forgalom (80-as port) az internethez az NVA (tűzfal)
4. Minden forgalmat (minden porthoz) IIS01 AppVM1 engedélyezett
5. Az összes bejövő forgalom (minden porthoz) az internetről a teljes virtuális hálózatot (alhálózatok mindkét) megtagadva
6. Az összes bejövő forgalom (minden porthoz) a Frontend alhálózatból a Backend alhálózathoz megtagadva

A következő szabályok kötött alhálózatok, ha a HTTP-kérelem a webkiszolgálón, 3 szabályokat is az internetről bejövő (engedélyezése) és 5 (megtagadni) szeretné alkalmazni, de mivel a szabály 3 egy magasabb prioritással bír, csak azt csak akkor vonatkozik, és 5 szabály nem lesz play kerülhet. A HTTP-kérelem így szeretné tenni, hogy a tűzfal. Ha ugyanaz a forgalom próbál elérni az DNS01 kiszolgálót, szabály 5 (Megtagadás) lenne az első alkalmazni, és szeretné, hogy a kiszolgáló nem engedélyezett a forgalmat. 6 (Megtagadás) szabály blokkolja a Frontend alhálózathoz van szó, a Backend alhálózathoz (kivéve a engedélyezett forgalom szabályokban 1 és 4) a, ez védelmet nyújt a háttér hálózathoz, abban az esetben, ha egy támadó biztonság sérüléseinek előtér, a támadó a webalkalmazás volna korlátozott hozzáféréssel a háttérrendszer "védett" hálózaton (csak a AppVM01 kiszolgálón kitett erőforrások).

Nincs olyan alapértelmezett kimenő szabály, amely lehetővé teszi, hogy a kimenő forgalom az internethez. Ebben a példában a Microsoft most átengedi a kimenő forgalmat, és nem módosítja az egyetlen kimenő szabályok. A forgalom zárolását, mindkét irányban, a felhasználó definiált útválasztási szükség, ez van írja le egy másik példa is található a [fő biztonsági határ dokumentum][HOME].

A fent tárgyaltuk NSG-szabályok nagyon hasonlóak az NSG-szabályok [1 -. példa az NSG-ket egy egyszerű DMZ Build][Example1]. Tekintse át az egyes NSG-szabályokat és az attribútumok részletes tekintse meg a dokumentum a NSG leírását.

## <a name="firewall-rules"></a>Tűzfalszabályok
A kezelési ügynök a számítógépen a tűzfal felügyeletéhez, és a szükséges konfigurációk létrehozásához telepíteni kell. A dokumentáció a tűzfal (vagy más NVA) szállítójával tekintse meg az eszközök felügyelete. Ez a szakasz többi magát, a tűzfal a szállító felügyeleti ügyfél (azaz nem az Azure portál vagy PowerShell) keresztül lesz konfigurálását.

Ügyfél letöltése és az ebben a példában használt Barracuda kapcsolódás itt található: [Barracuda NG rendszergazda](https://techlib.barracuda.com/NG61/NGAdmin)

A tűzfalon szabályok továbbítási kell létrehozni. Ebben a példában csak az internetes forgalmat az adathoz kötött a tűzfal, majd a webkiszolgáló irányítja, mert csak egy továbbító NAT-szabály van szükség. A Barracuda NextGen tűzfalon használt ebben a példában a szabály lenne egy cél NAT-szabály ("nyári időszámítás NAT") továbbítani a forgalmat.

Hozza létre a következő szabályt (vagy ellenőrizze a meglévő alapértelmezett szabályok), a Barracuda NG felügyeleti ügyfél irányítópultról indítása a konfiguráció lapjának megjelenítéséhez, a működési konfigurációs szakaszban kattintson szabálykészletben. A rács nevű, "Main szabályok" jelennek meg a meglévő aktív és inaktív szabályok a tűzfalon. A rács jobb felső sarkában a kisméretű, zöld "+" gombra, ide kattintva hozzon létre egy új szabályt (Megjegyzés: a tűzfal "zárolva" a változásokat, ha a gomb "Lock" megjelölve, és nem tudja létrehozni vagy szabályok szerkesztése, kattintson erre a gombra kattintva "zárolásának feloldásához" a ruleset és Szerkesztés engedélyezése). Meglévő szabály szerkesztése, válassza ki, hogy a szabály, kattintson a jobb gombbal és válassza ki a szabály szerkesztése.

Hozzon létre egy új szabályt, és adjon meg egy nevet, például a "WebTraffic". 

A cél NAT-szabály ikon néz ki: ![cél NAT ikon][2]

A szabály maga hasonló ehhez hasonló lenne:

![Tűzfalszabály][3]

Itt a bejövő címet, amely a tűzfal találatok elérni HTTP (80-as vagy a HTTPS-hez a 443-as port) fog kell elküldését a tűzfal "DHCP1 helyi IP-címe" illesztőfelület és átirányítja a Web Server 10.0.1.5 IP-címét. Mivel a forgalom 80-as porton várható, és továbblép a webkiszolgálónak a 80-as port nem port módosítása volt szükség. Azonban a cél lista sikerült lett 10.0.1.5:8080 Ha így fordítása a bejövő 80-as port a tűzfalon a webkiszolgálón a 8080-as a bejövő portot a 8080-as porton figyel a webkiszolgálón.

A kapcsolódás módját kell is lehet szereplő, az internetről, a cél szabály "Dinamikus SNAT" legmegfelelőbb. 

Habár egyetlen szabály jött létre fontos, hogy helyesen van-e állítva a hozzá tartozó prioritás. Ha a szabályok a tűzfalon az új szabály van alján (alatt a "BLOCKALL" szabály) a rácsban soha nem érkezni fog szerepet. Győződjön meg arról, a webes forgalomban az újonnan létrehozott szabály a BLOCKALL szabály felett van.

Ha a szabály jön létre, kell leküldeni a tűzfalat, és majd aktiválni, ha ez nem történik a szabály módosítása nem lépnek érvénybe. A leküldéses és az aktiválási folyamat a következő szakaszban ismertetett.

## <a name="rule-activation"></a>A szabály aktiválás
A ruleset módosíthatja, hogy ez a szabály hozzáadása, az a ruleset kell feltölthetők a tűzfalhoz és aktiválva.

![Tűzfal szabály aktiválás][4]

A felügyeleti ügyfél jobb felső sarkában található egy fürt gombok vannak. A módosított szabályok és a tűzfalon küldendő a "Küldési módosítások" gombra, majd kattintson az "Aktiválás" gombra.

Az az aktiválás, a tűzfal szabálykészletben ez példa környezet build befejeződött. Szükség esetén a References szakaszában, a feladás egy vagy több build parancsfájlok futtatásával hozzáadhat egy alkalmazást ebben a környezetben tesztelheti a forgalom forgatókönyvek alatt.

> [!IMPORTANT]
> Nagyon fontos, hogy rendszer nem kattint a webkiszolgáló közvetlenül megvalósításához. Ha egy böngészőben kér FrontEnd001.CloudApp.Net egy HTTP-lap, a HTTP-végpont (80-as port) továbbítja a forgalmat a tűzfal nem a webkiszolgálón. A tűzfal, majd a szabály megfelelően a fenti létrehozott, a webkiszolgáló kérő NAT.
> 
> 

## <a name="traffic-scenarios"></a>Forgalom forgatókönyvek
#### <a name="allowed-web-to-web-server-through-firewall"></a>(Engedélyezett) Webes a webkiszolgáló tűzfalon keresztül
1. Internetes felhasználói kérelmek HTTP oldal FrontEnd001.CloudApp.Net (Internet Facing Felhőszolgáltatás)
2. Felhőalapú szolgáltatás fázisok forgalmat a 80-as port a helyi tűzfal megfelelő felületéről 10.0.1.4:80 nyitott végpontok keresztül
3. Frontend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. NSG szabály 1 (DNS) nem teljesül, a következő szabály áthelyezése
   2. NSG szabály 2 (RDP) nem teljesül, a következő szabály áthelyezése
   3. NSG 3. szabály (Internet tűzfalhoz) alkalmazza, akkor engedélyezett, befejezése szabály feldolgozása
4. Forgalom találatok belső IP-címet a tűzfal (10.0.1.4)
5. Továbbító tűzfalszabály tekintse meg a 80-as portot a forgalom, irányítja át a webkiszolgáló IIS01
6. IIS01 figyeli a webes forgalom, ezt a kérelmet kap, és elindítja a kérés feldolgozása
7. IIS01 az SQL Server a AppVM01 adatokat kéri
8. Nincs kimenő szabályok Frontend alhálózaton, forgalom engedélyezve van
9. A Backend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. NSG szabály 1 (DNS) nem teljesül, a következő szabály áthelyezése
   2. NSG szabály 2 (RDP) nem teljesül, a következő szabály áthelyezése
   3. NSG 3. szabály (Internet tűzfalhoz) nem teljesül, a közvetkező szabályának áthelyezése
   4. NSG 4. szabály (a AppVM01 IIS01) teljesül, a forgalom engedélyezve van, akkor állítsa le a szabály feldolgozása
10. AppVM01 dokumentálásáért és az SQL-lekérdezést kap
11. A válasz engedélyezett, mert nincsenek meg a Backend alhálózathoz kimenő szabályok
12. Frontend alhálózathoz bejövő szabály feldolgozása kezdődik:
    1. Nincs érvényes bejövő szabály NSG a Frontend alhálózathoz, így nincs az NSG-szabályok vonatkoznak a Backend alhálózathoz-forgalom
    2. A alapértelmezett rendszerszintű szabály, amely lehetővé teszi az alhálózatok közötti forgalmat lehetővé tenné a forgalmat, a forgalom engedélyezve van.
13. Az IIS-kiszolgálót az SQL-válasz fogadása és a HTTP-válasz befejezése és a kérelmező küld
14. Mivel ez a tűzfal NAT munkamenetének, a válasz a cél (kezdetben) nem a tűzfal
15. A válasz fogadása a webkiszolgáló a tűzfalat, és továbbítja azokat a Internet felhasználónak
16. Mivel nincsenek a Frontend alhálózaton a válasz nem kimenő szabályok engedélyezett, és az internetes felhasználói kap, a kért weblap.

#### <a name="allowed-rdp-to-backend"></a>(Engedélyezett) RDP háttérrendszeréhez
1. Server Admin interneten kérelmek AppVM01 BackEnd001.CloudApp.Net:xxxxx, ahol xxxxx az RDP számára (a hozzárendelt port található az Azure-portál vagy a PowerShell segítségével) AppVM01 véletlenszerűen hozzárendelt portszámot az RDP-munkamenetet
2. A tűzfalon csak a FrontEnd001.CloudApp.Net címet figyeli, mivel nem részt vesz a forgalom áramlását az
3. Backend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. NSG szabály 1 (DNS) nem teljesül, a következő szabály áthelyezése
   2. NSG szabály 2 (RDP) alkalmazza, akkor engedélyezett, befejezése szabály feldolgozása
4. A nem kimenő szabályokat alapértelmezett szabályokat alkalmazni, és a forgalom engedélyezve van
5. RDP-munkamenetbe engedélyezve van
6. Felhasználói nevének jelszavának megadását kéri az AppVM01

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Engedélyezett) Web Server DNS-címkeresés DNS-kiszolgálón
1. Webalkalmazás-kiszolgálón, IIS01, egy adatcsatorna www.data.gov: igényeinek, de a címek feloldására igényeinek.
2. A hálózati konfigurációt a VNet listák DNS01 (a háttér alhálózaton 10.0.2.4) elsődleges DNS-kiszolgálóként, IIS01 küld a DNS-kérelem DNS01
3. Nincs kimenő szabályok Frontend alhálózaton, forgalom engedélyezve van
4. Backend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. NSG szabály 1 (DNS) alkalmazza, akkor engedélyezett, befejezése szabály feldolgozása
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

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Engedélyezett) Server-hozzáférés fájl AppVM01
1. IIS01 kér AppVM01 fájlba
2. Nincs kimenő szabályok Frontend alhálózaton, forgalom engedélyezve van
3. A Backend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. NSG szabály 1 (DNS) nem teljesül, a következő szabály áthelyezése
   2. NSG szabály 2 (RDP) nem teljesül, a következő szabály áthelyezése
   3. NSG 3. szabály (Internet tűzfalhoz) nem teljesül, a közvetkező szabályának áthelyezése
   4. NSG 4. szabály (a AppVM01 IIS01) teljesül, a forgalom engedélyezve van, akkor állítsa le a szabály feldolgozása
4. AppVM01 a kérelmet kap, és válaszol, a fájl (feltéve, hogy van engedélyezve)
5. A válasz engedélyezett, mert nincsenek meg a Backend alhálózathoz kimenő szabályok
6. Frontend alhálózathoz bejövő szabály feldolgozása kezdődik:
   1. Nincs érvényes bejövő szabály NSG a Frontend alhálózathoz, így nincs az NSG-szabályok vonatkoznak a Backend alhálózathoz-forgalom
   2. A alapértelmezett rendszerszintű szabály, amely lehetővé teszi az alhálózatok közötti forgalmat lehetővé tenné a forgalmat, a forgalom engedélyezve van.
7. Az IIS-kiszolgálót kap a fájl

#### <a name="denied-web-direct-to-web-server"></a>(Megtagadva) Közvetlenül a webkiszolgálón webalkalmazás
Mivel a webkiszolgálón, IIS01 és a tűzfalon az azonos felhőalapú szolgáltatás nyilvános felé néző IP-cím-os osztoznak. Így minden HTTP-forgalom volna átirányítja a tűzfalon. A kérelem sikeresen kiszolgálta lenne, amíg nem tud közvetlenül lépni a webkiszolgáló, az átadott, úgy tervezték, a tűzfalon keresztül először. Ebben a szakaszban a forgalom áramlását az első forgatókönyvben talál.

#### <a name="denied-web-to-backend-server"></a>(Megtagadva) Webes háttérkiszolgálóra
1. Internetes felhasználó megpróbál hozzáférni egy fájl AppVM01 a BackEnd001.CloudApp.Net szolgáltatáson keresztül
2. Mivel nincsenek nyissa meg a fájlmegosztás nincsenek végpontok, ez nem lenne továbbítja a felhőalapú szolgáltatás, és így elérni a kiszolgálót
3. Ha valamilyen okból a végpontok nyitott, NSG szabály 5 (Internet virtuális hálózatba) blokkolná-e a forgalom

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Megtagadva) Webes DNS-címkeresés DNS-kiszolgálón
1. Internetes felhasználó megpróbálja megkeresni az egy belső DNS-rekordot a DNS01 a BackEnd001.CloudApp.Net szolgáltatáson keresztül
2. Mivel nincsenek végpontok nyissa meg a DNS, ez nem lenne továbbítja a felhőalapú szolgáltatás, és így elérni a kiszolgálót
3. Ha valamilyen okból a végpontok nyitott, NSG szabály 5 (Internet virtuális hálózatba) blokkolná-e a forgalom (Megjegyzés: csak akkor nem vonatkozik két okból szabály 1 (DNS), először a forrás címe az interneten, ez a szabály csak a helyi virtuális hálózat, mint a forrás vonatkozik Ez a egy olyan engedélyezési szabály, akkor soha nem letiltsa a forgalom)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Megtagadva) Webes SQL-hozzáférés tűzfalon keresztül
1. Internetes felhasználó SQL adatokat kér a FrontEnd001.CloudApp.Net (Internet Facing Felhőszolgáltatás)
2. Mivel nincsenek végpontok nyissa meg az SQL, ez nem lenne továbbítja a felhőalapú szolgáltatás, és a tűzfal nem tudnák elérni
3. Ha valamilyen okból végpontok voltak nyitva, a Frontend alhálózathoz megkezdi a bejövő szabály feldolgozása:
   1. NSG szabály 1 (DNS) nem teljesül, a következő szabály áthelyezése
   2. NSG szabály 2 (RDP) nem teljesül, a következő szabály áthelyezése
   3. NSG 2. szabály (Internet tűzfalhoz) alkalmazza, akkor engedélyezett, befejezése szabály feldolgozása
4. Forgalom találatok belső IP-címet a tűzfal (10.0.1.4)
5. Tűzfal SQL nincs továbbítási szabályaik rendelkezik, és a forgalom esik

## <a name="conclusion"></a>Összegzés
Ez viszonylag egyszerű módja az alkalmazás egy tűzfal védi, és a háttér-alhálózathoz, a bejövő forgalom elkülönítésére.

További példákat és a hálózati biztonsági határokat egy áttekintést talál [Itt][HOME].

## <a name="references"></a>Referencia
### <a name="main-script-and-network-config"></a>Fő parancsfájlt és a hálózati konfiguráció
A teljes parancsfájl menthető egy olyan PowerShell-parancsfájlt. A hálózati konfiguráció mentése "NetworkConf2.xml" fájlba.
Szükség szerint módosítsa a felhasználó által definiált változókat. Futtassa a parancsfájlt, majd kövesse a tűzfal szabály beállítása a fenti.

#### <a name="full-script"></a>Teljes körű parancsfájl
Ez a parancsfájl lesz, a felhasználó által definiált változóknak alapján:

1. Csatlakozás Azure-előfizetéshez
2. Új tárfiók létrehozása
3. Hozzon létre egy új virtuális hálózat és a hálózati konfigurációs fájljában definiált két alhálózat
4. 4 a windows server virtuális gépek létrehozása
5. Adja meg, beleértve az NSG:
   * Egy NSG létrehozása
   * Azt a szabályoknak feltöltése
   * Az NSG kötése a megfelelő alhálózatokat

A PowerShell parancsfájl fusson helyben egy internethez csatlakoztatott PC vagy a kiszolgáló.

> [!IMPORTANT]
> Ezt a parancsfájlt, amikor előfordulhat figyelmeztetések vagy a PowerShellben pop más tájékoztató üzeneteit. Piros csak hibaüzenetek aggodalomra.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
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
       myFirewall - 10.0.1.4
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
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

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
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
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
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Az NSG bejövő DMZ"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Cél NAT ikon"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Tűzfalszabály"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Tűzfal szabály aktiválás"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
