---
title: ILB App Service Environment-környezet integrálható az Application Gateway – Azure
description: Az ILB App Service-környezet az alkalmazások integrálása egy Application Gateway a forgatókönyv
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
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ea46b5e57e4e508a3311de8633ae61d346b574eb
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273816"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Az ILB App Service-környezet integrálható az Azure Application Gateway #

A [App Service Environment-környezet](./intro.md) üzembe helyezése az Azure App Service egy ügyfél az Azure virtuális hálózat alhálózatában van. Is üzembe helyezhető egy nyilvános vagy privát végponttal az alkalmazás eléréséhez. Az App Service Environment egy privát végponttal (azaz egy belső terheléselosztót) telepítése egy ILB App Service Environment-környezet neve.  

Webalkalmazás-tűzfalak biztonságos súgó a webes alkalmazások vizsgálatával szerezheti be a bejövő webes forgalmat blokkolja az SQL-injektálások, a Webhelyközi, parancsfájl-kezelési rosszindulatú feltöltéseket és DDoS-alkalmazásokat és egyéb támadások. Szintén átvizsgálja a válaszokat a háttér-kiszolgálókról az adatok adatveszteség-megelőzési (DLP). A WAF-eszközzel megtekintheti az Azure marketplace-ről, vagy használhatja a [Azure Application Gateway][appgw].

Az Azure Application Gateway-példány virtuális berendezés által biztosított 7. rétegbeli terheléselosztást, SSL-kiürítés és webes alkalmazások tűzfal (WAF) védelmét. Figyelheti, hogy az egy nyilvános IP-cím és útvonal forgalmat az alkalmazás-végponthoz. A következő információkat ismerteti, hogyan lehet egy konfigurált WAF application gateway integrálása egy alkalmazást egy ILB App Service-környezetben.  

Az application gateway az ILB App Service Environment-integrációjának van az alkalmazás szintjén. Az application gateway az ILB App Service-környezet konfigurál, amikor végzett, konkrét alkalmazások esetén az ILB App Service-környezetben. Ez a módszer lehetővé teszi, hogy egy ILB App Service-környezet a biztonságos több-bérlős alkalmazások üzemeltetése.  

![Az Application gateway ILB App Service Environment-alkalmazás mutató][1]

A bemutató keretében a következő lépéseket fogja végrehajtani:

* Az Azure Application Gateway létrehozása.
* Konfigurálja az Application Gateway, mutasson az ILB App Service-környezet alkalmazásban.
* Az alkalmazás, tartsa tiszteletben az egyéni tartománynév konfigurálása.
* Szerkessze a nyilvános DNS-állomás neve, amely az application gateway mutat.

## <a name="prerequisites"></a>Előfeltételek

Az Application Gateway integrálása az ILB App Service-környezet, az alábbiak szükségesek:

* Egy ILB App Service-környezet.
* Az ILB App Service Environment-ban futó alkalmazás.
* Az internetes irányítható tartománynév az ILB App Service-környezetben az alkalmazással használandó.
* Az ILB-címmel, amely az ILB App Service-környezetben használja. Ez az információ van az App Service Environment-környezet portál **beállítások** > **IP-címek**:

    ![Példa listája az ILB App Service-környezet által használt IP-címek][9]
    
* Egy nyilvános DNS-neve, amellyel később az Application Gateway mutasson. 

Egy ILB App Service Environment-környezet létrehozásával kapcsolatos részletekért lásd: [létrehozása és a egy ILB App Service Environment-környezet használatával][ilbase].

Ez a cikk feltételezi, hogy az azonos Azure virtuális hálózat, ahol telepíti az App Service-környezetben lévő Application Gateway. Az Application Gateway létrehozása előtt válasszon ki vagy hozzon létre egy alhálózatot az átjáró üzemeltetéséhez használt. 

Használjon egy alhálózatot, amely nem az egyik nevű átjáró-alhálózat. Ha az Application Gateway átjáró-alhálózat helyezi, nem hozható létre egy virtuális hálózati átjáró később fogja. 

Az átjáró, amely az ILB App Service-környezetben használja az alhálózat nem állítható. Az App Service-környezet, hogy az alhálózat is lehet.

## <a name="configuration-steps"></a>Konfigurációs lépések ##

1. Az Azure Portalon lépjen a **új** > **hálózati** > **Application Gateway**.

