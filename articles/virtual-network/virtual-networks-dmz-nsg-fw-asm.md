---
title: Szegélyhálózat (DMZ) Példa – semleges védheti alkalmazásait egy tűzfallal és NSG-k |} A Microsoft Docs
description: Semleges zóna kialakítása a tűzfallal és hálózati biztonsági csoportok (NSG)
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
ms.openlocfilehash: fdc4885c079a3659d394517f0a10394eff0720c8
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119153"
---
# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>2 – példa semleges védheti alkalmazásait egy tűzfallal és NSG-k
[Térjen vissza a biztonsági határ ajánlott eljárások lap][HOME]

Ebben a példában egy DMZ-t hoz létre tűzfalat, négy windows-kiszolgálók és hálózati biztonsági csoportok. Azt is végigvezetik az adja meg az egyes lépések jobban megértheti a megfelelő parancsokat. Emellett van egy forgalom forgatókönyv részt, hogy egy részletes részletes hogyan halad a forgalom a rétege a DMZ-n keresztül. Végül az hivatkozások szakaszban, a teljes kódját és utasítás összeállításához, teszteléséhez, és kísérletezzen a különböző forgatókönyvekben ebben a környezetben. 

![Szegélyhálózat (DMZ) az nva-t és az NSG bejövő][1]

## <a name="environment-description"></a>Környezet leírása
Ebben a példában van egy előfizetést, amely a következőket tartalmazza:

* Cloud services két: "FrontEnd001" és "BackEnd001"
* Virtuális hálózat "CorpNetwork", két alhálózattal: "Előtér" és "Háttér"
* Mindkét alhálózat alkalmazott egyetlen hálózati biztonsági csoport
* Ebben a példában a Barracuda NextGen tűzfal, egy hálózati virtuális berendezésen az előtérben levő alhálózathoz csatlakozik.
* A Windows Server, amely egy webalkalmazás-kiszolgáló ("IIS01") jelöli.
* Vissza az alkalmazást képviselő két windows-kiszolgálók záró kiszolgálók ("AppVM01", "AppVM02")
* A Windows server DNS-kiszolgáló ("DNS01") jelölő

> [!NOTE]
> Bár ebben a példában a Barracuda NextGen tűzfal, a különböző hálózati virtuális berendezések számos ebben a példában használható.
> 
> 

Az alábbi references szakaszában van egy PowerShell-parancsprogram, amely a fent leírt környezetben a legtöbb fog létrehozni. A virtuális gépek és virtuális hálózatok létrehozásához kell elvégeznie a példaszkript, bár nem ismerteti a jelen dokumentum részletesen.

A környezet létrehozásához:

1. Mentse a hálózati konfigurációs xml-fájlt a references szakaszában szereplő (frissített nevét, helyét és IP-címeket az adott esethez)
2. Frissítse a felhasználói változók a parancsfájl felel meg a környezetben, a parancsfájl az, hogy futtatni (előfizetés, szolgáltatásnevek stb.)
3. Hajtsa végre a szkriptet a PowerShellben

**Megjegyzés**: A régió, a PowerShell-parancsfájl esetében meg kell egyeznie a hálózati konfigurációs xml-fájl esetében.

A szkript sikeres futtatása után a következő utáni lépéseket lehet tenni:

1. A tűzfalszabályok beállítása, ez foglalkozik című szakaszt az alábbi: Tűzfalszabály.
2. Igény szerint a hivatkozások szakaszban két parancsfájlt a webkiszolgáló és a egy egyszerű webalkalmazást, hogy a tesztelés a DMZ-konfigurációval rendelkező alkalmazáskiszolgáló beállításához vannak.

Az alábbi szakasz ismerteti a legtöbb olyan hálózati biztonsági csoportok viszonyított parancsfájlok utasítás.

## <a name="network-security-groups-nsg"></a>Hálózati biztonsági csoportok (NSG)
Ebben a példában egy NSG-csoport beépített, és aztán betölti a hat szabályokkal. 

