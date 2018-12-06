---
title: Külső Azure App Service environment létrehozása
description: Azt ismerteti, hogyan hozhat létre egy App Service Environment-környezet létrehozása során, egy alkalmazás vagy önálló
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 6df708c97750c89c850c993d0e1a43ded01934a2
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959963"
---
# <a name="create-an-external-app-service-environment"></a>Külső App Service environment létrehozása #

Az Azure App Service Environment az Azure App Service egy olyan példánya, amelyet egy Azure virtuális hálózat alhálózatában helyeztek üzembe. Az App Service Environment (ASE) üzembe helyezésének két módja van:

- Egy virtuális IP-cím vagy külső IP-cím, azaz külső ASE használatával.
- A VIP-nek a belső IP-cím, az gyakran nevezik ILB ASE mert a belső végpont egy belső terheléselosztóval (ILB).

Ez a cikk bemutatja, hogyan hozhat létre a külső ASE környezetben. Az ASE áttekintését lásd: [az App Service Environment bemutatása][Intro]. Az ILB ASE létrehozásával kapcsolatos információkért lásd: [létrehozása és használata az ILB ASE][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Az ASE létrehozása előtt ##

Az ASE létrehozását követően nem módosítható a következő:

- Hely
- Előfizetés
- Erőforráscsoport
- Használt virtuális hálózat
- Alhálózat használata
- Alhálózat mérete

> [!NOTE]
> Válassza ki a virtuális hálózathoz, és adjon meg egy alhálózatot, győződjön meg arról, hogy elég nagy a későbbi növekedéshez és skálázási igényeihez. Azt javasoljuk, hogy egy méretű `/24` 256-címekkel.
>

## <a name="three-ways-to-create-an-ase"></a>Háromféle lehetőség az azonnali ASE létrehozása ##

ASE létrehozása három módja van:

- **App Service-csomag létrehozása során**. Ez a módszer az ASE-t és az App Service-csomagot hoz létre egy lépésben.
- **Önálló műveletként**. Ezzel a módszerrel hoz létre egy különálló ASE, amely, az ASE. Ez a módszer egy olyan speciális folyamat ASE létrehozása. Használhatja az ILB ASE létrehozása.
- **Az Azure Resource Manager-sablon**. Ez a módszer van a tapasztalt felhasználók számára. További információkért lásd: [ASE létrehozása sablonból][MakeASEfromTemplate].

Külső ASE rendelkezik egy nyilvános virtuális IP-CÍMEK, ami azt jelenti, hogy az alkalmazások az ASE minden HTTP/HTTPS-forgalmat eléri-e az internetről elérhető IP-címet. Az ilb ASE rendelkezik az alhálózatról, az ASE által használt IP-címet. Az ILB ASE környezetben üzemeltetett alkalmazások nem csatlakoznak közvetlenül az internethez.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Az ASE és a egy App Service-csomag létrehozása együtt ##

Az App Service-csomag egy olyan tároló, az alkalmazásokat. Ha létrehozott egy alkalmazást az App Service-ben, válasszon, vagy hozzon létre egy App Service-csomagot. App Service Environment-környezetek tartsa App Service-csomagok, és az App Service-csomagok az alkalmazások tárolására.

ASE létrehozása egy App Service-csomag létrehozása közben:

1. Az a [az Azure portal](https://portal.azure.com/)válassza **erőforrás létrehozása** > **Web + mobil** > **webalkalmazás**.

    ![Webalkalmazás létrehozása][1]

1. Válassza ki előfizetését. Az alkalmazás és az ASE jönnek létre az ugyanazon előfizetésben található.

1. Válasszon ki vagy hozzon létre egy erőforráscsoportot. Az erőforráscsoportok egységként kezelheti a kapcsolódó Azure-erőforrásokat. Erőforráscsoportok is hasznosak, ha az alkalmazások szerepköralapú hozzáférés-vezérlés szabályok létrehozása. További információkért lásd: a [Azure Resource Manager áttekintése][ARMOverview].

1. Válassza ki az operációs rendszer (Windows, Linux vagy a Docker). 

1. Az App Service-csomagot, majd válassza ki és **hozzon létre új**. Linuxos web apps és a Windows web Apps alkalmazások azonos App Service-csomagot nem lehet, de az azonos App Service-környezet is lehet. 

    ![Új App Service-csomag][2]

1. Az a **hely** legördülő listában válassza ki a régiót, ahol szeretné az ASE létrehozása. Ha egy meglévő ASE választja, az új ASE Környezetek nem jön létre. Az App Service-csomag az ASE-t választotta, létrejön. 

1. Válassza ki **tarifacsomag**, és válassza ki az egyik a **elkülönített** díjköteles termékváltozatok. Ha úgy dönt, egy **elkülönített** Termékváltozat kártya és a egy helyet, amely nem az ASE, egy új ASE jön létre az adott helyen. ASE létrehozása a folyamat indításához válassza **kiválasztása**. A **elkülönített** Termékváltozat csak az ASE együtt érhető el. Még nem használható semmilyen más díjszabási Termékváltozat az ASE környezetben más, **elkülönített**. 

    ![Tarifacsomag][3]

1. Adja meg az ASE nevét. Ezt a nevet használja az alkalmazások a megcímezhető nevében. Ha a neve, az ASE _appsvcenvdemo_, a tartománynév *. appsvcenvdemo.p.azurewebsites.net*. Ha létrehoz egy alkalmazást nevű *mytestapp*, címezhetővé válnak, mytestapp.appsvcenvdemo.p.azurewebsites.net. A név szóközt nem használható. Nagybetűs karaktereket használ, ha a tartománynév ilyen nevű kisbetűs teljes verzióját.

    ![Új App Service-csomag neve][4]

1. Adja meg az Azure virtuális hálózat adatait. Ezek közül bármelyikre **létrehozása új** vagy **válasszon meglévő**. Választhatja egy meglévő virtuális hálózat csak akkor, ha a virtuális hálózat rendelkezik a kiválasztott régióban érhető el. Ha **hozzon létre új**, adja meg a virtuális hálózat nevét. Ilyen nevű új Resource Manager VNet létrejön. Akkor használja, a címtér `192.168.250.0/23` a kiválasztott régióban. Ha **meglévő**, kell tennie:

    a. Válassza ki a virtuális hálózat címterülete, ha egynél több.

    b. Adja meg az új alhálózat nevét.

    c. Válassza ki az alhálózat méretét. *Fontos, hogy válassza ki a méretét elég nagy az ASE jövőbeli növekedésének megfelelően.* Javasoljuk, hogy `/25`, amely 128 címet tartalmaz, és képes kezelni egy maximális méretű ASE Környezetet. Nem ajánlott `/28`, például mert csak 16 címek érhetők el. Infrastruktúra legalább hét címeket használ, és a egy másik 5 az Azure-hálózatot használ. Az egy `/28` alhálózat, hogy hagyta App Service-csomag az ILB ASE-példányok legfeljebb 4 App Service-csomag példányok külső ASE méretezés és csak 3.

    d. Válassza ki az alhálózati IP-címtartományt.

1. Válassza ki **létrehozás** az ASE létrehozása. Ez a folyamat is hoz létre az App Service-csomag és az alkalmazás. Az ASE App Service-csomagot és alkalmazást is ugyanabban az előfizetésben és ugyanazt az erőforráscsoportot is. Ha az ASE környezetnek szüksége van egy külön erőforráscsoportot, vagy ha az ILB ASE környezetben van szüksége, kövesse az ASE létrehozása saját maga.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Az ASE és a egy egyéni Docker-rendszerkép együttes használata Linux webalkalmazás létrehozása

1. Az a [az Azure portal](https://portal.azure.com/), **erőforrás létrehozása** > **Web + mobil** > **Web App for containers szolgáltatásban.** 

    ![Webalkalmazás létrehozása][7]

1. Válassza ki előfizetését. Az alkalmazás és az ASE jönnek létre az ugyanazon előfizetésben található.

1. Válasszon ki vagy hozzon létre egy erőforráscsoportot. Az erőforráscsoportok egységként kezelheti a kapcsolódó Azure-erőforrásokat. Erőforráscsoportok is hasznosak, ha az alkalmazások szerepköralapú hozzáférés-vezérlés szabályok létrehozása. További információkért lásd: a [Azure Resource Manager áttekintése][ARMOverview].

1. Az App Service-csomagot, majd válassza ki és **hozzon létre új**. Linuxos web apps és a Windows web Apps alkalmazások azonos App Service-csomagot nem lehet, de az azonos App Service-környezet is lehet. 

    ![Új App Service-csomag][8]

1. Az a **hely** legördülő listában válassza ki a régiót, ahol szeretné az ASE létrehozása. Ha egy meglévő ASE választja, az új ASE Környezetek nem jön létre. Az App Service-csomag az ASE-t választotta, létrejön. 

1. Válassza ki **tarifacsomag**, és válassza ki az egyik a **elkülönített** díjköteles termékváltozatok. Ha úgy dönt, egy **elkülönített** Termékváltozat kártya és a egy helyet, amely nem az ASE, egy új ASE jön létre az adott helyen. ASE létrehozása a folyamat indításához válassza **kiválasztása**. A **elkülönített** Termékváltozat csak az ASE együtt érhető el. Még nem használható semmilyen más díjszabási Termékváltozat az ASE környezetben más, **elkülönített**. 

    ![Tarifacsomag][3]

1. Adja meg az ASE nevét. Ezt a nevet használja az alkalmazások a megcímezhető nevében. Ha a neve, az ASE _appsvcenvdemo_, a tartománynév *. appsvcenvdemo.p.azurewebsites.net*. Ha létrehoz egy alkalmazást nevű *mytestapp*, címezhetővé válnak, mytestapp.appsvcenvdemo.p.azurewebsites.net. A név szóközt nem használható. Nagybetűs karaktereket használ, ha a tartománynév ilyen nevű kisbetűs teljes verzióját.

    ![Új App Service-csomag neve][4]

1. Adja meg az Azure virtuális hálózat adatait. Ezek közül bármelyikre **létrehozása új** vagy **válasszon meglévő**. Választhatja egy meglévő virtuális hálózat csak akkor, ha a virtuális hálózat rendelkezik a kiválasztott régióban érhető el. Ha **hozzon létre új**, adja meg a virtuális hálózat nevét. Ilyen nevű új Resource Manager VNet létrejön. Akkor használja, a címtér `192.168.250.0/23` a kiválasztott régióban. Ha **meglévő**, kell tennie:

    a. Válassza ki a virtuális hálózat címterülete, ha egynél több.

    b. Adja meg az új alhálózat nevét.

    c. Válassza ki az alhálózat méretét. *Fontos, hogy válassza ki a méretét elég nagy az ASE jövőbeli növekedésének megfelelően.* Javasoljuk, hogy `/25`, amely 128 címet tartalmaz, és képes kezelni egy maximális méretű ASE Környezetet. Nem ajánlott `/28`, például mert csak 16 címek érhetők el. Infrastruktúra legalább hét címeket használ, és a egy másik 5 az Azure-hálózatot használ. Az egy `/28` alhálózat, hogy hagyta App Service-csomag az ILB ASE-példányok legfeljebb 4 App Service-csomag példányok külső ASE méretezés és csak 3.

    d. Válassza ki az alhálózati IP-címtartományt.

1.  Válassza a "Tároló konfigurálása."
    * Adja meg az egyéni rendszerkép nevét (is használhatja az Azure Container Registry, Docker Hub és a saját privát regisztrációs adatbázis). Ha nem szeretné használni a saját egyéni tárolóját, csak a saját kódjára és egy beépített rendszerkép használata az App Service-ben linuxon, az a fenti utasítások szerint. 

    ![Tároló konfigurálása][9]

1. Válassza ki **létrehozás** az ASE létrehozása. Ez a folyamat is hoz létre az App Service-csomag és az alkalmazás. Az ASE App Service-csomagot és alkalmazást is ugyanabban az előfizetésben és ugyanazt az erőforráscsoportot is. Ha az ASE környezetnek szüksége van egy külön erőforráscsoportot, vagy ha az ILB ASE környezetben van szüksége, kövesse az ASE létrehozása saját maga.


## <a name="create-an-ase-by-itself"></a>Önmagában az ASE létrehozása ##

Ha egy ASE önálló hoz létre, azt nem szerepel. Az üres ASE továbbra is havi díjfizetési kötelezettséggel az infrastruktúra. Kövesse az alábbi lépéseket az ILB ASE létrehozása, vagy ASE létrehozása a saját erőforráscsoport. Miután létrehozta az ASE-t, létrehozhat alkalmazásokat, a szokásos folyamat használatával. Válassza az új ASE Környezetet helyként.

1. Keresés az Azure Marketplace **App Service Environment-környezet**, vagy válasszon ki **erőforrás létrehozása** > **webes Mobile** > **alkalmazás Környezet szolgáltatás**. 

1. Adja meg az ASE nevét. Ezt a nevet használja az ASE környezetben létrehozott alkalmazások. Ha a név *mynewdemoase*, az altartomány nevével együtt van *. mynewdemoase.p.azurewebsites.net*. Ha létrehoz egy alkalmazást nevű *mytestapp*, címezhetővé válnak, mytestapp.mynewdemoase.p.azurewebsites.net. A név szóközt nem használható. Nagybetűs karaktereket használ, a tartománynév-e a név kisbetűs teljes verzióját. ILB használja, ha az ASE neve nincs használatban az altartomány, de ehelyett explicit módon meghatározva ASE létrehozása során.

    ![ASE elnevezése][5]

1. Válassza ki előfizetését. Ez az előfizetés akkor is, amelyiket az ASE összes alkalmazást használó. Az ASE nem helyezhető el, amely egy másik előfizetésben található virtuális hálózaton.

1. Válassza ki, vagy adjon meg egy új erőforráscsoportot. Az ASE használt erőforráscsoport azonosnak kell lennie, amely a virtuális hálózat szolgál. Ha egy meglévő Vnetet, az erőforráscsoport kiválasztása az ASE módosul, a virtuális hálózat megfelelően. *Az ASE létrehozhat egy erőforráscsoportot, amely eltér a virtuális hálózatok közötti erőforráscsoportot egy Resource Manager-sablon használatakor.* Az ASE létrehozása sablonból: [App Service-környezet létrehozása sablonból][MakeASEfromTemplate].

    ![Erőforráscsoport kiválasztása][6]

1. Válassza ki a virtuális hálózat és a helyet. Hozzon létre egy új virtuális hálózatot, vagy egy meglévő virtuális hálózat kiválasztása: 

    * Új virtuális hálózat kiválasztása esetén új nevet és helyet adhat meg. 
    
    * Az új VNet a cím-tartományt 192.168.250.0/23 és a egy alapértelmezett nevű alhálózattal rendelkezik. Az alhálózat 192.168.250.0/24 definiálható. Válassza ki a Resource Manager VNet csak. A **VIP típusa** kijelölés határozza meg, ha az ASE közvetlenül hozzáférni az internetről (külső), vagy egy ILB használ. Ezek a beállítások kapcsolatos további információkért lásd: [létrehozása és használata az App Service-környezet belső terheléselosztó][MakeILBASE]. 

      * Ha **külső** számára a **VIP típusa**, kiválaszthatja, hogy hány külső IP-címeket a rendszer a létrehozott IP-alapú SSL célokra. 
    
      * Ha **belső** számára a **VIP típusa**, meg kell adnia a tartományt, az ASE-t használja. Az ASE üzembe helyezhető nyilvános vagy magánhálózati címtartomány használó virtuális hálózathoz. Nyilvános címtartományt a virtuális hálózat használatához szüksége előre a virtuális hálózat létrehozásához. 
    
    * Ha egy meglévő Vnetet, egy új alhálózatot az ASE létrehozásakor jön létre. *A portálon egy előre létrehozott alhálózat nem használható. Létrehozhat egy ASE meglévő-alhálózattal, ha a Resource Manager-sablont használ.* Az ASE létrehozása sablonból: [App Service-környezet létrehozása sablonból][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>App Service-környezet v1 ##

Az App Service Environment (az ASEv1) első verziója példánya továbbra is létrehozhat. A folyamat elindításához, keressen a piactéren **App Service Environment-környezet v1**. Az ASE-t ugyanúgy, ahogy a különálló ASE létrehozása hoz létre. Amikor elkészült, az asev1-ben van két előtérrendszerekből és két. Az asev1-ben a előtérrendszerekből és kell kezelni. A rendszer automatikusan hozzáadja az App Service-csomagok létrehozásakor. Az előtérrendszerek a HTTP/HTTPS-végpontként viselkednek, és küldeni a forgalmat a feldolgozók. A munkavállalók a szerepköröket, amelyek az alkalmazások üzemeltetéséhez. Az ASE létrehozását követően módosíthatja a előtérrendszerekből és mennyisége. 

Az ASEv1 kapcsolatos további információkért lásd: [az App Service Environment v1 bemutatása][ASEv1Intro]. További információkat a skálázásról, kezelése és figyelése az asev1-ben, lásd: [App Service Environment konfigurálása][ConfigureASEv1].

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
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
