---
title: Külső ASE létrehozása
description: Ismerje meg, hogyan hozhat létre egy App Service-környezetet egy alkalmazással, vagy hozzon létre egy önálló (üres) ASE-t.
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6c4838e3226b91cbb5d6f86b83266a986418c120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430512"
---
# <a name="create-an-external-app-service-environment"></a>Külső alkalmazásszolgáltatás-környezet létrehozása

Az Azure App Service Environment az Azure App Service egy olyan példánya, amelyet egy Azure virtuális hálózat alhálózatában helyeztek üzembe.

> [!NOTE]
> Minden App Service-környezet rendelkezik egy virtuális IP (VIP), amely az App Service-környezetben való kapcsolatfelvételre használható.

Az App Service Environment (ASE) üzembe helyezésének két módja van:

- Egy virtuális IP-cím vagy külső IP-cím, azaz külső ASE használatával.
- A virtuális IP-cím a virtuális IP-cím, gyakran nevezik egy ILB ASE, mert a belső végpont egy belső terheléselosztó (ILB).

Ez a cikk bemutatja, hogyan hozhat létre egy külső ASE. Az ASE áttekintését az [App Service-környezet bemutatkozása][Intro]című témakörben találja. Az ILB ASE létrehozásáról az [ILB ASE létrehozása és használata][MakeILBASE]című témakörben talál további információt.

## <a name="before-you-create-your-ase"></a>Az ASE létrehozása előtt

Az ASE létrehozása után nem módosíthatja a következőket:

- Hely
- Előfizetés
- Erőforráscsoport
- Használt virtuális hálózat
- Használt alhálózat
- Alhálózat mérete

> [!NOTE]
> Ha kiválaszt egy virtuális hálózatot, és megad egy alhálózatot, győződjön meg arról, hogy elég nagy ahhoz, hogy a jövőbeli növekedési és skálázási igényeket. 256 címből álló `/24` méretet ajánlunk.
>

## <a name="three-ways-to-create-an-ase"></a>Az ASE létrehozásának három módja

Az ASE létrehozásának három módja van:

- **App Service-csomag létrehozása közben.** Ez a módszer egy lépésben hozza létre az ASE-t és az App Service-csomagot.
- **Önálló akcióként.** Ez a módszer létrehoz egy önálló ASE, amely egy ASE semmit sem benne. Ez a módszer egy fejlettebb folyamat ase létrehozásához. Segítségével hozzon létre egy ASE egy ILB.You use it to create an ASE with a ILB.
- **Egy Azure Resource Manager-sablonból.** Ez a módszer haladó felhasználókszámára van. További információ: [AsE létrehozása sablonból.][MakeASEfromTemplate]

Egy külső ASE rendelkezik egy nyilvános VIRTUÁLIS IP-, ami azt jelenti, hogy az összes HTTP/HTTPS-forgalom az ASE-ben az alkalmazások egy internet-en keresztül elérhető IP-címet. Az ILB-vel rendelkező ASE rendelkezik az ASE által használt alhálózat IP-címével. Az ILB ASE-ben tárolt alkalmazások nincsenek közvetlenül az interneten.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Hozzon létre egy ASE-csomagot és egy App Service-csomagot együtt

Az App Service-csomag egy alkalmazások tárolója. Amikor létrehoz egy alkalmazást az App Service-ben, kiválaszt vagy létrehoz egy App Service-csomagot. Az App Service-környezetek alkalmazásszolgáltatási csomagokat tartanak, az App Service-csomagok pedig alkalmazásokat.

Ase létrehozása az App Service-csomag létrehozása közben:

