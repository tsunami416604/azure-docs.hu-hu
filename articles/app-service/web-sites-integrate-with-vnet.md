---
title: Alkalmazás integrálása az Azure virtuális hálózatával
description: Alkalmazások integrálása az Azure App Service-ben az Azure virtuális hálózatokkal.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673225"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Alkalmazás integrálása egy Azure-beli virtuális hálózattal
Ez a dokumentum ismerteti az Azure App Service virtuális hálózati integrációs funkcióját, és azt, hogy hogyan állíthatja be az [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)alkalmazásaival. [Az Azure virtuális hálózatok][VNETOverview] (VNets) lehetővé teszi, hogy az Azure-erőforrások nagy részét egy nem internetes irányítható hálózatba helyezze.  

Az Azure App Service két változatban rendelkezik.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Virtuális hálózat integrációjának engedélyezése 

1. Nyissa meg a Hálózati felhasználói felületet az App Service-portálon. A Virtuálishálózat-integráció csoportban válassza a *"Kattintson ide a konfiguráláshoz"* lehetőséget. 

1. Válassza **a Virtuális hálózat hozzáadása**lehetőséget.  

   ![Virtuális hálózat-integráció kiválasztása][1]

1. A legördülő lista tartalmazza az összes Erőforrás-kezelő virtuális hálózatok az előfizetésben ugyanabban a régióban, és az alábbiakban, hogy egy listát az összes erőforrás-kezelő virtuális hálózatok az összes többi régióban. Válassza ki azt a virtuális hálózatot, amelyhez integrálni szeretne.

   ![Válassza ki a virtuális hálózatot][2]

   * Ha a virtuális hálózat ugyanabban a régióban van, akkor hozzon létre egy új alhálózatot, vagy válasszon egy üres, már meglévő alhálózatot. 

   * Ha egy másik régióban lévő virtuális hálózatot szeretne kijelölni, rendelkeznie kell egy virtuális hálózati átjáróval, amelyen a pont-hely engedélyezve van.

   * Ha klasszikus virtuális hálózattal szeretne integrálni, ahelyett, hogy a virtuális hálózat legördülő menüre kattintana, válassza a Kattintson ide lehetőséget **a klasszikus virtuális hálózathoz való csatlakozáshoz.** Válassza ki a kívánt klasszikus virtuális hálózatot. A cél virtuális hálózatnak már rendelkeznie kell egy virtuális hálózati átjáróval, amelyen a pont-hely engedélyezve van.

    ![Klasszikus virtuális hálózat kiválasztása][3]
    
Az integráció során az alkalmazás újraindul.  Az integráció befejezése után a virtuális hálózat on a virtuális hálózat, amely integrálva van. 

Miután az alkalmazás integrálva van a virtuális hálózattal, ugyanazt a DNS-kiszolgálót fogja használni, amelyhez a virtuális hálózat konfigurált, kivéve, ha az Azure DNS-beli privát zónák. Jelenleg nem használhatja a virtuális hálózat-integrációt az Azure DNS-hálózati zónákkal.

## <a name="regional-vnet-integration"></a>Regionális virtuális hálózatok integrációja

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>A regionális virtuális hálózatok integrálásának működése

Az App Service-ben lévő alkalmazások feldolgozói szerepkörökön vannak tárolva. Az alapszintű és a magasabb díjszabási csomagok dedikált tárhelycsomagok, ahol nincsenek más ügyfelek számítási feladatok fut ugyanazon a munkavállalókon. A regionális virtuális hálózat integrációja a delegált alhálózatban lévő címekkel rendelkező virtuális összeköttetések csatlakoztatásával működik. Mivel a honnan származó cím a virtuális hálózatban található, a virtuális hálózaton vagy a virtuális hálózaton keresztül a virtuális hálózaton lévő legtöbb dologhoz ugyanúgy hozzáférhet, mint a virtuális hálózat virtuális gépe. A hálózati megvalósítás más, mint egy virtuális gép futtatása a virtuális hálózatban, és ezért egyes hálózati funkciók még nem érhetők el a szolgáltatás használata közben.

![A regionális virtuális hálózatok integrációja működése][5]

Ha a regionális virtuális hálózat integrációja engedélyezve van, az alkalmazás továbbra is kimenő hívásokat az internetre ugyanazon a csatornán keresztül, mint a normál. Az alkalmazás tulajdonságaiportálon felsorolt kimenő címek továbbra is az alkalmazás által használt címek. Milyen változások az alkalmazás, a szolgáltatás végpont biztonságos szolgáltatások vagy RFC 1918 címek megy a virtuális hálózatba. Ha WEBSITE_VNET_ROUTE_ALL 1-re van állítva, akkor az összes kimenő forgalom elküldhető a virtuális hálózatba. 

