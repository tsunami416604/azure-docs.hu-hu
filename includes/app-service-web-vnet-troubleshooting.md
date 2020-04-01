---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 652d42d6e2d9e909c3a03bd82a3a36f91bc73807
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419537"
---
A funkció könnyen beállítható, de ez nem jelenti azt, hogy a tapasztalat problémamentes lesz. Ha problémákat tapasztal a kívánt végpont elérésével kapcsolatban, néhány segédprogram ot használhat a kapcsolat teszteléséhez az alkalmazáskonzolról. Két konzolt használhat. Az egyik a Kudu konzol, a másik pedig a konzol az Azure Portalon. Ha el szeretné érni a Kudu konzolt az alkalmazásból, nyissa meg a**Kudu** **eszközök** > lehetőséget. A Kudo konzolt a [sitename].scm.azurewebsites.net címen is elérheti. A webhely betöltése után lépjen a **Debug konzol** fülre. Ha az alkalmazásból szeretné megfokkal megfokkal elérhetőnek kell lennie az Azure Portalon tárolt konzolnak, nyissa meg a Tools Console című > **webhelyet.** **Tools**

#### <a name="tools"></a>Eszközök
Az eszközök **ping**, **nslookup**, és **tracert** nem fog működni a konzolon keresztül, mert a biztonsági korlátok. Az üresség kitöltéséhez két külön szerszámot adhozzá. A DNS-funkciók teszteléséhez hozzáadtunk egy **nameresolver.exe**nevű eszközt. A szintaxis a következő:

    nameresolver.exe hostname [optional: DNS Server]

A nameresolver segítségével ellenőrizheti az alkalmazás által függő állomásneveket. Így tesztelheti, hogy van-e valami helytelenül konfigurálva a DNS-sel, vagy esetleg nincs hozzáférése a DNS-kiszolgálóhoz. Az alkalmazás által használt DNS-kiszolgálót a WEBSITE_DNS_SERVER és a WEBSITE_DNS_ALT_SERVER környezeti változóinak megtekintésével láthatja.

A következő eszközzel tesztelheti a TCP-kapcsolatot egy állomás- és portkombinációval. Ezt az eszközt **tcpping-nek nevezik,** és a szintaxis a következő:

    tcpping.exe hostname [optional: port]

A **tcpping** segédprogram jelzi, hogy el érhet-e egy adott állomást és portot. Csak akkor mutathat sikeres, ha egy alkalmazás figyeli a gazdagép és a port kombinációt, és az alkalmazásból hálózati hozzáférés van a megadott állomáshoz és porthoz.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>A virtuális hálózaton tárolt erőforrásokhoz való hozzáférés hibakeresése
Számos dolog megakadályozhatja, hogy az alkalmazás elérjen egy adott gazdatestet és portot. Az idő nagy részében ez az egyik ilyen dolog:

* **A tűzfal útban van.** Ha van egy tűzfal az úton, akkor nyomja meg a TCP időout. A TCP időhosszabbítása ebben az esetben 21 másodperc. A **tcpping** eszközzel tesztelje a kapcsolatot. A TCP időtúllépésre számos tűzfalon túli dolog is okozhatja, de ott kezdődhet.
* **A DNS nem érhető el.** A DNS-időelés DNS-kiszolgálónként 3 másodperc. Ha két DNS-kiszolgálóval rendelkezik, az időelés 6 másodperc. A nameresolver segítségével ellenőrizheti, hogy a DNS működik-e. Az nslookup nem használható, mert az nem azt a DNS-t használja, amelyhez a virtuális hálózat konfigurálva van. Ha nem érhető el, lehet, hogy egy tűzfal vagy NSG blokkolja a DNS-hez való hozzáférést, vagy lehet, hogy nem működik.

Ha ezek az elemek nem válaszolnak a problémáidra, először a következő dolgokat keresd:

**Regionális virtuális hálózatok integrációja**
* Az úti cél nem RFC1918-as cím, és nincs WEBSITE_VNET_ROUTE_ALL 1-re állítva?
* Van egy NSG blokkolja a kilépést az integrációs alhálózatból?
* Ha az Azure ExpressRoute-on vagy egy VPN-en keresztül megy, a helyszíni átjáró úgy van beállítva, hogy a forgalmat visszairányítsa az Azure-ba? Ha el tudja érni a végpontok a virtuális hálózatban, de nem a helyszínen, ellenőrizze az útvonalakat.
* Rendelkezik elegendő engedéllyel az integrációs alhálózaton a delegálás beállításához? A regionális virtuális hálózatintegrációs konfiguráció során az integrációs alhálózat delegált a Microsoft.Web. A virtuális hálózat integrációs felhasználói felülete automatikusan delegálja az alhálózatot a Microsoft.Web webhelyen. Ha a fiók nem rendelkezik megfelelő hálózati engedélyekkel a delegálás beállításához, szüksége lesz valakire, aki beállíthatja az integrációs alhálózat attribútumait az alhálózat delegálásához. Az integrációs alhálózat manuális delegálásához nyissa meg az Azure Virtual Network alhálózati felhasználói felületet, és állítsa be a Microsoft.Web delegálását.

