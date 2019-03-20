---
title: Szegélyhálózat-alapú hálózati például – Build szegélyhálózaton alkalmazások tűzfallal való védelmét és NSG-k |} A Microsoft Docs
description: A szegélyhálózat tűzfallal és hálózati biztonsági csoportok (NSG-k) létrehozása
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
ms.openlocfilehash: e0271c9212b093bd803518ebeaa4b7d9682cc773
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997470"
---
# <a name="example-2-build-a-perimeter-network-to-protect-applications-with-a-firewall-and-nsgs"></a>2. példa Szegélyhálózaton védheti alkalmazásait egy tűzfallal és NSG-k létrehozása
[Térjen vissza a Microsoft cloud services és a hálózati biztonság lap][HOME]

Ez a példa bemutatja, hogyan hozhat létre a szegélyhálózaton (más néven *DMZ*, *demilitarizált zóna*, és *alapos szűrésen átesett alhálózati*) egy tűzfal, négy Windows Server rendszerű számítógépek és a hálózati biztonsági csoportok (NSG-k). Adja meg az egyes lépések jobban megértheti a megfelelő parancsokat minden egyes részleteit tartalmazza. A "Forgalom forgatókönyvek" szakasz hogyan forgalom halad keresztül a szegélyhálózaton a rétege részletes leírását tartalmazza. Emellett a "Hivatkozások" szakaszban a teljes kód és a vonatkozó, hogyan hozhat létre ebben a környezetben, más forgatókönyvekkel kísérletezhet és biztosít.

![Bejövő nva-t és az NSG-kkel a peremhálózaton][1]

## <a name="environment"></a>Környezet 
Ebben a példában az Azure-előfizetéssel, amely tartalmazza ezeket az elemeket a forgatókönyv alapul:

* Cloud services két: FrontEnd001 és BackEnd001.
* Virtuális hálózat két alhálózattal rendelkező CorpNetwork nevű: FrontEnd és BackEnd.
* Mindkét alhálózat alkalmazott egy NSG.
* Egy hálózati virtuális berendezések: A Barracuda NextGen tűzfal az előtérben levő alhálózathoz csatlakozik.
* A Windows Server rendszerű számítógépre webes alkalmazáskiszolgáló jelöli: IIS01.
* Két Windows Server számítógépek háttér-alkalmazáskiszolgálókat képviselő: AppVM01 és AppVM02.
* DNS-kiszolgálót képviselő Windows Server rendszerben: DNS01.

> [!NOTE]
> Bár ebben a példában a Barracuda NextGen tűzfal, számos hálózati virtuális berendezések lehet használni.
> 
> 

A "Hivatkozás" című szakaszban található PowerShell-szkriptet hoz létre a környezetben, az itt leírtak szerint a legtöbb. A virtuális gépek és virtuális hálózatok a parancsfájl által épített, de a folyamatok nem ebben a dokumentumban részletes leírása.

A környezet létrehozásához:

1. Mentse a hálózati konfigurációs XML-fájl tartalmazza a "Hivatkozások" szakaszban (frissített neveket, helyek és IP-címek a forgatókönyvnek megfelelően).
2. Frissítse a felhasználó által definiált változókat a PowerShell parancsfájl megfelelően a környezetben, a parancsprogram futtatása ellen (előfizetések szolgáltatásnevek és így tovább).
3. Futtassa a szkriptet a PowerShellben.

> [!NOTE]
> A PowerShell-parancsfájl a megadott területet meg kell egyeznie a régió van megadva. a hálózati konfigurációs XML-fájlban.
>
>

A szkript sikeres futtatása után is elvégezheti ezeket a lépéseket:

1. A tűzfalszabályok beállítása. Ez a cikk "Tűzfalszabályok" című szakaszában talál.
2. Ha azt szeretné, beállíthatja a webkiszolgáló és a egy egyszerű webalkalmazást, hogy a tesztelés a szegélyhálózat-alapú hálózati konfigurációval rendelkező alkalmazások kiszolgálói. Használhatja a "Hivatkozás" szakaszban található két alkalmazás-parancsprogramokat.

