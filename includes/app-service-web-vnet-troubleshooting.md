---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671489"
---
Bár a funkció könnyen beállítható, ez nem jelenti azt, hogy a tapasztalat problémamentes lesz. Ha problémákba ütközik a kívánt végpont elérésével, vannak segédprogramok, amelyek segítségével tesztelheti a kapcsolatot az alkalmazáskonzolról. Két konzolt használhat. Az egyik a Kudu konzol, a másik pedig a konzol az Azure Portalon. Ha el szeretné érni a Kudu konzolt az alkalmazásból, nyissa meg az Eszközök -> Kudu. A Kudo konzolt a [sitename].scm.azurewebsites.net címen is elérheti. Miután a webhely betöltődik, lépjen a Debug konzol fülre. Az Azure Portalüzemeltetett konzol hoz, majd az alkalmazásból az Eszközök -> Console. 

#### <a name="tools"></a>Eszközök
Az eszközök **ping**, **nslookup**, és **tracert** nem fog működni a konzolon keresztül biztonsági korlátok miatt. Az üresség kitöltéséhez két külön szerszámot adtak hozzá. A DNS-funkciók teszteléséhez hozzáadtunk egy nameresolver.exe nevű eszközt. A szintaxis a következő:

    nameresolver.exe hostname [optional: DNS Server]

**A nameresolver** segítségével ellenőrizheti az alkalmazás által függő állomásneveket. Így tesztelheti, hogy van-e rosszul konfigurált adat a DNS-sel, vagy esetleg nincs hozzáférése a DNS-kiszolgálóhoz. Az alkalmazás által a konzolon használt DNS-kiszolgálót a környezeti WEBSITE_DNS_SERVER és WEBSITE_DNS_ALT_SERVER.

A következő eszköz lehetővé teszi, hogy tesztelje a TCP-kapcsolat egy állomás és port kombinációja. Ezt az eszközt **tcpping-nek nevezik,** és a szintaxis a következő:

    tcpping.exe hostname [optional: port]

A **tcpping** segédprogram jelzi, hogy el érhet-e egy adott állomást és portot. Csak akkor mutathat sikert, ha: van egy alkalmazás figyel a gazdagép és a port kombinációja, és van hálózati hozzáférés az alkalmazásból a megadott gazdagéphez és porthoz.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Virtuális hálózat által üzemeltetett erőforrásokhoz való hozzáférés hibakeresése
Számos olyan dolog van, amely megakadályozhatja, hogy az alkalmazás elérjen egy adott gazdatestet és portot. Az idő nagy részében ez az egyik három dolog:

* **A tűzfal útban van.** Ha van egy tűzfal az úton, akkor nyomja meg a TCP időout. A TCP időhosszabbítása ebben az esetben 21 másodperc. A **tcpping** eszközzel tesztelje a kapcsolatot. A TCP időtúljárások a tűzfalakon túl sok mindennek okozhatnak, de ott kezdődnek. 
* **A DNS nem érhető el.** A DNS-időmeghosszabbítás DNS-kiszolgálónként három másodperc. Ha két DNS-kiszolgálóval rendelkezik, az időelés 6 másodperc. A nameresolver segítségével ellenőrizheti, hogy a DNS működik-e. Ne feledje, hogy nem használhatja az nslookup-ot, mivel az nem használja azt a DNS-t, amelyhez a virtuális hálózat konfigurálva van. Ha nem érhető el, lehet, hogy egy tűzfal vagy NSG blokkolja a DNS-hez való hozzáférést, vagy lehet, hogy nem működik.

Ha ezek az elemek nem válaszolnak a problémáidra, először a következő dolgokat keresd: 

**regionális virtuális hálózat integrációja**
* az úticél nem RFC1918-as cím, és nincs WEBSITE_VNET_ROUTE_ALL 1-re állítva?
* van egy NSG blokkolja a kilépést az integrációs alhálózatból?
* ha expresszroute-on vagy VPN-en keresztül halad, a helyszíni átjáró úgy van beállítva, hogy a forgalmat visszairányítsa az Azure-ba? Ha a virtuális hálózat végpontjait elérheti, de a helyszíni nem, ellenőrizze az útvonalakat.
* elegendő engedéllyel rendelkezik a delegálás beállításához az integrációs alhálózaton? A regionális virtuálishálózat-integrációs konfiguráció során az integrációs alhálózat delegálva lesz a Microsoft.Web számára. A virtuális hálózat integrációs felhasználói felülete automatikusan delegálja az alhálózatot a Microsoft.Web webhelyen. Ha fiókja nem rendelkezik megfelelő hálózati engedélyekkel a delegálás beállításához, szüksége lesz valakire, aki az integrációs alhálózaton beállíthatja az alhálózat delegálásához szükséges attribútumokat. Az integrációs alhálózat manuális delegálásához nyissa meg az Azure Virtual Network alhálózati felhasználói felületet, és állítsa be a Microsoft.Web delegálását. 

