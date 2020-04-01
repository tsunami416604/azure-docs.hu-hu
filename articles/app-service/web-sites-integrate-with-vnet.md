---
title: Alkalmazások integrálása az Azure virtuális hálózatával
description: Integrálja az alkalmazásokat az Azure App Service-ben az Azure virtuális hálózataival.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a1a9739c444db2e41d55b8876011c066f2e71ca3
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421370"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Az alkalmazás integrálása az Azure virtuális hálózatával

Ez a cikk ismerteti az Azure App Service virtuális hálózat integrációs funkcióját, és azt, hogy miként állíthatja be az [Azure App Service-beli](https://go.microsoft.com/fwlink/?LinkId=529714)alkalmazásokkal. Az [Azure Virtual Network][VNETOverview]segítségével számos Azure-erőforrást nem internetes irányítható hálózatba helyezhet.

Az Azure App Service két változatban:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Virtuális hálózat integrációjának engedélyezése

1. Nyissa meg a Hálózati felhasználói **felületet** az App Service-portálon. A **Virtuálishálózat-integráció**csoportban válassza **a Kattintson ide lehetőséget a beállításhoz.**

1. Válassza **a Virtuális hálózat hozzáadása**lehetőséget.

   ![Virtuális hálózat-integráció kiválasztása][1]

1. A legördülő lista tartalmazza az összes Azure Resource Manager virtuális hálózatok az előfizetés ugyanabban a régióban. Alatta az erőforrás-kezelő i virtuális hálózatainak listája látható az összes többi régióban. Válassza ki azt a virtuális hálózatot, amelybe integrálni szeretné.

   ![A virtuális hálózat kiválasztása][2]

   * Ha a virtuális hálózat ugyanabban a régióban található, hozzon létre egy új alhálózatot, vagy válasszon ki egy üres, már létező alhálózatot.
   * Egy másik régióban lévő virtuális hálózat kiválasztásához rendelkeznie kell egy virtuális hálózati átjárókiépítéssel, amelyen a pont-hely engedélyezve van.
   * Ha klasszikus virtuális hálózattal szeretne integrálódni, a **Virtuális hálózat** legördülő lista kiválasztása helyett válassza a Kattintson ide lehetőséget **a klasszikus virtuális hálózathoz való csatlakozáshoz.** Válassza ki a kívánt klasszikus virtuális hálózatot. A célvirtuális hálózatnak már rendelkeznie kell olyan virtuális hálózati átjáróval, amelyen engedélyezve van a pont-hely.

    ![Klasszikus virtuális hálózat kiválasztása][3]

Az integráció során az alkalmazás újraindul. Amikor az integráció befejeződött, látni fogja a részleteket a virtuális hálózaton, amelybe integrálva van.

Miután az alkalmazás integrálva van a virtuális hálózattal, ugyanazt a DNS-kiszolgálót használja, amelyhez a virtuális hálózat van konfigurálva, kivéve, ha az Azure DNS-hálózati zónák. Jelenleg nem használhatja a virtuális hálózat-integrációt az Azure DNS-hálózati zónákkal.

## <a name="regional-vnet-integration"></a>Regionális virtuális hálózatok integrációja

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>A regionális virtuális hálózatok integrációja működése

Az App Service-ben lévő alkalmazások feldolgozói szerepkörökön vannak tárolva. Az alapszintű és a magasabb díjszabási csomagok dedikált tárhelycsomagok, ahol nincsenek más ügyfelek számítási feladatai ugyanazon a dolgozókon. A regionális virtuális hálózat integrációja a delegált alhálózatban lévő címekkel rendelkező virtuális összeköttetések csatlakoztatásával működik. Mivel a from address a virtuális hálózatban van, a virtuális hálózaton vagy a virtuális hálózaton keresztül a legtöbb dolog elérhető, mint ha a virtuális hálózat virtuális gépe tenné. A hálózati megvalósítás más, mint a virtuális gép futtatása a virtuális hálózatban. Ezért egyes hálózati funkciók még nem érhetők el ehhez a funkcióhoz.

![A regionális virtuális hálózatok integrációja működése][5]

Ha a regionális virtuális hálózat integrációja engedélyezve van, az alkalmazás kimenő hívásokat kezdeményez az internetre ugyanazon a csatornán keresztül, mint a normál. Az alkalmazás tulajdonságaiportálon felsorolt kimenő címek azok a címek, amelyeket az alkalmazás továbbra is használ. Milyen változások at az alkalmazás a hívások szolgáltatás végpont biztonságos szolgáltatások, vagy RFC 1918 címek bemegy a virtuális hálózatba. Ha WEBSITE_VNET_ROUTE_ALL 1-re van állítva, az összes kimenő forgalom elküldhető a virtuális hálózatba.

A szolgáltatás dolgozónként csak egy virtuális felületet támogat. Dolgozónként egy virtuális felület egy regionális virtuális hálózat-integrációt jelent az App Service-csomagonként. Az összes alkalmazás ugyanabban az App Service-csomagban használhatja ugyanazt a virtuális hálózat-integráció. Ha egy másik virtuális hálózathoz való csatlakozáshoz szüksége van egy alkalmazásra, létre kell hoznia egy másik App Service-csomagot. A használt virtuális felület nem olyan erőforrás, amelyhez az ügyfelek közvetlen hozzáféréssel rendelkeznek.

A technológia működésének jellege miatt a virtuális hálózat-integrációval használt forgalom nem jelenik meg az Azure Network Watcher vagy az NSG folyamatnaplóiban.

## <a name="gateway-required-vnet-integration"></a>Átjáróhoz szükséges virtuális hálózat integrációja

Az átjáróáltal igényelt virtuális hálózati integráció támogatja a virtuális hálózathoz való csatlakozást egy másik régióban vagy egy klasszikus virtuális hálózathoz. Átjáró által igényelt virtuális hálózat integrációja:

* Lehetővé teszi, hogy egy alkalmazás egyszerre csak egy virtuális hálózathoz csatlakozzon.
* Lehetővé teszi, hogy legfeljebb öt virtuális hálózat integrálható legyen egy App Service-csomagba.
* Lehetővé teszi, hogy ugyanazt a virtuális hálózatot több alkalmazás használja egy App Service-csomagban anélkül, hogy befolyásolná az App Service-csomag által használható teljes számot. Ha hat alkalmazás használja ugyanazt a virtuális hálózatot ugyanabban az App Service-csomagban, amely egy használatban lévő virtuális hálózatnak számít.
* Támogatja a 99,9%-os SLA miatt az SLA az átjárón.
* Lehetővé teszi, hogy az alkalmazások azt a DNS-t használják, amelyhez a virtuális hálózat konfigurálva van.
* Az alkalmazáshoz való csatlakozáshoz szükséges az SSTP-pont-hely VPN-nel konfigurált virtuális hálózati útvonalalapú átjáró szükséges.

Az átjáróáltal igényelt virtuális hálózat-integráció nem használható:

* Linux-alkalmazásokkal.
* Az Azure ExpressRoute-hoz csatlakoztatott virtuális hálózattal.
* A szolgáltatásvégpont biztonságos erőforrásainak elérése.
* Olyan együttélési átjáróval, amely támogatja az ExpressRoute és a pont-hely vagy a helyek közötti VPN-eket is.

### <a name="set-up-a-gateway-in-your-virtual-network"></a>Átjáró beállítása a virtuális hálózatban ###

Átjáró létrehozása:

1. [Hozzon létre egy átjáró alhálózatot][creategatewaysubnet] a virtuális hálózatban.  

1. [Hozza létre a VPN-átjárót.][creategateway] Válasszon útvonalalapú VPN-típust.

1. [Állítsa be a pont-hely címeket][setp2saddresses]. Ha az átjáró nem az alaptermékváltozatban található, akkor az IKEV2-t le kell tiltani a pont-hely konfigurációban, és ki kell jelölni az SSTP-t. A helyek közötti címterületnek a 1918-as Számú RFC-címblokk 10.0.0/8, 172.16.0.0/12 és 192.168.0.0/16.

Ha az átjárót az App Service virtuális hálózat-integrációval való használatra hozza létre, nem kell tanúsítványt feltöltenie. Az átjáró létrehozása 30 percet is igénybe vehet. Az átjáró kikiépítéséig nem integrálhatja az alkalmazást a virtuális hálózattal.

### <a name="how-gateway-required-vnet-integration-works"></a>Az átjáró által igényelt virtuális hálózat integrációjának működése

Az átjáróáltal igényelt virtuális hálózat-integráció a pont-hely VPN technológiára épül. A pont-hely VPN-ek korlátozzák az alkalmazást tároló virtuális gép hálózati hozzáférését. Az alkalmazások csak hibrid kapcsolatokon vagy virtuális hálózatok integráción keresztül küldhetnek forgalmat az internetre. Ha az alkalmazás úgy van konfigurálva a portállal, hogy az átjáróáltal igényelt virtuális hálózati integrációt használjon, az Ön nevében egy összetett egyeztetést kezel nek az átjárón és az alkalmazásoldalon lévő tanúsítványok létrehozásához és hozzárendeléséhez. Az eredmény az, hogy az alkalmazások üzemeltetéséhez használt dolgozók közvetlenül csatlakozhatnak a kiválasztott virtuális hálózat virtuális hálózati átjárójához.

![Az átjáró által igényelt virtuális hálózat integrációjának működése][6]

### <a name="access-on-premises-resources"></a>Helyszíni erőforrások elérése

Az alkalmazások a helyek közötti kapcsolattal rendelkező virtuális hálózatokkal való integrációval érhetik el a helyszíni erőforrásokat. Ha átjáró által igényelt virtuális hálózat-integrációt használ, frissítse a helyszíni VPN-átjáró útvonalakat a pont-hely címblokkokkal. A helyek közötti VPN első beállításakor a konfiguráláshoz használt parancsfájlok nak megfelelően kell beállítaniuk az útvonalakat. Ha a helyek közötti VPN létrehozása után adja hozzá a pont-hely címeket, manuálisan kell frissítenie az útvonalakat. Ennek az átjárónkénti eltérő részletei itt nem ismertetésre. A BGP nem konfigurálható helyek közötti VPN-kapcsolattal.

Nincs szükség további konfigurációra ahhoz, hogy a regionális virtuális hálózat integrációs szolgáltatás a virtuális hálózaton keresztül eljusson a helyszíni erőforrásokhoz. Egyszerűen csak csatlakoztatnia kell a virtuális hálózatot a helyszíni erőforrásokhoz az ExpressRoute vagy a helyek közötti VPN használatával.

> [!NOTE]
> Az átjáró által igényelt virtuális hálózati integrációs szolgáltatás nem integrálja az alkalmazást egy ExpressRoute-átjáróval rendelkező virtuális hálózattal. Még akkor is, ha az ExpressRoute-átjáró [együttélési módban][VPNERCoex]van konfigurálva, a virtuális hálózat integrációja nem működik. Ha ExpressRoute-kapcsolaton keresztül kell erőforrásokat elérnie, használja a regionális virtuális hálózati integrációs szolgáltatást vagy egy [App Service-környezetet,][ASE]amely a virtuális hálózatban fut.
> 
> 

### <a name="peering"></a>Társviszony-létesítés

Ha a regionális virtuális hálózat-integrációval társviszony-létesítést használ, nem kell további konfigurációt végeznie.

Ha átjáró által igényelt virtuális hálózat integrációtárs-létesítés, konfigurálnia kell néhány további elemeket. A társviszony-létesítés konfigurálása az alkalmazással való együttműködésre:

1. Társviszony-létesítési kapcsolat hozzáadása azon a virtuális hálózaton, amelyhez az alkalmazás csatlakozik. A társviszony-létesítési kapcsolat hozzáadásakor engedélyezze a **virtuális hálózati hozzáférés engedélyezése lehetőséget,** és válassza a Továbbított forgalom **engedélyezése** és **az átjáró-átvitel engedélyezése lehetőséget.**
1. Vegyen fel egy társviszony-létesítési kapcsolatot a virtuális hálózaton, amely a virtuális hálózathoz csatlakozik. Amikor hozzáadja a társviszony-létesítési kapcsolatot a célvirtuális hálózaton, engedélyezze a **virtuális hálózati hozzáférés engedélyezése lehetőséget,** és válassza a Továbbított forgalom **engedélyezése** és a **Távoli átjárók engedélyezése lehetőséget.**
1. Nyissa > meg az App **Service-csomag** > **hálózati****virtuálishálózat-integrációs** felhasználói felületét a portálon. Válassza ki azt a virtuális hálózatot, amelyhez az alkalmazás csatlakozik. Az útválasztási szakaszban adja hozzá annak a virtuális hálózatnak a címtartományát, amely az alkalmazáshoz csatlakozik.

## <a name="manage-vnet-integration"></a>Virtuális hálózat integrációjának kezelése

A virtuális hálózathoz való csatlakozás és a kapcsolat bontása alkalmazásszinten történik. A virtuális hálózatok több alkalmazás közötti integrációját befolyásoló műveletek az App Service-csomag szintjén vannak. Az alkalmazás > **hálózati** > **virtuálishálózati integrációs** portál, a virtuális hálózat részleteit kaphat. Hasonló információkat az App Service-csomag > szintjén az App **Service-csomag** > **hálózati****vnet-integrációs** portálon láthatja.

Az egyetlen művelet, amelyet a VNet-integrációs példány alkalmazásnézetében elvihet, az alkalmazás leválasztása a virtuális hálózatról, amelyhez jelenleg csatlakozik. Ha le szeretné választani az alkalmazást a virtuális hálózatról, válassza a **Kapcsolat bontása**lehetőséget. Az alkalmazás újraindul, amikor bontja a kapcsolatot egy virtuális hálózattal. A kapcsolat bontása nem változtatja meg a virtuális hálózatot. Az alhálózat vagy az átjáró nem törlődik. Ha ezután törölni szeretné a virtuális hálózatot, először válassza le az alkalmazást a virtuális hálózatról, és törölje a benne lévő erőforrásokat, például az átjárókat.

Az App Service-csomag virtuális hálózat integrációs felhasználói felülete megmutatja az app szolgáltatáscsomagban az alkalmazások által használt összes virtuális hálózati integrációt. Az egyes virtuális hálózatok részleteinek megtekintéséhez válassza ki az önt érdeklő virtuális hálózatot. Az átjárók által igényelt virtuális hálózat-integrációhoz itt két műveletet hajthat végre:

* **Szinkronizálási hálózat**: A szinkronizálási hálózati művelet csak az átjárófüggő virtuális hálózat integrációs szolgáltatásához használatos. A szinkronizálási hálózati művelet biztosítja, hogy a tanúsítványok és a hálózati adatok szinkronban legyenek. Ha hozzáadja vagy módosítja a virtuális hálózat DNS-ét, hajtson végre szinkronizálási hálózati műveletet. Ez a művelet újraindítja a virtuális hálózatot használó alkalmazásokat.
* **Útvonalak hozzáadása:** Útvonalak hozzáadása kimenő forgalmat generál a virtuális hálózatba.

### <a name="gateway-required-vnet-integration-routing"></a>Átjáróáltal igényelt virtuális hálózati integrációs útválasztás
A virtuális hálózatban definiált útvonalak segítségével irányíthatja a forgalmat a virtuális hálózatba az alkalmazásból. További kimenő forgalom küldése a virtuális hálózatba, adja hozzá ezeket a címblokkokat itt. Ez a funkció csak átjáróáltal igényelt virtuális hálózat-integrációval működik. Az útvonaltáblák nem befolyásolják az alkalmazás forgalmát, ha átjáróáltal igényelt virtuális hálózati integrációt használ, ahogy a regionális virtuális hálózat-integrációval teszik.

### <a name="gateway-required-vnet-integration-certificates"></a>Átjáróhoz szükséges virtuális hálózati integrációs tanúsítványok
Ha az átjáró által igényelt virtuális hálózat integrációja engedélyezve van, a kapcsolat biztonságának biztosítása érdekében szükség van a tanúsítványok cseréjére. A tanúsítványok mellett a DNS-konfiguráció, útvonalak és más hasonló dolgok, amelyek leírják a hálózatot.

Ha a tanúsítványok vagy a hálózati adatok módosultak, válassza **a Hálózat szinkronizálása**lehetőséget. Ha a Hálózat szinkronizálása lehetőséget **választja,** rövid kimaradást okoz az alkalmazás és a virtuális hálózat közötti kapcsolatban. Amíg az alkalmazás nem indul újra, a kapcsolat elvesztése a webhely nem megfelelő működését okozhatja.

## <a name="pricing-details"></a>Díjszabás részletei
A regionális virtuális hálózat integrációs funkció nem rendelkezik további díjat az App Service-csomag tarifacsomag-díjakon túl.

Az átjáróhoz szükséges Virtuálishálózat-integráció funkció használatához három díj kapcsolódik:

* **App Service-csomag díjszabási díja:** Az alkalmazásoknak standard, prémium vagy PremiumV2 App Service-csomagban kell lenniük. Ezekről a költségekről további információt az [App Service díjszabása][ASPricing]című témakörben talál.
* **Adatátviteli költségek**: Az adatforgalom díja akkor is, ha a virtuális hálózat ugyanabban az adatközpontban van. Ezeket a díjakat az [adatátviteli díjak részletei][DataPricing]ismertetik.
* **VPN-átjáró költségek:** A virtuális hálózati átjáró nak olyan költsége van, amely a pont-hely VPN-hez szükséges. További információ: [VPN-átjáró díjszabása][VNETPricing].

## <a name="troubleshooting"></a>Hibaelhárítás

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

A CLI-támogatás a regionális virtuális hálózat-integrációhoz érhető el. A következő parancsok eléréséhez telepítse az Azure CLI .to access the following commands, [install the Azure CLI][installCLI].

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

Az átjáróáltal igényelt virtuális hálózat integrációja esetén integrálhatja az App Service-t egy Azure virtuális hálózattal a PowerShell használatával. A használatra kész parancsfájlról az [Alkalmazás csatlakoztatása az Azure App Service-ben egy Azure virtuális hálózathoz .for](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)a ready-to-run script, for Connect an app in a Azure App Service.


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
