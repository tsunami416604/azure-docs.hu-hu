---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671489"
---
Habár a funkció egyszerűen beállítható, ez nem jelenti azt, hogy a probléma ingyenes lesz. Ha problémák merülnek fel a kívánt végpont elérésekor, néhány segédprogram használható az alkalmazás-konzolról való kapcsolat tesztelésére. Két konzolt használhat. Az egyik a kudu-konzol, a másik pedig a Azure Portal konzolja. A kudu-konzol alkalmazásból való eléréséhez nyissa meg az eszközök-> kudu. A Kudo-konzolt a következő helyen is elérheti: [sitename]. SCM. azurewebsites. net. A webhely betöltése után lépjen a Debug konzol lapra. Az Azure Portal üzemeltetett konzoljának eléréséhez nyissa meg az eszközök-> konzolt. 

#### <a name="tools"></a>Eszközök
A **ping**, az **nslookup**és a **tracert** eszköz a biztonsági korlátozások miatt nem fog működni a konzolon. Az üresség kitöltéséhez két különálló eszköz lett hozzáadva. A DNS-funkciók teszteléséhez hozzáadott egy nameresolver. exe nevű eszközt. A szintaxis a következő:

    nameresolver.exe hostname [optional: DNS Server]

A **nameresolver** segítségével megtekintheti az alkalmazástól függő gazdagépeket. Így ellenőrizheti, hogy van-e valamilyen helytelenül konfigurálva a DNS-sel, vagy lehet, hogy nem fér hozzá a DNS-kiszolgálóhoz. Az alkalmazás által a konzolon használt DNS-kiszolgáló a környezeti változók WEBSITE_DNS_SERVER és WEBSITE_DNS_ALT_SERVER alapján tekinthető meg.

A következő eszköz lehetővé teszi a TCP-kapcsolat tesztelését egy gazdagép és port kombinációhoz. Az eszköz neve **tcpping** , és a szintaxis a következő:

    tcpping.exe hostname [optional: port]

A **tcpping** segédprogrammal megtudhatja, hogy elérhető-e egy adott gazdagép és port. Ez csak akkor mutathat sikert, ha: van egy alkalmazás, amely a gazdagép és a port kombinációját figyeli, és az alkalmazásból hálózati hozzáférés van a megadott gazdagéphez és porthoz.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>A VNet által üzemeltetett erőforrásokhoz való hozzáférés hibakeresése
Számos dolog megakadályozhatja, hogy az alkalmazás elérjen egy adott gazdagépet és portot. Az idő nagy része három dolog egyike:

* **A tűzfal éppen folyamatban van.** Ha tűzfallal rendelkezik, a TCP-időtúllépést fogja elérni. Ebben az esetben a TCP-időkorlát 21 másodperc. A kapcsolat teszteléséhez használja a **tcpping** eszközt. A TCP-időtúllépések a tűzfalakon túl sok dolog okozhatja, de ott is elindulhat. 
* **A DNS nem érhető el.** A DNS-időtúllépési érték a DNS-kiszolgálón három másodperc. Ha két DNS-kiszolgálóval rendelkezik, az időtúllépés 6 másodperc. A nameresolver használatával ellenőrizze, hogy a DNS működik-e. Ne feledje, hogy nem használhatja az nslookupt úgy, hogy ne használja a DNS-t, amelyet a VNet konfigurált. Ha nem érhető el, lehet, hogy tűzfallal vagy NSG blokkolja a hozzáférést a DNS-hez, vagy leállt.

Ha ezek az elemek nem válaszolnak a problémákra, először tekintse meg a következő dolgokat: 

**regionális VNet-integráció**
* a cél egy nem RFC1918-címet tartalmaz, és nincs WEBSITE_VNET_ROUTE_ALL beállítva 1-re?
* van NSG blokkolása az integrációs alhálózatról?
* Ha a ExpressRoute vagy egy VPN-en halad át, a helyszíni átjáró úgy van konfigurálva, hogy a forgalmat az Azure-ba irányítsa? Ha a VNet a végpontokat is elérheti, de a helyszínen nem, ellenőrizze az útvonalakat.
* van elegendő engedélye a delegálás beállítására az integrációs alhálózaton? A regionális VNet integrációs konfigurációjában az integrációs alhálózat a Microsoft. Web számára lesz delegálva. A VNet-integráció felhasználói felülete automatikusan delegálja az alhálózatot a Microsoft. Web számára. Ha a fiókja nem rendelkezik megfelelő hálózati engedélyekkel a delegálás beállításához, szüksége lesz valakire, aki az integrációs alhálózaton attribútumokat állíthat be az alhálózat delegálásához. Az integrációs alhálózat manuális delegálásához nyissa meg az Azure Virtual Network alhálózat felhasználói felületét, és állítsa be a Microsoft. Web delegálását. 

