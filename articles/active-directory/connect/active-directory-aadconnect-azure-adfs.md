---
title: "Az Active Directory összevonási szolgáltatások az Azure-ban | Microsoft Docs"
description: "Ebből a dokumentumból megtanulhatja, hogyan helyezze üzembe az AD FS szolgáltatást az Azure-ban, és biztosítson ezzel magas fokú rendelkezésre állást."
keywords: "AD FS üzembe helyezése azure-ban, azure adfs üzembe helyezése, azure adfs, azure ad fs, adfs üzembe helyezése, ad fs üzembe helyezése, adfs azure-ban, adfs üzembe helyezése azure-ban, AD FS üzembe helyezése azure-ban, adfs azure, az AD FS bemutatása, Azure, AD FS az Azure-ban, iaas, ADFS, adfs áthelyezése az azure-ba"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: 692a188c-badc-44aa-ba86-71c0e8074510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2017
ms.author: anandy;billmath
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: b9a3b64d9de48f17a295ca7a9ea58cf26e8f83ed
ms.openlocfilehash: 7a7ac3f34860cedb05e9e4423ca7878b2ed5913a
ms.lasthandoff: 02/28/2017

---
# <a name="deloying-active-directory-federation-services-in-azure"></a>Az Azure-ban található Active Directory összevonási szolgáltatások üzembe helyezése
Az AD FS egyszerű, mégis biztonságos identitás-összevonást, valamint webes egyszeri bejelentkezési (SSO) funkciókat biztosít. Az Azure AD vagy O365 segítségével megvalósított összevonás lehetővé teszi a felhasználóknak a helyszíni bejelentkezési adatok segítségével történő hitelesítést, valamint a felhőben futó erőforrások elérését. Ezért fontos, hogy magas rendelkezésre állást biztosító AD FS-infrastruktúrát alkalmazzon, amely garantálja a helyszíni és a felhőben lévő erőforrások elérhetőségét. Az AD FS Azure-ban történő üzembe helyezésével minimális erőfeszítéssel kialakíthatja a magas rendelkezésre állást.
Az AD FS Azure-ban történő üzembe helyezése számos előnnyel jár. Alább ezek közül sorolunk fel néhányat:

* **Magas rendelkezésre állás** – Az Azure rendelkezésre állási csoportok erejével garantálhatja az infrastruktúra magas rendelkezésre állását.
* **Egyszerű skálázás** – Nagyobb teljesítményre van szüksége? Az Azure-ban mindössze néhány kattintással erősebb gépekre válthat.
* **Különböző régiók közötti redundancia** – Az Azure Geo Redundancy szolgáltatása globális léptékű magas rendelkezésre állást garantál.
* **Egyszerű felügyelet** – Az Azure Portal rendkívül könnyen használható felügyeleti funkciói révén az infrastruktúra kezelése kiemelkedően egyszerű és problémamentes feladat. 

## <a name="design-principles"></a>Tervezési alapelvek
![Az üzembe helyezés megtervezése](./media/active-directory-aadconnect-azure-adfs/deployment.png)

A fenti diagram az AD FS-infrastruktúra Azure-ban való üzembe helyezéséhez javasolt alapszintű topológiát mutatja be. Alább részletesen is leírjuk a topológia különböző részei mögött álló elveket:

