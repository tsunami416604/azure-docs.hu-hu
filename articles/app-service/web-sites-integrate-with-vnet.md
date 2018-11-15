---
title: Alkalmazások integrálása az Azure-beli virtuális hálózathoz
description: Bemutatja, hogyan csatlakozhat egy új vagy meglévő Azure virtuális hálózat az Azure App Service-alkalmazás
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: ccompy
ms.openlocfilehash: 8a6d7fef902a3bd240b152cb15d6852a5fa0e7c6
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51687306"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Az alkalmazás integrálása az Azure-beli virtuális hálózathoz
Ez a dokumentum ismerteti az Azure App Service virtuális hálózat integrációja, és bemutatja, hogyan állíthatja be az Apps [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). [Azure virtuális hálózatok] [ VNETOverview] (Vnetek) lehetővé teszi számos, az Azure-erőforrások nem internetes routeable hálózati helyezni. Ezek a hálózatok csatlakozhat a helyszíni hálózatot VPN-technológiák segítségével. 

Az Azure App Service rendelkezik két formában. 

1. A több-bérlős rendszerek, amelyek támogatják az elkülönített kivételével díjszabások széles
2. Az App Service Environment (ASE), amely üzembe helyezi a virtuális hálózat be. 

Ez a dokumentum halad végig a VNet-integráció szolgáltatást, amely a több-bérlős App Service-ben a használatra szántak.  Ha az alkalmazás [App Service Environment-környezet][ASEintro], akkor már egy virtuális hálózatban, és nem igényli a VNet-integráció funkció az azonos virtuális hálózatban található erőforrások eléréséhez.

VNet-integráció a webes alkalmazás hozzáférést biztosít a virtuális hálózatban lévő erőforrásokra irányuló, de nem titkos való hozzáférést biztosít a webes alkalmazás a virtuális hálózatról. Privát hozzáférést utal, hogy az alkalmazás csak elérhetőnek magánhálózaton például az Azure virtuális hálózatban. Privát hozzáférést csak akkor használható a konfigurált egy belső Load Balancer (ILB) rendelkező ASE. Az ILB ASE használata részletesen, kezdje Itt a cikk: [létrehozása és a egy ILB ASE használatával][ILBASE]. 

VNet-integráció-adatbázisok az alkalmazások elérésének lehetővé tétele és a virtuális hálózatban futó webszolgáltatások gyakran használják. A VNet-integráció nem kell egy nyilvános végpontot elérhetővé az alkalmazások is, de a gép nem internetes irányítható Magáncímeket helyette használja. 

A VNet-integráció funkciót:

* Standard, prémium szintű vagy PremiumV2 díjszabási csomag szükséges 
* együttműködik a klasszikus vagy Resource Manager 
* támogatja a TCP és UDP
* együttműködik a webes, mobil-, API apps és a függvényalkalmazások
* lehetővé teszi az alkalmazás egyszerre csak 1 virtuális hálózathoz való kapcsolódáshoz
* lehetővé teszi, hogy az App Service-csomag integrációját a legfeljebb öt virtuális hálózatok 
* lehetővé teszi, hogy az azonos virtuális hálózaton használható több alkalmazás az App Service-csomag
* a pont – hely típusú VPN konfigurált virtuális hálózati átjáró szükséges.
* 99,9 %-os szolgáltatói szerződés miatt az SLA-t támogatja az átjárón

Néhány dolog, amely VNet-integráció nem támogatja, beleértve:

* a meghajtó csatlakoztatása
* AD-integráció 
* NetBios
* privát hozzáférést
* az ExpressRoute más erőforrások elérése 
* a Szolgáltatásvégpontok közötti erőforrások elérése 

### <a name="getting-started"></a>Első lépések
Az alábbiakban néhány dolog láthat, tartsa szem előtt a webalkalmazás csatlakozik egy virtuális hálózatot:

* Cél virtuális hálózat pont – hely VPN-útválasztó-alapú átjáróval engedélyezve van, mielőtt az alkalmazás csatlakoztatva kell rendelkeznie. 
* A virtuális hálózat, az App Service Plan(ASP) ugyanabban az előfizetésben kell lennie.
* Az alkalmazásokat, amelyek integrálhatók a virtuális hálózat használatához a virtuális hálózathoz tartozó DNS.