> [!TIP]
> Általánosan fogalmazva az "Engedélyezés" szabályainak először hozzon létre, és ezután az "Elutasítás" általánosabb érvényűvé szabályok utolsó. A hozzárendelt prioritásokkal azt határozza meg szabályokat, amelyek az első értékeli ki. Forgalom kiderül, hogy egy adott szabály vonatkozik, ha nincsenek további szabályok értékeli ki. Az NSG-szabályok vagy (az alhálózat szempontjából) a bejövő vagy kimenő irányban is érvényesek.
> 
> 

Deklaratív a következő szabályok beépített folyamatban van a bejövő forgalmat:

1. Belső DNS-forgalmat (53-as port)
2. RDP-forgalmat (3389-es port) az internetről bármely virtuális géphez
3. HTTP-forgalmat (80-as port) az internetről érkező, az nva-n (tűzfal)
4. Minden újabb forgalmát (minden porthoz) IIS01 AppVM1 engedélyezett
5. Virtuális hálózat (mindkét alhálózat) bármely (minden porthoz) az internetről forgalmat a teljes megtagadva
6. Az összes bejövő forgalom (minden porthoz) a felhasználói réteg alhálózatáról a háttérrendszer alhálózatának megtagadva

A következő szabályok kötött mindegyik olyan alhálózatban, ha a webkiszolgálón, 3 szabályokat is az internetről bejövő HTTP-kérés (engedélyezése) és 5 (nem engedélyezi a) a alkalmazni szeretne, de mivel a szabály 3 egy magasabb prioritással bír, csak a alkalmazni, és szabály 5 lenne nem jut. Így a HTTP-kérelem szeretné engedélyezni a tűzfalon. Ha ugyanaz a forgalom próbál elérni az DNS01 kiszolgálót, 5 (Megtagadás) szabály lenne a alkalmazni az első, és szeretne átadni a kiszolgálónak nem engedélyezi a forgalmat. 6 (Megtagadás) szabály blokkolja az előtérben levő alhálózathoz (kivéve az 1. és 4 szabályok engedélyezett forgalom) a háttérrendszer alhálózatának léptünk, ez védelmet biztosít a háttérrendszer-hálózat, abban az esetben, ha egy támadó feltörések előtér, a támadó a webalkalmazás lenne korlátozott hozzáféréssel a háttérrendszer "védett" hálózat (csak az erőforrások elérhetővé tett a AppVM01 kiszolgálón).

Nincs olyan alapértelmezett kimenő szabály, amely lehetővé teszi, hogy az internetre irányuló forgalom. Ebben a példában folyamatban van lehetővé teszi a kimenő forgalom és nem módosítja a kimenő szabályok. Való zárolását, így a forgalom mindkét irányban, felhasználói meghatározott útválasztás szükség, ez van egy másik példa, amely is szerepel a megvizsgálta a [fő biztonsági határ dokumentum][HOME].

A fent tárgyaltuk NSG-szabályok nagyon hasonlóak az NSG-szabályokat a [1. példa – hozhat létre egy egyszerű Szegélyhálózat NSG-k][Example1]. Tekintse át a nevezett dokumentum részletes át az egyes NSG-szabály és a hozzá tartozó attribútumok az NSG-t leírása.

## <a name="firewall-rules"></a>Tűzfalszabályok
A tűzfal kezelése és a szükséges konfigurációk létrehozásához a számítógépen telepíteni kell egy kezelési ügyfél. Tekintse át a dokumentáció a tűzfalon (vagy más NVA) szállító az eszköz kezelésére. Ez a szakasz további része, a tűzfal a szállító felügyeleti ügyfél (tehát nem az Azure Portalon vagy a PowerShell) keresztül lesz konfigurálását.

