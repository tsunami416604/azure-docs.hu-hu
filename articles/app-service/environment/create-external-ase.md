---
title: Külső Azure App Service-környezet létrehozása
description: Ismerteti, hogyan hozzon létre egy App Service-környezet egy alkalmazás vagy önálló létrehozása során
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
ms.openlocfilehash: debfff03ea9a4de4fb2cd69779d58709a6a3a34f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="create-an-external-app-service-environment"></a>Külső App Service-környezet létrehozása #

Az Azure App Service Environment az Azure App Service egy olyan példánya, amelyet egy Azure virtuális hálózat alhálózatában helyeztek üzembe. Az App Service Environment (ASE) üzembe helyezésének két módja van:

- Egy virtuális IP-cím vagy külső IP-cím, azaz külső ASE használatával.
- A virtuális IP-címre a belső IP-címet gyakran nevezik egy ILB ASE, mert a belső végpont nem egy belső terheléselosztón (ILB).

Ez a cikk bemutatja, hogyan hozzon létre egy külső ASE. A ASE áttekintését lásd: [az App Service Environment bemutatása][Intro]. Egy ILB ASE létrehozásával kapcsolatos további információkért lásd: [létrehozása és használata egy ILB ASE][MakeILBASE].

## <a name="before-you-create-your-ase"></a>A ASE létrehozása előtt ##

Miután létrehozta a ASE, nem módosítható a következő:

- Hely
- Előfizetés
- Erőforráscsoport
- A virtuális hálózaton használt
- Használt
- Alhálózat mérete

> [!NOTE]
> Ha egy virtuális hálózat kiválasztása, és adjon meg egy alhálózatot, győződjön meg arról, hogy elég nagy ahhoz, hogy megfeleljen a jövőbeli növekedésre. Azt javasoljuk, hogy a méretet `/25` 128-címekkel.
>

## <a name="three-ways-to-create-an-ase"></a>Három módon hozhat létre egy ASE ##

Hozzon létre egy ASE három módja van:

- **Az App Service-csomag létrehozása során**. Ezzel a módszerrel hoz létre a ASE és az App Service-csomag egy lépésben.
- **Önálló műveletként**. Ez a módszer hoz létre önálló ASE, ez az egy a lapokat ASE. Ez a módszer az egy speciális folyamat egy ASE létrehozásához. Használhat egy ILB egy ASE létrehozásához.
- **Az Azure Resource Manager sablon**. Ez a módszer van a tapasztalt felhasználók számára. További információkért lásd: [egy ASE létrehozása sablonból][MakeASEfromTemplate].

Egy külső ASE rendelkezik nyilvános virtuális IP-címhez, ami azt jelenti, hogy az összes HTTP/HTTPS-forgalmat a ASE található alkalmazásokra mutató találatok internetről elérhető IP-címet. Egy ASE egy ILB a rendelkezik a ASE használják az alhálózat IP-címeit. A-Példánynak környezetben futó alkalmazások nem közvetlenül elérhetővé tenni az interneten.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Hozzon létre egy ASE és az App Service-csomag együtt ##

Az App Service-csomag egy olyan tároló, az alkalmazások. Ha alkalmazást hoz létre az App Service-ben, akkor válasszon, vagy hozzon létre az App Service-csomag. App Service-környezetek tartsa App Service-csomagok, és az App Service-csomagok tárolására az alkalmazások.

Egy ASE létrehozása, az App Service-csomag létrehozása során:

1. Az a [Azure-portálon](https://portal.azure.com/), jelölje be **hozzon létre egy erőforrást** > **Web + mobil** > **webalkalmazás**.

    ![Webalkalmazás létrehozása][1]

2. Válassza ki előfizetését. Az alkalmazás és a ASE azonos előfizetések jönnek létre.

3. Válasszon ki vagy hozzon létre egy erőforráscsoportot. Az erőforráscsoportok egységként kezelheti a kapcsolódó Azure-erőforrások. Erőforráscsoportok is hasznosak, ha az alkalmazások szerepköralapú hozzáférés-vezérlés szabályok létrehozása. További információkért lásd: a [Azure Resource Manager áttekintése][ARMOverview].

4. Válassza ki az operációs rendszer. 

    * Egy új, előzetes verziójú funkciók üzemeltető egy Linux app Service-környezetben, így javasoljuk, hogy nem kell hozzáadni Linux alkalmazások be egy ASE termelési számítási feladatokhoz folyamatban. 
    * A Linux-alkalmazás hozzáadása egy ASE be azt jelenti, hogy a ASE is csak előzetes módban működik. 

5. Az App Service-csomagot, majd válassza ki és **hozzon létre új**. Linux-webalkalmazások és a Windows web apps a azonos App Service-csomag nem lehet, de a azonos App Service Environment-környezetben lehet. 

    ![Új App Service-csomag][2]

6. Az a **hely** legördülő listában válassza ki a régió, ahol szeretné létrehozni a ASE. Ha egy meglévő ASE, egy új ASE nem jön létre. Az App Service-csomag a kiválasztott ASE jön létre. 

    > [!NOTE]
    > Linux ASE csakis 6 régiókban pillanatnyilag: **USA nyugati régiója, USA keleti régiója, Nyugat-Európában, Észak-Európa, Kelet-Ausztrália, Délkelet-Ázsia.** Mivel a ASE Linux előzetes verziójú funkciók, ne válassza egy ASE, hogy ez az előzetes kiadás előtt hozna létre.
    >

7. Válassza ki **tarifacsomag**, és válasszon a **elszigetelt** termékváltozatok árképzési. Ha úgy dönt, egy **elszigetelt** SKU kártya és olyan helyre, amely nem egy ASE, egy új ASE azon a helyen jön létre. Válassza ki a megkezdéséhez hozzon létre egy ASE, **válasszon**. A **elszigetelt** SKU csak egy mértékéig párhuzamosan érhető el. Is nem használhatja semmilyen más árképzési SKU-környezetben eltérő **elszigetelt**. 

    * A Linux a ASE Preview-ban 50 %-os kedvezményes alkalmazza a (lesz kedvezmény nem maga a ASE simán-díja) különítve másikra.

    ![Tarifacsomag kiválasztása][3]

8. Adja meg a ASE nevét. Ez a név az alkalmazások megcímezhető neve szerepel. Ha a név a hajlamosnak _appsvcenvdemo_, a tartománynév *. appsvcenvdemo.p.azurewebsites.net*. Ha nevű alkalmazást hoz létre *mytestapp*, megcímezhető: mytestapp.appsvcenvdemo.p.azurewebsites.net. A névben szóköz nem használható. Használhatnak nagybetűket, ha a tartománynév ilyen nevű kisbetűs teljes verzióját.

    ![Új App Service-csomag neve][4]

9. Adja meg az Azure virtuális hálózati adatait. Válassza ki vagy **új** vagy **válasszon meglévő**. Kiválaszthatja azokat egy meglévő virtuális hálózatot áll rendelkezésre, csak ha egy virtuális hálózat szerepel a kiválasztott régióban. Ha **hozzon létre új**, adjon meg egy nevet a virtuális hálózat. Ilyen nevű új erőforrás-kezelő VNet létrejön. A címterület használ `192.168.250.0/23` a kiválasztott régióban. Ha **meglévő**, kell:

    a. A virtuális hálózat címterülete, akkor válassza, ha egynél több.

    b. Adjon meg egy új alhálózat neve.

    c. Adja meg az alhálózat. *Ne felejtse el kiválasztásához elég nagy a ASE jövőbeli növekedésének megfelelően.* Ajánlott `/25`, amely 128-címekkel rendelkezik, és kezelni tud a maximális méretű ASE. Nem ajánlott `/28`, például mert csak 16 címek érhetők el. Infrastruktúra legalább hét címeket használ, és egy másik 5 Azure Networking használja. Az egy `/28` alhálózathoz folyamatban hagyta App Service-csomag egy ILB ASE példányának legfeljebb egy külső ASE 4 az alkalmazásszolgáltatási csomag példányok méretezési és csak 3.

    d. Válassza ki azt az alhálózati IP-címtartomány.

10. Válassza ki **létrehozása** a ASE létrehozásához. Ez a folyamat is létrehoz az App Service-csomag és az alkalmazás. A ASE App Service-csomagot, és az alkalmazás a ugyanahhoz az előfizetéshez és is a ugyanabban az erőforráscsoportban vannak. Ha a ASE kell egy külön erőforráscsoportot, vagy ha egy ILB ASE van szüksége, létrehozásához hajtsa végre a lépéseket egy ASE önmagában.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Egy ASE és egy egyéni Docker lemezképpel együtt a Linux-webalkalmazás létrehozása

1. Az a [Azure-portálon](https://portal.azure.com/), **hozzon létre egy erőforrást** > **Web + mobil** > **Web App az tárolókat.** 

    ![Webalkalmazás létrehozása][7]

2. Válassza ki előfizetését. Az alkalmazás és a ASE azonos előfizetések jönnek létre.

3. Válasszon ki vagy hozzon létre egy erőforráscsoportot. Az erőforráscsoportok egységként kezelheti a kapcsolódó Azure-erőforrások. Erőforráscsoportok is hasznosak, ha az alkalmazások szerepköralapú hozzáférés-vezérlés szabályok létrehozása. További információkért lásd: a [Azure Resource Manager áttekintése][ARMOverview].

4. Az App Service-csomagot, majd válassza ki és **hozzon létre új**. Linux-webalkalmazások és a Windows web apps a azonos App Service-csomag nem lehet, de a azonos App Service Environment-környezetben lehet. 

    ![Új App Service-csomag][8]

5. Az a **hely** legördülő listában válassza ki a régió, ahol szeretné létrehozni a ASE. Ha egy meglévő ASE, egy új ASE nem jön létre. Az App Service-csomag a kiválasztott ASE jön létre. 

    > [!NOTE]
    > Linux ASE csakis 6 régiókban pillanatnyilag: **USA nyugati régiója, USA keleti régiója, Nyugat-Európában, Észak-Európa, Kelet-Ausztrália, Délkelet-Ázsia.** Mivel a ASE Linux előzetes verziójú funkciók, ne válassza egy ASE, hogy ez az előzetes kiadás előtt hozna létre.
    >

6. Válassza ki **tarifacsomag**, és válasszon a **elszigetelt** termékváltozatok árképzési. Ha úgy dönt, egy **elszigetelt** SKU kártya és olyan helyre, amely nem egy ASE, egy új ASE azon a helyen jön létre. Válassza ki a megkezdéséhez hozzon létre egy ASE, **válasszon**. A **elszigetelt** SKU csak egy mértékéig párhuzamosan érhető el. Is nem használhatja semmilyen más árképzési SKU-környezetben eltérő **elszigetelt**. 

    * A Linux a ASE Preview-ban 50 %-os kedvezményes alkalmazza a (lesz kedvezmény nem maga a ASE simán-díja) különítve másikra.

    ![Tarifacsomag kiválasztása][3]

7. Adja meg a ASE nevét. Ez a név az alkalmazások megcímezhető neve szerepel. Ha a név a hajlamosnak _appsvcenvdemo_, a tartománynév *. appsvcenvdemo.p.azurewebsites.net*. Ha nevű alkalmazást hoz létre *mytestapp*, megcímezhető: mytestapp.appsvcenvdemo.p.azurewebsites.net. A névben szóköz nem használható. Használhatnak nagybetűket, ha a tartománynév ilyen nevű kisbetűs teljes verzióját.

    ![Új App Service-csomag neve][4]

8. Adja meg az Azure virtuális hálózati adatait. Válassza ki vagy **új** vagy **válasszon meglévő**. Kiválaszthatja azokat egy meglévő virtuális hálózatot áll rendelkezésre, csak ha egy virtuális hálózat szerepel a kiválasztott régióban. Ha **hozzon létre új**, adjon meg egy nevet a virtuális hálózat. Ilyen nevű új erőforrás-kezelő VNet létrejön. A címterület használ `192.168.250.0/23` a kiválasztott régióban. Ha **meglévő**, kell:

    a. A virtuális hálózat címterülete, akkor válassza, ha egynél több.

    b. Adjon meg egy új alhálózat neve.

    c. Adja meg az alhálózat. *Ne felejtse el kiválasztásához elég nagy a ASE jövőbeli növekedésének megfelelően.* Ajánlott `/25`, amely 128-címekkel rendelkezik, és kezelni tud a maximális méretű ASE. Nem ajánlott `/28`, például mert csak 16 címek érhetők el. Infrastruktúra legalább hét címeket használ, és egy másik 5 Azure Networking használja. Az egy `/28` alhálózathoz folyamatban hagyta App Service-csomag egy ILB ASE példányának legfeljebb egy külső ASE 4 az alkalmazásszolgáltatási csomag példányok méretezési és csak 3.

    d. Válassza ki azt az alhálózati IP-címtartomány.

9.  Válassza a "Tároló konfigurálása."
    * Adja meg az egyéni rendszerkép nevének (használható Azure tároló beállításjegyzék, Docker Hub és a saját személyes beállításjegyzék). Ha nem szeretné használni a saját egyéni tároló, csak kapcsolja a kódot és egy beépített lemezképet használja az App Service Linux, a fenti utasítások használatával. 

    ! [A tároló konfigurálása] [9]

10. Válassza ki **létrehozása** a ASE létrehozásához. Ez a folyamat is létrehoz az App Service-csomag és az alkalmazás. A ASE App Service-csomagot, és az alkalmazás a ugyanahhoz az előfizetéshez és is a ugyanabban az erőforráscsoportban vannak. Ha a ASE kell egy külön erőforráscsoportot, vagy ha egy ILB ASE van szüksége, létrehozásához hajtsa végre a lépéseket egy ASE önmagában.


## <a name="create-an-ase-by-itself"></a>Hozzon létre egy ASE önmagában ##

Ha létrehoz egy ASE önálló, lejárt, semmi nem azt. Egy üres ASE továbbra is azt eredményezi azok háromszorosa infrastruktúra havi járnak. Kövesse az alábbi lépéseket egy ILB egy ASE létrehozásához, vagy hozzon létre egy ASE saját erőforráscsoportban. Miután létrehozta a ASE, létrehozhat alkalmazások azt a szokásos folyamat használatával. Jelölje ki az új ASE helyeként.

1. Keresse meg az Azure piactérről **App Service Environment-környezet**, vagy válasszon **hozzon létre egy erőforrást** > **webes mobil** > **App Környezet szolgáltatás**. 

2. Adja meg a ASE nevét. Ez a név a ASE létrehozott alkalmazások szolgál. Ha a név *mynewdemoase*, altartománynév van *. mynewdemoase.p.azurewebsites.net*. Ha nevű alkalmazást hoz létre *mytestapp*, megcímezhető: mytestapp.mynewdemoase.p.azurewebsites.net. A névben szóköz nem használható. Nagybetűs karaktereket használ, ha a tartománynév neve kisbetűs teljes verzióját. Egy ILB használatakor a ASE neve nem szerepel a altartomány, de ehelyett explicit módon megadott ASE létrehozása során.

    ![ASE elnevezése][5]

3. Válassza ki előfizetését. Ez az előfizetés fontos is, amelyek a ASE összes alkalmazást. A ASE nem helyezhető el, amely egy másik előfizetésben található a Vneten belül.

4. Válassza ki vagy adja meg egy új erőforráscsoportot. Az erőforráscsoport a ASE használt azonosnak kell lennie egy, a virtuális hálózat szolgál. Ha egy meglévő virtuális hálózatot választ ki, az erőforráscsoport kiválasztása a ASE a módosul, a virtuális hálózat megfelelően. *Egy erőforráscsoport, amely eltér a virtuális hálózat erőforráscsoport a Resource Manager-sablon egy ASE hozhatja létre.* Egy ASE létrehozása sablonból: [App Service-környezet létrehozása sablonból][MakeASEfromTemplate].

    ![Erőforráscsoport kiválasztása][6]

5. Válassza ki a virtuális hálózat és a helyet. Hozzon létre új virtuális hálózatot, vagy válasszon egy meglévő virtuális hálózatot: 

    * Ha új virtuális hálózatot választja, megadhatja a nevét és helyét. Ha azt tervezi, a ASE a Linux-alkalmazások futtatására, csak a 6 régiók pillanatnyilag támogatottak: **USA nyugati régiója, USA keleti régiója, Nyugat-Európában, Észak-Európa, Kelet-Ausztrália, Délkelet-Ázsia.** 
    
    * Az új VNet rendelkezik, a cím tartomány 192.168.250.0/23 és alapértelmezett nevű alhálózat. Az alhálózat 192.168.250.0/24 típusúként van definiálva. Egy erőforrás-kezelő virtuális hálózat jelölhet ki. A **VIP típus** kijelölés határozza meg, ha a ASE közvetlenül elérhető az internetről (külső), vagy ha egy ILB használ. A beállításokkal kapcsolatos további információkért lásd: [létrehozása és használata az App Service-környezet belső terheléselosztót][MakeILBASE]. 

      * Ha **külső** a a **VIP típus**, kiválaszthatja, hogy hány külső IP-címeket a rendszer IP-alapú SSL célokra jön létre. 
    
      * Ha **belső** a a **VIP típus**, meg kell adnia a tartományt, amelyikhez a ASE használja. Egy ASE egy virtuális hálózat által használt nyilvános és magánhálózati címtartományai történő telepítése. Egy nyilvános-címtartománnyal rendelkező egy Vnetet használatához szüksége időben a VNet létrehozásához. 
    
    * Ha egy meglévő virtuális hálózatot választ, egy új alhálózat létre van hozva a ASE létrehozásakor. *Nem használhat egy korábban létrehozott alhálózati a portálon. Létrehozhat egy ASE egy létező alhálózatot a Resource Manager-sablon használatakor.* Egy ASE létrehozása sablonból: [egy App Service Environment-környezet létrehozása sablonból][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>App Service-környezet v1 ##

Továbbra is létrehozhat az első verzió App Service Environment-környezet (ASEv1) példányát. A folyamat elindításához keresése a piactéren **App Service Environment-környezet v1**. A ASE hoz létre az Ön által létrehozott az önálló ASE azonos módon. Amikor elkészült, a ASEv1 van két előtér-webkiszolgálóinak és két alkalmazottak. A ASEv1 az előtér-webkiszolgálóinak és alkalmazottak kell kezelni. Azok még nem automatikusan az App Service-csomagok létrehozásakor. Az előtér-webkiszolgálóinak működjön, és a HTTP/HTTPS-végpontnak, és a munkavállalók forgalmat küldeni. A munkavállalók a szerepköröket, amelyek az alkalmazások tárolására. Az előtér-webkiszolgálóinak és alkalmazottak mennyiségét a ASE létrehozása után módosíthatja. 

ASEv1 kapcsolatos további információkért lásd: [az App Service Environment-környezet v1 bemutatása][ASEv1Intro]. További információ a méretezés, kezelésére és ASEv1, figyelés: [az App Service-környezetek konfigurálása][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