A szolgáltatás dolgozónként csak egy virtuális felületet támogat.  Dolgozónként egy virtuális felület egy regionális virtuális hálózat-integrációt jelent az App Service-csomagonként. Az összes alkalmazás ugyanabban az App Service-csomagban használhatja ugyanazt a virtuális hálózat integráció, de ha szüksége van egy alkalmazás, hogy csatlakozzon egy további virtuális hálózathoz, létre kell hoznia egy másik App Service-csomag. A használt virtuális felület nem olyan erőforrás, amelyhez az ügyfelek közvetlen hozzáféréssel rendelkeznek.

A technológia működésének jellege miatt a virtuális hálózat integrációval használt forgalom nem jelenik meg a Network Watcher vagy az NSG folyamatnaplókban.  

## <a name="gateway-required-vnet-integration"></a>Átjáró szükséges virtuális hálózati integráció

Átjáró szükséges virtuális hálózat integrációja támogatja a virtuális hálózathoz való csatlakozást egy másik régióban, vagy egy klasszikus virtuális hálózathoz. Az átjáró szükséges virtuális hálózati integráció: 

* Lehetővé teszi, hogy egy alkalmazás egyszerre csak 1 virtuális hálózathoz csatlakozzon
* Lehetővé teszi, hogy legfeljebb öt virtuális hálózat integrálható legyen egy App Service-csomagba 
* Lehetővé teszi, hogy ugyanazt a virtuális hálózatot több alkalmazás használja egy App Service-csomagban anélkül, hogy befolyásolna az App Service-csomag által használható teljes számot.  Ha hat alkalmazás ugyanazt a virtuális hálózatot használja ugyanabban az App Service-csomagban, amely 1 virtuális hálózat használatban lévőnek számít. 
* Támogatja a 99,9%-os SLA-t az átjárón lévő SLA miatt
* Lehetővé teszi, hogy az alkalmazások a virtuális hálózat által használt DNS-t használják
* Az alkalmazáshoz való csatlakozáshoz az SSTP-pont-hely VPN-nel konfigurált virtuális hálózati útvonalalapú átjárószükséges. 

Nem használhatja az átjáró szükséges virtuális hálózat-integráció:

* Linux os alkalmazásokkal
* Az ExpressRoute-hoz csatlakoztatott virtuális hálózattal 
* A szolgáltatásvégpontok biztonságos erőforrásainak elérése
* Együttélési átjáróval, amely támogatja az ExpressRoute-ot, és a hely/hely a hely VPN-re mutat

### <a name="set-up-a-gateway-in-your-vnet"></a>Átjáró beállítása a virtuális hálózatban ###

Átjáró létrehozása:

1. [Hozzon létre egy átjáró-alhálózatot][creategatewaysubnet] a virtuális hálózatban.  

1. [Hozza létre a VPN-átjárót.][creategateway] Válasszon útvonalalapú VPN-típust.

1. [Állítsa be a pontot a helycímekre][setp2saddresses]. Ha az átjáró nem az alaptermékváltozatban található, akkor az IKEV2-t le kell tiltani a pont-hely konfigurációban, és ki kell jelölni az SSTP-t. A hely közötti címterületnek az 1918-as számú RFC-címblokkban kell lennie, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Ha csak az átjárót hozza létre az App Service virtuális hálózat integrációval való használatra, akkor nem kell feltöltenie egy tanúsítványt. Az átjáró létrehozása 30 percet is igénybe vehet. Az átjáró kiépítése előtt nem integrálhatja az alkalmazást a virtuális hálózattal. 

### <a name="how-gateway-required-vnet-integration-works"></a>Az átjáró szükséges virtuális hálózati integrációjának működése

Az átjáró szükséges virtuális hálózati integráció, amely a ponttól a helyig vpn-technológián alapul. Pont a helyHEZ VPN korlátozza a hálózati hozzáférést csak az alkalmazást üzemeltető virtuális gépre. Az alkalmazások csak az internetre, hibrid kapcsolatokon vagy virtuális hálózatok integráción keresztül küldhetnek forgalmat. Ha az alkalmazás úgy van konfigurálva a portállal, hogy az átjáró szükséges virtuális hálózati integrációt használjon, az Ön nevében egy összetett egyeztetést kezel a rendszer az átjárón és az alkalmazásoldalon lévő tanúsítványok létrehozásához és hozzárendeléséhez. A végeredmény az, hogy az alkalmazások üzemeltetéséhez használt dolgozók képesek közvetlenül csatlakozni a virtuális hálózati átjáróhoz a kiválasztott virtuális hálózatban. 