* **Tartományvezérlő/ADFS-kiszolgáló**: ha ezernél kevesebb felhasználóval rendelkezik, egyszerűen telepítse az AD FS szerepkört a tartományvezérlőkre. Ha nem szeretné, hogy ez negatív hatással legyen a tartományvezérlők teljesítményére, illetve ha ezernél több felhasználóval rendelkezik, különálló kiszolgálókon helyezze üzembe az AD FS-t.
* **WAP-kiszolgáló**: a webalkalmazásproxy-kiszolgálókat azért kell üzembe helyeznie, hogy a felhasználók akkor is elérjék az AD FS-t, ha nem a vállalati hálózaton tevékenykednek.
* **DMZ**: a webalkalmazásoproxy-kiszolgálók a DMZ-be kerülnek, és a rendszer KIZÁRÓLAG TCP/443 hozzáférést engedélyez a DMZ és a belső alhálózat között.
* **Terheléselosztók**: az AD FS és a webalkalmazásproxy-kiszolgálók magas rendelkezésre állásának garantálása érdekében javasoljuk, hogy az AD FS-kiszolgálókon belső terheléselosztót, míg a webalkalmazásokhoz tartozó proxykiszolgálókon Azure Load Balancert használjon.
* **Rendelkezésre állási csoportok**: az AD FS üzemelő példánya redundanciájának garantálása érdekében javasoljuk, hogy a hasonló alkalmazások és szolgáltatások működtetésére foglaljon legalább két virtuális gépet rendelkezésre állási csoportba. Így legalább egy virtuális gép mindig elérhető lesz a tervezett vagy nem tervezett karbantartási események esetében is.
* **Tárfiókok**: javasoljuk, hogy rendelkezzen legalább két tárfiókkal. Ha csupán egyetlen tárfiókot használ, azzal rendszerkritikus meghibásodási pontot hozhat létre, amely ahhoz vezethet, hogy az üzemelő példány nem fog rendelkezésre állni, ha bekövetkezik az a valószínűtlen eset, hogy a tárfiók működése leáll. Azzal, ha két tárfiókot használ, lefedheti mind a két meghibásodási lehetőséget.
* **Hálózatok szétválasztása**: a webalkalmazásproxy-kiszolgálókat eltérő DMZ-hálózatokban helyezze üzembe. Osszon egy virtuális hálózatot két alhálózatra, majd a webalkalmazásproxy-kiszolgáló(ka)t helyezze üzembe az elszigetelt alhálózatban. Ezt követően egyszerűen megadhatja a hálózati biztonsági csoportok beállításait az egyes alhálózatokra vonatkozóan, és konfigurálhatja, hogy a rendszer csak a szükséges kommunikációt engedélyezze a két alhálózat között. Alább részletes információkat is megtudhat ezzel kapcsolatban az egyes üzemelőpéldány-típusokra vonatkozóan.

## <a name="steps-to-deploy-ad-fs-in-azure"></a>Az AD FS Azure-ban való üzembe helyezésének lépései
Az ebben a részben szereplő lépések útmutatást nyújtanak az alábbiakban leírt AD FS-infrastruktúra Azure-ban való üzembe helyezéséhez.

### <a name="1-deploying-the-network"></a>1. A hálózat üzembe helyezése
Ahogy fent már leírtuk, hozzon létre két, egyetlen virtuális hálózathoz tartozó különböző alhálózatot, vagy két teljesen különálló virtuális hálózatot (VNet). Ebben a cikkben egyetlen virtuális hálózatot hozunk létre, amelyet aztán két alhálózatra bontunk. Jelenleg ez az egyszerűbb megoldás, mivel két önálló virtuális hálózat esetében átjáróra lenne szükség a virtuális hálózatok között a kommunikációhoz.

**1.1 Virtuális hálózat létrehozása**

![Virtuális hálózat létrehozása](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)

Az Azure Portal webhelyen mindössze ki kell választania a kívánt virtuális hálózatot, amelyet aztán egy alhálózattal együtt akár egyetlen kattintással is üzembe helyezhet. Az INT alhálózat már definiálva van, és készen áll a virtuális gépek fogadására.
A következő lépés, hogy egy másik alhálózatot vegyünk fel a hálózatba. Ez lesz a DMZ-alhálózat. A DMZ-alhálózat létrehozásához egyszerűen tegye a következőket:

* Válassza ki az újonnan létrehozott hálózatot.
* A Tulajdonságok között válassza az Alhálózat lehetőséget.
* Az alhálózat paneljén kattintson a Hozzáadás gombra.
* Az alhálózat létrehozásához adja meg az alhálózat nevét és a címtartományt.

![Alhálózat](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)