## <a name="enabling-vnet-integration"></a>VNet-integráció engedélyezése

Van egy új verziója, a VNet-integráció szolgáltatás, amelyet az előzetes verzióban érhető el. Ez nem függ a pont – hely VPN és erőforrások elérésére is támogatja az expressroute-on vagy a végpontok közötti. További információ az új előzetes funkciót, ugorjon a dokumentum végén. 

### <a name="set-up-a-gateway-in-your-vnet"></a>A vnet-átjáró beállítása ###

Ha már rendelkezik egy pont – hely címekkel konfigurált átjáróval, továbbléphet, a VNet-integráció konfigurálása az alkalmazását.  
Az átjáró létrehozása:

1. [Hozzon létre egy átjáró-alhálózatot] [ creategatewaysubnet] a virtuális hálózatban található.  

1. [A VPN-átjáró létrehozása][creategateway]. Válassza ki egy útvonalalapú VPN-típussal.

1. [A pont beállítása helycímek][setp2saddresses]. Ha az átjáró az alapszintű termékváltozatban nem, majd a pont – hely konfigurációban le kell tiltani az IKEV2 és SSTP ki kell választani. A címterület kell legalább a következő címblokkok:

* 10.0.0.0/8 – Ez azt jelenti, hogy egy IP-címtartományt, a 10.0.0.0 10.255.255.255
* 172.16.0.0/12 – Ez azt jelenti, hogy egy IP-címtartományt, a 172.16.0.0 172.31.255.255 
* 192.168.0.0/16 – Ez azt jelenti, hogy egy IP-címtartományt, a 192.168.0.0 192.168.255.255

Ha a rendszer csak az átjáró létrehozása az App Service VNet-integráció, akkor nem kell feltölteni a tanúsítványt. Az átjáró létrehozása 30 percet is igénybe vehet. Nem lesz mindaddig, amíg az átjáró üzemképes integrálhatják az alkalmazás a virtuális hálózathoz. 

### <a name="configure-vnet-integration-with-your-app"></a>VNet-integráció konfigurálása az App-alkalmazással ###

VNet-integráció engedélyezése az alkalmazásban: 

1. Az alkalmazás az Azure Portalon nyissa meg és nyissa meg az alkalmazásbeállítások, és válassza ki a hálózatkezelés > VNet-integráció. Az ASP a Standard Termékváltozat méretezheti, vagy még jobb a VNet-integráció konfigurálása előtt. 
 ![Felhasználói felület VNet-integráció][1]

1. Válassza ki **adja hozzá a virtuális hálózat**. 
 ![Adja hozzá a VNet-integráció][2]

1. Válassza ki a virtuális hálózathoz. 
  ![Válassza ki a virtuális hálózat][8]
  
Az alkalmazás az utolsó lépés után újraindítja.  

## <a name="how-the-system-works"></a>A rendszer működése
A virtuális hálózat integrációja a pont – hely VPN-technológia épül. Az Azure App Service Apps egy több-bérlős rendszerben, amely kizárja az alkalmazás közvetlenül a virtuális hálózat kiépítési üzemelnek. A pont – hely technológia korlátozza a hálózati hozzáférés csak az alkalmazást futtató virtuális géphez. Alkalmazások korlátozva csak kiküldeni forgalom az internethez, vagy VNet-integráció hibrid kapcsolatok keresztül. 

![Hogyan működik a VNet-integráció][3]

## <a name="managing-the-vnet-integrations"></a>A virtuális hálózathoz való integrációt kezelése
Az alkalmazás szintjén van arra, hogy csatlakoztatása és leválasztása a virtuális hálózathoz. A VNet-integráció egyszerre több alkalmazással érintő műveletekre az App Service csomag szintjén. Az alkalmazás egyedi azonosítója részletekért a virtuális hálózaton. Ugyanazokat az információkat az ASP szintjén is megjelennek. 

 ![Virtuális hálózat részletei][4]