Ez a szakasz azt ismerteti, hogy kapcsolódik az NSG-ket a parancsprogram-utasítások a legtöbb.

## <a name="nsgs"></a>NSG-k
Ebben a példában egy NSG-csoport beépített, és aztán betölti a hat szabályokkal.

> [!TIP]
> Általánosságban véve az "Engedélyezés" szabályainak először hozzon létre, és az "Elutasítás" általánosabb érvényűvé szabályok utolsó. A hozzárendelt prioritás vezérlők, amelyek szabályok első értékeli ki. Miután adatforgalom található, amely egy adott szabály vonatkozik, nincsenek további szabályok értékeli ki. NSG-szabályokat alkalmazhat a bejövő vagy kimenő irányát (az alhálózat szempontjából).
> 
> 

Deklaratív ezek a szabályok beépített bejövő forgalomra:

1. Belső DNS-forgalom (53-as port) használata engedélyezett.
2. RDP (3389-es port) az internetről forgalmat az összes virtuális gép használata engedélyezett.
3. HTTP (80-as port) az internetről forgalmat az nva-n (tűzfal) való használata engedélyezett.
4. Minden újabb forgalmát (minden porthoz) IIS01 AppVM01 használata engedélyezett.
5. Az összes (minden porthoz) az internetről forgalmat a teljes virtuális hálózathoz (mindkét alhálózat) a rendszer megtagadja.
6. Minden forgalmat (minden porthoz) a felhasználói réteg alhálózatáról a háttérrendszer alhálózatának megtagadva.

A következő szabályok kötve, mindegyik olyan alhálózatban, ha a HTTP-kérést a webkiszolgáló, az internetről bejövő mindkét szabály 3 (engedélyezése) és a szabályok 5 (elutasítása) tűnik a alkalmazni, de a 3. szabály egy magasabb prioritással bír, mivel csak alkalmazni fogja. 5. szabály nem jut. Így a HTTP-kérelem engedélyezni a tűzfalon.

Ha ugyanaz a forgalom próbál elérni az DNS01 kiszolgálót, a szabály 5 (elutasítása) a alkalmazni, az első lenne, így a forgalom nem engedélyezett az a kiszolgálóra. 6. szabály (elutasítása) letiltja az előtér-alhálózatot tudjon kommunikálni a háttérrendszer alhálózatának (kivéve a forgalom az 1. és 4 szabályok engedélyezve). Ez védelmet biztosít a háttérrendszer-hálózat abban az esetben a támadó károsan befolyásolja az előtérbeli a webalkalmazás. Ebben az esetben a támadó volna csak korlátozott hozzáféréssel rendelkeznek a háttérrendszer "védett" hálózathoz. (A támadó elvileg látnia kell a AppVM01 kiszolgálón elérhető erőforrások elérését.)

Nincs olyan alapértelmezett kimenő szabály, amely lehetővé teszi, hogy az internetre irányuló forgalom. Ebben a példában folyamatban van lehetővé teszi a kimenő forgalom és nem módosítja a kimenő szabályok. Zárolhatja a forgalom mindkét irányba, szüksége felhasználó által meghatározott útválasztást. Ez a módszer egy másik példa a talál további információt a [fő biztonsági határ dokumentum][HOME].

Az itt leírtak szerint az NSG-szabályokat az NSG-szabályokat a hasonlóak [1. példa – hozhat létre egy egyszerű Szegélyhálózat NSG-k][Example1]. Tekintse át az ebben a cikkben részletes meg minden egyes NSG-szabály és a hozzá tartozó attribútumok az NSG-t leírása.

## <a name="firewall-rules"></a>Tűzfalszabályok
A számítógépen, hogy a tűzfal kezelése és a szükséges konfigurációk létrehozásához egy kezelési ügyfél telepítenie kell. Tekintse meg az eszköz kezeléséről a dokumentáció a tűzfalon (vagy más NVA) szállító. Ez a szakasz a többi, a tűzfalon keresztül a gyártói felügyeleti ügyfél (nem az Azure Portalon vagy a PowerShell) konfigurálását írja le.

