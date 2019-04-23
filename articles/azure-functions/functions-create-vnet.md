---
title: Az Azure Functions integrálható az Azure-beli virtuális hálózathoz
description: Részletes útmutató, amely bemutatja, hogyan csatlakozhat Azure-beli virtuális hálózathoz függvény
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 96ab479d3373eb6e575a00898f7007a4df252e39
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002791"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Függvényalkalmazás integrálása az Azure-beli virtuális hálózathoz

Az oktatóanyag bemutatja, hogyan lehet egy Azure virtuális hálózatban lévő erőforrások eléréséhez az Azure Functions használatával.

A jelen oktatóanyag esetében egy WordPress-webhely, amely nem érhető el az internetről érkező virtuális hálózatban lévő virtuális gépen helyezünk üzembe. Majd az interneten és a virtuális hálózati hozzáféréssel rendelkező függvény helyezünk üzembe. Ez a függvény használjuk arra, hogy elérje a virtuális hálózaton belül üzembe helyezett WordPress webhely erőforrásokat.

A rendszer működésével kapcsolatos további információkért hibaelhárítás és speciális konfiguráció megtekintéséhez [az alkalmazás integrálása az Azure-beli virtuális hálózathoz](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). A prémium szintű csomag az Azure functions a web apps szolgáltatásban, megegyező üzemeltetési funkciókat rendelkezik, így a kívánt funkciókat és korlátozásokat a cikk a alkalmazni funkciók.

## <a name="topology"></a>Topológia

 ![Virtuális hálózati integráció felhasználói felülete][1]

## <a name="create-a-vm-inside-a-virtual-network"></a>Egy virtuális hálózatban lévő virtuális gép létrehozása

Először létrehozunk egy előre konfigurált virtuális Gépet, amely a WordPress egy virtuális hálózaton belül. 

A virtuális gép WordPress azért választottunk, mert a közé tartozik a legkevésbé költséges erőforrások, a virtuális hálózatokon belüli telepíthető. Vegye figyelembe, hogy ebben a forgatókönyvben minden erőforrás egy virtuális hálózatban, például REST API-k, App Service Environment-környezetek és más Azure-szolgáltatásokkal is együttműködik.

1. Nyissa meg az Azure Portalt.
2. Adjon hozzá egy új erőforrást nyissa meg a **erőforrás létrehozása** panelen.
3. Keresse meg "[WordPress LEMP7 maximális teljesítmény, a CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)" és a hozzá tartozó létrehozása panel megnyitásához. 
4. Az a **alapjai** lapra, konfigurálja a virtuális Gépet a következő információkat:
    1. Hozzon létre egy új erőforráscsoportot, hogy egyszerűbb az oktatóanyag végén található erőforrások karbantartása a virtuális gép számára. Itt "Függvény-VNET-Tutorial" adatokat használjuk példaként.
    1. Adjon meg egy egyedi nevet a virtuális gépet. "A virtuális hálózat – Wordpress" használjuk példaként.
    1. Válassza ki az Önhöz legközelebbi régiót.
    1. Válassza ki a méretet, B1s (1 vCPU, 1 GB memória).
    1. A rendszergazdai fiók válassza ki a jelszó-hitelesítést, és adja meg az egyedi felhasználónévvel és jelszóval. A jelen oktatóanyag esetében nem kell bejelentkezni a virtuális Gépet, ha hibaelhárításkor.
    
        ![Alapvető beállítások lapon a virtuális gép létrehozása](./media/functions-create-vnet/create-vm-1.png)

1. Helyezze át a **hálózatkezelés** lapra, és adja meg a következőket:
    1.  Hozzon létre egy új virtuális hálózatot.
    1.  Adja meg a címtartományt és a egy alhálózatot a címtartományán belül. Az alhálózat méretét határozza meg az App Service-csomagot is használhat, hogy hány virtuális gépet. Ha IP-címzést és alhálózatok nem ismeri a, van egy [dokumentum, amely megismerteti az alapokkal](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). IP-címkezelés és alhálózatok fontosak ebben a forgatókönyvben, ezért azt javasoljuk, hogy néhány cikkekben, és tekintse meg néhány videóit, logikus, amíg. 
    
        Ebben a példában azt Ön engedélyezés 10.10.1.0/24 alhálózatához a 10.10.0.0/16 hálózatot használja. Folyamatban van túlzott és egy /16 használatával alhálózati mivel könnyen kiszámítása, mely alhálózatok érhetők el a 10.10.0.0/16 hálózatban.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. Lépjen vissza a **hálózatkezelés** lapon, a nyilvános IP-cím beállítva **None**. Ebben a lépésben telepíti a virtuális Gépet a hozzáférés csak a virtuális hálózathoz.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. A virtuális gép létrehozásához. A folyamat körülbelül 5 percet vesz igénybe.
8. A virtuális gép létrehozása után nyissa meg a **hálózatkezelés** lapra, és jegyezze fel a magánhálózati IP-cím későbbi használatra. A virtuális gép nem rendelkezhet egy nyilvános IP-címet.

    ![14]

Most már teljes mértékben a virtuális hálózaton belül üzembe helyezett egy WordPress-webhelyet. Ez a hely nem érhető el a nyilvános interneten keresztül.

## <a name="create-a-function-app-in-a-premium-plan"></a>Egy függvényalkalmazás létrehozása egy prémium szintű csomag