A hálózati funkció állapota lapon láthatja, ha az alkalmazás csatlakoztatva van-e a virtuális hálózathoz. Ha a virtuális hálózat átjárójának valamilyen okból nem működik, az állapotüzenet látható, a nem csatlakozó. 

Most már elérhető az alkalmazás szintű virtuális hálózatok közötti integráció felhasználói felületén pedig ugyanaz, mint a részletes információkat kap az ASP információk. Azok az elemek a következők:

* Virtuális hálózat neve – a virtuális hálózat felhasználói felület mutató hivatkozások
* Hely - jeleníti meg a virtuális hálózat helyét. Egy másik helyen található virtuális hálózat integrációja okozhat a késési problémák az alkalmazáshoz. 
* Tanúsítvány állapota – tükrözi, ha az App Service-csomag és a virtuális hálózat között a szinkronban-e a tanúsítványok.
* Átjáró állapota – az átjárók kell le valamilyen okból majd az alkalmazás nem fér hozzá a virtuális hálózatban található erőforrások. 
* Virtuális hálózati címtér - IP-címterét a virtuális hálózat mutatja be. 
* Pont – hely címtér - hely IP-címtér a pont a virtuális hálózat mutatja be. Irányuló hívásokhoz lesz a virtuális hálózathoz, ha az alkalmazás-feladó címe lesz címek egyikének. 
* Helyek közötti címtér - segítségével site-to-site VPN-ek a helyszíni erőforrásokhoz vagy más virtuális hálózatok közötti kapcsolatot a virtuális hálózat. Itt jelennek meg az IP-címtartományok VPN-kapcsolatra van definiálva.
* DNS-kiszolgálók – mutat be a virtuális hálózat DNS-kiszolgálók konfigurálva.
* Irányított IP-címek a virtuális hálózathoz – mutat be a címblokkok irányítva használt meghajtó adatforgalom a virtuális hálózat 

Az egyetlen művelet elvégezhető az alkalmazás a VNet-integráció nézetében, hogy az alkalmazás leválasztása a virtuális hálózat jelenleg csatlakoztatva van. Az alkalmazás leválasztása a virtuális hálózaton, válassza ki a **Disconnect**. Az alkalmazás újraindul, amikor leválasztja a virtuális hálózaton. Leválasztja a virtuális hálózat nem változik. A virtuális hálózat és annak konfigurációját, beleértve az átjárók változatlan marad. Ha meg kívánja törölni a virtuális hálózathoz, először törölje az erőforrásokat, beleértve az átjárók szeretné. 

Szeretné elérni az ASP virtuális hálózatok közötti integráció felhasználói felületén, nyissa meg az ASP felhasználói felületén, és válassza **hálózatkezelés**.  Válassza a VNet-integráció **kattintson ide a konfiguráláshoz** a hálózati szolgáltatás állapota felhasználói felületének megnyitásához.

![Az ASP VNet-integráció információk][5]

Az ASP virtuális hálózatok közötti integráció felhasználói felület bemutatják, a virtuális hálózatok, az alkalmazások az ASP által használt összes. Az App Service-csomag alkalmazások által csatlakoztatott legfeljebb 5 virtuális hálózatok is rendelkezhet. Minden alkalmazás egyetlen integrace nakonfigurovaná is rendelkezhet. Az egyes virtuális hálózatok részleteinek megtekintéséhez kattintson az Önt érdeklő virtuális hálózaton. Nincsenek két műveletet itt is végezhet.

* **Hálózat szinkronizálása**. A szinkronizálási hálózati művelet gondoskodik arról, hogy a tanúsítványok és hálózati adatok szinkronizálva legyenek. Adja hozzá, vagy módosítsa a virtuális hálózat DNS-ben, ha kíván végezni egy **hálózat szinkronizálása** műveletet. Ez a művelet újraindul a virtuális hálózat használatával bármilyen alkalmazást.
* **Útvonalak hozzáadása** útvonalak hozzáadása a hatékony felhasználhatóságot kimenő forgalom, a virtuális hálózathoz.

**Útválasztás** forgalom irányítása a virtuális hálózat az alkalmazásból, az útvonalakat a virtuális hálózathoz meghatározott szolgálnak. Ha szeretne további kimenő adatforgalmat küldjön a vnetben, ezután adhatja csak hozzá ezen címterületet.   

