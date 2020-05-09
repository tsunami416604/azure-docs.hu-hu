---
title: ELAVULT Kanári-kiadás az Azure DC/OS-fürtön található vamp-mel
description: A kitatarozás használata a Kanári-kiadási szolgáltatásokhoz és az intelligens forgalmi szűrés alkalmazása Azure Container Service DC/OS-fürtön
author: gggina
ms.service: container-service
ms.topic: conceptual
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: dfdf6e1f8edfb4dafaf93e62090ed51878f9b2aa
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734824"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>ELAVULT Az Azure Container Service DC/OS-fürtön található vamp kiadási szolgáltatásokkal rendelkező csicsörke

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Ebben az útmutatóban a Azure Container Service egy DC/OS-fürttel rendelkező Vampt állítottunk be. A "Sava" nevű kisegítő szolgáltatás a "Száva" változatot, majd az intelligens forgalmi szűrés alkalmazásával oldja fel a szolgáltatás inkompatibilitását a Firefoxban. 

> [!TIP] 
> Ebben az útmutatóban a vamp egy DC/OS-fürtön fut, de a Vampt a Kubernetes-mel is használhatja Orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>A Kanári-verziókról és a Vampről


A [Kanári-felszabadítás](https://martinfowler.com/bliki/CanaryRelease.html) egy olyan, az innovatív szervezetek által elfogadott intelligens üzembe helyezési stratégia, mint a Netflix, a Facebook és a Spotify. Ez egy olyan megközelítés, amely logikus, mivel csökkenti a problémákat, bevezeti a biztonsági hálókat, és növeli az innovációt. Miért nem minden vállalat használja ezt? A CI/CD-folyamatok kiterjesztése a Kanári-stratégiákba kiterjedő összetettséget tesz szükségessé, és széleskörű devops ismereteket és tapasztalatokat igényel. Ez elég ahhoz, hogy letiltsa a kisebb vállalatokat és a vállalatokat, még az első lépések előtt. 

A [vamp](https://vamp.io/) egy nyílt forráskódú rendszer, amely megkönnyíti a váltást, és lehetővé teszi a Kanári-funkciók használatát a kívánt tároló-ütemező számára. A vamp Canary-funkciója meghaladja a százalékos alapú bevezetést. A forgalom szűrése és felosztása a feltételek széles köre alapján lehetséges, például adott felhasználók, IP-tartományok vagy eszközök megcélzására. A vamp nyomon követi és elemzi a teljesítménymutatókat, így lehetővé teszi a valós adatokon alapuló automatizálást. Beállíthatja a hibák automatikus visszaállítását, vagy a terhelés vagy a késés alapján méretezheti az egyes szolgáltatási változatokat.

## <a name="set-up-azure-container-service-with-dcos"></a>A Azure Container Service beállítása a DC/OS-vel



1. [Helyezzen üzembe egy DC/os fürtöt](container-service-deployment.md) egy főkiszolgálóval és két alapértelmezett méretű ügynökkel. 

2. [Hozzon létre egy SSH-alagutat](../container-service-connect.md) a DC/os fürthöz való kapcsolódáshoz. Ez a cikk azt feltételezi, hogy a fürtön a 80-es helyi porton alagút található.


## <a name="set-up-vamp"></a>A vamp beállítása

Most, hogy már fut egy DC/OS-fürt, a DC/OS felhasználói felületéről telepítheti a Vampt\/(http:/localhost: 80). 

![A DC/OS UI felhasználói felülete](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

A telepítés két lépésben történik:

1. **Telepítse a Elasticsearch**.

2. Ezután **telepítse a vampt** a vamp DC/os Universe csomag telepítésével.

### <a name="deploy-elasticsearch"></a>Elasticsearch üzembe helyezése

A vamp használatához Elasticsearch szükséges a metrikák gyűjtéséhez és az összesítéshez. A [Magneticio Docker-rendszerképekkel](https://hub.docker.com/r/magneticio/elastic/) kompatibilis vamp Elasticsearch-verem helyezhető üzembe.

1. A DC/OS felhasználói felületén válassza a **szolgáltatások** lehetőséget, és kattintson a **szolgáltatás telepítése**elemre.

2. Válassza ki a **JSON-módot** az **új szolgáltatás telepítése** előugró ablakban.

   ![JSON-mód kiválasztása](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Illessze be a következő JSON-t. Ez a konfiguráció 1 GB RAM memóriával futtatja a tárolót, valamint egy alapszintű állapot-ellenőrzését a Elasticsearch-porton.
  
   ```JSON
   {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
   }
   ```
  

3. Kattintson a **telepítés**elemre.

   A DC/OS üzembe helyezi a Elasticsearch tárolót. A **szolgáltatások** oldalon nyomon követheti a folyamat előrehaladását.  

   ![telepíti az e-t? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>A vamp üzembe helyezése

A Elasticsearch jelentések **futtatása**után felveheti a vamp DC/os Universe csomagot. 

1. Nyissa meg az **univerzumot** , és keressen rá a **vamp**kifejezésre. 
   ![Vamp a DC/OS Universe-ben](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Kattintson a **telepítés** elemre a vamp-csomag mellett, majd válassza a **speciális telepítés**lehetőséget.

3. Görgessen lefelé, és írja be a következő elasticsearch `http://elasticsearch.marathon.mesos:9200`-URL-címet:. 

   ![Adja meg a Elasticsearch URL-címét](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Kattintson a **felülvizsgálat és telepítés**gombra, majd kattintson a **telepítés** gombra a telepítés elindításához.  

   A DC/OS üzembe helyezi az összes szükséges vamp-összetevőt. A **szolgáltatások** oldalon nyomon követheti a folyamat előrehaladását.
  
   ![A vamp üzembe helyezése Universe-csomagként](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Az üzembe helyezés befejezését követően a vamp felhasználói felülete a következőket érheti el:

   ![Vamp-szolgáltatás a DC/OS-ben](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Vamp – felhasználói felület](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Az első szolgáltatás üzembe helyezése

Most, hogy a vamp már működik, üzembe helyezi a szolgáltatást egy tervből. 

A legegyszerűbb formájában a [vamp tervrajza](https://docs.vamp.io/how-vamp-works/vamp-and-kubernetes#vamp-deployments) a telepítendő végpontokat (átjárókat), fürtöket és szolgáltatásokat ismerteti. A vamp a fürtök használatával csoportosítja ugyanazon szolgáltatás különböző változatait logikai csoportokba a Kanári-kibocsátáshoz vagy A/B teszteléshez.  

Ez a forgatókönyv egy Száva nevű, a 1,0-es verzióban található, a [**savat**](https://github.com/magneticio/sava-product)használó minta monolitikus alkalmazást használ. A Monolith egy Docker-tárolóba van csomagolva, amely a magneticio/Sava: 1.0.0 alatt található Docker hub-ban. Az alkalmazás általában a 8080-es porton fut, de ebben az esetben az 9050-es porton keresztül szeretné közzétenni. Az alkalmazás üzembe helyezése a vamp segítségével egy egyszerű terv használatával.

1. Ugrás az üzemelő **példányokra**.

2. Kattintson a **Hozzáadás** parancsra.

3. Illessze be a következő terv YAML. Ez a terv egy olyan fürtöt tartalmaz, amely csak egy szolgáltatási változattal rendelkezik, amely egy későbbi lépésben módosul:

   ```YAML
   name: sava                        # deployment name
   gateways:
    9050: sava_cluster/webport      # stable endpoint
   clusters:
    sava_cluster:               # cluster to create
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
   ```

4. Kattintson a **Save** (Mentés) gombra. A vamp elindítja az üzembe helyezést.

A központi telepítés a **központi telepítések** lapon jelenik meg. Kattintson a központi telepítésre az állapotának figyeléséhez.

![Vamp felhasználói felület – Száva üzembe helyezése](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![Száva szolgáltatás a vamp felhasználói felületén](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

A rendszer két átjárót hoz létre, amelyek az **átjárók** lapon vannak felsorolva:

* stabil végpont a futó szolgáltatás eléréséhez (9050-es port) 
* egy vamp által felügyelt belső átjáró (További információ az átjáróról később). 

![Vamp felhasználói felület – Sava-átjárók](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

A Száva szolgáltatás már telepítve van, de nem fér hozzá külsőleg, mert a Azure Load Balancer még nem tudja továbbítani a forgalmat. A szolgáltatás eléréséhez frissítse az Azure hálózati konfigurációját.


## <a name="update-the-azure-network-configuration"></a>Az Azure hálózati konfigurációjának frissítése

A vamp üzembe helyezte a Száva szolgáltatást a DC/OS-ügynökök csomópontjain, így stabil végpontot mutat be a 9050-as porton. Ha a szolgáltatást a DC/OS fürtön kívülről szeretné elérni, hajtsa végre a következő módosításokat az Azure hálózati konfigurációjában a fürt üzembe helyezése során: 

1. **Konfigurálja a Azure Load Balancert** az ügynökökhöz (az **dcos-Agent-LB-XXXX**nevű erőforráshoz), és egy olyan szabályt, amely az 9050-as porton továbbítja a forgalmat a Száva példányokra. 

2. **Frissítse a hálózati biztonsági csoportot** a nyilvános ügynökökhöz (az **XXXX-Agent-Public-NSG-XXXX**nevű erőforráshoz), hogy engedélyezze a forgalmat az 9050-as porton.

A feladatok a Azure Portal használatával történő végrehajtásának részletes lépéseiért tekintse meg a [Azure Container Service alkalmazáshoz való nyilvános hozzáférés engedélyezése](container-service-enable-public-access.md)című témakört. A 9050-es portot az összes portbeállítások esetében meg kell adni.


Miután minden létrejött, nyissa meg a DC/OS ügynök Load Balancer **Áttekintés** paneljét (a **dcos-Agent-LB-XXXX**nevű erőforrást). Keresse meg a **nyilvános IP-címet**, és használja a címet a Sava eléréséhez a 9050-es porton.

![Azure Portal – nyilvános IP-cím lekérése](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![Sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Kanári-kiadás futtatása

Tegyük fel, hogy az alkalmazás egy olyan új verziója van, amelynek a Kanári-kiadását szeretné éles környezetben használni. A magneticio/Sava: 1.1.0 tárolóként van tárolva, és készen áll a használatra. A vamp használatával egyszerűen hozzáadhat új szolgáltatásokat a futó üzembe helyezéshez. Ezek az "egyesített" szolgáltatások a fürt meglévő szolgáltatásai mellett települnek, és 0%-os súlyozást kapnak. A forgalom elosztásának módosítása után a rendszer nem irányítja át a forgalmat egy újonnan egyesített szolgáltatásba. A vamp felhasználói felületének súlyozási csúszkája teljes körűen szabályozhatja az eloszlást, ami lehetővé teszi a növekményes módosításokat (Kanári-kiadás) vagy egy azonnali visszaállítást.

### <a name="merge-a-new-service-variant"></a>Új szolgáltatási változat egyesítése

Az új Száva 1,1 szolgáltatás egyesítése a futó üzembe helyezéssel:

1. A vamp felhasználói felületén kattintson a **tervrajzok**lehetőségre.

2. Kattintson a **Hozzáadás** és beillesztés lehetőségre a következő terv YAML: Ez a terv egy új szolgáltatási változatot (Sava: 1.1.0) ismertet a meglévő fürtön belüli üzembe helyezéshez (sava_cluster).

   ```YAML
   name: sava:1.1.0      # blueprint name
   clusters:
    sava_cluster:       # cluster to update
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
   ```
  
3. Kattintson a **Save** (Mentés) gombra. A tervet a **tervrajzok** oldalon tároljuk és listázjuk.

4. Nyissa meg a művelet menüt a Sava: 1.1 tervben, majd kattintson az **Egyesítés a**következőre elemre.

   ![Vamp felhasználói felület – tervrajzok](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Válassza ki a **Sava** -telepítést, és kattintson az **Egyesítés**elemre.

   ![Vamp-felhasználói felület – terv egyesítése az üzembe helyezéshez](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

A vamp üzembe helyezi az új Sava: 1.1.0 szolgáltatási változatot a tervben, a Sava: 1.0.0 mellett, a futó telepítés **sava_cluster** . 

![Vamp felhasználói felület – frissített Sava-telepítés](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

A **Sava/sava_cluster/webport** -átjáró (a fürt végpontja) is frissül, és egy útvonalat ad hozzá az újonnan üzembe helyezett Sava: 1.1.0-hez. Ezen a ponton a rendszer nem irányítja át a forgalmat (a **súlyozás** 0%-ra van állítva).

![Vamp felhasználói felület – fürt átjárója](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Kanári-kiadás

Ha a Száva mindkét verzióját ugyanabban a fürtben telepítette, a **súlyozás** csúszkájának mozgatásával módosítsa a közöttük lévő forgalom eloszlását.

1. Kattintson ![a vamp felhasználóifelület-](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) Szerkesztés elemre a **súlyozás**mellett.

2. Állítsa a súlyozási eloszlást 50%-%/50%-ra, majd kattintson a **Mentés**gombra.

   ![Vamp felhasználói felület – az átjáró súlyozási csúszkája](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Térjen vissza a böngészőhöz, és frissítse a Száva oldalt néhányszor. A Száva-alkalmazás mostantól a Sava: 1.0 oldal és a Száva: 1.1 oldal között vált.

   ![váltakozó Sava 1.0 és Sava 1.1 szolgáltatások](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Az oldal ezen váltakozása a böngészőben "inkognitóban" vagy "névtelen" módban működik, a statikus eszközök gyorsítótárazása miatt.
  >

### <a name="filter-traffic"></a>Forgalom szűrése

Tegyük fel, hogy az üzembe helyezés után olyan inkompatibilitást észlelt a Sava: 1.1.0-ben, amely a Firefox böngészőben megjelenített problémákat okoz. A vamp beállítható úgy, hogy a bejövő forgalmat szűrje, és visszairányítsa az összes Firefox-felhasználót az ismert stabil Sava: 1.0.0-ra. Ez a szűrő azonnal feloldja a Firefox-felhasználók megszakadását, míg mindenki más továbbra is élvezheti a jobb Sava: 1.1.0 előnyeit.

A vamp az átjáró útvonalai közötti adatforgalom szűrésére **vonatkozó feltételeket** használ. A rendszer először az egyes útvonalakon alkalmazott feltételek alapján szűri és irányítja a forgalmat. Az összes fennmaradó forgalom elosztása az átjáró súlyozási beállítása szerint történik.

Létrehozhat egy olyan feltételt, amely az összes Firefox-felhasználót szűrni tudja, és a régi Sava: 1.0.0-ba irányítja:

1. A Sava/sava_cluster/webport- **átjárók** lapon kattintson a ![vamp UI-Edit](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) (Szerkesztés) elemre, és adjon hozzá egy **feltételt** a Sava/sava_cluster/Sava: 1.0.0/webport. 

2. Adja meg a feltételt a **felhasználó-ügynök = = Firefox** elemnél](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png), majd kattintson a vamp UI – Mentés lehetőségre ![.

   A vamp hozzáadja a feltételt a 0%-os alapértelmezett értékkel. A forgalom szűrésének megkezdéséhez módosítania kell a feltétel erősségét.

3. Kattintson ![a vamp UI-](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) Edit (Szerkesztés) elemre a feltételre alkalmazott **erősség** módosításához.
 
4. Állítsa az **erősséget** 100%-ra ![, majd kattintson a](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) vamp UI – Mentés gombra.

   A vamp most elküldi a feltételnek megfelelő összes forgalmat (az összes Firefox-felhasználót) a Sava: 1.0.0-ba.

   ![Vamp felhasználói felület – feltétel alkalmazása az átjáróra](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Végül módosítsa az átjáró súlyozását, hogy az összes fennmaradó forgalmat (az összes nem Firefox-felhasználót) az új Sava: 1.1.0-be küldje. Kattintson ![a vamp felhasználóifelület-](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) Szerkesztés elemre, és állítsa be a súlyozási eloszlást **úgy, hogy** a 100% a következő útvonalra legyen irányítva: Sava/sava_cluster/Sava: 1.1.0/webport.

   A feltétel által nem szűrt összes forgalom most az új Sava: 1.1.0-re van irányítva.

6. A szűrő működés közbeni megtekintéséhez nyisson meg két különböző böngészőt (egy Firefox és egy másik böngészőt), és mindkettőből nyissa meg a Sava szolgáltatást. Az összes Firefox-kérelmet a Sava: 1.0.0-ba küldik, míg az összes többi böngésző a Sava: 1.1.0 címre van irányítva.

   ![Vamp – felhasználói felület – a forgalom szűrése](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Összegzés

Ez a cikk egy DC/OS-fürtön a vamp gyors bemutatása volt. A kezdők számára a Azure Container Service DC/OS-fürtön készült vamp, amely egy, a kihelyezett végponton (átjárón) keresztül érhető el, és elérhetővé tett egy szolgáltatást.

A vamp néhány hatékony funkcióját is megérintettük: új szolgáltatási változat egyesítése a futó üzembe helyezéshez és növekményes bevezetése, majd a forgalom szűrése az ismert inkompatibilitás feloldása érdekében.


## <a name="next-steps"></a>További lépések

* További információ a vamp-műveletek a [vamp REST API](https://docs.vamp.io/how-vamp-works/events-and-metrics#events)való kezeléséről.

* Készítsen vamp Automation-parancsfájlokat a Node. js-ben, és futtassa őket [vamp-munkafolyamatként](https://docs.vamp.io/how-vamp-works/concepts-and-components#workflows).

* További [vamp-oktatóanyagokat](https://docs.vamp.io/tutorials/)talál.