![DMZ-alhálózat](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. Hálózati biztonsági csoportok létrehozása**

A hálózati biztonsági csoport (NSG) tartalmazza a hozzáférés-vezérlési (ACL) szabályok listáját, amelyek megszabják, hogy milyen típusú hálózati forgalom érhesse el a virtuális hálózatban futó virtuálisgép-példányokat. Az NSG-ket alhálózatokhoz vagy az alhálózaton belüli virtuálisgép-példányokhoz lehet hozzárendelni. Ha az NSG-t hozzárendelik egy alhálózathoz, az ACL-szabályok érvényesek lesznek az alhálózatban lévő összes virtuálisgép-példányra.
Ebben az útmutatóban két NSG-t fogunk létrehozni: egyet-egyet a belső hálózathoz és a DMZ-hez. Ezek neve rendre NSG_INT, illetve NSG_DMZ lesz.

![Az NSG létrehozása](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

Az NSG létrehozását követően 0 bejövő és 0 kimenő szabály lesz látható. Ha telepítette és működésbe hozta a szerepköröket a megfelelő kiszolgálókon, a kívánt biztonsági szintnek megfelelően állítsa be a bejövő és kimenő forgalomra vonatkozó szabályokat.

![Az NSG inicializálása](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

Az NSG-k létrehozását követően társítsa az NSG_INT csoportot az INT-alhálózathoz, az NSG_DMZ csoportot pedig a DMZ-alhálózathoz. Alább láthatja az ezt bemutató képernyőképet:

![Az NSG konfigurálása](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* Kattintson az Alhálózatok lehetőségre az alhálózatok paneljének megnyitásához.
* Válassza ki az NSG-hez társítani kívánt alhálózatot. 

A konfigurációt követően az Alhálózatok panelnek a következő módon kell megjelennie:

![Alhálózatok panel az NSG társítását követően](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. Kapcsolat létrehozása a helyszíni rendszerrel**

A tartományvezérlő (DC) Azure-ban való üzembe helyezéséhez kapcsolatot kell kialakítanunk a helyszíni rendszerrel. Az Azure számos kapcsolódási lehetőséget kínál, amelyek segítségével összekötheti a helyszíni és az Azure-infrastruktúrát.

* Pont–hely kapcsolat
* Virtuális hálózat, helyek közötti kapcsolat
* ExpressRoute

Javasoljuk, hogy használja az ExpressRoute megoldást. Az ExpressRoute használatával privát kapcsolatok hozhatók létre az Azure-adatközpontok és a helyszíni vagy a bérelt kiszolgálói környezetben üzemelő infrastruktúra között. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Ezért ezek a kapcsolatok megbízhatóbbak, mint az átlagos internetes kapcsolatok, ráadásul jobb a teljesítményük, rövidebb a válaszidejük, és magasabb fokú biztonságot nyújtanak.
Habár mi az ExpressRoute használatát javasoljuk, bármelyik kapcsolódási módszert választhatja, ha az jobban megfelel szervezete igényeinek. Ha többet szeretne megtudni az ExpressRoute-ról és az ExpressRoute által kínált különböző kapcsolódási lehetőségekről, olvassa el [Az ExpressRoute technikai áttekintése](https://aka.ms/Azure/ExpressRoute) című cikket.

### <a name="2-create-storage-accounts"></a>2. Tárfiókok létrehozása
A magas rendelkezésre állás fenntartása érdekében hozzon létre két tárfiókot, így a rendszer elérhetősége sosem egyetlen tárfióktól fog függni. Az egyes rendelkezésre állási csoportokhoz tartozó gépeket ossza két csoportra, majd rendeljen a csoportokhoz egy-egy tárfiókot.

![Tárfiókok létrehozása](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. Rendelkezésre állási csoportok létrehozása
Hozzon létre egyenként legalább 2 gépet tartalmazó rendelkezésre állási csoportokat a szerepkörök számára (tartományvezérlő/AD FS és WAP). Így a szerepkörök magasabb fokú rendelkezésre állást tudnak garantálni. A rendelkezésre állási csoportok létrehozása előtt határozza meg a következő paramétereket:

* **Tartalék tartományok**: az azonos tartalék tartományba tartozó virtuális gépek ugyanazt az áramforrást és fizikai hálózati kapcsolót használják. Javasoljuk, hogy használjon legalább 2 különböző tartalék tartományt. A beállítás alapértelmezett értéke a 3, ennél az üzemelő példánynál meghagyhatja ezt.
* **Frissítési tartományok**: az azonos frissítési tartományhoz tartozó gépeket a rendszer egyszerre indítja újra a frissítés során. Érdemes legalább 2 különböző frissítési tartományt használni. A beállítás alapértelmezett értéke az 5, ennél az üzemelő példánynál meghagyhatja ezt.

![Rendelkezésre állási csoportok](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

Hozza létre a következő rendelkezésre állási csoportokat:

| Rendelkezésre állási csoport | Szerepkör | Tartalék tartományok | Frissítési tartományok |
|:---:|:---:|:---:|:--- |
| contosodcset |Tartományvezérlő/ADFS |3 |5 |
| contosowapset |WAP |3 |5 |

### <a name="4----deploy-virtual-machines"></a>4.    Virtuális gépek üzembe helyezése
A következő lépés, hogy üzembe helyezzük a virtuális gépeket, amelyek futtatni fogják az infrastruktúra különböző szerepköreit. Mindegyik rendelkezésre állási csoportban használjon legalább két gépet. Az alapszintű üzemelő példányhoz hozzon létre hat virtuális gépet.

| Gép | Szerepkör | Alhálózat | Rendelkezésre állási csoport | Tárfiók | IP-cím |
|:---:|:---:|:---:|:---:|:---:|:---:|
| contosodc1 |Tartományvezérlő/ADFS |INT |contosodcset |contososac1 |Statikus |
| contosodc2 |Tartományvezérlő/ADFS |INT |contosodcset |contososac2 |Statikus |
| contosowap1 |WAP |DMZ |contosowapset |contososac1 |Statikus |
| contosowap2 |WAP |DMZ |contosowapset |contososac2 |Statikus |

Talán észrevette, hogy egyetlen NSG-t sem adtunk meg. Ez azért történt így, mert az Azure-ban az NSG-ket az alhálózatok szintjén is használhatja. Így az alhálózathoz vagy az NIC-objektumhoz társított különböző NSG-k segítségével az egyes gépek hálózati forgalmát is szabályozhatja. További információk: [Mi az a hálózati biztonsági csoport (NSG)?](https://aka.ms/Azure/NSG).
Ha Ön kezeli a DNS-t, javasoljuk, hogy statikus IP-címeket használjon. Használhatja az Azure DNS-t is, és a tartomány DNS-rekordjaiban az Azure-beli teljes tartománynevük alapján is hivatkozhat az új gépekre.
Az üzembe helyezés befejezését követően a virtuális gépek paneljének az alábbihoz hasonlóan kell megjelennie:

![Virtuális gépek üzembe helyezve](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5. Tartományvezérlő-/AD FS-kiszolgálók konfigurálása
 A bejövő kérések hitelesítéséhez az AD FS-nek kapcsolatban kell állnia a tartományvezérlővel. Javasoljuk, hogy helyezze üzembe az Azure-ban a tartományvezérlő replikáját, mivel ezzel hitelesítés alkalmával elkerülheti a költséges utat az Azure és a helyszíni tartományvezérlő között. A magas rendelkezésre állás kialakítása érdekében javasoljuk, hogy hozzon létre egy legalább 2 tartományvezérlőt tartalmazó rendelkezésre állási csoportot.

| Tartományvezérlő | Szerepkör | Tárfiók |
|:---:|:---:|:---:|
| contosodc1 |Replika |contososac1 |
| contosodc2 |Replika |contososac2 |

* Léptesse elő a két kiszolgálót replika tartományvezérlőnek DNS-sel.
* Konfigurálja az AD FS-kiszolgálókat: telepítse az AD FS szerepkört a Kiszolgálókezelővel.

### <a name="6----deploying-internal-load-balancer-ilb"></a>6.    A belső terheléselosztó (ILB) üzembe helyezése
**6.1.    Az ILB létrehozása**

ILB üzembe helyezéséhez válassza a Terheléselosztók lehetőséget az Azure Portal webhelyen, majd kattintson a hozzáadás (+) ikonra.

> [!NOTE]
> Ha a menüben nem látja a **Terheléselosztók** lehetőséget, kattintson a portálon bal oldalt alul található **Tallózás** gombra, és görgessen lefelé, amíg meg nem látja a **Terheléselosztók** elemet.  Ha megtalálta, kattintson a sárga csillagra. Ezzel a lehetőség bekerül a menübe. Most az új terheléselosztó ikont választva nyissa meg a panelt. Ezen megkezdheti a terheléselosztó konfigurálását.
> 
> 

![Terheléselosztó tallózása](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **Név**: adjon megfelelő nevet a terheléselosztónak.
* **Séma**: mivel a terheléselosztót az AD FS-kiszolgálók elé fogja helyezni, és CSAK a belső hálózati kapcsolatok kezelésére fogja használni, válassza a „Belső” lehetőséget.
* **Virtuális hálózat**: adja meg a virtuális hálózatot, amelyben üzembe szeretné helyezni az AD FS-t.
* **Alhálózat**: adja meg a belső alhálózatot.
* **IP-címkiosztás**: dinamikus.

![Belső terheléselosztó](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)

Miután rákattint a Létrehozás gombra, a rendszer üzembe helyezi az ILB-t. Ezt követően meg kell jelennie a terheléselosztók listájában:

![Terheléselosztók listája az ILB létrehozását követően](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)

A következő lépés a háttérkészlet és a háttérmintavétel beállítása.

**6.2.    Az ILB-háttérkészlet konfigurálása**

Válassza ki az újonnan létrehozott ILB-t a Terheléselosztók panelen. Megnyílik a beállítások panelje. 

1. Válasszon a beállítási panelen elérhető háttérkészletek közül.
2. A Háttérkészlet hozzáadása panelen kattintson a Virtuális gép felvétele lehetőségre.
3. Megjelenik egy panel, amelyen kiválaszthatja a rendelkezésre állási csoportot.
4. Válassza az AD FS rendelkezésre állási csoportját.

![Az ILB-háttérkészlet konfigurálása](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)

**6.3.    A mintavétel konfigurálása**

Az ILB beállítási paneljén válassza a Mintavételek lehetőséget.

1. Kattintson a Hozzáadás gombra.
2. Adja meg a mintavétel adatait. a. **Név**: a mintavétel neve. b. **Protokoll**: TCP. c. **Port**: 443 (HTTPS). d. **Időköz**: 5 (alapértelmezett érték) – ez az az időköz, amelynek elteltével az ILB mintavételt végez a gépeken, az „e” háttérkészletben. **Nem kifogástalan állapot küszöbértéke**: 2 (alapértelmezett érték) – ez az a küszöbérték, amely meghatározza, hogy az ILB hány egymást követő mintavételi hiba után deklarálja, hogy a gép nem válaszol. A nem válaszoló gépre a rendszer nem irányít forgalmat.

![Az ILB-mintavétel konfigurálása](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)

**6.4.    Terheléselosztási szabályok létrehozása**

A forgalom hatékony elosztása érdekében állítson be terheléselosztási szabályokat az ILB-n. Terheléselosztási szabály létrehozása: 

1. Az ILB beállítási paneljén válassza a Terheléselosztási szabály lehetőséget.
2. A Terheléselosztási szabály panelen kattintson a Hozzáadás gombra.
3. A Minden terheléselosztási szabály panelen: a. **Név**: adja meg a szabály nevét. b. **Protokoll**: válassza a TCP lehetőséget. c. **Port**: 443. d. **Háttérport**: 443. e. **Háttérkészlet**: válassza ki az AD FS-fürthöz korábban létrehozott készletet. f. **Mintavétel**: válassza ki az AD FS-kiszolgálókhoz korábban létrehozott mintavételt.

![ILB-terheléselosztási szabályok konfigurálása](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5.    A DNS frissítése az ILB-vel**

Lépjen be a DNS-kiszolgálóra, és hozzon létre egy CNAME-et az ILB számára. A CNAME az összevonási szolgáltatáshoz tartozzon, az IP-cím pedig mutasson az ILB IP-címére. Ha például az ILB DIP-címe 10.3.0.8, a telepített összevonási szolgáltatás pedig az fs.contoso.com, hozzon létre egy CNAME-et az fs.contoso.com-hoz, és mutasson a 10.3.0.8 címre.
Ezzel garantálja, hogy az fs.contoso.com-ot érintő kommunikáció eljut az ILB-re, és a megfelelő utat járja be.

### <a name="7----configuring-the-web-application-proxy-server"></a>7.    A webalkalmazásproxy-kiszolgálók konfigurálása
**7.1.    A webalkalmazásproxy-kiszolgálók konfigurálása az AD FS-kiszolgálók elérésére**

Hogy a webalkalmazásproxy-kiszolgálók el tudják érni az ILB mögötti AD FS-kiszolgálókat, hozzon létre egy rekordot a %systemroot%\system32\drivers\etc\hosts könyvtárban az ILB számára. A megkülönböztető név (DN) legyen az összevonási szolgáltatás neve, például fs.contoso.com. IP-címként pedig az ILB IP-címét (a példában&10;.3.0.8) adja meg.

**7.2.    A webalkalmazás-proxy szerepkör telepítése**

Miután beállította, hogy a webalkalmazásproxy-kiszolgálók képesek legyenek elérni az ILB mögötti AD FS-kiszolgálókat, telepítheti a webalkalmazásproxy-kiszolgálókat. A webalkalmazásproxy-kiszolgálókat nem szükséges csatlakoztatni a tartományhoz. Telepítse a két webalkalmazásproxy-kiszolgálóra a webalkalmazás-proxy szerepköröket. Ehhez válassza a Távelérés szerepkört. A kiszolgálókezelő végigvezeti a WAP telepítésén.
A WAP üzembe helyezésével kapcsolatos további információkért olvassa el az [Install and Configure the Web Application Proxy Server](https://technet.microsoft.com/library/dn383662.aspx) (A webalkalmazás-proxy kiszolgálók telepítése és konfigurálása) című cikket.

### <a name="8----deploying-the-internet-facing-public-load-balancer"></a>8.    Az internetre irányuló (nyilvános) terheléselosztó üzembe helyezése
**8.1.    Az internetre irányuló (nyilvános) terheléselosztó létrehozása**

Az Azure Portal webhelyen válassza a Terheléselosztók lehetőséget, majd kattintson a Hozzáadás gombra. A Terheléselosztó létrehozása panelen adja meg az alábbi adatokat:

1. **Név**: a terheléselosztó neve.
2. **Séma**: Nyilvános – ezzel a beállítással közölheti az Azure-ral, hogy a terheléselosztóhoz nyilvános cím szükséges.
3. **IP-cím**: hozzon létre új (dinamikus) IP-címet.

![Internetre irányuló terheléselosztó](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

Az üzembe helyezést követően a terheléselosztó megjelenik a Terheléselosztók listában.

![Terheléselosztók listája](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)

**8.2.    DNS-címke hozzárendelése a nyilvános IP-címhez**

A Terheléselosztók panelen kattintson az újonnan létrehozott terheléselosztó bejegyzésére. Megnyílik a konfigurációs panel. A nyilvános IP-cím DNS-címkéjének beállításához kövesse az alábbi lépéseket:

1. Kattintson a nyilvános IP-címre. Ezzel megnyitja a nyilvános IP-cím beállításait tartalmazó panelt.
2. Kattintson a Konfiguráció lehetőségre.
3. Adja meg a DNS-címkét. Ez lesz a nyilvános, bárhonnan elérhető DNS-címke, például contosofs.westus.cloudapp.azure.com. Felvehet egy bejegyzést az összevonási szolgáltatás külső DNS-e (például fs.contoso.com) számára, amelyet a rendszer a külső terheléselosztó DNS-címkéjéhez rendel (contosofs.westus.cloudapp.azure.com).

![Az internetre irányuló terheléselosztó konfigurálása](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Az internetre irányuló terheléselosztó konfigurálása (DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3.    Az internetre irányuló (nyilvános) terheléselosztó háttérkészletének konfigurálása** 

Az internetre irányuló (nyilvános) terheléselosztó háttérkészletének a WAP-kiszolgálóknál használt rendelkezésre állási csoportként való beállításához végezze el ugyanazokat a lépéseket, amelyeket a belső terheléselosztó létrehozásakor bemutattunk. Példa: contosowapset.

![Az internetre irányuló terheléselosztó háttérkészletének konfigurálása](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)

**8.4.    Mintavétel konfigurálása**

A WAP-kiszolgálók háttérkészletéhez tartozó mintavétel konfigurálásához végezze el a belső terheléselosztó konfigurálására vonatkozó lépéseket.

![Az internetre irányuló terheléselosztó mintavételének konfigurálása](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)

**8.5.    Terheléselosztási szabály(ok) létrehozása**

A 443-as TCP-portra vonatkozó terheléselosztási szabályok konfigurálásához végezze el az ILB esetében ismertetett lépéseket.

![Az internetre irányuló terheléselosztó terheléselosztási szabályainak konfigurálása](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)

### <a name="9----securing-the-network"></a>9.    A hálózat biztonságának beállítása
**9.1.    A belső alhálózat biztonságának beállítása**

Általánosságban elmondható, hogy a belső alhálózat biztonságának kialakításához a következő szabályok szükségesek (a lenti sorrendben).

| Szabály | Leírás | Folyamat |
|:--- |:--- |:---:|
| AllowHTTPSFromDMZ |A DMZ felől érkező HTTPS-kommunikáció engedélyezése |Bejövő |
| DenyInternetOutbound |Tiltja az internet-hozzáférést |Kimenő |

![INT-hozzáférési szabályok (bejövő)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

[comment]: <> (![INT-hozzáférési szabályok (bejövő)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png)) [comment]: <> (![INT-hozzáférési szabályok (kimenő)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))

**9.2.    A DMZ-alhálózat biztonságának beállítása**

| Szabály | Leírás | Folyamat |
|:--- |:--- |:---:|
| AllowHTTPSFromInternet |Az internet felől a DMZ-be irányuló HTTPS-forgalom engedélyezése |Bejövő |
| DenyInternetOutbound |A HTTPS-től eltérő típusú forgalom tiltása az internet felé |Kimenő |

![EXT-hozzáférési szabályok (bejövő)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

[comment]: <> (![EXT-hozzáférési szabályok (bejövő)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png)) [comment]: <> (![EXT-hozzáférési szabályok (kimenő)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))

> [!NOTE]
> Ha az ügyfelek esetében felhasználói tanúsítvány hitelesítésére (clientTLS típusú hitelesítés X509 felhasználói tanúsítványok használatával) van szükség, az AD FS-nél engedélyezni kell a 49443-as TCP-porton a bejövő hozzáférést.
> 
> 

### <a name="10----test-the-ad-fs-sign-in"></a>10.    Az AD FS-bejelentkezés tesztelése
Az AD FS legegyszerűbben az IdpInitiatedSignon.aspx oldal segítségével tesztelhető. Ahhoz, hogy ez megvalósítható legyen, engedélyezze az IdpInitiatedSignOn tulajdonságot az AD FS tulajdonságaiban. Az AD FS beállításának ellenőrzéséhez kövesse az alábbi lépéseket:

1. Az engedélyezéshez futtassa az alábbi parancsmagot az AD FS-kiszolgálón a PowerShell segítségével.
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true 
2. Egy külső gépről nyissa meg a következő címet: https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx.  
3. Az AD FS oldalának az alábbi módon kell megjelennie:

![Bejelentkezési lap tesztelése](./media/active-directory-aadconnect-azure-adfs/test1.png)

A sikeres bejelentkezést követően a rendszer az alább látható üzenetet jeleníti meg:

![Teszt sikeres](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Sablon az AD FS üzembe helyezéséhez az Azure-ban
A sablon egy 6 gépes beállítást helyez üzembe, 2 a tartományvezérlőkhöz, 2 az AD FS-hez és 2 a WAP-hoz.

[AD FS az Azure Deployment Template-ben](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

A sablon telepítése közben használhat egy meglévő virtuális hálózatot, vagy létrehozhat egy újat. Alább látható az üzembe helyezés testreszabásához rendelkezésre álló különféle paraméterek listája, a paraméter üzembe helyezési folyamatban való használatának leírásával együtt. 

| Paraméter | Leírás |
|:--- |:--- |
| Hely |Az erőforrások üzembe helyezésének régiója, például az USA keleti régiója. |
| StorageAccountType |A létrehozott tárfiók típusa |
| VirtualNetworkUsage |Jelzi, hogy új virtuális hálózat lesz-e létrehozva vagy egy meglévő kerül használatra |
| VirtualNetworkName |A létrehozandó virtuális hálózat neve, kötelező mind meglévő, mind új virtuális hálózat használata esetén |
| VirtualNetworkResourceGroupName |Megadja az erőforráscsoport nevét, ahol a meglévő virtuális hálózat található. Ha meglevő virtuális hálózatot használ, ez kötelező paraméterré válik, hogy az üzembe helyezés meg tudja találni a meglévő virtuális hálózat azonosítóját |
| VirtualNetworkAddressRange |Az új virtuális hálózat címtartománya, kötelező ha új virtuális hálózatot hoz létre |
| InternalSubnetName |A belső alhálózat neve, kötelező mindkét virtuális hálózat használat lehetőség esetén (új vagy meglévő) |
| InternalSubnetAddressRange |A belső alhálózat címtartománya, ami tartalmazza a tartományvezérlőket és az ADFS-kiszolgálókat, kötelező új virtuális hálózat létrehozásakor. |
| DMZSubnetAddressRange |A dmz alhálózat címtartománya, ami tartalmazza a Windows alkalmazás proxykiszolgálókat, kötelező új virtuális hálózat létrehozásakor. |
| DMZSubnetName |A belső alhálózat neve, kötelező mindkét virtuális hálózat használat lehetőség esetén (új vagy meglévő). |
| ADDC01NICIPAddress |Az első tartományvezérlő belső IP-címe, ez az IP-cím statikusan hozzá lesz rendelve a Tartományvezérlőhöz, valamint valós IP-címnek kell lennie a belső alhálózaton belül |
| ADDC02NICIPAddress |A második tartományvezérlő belső IP-címe, ez az IP-cím statikusan hozzá lesz rendelve a Tartományvezérlőhöz, valamint valós IP-címnek kell lennie a belső alhálózaton belül |
| ADFS01NICIPAddress |Az első ADFS-kiszolgáló belső IP-címe, ez az IP-cím statikusan hozzá lesz rendelve a ADFS-kiszolgálóhoz, valamint valós IP-címnek kell lennie a belső alhálózaton belül |
| ADFS02NICIPAddress |A második ADFS-kiszolgáló belső IP-címe, ez az IP-cím statikusan hozzá lesz rendelve a ADFS-kiszolgálóhoz, valamint valós IP-címnek kell lennie a belső alhálózaton belül |
| WAP01NICIPAddress |Az első WAP-kiszolgáló belső IP-címe, ez az IP-cím statikusan hozzá lesz rendelve a WAP-kiszolgálóhoz, valamint valós IP-címnek kell lennie a DMZ alhálózaton belül |
| WAP02NICIPAddress |A második WAP-kiszolgáló belső IP-címe, ez az IP-cím statikusan hozzá lesz rendelve a WAP-kiszolgálóhoz, valamint valós IP-címnek kell lennie a DMZ alhálózaton belül |
| ADFSLoadBalancerPrivateIPAddress |Az ADFS terheléselosztó belső IP-címe, ez az IP-cím statikusan hozzá lesz rendelve a terheléselosztóhoz, valamint valós IP-címnek kell lennie a belső alhálózaton belül |
| ADDCVMNamePrefix |A tartományvezérlők virtuális gépnév előtagja |
| ADFSVMNamePrefix |Az ADFS-kiszolgálók virtuális gépnév előtagja |
| WAPVMNamePrefix |A WAP-kiszolgálók virtuális gépnév előtagja |
| ADDCVMSize |A tartományvezérlők virtuálisgép-mérete |
| ADFSVMSize |Az ADFS-kiszolgálók virtuálisgép-mérete |
| WAPVMSize |A WAP-kiszolgálók virtuálisgép-mérete |
| AdminUserName |A virtuális gépek helyi rendszergazdájának neve |
| AdminPassword |A virtuális gépek helyi rendszergazdafiókjának a jelszava |

## <a name="additional-resources"></a>További források
* [Rendelkezésre állási csoportok](https://aka.ms/Azure/Availability) 
* [Azure Load Balancer](https://aka.ms/Azure/ILB)
* [Belső terheléselosztó](https://aka.ms/Azure/ILB/Internal)
* [Internetre irányuló terheléselosztó](https://aka.ms/Azure/ILB/Internet)
* [Tárfiókok](https://aka.ms/Azure/Storage)
* [Azure virtuális hálózatok](https://aka.ms/Azure/VNet)
* [Az AD FS és a webalkalmazás-proxy hivatkozások](http://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>Következő lépések
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
* [Az AD FS konfigurálása és felügyelete az Azure AD Connect segítségével](active-directory-aadconnectfed-whatis.md)
* [Az AD FS nagy rendelkezésre állású, több földrajzi régióra kiterjedő üzembe helyezése az Azure Traffic Managerrel](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)


