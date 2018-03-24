---
title: A ILB App Service Environment-környezet integrálható a meglévő Alkalmazásátjáró
description: Az Alkalmazásátjáró a ILB App Service Environment-környezet az alkalmazások integrálása forgatókönyv
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
ms.openlocfilehash: c64b686d7a9016b3834096ebc88179db8972098f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="integrate-your-ilb-app-service-environment-with-an-application-gateway"></a>A ILB App Service Environment-környezet integrálható a meglévő Alkalmazásátjáró #

A [App Service Environment-környezet](./intro.md) az alhálózat egy ügyfél az Azure virtuális hálózat az Azure App Service üzemelő példány. Központilag telepíthető egy nyilvános vagy privát végponttal az alkalmazás eléréséhez. A központi telepítés az App Service Environment-környezet egy titkos végponttal (Ez azt jelenti, hogy a belső terheléselosztók) ILB App Service-környezetek nevezik.  

Az Azure Application Gateway egy virtuális készüléket, amely 7 réteg terheléselosztás, SSL-feladatkiszervezést és a webes alkalmazás tűzfalat (waf-ot). Ez a figyelheti egy nyilvános IP-cím- és útvonal forgalom az alkalmazás végpontjának. 

A következő információkat ismerteti, hogyan WAF konfigurált Alkalmazásátjáró integrálható egy alkalmazás egy ILB App Service Environment-környezetben.  

Az Alkalmazásátjáró ILB App Service-környezetet a rendszer integrálása az alkalmazás szintjén van. Ha a ILB App Service Environment-környezet az Alkalmazásátjáró konfigurál, végzett az adott alkalmazásokhoz a ILB App Service Environment-környezetben. Ez a módszer lehetővé teszi, hogy üzemeltetéséhez biztonságos több-bérlős egy egyetlen ILB App Service Environment-környezetben.  

![Az Alkalmazásátjáró alkalmazást az ILB App Service Environment mutat][1]

A bemutató keretében a következő lépéseket fogja végrehajtani:

* Alkalmazásátjáró létrehozása.
* Konfigurálja az Alkalmazásátjáró szeretne egy a ILB App Service Environment-környezetben.
* Állítsa be alkalmazását az egyéni tartománynév tiszteletben.
* Szerkessze a nyilvános DNS-állomásnevet, amely az Alkalmazásátjáró mutat.

## <a name="prerequisites"></a>Előfeltételek

Az Alkalmazásátjáró integrálása a ILB App Service Environment-környezet, az alábbiak szükségesek:

* An ILB App Service Environment.
* A ILB App Service Environment-környezetben futó alkalmazások.
* Az internetes irányítható tartománynév az alkalmazás a ILB App Service Environment-környezetben használható.
* Használja a ILB App Service Environment-környezet ILB cím. Ez az információ van az App Service Environment-környezet portál **beállítások** > **IP-címek**:

    ![Példa a ILB App Service Environment-környezet által használt IP-címek listája][9]
    
* Egy nyilvános DNS-nevet, amellyel később az Alkalmazásátjáró mutasson. 

További ILB App Service-környezetek létrehozásával kapcsolatos további információkért lásd: [létrehozása és használata ILB App Service-környezetek][ilbase].

Ez a cikk feltételezi, hogy az azonos Azure virtuális hálózat, amelyen az App Service Environment-környezet üzembe van helyezve az Alkalmazásátjáró. Az Alkalmazásátjáró létrehozása előtt alhálózat kiválasztása vagy létrehozása egy, az átjáró üzemeltetésére használhatja. 

Egy alhálózatot, amely nem a egy elnevezett GatewaySubnet kell használnia. Ha az Alkalmazásátjáró be GatewaySubnet, nem lehet létrehozni a virtuális hálózati átjáró később lesz. 

Az átjáró, amely a ILB App Service-környezetet használ az alhálózat nem helyezhető. Az App Service Environment-környezet, hogy az alhálózat lehet.

## <a name="configuration-steps"></a>Konfigurációs lépések ##

1. Az Azure-portálon lépjen **új** > **hálózati** > **Alkalmazásátjáró**.

