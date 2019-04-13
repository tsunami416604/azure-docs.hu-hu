---
title: Az Azure Functions integrálható az Azure-beli virtuális hálózathoz
description: Egy részletes oktatóanyag bemutatja, hogyan függvény csatlakozhat az Azure-beli virtuális hálózathoz
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: f8ddcbcb25dc45ee71304ffa1bc5c0c4d3751b61
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523732"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Függvényalkalmazás integrálása az Azure-beli virtuális hálózathoz

A részletes oktatóanyag bemutatja, hogyan csatlakozhat egy Azure virtuális Hálózatban található erőforrások az Azure Functions használatával.

Ebben az oktatóanyagban bevezetjük egy WordPress-webhelyet egy saját, nem – elérhető az internetről, virtuális hálózat egy virtuális gépen. Azt fogja majd függvény üzembe helyezése egy hozzáféréssel rendelkező az internethez és a VNET is. Ez a függvény használjuk a virtuális hálózaton belül üzembe helyezett WordPress webhely az erőforrások eléréséhez.

A rendszer működésével kapcsolatos további információkért a hibaelhárítás és a speciális konfigurációt, lásd a dokumentum [az alkalmazás integrálása az Azure Virtual Network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). A prémium szintű csomag az Azure Functions a web apps szolgáltatásban, megegyező üzemeltetési funkciókat rendelkezik, így a kívánt funkciókat és korlátozások is érvényesek funkciók.

## <a name="topology"></a>Topológia

 ![Felhasználói felület VNet-integráció][1]

## <a name="creating-a-vm-inside-of-a-vnet"></a>Egy virtuális hálózaton belüli virtuális gép létrehozása

Első lépésként, hozunk létre egy virtuális hálózaton belül Wordpress előre konfigurált virtuális gép. 

A virtuális gép WordPress lett választva, mert közé tartozik a legkevésbé költséges erőforrások, amely telepíthető egy virtuális hálózaton belül. Vegye figyelembe, hogy ebben a forgatókönyvben minden erőforrás egy virtuális hálózaton, például REST API-k, App Service Environment-környezetek és más Azure-szolgáltatásokkal is együttműködik.

1. Az Azure Portal megnyitása
2. Adjon hozzá egy új erőforrást az "Erőforrás létrehozása" panel megnyitásával
3. Keresse meg "[WordPress LEMP7 maximális teljesítmény, a CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)", és nyissa meg a létrehozási panelt 
4. A létrehozás panelen konfigurálja a virtuális Gépet a következő információkat:
    1. Hozzon létre egy új erőforráscsoportot, hogy egyszerűbb az oktatóanyag végén található erőforrások karbantartása a virtuális gép számára. E nevű, "Függvény-VNET-Tutorial" erőforráscsoport
    1. Adjon meg egy egyedi nevet a virtuális gépet. E nevű adatbányászatot "VNET-Wordpress"
    1. Válassza ki az Önhöz legközelebbi régiót
    1. Válassza ki a méretet, B1s (1 vcpu, 1 GB memória)
    1. A rendszergazdai fiók válassza ki a jelszó-hitelesítést, és adja meg az egyedi felhasználónévvel és jelszóval. A jelen oktatóanyag esetében nem kell a virtuális gép bejelentkezni, kivéve, ha a hibaelhárításkor.
    
        ![Virtuális gép alapvető beállítások lap létrehozása](./media/functions-create-vnet/create-vm-1.png)

1. Helyezze át a hálózat lapot, és adja meg a következőket:
    1.  Új virtuális hálózat létrehozása
    1.  Adja meg a kívánt magánhálózaticím-tartomány és az alhálózati címtartományt belül. Az alhálózat méretét határozza meg az App Service-csomagot is használhat, hogy hány virtuális gépet. IP-címzést és alhálózatok nem ismeri az Ön, van-e egy [dokumentum alapvető kiterjedő](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). IP-címkezelés és alhálózatok fontosak ebben a forgatókönyvben így I néhány cikkek olvasása és figyelése néhány videóit, amíg logikus javasolja. 
        1. Ebben a példában I választotta a 10.10.0.0/16 hálózati 10.10.1.0/24 alhálózatához. Overprovision, és egy /16 kiválasztott alhálózat mivel könnyen kiszámítása, mely alhálózatok érhetők el a 10.10.0.0/16 hálózatban.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. Vissza a hálózat lapon állítsa be a nyilvános IP-cím "Nincs." Ez telepíti a hozzáférést a virtuális gép csak a virtuális hálózathoz.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. A virtuális gép létrehozásához. Ekkor körülbelül 5 perc.
8. A virtuális gép létrehozása után látogasson el a hálózat lapot, és jegyezze fel a magánhálózati IP-cím későbbi használatra. A virtuális gép nem rendelkezhet egy nyilvános IP-címet.

    ![14]

