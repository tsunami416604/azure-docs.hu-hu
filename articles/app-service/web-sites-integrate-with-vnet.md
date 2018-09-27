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
ms.date: 09/24/2018
ms.author: ccompy
ms.openlocfilehash: 5eab09d5dffe16517e8c18eb0281716618ca0286
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166223"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Az alkalmazás integrálása az Azure-beli virtuális hálózathoz
Ez a dokumentum ismerteti az Azure App Service virtuális hálózat integrációja, és bemutatja, hogyan állíthatja be az Apps [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Ha ismeri az Azure-beli virtuális hálózatok (Vnetek), akkor egy olyan funkció, amely lehetővé teszi számos, az Azure-erőforrások elérését Ön szabályozza a nem internet routeable hálózat helyezni. Ezek a hálózatok csatlakozhat a helyszíni hálózataihoz többféle VPN technológia használatával. További tudnivalók az Azure-beli virtuális hálózatok, az adatok itt kezdje: [Azure Virtual Network áttekintése][VNETOverview]. 

Az Azure App Service rendelkezik két formában. 

1. A több-bérlős rendszereket, amelyek támogatják a teljes tartomány díjszabási csomaggal érhető el
2. Az App Service Environment (ASE) prémium szintű funkció, amely üzembe helyezi azokat a virtuális hálózathoz. 

Ez a dokumentum halad végig a VNet-integráció és a nem App Service Environment-környezet. Ha szeretne további információ az ASE-funkció, kezdje itt az adatokat: [App Service Environment bemutatása][ASEintro].

VNet-integráció a webes alkalmazás hozzáférést biztosít a virtuális hálózatban lévő erőforrásokra irányuló, de nem titkos hozzáférési jogot a webes alkalmazás a virtuális hálózatról. Privát hozzáférést utal, hogy az alkalmazás csak elérhetőnek magánhálózaton például az Azure virtuális hálózatban. Privát hozzáférést csak akkor használható a konfigurált egy belső Load Balancer (ILB) rendelkező ASE. Az ILB ASE használata részletesen, kezdje Itt a cikk: [létrehozása és a egy ILB ASE használatával][ILBASE]. 

Egy gyakori forgatókönyv, ahol a VNet-integráció használna az engedélyezi a hozzáférést a webalkalmazás egy adatbázisba vagy egy virtuális gépen az Azure virtuális hálózaton futó webes szolgáltatás. A VNet-integráció nem kell egy nyilvános végpontot elérhetővé az alkalmazások is, de a gép nem internetes irányítható Magáncímeket helyette használja. 

A VNet-integráció funkciót:

* egy Standard, prémium szintű vagy elkülönített díjszabású csomag szükséges 
* együttműködik a klasszikus vagy Resource Manager 
* támogatja a TCP és UDP
* együttműködik a webes, mobil-, API apps és a függvényalkalmazások
* lehetővé teszi az alkalmazás egyszerre csak 1 virtuális hálózathoz való kapcsolódáshoz
* lehetővé teszi, hogy az App Service-csomag integrációját a legfeljebb öt virtuális hálózatok 
* lehetővé teszi, hogy az azonos virtuális hálózaton használható több alkalmazás az App Service-csomag
* a virtuális hálózati átjáró 99,9 %-os szolgáltatói szerződés miatt az SLA-t támogatja

Néhány dolog, amely a VNet-integráció nem támogatja, beleértve:

* a meghajtó csatlakoztatása
* AD-integráció 
* NetBios
* privát hozzáférést

### <a name="getting-started"></a>Első lépések
Az alábbiakban néhány dolog láthat, tartsa szem előtt a webalkalmazás csatlakozik egy virtuális hálózatot:

* VNet-integráció csak az alkalmazásokkal működik egy **Standard**, **prémium**, vagy **elkülönített** tarifacsomagot. Ha a funkció engedélyezéséhez, és ezután méretezése App Service-csomag egy nem támogatott díjszabási csomagot, az alkalmazások megszakad az általuk használt virtuális hálózatokhoz. 
* Ha már létezik a cél virtuális hálózattal, hogy pont – hely VPN engedélyezve van a dinamikus útválasztású átjáró, mielőtt csatlakozhat egy alkalmazáshoz kell rendelkeznie. Ha az átjáró a statikus útválasztás van konfigurálva, nem engedélyezheti a pont – hely virtuális magánhálózati (VPN).
* A virtuális hálózat, az App Service Plan(ASP) ugyanabban az előfizetésben kell lennie.
* Ha az átjáró már létezik a pont – hely engedélyezve van, és nem szerepel az alapszintű Termékváltozat, IKEV2 le kell tiltani a pont – hely konfigurációban.
* Az alkalmazásokat, amelyek integrálhatók a virtuális hálózat használatához a virtuális hálózathoz tartozó DNS.
* Alapértelmezés szerint az integráló alkalmazások csak irányíthatja a forgalmat a virtuális hálózat a virtuális hálózathoz definiált útvonalak alapján be. 

## <a name="enabling-vnet-integration"></a>VNet-integráció engedélyezése

Lehetősége van az alkalmazás egy új vagy meglévő virtuális hálózathoz csatlakozni. Az integrációs részeként egy új hálózatot hoz létre, ha csak a virtuális hálózat létrehozása, valamint egy dinamikus útválasztású átjárót az Ön számára előre konfigurálva, és pont – hely VPN engedélyezve van. 

> [!NOTE]
> Egy új virtuális hálózati integráció konfigurálásához több percig is eltarthat. 
> 
> 

Ahhoz, hogy a VNet-integráció, nyissa meg az alkalmazás beállításait, és válassza ki a hálózatkezelés. A felhasználói felületen, a megnyíló három hálózatkezelési lehetőségeket kínál. Ez az útmutató csak követését VNet-integráció, azonban a hibrid kapcsolatok és App Service Environment-környezetek ismertetik a jelen dokumentum. 

Az alkalmazás a nem a megfelelő tarifacsomagot, ha a felhasználói felület lehetővé teszi, hogy a terv szerint egy magasabb díjszabási csomagot tetszőleges méretezése.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Egy már meglévő virtuális hálózattal VNet-integráció engedélyezése
A virtuális hálózatok közötti integráció felhasználói felület lehetővé teszi a virtuális hálózatok listájából válassza ki. A klasszikus virtuális hálózatok jelezve, hogy például a virtuális hálózat neve melletti zárójelben a "klasszikus" szót. A lista van rendezve, hogy a Resource Manager virtuális hálózatok állnak a lista elején. Az alábbi képen láthatja, hogy csak egy virtuális hálózatot is kijelölhető. Több oka, hogy egy virtuális hálózatok szürkén jelennek meg, beleértve a:

* a virtuális hálózaton van egy másik előfizetésben, amely a fiók hozzáféréssel rendelkezik.
* a virtuális hálózat nem rendelkezik pont-hely engedélyezve
* a virtuális hálózat nem rendelkezik egy dinamikus útválasztású átjárót.

![][2]

Integráció engedélyezéséhez kattintson a VNet-integráció kívánt. Miután kiválasztotta a virtuális hálózat, az alkalmazás automatikusan újraindul a módosítások érvénybe léptetéséhez. 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Enable pont – hely egy klasszikus virtuális hálózaton

Ha a virtuális hálózat nem rendelkezik átjáróval, és nem rendelkezik a pont – hely, majd meg kell beállítania, amely első. Egy klasszikus virtuális hálózatot az átjáró konfigurálásához lépjen a portálra, és virtuális Networks(classic) listájának megjelenítéséhez. Válassza ki a hálózatot, amelyet integrálni szeretne. Válassza ki a VPN-kapcsolatok. Itt a VPN és a is, hogy azt az átjáró létrehozása pontot hozhat létre. Az átjáró merülnek fel nagyjából 30 percet vesz igénybe.

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Pont – hely a Resource Manager VNet engedélyezése
Konfigurálása egy Resource Manager virtuális hálózati átjáró és pont – hely, használhatja PowerShell-lel dokumentált Itt [PowerShell-lel virtuális hálózathoz pont – hely kapcsolat konfigurálása] [ V2VNETP2S] vagy használja az Azure Portalon leírtak szerint itt [az Azure portal virtuális hálózat pont – hely kapcsolat konfigurálása][V2VNETPortal]. Ez a funkció végrehajtásához a felhasználói felület még nem érhető el. A pont – hely konfiguráció tanúsítványok létrehozása nincs szükségünk. Tanúsítványok automatikusan létrejönnek, amikor a WebApp csatlakozik a virtuális hálózathoz, a portál használatával. 

### <a name="creating-a-pre-configured-vnet"></a>Előre konfigurált virtuális hálózat létrehozása
Ha szeretne létrehozni egy új virtuális hálózat, az átjáró konfigurálva van, és a pont – hely, az App Service hálózatkezelési felhasználói felület a képességet, ehhez azonban csak a Resource Manager virtuális hálózat rendelkezik. Ha szeretne egy átjárót, és a pont – hely egy klasszikus virtuális hálózat létrehozása, majd szüksége ehhez manuálisan a hálózat felhasználói felületén keresztül. 

A virtuális hálózatok közötti integráció felhasználói felületen Resource Manager virtuális hálózat létrehozásához válassza **hozzon létre új virtuális hálózat** , és adja meg a:

* Virtuális hálózat neve
* Virtuális hálózat címterülete
* Alhálózat neve
* Alhálózati címterület
* Átjáró címterülete
* Pont-hely típusú címterület

Ha azt szeretné, hogy a virtuális hálózat bármely más hálózatokhoz csatlakoztatásához, majd ne válassza háttérszínnek IP-címtér, amely átfedésben van ezekhez a hálózatokhoz. 

> [!NOTE]
> Az átjáró Resource Managerbeli virtuális hálózat létrehozása nagyjából 30 percet vesz igénybe, és jelenleg a virtuális hálózat és a nem integrálható az alkalmazás. A virtuális hálózati átjáró létrehozása után kell térjen vissza az alkalmazás virtuális hálózatok közötti integráció felhasználói felületén, és válassza ki az új Vnetet.
> 
> 

![][3]

Azure virtuális hálózatok általában hoz létre a rendszer magánhálózati címek. A VNet-integráció alapértelmezés szerint a szolgáltatás bármely ezen IP-címtartományok esetében be a virtuális hálózat felé irányuló forgalmat irányítja. A magánhálózati IP-címtartományok a következők:

* – Ez megegyezik a 10.0.0.0 - 10.0.0.0/8 10.255.255.255
* – Ez megegyezik a 172.16.0.0 - 172.16.0.0/12 172.31.255.255 
* – Ez megegyezik a 192.168.0.0 - 192.168.0.0/16 192.168.255.255

A virtuális hálózat címtere kell megadni a CIDR-jelölésrendszerben. Ha ismeri a CIDR-jelölés, módszert a címblokkok, IP-cím és a hálózati maszk jelölő egész szám megadásával. Vegye figyelembe, hogy 10.1.0.0/24 lenne 256 címet, és 10.1.0.0/25 128 címet lenne egy rövid összefoglalása. Egy IPv4-cím egy tulajdonságot/32 lenne csak 1 cím. 

Ha itt a DNS-kiszolgáló adatait, majd, hogy van beállítva a virtuális hálózathoz. Virtuális hálózat létrehozása után szerkesztheti ezt az információt a virtuális hálózatok közötti felhasználói felületéről. Ha módosítja a virtuális hálózat DNS-ben, majd szüksége a hálózat szinkronizálása műveletet.

Amikor létrehoz egy klasszikus virtuális hálózat a virtuális hálózatok közötti integráció felhasználói felületén, azt hoz létre virtuális hálózat ugyanabban az erőforráscsoportban az alkalmazás. 

## <a name="how-the-system-works"></a>A rendszer működése
Valójában ez a funkció az alkalmazás csatlakoztatása a virtuális hálózathoz pont – hely VPN technológiára épül. Az Azure App Service Apps rendelkezik egy több-bérlős rendszer-architektúra, amely kizárja az alkalmazás közvetlenül a virtuális hálózat kiépítése, ahogyan az a virtual machines. Pont – hely technológia építve azt csak a virtuális gép az alkalmazást üzemeltető való hálózati hozzáférés korlátozásához. Úgy, hogy az alkalmazások csakis azokhoz a hálózatokat, amelyeket konfigurálnia, hogy hozzáférjenek a hálózathoz hozzáférést tovább korlátozza az alkalmazás-gazdagépeken. 

![][4]

Ha a virtuális hálózat DNS-kiszolgáló még nem konfigurálta, az alkalmazás IP-címek a virtuális hálózat erőforrás eléréséhez használni kell. IP-címek használata esetén ne feledje, hogy ez a funkció fő előnye, hogy lehetővé teszi a magánhálózaton belül a magánhálózati címek használatát. Ha az alkalmazás nyilvános IP-címek a virtuális gépek egyik ezután nem használja a VNet-integráció szolgáltatást, és az interneten keresztül történik a kommunikáció használatára.

## <a name="managing-the-vnet-integrations"></a>A virtuális hálózathoz való integrációt kezelése
Az alkalmazás szintjén van arra, hogy csatlakoztatása és leválasztása a virtuális hálózathoz. A VNet-integráció egyszerre több alkalmazással érintő műveletekre ASP szintjén. A felhasználói felületen, az alkalmazás szintjén látható részletekért a virtuális hálózaton. A legtöbb ugyanazokat az információkat az ASP szintjén is látható. 

![][5]

A hálózati funkció állapota lapon láthatja, ha az alkalmazás csatlakoztatva van-e a virtuális hálózathoz. Ha a virtuális hálózat átjárójának valamilyen okból nem működik, majd ez jelenik meg, nincs csatlakoztatva. 

Most már elérhető az alkalmazás szintű virtuális hálózatok közötti integráció felhasználói felületén pedig ugyanaz, mint a részletes információkat kap az ASP információk. Azok az elemek a következők:

* Virtuális hálózat neve – Ez a hivatkozás megnyílik az Azure virtuális hálózat felhasználói felület
* Hely – a virtuális hálózat helyét tükrözi. Egy másik helyen található virtuális hálózat integrálása lehetőség.
* Tanúsítvány állapota – nincsenek tanúsítványok védelmét a VPN-kapcsolatot az alkalmazás és a virtuális hálózat között. Ez jeleníti meg egy tesztet annak érdekében, hogy szinkronban.
* Átjáró állapota – az átjárók kell le valamilyen okból majd az alkalmazás nem fér hozzá a virtuális hálózatban található erőforrások. 
* Virtuális hálózati címtér – Ez az IP-címterét a virtuális hálózat számára. 
* Pont – hely címtér – Ez az a pont, a webhely IP-címtartomány a virtuális hálózat számára. Az alkalmazás látható, a címtér az IP-címekről érkező kommunikációt. 
* Címtér hely - hely helyek közötti VPN-ek használatával a helyszíni erőforrásokhoz vagy más virtuális hálózatok közötti kapcsolatot a virtuális hálózat. Kell majd, hogy a VPN-kapcsolat itt jeleníti meg az IP-címtartományokkal definiált konfigurált.
* DNS-kiszolgálók – Ha a virtuális hálózattal konfigurált DNS-kiszolgálók és az itt felsorolt.
* A VNet - van irányítva IP-címek listáját, amely a virtuális hálózat rendelkezik definiált útválasztás IP-címek, és itt ezeket a címeket show. 

Az egyetlen művelet elvégezhető az alkalmazás a VNet-integráció nézetében, hogy az alkalmazás leválasztása a virtuális hálózat jelenleg csatlakoztatva van. Az alkalmazás leválasztása a virtuális hálózaton, válassza a felső le. Ez a művelet nem változtatja meg a virtuális hálózathoz. A virtuális hálózat és annak konfigurációját, beleértve az átjárók változatlan marad. Ha meg kívánja törölni a virtuális hálózathoz, először törölje az erőforrásokat, beleértve az átjárók szeretné. 

Az App Service-csomag nézet rendelkezik egy további műveletek száma. Azt is érhető el eltérően, mint az alkalmazásból. Elérte az ASP hálózatkezelés felhasználói felületén, nyissa meg az ASP felhasználói Felületet, és görgessen le. Válassza ki a "Hálózati funkció állapota" a hálózati szolgáltatás állapota felhasználói felületének megnyitásához. Válassza ki a "Ide kattintva kezelheti" a virtuális hálózathoz való integrációt az ASP a listához.

![][6]

Az ASP helye, ne felejtse el, ha megnézi a virtuális hálózatok integrálása, helyeinek jó. Ha a virtuális hálózat egy másik helyen, sokkal valószínűbb, hogy késési problémák. 

A virtuális hálózat integrálva egy emlékeztető hány virtuális hálózatok az alkalmazások integrálva vannak az ASP és hány rendelkezhet. 

Az egyes virtuális hálózatok hozzáadott részleteinek megtekintéséhez kattintson a az Önt érdeklő VNet. A korábban feljegyzett adatok mellett is láthatja a jelenlegi ASP, hogy a virtuális hálózat által használt alkalmazások listáját. 

Műveletek vonatkozóan nincsenek két elsődleges műveleteket. Az első az lehetővé teszi, hogy a virtuális hálózat, az alkalmazást elhagyó forgalomra hangból útvonalak. A második műveletet a rendszer azon képessége, tanúsítványok és hálózati adatok szinkronizálása.

![][7]

**Útválasztás** , a korábban feljegyzett, a virtuális hálózathoz definiált útvonalak irányítja a forgalmat a virtuális hálózat az alkalmazásból, mire használható. Van néhány felhasználása ellenére, hogy hol ügyfelek szeretne küldeni további kimenő forgalmat az alkalmazásokból a vnetben, és azok ezt a funkciót biztosítja. Mi történik a forgalmat a követően, hogy hogyan konfigurálja az ügyfél a Vnetben legfeljebb. 

**Tanúsítványok** a tanúsítvány állapotát tükrözi által végzett az App Service ellenőrzése, hogy továbbra is jó-e a tanúsítványok az általunk a VPN-kapcsolat ellenőrzése. Ha engedélyezve van a VNet-integráció, majd ez az első integrációt arra adott virtuális hálózatban ez ASP, az alkalmazások van-e a szükséges exchange-tanúsítványok biztosítása a kapcsolat biztonságát. A tanúsítványok együtt lekérjük a DNS-konfigurációt, útvonalak és más hasonló dolgot, amelyek ismertetik a hálózaton.
Ha ezeket a tanúsítványokat vagy hálózati adatok megváltozik, akkor szüksége "Hálózat szinkronizálása" gombra. **Megjegyzés:**: "Hálózat szinkronizálása" gombra, majd a rövid kimaradásokat okozhat az alkalmazás és a virtuális hálózat közötti kapcsolat. Amíg nem indítja újra az alkalmazást, a kapcsolat megszakadása okozhat a hely nem működhet megfelelően. 

## <a name="accessing-on-premises-resources"></a>Hozzáférés a helyszíni erőforrások
A VNet-integráció a funkció előnyeit egyik, ha a virtuális hálózat csatlakozik a helyi hálózatot webhelyek közötti VPN-nel az alkalmazások férhet hozzá a helyszíni erőforrások az alkalmazásból. A funkció működéséhez azonban szükség lehet az útvonalakat a helyszíni VPN-átjáró frissítse az a pont – hely IP-tartományban. Ha először be, a helyek közötti VPN konfigurálásához használt parancsfájlokra beállítása kell többek között a pont – hely VPN útvonalakat. Ha a pont – hely VPN a helyek közötti VPN létrehozása után adja hozzá, majd frissítenie az útvonalak manuálisan. Megtudhatja, hogyan valósítható meg / átjáró eltérőek lehetnek, és nem ebben a témakörben találhatók. 

> [!NOTE]
> A VNet-integráció funkciót és a egy ExpressRoute-átjáróval rendelkező virtuális hálózat nem integrálható egy alkalmazást. Akkor is, ha az ExpressRoute-átjárót konfigurált [együttes használata mód] [ VPNERCoex] a VNet-integráció nem működik. Van szükség a erőforrások eléréséhez ExpressRoute-kapcsolaton keresztül, akkor használhat egy [App Service Environment-környezet][ASE], amely fut a virtuális hálózatban található.
> 
> 

## <a name="pricing-details"></a>Díjszabás részletei
Van néhány díjszabás kell ügyelnie, ha a VNet-integráció szolgáltatással részleteiről. Nincsenek 3 kapcsolódó költségek, ez a funkció használatához:

* ASP-réteg követelményeinek díjszabása
* Adatátviteli költségek
* VPN-átjáró költségek.

Az alkalmazások használhatják ezt a szolgáltatást, kell lennie a Standard vagy prémium szintű App Service-csomag. A költségekre Itt láthatja a további részletek: [App Service díjszabását][ASPricing]. 

Pont – hely VPN-eket kezelésének módját, mert mindig rendelkezik kimenő díját a VNet-integráció-kapcsolaton keresztül akkor is, ha a virtuális hálózat ugyanabban az adatközpontban. Ezeket a díjakat vannak megtekintéséhez figyelje itt: [Data Transfer Díjszabásának részletei][DataPricing]. 

Az utolsó elem a virtuális hálózati átjárók költségét. Ha már nincs szüksége az átjárók más, mint például a helyek közötti VPN-eket, majd licencdíjat kell fizetni a VNet-integráció szolgáltatást támogató átjárók esetében. Részletek vannak-e költségek itt: [VPN Gateway díjszabás][VNETPricing]. 

## <a name="troubleshooting"></a>Hibaelhárítás
Amíg a funkció segítségével egyszerűen állíthat, ez nem jelenti azt, hogy a felhasználói élmény lesz-e a probléma ingyenes. Kell észlel a kívánt végpontra elérésével kapcsolatos problémák bizonyos segédprogramokat használhatja az app-konzol közötti kapcsolat teszteléséhez. Nincsenek két konzol környezeteket használhat. A Kudu konzol az egyik, a másik pedig a konzolt, amelyet az Azure Portalon. Ugrás a eszközök lekérheti a Kudu konzol az alkalmazás -> Kudu. Ez megegyezik a helynév: [%] lépjen. scm.azurewebsites.net. Miután, amely megnyílik, nyissa meg a hibakeresési konzolt lapot. Eszközök válassza az Azure-portál központi konzolon, majd az alkalmazásából való beolvasásához konzol ->. 

#### <a name="tools"></a>Eszközök
Az eszközök **ping**, **nslookup** és **tracert** biztonsági okokból a konzolon keresztül nem fog működni. Töltse ki a void ott lett hozzáadva két különálló eszközökkel. DNS-funkciók teszteléséhez hozzáadtunk egy nameresolver.exe nevű eszközt. A szintaxis a következő:

    nameresolver.exe hostname [optional: DNS Server]

Használhat **nameresolver** ellenőrizze a gazdagép neve, amely az alkalmazása függ. Ezzel a módszerrel tesztelheti Ha semmit a DNS rosszul konfigurálva van, vagy esetleg nem fér hozzá a DNS-kiszolgáló.

A következő eszköz lehetővé teszi, hogy az állomás és port kombinációjához TCP-kapcsolat tesztelése. Ez az eszköz neve **tcpping** és szintaxisa:

    tcpping.exe hostname [optional: port]

A **tcpping** segédprogram jelzi, hogy egy adott állomás és port elérheti. Csak megmutatja sikeres ha: egy alkalmazás figyeli, az állomás és port kombinációját, és a megadott állomás és port az alkalmazásból a hálózati hozzáférést.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Hibakeresés a virtuális hálózathoz való hozzáférés üzemeltetett erőforrásokhoz
Létezik néhány dolog, amely megakadályozhatja, hogy az alkalmazás egy adott állomás és port elérését. A legtöbbször egyike három dolgot:

* **Van egy tűzfal ugyanúgy** módon van egy tűzfal, ha a TCP időtúllépési eléri-e. Ebben az esetben ez 21 másodperc. Használja a **tcpping** eszköz kapcsolat teszteléséhez. TCP-időtúllépések miatt tűzfalak túl sok-sok dolog lehet, de kezdeni. 
* **Nem érhető el DNS** a DNS-időtúllépési érték egy DNS kiszolgálón három másodperc. Ha két DNS-kiszolgálók, az időtúllépési érték 6 másodperc. Nameresolver segítségével megtekintheti, hogy működik-e a DNS. Ne felejtse el, nem használhatja az nslookup, amely nem használja a DNS-ben a virtuális hálózat van beállítva.
* **Érvénytelen P2S IP-címtartomány** helyet iprange a pontot kell lennie az RFC 1918 magánhálózati IP-címtartományokkal (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Ha a tartományon kívül esik, amely IP-címek használ, dolgot nem fog működni. 

Ha azok az elemek nem választ a problémára, keresse meg az egyszerű többek között az első: 

* Az átjárót a portálon fel állapottal jelenik meg?
* Ehhez tanúsítványokat megjelenítése, hogy szinkronban?
* Bárki változtatta meg a hálózati konfigurációt az érintett ASP "Sync hálózat" művelet végrehajtása nélkül? 

Ha az átjáró nem működik, majd hálózatra, készítsen biztonsági mentést. Ha a tanúsítványok nincsenek szinkronban, az ASP nézetében, a VNet-integráció, és nyomja le a "Hálózat szinkronizálása". Ha azt gyanítja, hogy az a virtuális hálózat konfigurációja változás történt, és ez nem szinkronizálás esetén az ASP majd nyissa meg a VNet-integráció ASP nézetét, és kattintson a "Hálózat szinkronizálása" közvetlenül emlékeztetőként, ez a rövid kimaradásokat okoz a virtuális hálózatok közötti kapcsolat és az alkalmazások. 

Ha, hogy minden rendben, akkor szüksége megismerésére egy kicsit részletesebben:

* Vannak-e bármilyen más alkalmazást, VNet-integráció segítségével az ugyanazon virtuális hálózatban található erőforrások eléréséhez? 
* Látogasson el az alkalmazás-konzolon és bármilyen egyéb erőforrásokat a virtuális hálózat eléréséhez tcpping használni? 

Ha a fentiek bármelyike igaz, majd a VNet-integráció nem okoz gondot, és a probléma valahol máshol. Ez az, ahol kap további kihívást lennie, mert nem egyszerű lehet, hogy miért nem tudja elérni a gazdagépet: port. Okai a következők:

* megakadályozza a hozzáférést az alkalmazásport a webhely IP-címtartomány a pont a gazdagép van a tűzfal. Nyilvános hozzáférés határainak átlépése alhálózatok gyakran van szükség.
* a cél gazdagép nem működik
* az alkalmazás nem működik
* a megfelelő IP- vagy állomásnév kellett
* az alkalmazás a várttól mint egy másik porton figyel. Ellenőrizheti ezt is, amelyeken az alakzatot, és használja a "netstat - aon" a cmd parancsot a parancssorba. Ez bemutatja, milyen folyamat azonosítója melyik portot figyeli. 
* a hálózati biztonsági csoportok vannak konfigurálva, hogy azok való hozzáférés letiltása az alkalmazás-állomás és port a helyet iprange pont oly módon

Ne feledje, hogy milyen IP nem ismeri a pont – hely IP-címtartományban, amely az alkalmazás fogja használni, így engedélyezze a hozzáférést a teljes tartomány szükséges. 

További hibakeresési lépések az alábbiak:

* csatlakozhat a virtuális hálózatban található virtuális Géphez, és próbálja meg elérni az erőforrást állomás: port onnan. TCP teszteléséhez hozzáférés használata a PowerShell-paranccsal **test-netconnection**. A szintaxis a következő:

      test-netconnection hostname [optional: -Port]

* viszi, megjelenik egy alkalmazást, egy virtuális gép és a teszt hozzáférést az adott állomáson és porton az alkalmazásból a konzolról

#### <a name="on-premises-resources"></a>A helyszíni erőforrásokhoz ####

Ha az alkalmazás nem érhető el egy erőforrást a helyszíni, majd ellenőrizze, ha az erőforrás a elérheti a virtuális hálózat. Használja a **test-netconnection** PowerShell paranccsal végezheti el. A virtuális gép nem tudja elérni a helyszíni erőforráshoz, majd győződjön meg arról, hogy a helyek közötti VPN-kapcsolat működik. Ha működik, majd ellenőrizze a ugyanazokat a műveleteket, valamint a helyszíni átjáró konfigurációjának és állapotának korábban feljegyzett. 

Ha a virtuális hálózat VM elérheti a helyszíni rendszerbe, de az alkalmazás nem tudja majd a hiba oka valószínűleg egy, a következő:

* az útvonalak nincsenek konfigurálva a ponttal hely IP-címtartományokhoz a helyszíni átjáróban
* a hálózati biztonsági csoportok forgalomszűrők blokkolják a hozzáférést a pont – hely IP-címtartomány
* a helyszíni tűzfal forgalomszűrők blokkolják a pont – hely IP-címtartomány érkező forgalom
* a vnetben, amely megakadályozza, hogy a pont – hely alapú forgalom elérhesse a helyszíni hálózatot rendelkezik egy meghatározott felhasználó alapján

## <a name="powershell-automation"></a>PowerShell automation

Az App Service integrálható az Azure Virtual Network PowerShell-lel. Az alkalmazást kész parancsprogramok esetén lásd: [az Azure App Service alkalmazás csatlakoztatása az Azure Virtual Networkhöz](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>A hibrid kapcsolatok és az App Service Environment-környezetek
Nincsenek három funkciók, amelyek lehetővé teszik a virtuális hálózatok közötti üzemeltetett erőforrásokhoz való hozzáférést. Ezek a következők:

* VNet-integráció
* Hibrid kapcsolatok
* App Service-környezetek

Hibrid kapcsolatokhoz szükség van a hálózaton a hibrid kapcsolat Manager(HCM) nevű továbbító ügynök telepítését. A HCM képesnek kell lennie az Azure-bA és az alkalmazás csatlakoztatása. Ez a megoldás különösen nagyszerű, például a helyszíni hálózathoz egy távoli hálózatról, vagy akár egy másik felhőben üzemeltetett hálózati, mert az internetről elérhető végpont nem igényel. A HCM csak akkor fut, a Windows, illetve azt, hogy legfeljebb öt olyan példányai, magas rendelkezésre állást biztosít. Hibrid kapcsolatok csak az, ha TCP támogatja, és minden hibrid kapcsolat végpontja meg kell egyeznie a megadott gazdagép: port kombinációjához. 

Az App Service Environment funkció lehetővé teszi, hogy futtatását az Azure App Service egy példányát a virtuális hálózatban található. Ez lehetővé teszi az erőforrások az alkalmazások eléréséhez további lépések nélkül a virtuális hálózatban található. App Service Environment egyéb előnyei, a Dv2-alapú feldolgozók használható az legfeljebb 14 GB RAM. Egy másik előnye, hogy a rendszer igény szerint méretezheti. Ellentétben a több-bérlős környezetekben, ahol az ASP legfeljebb 20 példány az ASE környezetben méretezhető legfeljebb 100 ASP-példányhoz. Az ASE átalányalapú ezt a Virtuálishálózat-integráció nem kap dolog, hogy az App Service Environment-ExpressRoute VPN is dolgozhat. 

Bár egyes használnak megkülönbözteti a kis között átfedés van, ezek közül egyik sem cserélje le a többi. Hogy mely szolgáltatásokat szeretné az igényeinek megfelelően vannak kötve. Példa:

* Ha a fejlesztők, és szeretné futtatni a webhelyen az Azure-ban, és azok az íróasztal mellett a munkaállomáson-adatbázisának eléréséhez, a legegyszerűbb dolog használandó hibrid kapcsolatok. 
* Ha Ön egy nagy szervezet, amely nagy mennyiségű fel szeretné webes tulajdonságok a nyilvános felhő, és kezelheti őket a saját hálózati, majd nyissa meg az App Service environmenttel szeretné. 
* Ha több, a virtuális hálózatban található erőforrások elérését igénylő alkalmazások, VNet-integráció, a mód. 

Ha a virtuális hálózat már csatlakoztatva van a helyszíni hálózathoz, majd a VNet-integráció vagy az App Service-környezet használata egyszerű módja a helyszíni erőforrások. A virtuális hálózat nem kapcsolódik a helyszíni hálózathoz, majd, hogy a virtuális hálózat és a telepítés a HCM összehasonlítása a helyek közötti VPN beállításához a sokkal több terhelést jelenthet. 

A működési különbségek túl van vannak is díjszabásbeli különbségeket. Az App Service Environment funkció a prémium szolgáltatásajánlat azonban kínálja a legtöbb hálózati konfigurációs lehetőségekkel más nagyszerű funkciók mellett. VNet-integráció a Standard vagy prémium szintű ASP használható, és tökéletes megoldás, a több-bérlős App Service-ben a virtuális hálózatban található erőforrások biztonságosan felhasználásához. Hibrid kapcsolatok jelenleg a fiók, amely ingyenes, és kérje le fokozatosan drágább szintek alapján van szüksége a BizTalk függ. Esetén, azonban több több hálózaton működik, nincs más szolgáltatás például a hibrid kapcsolataira, melyek lehetővé teszik a jól több mint 100, különálló hálózatokból erőforrások eléréséhez. 

## <a name="new-vnet-integration"></a>Új VNet-integráció ##

Van egy új verziója, a VNet-integráció funkció, amely nem függ a pont – hely VPN-technológia. Ezen új verziója előzetes verzióban érhető el. Az új VNet-integráció funkciót a következő jellemzőkkel rendelkezik.

- Az új funkció a Resource Manager virtuális hálózatban található egy nem használt alhálózaton van szükség.
- Az App Service-csomag példányonként egy címet használja. Alhálózat méretét a hozzárendelés után nem módosítható, mivel a is több, mint a maximális skálázhatósága alhálózatot használjon. 32-címekkel rendelkező/27-es nem az ajánlott mérete, mert a 20-példányok méretezett App Service-csomagot, amely helyet biztosít.
- Védett erőforrásokhoz az új VNet-integráció funkciót a szolgáltatásvégpont használhatja fel. Az alhálózat konfigurálása Szolgáltatásvégpontok az alkalmazását, az alkalmazáshoz rendelt való hozzáférés engedélyezése
- Erőforrások eléréséhez több ExpressRoute-kapcsolatok további konfiguráció nélkül
- Nincs átjárót kell használnia az új virtuális hálózat integrációja
- Az App Service-csomag a Standard, prémium szintű vagy PremiumV2 csomagot kell lennie.
- Az új funkciót csak a újabb Azure App Service-skálázási egység érhető el. A portál jelzi, ha az alkalmazás az új VNet-integráció szolgáltatással. 
- Az alkalmazás és a virtuális hálózat ugyanabban a régióban kell lennie

Az új VNet-integráció szolgáltatás érhető el kezdetben csak az Észak-Európa és kelet-USA régióban.


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