**Átjáróhoz szükséges virtuális hálózat integrációja**
* Az RFC 1918-as tartományban van a pont-hely címtartomány (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Az átjáró megjelenik a portálon? Ha az átjáró nem működik, akkor hozza vissza.
* A tanúsítványok szinkronban vannak, vagy azt gyanítja, hogy a hálózati konfiguráció megváltozott?  Ha a tanúsítványok nincsenek szinkronban, vagy azt gyanítja, hogy olyan változás történt a virtuális hálózati konfiguráción, amely nem volt szinkronizálva az asp-kkel, válassza a **Hálózat szinkronizálása**lehetőséget.
* Ha vpn-kapcsolaton megy keresztül, a helyszíni átjáró úgy van beállítva, hogy a forgalmat visszairányítsa az Azure-ba? Ha el tudja érni a végpontok a virtuális hálózatban, de nem a helyszínen, ellenőrizze az útvonalakat.
* Olyan együttélési átjárót próbál használni, amely támogatja a ponttól a helyig és az ExpressRoute-ot is? Az együttélési átjárók nem támogatottak a virtuális hálózatok integrációja.

A hálózati problémák hibakeresése kihívást jelent, mert nem látja, hogy mi blokkolja a hozzáférést egy adott állomás:port kombinációhoz. Néhány ok a következők:

* Van egy tűzfal fel a fogadó, amely megakadályozza a hozzáférést az alkalmazás port a pont-hely IP-tartományban. Az alhálózatok keresztezése gyakran nyilvános hozzáférést igényel.
* A célállomásod nem.
* A jelentkezési lapod nem fogy.
* Rossz IP vagy hostname volt.
* Az alkalmazás a várttól eltérő porton figyel. A folyamatazonosítót a figyelőporttal egyeztetheti a végpontgazda "netstat -aon" használatával.
* A hálózati biztonsági csoportok úgy vannak konfigurálva, hogy megakadályozzák az alkalmazásgazda- és porthoz való hozzáférést a pont-hely IP-tartományból.

Nem tudja, hogy az alkalmazás ténylegesen milyen címet használ. Ez lehet bármilyen cím az integrációs alhálózatban vagy a pont-hely címtartományban, ezért engedélyeznie kell a hozzáférést a teljes címtartományból.

További hibakeresési lépések:

* Csatlakozzon egy virtuális géphez a virtuális hálózatban, és próbálja meg elérni az erőforrás-állomás:port onnan. A TCP-hozzáférés teszteléséhez használja a PowerShell-parancs **teszthálózati kapcsolatát.** A szintaxis a következő:

      test-netconnection hostname [optional: -Port]

* Hozzon létre egy alkalmazást egy virtuális gépen, és tesztelje az adott állomáshoz és porthoz való hozzáférést az alkalmazásból az alkalmazásból a **tcpping**használatával.

#### <a name="on-premises-resources"></a>Helyszíni erőforrások ####

Ha az alkalmazás nem tud elérni egy erőforrást a helyszínen, ellenőrizze, hogy el tudja-e érni az erőforrást a virtuális hálózatról. Használja a **test-netconnection** PowerShell parancsot a TCP-hozzáférés ellenőrzéséhez. Ha a virtuális gép nem tudja elérni a helyszíni erőforrást, előfordulhat, hogy a VPN- vagy ExpressRoute-kapcsolat nincs megfelelően konfigurálva.

Ha a virtuális hálózat által üzemeltetett virtuális gép elérheti a helyszíni rendszert, de az alkalmazás nem, az ok valószínűleg az alábbi okok egyike:

* Az útvonalak nincsenek konfigurálva az alhálózat vagy a pont-hely címtartományok a helyszíni átjáró.
* A hálózati biztonsági csoportok blokkolják a hozzáférést a pont-hely IP-tartományhoz.
* A helyszíni tűzfalak blokkolják a forgalmat a pont-hely IP-tartományból.
* Nem RFC 1918-as címet próbál elérni a regionális virtuális hálózat integrációs funkciójával.