2. Az a **alapjai** terület:

   a. A **neve**, adja meg az Application Gateway neve.

   b. A **szint**válassza **WAF**.

   c. A **előfizetés**, válassza ki az App Service Environment-környezet virtuális hálózat által használt ugyanahhoz az előfizetéshez.

   d. A **erőforráscsoport**, hozzon létre vagy válassza ki az erőforráscsoportot.

   e. A **hely**, válassza ki az App Service Environment-környezet virtuális hálózat helyén.

   ![Új Application Gateway létrehozása alapjai][2]

3. Az a **beállítások** terület:

   a. A **virtuális hálózati**, válassza ki az App Service Environment-környezet virtuális hálózatot.

   b. A **alhálózati**, válassza ki az alhálózatot, ahol az Application Gateway kell telepíteni. Ne használjon GatewaySubnet, mert megakadályozza, hogy a VPN-átjárók létrehozása.

   c. A **IP-cím típusa**válassza **nyilvános**.

   d. A **nyilvános IP-cím**, válassza ki a nyilvános IP-címet. Ha nem rendelkezik ilyennel, hozzon létre egyet most.

   e. A **protokoll**válassza **HTTP** vagy **HTTPS**. HTTPS konfigurálása esetén, meg kell adnia egy PFX-tanúsítvány.

   f. A **webalkalmazási tűzfal**, engedélyezheti a tűzfalat, és is állíthatja ezt a **észlelési** vagy **megelőzési** tetszés szerint.

   ![Új Application Gateway-létrehozási beállítások][3]
    
4. Az a **összefoglalás** részen tekintse át a beállításokat, és válassza ki **OK**. Az Application Gateway is igénybe vehet egy kicsit több mint 30 perc a telepítés befejezéséhez.  

5. Az Application Gateway telepítés befejezése után nyissa meg az Application Gateway-portálról. Válassza ki **háttérkészlet**. ILB-címét adja meg az ILB App Service-környezet.

   ![Háttérkészlet konfigurálása][4]

6. A háttérkészlet konfigurálása a folyamat befejezése után jelölje ki a **állapotadat-mintavételek**. Hozzon létre egy állapotmintát az alkalmazáshoz használni kívánt tartománynév esetében. 

   ![Állapotminták konfigurálása][5]
    
7. Az állapot-mintavételei konfigurálásának folyamatán befejezése után jelölje ki a **HTTP-beállítások**. Válassza ki a meglévő beállítások szerkesztése **az egyéni mintavétel**, és válassza ki a mintavételt, amelyet a konfigurált.

   ![HTTP-beállítások konfigurálása][6]
    
8. Nyissa meg az Application Gateway **áttekintése** szakaszt, és másolja a nyilvános IP-címet, amely az Application Gateway használja. Állítsa be az IP-cím, egy A rekordot az alkalmazás tartománynevet, vagy egy CNAME rekordot a DNS-név arra a címre használata. Válassza ki a nyilvános IP-címet, és másolja a nyilvános IP-cím felhasználói felületről helyett másolja azt a hivatkozásból az Application Gateway az egyszerűbb **áttekintése** szakaszban. 

   ![Application Gateway-portál][7]

9. Állítsa be az alkalmazás számára az egyéni tartománynév az ILB App Service Environment környezetben. Nyissa meg az alkalmazás a portálon, és a **beállítások**válassza **egyéni tartományok**.

   ![Egyéni tartománynév beállítása az alkalmazásban][8]

Nincs információ a web apps számára egyéni tartománynevek beállítása a cikkben a [a webalkalmazás egyéni tartománynevek beállítása][custom-domain]. Azonban az alkalmazás egy ILB App Service-környezetben, a tartománynévnek minden olyan érvényesítése nem áll rendelkezésre. Mivel Ön a tulajdonosa, amely az alkalmazás végpontok kezeli a DNS-ben, bármilyen kívánt meg ott helyezheti. Ebben az esetben adja hozzá az egyéni tartománynév nem kell lennie a DNS-ben, de azt is konfigurálni kell az alkalmazását. 

Miután befejeződik a telepítés, és engedélyezte, hogy egy rövid ideig a DNS módosításainak propagálása, elérheti az alkalmazás az Ön által létrehozott egyéni tartománynév használatával. 


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