A következő lépés, hogy egy függvényalkalmazás létrehozása egy prémium szintű csomag. Prémium szintű csomagot elérhetővé teszi a kiszolgáló nélküli méretezés egy dedikált App Service-csomag minden előnyét. A Használatalapú csomag segítségével létrehozott függvényalkalmazás nem támogatják a virtuális hálózat integrációja.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-virtual-network"></a>A függvényalkalmazás csatlakoztatása a virtuális hálózathoz

A WordPress-webhely üzemeltetése a virtuális hálózaton található fájlokat mostantól kapcsolódhat a függvényalkalmazás a virtuális hálózathoz.

1.  Válassza ki az előző lépésben a függvényalkalmazáshoz a portálon **platformfunkciók**. Válassza ki **hálózatkezelés**.

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  Válassza ki **kattintson ide a konfiguráláshoz** alatt **VNet-integráció**.

    ![A hálózati szolgáltatás konfigurálásához állapota](./media/functions-create-vnet/Networking-1.png)

1. Válassza ki a virtuális hálózati integráció lapon **hozzáadása virtuális hálózathoz (előzetes verzió)**.

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  Hozzon létre egy új alhálózatot a függvény és használata App Service-csomag. Vegye figyelembe, hogy az alhálózat mérete korlátozza a virtuális gépeket adhat hozzá az App Service-csomag teljes száma. A virtuális hálózat automatikusan irányítja át a virtuális hálózaton, így nem számít, hogy a függvény szerepel-e egy másik alhálózatot a virtuális gépről az alhálózatok közötti forgalmat. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-virtual-network"></a>Hozzon létre egy függvényt, amely a virtuális hálózat olyan erőforráshoz fér hozzá

A függvényalkalmazás most már elérheti a WordPress-webhely a virtuális hálózathoz. Tehát, hogy a fájl eléréséhez, és szolgálja ki, a felhasználó számára a függvény használatával. Ebben a példában fogunk használni egy WordPress-webhely az API-t és a egy proxyként a hívó funkció, mivel korábban már könnyen és jelenítheti meg. 

Ugyanilyen könnyen használhatja egy virtuális hálózaton belül üzembe helyezett API. Kód, amely az API-hoz a virtuális hálózaton belül üzembe helyezett API-hívást hajt végre egy másik függvényt is használhatja. A virtuális hálózaton belül vannak üzembe helyezve egy SQL Server-példány egy teljes példa.

1. A portálon nyissa meg az előző lépésben a függvényalkalmazást.
1. Hozzon létre egy proxyt kiválasztásával **proxyk** > **+**.

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. Konfigurálja a proxykiszolgáló nevét és útvonalat. Ebben a példában használja a "/ rendszerkövetelményekben" útvonalként.
1. Adja meg a korábban a WordPress-webhely IP, és állítsa be **háttérkiszolgáló URL-cím** , `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

Most látogasson el a háttér-URL-CÍMÉT közvetlenül a egy új böngészőlapon való beillesztésével meg, ha az oldal kell időtúllépés. Ennek az az oka a WordPress-webhelye csatlakozik a virtuális hálózathoz, és nem az interneten. Illessze be a proxykiszolgáló URL-CÍMÉT a böngésző, ha a virtuális hálózaton belüli megjelennie (WordPress-webhelye lekért) üzem kép. 

A függvényalkalmazás az internetről és a virtuális hálózat is kapcsolódik. A proxy egy kérelem fogadása a nyilvános interneten keresztül, és ezután átjáróként mentén kérelmet továbbítja a virtuális hálózatban az egyszerű HTTP-proxyt. A proxy a választ, majd továbbítja a nyilvános interneten keresztül. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>További lépések

Prémium szintű csomagot futó függvények, web apps-csomagok PremiumV2 ugyanazon az alapul szolgáló App Service-ben infrastruktúrán osztozzon. A web apps a dokumentáció a prémium szintű csomag függvények vonatkozik.

* [További információ a hálózatkezelési funkciók beállításait](./functions-networking-options.md)
* [Olvassa el a függvények hálózat – gyakori kérdések](./functions-networking-faq.md)
* [További információ az Azure-beli virtuális hálózatok](../virtual-network/virtual-networks-overview.md)
* [További hálózati funkciókat és hozzáférés-vezérlés az App Service Environment-környezetek engedélyezése](../app-service/environment/intro.md)
* [Hibrid kapcsolatok segítségével csatlakozzon az egyes helyszíni erőforrásait tűzfal módosítás nélkül](../app-service/app-service-hybrid-connections.md)
* [További tudnivalók a Functions-proxyk](./functions-proxies.md)

<!--Image references -->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/create-function-app.png
[3]: ./media/functions-create-vnet/create-app-service-plan.png
[4]: ./media/functions-create-vnet/configure-vnet.png
[5]: ./media/functions-create-vnet/create-vm-1.png
[6]: ./media/functions-create-vnet/create-vm-2.png
[7]: ./media/functions-create-vnet/create-vm-2-1.png
[8]: ./media/functions-create-vnet/networking-1.png
[9]: ./media/functions-create-vnet/networking-2.png
[10]: ./media/functions-create-vnet/networking-3.png
[11]: ./media/functions-create-vnet/new-proxy.png
[12]: ./media/functions-create-vnet/create-proxy.png
[14]: ./media/functions-create-vnet/vm-networking.png
