---
title: Alkalmazások integrálása az Azure virtuális hálózat
description: Bemutatja, hogyan csatlakozzon az Azure App Service alkalmazás egy új vagy meglévő Azure virtuális hálózat
services: app-service
documentationcenter: ''
author: ccompy
manager: erikre
editor: cephalin
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: ccompy
ms.openlocfilehash: 83f5c64926eb9b718463c415a5478af374245f31
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Az alkalmazás integrálása az Azure virtuális hálózat
Ez a dokumentum az Azure App Service virtuális hálózati integráció funkció használatát ismerteti és bemutatja, hogyan állíthatja be az alkalmazásokkal [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Ha ismeri az Azure virtuális hálózatokról (Vnetekről), ez az a képesség, amely lehetővé teszi egy nem internetes routeable hálózat elérését Ön szabályozza az Azure-erőforrások számos helyezendő. Ezek a hálózatok csatlakozhat a helyszíni VPN technológiáin különböző hálózatokhoz. Többet szeretne megtudni az Azure Virtual Network, indítsa el az adatok itt: [Azure virtuális hálózat áttekintése][VNETOverview]. 

Az Azure App Service rendelkezik két űrlap. 

1. A több-bérlős rendszereket, amelyek támogatják a díjszabások teljes skáláját
2. Az App Service-környezet (ASE) prémium funkció, amely telepíti azokat a virtuális hálózat. 

Ez a dokumentum végighalad a virtuális integráció és nem App Service Environment-környezet. Ha azt szeretné, további információt a ASE funkció, indítsa el az információkat itt: [App Service Environment bemutatása][ASEintro].

Virtuális integráció a webes alkalmazás hozzáférést biztosít a virtuális hálózatán lévő erőforrásokat, de nem személyes hozzáférési jogot a webalkalmazás a virtuális hálózathoz. Titkos hozzáférés utal, hogy az alkalmazás csak egy privát hálózatról, mint az Azure virtuális hálózat belül érhető el. Titkos hozzáférés csak érhető el egy konfigurált egy belső Load Balancer (ILB) rendelkező mértékéig. Egy ILB ASE használata részletesen, kezdje Itt a cikk: [létrehozása és használata egy ILB ASE][ILBASE]. 

Egy gyakori forgatókönyv, ahol a VNet integrációs használna adatbázis vagy az Azure virtuális hálózat a virtuális gépen futó webes szolgáltatás a webes alkalmazás hozzáférést tesz lehetővé a rendszer. A VNet integrációja nem kell tenni egy nyilvános végpontot, az alkalmazások a virtuális gép azonban is használja helyette a saját nem internetes elérhető címet. 

A virtuális hálózat integrációs szolgáltatás számára:

* a Standard, Premium vagy terv árképzési elszigetelt igényel 
* Klasszikus vagy erőforrás-kezelő virtuális hálózat együttműködik 
* támogatja a TCP és UDP
* együttműködik a webes, mobil API-alkalmazások és függvény alkalmazások
* lehetővé teszi egy alkalmazás egyszerre csak 1 VNet való kapcsolódáshoz
* lehetővé teszi, hogy az egy App Service-csomag integrációját legfeljebb öt Vnetek 
* lehetővé teszi, hogy ugyanazt a virtuális hálózatot, több alkalmazást az App Service-csomag által használt
* egy miatt a szolgáltatásiszint-szerződést 99,9 %-os SLA támogatja a virtuális hálózat átjáróval

Néhány dolgot virtuális integráció nem támogatja, többek között:

* a meghajtó csatlakoztatása
* AD-integrációs 
* NetBios
* személyes hozzáférés

### <a name="getting-started"></a>Első lépések
Az alábbiakban tartsa szem előtt a webalkalmazást egy virtuális hálózathoz való csatlakozás előtt a következőkre:

* Virtuális integráció csak olyan alkalmazások működik egy **szabványos**, **prémium**, vagy **elszigetelt** árképzési terv. Ha a funkció engedélyezéséhez, majd App Service-csomag skálázása egy nem támogatott díjszabási csomaggal az alkalmazások kapcsolata megszakad a Vnetek használják a. 
* Ha a cél virtuális hálózat már létezik, pont-pont VPN engedélyezve van a dinamikus útválasztási átjáróval, mielőtt csatlakozhat egy alkalmazáshoz kell rendelkeznie. Ha az átjáró statikus útválasztással van konfigurálva, nem engedélyezhető a pont-pont virtuális magánhálózati (VPN).
* A virtuális hálózat, az App Service Plan(ASP) ugyanahhoz az előfizetéshez kell lennie.
* Ha az átjáró már pont-pont engedélyezve van, és nincs-e az alapszintű Termékváltozat, IKEV2 a pont-pont konfigurációjában le kell tiltani.
* Az alkalmazásokat, amelyekbe beépül az egy Vnetet a DNS-ben, hogy a virtuális hálózat megadott használja.
* Alapértelmezés szerint az integrációs alkalmazások csak útvonal-forgalmat a virtuális hálózat a virtuális hálózat definiált útvonalak alapján történő. 

## <a name="enabling-vnet-integration"></a>Virtuális integráció engedélyezése

Lehetősége van az alkalmazás egy új vagy meglévő virtuális hálózathoz csatlakozni. Ha létrehoz egy új hálózati integráció részeként, mellett csak a VNet létrehozása, a dinamikus útválasztó átjáró meg előre konfigurálva, és pont hely VPN engedélyezve van. 

> [!NOTE]
> Egy új virtuális hálózati integráció konfigurálásához több percet is igénybe vehet. 
> 
> 

Virtuális integráció engedélyezéséhez nyissa meg az alkalmazás beállításait, és válassza a hálózat. A felhasználói felület, a megnyitott három hálózatkezelési lehetőségeket kínál. Ez az útmutató csak érintetlen virtuális integráció be, ha hibrid kapcsolatok és az App Service Environment-környezetek ismertetése a dokumentum későbbi szakaszában. 

Ha az alkalmazás van nem a megfelelő árképzési terv, a felhasználói felület lehetővé teszi a nagyobb tarifacsomagot az Ön által választott tervezi méretezni.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Egy már meglévő virtuális hálózaton VNet-integráció engedélyezése
A virtuális hálózat integrációs felhasználói felület lehetővé teszi a Vnetek listájából válassza ki. A hagyományos Vneteket jelezve, hogy például a Word "Klasszikus" zárójelek között a virtuális hálózat neve mellett. A lista rendezett-úgy, hogy az erőforrás-kezelő Vnetek vannak felsorolva. Az ábrán a lent látható módon tekintheti meg, hogy csak egy virtuális hálózat választható ki. Több oka, hogy a virtuális hálózat is jelenítette meg többek között:

* egy másik előfizetésben található, amely a fiókjának van hozzáférési joga van a virtuális hálózat
* a virtuális hálózat nem rendelkezik pont kompatibilis helyhez
* a virtuális hálózat nem rendelkezik egy dinamikus útválasztási átjáró

![][2]

Ahhoz, hogy integrációs egyszerűen kattintson a integrálni kívánt VNet. Miután kiválasztotta a virtuális hálózat, az alkalmazás automatikusan újraindul a módosítások életbe léptetéséhez. 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>A hely egy klasszikus virtuális pont engedélyezése
Ha a virtuális hálózat nem kell egy átjárót, és helyhez ponttal rendelkezik, akkor rendelkezik, amely beállításához először. Ehhez a klasszikus virtuális hálózatot, nyissa meg a [Azure-portálon] [ AzurePortal] és jelenítse meg a virtuális Networks(classic) listáját. Itt kattintson a kívánt integrálása, majd kattintson a nagy jelölését a VPN-kapcsolatok nevű Essentials a hálózaton. Itt hozhat létre a VPN és a van arra is, akkor hozzon létre egy átjáró mutasson. Az átjáró létrehozása révén helyhez halad át a pont után körülbelül 30 percet, mielőtt kész van. 

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Egy erőforrás-kezelő virtuális hely pont engedélyezése
Egy erőforrás-kezelő virtuális hálózat egy átjáró és egy helyhez pontot konfigurál, használhatja vagy a PowerShell megfelelően Itt [PowerShell virtuális hálózat egy pont – hely kapcsolat beállítása] [ V2VNETP2S] , vagy használja az Azure-portálon dokumentált Itt [egy Vnetet az Azure portál használatával egy pont – hely kapcsolat beállítása][V2VNETPortal]. Ez a funkció végrehajtásához a felhasználói felület még nem érhető el. Ne feledje, hogy nem szeretné, hogy a pont Helykonfiguráció olyan tanúsítványokat hoznak létre. Ez a rendszer automatikusan konfigurálja a webalkalmazás csatlakozhat a virtuális hálózat. 

### <a name="creating-a-pre-configured-vnet"></a>Egy előre konfigurált virtuális hálózat létrehozása
Ha szeretne létrehozni egy új virtuális hálózat átjáróként konfigurált és a pont-pont, a felhasználói felület hálózati App Service rendelkezik a funkció a teendő, de csak egy erőforrás-kezelő virtuális hálózat. Ha szeretne egy klasszikus virtuális hálózatot hozzon létre egy átjáró és a pont-pont, akkor szüksége ehhez manuálisan a hálózat felhasználói felületen keresztül. 

Hozzon létre egy erőforrás-kezelő virtuális hálózat virtuális integráció felületről, egyszerűen válassza **új virtuális hálózat létrehozása** , és adja meg a:

* Virtuális hálózat neve
* Virtuális hálózat címterülete
* Alhálózat neve
* Alhálózati címterület
* Átjáró címterülete
* Pont-hely típusú címterület

Ha azt szeretné, hogy ez a virtuális hálózat bármely más hálózatokhoz, majd ne válassza háttérszínnek IP-címtartomány átfedésben az ezekhez a hálózatokhoz. 

> [!NOTE]
> Erőforrás-kezelő VNet létrehozása egy átjáró körülbelül 30 percet vesz igénybe, és jelenleg nem integrálható a virtuális hálózat az alkalmazást. Az átjáró a virtuális hálózat létrejötte után kell térjen vissza az alkalmazás virtuális integráció felhasználói felület, és válassza ki az új Vnetet.
> 
> 

![][3]

Az Azure Vnetekhez általában jönnek létre magánhálózati címek. A virtuális integráció alapértelmezés szerint a szolgáltatás a virtuális hálózat be ezeket az IP-címtartományok bármely adatforgalmat irányítja. A privát IP-címtartományok a következők:

* -Ez megegyezik a 10.0.0.0 - 10.0.0.0/8 10.255.255.255
* -Ez megegyezik a 172.16.0.0 - 172.16.0.0/12 172.31.255.255 
* -Ez megegyezik a 192.168.0.0 - 192.168.0.0/16 192.168.255.255

A virtuális hálózat címterületen belülre kell CIDR-formátumban kell megadni. Ha nincs tisztában a CIDR-formátumban, egy határozható meg a címblokkok IP-cím és a hálózati maszk jelölő egész. Egy rövid összefoglaló vegye figyelembe, hogy 10.1.0.0/24 256 címet és 10.1.0.0/25 lenne 128 címek. Egy IPv4-cím egy /32 csak 1 cím lenne. 

Ha itt a DNS-kiszolgáló adatait, majd, hogy be van állítva a virtuális hálózat. VNet létrehozása után szerkesztheti ezt az információt a VNet felhasználói felületéről. Ha módosítja a DNS a vnet, akkor szüksége szinkronizálási hálózati művelet végrehajtására.

Amikor létrehoz egy klasszikus virtuális hálózatot a virtuális integráció felhasználói felület használatával, hozna létre egy Vnetet az alkalmazás ugyanabban az erőforráscsoportban. 

## <a name="how-the-system-works"></a>A rendszer működése
Ez a funkció a színfalak pont – hely típusú VPN-technológia az alkalmazás csatlakozni a virtuális hálózat felett hoz létre. Az Azure App Service alkalmazások is rendelkeznek egy több-bérlős rendszer-architektúra, amely kizárja a Vneten belül közvetlenül egy alkalmazás kiépítés módon történik a virtuális gépek. Pont-pont technológia építve azt csak a virtuális gépek üzemeltetési az alkalmazáshoz való hálózati hozzáférés korlátozásához. A hálózati hozzáférés tovább korlátozza az alkalmazás állomásokhoz érdekében, hogy az alkalmazások csak a hálózatok, amelyeknél megadta, hogy hozzáférjenek. 

![][4]

Ha a virtuális hálózati DNS-kiszolgáló még nincs konfigurálva, az alkalmazás kell IP-címek használatát a virtuális hálózat az erőforrás elérésére. IP-címet használja, ne feledje, hogy a fő előnye, hogy ez a funkció, hogy lehetővé teszi a magánhálózaton belül a személyes címek használatához. Ha az alkalmazás használatára a nyilvános IP-címek a virtuális gépek közül legalább egy, és nem használják a virtuális hálózat integrációs szolgáltatás számára, és megfelelően kommunikálnak az interneten keresztül.

## <a name="managing-the-vnet-integrations"></a>A virtuális integráció kezelése
Csatlakoztatása és leválasztása a virtuális hálózatba van az alkalmazás szintjén. Hatással lehet a virtuális integráció több alkalmazás között olyan ASP szinten. A felhasználói felületen, az alkalmazás szintjén látható Részletek kaphat, ha a virtuális hálózaton. Ugyanazokat az információkat a legtöbb is látható, az ASP szintjén. 

![][5]

A hálózati szolgáltatás állapota lapon látható, ha az alkalmazás csatlakozik-e a virtuális hálózat. Ha a virtuális hálózat átjáró bármilyen oknál fogva nem működik, majd ez látható, nincs csatlakoztatva. 

Most már rendelkezik az alkalmazás szintű VNet integrációs felhasználói felülete megegyezik a részletes információkat az ASP kap érhető el információ. Az alábbiakban azokat a cikkeket:

* Virtuális hálózat neve – Ez a hivatkozás megnyitja az Azure virtuális hálózat felhasználói felület
* Hely - ez tükrözi a virtuális hálózat helyét. A virtuális hálózat egy másik helyen integrálása lehetőség.
* Tanúsítványállapot - tanúsítványok használatával teszi biztonságossá a VPN-kapcsolatot az alkalmazás és a virtuális hálózat között van. Ez a teszt, azok szinkronban tükrözi.
* Az átjáró állapotának - az átjárók kell le valamilyen okból majd az alkalmazás nem fér hozzá a virtuális hálózaton lévő erőforrások. 
* Virtuális címterület - Ez az a Vnethez tartozó IP-címterület. 
* Mutasson a hely címterület - Ez az a hely IP-címtér a Vnethez tartozó pontot. Az alkalmazás megjeleníti az IP-címek, ez a címtartomány a érkező kommunikációt. 
* Hely és hely címterület - helyek közötti VPN segítségével csatlakozzon a virtuális hálózat, a helyszíni erőforrásokhoz vagy más virtuális hálózatba. Kell, akkor az IP-címtartományok meghatározott VPN-kapcsolat itt látható konfigurálva.
* DNS-kiszolgálók – Ha a virtuális hálózaton konfigurált DNS-kiszolgálók és az itt felsorolt.
* Az a virtuális hálózatba irányított - ott IP-címek, a virtuális hálózat van definiálva a következő útválasztási IP-címeket, és itt ezeket a címeket megjelenítése listáját. 

Az egyetlen elvégezhető az alkalmazás virtuális integráció nézetében művelet az alkalmazás bontja a virtuális hálózat jelenleg csatlakoztatva van. Ez egyszerűen kattintson Disconnect tetején. Ez a művelet nem módosítja a virtuális hálózat. A virtuális hálózat és a konfigurációját, beleértve az átjárók változatlan marad. Ha ezután törölni kívánja a virtuális hálózat, először törli az erőforrást, beleértve az átjárók a szeretné. 

Az App Service-csomag nézet számos további műveletek. Is hozzáférés másképp mint az alkalmazásból. Elérni az ASP hálózat felhasználói felületén nyissa meg az ASP felhasználói felületi és görgessen le. Van egy felhasználói felületi elem nevű hálózati szolgáltatás állapotát. Egyes kisebb adatok körül virtuális integráció biztosít. A felhasználói felület kattintva megnyílik a hálózati szolgáltatás állapot felhasználói felületi. Majd kattintson a "Kattintson ide kezelése", ha a felhasználói felület, amely tartalmazza a virtuális hálózat integrációja az ASP a nyílik meg.

![][6]

Az ASP helye kezelőkonzoljából nézve a Vnetek integrációhoz helyeinek megjegyezhető helyes. Ha a virtuális hálózat egy másik helyen valószínűleg sokkal tekintse meg a késési problémák szempontjából. 

A Vnetek integrálva a hány Vnetek egy emlékeztető, az alkalmazások integrálva vannak az ASP és hány, akkor is. 

Minden egyes virtuális hálózaton hozzáadott részleteinek megjelenítéséhez kattintson a virtuális hálózaton szüksége. Mellett, amely korábban feljegyzett részleteket is megtekintheti az ASP, hogy a VNet-t használó alkalmazások listáját. 

Műveletek tekintetében nincsenek két elsődleges műveletet. Az egyik az alkalmazás azokat a virtuális hálózat elhagyó forgalomra meghajtó útvonalakat hozzáadását. A második műveletet azt a képességet, tanúsítványok és a hálózati adatok szinkronizálása.

![][7]

**Útválasztás** , a virtuális hálózat definiált útvonalak irányítja a forgalmat a virtuális hálózat az alkalmazásból történő alkalmazott korábban feljegyzett. Ahol az ügyfelek további kimenő adatforgalmat küldjön egy alkalmazás a virtuális hálózat és a számukra ezt a képességet szeretnék valósul meg, ha nincsenek olyan néhány felhasználását. Mi történik a forgalmat, követően, hogy hogyan konfigurálja az ügyfél az a virtuális hálózat legfeljebb. 

**Tanúsítványok** a tanúsítvány állapotát tükrözi a ellenőrzés által végzett az App Service ellenőrzése, hogy a tanúsítványok, amelyeket a VPN-kapcsolat használatával hoztuk továbbra is megfelelőek. Virtuális integráció engedélyezve van, majd ha ez az első integráció, hogy a virtuális hálózatba érkezett az ASP, az alkalmazások nincs a szükséges exchange a tanúsítványok a kapcsolati védelme érdekében. A tanúsítványok együtt azt lekérése a DNS-konfiguráció, útvonalakat és más hasonló dolgot a hálózati leíró.
Ha ezeket a tanúsítványokat vagy hálózati adatok módosul, akkor szüksége "Sync hálózat" gombra. **Megjegyzés:**: "Sync hálózat" kattintva, majd a rövid kimaradásokat okozhat az alkalmazás és a virtuális hálózat közötti kapcsolat. Az alkalmazás nem indítják újra, amíg a kapcsolat megszakadása okozhat a funkciót nem webhelyét megfelelően. 

## <a name="accessing-on-premises-resources"></a>Hozzáférés a helyszíni erőforrások
A virtuális hálózat integrációs szolgáltatás előnyei egyik, ha a virtuális hálózat csatlakozik a helyi hálózati helyek közötti VPN-nel rendelkező, majd az alkalmazások hozzáférhetnek a helyszíni erőforrások az alkalmazásból. Ennek ellenére előfordulhat, hogy módosítania a helyszíni VPN-átjáró felé mutató útvonalakat a hely IP-pont között. Ha először be, a webhelyek közötti VPN használatával konfigurálja a parancsfájlok beállítása kell útvonalakat, beleértve a hely VPN. Ha a webhelyek közötti VPN létrehozása után a pontok hozzá hely típusú VPN, akkor szüksége az útvonalak manuális frissítésével. Ez a részletes átjárón eltérők lehetnek, és a dokumentum nem ismerteti. 

> [!NOTE]
> A virtuális hálózat integrációs szolgáltatás számára nem integrálható egy Vnetet, amely rendelkezik egy ExpressRoute-átjárót egy alkalmazást. Akkor is, ha az ExpressRoute-átjárót konfigurált [párhuzamos módban] [ VPNERCoex] a virtuális integráció nem működik. Ha egy ExpressRoute-kapcsolaton keresztül erőforrások eléréséhez szükséges, akkor is használhat egy [App Service Environment-környezet][ASE], amelyen fut a virtuális hálózat.
> 
> 

## <a name="pricing-details"></a>Díjszabás részletei
Van néhány apró kell ügyelnie, ha a virtuális hálózat integrációs szolgáltatás számára a díjszabás. Ez a funkció használatához 3 kapcsolódó díjakat van:

* Az ASP árképzési szint követelmények
* Adatátviteli költségek
* VPN-átjáró költségeket.

Az alkalmazások között használhatják ezt a szolgáltatást akkor kell Standard vagy prémium szintű App Service-csomag. További információt itt azok a költségek is olvashat: [App Service szolgáltatás díjszabása][ASPricing]. 

Hely VPN-hálózatokhoz módjának pont vannak kezelve, mindig van kimenő adatok járnak a virtuális integráció kapcsolaton keresztül akkor is, ha a virtuális hálózat ugyanabban az adatközpontban. Mik azok a ezeket a költségeket megtekintéséhez tekintse meg itt: [adatok átvitele Díjszabásának részleteit][DataPricing]. 

Az utolsó elem a virtuális hálózat átjáró költségét. Ha máshová, például a helyek közötti VPN átjáró nincs szüksége, majd fizet átjárók virtuális integráció funkciójának támogatásához. Részletek érhetők el a költségekre itt: [VPN-átjáró árképzési][VNETPricing]. 

## <a name="troubleshooting"></a>Hibaelhárítás
Bár a funkció egyszerűen állíthat be, hogy nem jelenti azt, hogy a felhasználói élmény lesz-e szabad probléma. A kívánt végpont elérésével kapcsolatos problémák kell észlel néhány segédprogram segítségével ellenőrizze az alkalmazás-konzol közötti kapcsolatot. Nincsenek két konzol feladatait is használhatja. A Kudu konzolról van, és a másik pedig a konzolt, amely az Azure portálon érhető el. Az alkalmazásból a Kudu konzol eléréséhez nyissa meg az eszközök -> Kudu. Ez megegyezik a [sitename] címen. scm.azurewebsites.net. Ha egyszerűen nyit, nyissa meg a hibakeresési konzoljára. Az Azure-portál központi konzolon, majd a alkalmazás segítségével nyissa meg eszközök konzol ->. 

#### <a name="tools"></a>Eszközök
Az eszköz pingelést, nslookup és tracert a konzollal biztonsági korlátozások miatt nem fognak működni. Töltse ki a "void" ott lett hozzáadva két külön eszközök. DNS-funkcióinak teszteléséhez hozzáadott egy nameresolver.exe nevű eszköz. A szintaxis a következő:

    nameresolver.exe hostname [optional: DNS Server]

Nameresolver segítségével ellenőrizze a gazdagép neve, amely az alkalmazás függ. Ezzel a módszerrel tesztelheti Ha semmit a DNS-beli rosszul konfigurálva van, vagy lehet, hogy nem fér hozzá a DNS-kiszolgáló.

A következő eszköz lehetővé teszi TCP-kapcsolat állomás és port kombinációjához tesztelésére. Ez az eszköz tcpping.exe nevezik, és a szintaxis a következő:

    tcpping.exe hostname [optional: port]

A tcpping segédprogram jelzi, hogy ha érhető el egy adott állomás és port. Csak megmutatja sikeres ha: egy alkalmazás a következőnél: az állomás és port kombinációját, és a hálózati hozzáférés az alkalmazásból a megadott állomás és port.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Virtuális hálózat eléréséhez hibakeresés üzemeltetett erőforrásokhoz
Számos dolgot, amely megakadályozhatja, hogy az alkalmazás egy adott állomás és port elérését. A legtöbbször ennek három művelet egyike:

* **A módon van egy tűzfal** Ha tűzfal módon, hogy elért a TCP időtúllépési. Ebben az esetben ez 21 másodperc. Kapcsolat tesztelése a tcpping eszközzel. TCP-időtúllépések tűzfalak túl sok dolgot oka az lehet, de kezdeni. 
* **Nem érhető el DNS** a DNS-időtúllépési érték egy DNS-kiszolgálón három másodperc. Ha két DNS-kiszolgálók, az időtúllépési érték 6 másodperc. Nameresolver segítségével megtekintheti, hogy működik-e a DNS. Ne felejtse el, nem használhatja az nslookup, amely nem használja a DNS, a virtuális hálózat van konfigurálva.
* **Érvénytelen P2S IP-címtartomány** a hely IP-címtartomány pontot kell lennie az RFC 1918 privát IP-címtartományok (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Ha a tartomány IP-címeket használ, amely kívül, dolgot nem fognak működni. 

Ha ezen elemekre nem válaszolja meg a problémát, keresse meg az egyszerű többek között az első: 

* Az átjáró, hogy fel a portálon megjelenítése nem?
* Tanúsítványok, hogy szinkronban jelennek?
* Birtokában bárki változtatta meg a hálózati konfigurációt a érintett ASP "Sync hálózat" nélkül? 

Ha az átjáró nem működik, majd hálózatra kell kapcsolni, készítsen biztonsági másolatot. Ha a tanúsítványok nincsenek szinkronban, nyissa meg a virtuális integráció ASP nézetbe, és kattintson a "Sync hálózat". Ha azt gyanítja, hogy az a VNet konfigurációját végzett változás történt, és azt nem történt szinkronizálás üzembe helyezése az ASP, majd nyissa meg az ASP a VNet-integráció nézetében, és kattintson a "Sync hálózat" csak ne feledje, a rövid kimaradásokat ennek hatására az alkalmazások és a VNet-kapcsolatot. 

Ha, amely minden rendben, akkor meg kell ásva egy kicsit:

* Vannak-e bármilyen más alkalmazások virtuális integráció ugyanazon virtuális erőforrások eléréséhez? 
* Lehet, a app konzolon és használni tcpping a Vneten található más erőforrások eléréséhez? 

Ha a fentiek közül egyik sem teljesül, majd a virtuális integráció rendben, és a probléma valahol máshol. Ez azért, ahol lekérdezi több kihívást számít, mivel nincs egyszerű mód miért nem éri el a gazdagép és a portszám megjelenítéséhez. Okai a következők:

* a tűzfal elérésének megakadályozásához, az alkalmazás port az a hely IP-címtartomány ponton az állomás rendelkezik. Alhálózatok gyakran meghaladó nyilvános hozzáférés szükséges.
* a célállomás nem működik
* az alkalmazás nem működik
* a megfelelő IP- vagy állomásnév volt
* az alkalmazás a várttól mint egy másik portot figyeli. Ellenőrizheti ezt állapotra vált, hogy a gazdagép-kiszolgálóra, és használja a "netstat – aon" cmd parancssorából. Ez azt jelenti, melyik folyamat azonosítója melyik portot figyeli. 
* a hálózati biztonsági csoportok oly módon, hogy azok tagadni a hozzáférést az alkalmazás-állomás és port az a hely IP-címtartomány ponton vannak konfigurálva.

Ne feledje, hogy nem tudja milyen IP-a pont számára, hogy az alkalmazás fogja használni, engedélyeznie kell a hozzáférést a teljes tartományon a hely IP-címtartományt. 

További hibakeresési lépések az alábbiak:

* Jelentkezzen be egy másik virtuális gép a Vnetben, és próbálja meg elérni a erőforrás állomás: port onnan. Nincsenek az egyes TCP ping segédprogramokat használhatja erre a célra, vagy még akkor is használhatja a telnet, ha szükséges. Itt célja csak annak megállapítása, hogy kapcsolódási hiba az adott virtuális Gépről. 
* elindítani egy alkalmazás egy másik virtuális Gépet és a vizsgálati hozzáférést adott állomás és port az alkalmazásból a konzolról

#### <a name="on-premises-resources"></a>A helyszíni erőforrások ####
Ha az alkalmazás nem érhető el a helyszíni erőforrások, ellenőrizni kell elsőként, ha érhető el erőforrás a Vneten belül. Biztosan működik, ha megpróbál elérni a helyszíni alkalmazást egy virtuális Gépet a Vneten belül. A telnet vagy a TCP ping segédprogramot használhatja. A virtuális Gépet a helyi erőforrás nem érhető el, majd győződjön meg arról, hogy működik-e a helyek közötti VPN-kapcsolatot. Hogy működik, majd ellenőrizze a ugyanazokat a műveleteket, valamint a helyszíni átjáró konfigurációs és állapotadatainak korábban feljegyzett. 

Ha a virtuális hálózat most méretű képes elérni a helyszíni rendszer, de az alkalmazás nem, akkor a hiba oka valószínűleg egy, a következő:

* az útvonalakat a hely IP-címtartományai a helyszíni átjáró pontot tartalmazó nincs konfigurálva
* a hálózati biztonsági csoportok blokkolják a hozzáférést a hely IP-címtartomány pont
* a helyszíni tűzfalak blokkolják a hely IP-címtartomány a pont-forgalom
* egy felhasználó definiált Route(UDR) vannak a virtuális hálózat, amely megakadályozza, hogy a pont helyrendszer-alapú forgalmat a helyszíni hálózat elérése

## <a name="powershell-automation"></a>PowerShell-automatizálás

App Service integrálható az Azure Virtual Network PowerShell használatával. Készen áll a futásra parancsfájl, lásd: [az Azure App Service alkalmazás csatlakoztatása az Azure-beli virtuális hálózathoz](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>Hibrid kapcsolatok és az App Service-környezetek
Nincsenek három szolgáltatást, amely a virtuális hálózaton található erőforrások elérésének lehetővé tétele. Ezek a következők:

* Virtuális integráció
* Hibrid kapcsolatok
* App Service-környezetek

Hibrid kapcsolatok szükséges a hibrid kapcsolat Manager(HCM) meghívta a hálózat továbbító ügynök telepítését. A HCM képesnek kell lennie az Azure-bA és az alkalmazás kapcsolódni. Ez a megoldás különösen nagy, például a helyi hálózat egy távoli hálózatról, vagy akár egy másik felhőben tárolt hálózati, mert nem szükséges az interneten elérhető végponton. A HCM csak akkor fut, a Windows, illetve azt, hogy a magas rendelkezésre állás biztosításához futtató legfeljebb öt példányok. Hibrid kapcsolatok csak akkor, ha TCP támogatja, és minden egyes HC végpont meg kell egyeznie a megadott állomás: port kombinációjához. 

Az App Service Environment-környezet funkció lehetővé teszi a virtuális hálózat az Azure App Service egy példánya futtatásához. Ez lehetővé teszi, hogy az alkalmazások az Azure-erőforrások bármely további lépések nélkül a virtuális hálózat. Az App Service-környezetek egyéb előnyeit, hogy használható-alapú Dv2 munkavállalók 14 GB RAM Memóriát. Egy másik előnye, hogy a rendszer az igényeknek méretezheti. A több-bérlős környezetekben, ahol az ASP legfeljebb 20 példányok, eltérően-környezetben méretezheti legfeljebb 100 ASP példányok. Egy mértékéig, amely a virtuális hálózat integrációja nem kap a dolog, hogy az App Service-környezetek együttműködik az ExpressRoute VPN. 

Míg néhány eset átfedés használja, a szolgáltatás egyik cserélje le a többi. Ismerete, hogy mely szolgáltatásokat szeretné igénybe van kötve az igényeinek. Példa:

* Ha a fejlesztők és egyszerűen szeretne futtatni egy helyet az Azure-ban, és annak érik el az adatbázist a munkaállomáson a saját számítógépéről, a legegyszerűbb dolog használata hibrid kapcsolatok. 
* Ha olyan nagy szervezethez, amely fel szeretné függeszteni számos webtulajdonaiban a nyilvános felhő, és kezelheti őket a saját hálózati, majd nyissa meg az App Service-környezet szeretné. 
* Ha az App Service számos üzemeltetett alkalmazások és egyszerűen szeretné, hogy a virtuális hálózaton lévő erőforrások eléréséhez, majd virtuális integráció nyissa meg a módja. 

A használati esetek túl van néhány egyszerűség kapcsolatos szempontjai. Ha a virtuális hálózat már csatlakoztatva van a helyszíni hálózathoz, majd ezzel a virtuális integráció vagy az App Service-környezetek egyszerű módja a helyi erőforrást. Másrészt a virtuális hálózat nincs csatlakoztatva a helyszíni hálózat majd esetén a virtuális hálózaton, és a telepítés a HCM összehasonlítása a helyek közötti virtuális magánhálózat beállításához sokkal több terhelése. 

A funkcionális eltérések túl van vannak is árképzési eltéréseket. Az App Service Environment-környezet jellemzője, a prémium szolgáltatásajánlat de kínál a legtöbb hálózati konfigurációs lehetőségek mellett nagyszerű szolgáltatásokat. Virtuális integráció Standard vagy prémium ASP használható, és biztonságosan fogyassza az erőforrásokat a vnetben a több-bérlős App Service a tökéletes megoldás. Hibrid kapcsolatok jelenleg a fiók, amely szinteken, indítsa el a szabad és majd beolvasni a fokozatosan drágább szükséges memóriamennyiség alapján BizTalk függ. Sok hálózatokon keresztül működik, ha ismét, nincs más szolgáltatás például a hibrid kapcsolatok, amelyek lehetővé teszik a is több mint 100 elkülönülő hálózatokon lévő erőforrások eléréséhez. 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
