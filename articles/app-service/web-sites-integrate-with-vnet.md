---
title: Alkalmazás integrálása az Azure Virtual Network
description: Azure App Service alkalmazások integrálása az Azure Virtual Networks szolgáltatással.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673225"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Az alkalmazás integrálása Azure-Virtual Network
Ez a dokumentum ismerteti a Azure App Service Virtual Network Integration funkciót, valamint azt, hogyan állíthatja be az alkalmazásokkal a [Azure app Serviceban](https://go.microsoft.com/fwlink/?LinkId=529714). Az [Azure Virtual Networks][VNETOverview] (virtuális hálózatok) lehetővé teszi, hogy számos Azure-erőforrást egy nem internetes útválasztású hálózaton helyezzen el.  

A Azure App Service két változattal rendelkezik.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

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

## <a name="regional-vnet-integration"></a>Regionális VNet-integráció

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>A regionális VNet-integráció működése

A App Serviceban lévő alkalmazások a feldolgozói szerepkörökben vannak tárolva. Az alapszintű és a magasabb díjszabási csomagok olyan dedikált üzemeltetési csomagok, amelyeken nincs más, ugyanazon dolgozón futó ügyfél-munkaterhelés. A regionális VNet-integráció úgy működik, hogy a virtuális adaptereket a meghatalmazott alhálózat címeivel csatlakoztatja. Mivel a feladó címe a VNet található, a VNet és a saját VNet is elérheti a legtöbb dolgot, ugyanúgy, mint egy virtuális gép a. A hálózatkezelés megvalósítása eltér a virtuális gép VNet való futtatástól, ezért a funkció használata során a hálózati szolgáltatások némelyike még nem érhető el.

![A regionális VNet-integráció működése][5]

Ha a regionális VNet-integráció engedélyezve van, az alkalmazás továbbra is a normál módon megegyező csatornákon keresztül hajtja végre a kimenő hívásokat az interneten. Az alkalmazás tulajdonságai portálon felsorolt kimenő címek továbbra is az alkalmazás által használt címek. Az alkalmazás változásai, a Service Endpoint Secure Services vagy az RFC 1918-címek meghívása a VNet. Ha WEBSITE_VNET_ROUTE_ALL értéke 1, akkor a rendszer minden kimenő forgalmat elküldhet a VNet. 

A szolgáltatás csak egy virtuális felületet támogat a munkavégzők esetében.  A munkavégzők egy virtuális felülete egy regionális VNet integrációt jelent App Service csomagon keresztül. Az azonos App Service tervben szereplő összes alkalmazás használhatja ugyanazt a VNet-integrációt, de ha egy alkalmazásnak egy további VNet való csatlakozásra van szüksége, létre kell hoznia egy másik App Service tervet. A használt virtuális felület nem erőforrás, amelyhez az ügyfeleknek közvetlen hozzáférésük van.

A technológia működésének jellegéből adódóan a VNet-integrációhoz használt forgalom nem jelenik meg Network Watcher vagy NSG flow-naplókban.  

## <a name="gateway-required-vnet-integration"></a>Átjáró szükséges VNet-integráció

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

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

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
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