2. Az a **alapjai** területen:

   a. A **neve**, adja meg az Alkalmazásátjáró nevét.

   b. A **réteg**, jelölje be **WAF**.

   c. A **előfizetés**, válassza ki az App Service Environment-környezet virtuális hálózat által használt ugyanahhoz az előfizetéshez.

   d. A **erőforráscsoport**, hozzon létre vagy válasszon ki az erőforráscsoportot.

   e. A **hely**, válassza ki az App Service Environment-környezet virtuális hálózat helyét.

   ![Új alkalmazás átjáró létrehozása alapjai][2]

3. Az a **beállítások** területen:

   a. A **virtuális hálózati**, válassza ki az App Service Environment-környezet virtuális hálózatot.

   b. A **alhálózati**, jelölje ki az alhálózatot, ahol az alkalmazás átjárót kell telepíteni. Ne használjon GatewaySubnet, mivel megakadályozza, hogy a VPN-átjárók létrehozása.

   c. A **IP-cím típusa**, jelölje be **nyilvános**.

   d. A **nyilvános IP-cím**, válassza ki a nyilvános IP-cím. Ha még nincs fiókja, létrehozhat egy tárhelyet.

   e. A **protokoll**, jelölje be **HTTP** vagy **HTTPS**. Konfigurálja a HTTPS-hez, ha meg kell adnia egy PFX-tanúsítvány.

   f. A **webalkalmazási tűzfal**, engedélyezze a tűzfalat, és is beállíthat két **észlelési** vagy **megelőzési** igényei szerint.

   ![Új átjáró létrehozása Alkalmazásbeállítások][3]
    
4. Az a **összegzés** szakaszt, tekintse át a beállításokat, és válassza ki **OK**. Az Alkalmazásátjáró eltarthat valamivel több mint 30 perc a telepítés befejezéséhez.  

5. Az Alkalmazásátjáró telepítő befejezése után nyissa meg az alkalmazás átjáró portálra. Válassza ki **háttérkészlet**. Vegye fel a Példánynak a ILB App Service Environment-környezet.

   ![Háttérkészlet konfigurálása][4]

6. A háttér-készlet folyamatán befejezése után válassza ki a **állapot-mintavételi csomagjai**. Hozzon létre egy, az alkalmazás használni kívánt tartománynév állapotmintáihoz. 

   ![Állapotminták konfigurálása][5]
    
7. A health mintavételt folyamatán befejezése után válassza ki a **HTTP-beállítások**. A meglévő beállítások szerkesztése, jelölje be **az egyéni mintavételi**, és válassza ki a konfigurált mintavétel.

   ![HTTP-beállítások konfigurálása][6]
    
8. Nyissa meg az Alkalmazásátjáró **áttekintése** szakaszt, és másolja a nyilvános IP-címet, amely az Alkalmazásátjáró használja. Állítsa be az IP-címet, az alkalmazás tartománynév A rekordjainak, vagy egy olyan CNAME rekordot a DNS-neve, arra a címre használni. Válassza ki a nyilvános IP-cím, és másolja át a nyilvános IP-cím felhasználói felület helyett másolja az Alkalmazásátjáró szereplő könnyebben **áttekintése** szakasz. 

   ![Alkalmazás átjáró portál][7]

9. Állítsa be az egyéni tartománynév beállítása az alkalmazáshoz a ILB App Service Environment-környezetben. Nyissa meg az alkalmazásnak a portálon, és a **beállítások**, jelölje be **egyéni tartományok**.

   ![Egyéni tartománynév beállítása az alkalmazás][8]

A webalkalmazások használt egyéni tartománynevek beállításáról a cikkben szereplő információ [egyéni tartománynevek a webalkalmazás beállítása][custom-domain]. De ILB App Service-környezetek alkalmazás, nem minden a tartománynév ellenőrzése. A DNS-ben, amely felügyeli az alkalmazás végpontok saját, mert helyezhet el bármilyen kívánt legyenek. Az egyéni tartománynevet, amelyeket ebben az esetben nem kell lennie a DNS-ben, de azt is be kell állítani az alkalmazással. 

Miután a telepítés befejezése, és engedélyezte, hogy a DNS propagálására módosításainak a rövid időn, az alkalmazás által létrehozott egyéni tartománynév használatával végezheti el. 


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