Lásd: [Barracuda NG rendszergazdai](https://techlib.barracuda.com/NG61/NGAdmin) ügyfél letöltése és a Barracuda tűzfal ebben a példában használt csatlakozik.

Szeretne létrehozni a továbbítási szabályok a tűzfalon. Mivel ebben a példában a forgatókönyv csak internetes forgalmat irányítja a tűzfalhoz bejövő, és majd a webkiszolgáló, csak egy továbbító NAT-szabályt. A Barracuda tűzfal, ebben a példában használt a szabály lenne egy cél NAT-szabályt (nyári időszámítás NAT) továbbítja a forgalmat.

A következő szabály létrehozásához (vagy ellenőrizheti a meglévő alapértelmezett szabályok), a lépések végrehajtása:
1. A Barracuda NG rendszergazdai ügyfél irányítópultján, a konfigurációs lapon található a **működési konfigurációs** szakaszban jelölje be **szabálykészletben**. 

   Rács nevű **fő szabályok** jeleníti meg a meglévő active és inaktiválva szabályok a tűzfalon.

2. Hozzon létre egy új szabályt, jelölje be a kis zöld **+** gomb a rács jobb felső sarkában. (A tűzfalat, előfordulhat, hogy zárolva. Ha egy gomb megjelölve látható **zárolási** és nem lehet létrehozni vagy szabályok szerkesztése, válassza ki a gomb zárolásának feloldása a szabálykészletben és szerkesztésének engedélyezése.)
  
3. Meglévő szabály szerkesztéséhez válassza ki a szabályt, kattintson a jobb gombbal, és válassza ki **szabály szerkesztése**.

Hozzon létre egy új szabályt, hasonló nevű **WebTraffic.** 

A cél NAT-szabály ikonra a következőhöz hasonló: ![Cél NAT ikon][2]

A szabály maga lesz a következőhöz hasonló:

![Tűzfalszabály létrehozása][3]

Találatok száma a HTTP (80-as vagy 443-as HTTPS-port) elérni a tűzfal bármely bejövő cím küldi ki a tűzfalat a DHCP1 helyi IP-kapcsolat lesz, és a rendszer átirányítja a webkiszolgáló 10.0.1.5 IP-címmel. Mivel a forgalom 80-as porton érkezik, és fogja a webalkalmazás-kiszolgáló 80-as porton, nem a port módosítása van szükség. De a cél lista sikerült volt 10.0.1.5:8080, ha a webkiszolgáló a 8080-as porton, amely lefordítja a bejövő 80-as portra a tűzfalon a webkiszolgálón a 8080-as a bejövő portot figyel.

Meg kell adni a kapcsolódás módját. A cél szabály az internetről a dinamikus SNAT a legmegfelelőbb módszer.

Annak ellenére, hogy csak létrehozott egy szabályt, fontos, hogy helyesen prioritási sorrendben. A rácsban, a tűzfal az összes szabály Ha ez a szabály a lap alján (alább BLOCKALL szabály), azt fogja soha nem jut. Ellenőrizze, hogy az új szabályt, a webes forgalom a BLOCKALL szabály felett.

A szabály létrehozása után kell küldje le a tűzfalat, és ezután aktiválja. Ezeket a lépéseket nem kell, ha a szabályt úgy módosítják érvénybe. Ez a szakasz a leküldéses és aktiválási folyamatát ismerteti.

## <a name="rule-activation"></a>A szabály az aktiválás
Most, hogy a szabály a szabálykészletben ad hozzá, akkor kell töltse fel a szabálykészletben a tűzfalat, és aktiválja.

![Tűzfal szabály aktiválás][4]

A felügyeleti ügyfél jobb felső sarokban látni fogja a gombok csoportja. Válassza ki **módosítások küldése** a módosított szabálykészletben küldeni a tűzfalat, és válassza ki a **aktiválás**.

Most, hogy a tűzfal szabálykészletben aktiválta, a környezet számára befejeződött. Ha azt szeretné, a minta alkalmazás parancsfájlok futtathatók a környezet alkalmazások felvétele "Hivatkozások" című szakasz. Ha egy alkalmazás hozzáadását, tesztelheti a következő szakaszban leírt forgalom forgatókönyveket.

> [!IMPORTANT]
> Meg kell vegye figyelembe, hogy közvetlenül addig nem éri a webkiszolgálón. Ha egy böngészőben kér FrontEnd001.CloudApp.Net egy HTTP-lap, a HTTP-végpontot (80-as port) a forgalmat továbbítja, a tűzfal, nem a webkiszolgáló. A tűzfal, majd a korábban létrehozott szabály miatt használ NAT a kérelem leképezése a webkiszolgáló.
> 
> 

## <a name="traffic-scenarios"></a>Forgalom forgatókönyvek
#### <a name="allowed-web-to-web-server-through-the-firewall"></a>(Engedélyezett) Webes webkiszolgáló a tűzfalon keresztül
1. Az internetes felhasználó kér (az internet felé néző felhőszolgáltatás) FrontEnd001.CloudApp.Net egy HTTP-lap.
2. A felhőszolgáltatás egy nyitott végpontok forgalmát továbbítja a tűzfal helyi kapcsolat 10.0.1.4:80 a 80-as porton.
3. Az előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
   1. NSG-szabály 1 (DNS) nem vonatkozik. Helyezze át a következő szabályt.
   2. NSG-szabály 2 (RDP) nem vonatkozik. Helyezze át a következő szabályt.
   3. NSG-szabály 3 (internet tűzfalhoz) a alkalmazni. Engedélyezi a forgalmat. Állítsa le a szabály feldolgozása.
4. Forgalom eléri a tűzfal (10.0.1.4 cím – ez) belső IP-címe.
5. Továbbítási szabály tűzfal határozza meg, hogy ez port 80 forgalmat, amely átirányítja a webkiszolgáló IIS01.
6. IIS01 figyeli a webes forgalmat, a kérelmet kap, és elindítja a kérés feldolgozása.
7. IIS01 információt kér a AppVM01 az SQL Server-példányon.
8. Nincsenek kimenő szabályok az előtérbeli alhálózat, így a forgalom engedélyezve van.
9. A háttérrendszer alhálózatának bejövő szabály feldolgozása kezdődik:
   1. NSG-szabály 1 (DNS) nem vonatkozik. Helyezze át a következő szabályt.
   2. NSG-szabály 2 (RDP) nem vonatkozik. Helyezze át a következő szabályt.
   3. NSG-szabály 3 (internet tűzfalhoz) nem vonatkozik. Helyezze át a következő szabályt.
   4. NSG-szabály 4 (a AppVM01 IIS01) vonatkozik. Engedélyezi a forgalmat. Állítsa le a szabály feldolgozása.
10. AppVM01 az SQL Server-példány megkapja a kérelmet, és válaszol.
11. Mivel nincsenek kimenő szabályok a háttérbeli alhálózat, a válasz használata engedélyezett.
12. Az előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
    1. Nincs NSG-szabály, amelyre vonatkozik bejövő forgalmat a háttérbeli alhálózat felől az előtérben levő alhálózathoz, így egyetlen sem az NSG-szabályokat a alkalmazni.
    2. A alapértelmezett rendszerszabály, amely lehetővé teszi az alhálózatok közötti adatforgalom lehetővé teszi, hogy a forgalmat, így a forgalom engedélyezve van.
13. IIS01 a válasz fogadása a AppVM01, befejezi a HTTP-válasz, és elküldi azokat a kérelmezőnek.
14. Mivel ez a tűzfal NAT munkamenetének, a válasz célpontja kezdetben a tűzfal.
15. A tűzfal a válasz fogadása a webkiszolgáló, és továbbítja azokat a internetes felhasználó számára.
16. Nincsenek kimenő szabályok a FrontEnd alhálózaton, mert a válasz engedélyezve van, és az internet felhasználó kapja meg a weblapot.

#### <a name="allowed-rdp-to-backend"></a>(Engedélyezett) Háttérbeli RDP-vel
1. Az interneten egy kiszolgálói rendszergazda kérelmek AppVM01 BackEnd001.CloudApp.Net az RDP-munkamenetet:*xxxxx*, ahol *xxxxx* AppVM01 RDP-vel a véletlenszerűen hozzárendelt portszámot szól. (Megtalálhatja a hozzárendelt portot az Azure Portalon vagy a PowerShell használatával.)
2. A tűzfal csak az FrontEnd001.CloudApp.Net címet figyeli, mert nem vesz a forgalom flow-val.
3. A háttérrendszer alhálózatának bejövő szabály feldolgozása kezdődik:
   1. NSG-szabály 1 (DNS) nem vonatkozik. Helyezze át a következő szabályt.
   2. NSG-szabály 2 (RDP) a alkalmazni. Engedélyezi a forgalmat. Állítsa le a szabály feldolgozása.
4. Nincsenek kimenő szabályok, mert az alapértelmezett szabályok vonatkoznak, és visszatérhet a forgalom engedélyezve van.
5. Az RDP-munkamenet engedélyezve van.
6. AppVM01 kérni fogja a felhasználónevet és jelszót.

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Engedélyezett) Web server a DNS-címkeresés DNS-kiszolgálón
1. A webes kiszolgáló, IIS01, szükséges, egy data: www.data.gov hírcsatorna, de fel kell oldania a címet.
2. A hálózati konfiguráció a virtuális hálózat látható DNS01 (a háttérbeli alhálózat 10.0.2.4 cím) az elsődleges DNS-kiszolgálóként. IIS01 DNS01 a DNS-kérelmet küld.
3. Mivel az előtérben levő alhálózathoz nincsenek kimenő szabályok, engedélyezi a forgalmat.
4. A háttérrendszer alhálózatának bejövő szabály feldolgozása kezdődik:
   1. NSG-szabály 1 (DNS) vonatkozik. Engedélyezi a forgalmat. Állítsa le a szabály feldolgozása.