Ügyfél letöltése és csatlakozik a Barracuda, ebben a példában használt vonatkozó utasítások itt található: [Barracuda NG rendszergazda](https://techlib.barracuda.com/NG61/NGAdmin)

A tűzfalon továbbítási szabályokat kell létrehozni. Ebben a példában csak az internetes forgalmat a kötött a tűzfal, majd a webkiszolgáló irányítja, mivel csak egy továbbító NAT-szabályhoz van szükség. Az ebben a példában használt, a Barracuda NextGen tűzfal a szabály lenne egy cél NAT-szabály ("nyári időszámítás NAT") adja át a forgalmat.

Hozza létre a következő szabályt (vagy ellenőrizze a meglévő alapértelmezett szabályok), a Barracuda NG rendszergazdai ügyfél irányítópultján, kezdve a konfiguráció lapjának megjelenítéséhez, az üzemeltetési konfiguráció szakaszban kattintson a Ruleset. A rács nevű, "Main szabályok" jelennek meg a meglévő aktív és inaktív szabályok a tűzfalon. A jobb felső sarkában található a rács van egy kis méretű, zöld "+" gombra kattint, ide kattintva hozzon létre egy új szabályt (Megjegyzés: a tűzfal "zárolva" a módosításokat, ha egy gombot "Zárolási" jelölésű, és nem lehet létrehozni vagy szabályok szerkesztése, kattintson erre a gombra kattintva a "feloldásához" a szabálykészletben és Szerkesztés engedélyezése). Ha szeretné szerkeszteni a meglévő szabályokat, válassza ki azt a szabályt, jobb gombbal, majd válassza a szabály szerkesztése.

Hozzon létre egy új szabályt, és adjon meg egy nevet, például a "WebTraffic". 

A cél NAT-szabály ikonra a következőhöz hasonló: ![Cél NAT ikon][2]

A szabály maga kellene kinéznie:

![Tűzfalszabály][3]

Itt minden bejövő címet, amely eléri a tűzfal megpróbálják elérni az HTTP (80-as vagy 443-as HTTPS-port) fog kell elküldését a tűzfal "A helyi IP-DHCP1" kapcsolat és átirányítja a webkiszolgáló a 10.0.1.5 IP-címét. Mivel a forgalmat 80-as porton érkező és a webalkalmazás-kiszolgáló 80-as portot fogja port változatlan volt szükség. Azonban a cél lista lehetett volna 10.0.1.5:8080, ha a webkiszolgáló így fordítja le a bejövő 80-as portra a tűzfalon a webkiszolgálón a bejövő portot 8080-as, 8080-as porton figyel.

A kapcsolódás módját kell is lehet szereplő, az internetről, a cél szabály "Dinamikus SNAT" legmegfelelőbb. 

Habár egyetlen szabály létrejött fontos, hogy helyesen van-e megadva a hozzá tartozó prioritás. Ha a tűzfal összes szabály, az új szabály be van kapcsolva az alsó (alább a "BLOCKALL" szabály) a rácsban, rendszer soha nem jut. Győződjön meg arról, a webes forgalmat az újonnan létrehozott szabály a BLOCKALL szabály felett van.

Ha a szabályt létrehozza, kell leküldeni a tűzfalat, és majd aktiválása, ha ez nem történik a szabályt úgy módosítják nem lépnek érvénybe. A leküldéses és az aktiválási folyamat a következő szakaszban leírt.

## <a name="rule-activation"></a>A szabály az aktiválás
A szabálykészletben Ez a szabály hozzáadása módosítás, az a szabálykészletben kell feltölteni a tűzfal és aktiválva.

![Tűzfal szabály aktiválás][4]

A jobb felső sarokban a felügyeleti ügyfél gombok fürtben vannak. A "Módosítások küldése" gombra kattintva a módosított szabályok küldeni a tűzfal, majd kattintson az "Aktiválás" gombra.

Az aktiválás, a tűzfal szabálykészletben Ez a példa környezet build elkészült. Szükség esetén a post build parancsfájlokat a hivatkozások szakaszban hozzáadása teszteléséhez használandó alkalmazást erre a környezetre lehet futtatni a forgalom forgatókönyvek alatt.

> [!IMPORTANT]
> Vegye figyelembe, hogy Ön nem eléri a webalkalmazás-kiszolgáló közvetlenül rendkívül fontos. Ha egy böngészőben kér FrontEnd001.CloudApp.Net egy HTTP-lap, a HTTP-végpontot (80-as port) továbbítja a forgalmat a tűzfal nem a webkiszolgáló. A tűzfal, majd a szabálya szerint fent létrehozott, a webkiszolgálónak kérő NAT.
> 
> 

## <a name="traffic-scenarios"></a>Forgalom forgatókönyvek
#### <a name="allowed-web-to-web-server-through-firewall"></a>(Engedélyezett) Webes a webkiszolgáló-tűzfalon keresztül
1. Internetes felhasználói kérések HTTP lapot FrontEnd001.CloudApp.Net (Internet Facing Felhőszolgáltatás)
2. Cloud service pass forgalmat a 80-as porton a tűzfal megfelelő felületéről helyi 10.0.1.4:80 nyitott végpontok
3. Előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
   1. NSG-szabály 1 (DNS) nem vonatkoznak, helyezze át a következő szabály
   2. NSG-szabály 2 (RDP) nem vonatkoznak, helyezze át a következő szabály
   3. NSG 3. szabály (Internet tűzfalhoz) a alkalmazni, a forgalom engedélyezett, állítsa le a szabály feldolgozása
4. Forgalom eléri a tűzfal (10.0.1.4 cím – ez) belső IP-címe
5. Továbbítás tűzfalszabály tekintse meg a forgalom a 80-as porton, irányítja át a webkiszolgáló IIS01
6. IIS01 figyeli a webes forgalmat, ezt a kérelmet kap, és elindítja a kérés feldolgozása
7. IIS01 kéri az SQL Server a AppVM01 információk
8. Nincs kimenő szabályok előtérbeli alhálózatán, forgalom engedélyezve van
9. A háttérrendszer alhálózatának bejövő szabály feldolgozása kezdődik:
   1. NSG-szabály 1 (DNS) nem vonatkoznak, helyezze át a következő szabály
   2. NSG-szabály 2 (RDP) nem vonatkoznak, helyezze át a következő szabály
   3. NSG 3. szabály (Internet tűzfalhoz) nem vonatkoznak, helyezze át a következő szabály
   4. NSG-szabály 4 (a AppVM01 IIS01) vonatkozik, a forgalom engedélyezve van, állítsa le a szabály feldolgozása
10. AppVM01 az SQL-lekérdezést kap, és válaszol
11. Mivel nincsenek a háttérbeli alhálózat kimenő szabályok engedélyezve van-e a válasz
12. Előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
    1. Nincs NSG szabály, amelyre vonatkozik a bejövő forgalmat a háttérbeli alhálózat felől az előtérben levő alhálózathoz, így egyike sem az NSG-szabályok alkalmazása
    2. A alapértelmezett rendszerszabály, amely lehetővé teszi az alhálózatok közötti adatforgalom lehetővé tenné a forgalmat, így a forgalom engedélyezve van.
13. Az IIS-kiszolgálón az SQL-válasz fogadása és befejezése a HTTP-válasz és küld a kérelmezőnek
14. Mivel ez a tűzfal NAT munkamenetének, a válasz cél (először) van tűzfal
15. A tűzfal a válasz fogadása a webkiszolgáló, és továbbítja a internetes felhasználó számára
16. Mivel ebben az esetben az előtérbeli alhálózat a válasz nem kimenő szabályok engedélyezve van, és az internetes felhasználó kapja meg a kért weblap.

#### <a name="allowed-rdp-to-backend"></a>(Engedélyezett) Háttérbeli RDP-vel
1. Kiszolgáló-rendszergazdai interneten kérelmek AppVM01 BackEnd001.CloudApp.Net:xxxxx, ahol xxxxx-e az RDP-vel (a hozzárendelt port találhatók az Azure Portal vagy a Powershellen keresztül) AppVM01 véletlenszerűen hozzárendelt portszámot az RDP-munkamenetet
2. A tűzfal csak az FrontEnd001.CloudApp.Net címet figyeli, mivel nem vesz a forgalom flow-val
3. Háttérbeli alhálózatot bejövő szabály feldolgozása kezdődik:
   1. NSG-szabály 1 (DNS) nem vonatkoznak, helyezze át a következő szabály
   2. NSG-szabály 2 (RDP) a alkalmazni, a forgalom engedélyezett, állítsa le a szabály feldolgozása
4. Kimenő szabályok, az alapértelmezett szabályok a alkalmazni, és a visszatérő forgalom engedélyezve van
5. RDP-munkamenet engedélyezve van
6. AppVM01 felkéri a felhasználónév jelszó

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Engedélyezett) A DNS-kiszolgáló kiszolgálói DNS-címkeresés webes
1. A Web Server, IIS01, egy adatcsatorna www.data.gov, igényeinek megfelelően, de igények címének feloldására.
2. A hálózati konfigurációt a virtuális hálózatok közötti listák DNS01 (a háttérbeli alhálózat 10.0.2.4 cím), az elsődleges DNS-kiszolgáló, IIS01 küld a DNS-kérelem DNS01
3. Nincs kimenő szabályok előtérbeli alhálózatán, forgalom engedélyezve van
4. Háttérbeli alhálózatot bejövő szabály feldolgozása kezdődik:
   1. NSG-szabály 1 (DNS) a alkalmazni, a forgalom engedélyezett, állítsa le a szabály feldolgozása
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

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Engedélyezett) Kiszolgáló-hozzáférés fájlt AppVM01
1. IIS01 AppVM01 fájlt kéri
2. Nincs kimenő szabályok előtérbeli alhálózatán, forgalom engedélyezve van
3. A háttérrendszer alhálózatának bejövő szabály feldolgozása kezdődik:
   1. NSG-szabály 1 (DNS) nem vonatkoznak, helyezze át a következő szabály
   2. NSG-szabály 2 (RDP) nem vonatkoznak, helyezze át a következő szabály
   3. NSG 3. szabály (Internet tűzfalhoz) nem vonatkoznak, helyezze át a következő szabály
   4. NSG-szabály 4 (a AppVM01 IIS01) vonatkozik, a forgalom engedélyezve van, állítsa le a szabály feldolgozása
