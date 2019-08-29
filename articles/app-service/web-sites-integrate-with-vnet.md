---
title: Alkalmazás integrálása az Azure Virtual Network-Azure App Service
description: Bemutatja, hogyan csatlakoztatható egy alkalmazás Azure App Service egy új vagy meglévő Azure-beli virtuális hálózathoz
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/21/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a96c02d1d7d2fae43e0a5915e9233bde842ce621
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066673"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Az alkalmazás integrálása Azure-Virtual Network
Ez a dokumentum ismerteti a Azure App Service Virtual Network Integration funkciót, valamint azt, hogyan állíthatja be az [](https://go.microsoft.com/fwlink/?LinkId=529714)alkalmazásokkal a Azure app Serviceban. [Azure-beli virtuális hálózatok][VNETOverview] A (virtuális hálózatok) lehetővé teszi, hogy számos Azure-erőforrást egy nem internetes útválasztású hálózaton helyezzen el.  

A Azure App Service két változattal rendelkezik. 

1. A több-bérlős rendszerek, amelyek támogatják az elkülönített árképzési csomagok teljes körét
2. A VNet üzembe helyező és az elkülönített díjszabási csomagot támogató App Service Environment

Ez a dokumentum a több-bérlős App Service való használatra szolgáló két VNet integrációs funkciót mutat be. Ha az alkalmazás App Service Environmentban [][ASEintro]van, akkor már egy VNet van, és nem igényli a VNet integrációs funkciójának használatát az erőforrások eléréséhez ugyanabban a VNet. Az összes App Service hálózati szolgáltatással kapcsolatos részletekért olvassa el a [app Service hálózatkezelési funkcióit](networking-features.md) .

A VNet-integrációs szolgáltatásnak két formája van

1. Az egyik verzió lehetővé teszi, hogy a virtuális hálózatok-integráció ugyanabban a régióban legyen. A szolgáltatás ezen formája egy alhálózatot igényel az azonos régióban található VNet. Ez a szolgáltatás még előzetes verzióban érhető el, de a Windows-alkalmazások éles számítási feladataihoz az alábbiakban ismertetett kikötések használhatók.
2. A másik verzió lehetővé teszi a virtuális hálózatok való integrációt más régiókban vagy klasszikus virtuális hálózatok. A szolgáltatás ezen verziója megköveteli a Virtual Network átjáró üzembe helyezését a VNet. Ez a pont – hely VPN-alapú szolgáltatás, és csak a Windows-alkalmazások esetében támogatott.

Egy alkalmazás egyszerre csak a VNet-integrációs szolgáltatás egyik formáját tudja használni. A kérdés az, hogy melyik szolgáltatást használja. Számos dolgot használhat. A egyértelmű differenciálók azonban a következők:

| Probléma  | Megoldás | 
|----------|----------|
| Egy adott régióban szeretné elérni az RFC 1918-es címeket (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16). | regionális VNet-integráció |
| Szeretné elérni az erőforrásokat egy másik régióban található klasszikus VNet vagy VNet | átjáró szükséges VNet-integráció |
| Az RFC 1918-végpontok elérését szeretné elérni a ExpressRoute között | regionális VNet-integráció |
| Erőforrásokat szeretne elérni a szolgáltatási végpontok között | regionális VNet-integráció |

Egyik funkció sem teszi lehetővé a nem RFC 1918-címek elérését a ExpressRoute között. Ehhez a-t kell használnia.

A regionális VNet-integráció használata nem köti össze a VNet a helyszíni vagy a szolgáltatási végpontok konfigurálásával. Ez külön hálózati konfiguráció. A regionális VNet-integráció egyszerűen lehetővé teszi, hogy az alkalmazás hívásokat hajtson végre a kapcsolati típusok között.

A használt verziótól függetlenül a VNet integrációja lehetővé teszi, hogy a webalkalmazás hozzáférhessen a virtuális hálózat erőforrásaihoz, de a virtuális hálózatról nem biztosít bejövő privát hozzáférést a webalkalmazáshoz. A privát helyhez való hozzáférés arra utal, hogy az alkalmazás csak a magánhálózaton keresztül érhető el, például egy Azure-beli virtuális hálózaton belülről. A VNet-integráció csak az alkalmazásból kimenő hívásokat tesz a VNet. 

A VNet integrációs funkciója:

* Standard, prémium vagy PremiumV2 díjszabási csomagot igényel 
* a TCP és az UDP támogatása
* együttműködik App Service alkalmazásokkal és a Function apps szolgáltatással

Néhány dolog, amit a VNet-integráció nem támogat, beleértve a következőket:

* meghajtó csatlakoztatása
* AD-integráció 
* NetBios

## <a name="regional-vnet-integration"></a>Regionális VNet-integráció 

Ha a VNet-integráció a virtuális hálózatok-ben van használatban az alkalmazással megegyező régióban, akkor legalább 32-as címmel rendelkező delegált alhálózat használatát igényli. Az alhálózat nem használható semmi máshoz. Az alkalmazásból érkező kimenő hívásokat a rendszer a delegált alhálózat címei alapján hozza létre. Ha a VNet-integráció ezen verzióját használja, a hívások a VNet címeiből jönnek létre. A VNet lévő címek használata lehetővé teszi az alkalmazás számára a következőket:

* A szolgáltatás végpont által védett szolgáltatásainak hívása
* Erőforrások elérése ExpressRoute-kapcsolatok között
* Hozzáférés az erőforrásokhoz a VNet, amelyhez csatlakozik
* Erőforrások elérése egymással összetartozó kapcsolatok között, beleértve az ExpressRoute-kapcsolatokat is

Ez a funkció előzetes verzióban érhető el, de a Windows-alkalmazások éles számítási feladatait a következő korlátozásokkal támogatja:

* Csak olyan címeket érhet el, amelyek az RFC 1918 tartományban találhatók. Ezek a 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 címterület címei.
* A globális társ-összekapcsolási kapcsolatok erőforrásai nem érhetők el
* Az alkalmazásból érkező forgalomhoz tartozó útvonalak nem állíthatók be a VNet
* A szolgáltatás csak olyan újabb App Service skálázási egységekből érhető el, amelyek támogatják a PremiumV2 App Service csomagokat.
* Az integrációs alhálózatot csak egy App Service-csomag használhatja
* A szolgáltatás nem használható a App Service Environmentban található elkülönített csomag-alkalmazásokban
* A szolgáltatáshoz egy nem használt alhálózat szükséges, amely egy/27 32-es címmel vagy nagyobb a Resource Manager-VNet
* Az alkalmazásnak és a VNet ugyanabban a régióban kell lennie
* Integrált alkalmazással nem törölhet VNet. Először el kell távolítania az integrációt 
* App Service-csomag esetében csak egy regionális VNet-integráció lehet. Ugyanazon a App Service csomagon belül több alkalmazás is használhatja ugyanazt a VNet. 
* Egy alkalmazás vagy egy App Service csomag előfizetése nem módosítható, amíg van olyan alkalmazás, amely regionális VNet-integrációt használ

Az egyes App Service-példányok esetében egy-egy-egy-egy-egy-egy Ha 5 példányra méretezi az alkalmazást, akkor a rendszer 5 címet használ. Mivel az alhálózat mérete nem módosítható a hozzárendelés után, olyan alhálózatot kell használnia, amely elég nagy ahhoz, hogy megfeleljen az alkalmazásnak. Az ajánlott méret a/26, 64 címmel. A/27 32-es címmel a prémium szintű App Service 20 példányban, ha nem módosította a App Service terv méretét. Ha felfelé vagy lefelé méretezi a App Service tervet, a rövid ideig kétszer annyi címet kell használnia. 

Ha azt szeretné, hogy alkalmazásai egy másik App Service tervben olyan VNet érjenek el, amely már egy másik App Service-csomagban található alkalmazáshoz csatlakozik, akkor a korábban létező VNet-integráció által használttól eltérő alhálózatot kell választania.  

A szolgáltatás előzetes verzióban is elérhető Linux rendszeren. A VNet-integrációs szolgáltatás használata Resource Manager-VNet ugyanabban a régióban:

1. Nyissa meg a hálózat KEZELŐFELÜLETét a portálon. Ha az alkalmazás képes az új funkció használatára, megjelenik egy VNet (előzetes verzió) hozzáadására szolgáló lehetőség.  

   ![VNet-integráció kiválasztása][6]

1. Válassza a **VNet hozzáadása (előzetes verzió)** lehetőséget.  

1. Válassza ki azt a Resource Manager-VNet, amelyet integrálni szeretne, majd hozzon létre egy új alhálózatot, vagy válasszon üres, már meglévő alhálózatot. Az integráció egy percnél kevesebb időt vesz igénybe. Az integráció során az alkalmazás újraindul.  Az integráció befejezését követően megtekintheti a beépített VNet részleteit, és egy szalagcímet a tetején, amely a funkció előzetes verziójának megadását mutatja be.

   ![Válassza ki a VNet és az alhálózatot][7]

Miután az alkalmazása integrálva van a VNet, ugyanazt a DNS-kiszolgálót fogja használni, amelyhez a VNet konfigurálva van. 

A regionális VNet-integrációhoz az integrációs alhálózat delegálása szükséges a Microsoft. Web számára.  A VNet-integráció felhasználói felülete automatikusan delegálja az alhálózatot a Microsoft. Web számára. Ha a fiókja nem rendelkezik megfelelő hálózati engedélyekkel a beállításához, szüksége lesz egy olyan személyre, aki az integrációs alhálózaton attribútumokat állíthat be az alhálózat delegálásához. Az integrációs alhálózat manuális delegálásához nyissa meg az Azure Virtual Network alhálózat felhasználói felületét, és állítsa be a Microsoft. Web delegálását.

Ha le szeretné bontani az alkalmazást a VNet,válassza a leválasztás lehetőséget. Ezzel újraindítja a webalkalmazást. 


#### <a name="web-app-for-containers"></a>Web App for Containers

Ha a beépített rendszerképeket használó Linuxon App Service használ, a regionális VNet-integrációs funkció további változtatások nélkül működik. Ha Web App for Containers használ, módosítania kell a Docker-rendszerképet, hogy használni lehessen a VNet-integrációt. A Docker-rendszerképben használja a PORT környezeti változót a fő webkiszolgáló figyelési portjának hardcoded helyett. A PORT környezeti változót automatikusan App Service platform állítja be a tároló indítási ideje szerint.

### <a name="service-endpoints"></a>Service Endpoints – szolgáltatásvégpont

Az új VNet-integrációs funkció lehetővé teszi a szolgáltatási végpontok használatát.  Ha szolgáltatási végpontokat szeretne használni az alkalmazással, az új VNet-integrációval csatlakozhat egy kiválasztott VNet, majd az integrációhoz használt alhálózaton konfigurálhatja a szolgáltatási végpontokat. 


### <a name="how-vnet-integration-works"></a>A VNet-integráció működése

A App Serviceban lévő alkalmazások a feldolgozói szerepkörökben vannak tárolva. Az alapszintű és a magasabb díjszabási csomagok olyan dedikált üzemeltetési csomagok, amelyeken nincs más, ugyanazon dolgozón futó ügyfél-munkaterhelés. A VNet integrációja a virtuális felületek a delegált alhálózaton lévő címekkel való csatlakoztatásával működik. Mivel a feladó címe a VNet található, a legtöbb dologhoz hozzáférhet a VNet, mint a VNet lévő virtuális gépekhez. A hálózatkezelés megvalósítása eltér a virtuális gép VNet való futtatástól, ezért a funkció használata során a hálózati szolgáltatások némelyike még nem érhető el.

![VNet-integráció](media/web-sites-integrate-with-vnet/vnet-integration.png)

Ha a VNet-integráció engedélyezve van, az alkalmazás továbbra is a normál módon megegyező csatornákon keresztül hajtja végre a kimenő hívásokat az interneten. Az alkalmazás tulajdonságai portálon felsorolt kimenő címek továbbra is az alkalmazás által használt címek. Az alkalmazás változásai, a Service Endpoint Secure Services vagy az RFC 1918-címek meghívása a VNet. 

A szolgáltatás csak egy virtuális felületet támogat a munkavégzők esetében.  A munkavégzők egy virtuális felülete egy regionális VNet integrációt jelent App Service csomagon keresztül. Az azonos App Service tervben szereplő összes alkalmazás használhatja ugyanazt a VNet-integrációt, de ha egy alkalmazásnak egy további VNet való csatlakozásra van szüksége, létre kell hoznia egy másik App Service tervet. A használt virtuális felület nem erőforrás, amelyhez az ügyfeleknek közvetlen hozzáférésük van.

A technológia működésének jellegéből adódóan a VNet-integrációhoz használt forgalom nem jelenik meg Network Watcher vagy NSG flow-naplókban.  

## <a name="gateway-required-vnet-integration"></a>Átjáró szükséges VNet-integráció 

Az átjáró szükséges VNet-integrációs funkciója:

* Bármely régióban virtuális hálózatok csatlakozhat a Resource Manager vagy a klasszikus virtuális hálózatok használatával
* Lehetővé teszi, hogy egy alkalmazás egyszerre csak 1 VNet kapcsolódjon
* Lehetővé teszi akár öt virtuális hálózatok integrálását egy App Service csomaggal 
* Lehetővé teszi, hogy ugyanazt a VNet egy App Service-csomagban több alkalmazás is felhasználja anélkül, hogy ez hatással lenne egy App Service-csomag által használható teljes számra.  Ha ugyanazokkal a VNet rendelkező 6 alkalmazásokkal rendelkezik ugyanazon a App Service csomagon, akkor 1 VNet számít. 
* Olyan Virtual Network átjárót igényel, amely pont – hely típusú VPN-kapcsolattal van konfigurálva
* A 99,9%-os SLA-t támogat az átjáró SLA-ja miatt

Ez a funkció nem támogatja a következőket:
* Használat Linux-alkalmazásokkal
* Erőforrásokhoz való hozzáférés ExpressRoute-on keresztül 
* Erőforrásokhoz való hozzáférés szolgáltatásvégpontokon keresztül 

### <a name="getting-started"></a>Első lépések

Az alábbiakban néhány dolgot figyelembe kell vennie, mielőtt csatlakoztatja a webalkalmazást egy virtuális hálózathoz:

* A célként megadott virtuális hálózatnak a pont-hely típusú VPN-nek engedélyezve kell lennie egy Route-alapú átjáróval ahhoz, hogy az alkalmazáshoz lehessen csatlakozni. 
* A VNet ugyanabban az előfizetésben kell lennie, mint a App Service-csomagnak (ASP).
* A VNet integrált alkalmazások az adott VNet megadott DNS-t használják.

### <a name="set-up-a-gateway-in-your-vnet"></a>Átjáró beállítása a VNet ###

Ha már van olyan átjáró, amely pont – hely címekkel van konfigurálva, ugorjon a VNet-integráció konfigurálása az alkalmazáshoz lehetőségre.  
Átjáró létrehozása:

1. [Hozzon létre egy átjáró][creategatewaysubnet] -alhálózatot a VNet.  

1. [Hozza létre a VPN-átjárót][creategateway]. Válasszon egy Route-alapú VPN-típust.

1. [Állítsa a pontot a hely címeire][setp2saddresses]. Ha az átjáró nem az alapszintű SKU-ban található, akkor a IKEV2 le kell tiltani a pont – hely konfigurációban, és az SSTP-t ki kell választani. A címterület a 1918-es RFC-címen, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Ha most hozza létre az átjárót App Service VNet-integrációval való használatra, akkor nem kell tanúsítványt feltöltenie. Az átjáró létrehozása akár 30 percet is igénybe vehet. Az alkalmazás nem integrálható a VNet, amíg az átjáró nincs kiépítve. 

### <a name="configure-vnet-integration-with-your-app"></a>VNet-integráció konfigurálása az alkalmazással 

Az VNet-integráció engedélyezése az alkalmazásban: 

1. Nyissa meg az alkalmazást a Azure Portalon, és nyissa meg az alkalmazás beállításait, és válassza a hálózatkezelés > VNet-integráció lehetőséget. Az ASP-nek standard SKU-ban kell lennie, vagy jobb a VNet-integrációs funkció használatához. 
 ![VNet-integráció felhasználói felülete][1]

1. Válassza a **VNet hozzáadása**lehetőséget. 
 ![VNet-integráció hozzáadása][2]

1. Válassza ki a VNet. 
  ![Válassza ki a VNet][8]
  
Az alkalmazás az utolsó lépés után újraindul.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Az átjáró szükséges VNet-integrációs funkciójának működése

Az átjáró szükséges VNet-integrációs funkciója a pont – hely VPN-technológiára épül. A pont – hely technológia korlátozza a hálózati hozzáférést az alkalmazást üzemeltető virtuális géphez. Az alkalmazások csak az interneten keresztül, Hibrid kapcsolatok vagy VNet-integráción keresztül küldhetnek forgalmat. 

![A VNet-integráció működése][3]

## <a name="managing-vnet-integration"></a>VNet-integráció kezelése
A VNet való csatlakozás és az azokhoz való kapcsolódás lehetősége az alkalmazás szintjén történik. A több alkalmazás közötti VNet-integrációt befolyásoló műveletek a App Service csomag szintjén találhatók. Az App > Networking > VNet Integration Portalon megtekintheti a VNet adatait. Az ASP-szinten hasonló információk jelennek meg az ASP > hálózatkezelés > VNet integrációs portálon, beleértve az adott App Service-csomaghoz tartozó alkalmazásokat is, amelyek egy adott integrációt használnak.

 ![VNet részletei][4]

A VNet-integráció felhasználói felületén elérhető információk megegyeznek az alkalmazás és az ASP-portálok között.

* VNet neve – hivatkozások a virtuális hálózat felhasználói felületére
* Hely – a VNet helyét tükrözi. Egy másik helyen lévő VNet való integráció késési problémákat okozhat az alkalmazásban. 
* Tanúsítvány állapota – azt jelzi, hogy a tanúsítványok szinkronizálva vannak-e a App Service-csomag és a VNet között.
* Átjáró állapota – ha az átjáró szükséges VNet-integrációt használja, láthatja az átjáró állapotát.
* VNet – a VNet IP-címét jeleníti meg. 
* Pont – hely címtartomány – megjeleníti a VNet hely IP-címének helyét. Ha az átjáró által igényelt funkció használata során hívásokat kezdeményez a VNet, az alkalmazás címe a következő címek egyike lesz. 
* Helyek közötti címtartomány – a helyek közötti VPN-eket használhatja a VNet helyszíni erőforrásokhoz vagy más VNet való összekapcsolásához. Itt jelennek meg a VPN-kapcsolattal megadott IP-címtartományok.
* DNS-kiszolgálók – a VNet konfigurált DNS-kiszolgálókat jeleníti meg.
* A VNet átirányított IP-címek – megjeleníti a VNet felé irányuló forgalom irányításához használt címeket. 

Az egyetlen művelet, amelyet a VNet-integráció alkalmazás nézetében elvégezhet, az alkalmazás leválasztása a VNet, amelyhez jelenleg csatlakozik. Az alkalmazás VNet való leválasztásához válassza a **Leválasztás**lehetőséget. Az alkalmazás újraindul, amikor leválaszt egy VNet. A kapcsolat bontása nem módosítja a VNet. Az alhálózat vagy átjáró nem törlődik. Ha ezt követően törölni szeretné a VNet, először le kell választania az alkalmazást a VNet, és törölnie kell az abban lévő erőforrásokat, például az átjárókat. 

Az ASP-VNet integrációs felhasználói felületének eléréséhez nyissa meg az ASP felhasználói felületét, és válassza a **hálózatkezelés**lehetőséget.  A VNet-integráció területen válassza a **Konfigurálás** lehetőséget a hálózati szolgáltatás állapotának felhasználói felületének megnyitásához.

![Az ASP VNet integrációs adatai][5]

Az ASP VNet-integráció felhasználói felülete megjeleníti az ASP-alkalmazások által használt összes virtuális hálózatok. Az egyes VNet részleteinek megtekintéséhez kattintson az Önt érdeklő VNet. Itt két műveletet hajthat végre.

* A **hálózat szinkronizálása**. A szinkronizálási hálózati művelet csak az átjárótól függő VNet-integrációs szolgáltatáshoz használható. A szinkronizálási hálózati művelet végrehajtása biztosítja, hogy a tanúsítványok és a hálózati adatok szinkronban legyenek. Ha a VNet DNS-t adja hozzá vagy módosítja, **szinkronizálási hálózati** műveletet kell végrehajtania. Ez a művelet újraindítja a VNet használó alkalmazásokat.
* **Útvonalak hozzáadása** Útvonalak hozzáadásakor a kimenő forgalom a VNet fog vezetni.

**Útválasztás** A VNet meghatározott útvonalak az alkalmazásból a VNet irányuló forgalom irányítására szolgálnak. Ha további kimenő forgalmat kell elküldeni a VNet, akkor itt adhatja hozzá ezeket a címeket. Ez a funkció csak az átjáróval megkövetelt VNet-integrációval működik.

**Tanúsítványok** Ha az átjárón engedélyezve van az VNet-integráció, akkor a kapcsolatok biztonságának biztosítása érdekében szükség van a tanúsítványok cseréjére. A tanúsítványokkal együtt a DNS-konfiguráció, az útvonalak és más hasonló, a hálózatot leíró dolgok.
Ha a tanúsítványok vagy a hálózati adatok módosulnak, akkor a "hálózat szinkronizálása" gombra kell kattintania. Ha a "hálózat szinkronizálása" gombra kattint, az alkalmazás és a VNet közötti kapcsolat rövid kimaradást okoz. Az alkalmazás nem indul újra, a kapcsolat elvesztése miatt előfordulhat, hogy a hely nem működik megfelelően. 

## <a name="accessing-on-premises-resources"></a>Helyszíni erőforrások elérése
Az alkalmazások a helyek közötti kapcsolatokkal rendelkező virtuális hálózatok integrálásával érhetik el a helyszíni erőforrásokat. Ha az átjáróhoz szükséges VNet-integrációt használja, frissítenie kell a helyszíni VPN Gateway-útvonalakat a pont – hely címtartomány-blokkokkal. Amikor először állítja be a helyek közötti VPN-t, a konfigurálásához használt parancsfájloknak megfelelően kell beállítania az útvonalakat. Ha a pont – hely címeket a helyek közötti VPN létrehozása után adja hozzá, akkor manuálisan kell frissítenie az útvonalakat. Az egyes átjárók működésének részletes leírását itt találja. A BGP-t nem lehet helyek közötti VPN-kapcsolattal konfigurálni.

Nincs szükség további konfigurációra ahhoz, hogy a regionális VNet integrációs szolgáltatás elérje a VNet és a helyszíni környezettel. Egyszerűen csatlakoznia kell a VNet a helyszíni környezethez a ExpressRoute vagy a helyek közötti VPN használatával. 

> [!NOTE]
> Az átjáró szükséges VNet-integrációs funkciója nem integrálja az alkalmazást olyan VNet, amely ExpressRoute-átjáróval rendelkezik. Még akkor is, ha az ExpressRoute- [][VPNERCoex] átjáró párhuzamosan van konfigurálva, a VNet-integráció nem működik. Ha egy ExpressRoute-kapcsolaton keresztül kell hozzáférni az erőforrásokhoz, akkor használhatja a regionális VNet integrációs funkciót vagy egy [app Service Environment][ASE], amely a VNet fut. 
> 
> 

## <a name="peering"></a>Társviszony-létesítés
Ha a regionális VNet-integrációval való összevonást használ, nincs szükség további konfigurálásra. 

Ha az átjárót a szükséges VNet-integrációval használja, néhány további elemet is be kell állítania. Az alkalmazással való együttműködés konfigurálása:

1. Adjon hozzá egy egyenrangú kapcsolatot azon a VNet, amelyhez az alkalmazás csatlakozik. A társítási kapcsolat hozzáadásakor engedélyezze a **virtuális hálózati hozzáférés engedélyezése beállítást** , és jelölje be a **továbbított forgalom** engedélyezése és az **átjáró átvitelének engedélyezése**beállítást.
1. Vegyen fel egy társas kapcsolatot a VNet, amely a csatlakoztatott VNet van csatlakoztatva. Ha hozzáadja a társítási kapcsolatot a cél VNet, engedélyezze a **virtuális hálózati hozzáférés engedélyezése** és a **továbbított forgalom** engedélyezése és a **távoli átjárók engedélyezése**beállítást.
1. Nyissa meg a App Service tervet > hálózatkezelés > VNet integrációs felhasználói felületét a portálon.  Válassza ki azt a VNet, amelyhez az alkalmazás csatlakozik. Az Útválasztás szakaszban adja meg a VNet azon VNet, amelyhez az alkalmazás csatlakoztatva van.  


## <a name="pricing-details"></a>Díjszabás
A regionális VNet-integrációs szolgáltatásnak nem kell további díjat fizetnie az ASP díjszabási szintjein túli használatért.

Az átjáró szükséges VNet-integrációs funkciója három kapcsolódó díjjal jár:

* Az ASP díjszabási szintjeinek díjai – az alkalmazásainak standard, prémium vagy PremiumV2 App Service-csomaggal kell rendelkezniük. Ezekről a költségekről itt talál további részleteket: [App Service díjszabása][ASPricing]. 
* Adatátviteli költségek – a kimenő adatforgalomért akkor is díjat számítunk fel, ha a VNet ugyanabban az adatközpontban található. Ezeket a díjakat [adatátvitel díjszabása][DataPricing]ismerteti. 
* VPN Gateway költségek – a pont – hely típusú VPN-hez szükséges VNet-átjáró költségeinek költsége. A részletek a [VPN Gateway díjszabási][VNETPricing] oldalán találhatók.


## <a name="troubleshooting"></a>Hibaelhárítás
Habár a funkció egyszerűen beállítható, ez nem jelenti azt, hogy a probléma ingyenes lesz. Ha problémák merülnek fel a kívánt végpont elérésekor, néhány segédprogram használható az alkalmazás-konzolról való kapcsolat tesztelésére. Két konzolt használhat. Az egyik a kudu-konzol, a másik pedig a Azure Portal konzolja. A kudu-konzol alkalmazásból való eléréséhez nyissa meg az eszközök-> kudu. A Kudo-konzolt a következő helyen is elérheti: [sitename]. SCM. azurewebsites. net. A webhely betöltése után lépjen a Debug konzol lapra. Az Azure Portal üzemeltetett konzoljának eléréséhez nyissa meg az eszközök-> konzolt. 

#### <a name="tools"></a>Eszközök
Az eszközök **pingelése**, az **nslookup** és a **tracert** a biztonsági korlátozások miatt nem fog működni a konzolon. Az üresség kitöltéséhez két különálló eszköz lett hozzáadva. A DNS-funkciók teszteléséhez hozzáadott egy nameresolver. exe nevű eszközt. A szintaxis a következő:

    nameresolver.exe hostname [optional: DNS Server]

A **nameresolver** segítségével megtekintheti az alkalmazástól függő gazdagépeket. Így ellenőrizheti, hogy van-e valamilyen helytelenül konfigurálva a DNS-sel, vagy lehet, hogy nem fér hozzá a DNS-kiszolgálóhoz. A DNS-kiszolgáló, amelyet az alkalmazás a-konzolon fog használni, a környezeti változók WEBSITE_DNS_SERVER és WEBSITE_DNS_ALT_SERVER tekintheti meg.

A következő eszköz lehetővé teszi a TCP-kapcsolat tesztelését egy gazdagép és port kombinációhoz. Az eszköz neve **tcpping** , és a szintaxis a következő:

    tcpping.exe hostname [optional: port]

A **tcpping** segédprogrammal megtudhatja, hogy elérhető-e egy adott gazdagép és port. Ez csak akkor mutathat sikert, ha: van egy alkalmazás, amely a gazdagép és a port kombinációját figyeli, és az alkalmazásból hálózati hozzáférés van a megadott gazdagéphez és porthoz.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>A VNet által üzemeltetett erőforrásokhoz való hozzáférés hibakeresése
Számos dolog megakadályozhatja, hogy az alkalmazás elérjen egy adott gazdagépet és portot. Az idő nagy része három dolog egyike:

* **A tűzfal éppen folyamatban van.** Ha tűzfallal rendelkezik, a TCP-időtúllépést fogja elérni. Ebben az esetben a TCP-időkorlát 21 másodperc. A kapcsolat teszteléséhez használja a **tcpping** eszközt. A TCP-időtúllépések a tűzfalakon túl sok dolog okozhatja, de ott is elindulhat. 
* **A DNS nem érhető el.** A DNS-időtúllépési érték a DNS-kiszolgálón három másodperc. Ha két DNS-kiszolgálóval rendelkezik, az időtúllépés 6 másodperc. A nameresolver használatával ellenőrizze, hogy a DNS működik-e. Ne feledje, hogy nem használhatja az nslookupt úgy, hogy ne használja a DNS-t, amelyet a VNet konfigurált. Ha nem érhető el, lehet, hogy tűzfallal vagy NSG blokkolja a hozzáférést a DNS-hez, vagy leállt.

Ha ezek az elemek nem válaszolnak a problémákra, először tekintse meg a következő dolgokat: 

**regionális VNet-integráció**
* a cél az RFC 1918-címe
* van olyan NSG, amely blokkolja az integrációs alhálózatról érkező kimenő forgalmat
* Ha a ExpressRoute vagy egy VPN-en halad át, a helyszíni átjáró úgy van konfigurálva, hogy az Azure-ba irányítsa a forgalmat? Ha elérheti a végpontokat a VNet, de nem a helyszínen, akkor ez jó választás.

**átjáró szükséges VNet-integráció**
* a pont – hely címtartomány az RFC 1918-tartományokban (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Megjelenik az átjáró a portálon? Ha az átjáró nem működik, hozza létre a biztonsági mentést.
* A tanúsítványok szinkronban jelennek meg, vagy feltételezi, hogy a hálózati konfiguráció megváltozott?  Ha a tanúsítványok nincsenek szinkronban, vagy ha azt gyanítja, hogy módosult a VNet konfigurációja, amely nem lett szinkronizálva a ASP, akkor nyomja meg a "Sync Network" (hálózat szinkronizálása) lehetőséget.
* Ha a ExpressRoute vagy egy VPN-en halad át, a helyszíni átjáró úgy van konfigurálva, hogy az Azure-ba irányítsa a forgalmat? Ha elérheti a végpontokat a VNet, de nem a helyszínen, akkor ez jó választás.

A hálózati problémák hibakeresése kihívást jelent, mivel nem látható, hogy mi blokkolja a hozzáférést egy adott gazdagéphez: Port kombináció. Az okok némelyike a következőkből áll:

* a gazdagépen lévő tűzfallal megakadályozhatja, hogy hozzáférjen az alkalmazás portjához a pontról a hely IP-tartományába. Az alhálózatok átlépéséhez gyakran nyilvános hozzáférésre van szükség.
* a célként megadott gazdagép nem működik
* az alkalmazás nem érhető el
* nem megfelelő IP-címet vagy állomásnevet adott meg
* az alkalmazás a várttól eltérő portot figyel. A folyamat AZONOSÍTÓját megtekintheti a figyelő porton a "netstat-Aon" paranccsal a végponti gazdagépen. 
* a hálózati biztonsági csoportok úgy vannak konfigurálva, hogy ne férhessenek hozzá az alkalmazás-gazdagéphez és-porthoz a pontról a hely IP-tartományába

Ne feledje, hogy nem tudja, milyen címet fog használni az alkalmazás ténylegesen. Az integrációs alhálózat vagy a pont – hely címtartomány bármely címe lehet, ezért a teljes címtartományból engedélyeznie kell a hozzáférést. 

További hibakeresési lépések a következők:

* kapcsolódjon egy virtuális géphez a VNet, és próbálja meg elérni az erőforrás-gazdagépet: innen a port. A TCP-hozzáférés teszteléséhez használja a következő PowerShell **-parancsot: test-NETCONNECTION**. A szintaxis a következő:

      test-netconnection hostname [optional: -Port]

* egy alkalmazás üzembe helyezése egy virtuális gépen, valamint a gazdagép és a port elérésének tesztelése a konzolról az **tcpping** használatával

#### <a name="on-premises-resources"></a>Helyszíni erőforrások ####

Ha az alkalmazás nem tud helyszíni erőforrást elérni, akkor ellenőrizze, hogy elérhető-e az erőforrás a VNet. Használja a **test-NETCONNECTION** PowerShell-parancsot a TCP-hozzáférés ellenőrzéséhez. Ha a virtuális gép nem tudja elérni a helyszíni erőforrást, előfordulhat, hogy a VPN-vagy ExpressRoute-kapcsolat nem megfelelően van konfigurálva.

Ha a VNet által üzemeltetett virtuális gép elérheti a helyszíni rendszerét, de az alkalmazása nem lehetséges, akkor az oka valószínűleg az alábbi okok egyike:

* az útvonalak nincsenek konfigurálva az alhálózattal, vagy a helyi átjárón lévő hely címeire mutatnak
* a hálózati biztonsági csoportok blokkolja a pont – hely IP-címtartomány elérését.
* a helyszíni tűzfalak blokkolja a pont – hely IP-címtartomány forgalmát
* nem RFC 1918-es címről szeretne elérni a regionális VNet integrációs funkciójával


## <a name="powershell-automation"></a>PowerShell-automatizálás

A PowerShell használatával integrálhatja App Serviceeit egy Azure-Virtual Network. A használatra kész parancsfájlhoz lásd: [alkalmazás Összekötése Azure app Service egy Azure-Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