5. A DNS-kiszolgáló fogadja a kérelmet.
6. A DNS-kiszolgáló nem rendelkezik a gyorsítótárazott címmel, és a egy legfelső szintű az interneten lévő DNS-kiszolgáló lekérdezi.
7. Nincsenek kimenő szabályok a háttérbeli alhálózat, mivel engedélyezi a forgalmat.
8. Az internetes DNS-kiszolgáló azt válaszolja. Mivel a munkamenet a cégen belül kezdeményezett, a válasz használata engedélyezett.
9. A DNS-kiszolgáló gyorsítótárazza a választ, és a IIS01 válaszol a kérésre.
10. Nincsenek kimenő szabályok a háttérbeli alhálózat, mivel engedélyezi a forgalmat.
11. Az előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
    1. Nincs NSG-szabály, amelyre vonatkozik bejövő forgalmat a háttérbeli alhálózat felől az előtérben levő alhálózathoz, így egyetlen sem az NSG-szabályokat a alkalmazni.
    2. A alapértelmezett rendszerszabály, amely lehetővé teszi az alhálózatok közötti adatforgalom lehetővé teszi, hogy a forgalmat, így a forgalom engedélyezve van.
12. IIS01 DNS01 a választ kap.

#### <a name="allowed-web-server-file-access-on-appvm01"></a>(Engedélyezett) Webkiszolgáló fájl elérése a AppVM01
1. IIS01 kér AppVM01 a fájlt.
2. Mivel az előtérben levő alhálózathoz nincsenek kimenő szabályok, engedélyezi a forgalmat.
3. A háttérrendszer alhálózatának bejövő szabály feldolgozása kezdődik:
   1. NSG-szabály 1 (DNS) nem vonatkozik. Helyezze át a következő szabályt.
   2. NSG-szabály 2 (RDP) nem vonatkozik. Helyezze át a következő szabályt.
   3. NSG-szabály 3 (internet tűzfalhoz) nem vonatkozik. Helyezze át a következő szabályt.
   4. NSG-szabály 4 (a AppVM01 IIS01) vonatkozik. Engedélyezi a forgalmat. Állítsa le a szabály feldolgozása.