4. AppVM01 dokumentálásáért fájllal (feltéve, hogy a hozzáférés engedélyezett), és a kérést kap
5. Mivel nincsenek a háttérbeli alhálózat kimenő szabályok engedélyezve van-e a válasz
6. Előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
   1. Nincs NSG szabály, amelyre vonatkozik a bejövő forgalmat a háttérbeli alhálózat felől az előtérben levő alhálózathoz, így egyike sem az NSG-szabályok alkalmazása
   2. A alapértelmezett rendszerszabály, amely lehetővé teszi az alhálózatok közötti adatforgalom lehetővé tenné a forgalmat, így a forgalom engedélyezve van.
7. Az IIS-kiszolgáló fogadja a fájl

#### <a name="denied-web-direct-to-web-server"></a>(Tiltott) Web-direct webkiszolgáló
Mivel a webkiszolgáló IIS01 és a tűzfal az ugyanazon a Felhőszolgáltatáson osztoznak az azonos nyilvános IP-cím. Így bármilyen HTTP-forgalom akkor átirányítja a tűzfalon. A kérelem sikeresen kiszolgált lenne, amíg nem tud közvetlenül lépni a webkiszolgáló, azt átadott, zavartalan a tűzfalon keresztül először. Tekintse meg az első forgatókönyv szerint ebben a szakaszban az adatforgalmat.

