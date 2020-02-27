---
title: Alkalmazás integrálása az Azure Virtual Network
description: Azure App Service alkalmazások integrálása az Azure Virtual Networks szolgáltatással.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 76139716fe11536faa0ff792185ba1643801c641
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649018"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Az alkalmazás integrálása Azure-Virtual Network
Ez a dokumentum ismerteti a Azure App Service Virtual Network Integration funkciót, valamint azt, hogyan állíthatja be az alkalmazásokkal a [Azure app Serviceban](https://go.microsoft.com/fwlink/?LinkId=529714). Az [Azure Virtual Networks][VNETOverview] (virtuális hálózatok) lehetővé teszi, hogy számos Azure-erőforrást egy nem internetes útválasztású hálózaton helyezzen el.  

A Azure App Service két változattal rendelkezik. 

1. A több-bérlős rendszerek, amelyek támogatják az elkülönített árképzési csomagok teljes körét
2. A VNet üzembe helyező és az elkülönített díjszabási csomagot támogató App Service Environment

Ez a dokumentum a több-bérlős App Service használható VNet-integrációs szolgáltatáson keresztül érhető el. Ha az alkalmazás [app Service Environmentban][ASEintro]van, akkor már egy VNet van, és nem igényli a VNet integrációs funkciójának használatát az erőforrások eléréséhez ugyanabban a VNet. Az összes App Service hálózati szolgáltatással kapcsolatos részletekért olvassa el a [app Service hálózatkezelési funkcióit](networking-features.md) .

A VNet integrációja lehetővé teszi, hogy a webalkalmazás hozzáférhessen a virtuális hálózat erőforrásaihoz, de nem biztosít bejövő privát hozzáférést a webalkalmazáshoz a VNet. A privát helyhez való hozzáférés arra utal, hogy az alkalmazás csak a magánhálózaton keresztül érhető el, például egy Azure-beli virtuális hálózaton belülről. A VNet-integráció csak az alkalmazásból kimenő hívásokat tesz a VNet. A VNet-integrációs szolgáltatás másképp viselkedik, ha a virtuális hálózatok ugyanabban a régióban és más régiókban virtuális hálózatok van használatban. A VNet-integrációs szolgáltatásnak két változata van.

1. Regionális VNet-integráció – Ha ugyanahhoz a régióhoz kapcsolódik a Resource Manager-virtuális hálózatok, egy dedikált alhálózattal kell rendelkeznie abban a VNet, amelyhez az integrációt végzi. 
2. Átjáró szükséges VNet-integráció – ha más régiókban lévő virtuális hálózatok vagy ugyanazon régióban lévő klasszikus VNet kapcsolódik, akkor szüksége van egy Virtual Network átjáróra, amelyet a cél VNet kiépít.

A VNet integrációs funkciói:

* Standard, prémium, PremiumV2 vagy rugalmas prémium díjszabási csomag megkövetelése 
* a TCP és az UDP támogatása
* App Service-alkalmazások és-függvények használata

Néhány dolog, amit a VNet-integráció nem támogat, beleértve a következőket:

* meghajtó csatlakoztatása
* AD-integráció 
* NetBios

Az átjáróhoz szükséges VNet-integráció csak a célként megadott VNet lévő erőforrásokhoz, illetve a VNet vagy a VPN-hez csatlakozó hálózatokhoz biztosít hozzáférést. Az átjáróhoz szükséges VNet-integráció nem teszi lehetővé a ExpressRoute-kapcsolatokon keresztül elérhető erőforrásokhoz való hozzáférést, vagy a szolgáltatási végpontokkal való munkát. 

A használt verziótól függetlenül a VNet integrációja lehetővé teszi, hogy a webalkalmazás hozzáférhessen a virtuális hálózat erőforrásaihoz, de a virtuális hálózatról nem biztosít bejövő privát hozzáférést a webalkalmazáshoz. A privát helyhez való hozzáférés arra utal, hogy az alkalmazás csak a magánhálózaton keresztül érhető el, például egy Azure-beli virtuális hálózaton belülről. A VNet-integráció csak az alkalmazásból kimenő hívásokat tesz a VNet. 

## <a name="enable-vnet-integration"></a>VNet-integráció engedélyezése 

1. Nyissa meg a hálózati felhasználói felületet a App Service portálon. A VNet-integráció területen válassza *a "kattintson ide a konfiguráláshoz"* lehetőséget. 

1. Válassza a **VNet hozzáadása**lehetőséget.  

   ![VNet-integráció kiválasztása][1]

1. A legördülő lista az előfizetésben található összes Resource Manager-virtuális hálózatok ugyanabban a régióban és alatta fogja tartalmazni, amely az összes többi régióban található Resource Manager-virtuális hálózatok listája. Válassza ki a VNet, amelybe integrálni kívánja a-t.

   ![Válassza ki a VNet][2]

   * Ha a VNet ugyanabban a régióban van, akkor hozzon létre egy új alhálózatot, vagy válasszon üres, már meglévő alhálózatot. 

   * Egy másik régióban lévő VNet kiválasztásához rendelkeznie kell egy olyan Virtual Network-átjáróval, amelynél engedélyezve van a pont – hely.

   * A klasszikus VNet való integráláshoz ahelyett, hogy a VNet legördülő menüre kattintana, válassza a **kattintson ide a klasszikus VNet való kapcsolódáshoz**lehetőséget. Válassza ki a kívánt klasszikus VNet. A célként megadott VNet már engedélyezve kell lennie egy olyan Virtual Network-átjárónak, amelynek a helyén engedélyezve van a pont.

    ![Klasszikus VNet kiválasztása][3]
    
Az integráció során az alkalmazás újraindul.  Az integráció befejezését követően megtekintheti az integrált VNet részleteit. 

Ha az alkalmazása integrálva van a VNet, akkor ugyanazt a DNS-kiszolgálót fogja használni, amelyet a VNet konfigurált, hacsak nem Azure DNS Private Zones. A VNet-integrációt jelenleg nem használhatja Azure DNS Private Zones.

## <a name="regional-vnet-integration"></a>regionális VNet-integráció

A regionális VNet-integráció használata lehetővé teszi, hogy az alkalmazás hozzáférjen:

* a VNet lévő erőforrások ugyanabban a régióban, amelyhez integrálva van 
* a virtuális hálózatok-ben lévő erőforrások a VNet azonos régióban találhatók
* szolgáltatás-végpont által védett szolgáltatások
* erőforrások ExpressRoute-kapcsolatokon keresztül
* azon VNet erőforrásai, amelyekhez csatlakozik
* többek között a ExpressRoute-kapcsolatokat is tartalmazó erőforrások
* Privát végpontok 

Ha VNet-integrációt használ az virtuális hálózatok-ben ugyanabban a régióban, akkor a következő Azure hálózati funkciókat használhatja:

* Hálózati biztonsági csoportok (NSG) – letilthatja a kimenő forgalmat egy olyan hálózati biztonsági csoporttal, amely az integrációs alhálózaton van elhelyezve. A bejövő szabályok nem érvényesek, mert a VNet-integráció nem használható a webalkalmazáshoz való bejövő hozzáférés biztosításához.
* Route Tables (UDR) – az integrációs alhálózaton elhelyezhető egy útválasztási táblázat, amely a kívánt kimenő forgalmat küldi el. 

Alapértelmezés szerint az alkalmazás csak a VNet irányítja át a RFC1918 forgalmat. Ha az összes kimenő forgalmat át szeretné irányítani a VNet, alkalmazza az alkalmazás beállítását WEBSITE_VNET_ROUTE_ALL az alkalmazásra. Az alkalmazás beállításának konfigurálása:

1. Nyissa meg a konfigurációs felhasználói felületet az alkalmazás-portálon. **Új Alkalmazásbeállítás** kiválasztása
1. Írja be a **WEBSITE_VNET_ROUTE_ALL** nevet a név mezőbe, az érték mezőbe pedig az **1** értéket.

   ![Alkalmazásbeállítás megadása][4]

1. Kattintson az **OK** gombra.
1. Kattintson a **Mentés** gombra

Ha az összes kimenő forgalmat átirányítja a VNet, a rendszer az integrációs alhálózatra alkalmazott NSG és UDR fogja alkalmazni. Ha az összes kimenő forgalmat átirányítja a VNet, a kimenő címek továbbra is az alkalmazás tulajdonságaiban felsorolt kimenő címek lesznek, kivéve, ha olyan útvonalakat ad meg, amelyek máshová nem küldik a forgalmat. 

Bizonyos korlátozások vonatkoznak a VNet-integrációnak az azonos régióban található virtuális hálózatok való használatára:

* A globális társ-összekapcsolási kapcsolatok erőforrásai nem érhetők el
* A szolgáltatás csak olyan újabb App Service skálázási egységekből érhető el, amelyek támogatják a PremiumV2 App Service csomagokat.
* Az integrációs alhálózatot csak egy App Service-csomag használhatja
* A szolgáltatás nem használható a App Service Environmentban található elkülönített csomag-alkalmazásokban
* A szolgáltatáshoz egy nem használt alhálózat szükséges, amely egy/27 32-es címmel vagy nagyobb erőforrás-kezelő VNet
* Az alkalmazásnak és a VNet ugyanabban a régióban kell lennie
* Integrált alkalmazással nem törölhet VNet. A VNet törlése előtt távolítsa el az integrációt. 
* Csak a virtuális hálózatok integrálható a webalkalmazással megegyező előfizetésben
* App Service-csomag esetében csak egy regionális VNet-integráció lehet. Ugyanazon a App Service csomagon belül több alkalmazás is használhatja ugyanazt a VNet. 
* Egy alkalmazás vagy egy App Service csomag előfizetése nem módosítható, amíg van olyan alkalmazás, amely regionális VNet-integrációt használ

Az egyes App Service-példányok esetében egy-egy-egy-egy-egy-egy Ha öt példányra méretezi az alkalmazást, akkor öt címet használ a rendszer. Mivel az alhálózat mérete nem módosítható a hozzárendelés után, olyan alhálózatot kell használnia, amely elég nagy ahhoz, hogy megfeleljen az alkalmazásnak. Az ajánlott méret a/26, 64 címmel. Az a/26, 64 címmel rendelkező prémium App Service-csomag 30 példányban. Ha felfelé vagy lefelé méretezi a App Service tervet, a rövid ideig kétszer annyi címet kell használnia. 

Ha azt szeretné, hogy alkalmazásai egy másik App Service tervben olyan VNet érjenek el, amely már egy másik App Service-csomagban található alkalmazáshoz csatlakozik, akkor a korábban létező VNet-integráció által használttól eltérő alhálózatot kell választania.  

A szolgáltatás előzetes verzióban érhető el a Linux rendszerben. A szolgáltatás Linux-formája csak az RFC 1918-címekre irányuló hívások kezdeményezését támogatja (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-app-for-containers"></a>Tárolókhoz készült Web App

Ha a beépített rendszerképeket használó Linuxon App Service használ, a regionális VNet-integráció további változtatások nélkül működik. Ha Web App for Containers használ, módosítania kell a Docker-rendszerképet, hogy használni lehessen a VNet-integrációt. A Docker-rendszerképben használja a PORT környezeti változót a fő webkiszolgáló figyelési portjának hardcoded helyett. A PORT környezeti változót automatikusan App Service platform állítja be a tároló indítási ideje szerint. Ha SSH-t használ, az SSH démont úgy kell konfigurálni, hogy figyelje a SSH_PORT környezeti változó által megadott portszámot a regionális VNet-integráció használatakor.  Az átjáró nem támogatja az VNet-integrációt a Linux rendszeren. 

### <a name="service-endpoints"></a>Szolgáltatási végpontok

A regionális VNet-integráció lehetővé teszi a szolgáltatási végpontok használatát.  Ha szolgáltatási végpontokat szeretne használni az alkalmazással, a regionális VNet-integrációval csatlakozhat egy kiválasztott VNet, majd az integrációhoz használt alhálózaton konfigurálhatja a szolgáltatási végpontokat. 

### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)

A hálózati biztonsági csoportok lehetővé teszik a bejövő és kimenő forgalom letiltását egy VNet lévő erőforrásokra. A regionális VNet-integrációt használó webalkalmazások [hálózati biztonsági csoporttal][VNETnsg] letilthatják a VNet vagy az interneten lévő erőforrásokra irányuló kimenő forgalmat. A nyilvános címekre irányuló forgalom letiltásához az alkalmazás beállítását WEBSITE_VNET_ROUTE_ALL 1-re kell beállítani. Egy NSG bejövő szabályai nem vonatkoznak az alkalmazásra, mivel a VNet-integráció csak az alkalmazásból érkező kimenő forgalmat érinti. A webalkalmazás bejövő forgalmának szabályozásához használja a hozzáférési korlátozások funkciót. Az integrációs alhálózatra alkalmazott NSG az integrációs alhálózatra alkalmazott útvonalaktól függetlenül érvényesek lesznek. Ha a WEBSITE_VNET_ROUTE_ALL értéke 1, és nem rendelkezett olyan útvonallal, amely az integrációs alhálózaton lévő nyilvános címek forgalmát érintette, akkor az összes kimenő forgalom továbbra is az integrációs alhálózathoz rendelt NSG lesz. Ha WEBSITE_VNET_ROUTE_ALL nem lett beállítva, a NSG csak a RFC1918-forgalomra lesz alkalmazva.

### <a name="routes"></a>Útvonalak

Az útválasztási táblázatok lehetővé teszik a kimenő forgalom továbbítását az alkalmazásból bárhonnan, bárhol is legyenek. Alapértelmezés szerint az útválasztási táblák csak a RFC1918-forgalmat érintik.  Ha a WEBSITE_VNET_ROUTE_ALL 1 értékre állítja, akkor az összes kimenő hívást érinti a rendszer. Az integrációs alhálózaton beállított útvonalak nem érintik a bejövő alkalmazások kéréseire adott válaszokat. A gyakori célhelyek lehetnek tűzfalak vagy átjárók. Ha a helyszíni összes kimenő forgalmat is át szeretné irányítani, az útválasztási táblázat segítségével elküldheti az összes kimenő forgalmat a ExpressRoute-átjárónak. Ha átirányítja a forgalmat egy átjáróra, ügyeljen arra, hogy a külső hálózatban lévő útvonalakat az összes válasz visszaküldéséhez adja meg.

A Border Gateway Protocol (BGP) útvonalak az alkalmazások forgalmát is érintik. Ha az ExpressRoute-átjáróhoz hasonló BGP-útvonalak vannak, akkor az alkalmazás kimenő forgalmát fogja érinteni. Alapértelmezés szerint a BGP-útvonalak csak az RFC1918-cél forgalmát érintik. Ha a WEBSITE_VNET_ROUTE_ALL értéke 1, akkor a BGP-útvonalakon az összes kimenő forgalom hatással lehet. 

### <a name="how-regional-vnet-integration-works"></a>A regionális VNet-integráció működése

A App Serviceban lévő alkalmazások a feldolgozói szerepkörökben vannak tárolva. Az alapszintű és a magasabb díjszabási csomagok olyan dedikált üzemeltetési csomagok, amelyeken nincs más, ugyanazon dolgozón futó ügyfél-munkaterhelés. A regionális VNet-integráció úgy működik, hogy a virtuális adaptereket a meghatalmazott alhálózat címeivel csatlakoztatja. Mivel a feladó címe a VNet található, a VNet és a saját VNet is elérheti a legtöbb dolgot, ugyanúgy, mint egy virtuális gép a. A hálózatkezelés megvalósítása eltér a virtuális gép VNet való futtatástól, ezért a funkció használata során a hálózati szolgáltatások némelyike még nem érhető el.

![A regionális VNet-integráció működése][5]

Ha a regionális VNet-integráció engedélyezve van, az alkalmazás továbbra is a normál módon megegyező csatornákon keresztül hajtja végre a kimenő hívásokat az interneten. Az alkalmazás tulajdonságai portálon felsorolt kimenő címek továbbra is az alkalmazás által használt címek. Az alkalmazás változásai, a Service Endpoint Secure Services vagy az RFC 1918-címek meghívása a VNet. Ha WEBSITE_VNET_ROUTE_ALL értéke 1, akkor a rendszer minden kimenő forgalmat elküldhet a VNet. 

A szolgáltatás csak egy virtuális felületet támogat a munkavégzők esetében.  A munkavégzők egy virtuális felülete egy regionális VNet integrációt jelent App Service csomagon keresztül. Az azonos App Service tervben szereplő összes alkalmazás használhatja ugyanazt a VNet-integrációt, de ha egy alkalmazásnak egy további VNet való csatlakozásra van szüksége, létre kell hoznia egy másik App Service tervet. A használt virtuális felület nem erőforrás, amelyhez az ügyfeleknek közvetlen hozzáférésük van.

A technológia működésének jellegéből adódóan a VNet-integrációhoz használt forgalom nem jelenik meg Network Watcher vagy NSG flow-naplókban.  

## <a name="gateway-required-vnet-integration"></a>átjáró szükséges VNet-integráció

Az átjáróhoz szükséges VNet-integráció támogatja a csatlakozást egy másik régióban lévő VNet vagy egy klasszikus VNet. Átjáró szükséges VNet-integráció: 

* Lehetővé teszi, hogy egy alkalmazás egyszerre csak 1 VNet kapcsolódjon
* Lehetővé teszi akár öt virtuális hálózatok integrálását egy App Service csomagon belül 
* Lehetővé teszi, hogy ugyanazt a VNet egy App Service-csomagban több alkalmazás is felhasználja anélkül, hogy ez hatással lenne egy App Service-csomag által használható teljes számra.  Ha hat alkalmazása ugyanazt a VNet használja ugyanabban a App Service-csomagban, akkor 1 VNet számítunk fel. 
* A 99,9%-os SLA-t támogat az átjáró SLA-ja miatt
* Lehetővé teszi az alkalmazások számára, hogy a VNet konfigurált DNS-t használják
* A használatához az SSTP pont – hely típusú VPN-kapcsolattal konfigurált Virtual Network Route-alapú átjáró szükséges ahhoz, hogy az alkalmazáshoz lehessen csatlakozni. 

Az átjáró szükséges VNet-integrációja nem használható:

* Linux-alkalmazásokkal
* ExpressRoute-vel összekapcsolt VNet 
* A szolgáltatási végpontok biztonságos erőforrásainak elérése
* Egy párhuzamos átjáróval, amely támogatja mindkét ExpressRoute, és a hely/hely közötti VPN-eket is.

### <a name="set-up-a-gateway-in-your-vnet"></a>Átjáró beállítása a VNet ###

Átjáró létrehozása:

1. [Hozzon létre egy átjáró-alhálózatot][creategatewaysubnet] a VNet.  

1. [Hozza létre a VPN-átjárót][creategateway]. Válasszon egy Route-alapú VPN-típust.

1. [Állítsa a pontot a hely címeire][setp2saddresses]. Ha az átjáró nem az alapszintű SKU-ban található, akkor a IKEV2 le kell tiltani a pont – hely konfigurációban, és az SSTP-t ki kell választani. A hely címterület pontjának az RFC 1918-es címek, a 10.0.0.0/8, a 172.16.0.0/12, a 192.168.0.0/16 elemnek kell lennie

Ha most hozza létre az átjárót App Service VNet-integrációval való használatra, akkor nem kell tanúsítványt feltöltenie. Az átjáró létrehozása akár 30 percet is igénybe vehet. Az alkalmazás nem integrálható a VNet, amíg az átjáró nincs kiépítve. 

### <a name="how-gateway-required-vnet-integration-works"></a>Az átjáró szükséges VNet-integrációjának működése

Az átjáró szükséges a VNet-integrációra, amely a pont-hely VPN-technológiára épül. A hely VPN-EK pontja korlátozza a hálózati hozzáférést az alkalmazást üzemeltető virtuális géphez. Az alkalmazások csak az interneten keresztül, Hibrid kapcsolatok vagy VNet-integráción keresztül küldhetnek forgalmat. Ha az alkalmazás az átjáró szükséges VNet-integráció használatára van konfigurálva, akkor az Ön nevében egy összetett egyeztetést kell felügyelni az átjárón és az alkalmazás oldalán lévő tanúsítványok létrehozásához és hozzárendeléséhez. Ennek végeredménye, hogy az alkalmazások üzemeltetéséhez használt munkavégzők közvetlenül tudnak csatlakozni a virtuális hálózati átjáróhoz a kiválasztott VNet. 

![Az átjáró szükséges VNet-integrációjának működése][6]

### <a name="accessing-on-premises-resources"></a>Helyszíni erőforrások elérése

Az alkalmazások a helyek közötti kapcsolatokkal rendelkező virtuális hálózatok integrálásával érhetik el a helyszíni erőforrásokat. Ha az átjáróhoz szükséges VNet-integrációt használja, frissítenie kell a helyszíni VPN Gateway-útvonalakat a pont – hely címtartomány-blokkokkal. Amikor először állítja be a helyek közötti VPN-t, a konfigurálásához használt parancsfájloknak megfelelően kell beállítania az útvonalakat. Ha a pont – hely címeket a helyek közötti VPN létrehozása után adja hozzá, akkor manuálisan kell frissítenie az útvonalakat. Az egyes átjárók működésének részletes leírását itt találja. A BGP-t nem lehet helyek közötti VPN-kapcsolattal konfigurálni.

Nincs szükség további konfigurációra ahhoz, hogy a regionális VNet integrációs szolgáltatás elérje a VNet és a helyszíni környezettel. Egyszerűen csatlakoznia kell a VNet a helyszíni környezethez a ExpressRoute vagy a helyek közötti VPN használatával. 

> [!NOTE]
> Az átjáró szükséges VNet-integrációs funkciója nem integrálja az alkalmazást olyan VNet, amely ExpressRoute-átjáróval rendelkezik. Még akkor [is, ha az ExpressRoute-átjáró][VPNERCoex] párhuzamosan van konfigurálva, a VNet-integráció nem működik. Ha egy ExpressRoute-kapcsolaton keresztül kell hozzáférni az erőforrásokhoz, akkor használhatja a regionális VNet integrációs funkciót vagy egy [app Service Environment][ASE], amely a VNet fut. 
> 
> 

### <a name="peering"></a>Társviszony-létesítés

Ha a regionális VNet-integrációval való összevonást használ, nincs szükség további konfigurálásra. 

Ha az átjárót a szükséges VNet-integrációval használja, néhány további elemet is be kell állítania. Az alkalmazással való együttműködés konfigurálása:

1. Adjon hozzá egy egyenrangú kapcsolatot azon a VNet, amelyhez az alkalmazás csatlakozik. A társítási kapcsolat hozzáadásakor engedélyezze a **virtuális hálózati hozzáférés engedélyezése beállítást** , és jelölje be a **továbbított forgalom** engedélyezése és az **átjáró átvitelének engedélyezése**beállítást.
1. Vegyen fel egy társas kapcsolatot a VNet, amely a csatlakoztatott VNet van csatlakoztatva. Ha hozzáadja a társítási kapcsolatot a cél VNet, engedélyezze a **virtuális hálózati hozzáférés engedélyezése** és a **továbbított forgalom** engedélyezése és a **távoli átjárók engedélyezése**beállítást.
1. Nyissa meg a App Service tervet > hálózatkezelés > VNet integrációs felhasználói felületét a portálon.  Válassza ki azt a VNet, amelyhez az alkalmazás csatlakozik. Az Útválasztás szakaszban adja meg a VNet azon VNet, amelyhez az alkalmazás csatlakoztatva van.  

## <a name="managing-vnet-integration"></a>VNet-integráció kezelése 

A VNet való csatlakozás és a kapcsolat bontása alkalmazási szinten történik. A több alkalmazás közötti VNet-integrációt befolyásoló műveletek a App Service csomag szintjén találhatók. Az App > Networking > VNet Integration Portalon megtekintheti a VNet adatait. A hasonló információkat az ASP-szinten tekintheti meg az ASP > hálózatkezelés > VNet integrációs portálon.

Az egyetlen művelet, amelyet a VNet-integráció alkalmazás nézetében elvégezhet, az alkalmazás leválasztása a VNet, amelyhez jelenleg csatlakozik. Az alkalmazás VNet való leválasztásához válassza a **Leválasztás**lehetőséget. Az alkalmazás újraindul, amikor leválaszt egy VNet. A kapcsolat bontása nem módosítja a VNet. Az alhálózat vagy átjáró nem törlődik. Ha ezt követően törölni szeretné a VNet, először le kell választania az alkalmazást a VNet, és törölnie kell az abban lévő erőforrásokat, például az átjárókat. 

Az ASP VNet-integráció felhasználói felülete megjeleníti az ASP-alkalmazások által használt összes VNet-integrációt. Az egyes VNet részleteinek megtekintéséhez kattintson az Önt érdeklő VNet. Az átjáró szükséges VNet-integrációja két műveletet hajt végre.

* A **hálózat szinkronizálása**. A szinkronizálási hálózati művelet csak az átjárótól függő VNet-integrációs szolgáltatáshoz használható. A szinkronizálási hálózati művelet végrehajtása biztosítja, hogy a tanúsítványok és a hálózati adatok szinkronban legyenek. Ha a VNet DNS-t adja hozzá vagy módosítja, **szinkronizálási hálózati** műveletet kell végrehajtania. Ez a művelet újraindítja a VNet használó alkalmazásokat.
* **Útvonalak hozzáadása** Útvonalak hozzáadásakor a kimenő forgalom a VNet fog vezetni. 

**Átjáró szükséges VNet integrációs útválasztás** A VNet meghatározott útvonalak az alkalmazásból a VNet irányuló forgalom irányítására szolgálnak. Ha további kimenő forgalmat kell elküldeni a VNet, akkor itt adhatja hozzá ezeket a címeket. Ez a funkció csak az átjáróval megkövetelt VNet-integrációval működik. Az útválasztási táblák nem érintik az alkalmazás adatforgalmát, ha átjáró szükséges VNet-integrációt használ a regionális VNet-integrációval.

**Átjáró szükséges VNet integrációs tanúsítványok** Ha az átjárón engedélyezve van az VNet-integráció, akkor a kapcsolatok biztonságának biztosítása érdekében szükség van a tanúsítványok cseréjére. A tanúsítványokkal együtt a DNS-konfiguráció, az útvonalak és más hasonló, a hálózatot leíró dolgok.
Ha a tanúsítványok vagy a hálózati adatok módosulnak, akkor a "hálózat szinkronizálása" gombra kell kattintania. Ha a "hálózat szinkronizálása" gombra kattint, az alkalmazás és a VNet közötti kapcsolat rövid kimaradást okoz. Az alkalmazás nem indul újra, a kapcsolat elvesztése miatt előfordulhat, hogy a hely nem működik megfelelően. 

## <a name="pricing-details"></a>Díjszabás részletei
A regionális VNet-integrációs szolgáltatásnak nem kell további díjat fizetnie az ASP díjszabási szintjein túli használatért.

Az átjáró szükséges VNet-integrációs funkciója három kapcsolódó díjjal jár:

* Az ASP díjszabási szintjeinek díjai – az alkalmazásainak standard, prémium vagy PremiumV2 App Service-csomaggal kell rendelkezniük. A költségekről további részleteket itt talál: [app Service díjszabás][ASPricing]. 
* Adatátviteli költségek – a kimenő adatforgalomért akkor is díjat számítunk fel, ha a VNet ugyanabban az adatközpontban található. Ezeket a díjakat [adatátvitel díjszabása][DataPricing]ismerteti. 
* VPN Gateway költségek – a pont – hely típusú VPN-hez szükséges VNet-átjáró költségeinek költsége. A részletek a [VPN Gateway díjszabási][VNETPricing] oldalán találhatók.

## <a name="troubleshooting"></a>Hibakeresés
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
* a cél nem RFC1918-címet tartalmaz, és nincs WEBSITE_VNET_ROUTE_ALL beállítva 1 értékre.
* van olyan NSG, amely blokkolja az integrációs alhálózatról érkező kimenő forgalmat
* Ha a ExpressRoute vagy egy VPN-en halad át, a helyszíni átjáró úgy van konfigurálva, hogy a forgalmat az Azure-ba irányítsa? Ha a VNet a végpontokat is elérheti, de a helyszínen nem, ellenőrizze az útvonalakat.
* van elegendő engedélye a delegálás beállítására az integrációs alhálózaton? A regionális VNet integrációs konfigurációjában az integrációs alhálózat a Microsoft. Web számára lesz delegálva. A VNet-integráció felhasználói felülete automatikusan delegálja az alhálózatot a Microsoft. Web számára. Ha a fiókja nem rendelkezik megfelelő hálózati engedélyekkel a delegálás beállításához, szüksége lesz valakire, aki az integrációs alhálózaton attribútumokat állíthat be az alhálózat delegálásához. Az integrációs alhálózat manuális delegálásához nyissa meg az Azure Virtual Network alhálózat felhasználói felületét, és állítsa be a Microsoft. Web delegálását. 

**átjáró szükséges VNet-integráció**
* a pont – hely címtartomány az RFC 1918-tartományokban (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Megjelenik az átjáró a portálon? Ha az átjáró nem működik, hozza létre a biztonsági mentést.
* A tanúsítványok szinkronban jelennek meg, vagy feltételezi, hogy a hálózati konfiguráció megváltozott?  Ha a tanúsítványok nincsenek szinkronban, vagy ha azt gyanítja, hogy módosult a VNet konfigurációja, amely nem lett szinkronizálva a ASP, akkor nyomja meg a "Sync Network" (hálózat szinkronizálása) lehetőséget.
* Ha a VPN-en halad át, a helyszíni átjáró úgy van konfigurálva, hogy a forgalmat az Azure-ba irányítsa? Ha a VNet a végpontokat is elérheti, de a helyszínen nem, ellenőrizze az útvonalakat.
* olyan párhuzamos átjárót próbál használni, amely támogatja mind a pont-hely, mind a ExpressRoute? Az együttélési átjárók nem támogatottak a VNet-integrációval 

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


## <a name="automation"></a>Automatizálás

A regionális VNet-integráció CLI-támogatással rendelkezik. Az alábbi parancsokhoz való hozzáféréshez [telepítse az Azure CLI][installCLI]-t. 

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

Az átjáróhoz szükséges VNet-integrációhoz a PowerShell használatával integrálhatja App Serviceeit egy Azure-Virtual Network. A használatra kész parancsfájlhoz lásd: [alkalmazás Összekötése Azure app Service egy Azure-Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-appsetting.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


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
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