4. AppVM01 a kérést kap, és elküldi a fájlt (feltéve, hogy a hozzáférés engedélyezett).
5. Mivel nincsenek kimenő szabályok a háttérbeli alhálózat, a válasz használata engedélyezett.
6. Az előtérbeli alhálózat bejövő szabály feldolgozása kezdődik:
   1. Nincs NSG-szabály, amelyre vonatkozik bejövő forgalmat a háttérbeli alhálózat felől az előtérben levő alhálózathoz, így egyetlen sem az NSG-szabályokat a alkalmazni.
   2. A alapértelmezett rendszerszabály, amely lehetővé teszi az alhálózatok közötti adatforgalom lehetővé teszi, hogy a forgalmat, így a forgalom engedélyezve van.
7. IIS01 kap a fájl.

#### <a name="denied-web-direct-to-web-server"></a>(Tiltott) Web-direct webkiszolgáló
A webkiszolgáló IIS01 és a tűzfal is ugyanazon a felhőszolgáltatáson, mert az azonos nyilvános IP-cím osztoznak. Tehát bármilyen HTTP-forgalmat a tűzfal van átirányítva. Kérelem sikeresen kiszolgált lenne, amíg azt nem válthat közvetlenül a webkiszolgálón. Továbbítja, zavartalan a tűzfalon keresztül először. Tekintse meg az első forgatókönyv szerint ebben a szakaszban az adatforgalmat.