**átjáró szükséges virtuális hálózat integrációja**
* a pont-hely címtartomány az RFC 1918 tartományokban (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Az átjáró megjelenik a portálon? Ha az átjáró nem működik, akkor hozza vissza.
* A tanúsítványok szinkronban vannak, vagy gyanítja, hogy a hálózati konfiguráció megváltozott?  Ha a tanúsítványok nincsenek szinkronban, vagy azt gyanítja, hogy a virtuális hálózat konfigurációja nem lett szinkronizálva az asp-k, majd nyomja meg a "Sync Network".
* Ha vpn-kapcsolaton megy keresztül, a helyszíni átjáró úgy van konfigurálva, hogy a forgalmat visszairányítsa az Azure-ba? Ha a virtuális hálózat végpontjait elérheti, de a helyszíni nem, ellenőrizze az útvonalakat.
* Olyan együttélési átjárót próbál használni, amely támogatja a ponttól a helyig és az ExpressRoute-ot is? Az együttélési átjárók nem támogatottak a Virtuálishálózat-integrációval.

A hálózati problémák hibakeresése kihívást jelent, mivel ott nem látható, hogy mi blokkolja a hozzáférést egy adott állomás:port kombinációhoz. Néhány okok közé tartozik:

* Van egy tűzfal fel a fogadó megakadályozza a hozzáférést az alkalmazás port a pont-hely IP-tartományban. Az alhálózatok keresztezéséhez gyakran nyilvános hozzáférés szükséges.
* A célállomásod nem.
* A jelentkezési lapod nem fogy.
* Rossz IP vagy hostname volt.
* Az alkalmazás a várttól eltérő porton figyel. A folyamatazonosítót a figyelőporttal egyeztetheti a végpontgazda "netstat -aon" használatával. 
* A hálózati biztonsági csoportok úgy vannak konfigurálva, hogy megakadályozzák az alkalmazásgazda- és porthoz való hozzáférést a ponttól a hely IP-tartományáig.

Ne feledje, hogy nem tudja, hogy az alkalmazás ténylegesen milyen címet fog használni. Ez lehet bármilyen cím az integrációs alhálózatban vagy a pont-hely címtartományban, ezért engedélyeznie kell a hozzáférést a teljes címtartományból. 

További hibakeresési lépések:

* Csatlakozzon egy virtuális géphez a virtuális hálózatban, és próbálja meg elérni az erőforrás-állomás:port onnan. A TCP-hozzáférés teszteléséhez használja a PowerShell-parancs **teszthálózati kapcsolatát.** A szintaxis a következő:

      test-netconnection hostname [optional: -Port]

* Egy alkalmazás előjátéka virtuális gépen, és az adott állomáshoz és porthoz való hozzáférés tesztelése az alkalmazásból az alkalmazásból **a tcpping** használatával

#### <a name="on-premises-resources"></a>Helyszíni erőforrások ####

Ha az alkalmazás nem tud elérni egy erőforrást a helyszínen, majd ellenőrizze, hogy a virtuális hálózatról elérheti-e az erőforrást. Használja a **test-netconnection** PowerShell parancsot a TCP-hozzáférés ellenőrzéséhez. Ha a virtuális gép nem tudja elérni a helyszíni erőforrást, előfordulhat, hogy a VPN- vagy ExpressRoute-kapcsolat nincs megfelelően konfigurálva.

Ha a virtuális hálózat üzemeltetett virtuális gépe elérheti a helyszíni rendszert, de az alkalmazás nem, akkor az ok valószínűleg az alábbi okok egyike:

* Az útvonalak nincsenek konfigurálva az alhálózattal vagy a helycímtartományokkal a helyszíni átjáróban.
* A hálózati biztonsági csoportok blokkolják a hozzáférést a pont-hely IP-tartományhoz.
* A helyszíni tűzfalak blokkolják a point-to-site IP-tartományból érkező forgalmat.
* Nem RFC 1918-as címet próbál elérni a regionális virtuális hálózat integrációs szolgáltatáshasználatával.