Most kell a virtuális hálózaton belül teljes mértékben üzembe helyezett egy wordpress-webhelyet. Ez a hely nem érhető el a nyilvános interneten keresztül.

## <a name="create-a-premium-plan-function-app"></a>Prémium szintű csomag Függvényalkalmazás létrehozása

A következő lépés, hogy egy függvényalkalmazás létrehozása egy prémium szintű csomag. A prémium szintű csomag egy új ajánlat, amely számos lehetőséget kínál előnyöket, a dedikált App Service-csomag, kiszolgáló nélküli méretezés. Használatalapú csomag függvényalkalmazások nem támogatják a VNet-integráció.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-vnet"></a>A Függvényalkalmazás csatlakozhat a virtuális hálózat

A WordPress-webhelyet üzemeltető virtuális hálózat található fájlokat mostantól kapcsolódhat a függvényalkalmazás a virtuális hálózathoz.

1.  Válassza ki az előző lépésben a függvényalkalmazáshoz a portálon **platformfunkciók**, majd **hálózatkezelés**

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  Válassza ki **kattintson ide a konfiguráláshoz** a VNet-integráció

    ![Konfigurálja a hálózati funkció állapota](./media/functions-create-vnet/Networking-1.png)

1. Válassza ki a virtuális hálózatok közötti integráció lapon **hozzáadása virtuális hálózathoz (előzetes verzió)**

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  Hozzon létre egy új alhálózatot az függvény és az App Service-csomag használata. Vegye figyelembe, hogy az alhálózat mérete korlátozza a virtuális gépeket adhat hozzá az app service-csomag teljes száma. A virtuális hálózat automatikusan irányítja át a virtuális hálózatban, így nem számít, hogy a függvény szerepel-e egy másik alhálózatot a virtuális gépről az alhálózatok közötti forgalmat. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-vnet"></a>Függvény, amely hozzáfér a virtuális Hálózatban található erőforrás létrehozása

A Függvényalkalmazás most már elérhető a virtuális hálózat és a wordpress-webhely fogunk hozzáférni a fájlhoz, és szolgálja ki, a felhasználó számára a függvény használatával. Ebben a példában használjuk egy wordpress-webhelyet az API-t, és a egy függvény proxyként a hívó funkciókként, mivel mindkét egyszerűen állítsa be és jelenítheti meg. Ugyanilyen könnyen használhatja egy virtuális hálózaton üzembe helyezett összes többi API, és kódot generálunk API-t egy másik függvényt, hogy a virtuális hálózaton belül üzembe helyezett API-hívások. Egy SQL server, a virtuális hálózaton belül üzembe helyezett egy tökéletes példa.

1. A portálon nyissa meg a Függvényalkalmazás az előző lépésben
1. Hozzon létre egy proxyt kiválasztásával **proxyk** > **+**

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. Konfigurálja a proxykiszolgáló nevét és útvonalat. Saját útvonalként /plant kiválasztott.
1. Adja meg a korábban a wordpress-webhely IP, és a háttérkiszolgáló URL-cím beállítása `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

Most látogasson el a háttérkiszolgáló URL-CÍMÉT közvetlenül a egy új böngészőlapon való beillesztésével kísérli meg, ha az oldal kell időtúllépés. Ez a lehetőség várható, wordpress-webhelye csak a virtuális hálózat és a nem az internethez csatlakozik. Ha a Proxy URL-címe illessze a virtuális hálózaton belül Wordpress-webhelye lekért, gyönyörű, üzemek képet kell megjelennie a böngészőben. 

A Függvényalkalmazás az internetről és a virtuális hálózat is kapcsolódik. A proxy egy kérelem fogadása a nyilvános interneten keresztül, és ezután átjáróként mentén kérelmet továbbítja a virtuális hálózatban az egyszerű HTTP-proxyt. A proxy a választ, majd továbbítja a nyilvános interneten keresztül. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>További lépések

Prémium szintű csomagot futó függvények ossza meg ugyanazt a mögöttes App Service-infrastruktúrát PV2 a Web Apps-csomagok. Ez azt jelenti, hogy minden, a Web Apps-dokumentáció a prémium szintű csomag függvények vonatkozik.

1. [További információ a hálózatkezelési funkciók itt beállításait](./functions-networking-options.md)
1. [Olvassa el a függvények itt hálózat – gyakori kérdések](./functions-networking-faq.md)
1. [További információ az Azure-beli virtuális hálózatok](../virtual-network/virtual-networks-overview.md)
1. [További hálózati funkciókat és hozzáférés-vezérlés az App Service Environment-környezetek engedélyezése](../app-service/environment/intro.md)
1. [Hibrid kapcsolatok használatával tűzfal módosítása nélkül az egyes helyszíni erőforrások eléréséhez](../app-service/app-service-hybrid-connections.md)
1. [További tudnivalók a Függvényproxykat](./functions-proxies.md)

<!--Image references-->
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