#### <a name="denied-web-to-backend-server"></a>(Tiltott) Webes háttérkiszolgálóra
1. Az internetes felhasználó megkísérel hozzáférni AppVM01 egy fájlt a BackEnd001.CloudApp.Net szolgáltatáson keresztül.
2. Végpontleképzőben nincs nyissa meg a fájlmegosztás, mert ez nem adja át a felhőalapú szolgáltatás, és a kiszolgáló nem érhető el.
3. Ha valamilyen okból a végpontok nyitva, NSG-szabály (internet, virtuális hálózat) 5 blokkolja a forgalmat.

#### <a name="denied-web-dns-lookup-on-the-dns-server"></a>(Tiltott) A DNS-kiszolgáló DNS-címkeresés webes
1. An internet user tries to look up an internal DNS record on DNS01 through the BackEnd001.CloudApp.Net service.
2. Nincsenek nyissa meg a DNS-végpontok, mert ez nem adja át a felhőalapú szolgáltatás, és a kiszolgáló nem érhető el.
3. Ha valamilyen okból a végpontok nyitva, NSG-szabály (internet, virtuális hálózat) 5 blokkolja a forgalmat. (1 [DNS] szabály két okból nem vonatkozik. Először a forrás címe az interneten, és ez a szabály vonatkozik, csak ha a helyi virtuális hálózaton a forrás. Második Ez a szabály egy engedélyezési szabályt, így soha nem tiltja a forgalmat.)

#### <a name="denied-web-to-sql-access-through-the-firewall"></a>(Tiltott) Webalkalmazás, SQL-hozzáféréssel a tűzfalon keresztül
1. Az internetes felhasználó SQL adatokat kér FrontEnd001.CloudApp.Net (egy internetkapcsolattal rendelkező felhőalapú szolgáltatás).
2. Végpontleképzőben nincs nyitott meg SQL esetén, mert ez nem adja át a felhőalapú szolgáltatás, és a tűzfal nem érhető el.
3. Ha valamilyen okból a végpontok nyitva, az előtérben levő alhálózathoz elindítja a bejövő szabály feldolgozása:
   1. NSG-szabály 1 (DNS) nem vonatkozik. Helyezze át a következő szabályt.
   2. NSG-szabály 2 (RDP) nem vonatkozik. Helyezze át a következő szabályt.
   3. NSG-szabály 3 (internet tűzfalhoz) a alkalmazni. Engedélyezi a forgalmat. Állítsa le a szabály feldolgozása.
4. Forgalom eléri a tűzfal (10.0.1.4 cím – ez) belső IP-címe.
5. A tűzfal nem tartozik továbbítási szabály meg SQL esetén, csökken a forgalom.

## <a name="conclusion"></a>Összegzés
Ez a példa bemutatja az alkalmazás egy tűzfal védelme, és a bejövő forgalmat a háttérbeli alhálózat elkülönítése viszonylag kézenfekvő módot.

