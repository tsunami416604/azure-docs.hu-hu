---
title: "A ILB ASE integrálása az Azure Alkalmazásátjáró"
description: "Az alkalmazás a ILB ASE integrálása az Azure Application Gateway forgatókönyv"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: ccompy
ms.openlocfilehash: eedad8824add7fe425d34975dab640fbee82c2bc
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2017
---
# <a name="integrating-your-ilb-ase-with-an-application-gateway"></a>A ILB ASE integrálása Alkalmazásátjáró #

A [Azure App Service környezetben (ASE)](./intro.md) az ügyfél Azure virtuális hálózat az alhálózat az Azure App Service üzemelő példány. Központilag telepíthető egy nyilvános vagy privát végponttal az alkalmazás eléréséhez. A központi telepítés, saját végponttal hajlamosnak egy ILB ASE nevezik.  
Az Azure Application Gateway a virtuális készülék réteg 7 terheléselosztás, SSL-feladatkiszervezést és WAF védelmét biztosító. Ez a figyelheti egy nyilvános IP-cím- és útvonal forgalom az alkalmazás végpontjának. A következő információkat ismerteti, hogyan integrálható a WAF Alkalmazásátjáró konfigurált egy alkalmazás egy ILB ASE és.  

Az Alkalmazásátjáró a ILB mértékéig integrálása az alkalmazás szintjén van.  Az Alkalmazásátjáró a ILB mértékéig konfigurálásakor végez az adott alkalmazásokhoz a ILB ASE a. Ez lehetővé teszi, hogy egy egyetlen ILB ASE a biztonságos több-bérlős üzemeltetéséhez.  

![Az Alkalmazásátjáró alkalmazást egy ILB ASE az mutat][1]

A bemutató keretében a következő lépéseket fogja végrehajtani:

* Alkalmazásátjáró létrehozása
* az Alkalmazásátjáró úgy, hogy egy alkalmazást a ILB ASE mutasson konfigurálása
* az alkalmazásnak, hogy fogadják el az egyéni tartománynév konfigurálása
* a nyilvános DNS-állomásnév mutat, az Application Gateway szerkesztése

Az Alkalmazásátjáró integrálja a ILB mértékéig, az alábbiak szükségesek:

* egy ILB ASE
* a ILB ASE futó alkalmazások
* az internetes irányítható tartománynév az alkalmazást a ILB ASE a használni kívánt
* a használják a ILB ASE ILB címet (Ez az az ASE portál **beállítások -> IP-címek**)

    ![A ILB ASE által használt IP-címek][9]
    
* egy nyilvános DNS-nevet, amellyel később az Alkalmazásátjáró pont 

A részletek egy ILB ASE létrehozásával, olvassa el a dokumentum [létrehozása és egy ILB ASE használatával][ilbase]

Ez az útmutató azt feltételezi, hogy azt szeretné, hogy a meglévő Alkalmazásátjáró azonos Azure virtuális hálózatban, amely a ASE központilag telepítik. Az Alkalmazásátjáró létrehozása megkezdése előtt alhálózat kiválasztása vagy létrehozása a, amely az Alkalmazásátjáró futtatására használhatja. Olyan alhálózatot, amely nem a egy elnevezett GatewaySubnet van, vagy az alhálózatot, használja a ILB ASE kell használnia.
Ha az Alkalmazásátjáró be a GatewaySubnet majd akkor lesz nem lehet létrehozni a virtuális hálózati átjáró később. Akkor is nem tegye közzé az alhálózat, használja a ILB ASE által a ASE, hogy az alhálózatban lehet.

## <a name="steps-to-configure"></a>Konfigurálásának lépései ##

1. Az Azure-portálon belül Ugrás **új > Hálózat > Application Gateway** 
    1. Adja meg:
        1. Az Alkalmazásátjáró neve
        1. WAF kiválasztása
        1. Válassza ki a ASE vnet használt ugyanahhoz az előfizetéshez
        1. Hozzon létre vagy az erőforráscsoport kiválasztása
        1. Válassza ki azt a helyet a ASE VNet a

    ![Új alkalmazás átjáró létrehozása alapjai][2]   
    1. A Beállítások területre készletben:
        1. A ASE virtuális hálózat
        1. Az alhálózat az Alkalmazásátjáró kell helyezhető üzembe. Végre nem használja a GatewaySubnet megakadályozza, hogy a VPN-átjárók létrehozása
        1. A nyilvános
        1. Válassza ki a nyilvános IP-cím. Ha nem rendelkezik egy majd hozzon létre egyet most
        1. Állítsa be a HTTP vagy HTTPS PROTOKOLLT. Ha a HTTPS-hez meg kell adnia egy PFX-tanúsítvány konfigurálása
        1. Válassza ki a webalkalmazás fireway beállításai. Itt engedélyezheti a tűzfal és állítania vagy észleléséhez vagy megelőzési ismertető igazítása.

    ![Új átjáró létrehozása Alkalmazásbeállítások][3]
    
    1. Az összefoglaló szakasz felülvizsgálat alatt, válassza ki a **Ok**. A telepítés befejezéséhez a Alkalmazásátjáró valamivel több mint 30 percet is igénybe vehet.  

2. Az Alkalmazásátjáró telepítő befejezése után nyissa meg az Alkalmazásátjáró portált. Válassza ki **háttérkészlet**.  Vegye fel a Példánynak a ILB ASE a.

    ![Háttérkészlet konfigurálása][4]

3. A háttérkészlet konfigurálásához a feldolgozás befejezése után válassza ki a **állapot-mintavételi csomagjai**. Hozzon létre egy állapotmintáihoz beállítása az alkalmazáshoz használni kívánt tartománynév. 

    ![Állapotminták konfigurálása][5]
    
4. A health mintavételt konfigurálásához feldolgozás befejezése után válassza ki **HTTP-beállítások**.  Szerkessze a meglévő beállítás van, jelölje be **az egyéni mintavételi**, és válassza ki a konfigurált mintavétel.

    ![HTTP-beállítások konfigurálása][6]
    
5. Nyissa meg az Alkalmazásátjáró **áttekintése**, és másolja a nyilvános IP-cím, a használt.  Állítsa be az IP-címet, az alkalmazás tartománynév A rekordjainak, vagy egy olyan CNAME rekordot a DNS-neve, arra a címre használni.  Válassza ki a nyilvános IP-cím, és másolja azt a nyilvános IP-címről felhasználói felület helyett másolja a hivatkozást a átjáró – áttekintés szakaszban könnyebben. 

    ![Alkalmazás átjáró portál][7]

6. A ILB ASE beállítani az egyéni tartománynév beállítása az alkalmazáshoz.  Navigáljon a portálon, és válassza a beállítások alapján az alkalmazás **egyéni tartományok**

![Egyéni tartománynév beállítása az alkalmazás][8]

A webalkalmazások használt egyéni tartománynevek itt beállításával kapcsolatos információk [egyéni tartománynevek a webalkalmazás beállítása][custom-domain]. Az alkalmazáshoz egy ILB ASE és, hogy a dokumentum különbség, hogy nincs-e bármilyen a tartománynév ellenőrzése.  Mivel a DNS-ben, amely felügyeli az alkalmazás végpontok saját helyezhet el bármilyen kívánt ott. Ebben az esetben adja hozzá az egyéni tartománynevet kell lennie a DNS-ben, de azt is be kell állítani az alkalmazással. 

Miután a telepítés befejezése, és engedélyezte, hogy a DNS-módosítások való terjesztése, a rövid időn akkor érhető el az alkalmazást a létrehozott egyéni tartománynevet. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: http://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