**Tanúsítványok** amikor VNet-integráció engedélyezve van, és van a szükséges exchange-tanúsítványok biztosítása a kapcsolat biztonságát. A tanúsítványok együtt vannak a DNS-konfigurációt, útvonalak és más hasonló dolgot, amelyek ismertetik a hálózaton.
Ha tanúsítványokat vagy hálózati adatok megváltozik, kattintson a "Hálózat szinkronizálása" szeretne. Ha "Hálózat szinkronizálása" gombra kattint, akkor miatt a rövid kimaradásokat a kapcsolat az alkalmazás és a virtuális hálózat között. Amíg az alkalmazás újraindítása után a nem a kapcsolat megszakadása okozhat a hely nem működhet megfelelően. 

## <a name="accessing-on-premises-resources"></a>Hozzáférés a helyszíni erőforrások
Alkalmazások integrálása a virtuális hálózatok helyek közötti kapcsolattal rendelkezik a helyszíni erőforrásokat érhetik el. Helyszíni erőforrások eléréséhez frissítenie a helyszíni VPN-átjáró útvonalakat a pont – hely címe blokkokkal. Ha a site-to-site VPN először be van állítva, a parancsprogramok segítségével konfigurálja kell útvonalak megfelelően beállítva. A pont – hely címeket ad hozzá, a site-to-site VPN létrehozása után, ha az útvonalak manuálisan frissíteni szeretné. Megtudhatja, hogyan valósítható meg / átjáró eltérőek lehetnek, és nem ebben a témakörben találhatók. Site-to-site VPN-kapcsolattal konfigurált BGP is rendelkezhet.

> [!NOTE]
> A VNet-integráció funkciót egy alkalmazás nem integrálható a virtuális hálózat, amely egy ExpressRoute-átjáróval rendelkezik. Akkor is, ha az ExpressRoute-átjárót konfigurált [együttes használata mód] [ VPNERCoex] a VNet-integráció nem működik. Van szükség a erőforrások eléréséhez ExpressRoute-kapcsolaton keresztül, akkor használhat egy [App Service Environment-környezet][ASE], amely fut a virtuális hálózatban található. 
> 
> 

## <a name="peering"></a>Társviszony-létesítés
VNet-integráció segítségével elérje az erőforrásokat a virtuális hálózatok társviszonyban állnak a virtuális hálózathoz csatlakozik. Konfigurálhatja a társviszony-létesítést úgy az alkalmazását:

1. Adja hozzá az alkalmazás virtuális Társhálózat-kapcsolatot csatlakozik. A társviszony-létesítési kapcsolat hozzáadásakor engedélyezése **virtuális hálózati hozzáférés engedélyezése** , és ellenőrizze **továbbított forgalom engedélyezése** és **átjárótranzit engedélyezése**.
1. A virtuális hálózattal, hogy társviszonyban áll a társviszony-létesítési kapcsolat hozzáadása a virtuális hálózathoz csatlakozik. A cél virtuális hálózatok közötti társviszony-létesítési kapcsolat hozzáadásakor engedélyezése **virtuális hálózati hozzáférés engedélyezése** , és ellenőrizze **továbbított forgalom engedélyezése** és **lehetővé teszi a távoli átjárók**.
1. Nyissa meg az App Service-csomag > Hálózat > virtuális hálózat integrációja felhasználói felület a portálon.  Válassza ki a virtuális hálózat, az alkalmazás csatlakozik. Az útválasztási szakasz alatt adja hozzá a címtartomány azon virtuális hálózat társviszonyban áll a virtuális hálózattal, az alkalmazás csatlakoztatva van.  


## <a name="pricing-details"></a>Díjszabás részletei
Nincsenek három kapcsolódó költségek a VNet-integráció funkció használatára:

* ASP-réteg követelményeinek díjszabása
* Adatátviteli költségek
* VPN-átjáró költségek.

Az alkalmazások kell lennie a Standard, prémium szintű vagy PremiumV2 App Service-csomag. A költségekre Itt láthatja a további részletek: [App Service díjszabását][ASPricing]. 