#### <a name="denied-web-to-backend-server"></a>(Tiltott) Webes háttérkiszolgálóra
1. Az Internet felhasználó megpróbál hozzáférni egy fájlt a AppVM01 a BackEnd001.CloudApp.Net szolgáltatáson keresztül
2. Mivel ebben az esetben végpontok nyissa meg a fájlmegosztás, ez a Felhőszolgáltatás nem kellene átadnia, és a kiszolgáló nem érhető el.
3. Ha valamilyen okból a végpontok volt megnyitva, NSG-szabály (Internet, virtuális hálózatok közötti) 5 le fog állni a forgalmat

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Tiltott) A DNS-kiszolgáló DNS-címkeresés webes
1. Az Internet felhasználó megpróbálja megkeresni egy belső DNS-rekordját DNS01 a BackEnd001.CloudApp.Net szolgáltatáson keresztül
2. Nincsenek nyissa meg a DNS-végpontok, mivel ez a Felhőszolgáltatás nem kellene átadnia, és a kiszolgáló nem érhető el.
3. Ha valamilyen okból a végpontok volt megnyitva, NSG-szabály (Internet, virtuális hálózatok közötti) 5 le fog állni a forgalmat (Megjegyzés: a szabály 1 (DNS) két okból nem alkalmazhatók, először a forrás címe az interneten, ez a szabály csak a helyi virtuális hálózat, mint a forrás vonatkozik Ez a egy olyan engedélyezési szabály, ezért soha nem lenne megtagadják a forgalmat)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Tiltott) Webalkalmazás SQL-hozzáférés tűzfalon keresztül
1. Internetes felhasználói SQL adatokat kér FrontEnd001.CloudApp.Net (Internet Facing Felhőszolgáltatás)
2. Végpontleképzőben nincs nyitott meg SQL esetén, mivel ez a Felhőszolgáltatás nem kellene átadnia, és a tűzfal nem érhető el.
3. Ha valamilyen okból végpontok volt megnyitva, az előtérben levő alhálózathoz megkezdi a bejövő szabály feldolgozása:
   1. NSG-szabály 1 (DNS) nem vonatkoznak, helyezze át a következő szabály
   2. NSG-szabály 2 (RDP) nem vonatkoznak, helyezze át a következő szabály
   3. NSG-t 2. szabály (Internet tűzfalhoz) vonatkozik, a forgalom engedélyezett, állítsa le a szabály feldolgozása
