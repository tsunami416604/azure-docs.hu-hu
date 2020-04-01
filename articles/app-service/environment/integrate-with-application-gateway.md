---
title: Integrálás az Application Gateway szolgáltatással
description: Ebből a célból az egész végigvezető útmutatóból megtudhatja, hogyan integrálhat egy alkalmazást az ILB-alkalmazásszolgáltatás-környezetbe egy alkalmazásátjáróval.
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e4838597c50898748eb4b33e81ff22eaeea37b30
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476890"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Az ILB App Service Environment és az Azure Application Gateway integrációja #

Az [App Service-környezet](./intro.md) az Azure App Service üzembe helyezése az ügyfél Azure virtuális hálózatának alhálózatában. Az alkalmazás-hozzáférés nyilvános vagy privát végpontjával telepíthető. Az App Service-környezet telepítése egy privát végpont (azaz egy belső terheléselosztó) az úgynevezett ILB App Service Environment.  

Webalkalmazás tűzfalak segítségével biztonságos a webes alkalmazások vizsgálatával bejövő webes forgalom blokkolja az SQL injekciók, Cross-Site Scripting, malware feltöltések & alkalmazás DDoS és egyéb támadások. Azt is megvizsgálja a válaszokat a háttér-webkiszolgálók adatveszteség-megelőzés (DLP). WaF-eszközt beszerezhet az Azure piactérről, vagy használhatja az [Azure Application Gateway-t.][appgw]

Az Azure Application Gateway egy virtuális berendezés, amely a 7-es réteg terheléselosztás, TLS/SSL kiszervezés és a webalkalmazás tűzfal (WAF) védelmet nyújt. Figyelheti a nyilvános IP-címet, és az alkalmazás végpontra irányíthatja a forgalmat. Az alábbi információk azt ismertetik, hogyan integrálható a WAF által konfigurált alkalmazásátjáró egy alkalmazásegy ILB App Service Environment.  

Az alkalmazásátjáró integrációja az ILB App Service Environment alkalmazásszintű. Amikor konfigurálja az alkalmazásátjárót az ILB App Service Environment környezettel, azt az ILB App Service-környezetben lévő bizonyos alkalmazásokhoz teszi. Ez a módszer lehetővé teszi a biztonságos több-bérlős alkalmazások egyetlen ILB-alkalmazásszolgáltatás-környezetben való üzemeltetését.  

![Alkalmazásátjáró, amely az ILB App Service-környezetben lévő alkalmazásra mutat][1]

A bemutató keretében a következő lépéseket fogja végrehajtani:

* Hozzon létre egy Azure-alkalmazásátjárót.
* Állítsa be az Application Gateway-t úgy, hogy az ILB App Service-környezetben mutasson egy alkalmazásra.
* Állítsa be az alkalmazást az egyéni tartománynév tiszteletben és tiszteletben tartandó.
* Az alkalmazásátjáróra mutató nyilvános DNS-állomásnév szerkesztése.

## <a name="prerequisites"></a>Előfeltételek

Az Application Gateway integrálása az ILB App Service-környezettel a következőkre van szüksége:

* ILB-alkalmazásszolgáltatási környezet.
* Az ILB App Service Környezetben futó alkalmazás.
* Az ILB App Service-környezetben az alkalmazással használandó internetes irányítható tartománynév.
* Az ILB-cím, amelyet az ILB App Service Environment használ. Ez az információ az App Service-környezet portálon található a **Beállítások** > **IP-címek**csoportban:

    ![Példa az ILB App Service Environment által használt IP-címek listájára][9]
    
* Nyilvános DNS-név, amely et később az Application Gateway-re mutat. 

Az ILB App Service-környezet létrehozásáról az [ILB-alkalmazásszolgáltatás-környezet létrehozása és használata című témakörben][ilbase]talál további részleteket.

Ez a cikk feltételezi, hogy szeretne egy application gateway ugyanabban az Azure-beli virtuális hálózat, ahol az App Service-környezet telepítve van. Az Alkalmazásátjáró létrehozása előtt válasszon vagy hozzon létre egy alhálózatot, amelyet az átjáró üzemeltetéséhez fog használni. 

Olyan alhálózatot kell használnia, amely nem a GatewaySubnet nevű alhálózat. Ha az Alkalmazásátjárót a GatewaySubnet-be helyezi, később nem tud virtuális hálózati átjárót létrehozni. 

Az átjárót az ILB App Service-környezet által használt alhálózatba sem helyezheti. Az App Service-környezet az egyetlen dolog, ami lehet ebben az alhálózatban.