**átjáró szükséges VNet-integráció**
* a pont – hely címtartomány az RFC 1918-tartományokban (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Megjelenik az átjáró a portálon? Ha az átjáró nem működik, hozza létre a biztonsági mentést.
* A tanúsítványok szinkronban jelennek meg, vagy feltételezi, hogy a hálózati konfiguráció megváltozott?  Ha a tanúsítványok nincsenek szinkronban, vagy ha azt gyanítja, hogy módosult a VNet konfigurációja, amely nem lett szinkronizálva a ASP, akkor nyomja meg a "Sync Network" (hálózat szinkronizálása) lehetőséget.
* Ha a VPN-en halad át, a helyszíni átjáró úgy van konfigurálva, hogy a forgalmat az Azure-ba irányítsa? Ha a VNet a végpontokat is elérheti, de a helyszínen nem, ellenőrizze az útvonalakat.
* olyan párhuzamos átjárót próbál használni, amely támogatja mind a pont-hely, mind a ExpressRoute? Az együttélési átjárók nem támogatottak a VNet-integrációval.

A hálózati problémák hibakeresése kihívást jelent, mivel nem látható, hogy mi blokkolja a hozzáférést egy adott gazdagéphez: Port kombináció. Az okok némelyike a következőkből áll:

* A gazdagépen lévő tűzfallal megakadályozhatja, hogy hozzáférjen az alkalmazás portjához a pontról a hely IP-tartományába. Az alhálózatok átlépéséhez gyakran nyilvános hozzáférésre van szükség.
* A célként megadott gazdagép nem működik.
* Az alkalmazás nem érhető el.
* Nem megfelelő IP-címet vagy állomásnevet adott meg.
* Az alkalmazás a várttól eltérő portot figyel. A folyamat AZONOSÍTÓját megtekintheti a figyelő porton a "netstat-Aon" paranccsal a végponti gazdagépen. 
* A hálózati biztonsági csoportok úgy vannak konfigurálva, hogy ne férhessenek hozzá az alkalmazás-gazdagéphez és-porthoz a pontról a hely IP-tartományába.

Ne feledje, hogy nem tudja, milyen címet fog használni az alkalmazás ténylegesen. Az integrációs alhálózat vagy a pont – hely címtartomány bármely címe lehet, ezért a teljes címtartományból engedélyeznie kell a hozzáférést. 

További hibakeresési lépések a következők:

* Kapcsolódjon egy virtuális géphez a VNet, és próbálja meg elérni az erőforrás-gazdagépet: innen a port. A TCP-hozzáférés teszteléséhez használja a következő PowerShell **-parancsot: test-NETCONNECTION**. A szintaxis a következő:

      test-netconnection hostname [optional: -Port]

* egy alkalmazás üzembe helyezése egy virtuális gépen, valamint a gazdagép és a port elérésének tesztelése a konzolról az **tcpping** használatával

#### <a name="on-premises-resources"></a>Helyszíni erőforrások ####

Ha az alkalmazás nem tud helyszíni erőforrást elérni, akkor ellenőrizze, hogy elérhető-e az erőforrás a VNet. Használja a **test-NETCONNECTION** PowerShell-parancsot a TCP-hozzáférés ellenőrzéséhez. Ha a virtuális gép nem tudja elérni a helyszíni erőforrást, előfordulhat, hogy a VPN-vagy ExpressRoute-kapcsolat nem megfelelően van konfigurálva.

Ha a VNet által üzemeltetett virtuális gép elérheti a helyszíni rendszerét, de az alkalmazása nem lehetséges, akkor az oka valószínűleg az alábbi okok egyike:

* Az útvonalak nincsenek konfigurálva az alhálózattal, vagy a helyszíni átjárón lévő hely címére mutatnak.
* A hálózati biztonsági csoportok blokkolja a pont – hely IP-címtartomány elérését.
* A helyszíni tűzfalak blokkolja a pont – hely IP-címtartomány forgalmát.
* Nem RFC 1918-es címről szeretne elérni a regionális VNet integrációs funkciójával.