Talál további példákat és a hálózat áttekintése biztonsági határokat [Itt][HOME].

## <a name="references"></a>Referencia
### <a name="full-script-and-network-config"></a>Teljes szkript és a hálózati konfiguráció
Mentse a teljes szkriptet egy PowerShell-parancsfájlt. Mentse a hálózati konfigurációs parancsfájl NetworkConf2.xml nevű fájlt.
Szükség szerint változtassa meg a felhasználó definiált-változókat. Futtassa a szkriptet, és kövesse az utasításokat, ez a cikk "Tűzfalszabályok" szakaszában.

#### <a name="full-script"></a>Teljes szkript
Ez a szkript a felhasználó által definiált változókat alapján lesz kövesse az alábbi lépéseket:

1. Csatlakozhat egy Azure-előfizetést.
2. Tárfiók létrehozása.
3. Hozzon létre egy virtuális hálózatot és két alhálózatot a hálózati konfigurációs fájlban megadott.
4. Négy Windows Server rendszerű virtuális gépek létrehozása.
5. Állítsa be az NSG-t. Konfigurálás befejeződik a következő lépéseket:
   * Létrehoz egy NSG-t.
   * Az NSG-szabályokkal tölti fel.
   * Az NSG-t összekapcsolja a megfelelő alhálózatokat.

Ez a PowerShell-szkript helyileg kell futtatnia egy internetkapcsolattal rendelkező számítógépen vagy kiszolgálón.

> [!IMPORTANT]
> Ez a szkript futtatásakor a figyelmeztetések és a többi tájékoztató üzenet jelenhet meg PowerShell. Csak kell aggódnia, amelyeket a piros színnel jelennek meg.
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of a perimeter network and Network Security Groups in an isolated network. (Azure only. No hybrid connections.)

     .DESCRIPTION
      This script will build out a sample perimeter network setup containing:
       - A default storage account for VM disks.
       - Two new cloud services.
       - Two subnets (the FrontEnd and BackEnd subnets).
       - A network virtual appliance (NVA): a Barracuda NextGen Firewall.
       - One server on the FrontEnd subnet (plus the NVA on the FrontEnd subnet).
       - Three servers on the BackEnd subnet.
       - Network Security Groups to allow/deny traffic patterns as declared.

      Before running the script, ensure the network configuration file is created in
      the directory referenced by the $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in many ways. Be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that you can use, but it should not be used for all scenarios. You
      are responsible for assessing your security needs and the appropriate protections
      and then effectively implementing those protections.

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

    # User-Defined Global Variables
      # These should be changed to reflect your subscription and services.
      # Invalid options will fail in the validation section.

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

    # User-Defined VM-Specific Config
        # To ensure proper NSG rule creation later in this script:
        #       - The web server must be VM 1.
        #       - The AppVM1 server must be VM 2.
        #       - The DNS server must be VM 4.
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG rule IP addresses
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
    # No user-defined variables or   #
    # configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create storage account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update subscription pointer to new storage account
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
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create virtual network
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create services
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
                # Set up all the endpoints we'll need once we're up and running.
                # Note: Web traffic goes through the firewall, so we'll need to set up an HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: An SSH endpoint is automatically created on port 22 when the appliance is created.
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

      # Add NSG rules
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

        # Assign the NSG to the subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-Script Manual Configuration
      # Configure firewall
      # Install test web app (run post-build script on the IIS server)
      # Install back-end resources (run post-build script on AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

#### <a name="network-config-file"></a>Hálózati konfigurációs fájl
Mentse az XML-fájlt a frissített helyek, és adja hozzá a hivatkozás ehhez a fájlhoz, az előző szkriptben $NetworkConfigFile változóban.

```xml
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
Ha szeretne egy mintaalkalmazás telepítése ezzel és más szegélyhálózat-alapú hálózati példák, tekintse meg a [alkalmazás mintaparancsfájl][SampleApp].

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "NSG bejövő Szegélyhálózat"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Cél NAT ikon"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Tűzfalszabály"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Tűzfal szabály aktiválás"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