![Az átjáró szükséges virtuális hálózati integrációjának működése][6]

### <a name="accessing-on-premises-resources"></a>Helyszíni erőforrások elérése

Az alkalmazások a helyek közötti kapcsolattal rendelkező virtuális hálózatokkal való integrációval érhetik el a helyszíni erőforrásokat. Ha az átjáró szükséges virtuális hálózati integráció, frissítenie kell a helyszíni VPN-átjáró útvonalak a pont-hely címblokkok. A helyek közötti VPN első beállításakor a konfiguráláshoz használt parancsfájlok nak megfelelően kell beállítaniuk az útvonalakat. Ha a helyek közötti VPN létrehozása után adja hozzá a pont-hely címeket, manuálisan kell frissítenie az útvonalakat. Részleteket, hogyan kell csinálni, hogy változik az átjáró, és nem itt ismertetésre. A BGP nem konfigurálható helyek közötti VPN-kapcsolattal.

Nincs szükség további konfigurációra a regionális virtuális hálózat integrációs szolgáltatás a virtuális hálózaton keresztül, és a helyszíni eléréséhez. Egyszerűen csak csatlakoztatnia kell a virtuális hálózatot a helyszíni ExpressRoute vagy egy hely közötti VPN használatával. 

> [!NOTE]
> Az átjáró szükséges Virtuálishálózati integrációs szolgáltatás nem integrálja az alkalmazást egy ExpressRoute-átjáróval rendelkező virtuális hálózattal. Még akkor is, ha az ExpressRoute-átjáró [együttélési módban][VPNERCoex] van konfigurálva, a virtuális hálózat integrációja nem működik. Ha egy ExpressRoute-kapcsolaton keresztül kell erőforrásokat elérnie, akkor használhatja a regionális virtuális hálózati integrációs szolgáltatást vagy egy [App Service-környezetet,][ASE]amely a virtuális hálózaton fut. 
> 
> 

### <a name="peering"></a>Társviszony-létesítés

Ha a társviszony-létesítésa a regionális virtuális hálózat integráció, nem kell semmilyen további konfigurációt. 

Ha a szükséges virtuális hálózat-integrációt használja a társviszony-létesítéssel, néhány további elemet kell konfigurálnia. A társviszony-létesítés konfigurálása az alkalmazással való együttműködésre:

1. Vegyen fel egy társviszony-létesítési kapcsolatot a virtuális hálózaton, amelyhez az alkalmazás csatlakozik. A társviszony-létesítési kapcsolat hozzáadásakor engedélyezze a **virtuális hálózati hozzáférés engedélyezése,** és jelölje be a továbbított forgalom engedélyezése és **az átjáró-átvitel engedélyezése** **jelölőnégyzetet.**
1. Vegyen fel egy társviszony-létesítési kapcsolatot a virtuális hálózaton, amely a virtuális hálózathoz csatlakozik. Amikor hozzáadja a társviszony-létesítési kapcsolatot a célvirtuális hálózathoz, engedélyezze a **virtuális hálózati hozzáférés engedélyezése,** és jelölje be **a Továbbított forgalom engedélyezése** és a **Távoli átjárók engedélyezése jelölőnégyzetet.**
1. Nyissa meg az App Service-csomag > hálózati > virtuálishálózati integrációs felhasználói felületét a portálon.  Válassza ki azt a virtuális hálózatot, amelyhez az alkalmazás csatlakozik. Az útválasztási szakaszban adja hozzá a virtuális hálózat címtartományát, amely az alkalmazáshoz csatlakozik.  

## <a name="managing-vnet-integration"></a>A virtuális hálózatok integrációjának kezelése 

A virtuális hálózathoz való csatlakozás és a kapcsolat bontása alkalmazásszinten történik. Műveletek, amelyek hatással lehetnek a virtuális hálózat integrációja több alkalmazás on the App Service csomag szintjén. Az alkalmazás > hálózati > virtuális hálózati integrációs portálon, a virtuális hálózat adatait is megkaphatja. Hasonló információkat az ASP szintjén az ASP > Networking > VNet-integrációs portálon láthatja.