1. Az [Azure portalon](https://portal.azure.com/)válassza az **Erőforrás** > létrehozása**web + mobil webalkalmazás** > **Web App**lehetőséget.

    ![Webalkalmazás létrehozása][1]

2. Válassza ki előfizetését. Az alkalmazás és az ASE ugyanabban az előfizetésben jönnek létre.

3. Válasszon ki vagy hozzon létre egy erőforráscsoportot. Az erőforráscsoportok segítségével kezelheti a kapcsolódó Azure-erőforrásokat egy egységként. Az erőforráscsoportok akkor is hasznosak, ha szerepköralapú hozzáférés-vezérlési szabályokat hoz létre az alkalmazásokhoz. További információért lásd [az Azure Resource Manager áttekintését][ARMOverview].

4. Válassza ki az operációs rendszert (Windows, Linux vagy Docker). 

5. Válassza az App Service-csomagot, majd az **Új létrehozása lehetőséget.** A Linux-webalkalmazások és a Windows-webalkalmazások nem lehetnek ugyanabban az App Service-csomagban, de ugyanabban az App Service-környezetben lehetnek. 

    ![Új App Service-csomag][2]

6. A **Hely** legördülő listában válassza ki azt a régiót, ahol létre szeretné hozni az ASE-t. Ha egy meglévő ASE-t választ, nem jön létre egy új ASE. Az App Service-csomag a kiválasztott ASE-ben jön létre. 

7. Válassza ki **a Tarifacsomagot**, és válasszon egyet az **Elkülönített** árképzési sk-ek közül. Ha egy **elkülönített** termékváltozat-kártyát és egy olyan helyet választ, amely nem ASE, egy új ASE jön létre az adott helyen. Az ASE létrehozásához a **Kijelölés**lehetőséget. Az **elkülönített** termékváltozat csak ase-vel együtt érhető el. Más árképzési termékváltozat nem használható az **Elkülönítetttől eltérő**ASE-ben. 

    ![Tarifacsomag kiválasztása][3]

8. Adja meg az ASE nevét. Ez a név az alkalmazások címezhető nevében használatos. Ha az ASE neve _appsvcenvdemo_, a tartománynév *.appsvcenvdemo.p.azurewebsites.net*. Ha létrehoz egy *mytestapp*nevű alkalmazást, az mytestapp.appsvcenvdemo.p.azurewebsites.net címen címezhető. A névben nem lehet szóközt használni. Ha nagybetűket használ, a tartománynév a név teljes kisverziója.

    ![Új App Service-csomag neve][4]

9. Adja meg az Azure virtuális hálózati adatait. Válassza az **Új létrehozása** vagy a **Meglévő kijelölése lehetőséget.** A meglévő virtuális hálózat kiválasztásának lehetősége csak akkor érhető el, ha a kijelölt régióban van virtuális hálózat. Ha az Új létrehozása lehetőséget **választja,** adja meg a virtuális hálózat nevét. Egy új erőforrás-kezelő virtuális hálózat jön létre ezzel a névvel. A kijelölt terület `192.168.250.0/23` címterületét használja. Ha a Meglévő kijelölése lehetőséget **választja,** a következőket kell a következőkre.

    a. Válassza ki a virtuális hálózat címblokk, ha több mint egy.

    b. Adjon meg egy új alhálózatnevet.

    c. Válassza ki az alhálózat méretét. *Ne feledje, hogy válasszon ki egy méretet elég nagy ahhoz, hogy alkalmazkodjon a jövőbeli növekedése az ASE.* Javasoljuk `/24`, amely 128 címet, és képes kezelni a maximális méretű ASE. Nem javasoljuk `/28`például, mert csak 16 cím áll rendelkezésre. Az infrastruktúra legalább hét címet használ, az Azure Networking pedig további 5 címet. Egy `/28` alhálózatban egy külső ASE-hez legfeljebb 4 App Service-csomagpéldány, és csak 3 App Service-csomagpéldány oka marad egy ILB ASE-hez.

    d. Jelölje ki az alhálózati IP-tartományt.

10. Válassza a **Létrehozás** gombot az ASE létrehozásához. Ez a folyamat az App Service-csomagot és az alkalmazást is létrehozza. Az ASE, az App Service-csomag és az alkalmazás mind ugyanazon előfizetés alatt, és ugyanabban az erőforráscsoportban is. Ha az ASE-nek külön erőforráscsoportra van szüksége, vagy ha ILB ASE-re van szüksége, kövesse az ASE-t.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Hozzon létre egy ASE-t és egy Linux-webalkalmazást egy egyéni Docker-lemezkép használatával

1. Az [Azure Portalon](https://portal.azure.com/) **hozzon létre egy erőforrás** > **web + mobil** > **webalkalmazás tárolókhoz.** 

    ![Webalkalmazás létrehozása][7]

1. Válassza ki előfizetését. Az alkalmazás és az ASE ugyanabban az előfizetésben jönnek létre.

1. Válasszon ki vagy hozzon létre egy erőforráscsoportot. Az erőforráscsoportok segítségével kezelheti a kapcsolódó Azure-erőforrásokat egy egységként. Az erőforráscsoportok akkor is hasznosak, ha szerepköralapú hozzáférés-vezérlési szabályokat hoz létre az alkalmazásokhoz. További információért lásd [az Azure Resource Manager áttekintését][ARMOverview].

1. Válassza az App Service-csomagot, majd az **Új létrehozása lehetőséget.** A Linux-webalkalmazások és a Windows-webalkalmazások nem lehetnek ugyanabban az App Service-csomagban, de ugyanabban az App Service-környezetben lehetnek. 

    ![Új App Service-csomag][8]

1. A **Hely** legördülő listában válassza ki azt a régiót, ahol létre szeretné hozni az ASE-t. Ha egy meglévő ASE-t választ, nem jön létre egy új ASE. Az App Service-csomag a kiválasztott ASE-ben jön létre. 

1. Válassza ki **a Tarifacsomagot**, és válasszon egyet az **Elkülönített** árképzési sk-ek közül. Ha egy **elkülönített** termékváltozat-kártyát és egy olyan helyet választ, amely nem ASE, egy új ASE jön létre az adott helyen. Az ASE létrehozásához a **Kijelölés**lehetőséget. Az **elkülönített** termékváltozat csak ase-vel együtt érhető el. Más árképzési termékváltozat nem használható az **Elkülönítetttől eltérő**ASE-ben. 

    ![Tarifacsomag kiválasztása][3]

1. Adja meg az ASE nevét. Ez a név az alkalmazások címezhető nevében használatos. Ha az ASE neve _appsvcenvdemo_, a tartománynév *.appsvcenvdemo.p.azurewebsites.net*. Ha létrehoz egy *mytestapp*nevű alkalmazást, az mytestapp.appsvcenvdemo.p.azurewebsites.net címen címezhető. A névben nem lehet szóközt használni. Ha nagybetűket használ, a tartománynév a név teljes kisverziója.

    ![Új App Service-csomag neve][4]

1. Adja meg az Azure virtuális hálózati adatait. Válassza az **Új létrehozása** vagy a **Meglévő kijelölése lehetőséget.** A meglévő virtuális hálózat kiválasztásának lehetősége csak akkor érhető el, ha a kijelölt régióban van virtuális hálózat. Ha az Új létrehozása lehetőséget **választja,** adja meg a virtuális hálózat nevét. Egy új erőforrás-kezelő virtuális hálózat jön létre ezzel a névvel. A kijelölt terület `192.168.250.0/23` címterületét használja. Ha a Meglévő kijelölése lehetőséget **választja,** a következőket kell a következőkre.

    a. Válassza ki a virtuális hálózat címblokk, ha több mint egy.

    b. Adjon meg egy új alhálózatnevet.

    c. Válassza ki az alhálózat méretét. *Ne feledje, hogy válasszon ki egy méretet elég nagy ahhoz, hogy alkalmazkodjon a jövőbeli növekedése az ASE.* Javasoljuk `/24`, amely 128 címet, és képes kezelni a maximális méretű ASE. Nem javasoljuk `/28`például, mert csak 16 cím áll rendelkezésre. Az infrastruktúra legalább hét címet használ, az Azure Networking pedig további 5 címet. Egy `/28` alhálózatban egy külső ASE-hez legfeljebb 4 App Service-csomagpéldány, és csak 3 App Service-csomagpéldány oka marad egy ILB ASE-hez.

    d. Jelölje ki az alhálózati IP-tartományt.

1.  Válassza a "Tároló konfigurálása" lehetőséget.
    * Adja meg az egyéni lemezkép nevét (használhatja az Azure Container Registry, a Docker Hub és a saját privát beállításjegyzék). Ha nem szeretné használni a saját egyéni tárolóját, csak hozza a kódot, és használjon beépített lemezképet a Linux app szolgáltatással, a fenti utasítások szerint. 

    ![Tároló konfigurálása][9]

1. Válassza a **Létrehozás** gombot az ASE létrehozásához. Ez a folyamat az App Service-csomagot és az alkalmazást is létrehozza. Az ASE, az App Service-csomag és az alkalmazás mind ugyanazon előfizetés alatt, és ugyanabban az erőforráscsoportban is. Ha az ASE-nek külön erőforráscsoportra van szüksége, vagy ha ILB ASE-re van szüksége, kövesse az ASE-t.


## <a name="create-an-ase-by-itself"></a>Hozzon létre egy ASE önmagában

Ha létrehoz egy ASE önálló, nincs benne semmi. Egy üres ASE továbbra is felmerül az infrastruktúra havi díja. Kövesse az alábbi lépéseket egy ILB-vel rendelkező ASE létrehozásához vagy egy ASE létrehozásához a saját erőforráscsoportjában. Miután létrehozta az ASE-t, alkalmazásokat hozhat létre benne a normál folyamat használatával. Válassza ki az új ASE-t helyként.

1. Keressen az Azure Marketplace for **App Service Environment alkalmazásban,** vagy válassza az Erőforrás >  > létrehozása**webalkalmazás-szolgáltatáskörnyezetének****Web Mobile** **létrehozását**. 

1. Adja meg az ASE nevét. Ez a név az ASE-ben létrehozott alkalmazásokhoz használatos. Ha a név *mynewdemoase*, az altartomány neve *.mynewdemoase.p.azurewebsites.net*. Ha létrehoz egy *mytestapp*nevű alkalmazást, az mytestapp.mynewdemoase.p.azurewebsites.net címen hívható. A névben nem lehet szóközt használni. Ha nagybetűket használ, a tartománynév a név teljes kisverziója. Ha ILB-t használ, az ASE-név nem használatos az altartományban, hanem explicit módon szerepel az ASE létrehozása során.

    ![ASE elnevezés][5]

1. Válassza ki előfizetését. Ez az előfizetés is az, amely az ASE-ben használt összes alkalmazás. Az ASE nem helyezhető el egy másik előfizetésben lévő virtuális hálózatban.

1. Új erőforráscsoport kijelölése vagy megadása. Az ASE-hez használt erőforráscsoportnak meg kell egyeznie a virtuális hálózathoz használt erőforráscsoporttal. Ha egy meglévő virtuális hálózatot választ, az ASE erőforráscsoport-kiválasztása frissül, hogy tükrözze a virtuális hálózat. *Erőforrás-kezelő sablon használata esetén létrehozhat egy ASE-t egy olyan erőforráscsoporttal, amely eltér a Virtuálishálózat erőforráscsoporttól.* Ha ase-t szeretne létrehozni egy sablonból, olvassa el az [App Service-környezet létrehozása sablonból][MakeASEfromTemplate]című témakört.

    ![Erőforráscsoport kiválasztása][6]

1. Válassza ki a virtuális hálózatot és a helyet. Létrehozhat egy új virtuális hálózatot, vagy kijelölhet egy meglévő virtuális hálózatot: 

    * Új virtuális hálózat kiválasztása esetén új nevet és helyet adhat meg. 
    
    * Az új virtuális hálózat címtartománya 192.168.250.0/23, és egy alapértelmezett nevű alhálózat. Az alhálózat definíciója: 192.168.250.0/24. Csak erőforrás-kezelő virtuális hálózatot választhat ki. A **VIP-típus** kiválasztása határozza meg, ha az ASE közvetlenül elérhető az internetről (külső), vagy ha ilb-t használ. Ezekről a beállításokról a [Belső terheléselosztó létrehozása és használata App Service-környezettel című témakörben olvashat bővebben.][MakeILBASE] 

      * Ha a KÜLSŐ lehetőséget **választja** a **VIP típushoz,** megadhatja, hogy a rendszer hány külső IP-címmel jön létre IP-alapú SSL-célokra. 
    
      * Ha a BELSŐ lehetőséget **választja** a **VIP típushoz,** meg kell adnia az ASE által használt tartományt. Az ASE-t nyilvános vagy privát címtartományokat használó virtuális hálózatba helyezheti üzembe. A virtuális hálózat nyilvános címtartományt, a virtuális hálózat ot előre létre kell hoznia. 
    
    * Ha egy meglévő virtuális hálózatot választ, az ASE létrehozásakor új alhálózat jön létre. *A portálon nem használható előre létrehozott alhálózat. Ha Erőforrás-kezelő sablont használ, létrehozhat egy ASE-t egy meglévő alhálózattal.* Ha ase-t szeretne létrehozni egy sablonból, olvassa el az [App Service-környezet létrehozása sablonból][MakeASEfromTemplate]című témakört.

## <a name="app-service-environment-v1"></a>App Service-környezet v1

Továbbra is létrehozhat példányokat az App Service-környezet (ASEv1) első verziójából. A folyamat elindításához keressen a Marketplace-en az **App Service Environment v1 szolgáltatásban.** Az ASE-t ugyanúgy hozza létre, mint az önálló ASE-t. Ha elkészült, az ASEv1 két előtér-végződést és két dolgozót rendelkezik. Az ASEv1 segítségével kezelnie kell az előtér-végeket és a dolgozókat. Nem adják hozzá őket automatikusan az App Service-csomagok létrehozásakor. Az előtér-végpontok HTTP/HTTPS-végpontként működnek, és forgalmat küldenek a dolgozóknak. A dolgozók azok a szerepkörök, amelyek az alkalmazásokat üzemeltetik. Az ASE létrehozása után módosíthatja az előtér-rendszerek és a dolgozók mennyiségét. 

Az ASEv1 szolgáltatásról az [App Service-környezet 1- es szolgáltatásának bemutatása][ASEv1Intro]című témakörben olvashat bővebben. Az ASEv1 méretezéséről, kezeléséről és figyeléséről az [App Service-környezet konfigurálása][ConfigureASEv1]című témakörben talál további információt.

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