## <a name="configuration-steps"></a>Konfigurációs lépések ##

1. Az Azure Portalon nyissa meg az **Új** > **hálózati** > **alkalmazásátjáró t.**

2. Az **Alapok** területen:

   a. A **Név mezőbe**írja be az Alkalmazásátjáró nevét.

   b. A **Szint csoportban**válassza a **WAF**lehetőséget.

   c. **Előfizetés esetén**válassza ki ugyanazt az előfizetést, amelyet az App Service-környezet virtuális hálózata használ.

   d. Az **Erőforráscsoport csoportban**hozza létre vagy jelölje ki az erőforráscsoportot.

   e. A **Hely területen**válassza ki az App Service-környezet virtuális hálózatának helyét.

   ![Az új alkalmazásátjáró létrehozásának alapjai][2]

3. A **Beállítások** területen:

   a. **Virtuális hálózat**esetén válassza ki az App Service-környezet virtuális hálózatát.

   b. Az **Alhálózat**esetében válassza ki azt az alhálózatot, amelyen az alkalmazásátjárót telepíteni kell. Ne használja a GatewaySubnet-et, mert megakadályozza a VPN-átjárók létrehozását.

   c. Az **IP-cím típusesetén**válassza **a Nyilvános**lehetőséget.

   d. **Nyilvános IP-cím**esetén válasszon egy nyilvános IP-címet. Ha még nincs, hozzon létre egyet most.

   e. A **Protokoll csoportban**válassza a **HTTP** vagy **https lehetőséget.** Ha https-hez konfigurál, pfx-tanúsítványt kell megadnia.

   f. A **webalkalmazás tűzfala**, engedélyezheti a tűzfalat, és beállíthatja azt is **az észleléshez** vagy **a megelőzéshez,** ahogy jónak látja.

   ![Új Alkalmazásátjáró-létrehozási beállítások][3]
    
4. Az **Összegzés szakaszban** tekintse át a beállításokat, és válassza az **OK gombot.** Az Application Gateway beállítása valamivel több mint 30 percet is igénybe vehet.  

5. Miután az Application Gateway befejezte a beállítást, nyissa meg az Application Gateway portálon. Válassza **a Háttérkészlet lehetőséget**. Adja meg az ILB-cím címét az ILB App Service-környezethez.

   ![Háttérkészlet konfigurálása][4]

6. A háttérkészlet konfigurálásának befejezése után válassza az **Állapotminta**lehetőséget. Hozzon létre egy állapotminta a tartománynevet, amelyet használni szeretne az alkalmazáshoz. 

   ![Állapotminták konfigurálása][5]
    
7. Az állapotminta konfigurálásának befejezése után válassza a **HTTP-beállítások lehetőséget.** Szerkesztheti a meglévő beállításokat, válassza **az Egyéni mintavétel használata**lehetőséget, és válassza ki a konfigurált mintavételt.

   ![HTTP-beállítások konfigurálása][6]
    
8. Nyissa meg az Application Gateway **áttekintése** szakaszt, és másolja az Application Gateway által használt nyilvános IP-címet. Állítsa be ezt az IP-címet A rekordként az alkalmazás tartománynevéhez, vagy használja a cím DNS-nevét egy CNAME rekordban. A nyilvános IP-cím kiválasztása és másolása a nyilvános IP-cím felhasználói felületéről, ahelyett, hogy az Alkalmazásátjáró **áttekintése** szakaszban található hivatkozásból másolhatja. 

   ![Alkalmazásátjáró-portál][7]

9. Állítsa be az alkalmazás egyéni tartománynevét az ILB App Service Environment környezetben. Nyissa meg az alkalmazást a portálon, és a **Beállítások**csoportban válassza az **Egyéni tartományok**lehetőséget.

   ![Egyéni tartománynév beállítása az alkalmazásban][8]

A webalkalmazások egyéni tartománynevének beállításáról a [Webalkalmazás egyéni tartománynevének beállítása][custom-domain]című cikkben olvashat. Az ILB App Service-környezetben lévő alkalmazások esetében azonban nincs érvényesítés a tartománynéven. Mivel ön az alkalmazásvégpontokat kezelő DNS-t birtokolja, bármit betehet oda, amit csak akar. Ebben az esetben az egyéni tartománynévnek nem kell a DNS-ben lennie, de továbbra is konfigurálni kell az alkalmazással. 

A telepítés befejezése után, és a DNS-módosítások terjesztésére rövid időre engedélyezte az alkalmazás terjesztését, az ön által létrehozott egyéni tartománynév használatával érheti el az alkalmazást. 


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
[appgw]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