Az egyetlen művelet, amelyet a virtuális hálózat-integráció alkalmazásnézetében az alkalmazás leválasztása a virtuális hálózatról, amelyhez jelenleg csatlakozik. Ha le szeretné választani az alkalmazást a virtuális hálózatról, válassza a **Kapcsolat bontása**lehetőséget. Az alkalmazás újraindul, amikor leválasztja a virtuális hálózatot. A kapcsolat bontása nem módosítja a virtuális hálózatot. Az alhálózat vagy az átjáró nem törlődik. Ha ezután törölni szeretné a virtuális hálózatot, először le kell választania az alkalmazást a virtuális hálózatról, és törölnie kell a benne lévő erőforrásokat, például az átjárókat. 

Az ASP virtuális hálózat integrációs felhasználói felülete megmutatja az ASP-ben lévő alkalmazások által használt összes virtuális hálózat-integrációt. Az egyes virtuális hálózatok részleteinek megtekintéséhez kattintson az önt érdeklő virtuális hálózatra. Az átjáró szükséges virtuális hálózati integrációhoz két műveletet hajthat végre.

* **Hálózat szinkronizálása**. A szinkronizálási hálózati művelet csak az átjáró-függő virtuális hálózat integrációs szolgáltatás. A szinkronizálási hálózati művelet biztosítja, hogy a tanúsítványok és a hálózati adatok szinkronban legyenek. Ha hozzáadja vagy módosítja a virtuális hálózat DNS-ét, **szinkronizálnia** kell a hálózati műveletet. Ez a művelet újraindítja a virtuális hálózatot használó alkalmazásokat.
* **Útvonalak hozzáadása** Útvonalak hozzáadása hajt kimenő forgalmat a virtuális hálózatba. 

**Átjáró szükséges virtuálishálózati integrációs útválasztás** A virtuális hálózatban definiált útvonalak az alkalmazásból irányítják a virtuális hálózatba irányuló forgalmat. Ha további kimenő forgalmat kell küldenie a virtuális hálózatba, akkor itt hozzáadhatja ezeket a címblokkokat. Ez a funkció csak a szükséges virtuális hálózat integrációja átjáróval működik. Az útvonaltáblák nem befolyásolják az alkalmazás forgalmát, ha átjáró szükséges virtuális hálózati integrációt használ, ahogy a regionális virtuális hálózat-integrációval.

**Az átjáró szükséges virtuális hálózati integrációs tanúsítványok** Ha az átjáró szükséges Virtuálishálózati integráció engedélyezve van, a kapcsolat biztonságának biztosítása érdekében szükség van a tanúsítványok cseréjére. A tanúsítványok mellett a DNS-konfiguráció, útvonalak és más hasonló dolgok, amelyek leírják a hálózatot.
Ha a tanúsítványok vagy a hálózati adatok módosulnak, a "Hálózat szinkronizálása" gombra kell kattintania. Ha a "Hálózat szinkronizálása" gombra kattint, rövid kimaradást okoz az alkalmazás és a virtuális hálózat közötti kapcsolatban. Amíg az alkalmazás nem indul újra, a kapcsolat elvesztése a webhely nem megfelelő működését okozhatja. 

## <a name="pricing-details"></a>Díjszabás részletei
A regionális virtuális hálózat integrációs funkció nem rendelkezik további díjat az ASP tarifacsomag-díjakon túli használatra.

Az átjáró használatához szükséges vnet-integrációs szolgáltatás három kapcsolódó díjat számít fel:

* ASP tarifacsomag díjai – Az alkalmazásoknak standard, prémium vagy PremiumV2 Alkalmazásszolgáltatási csomagban kell lenniük. Ezekről a költségekről további részleteket itt láthat: [App Service Pricing][ASPricing]. 
* Adatátviteli költségek – Az adatok kimenő forgalom, akkor is, ha a virtuális hálózat ugyanabban az adatközpontban van. Ezeket a díjakat az [adatátviteli díjszabás részletei ismertetik.][DataPricing] 
* VPN-átjáró költségek – A virtuális hálózat átjárója, amely szükséges a pont-hely VPN. A részletek a [VPN-átjáró díjszabási][VNETPricing] oldalán találhatók.

## <a name="troubleshooting"></a>Hibaelhárítás

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

Cli támogatás a regionális virtuális hálózat integrációja. A következő parancsok eléréséhez telepítse az [Azure CLI-t.][installCLI] 

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

Az átjáró szükséges Virtuálishálózat-integráció, integrálhatja az App Service egy Azure virtuális hálózat powershell használatával. A használatra kész parancsfájlról az [Alkalmazás csatlakoztatása az Azure App Service-ben egy Azure virtuális hálózathoz (Connect a.](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)


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