4. Forgalom eléri a tűzfal (10.0.1.4 cím – ez) belső IP-címe
5. Tűzfal nem tartozik továbbítási szabály meg SQL esetén, csökken a forgalom

## <a name="conclusion"></a>Összegzés
Ez viszonylag egyszerű módja az alkalmazás egy tűzfal védelmének, és a háttér-alhálózatot, bejövő forgalom elkülönítése.

További példákat és a hálózati biztonsági határok áttekintését találja [Itt][HOME].

## <a name="references"></a>Referencia
### <a name="main-script-and-network-config"></a>Fő parancsfájlba, és a hálózati konfiguráció
Mentse a teljes szkriptet egy PowerShell-parancsfájlt. Mentse a hálózati konfiguráció "NetworkConf2.xml" nevű fájl.
Szükség szerint módosítsa a felhasználó által definiált változókat. Futtassa a parancsfájlt, majd az útmutatást követve tűzfal szabály beállítása a fenti.

#### <a name="full-script"></a>Teljes szkript
Ez a szkript lesz, a felhasználó által definiált változóknak alapján:

1. Csatlakozás Azure-előfizetéshez
2. Új tárfiók létrehozása
3. Hozzon létre egy új virtuális hálózat és két alhálózatot a hálózati konfigurációs fájlban meghatározott
4. 4 a windows server rendszerű virtuális gépek létrehozása
5. Konfigurálja az NSG-t többek között:
   * Létrehozni egy NSG-t
   * Ez a szabályoknak feltöltése
   * Az NSG-t a megfelelő alhálózatokat kötése

Ez a PowerShell-szkript kell futtatni a egy internethez csatlakoztatott számítógépen vagy kiszolgálón.

> [!IMPORTANT]
> Ez a szkript futtatásakor a lehetséges, hogy figyelmeztetéseket vagy más tájékoztató üzeneteket, amely a pop a PowerShellben. Csak hibaüzenetek vörös színnel adhatnak okot.
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
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "NSG bejövő Szegélyhálózat"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Cél NAT ikon"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Tűzfalszabály"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Tűzfal szabály aktiválás"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