Nincs kimenő adatforgalom díja akkor is, ha a virtuális hálózat ugyanabban az adatközpontban. Ezeket a díjakat olyan [Data Transfer Díjszabásának részletei][DataPricing]. 

Nincs a VNet-átjáró szükséges, a pont – hely VPN-díjat fizetni. A részleteket a rendszer a [VPN Gateway díjszabás] [ VNETPricing] lapot.

## <a name="troubleshooting"></a>Hibaelhárítás
Amíg a funkció segítségével egyszerűen állíthat, ez nem jelenti azt, hogy a felhasználói élmény lesz-e a probléma ingyenes. Kell észlel a kívánt végpontra elérésével kapcsolatos problémák bizonyos segédprogramokat használhatja az app-konzol közötti kapcsolat teszteléséhez. Nincsenek két konzol használható. Az egyik a Kudu konzol a másik pedig a konzol az Azure Portalon. A Kudu konzol az alkalmazás eléréséhez, válassza a eszközök -> Kudu. Ez megegyezik a helynév: [%] lépjen. scm.azurewebsites.net. Miután, amely megnyílik, nyissa meg a hibakeresési konzolt lapot. Eszközök válassza az Azure-portál központi konzolon, majd az alkalmazásából való beolvasásához konzol ->. 

#### <a name="tools"></a>Eszközök
Az eszközök **ping**, **nslookup** és **tracert** biztonsági okokból a konzolon keresztül nem fog működni. Töltse ki a void, két különálló hozzáadott eszközök. DNS-funkciók teszteléséhez hozzáadtunk egy nameresolver.exe nevű eszközt. A szintaxis a következő:

    nameresolver.exe hostname [optional: DNS Server]

Használhat **nameresolver** ellenőrizze a gazdagép neve, amely az alkalmazása függ. Ezzel a módszerrel tesztelheti Ha semmit a DNS rosszul konfigurálva van, vagy esetleg nem fér hozzá a DNS-kiszolgáló.

A következő eszköz lehetővé teszi, hogy az állomás és port kombinációjához TCP-kapcsolat tesztelése. Ez az eszköz neve **tcpping** és szintaxisa:

    tcpping.exe hostname [optional: port]

A **tcpping** segédprogram jelzi, hogy egy adott állomás és port elérheti. Csak megmutatja sikeres ha: egy alkalmazás figyeli, az állomás és port kombinációját, és a megadott állomás és port az alkalmazásból a hálózati hozzáférést.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Hibakeresés a virtuális hálózathoz való hozzáférés üzemeltetett erőforrásokhoz
Létezik néhány dolog, amely megakadályozhatja, hogy az alkalmazás egy adott állomás és port elérését. A legtöbbször egyike három dolgot:

* **A tűzfal olyan módon.** Van egy tűzfal módja, ha eléri a a TCP időtúllépési. A TCP időtúllépési érték ebben az esetben 21 másodperc. Használja a **tcpping** eszköz kapcsolat teszteléséhez. TCP-időtúllépések miatt tűzfalak túl sok-sok dolog lehet, de kezdeni. 
* **DNS nem érhető el.** A DNS-időtúllépési érték három másodperc / DNS-kiszolgáló. Ha két DNS-kiszolgálók, az időtúllépési érték 6 másodperc. Nameresolver segítségével megtekintheti, hogy működik-e a DNS. Ne felejtse el, nem használhatja az nslookup, amely nem használ a DNS-ben a virtuális hálózat van beállítva.
* **A pont – hely címtartomány érvénytelen.** A pont – hely IP-címtartománynak kell lennie az RFC 1918 magánhálózati IP-címtartományokkal (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Ha a tartományon kívül esik, amely IP-címek használ, dolgot nem fog működni. 

Ha azok az elemek nem választ a problémára, keresse meg az egyszerű többek között az első: 

* Az átjárót a portálon fel állapottal jelenik meg?
* Ehhez tanúsítványokat megjelenítése, hogy szinkronban?
* Bárki változtatta meg a hálózati konfigurációt az érintett ASP "Sync hálózat" művelet végrehajtása nélkül? 

Ha az átjáró nem működik, majd hálózatra, készítsen biztonsági mentést. Ha a tanúsítványok nincsenek szinkronban, az ASP nézetében, a VNet-integráció, és nyomja le a "Hálózat szinkronizálása". Ha azt gyanítja, hogy az a virtuális hálózat konfigurációja, amely nem volt szinkronizálva az ASP-változás történt, majd nyomja le a "Hálózat szinkronizálása".  A "Hálózat szinkronizálása" művelet alkalmazások indítsa újra az ASP, amelyek integrálhatók a virtuális hálózathoz. 

Ha, hogy minden rendben, akkor szüksége megismerésére egy kicsit részletesebben:

* Vannak-e bármilyen más alkalmazást, VNet-integráció segítségével az ugyanazon virtuális hálózatban található erőforrások eléréséhez? 
* Látogasson el az alkalmazás-konzolon és bármilyen egyéb erőforrásokat a virtuális hálózat eléréséhez tcpping használni? 

Ha a fentiek bármelyike igaz, majd a VNet-integráció nem okoz gondot, és a probléma valahol máshol. Ez az, ahol kap további kihívást lennie, mert nem egyszerű lehet, hogy miért nem tudja elérni a gazdagépet: port. Okai a következők:

* megakadályozza a hozzáférést az alkalmazásport a webhely IP-címtartomány a pont a gazdagép van a tűzfal. Nyilvános hozzáférés határainak átlépése alhálózatok gyakran van szükség.
* a cél gazdagép nem működik
* az alkalmazás nem működik
* a megfelelő IP- vagy állomásnév kellett
* az alkalmazás a várttól mint egy másik porton figyel. A folyamat azonosítója a figyelőport meg tudja a végponti gazdagépen a "netstat - aon" használatával. 
* a hálózati biztonsági csoportok vannak konfigurálva, hogy azok való hozzáférés letiltása az alkalmazás-állomás és port a helyet iprange pont oly módon

Ne feledje, hogy milyen IP nem ismeri a pont – hely IP-címtartományban, amely az alkalmazás fogja használni, így engedélyezze a hozzáférést a teljes tartomány szükséges. 

További hibakeresési lépések az alábbiak:

* csatlakozhat a virtuális hálózatban található virtuális Géphez, és próbálja meg elérni az erőforrást állomás: port onnan. A TCP hozzáférési tesztelni, használja a PowerShell-paranccsal **test-netconnection**. A szintaxis a következő:

      test-netconnection hostname [optional: -Port]

* viszi, megjelenik egy alkalmazást, egy virtuális gép és a teszt hozzáférést az adott állomáson és porton az alkalmazásból a konzolról

#### <a name="on-premises-resources"></a>A helyszíni erőforrásokhoz ####

Ha az alkalmazás nem érhető el egy erőforrást a helyszíni, majd ellenőrizze, ha az erőforrás a elérheti a virtuális hálózat. Használja a **test-netconnection** TCP-hozzáférés ellenőrzése a PowerShell-parancsot. A virtuális gép nem tudja elérni a helyszíni erőforráshoz, majd győződjön meg arról, hogy a helyek közötti VPN-kapcsolat működik. Ha működik, majd ellenőrizze a ugyanazokat a műveleteket, valamint a helyszíni átjáró konfigurációjának és állapotának korábban feljegyzett. 

Ha a virtuális hálózat VM elérheti a helyszíni rendszer, de az alkalmazás nem tudja, majd oka valószínűleg egy, az alábbi okok miatt:

* az útvonalak nincsenek konfigurálva a ponttal hely IP-címtartományokhoz a helyszíni átjáróban
* a hálózati biztonsági csoportok forgalomszűrők blokkolják a hozzáférést a pont – hely IP-címtartomány
* a helyszíni tűzfal forgalomszűrők blokkolják a pont – hely IP-címtartomány érkező forgalom


## <a name="powershell-automation"></a>PowerShell automation

Az App Service integrálható az Azure Virtual Network PowerShell-lel. Az alkalmazást kész parancsprogramok esetén lásd: [az Azure App Service alkalmazás csatlakoztatása az Azure Virtual Networkhöz](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>A hibrid kapcsolatok és az App Service Environment-környezetek
Nincsenek három funkciók, amelyek lehetővé teszik a virtuális hálózatok közötti üzemeltetett erőforrásokhoz való hozzáférést. Ezek a következők:

* VNet-integráció
* Hibrid kapcsolatok
* App Service-környezetek

Hibrid kapcsolatokhoz szükség van a hálózaton a hibrid kapcsolat Manager(HCM) nevű továbbító ügynök telepítését. A HCM képesnek kell lennie az Azure-bA és az alkalmazás csatlakoztatása. Hibrid kapcsolatok nem igényel bejövő internetről elérhető végpont a távoli hálózat ez VPN-kapcsolat szükséges. A HCM csak akkor fut, a Windows, illetve azt, hogy legfeljebb öt olyan példányai, magas rendelkezésre állást biztosít. Hibrid kapcsolatok csak az, ha TCP támogatja, és minden hibrid kapcsolat végpontja meg kell egyeznie a megadott gazdagép: port kombinációjához. 

Az App Service Environment funkció lehetővé teszi, hogy az Azure App Service létrehozása egyetlen bérlő példány futtatásához a virtuális hálózatban található. Ha az alkalmazások App Service Environment-környezetben, majd az alkalmazások eléréséhez további lépések nélkül a virtuális hálózatban található erőforrások. Az App Service Environment-környezet az alkalmazások hatékony feldolgozók futtassa, és méretezhető legfeljebb 100 ASP-példányok és. A hálózatkezelési szolgáltatásokat, beleértve az ExpressRoute- és végpontok összes App Service Environment-környezetek működnek.  

Bár egyes használnak megkülönbözteti a kis között átfedés van, ezek közül egyik sem cserélje le a többi. Hogy mely szolgáltatásokat szeretné az igényeinek megfelelően vannak kötve. Példa:

* Ha a fejlesztők és a egy helyen futtatja az Azure-ban az íróasztal mellett a munkaállomáson adatbázis eléréséhez használandó a legegyszerűbb dolog hibrid kapcsolatok. 
* Ha Ön egy nagy szervezet, amely nagy mennyiségű fel szeretné webes tulajdonságok a nyilvános felhő, és kezelheti őket a saját hálózati, majd nyissa meg az App Service environmenttel szeretné. 
* Ha több, a virtuális hálózatban található erőforrások elérését igénylő alkalmazások, VNet-integráció, a mód. 

Ha a virtuális hálózat már csatlakoztatva van a helyszíni hálózathoz, majd a VNet-integráció használatával, vagy egy App Service Environment-környezet egy egyszerű módja annak felhasználását a helyszíni erőforrásokhoz. A virtuális hálózat nem kapcsolódik a helyszíni hálózathoz, majd, hogy a virtuális hálózat és a telepítés a HCM összehasonlítása a helyek közötti VPN beállításához a sokkal több terhelést jelenthet. 

A működési különbségek túl van vannak is díjszabásbeli különbségeket. Az App Service Environment funkció a prémium szolgáltatásajánlat azonban kínálja a legtöbb hálózati konfigurációs lehetőségekkel más nagyszerű funkciók mellett. VNet-integráció a Standard vagy prémium szintű ASP használható, és tökéletes megoldás, a több-bérlős App Service-ben a virtuális hálózatban található erőforrások biztonságosan felhasználásához. Hibrid kapcsolatok jelenleg a fiók, amely ingyenes, és kérje le fokozatosan drágább szintek alapján van szüksége a BizTalk függ. Esetén, azonban több több hálózaton működik, nincs más szolgáltatás például a hibrid kapcsolataira, melyek lehetővé teszik a jól több mint 100, különálló hálózatokból erőforrások eléréséhez. 

## <a name="new-vnet-integration"></a>Új VNet-integráció ##

Van egy új verziója, a VNet-integráció funkció, amely nem függ a pont – hely VPN-technológia. A már meglévő szolgáltatást, ellentétben az új előzetes verziójú funkció az ExpressRoute- és végpontok fog működni. 

Az új funkciót csak a újabb Azure App Service-skálázási egység érhető el. Ha PremiumV2 méretezheti, majd Ön egy App Service-ben újabb skálázási egység. A virtuális hálózatok közötti integráció felhasználói felület, a portál jelzi, ha az alkalmazás az új VNet-integráció szolgáltatással. 

Az új verzió előzetes verzióban érhető el, és a következő jellemzőkkel rendelkezik.

* Nincs átjárót kell használnia az új virtuális hálózat integrációja
* Az ExpressRoute-kapcsolatok között hozzáférhetnek erőforrásokhoz, az ExpressRoute integrálása mellett további konfiguráció csatlakoztatott virtuális hálózat nélkül.
* Az alkalmazás és a virtuális hálózat ugyanabban a régióban kell lennie
* Az új szolgáltatáshoz a Resource Manager virtuális hálózatban található egy nem használt alhálózat.
* Az App Service-csomag a Standard, prémium szintű vagy PremiumV2 terv kell lennie.
* Éles számítási feladatok nem támogatottak az új funkciót, bár előzetes verzióban érhető el
* Az alkalmazás egy Azure App Service-környezet, amely képes a prémium v2 vertikális felskálázása kell lennie.
* Az új VNet-integráció szolgáltatás nem működik az alkalmazások App Service Environment-környezetben.
* Integrált alkalmazás a virtuális hálózat nem törölhető.  
* Útválasztási táblázatokat és globális társviszony-létesítés még nem érhető el az új VNet-integráció.  
* Az App Service-csomag példányonként egy címet használja. Alhálózat méretét a hozzárendelés után nem módosítható, mivel a is több, mint a maximális skálázhatósága alhálózatot használjon. 32-címekkel rendelkező/27-es nem az ajánlott mérete, mert a 20-példányok méretezett App Service-csomagot, amely helyet biztosít.  Védett erőforrásokhoz az új VNet-integráció funkcióval szolgáltatásvégpont használhatja fel. Ehhez engedélyezze a szolgáltatásvégpontokat a virtuális hálózatok közötti integráció használt alhálózat.

Az új funkció használatához:

1. Nyissa meg a hálózat felhasználói felület a portálon. Ha az alkalmazás tudja használni az új szolgáltatást, majd látni fogja használni az új előzetes verziójú funkció teszi lehetővé.  

 ![Válassza ki az új előzetes verziójú VNet-integráció][6]

1. Válassza ki **hozzáadása virtuális hálózathoz (előzetes verzió)**.  

1. Válassza ki a Resource Manager virtuális hálózattal, szeretné integrálni és majd hozzon létre egy új alhálózatot vagy válasszon ki egy meglévő üres alhálózatot. Az integráció végrehajtásához kevesebb mint egy percet vesz igénybe. Az integráció során az alkalmazás újraindul.  Integráció befejezése után látni fogja részletek integrálva van a virtuális hálózatok közötti és a egy módosíthatóvá tájékoztatja, hogy a funkció előzetes verzióban érhető el.

 ![Válassza ki a virtuális hálózat és alhálózat][7]

Ahhoz, hogy az alkalmazást, amelynek része a virtuális hálózat DNS-kiszolgáló használatához, hozzon létre egy alkalmazásbeállítást az alkalmazás, ahol a név WEBSITE_DNS_SERVER, értéke pedig a kiszolgáló IP-címét.  Ha rendelkezik egy másodlagos DNS-kiszolgálót, majd hozzon létre egy másik, ahol a név WEBSITE_DNS_ALT_SERVER Alkalmazásbeállítás értéke pedig a kiszolgáló IP-címét. 

Az alkalmazás leválasztása a virtuális hálózaton, válassza ki a **Disconnect**. A művelet újraindítja a webalkalmazás. 

Az új VNet-integráció szolgáltatás lehetővé teszi, hogy a Szolgáltatásvégpontok használatát.  Szolgáltatásvégpontok használatára az alkalmazását, segítségével az új VNet-integráció kapcsolódni egy kijelölt virtuális hálózathoz, majd válassza a Szolgáltatásvégpontok az alhálózaton, az integráció során használt. 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

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
[creategatewaysubnet]: http://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: http://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: http://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
