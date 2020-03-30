---
title: (ELAVULT) Kanári-kiadás a Vamp segítségével az Azure DC/OS-fürtön
description: Vamp-kanárikiadási szolgáltatások használata és intelligens forgalomszűrés alkalmazása egy Azure Container Service DC/OS fürtön
author: gggina
ms.service: container-service
ms.topic: conceptual
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 2af20a1ddf4239b7eec6cceabf2ff9711959c128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77189107"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>(ELAVULT) Kanári-kiadási mikroszolgáltatások a Vamp egy Azure Container Service DC/OS-fürt

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Ebben a forgatókönyvben a Vamp az Azure Container Service egy DC/OS-fürt. Mi kanári mentesít a Vamp demo szolgáltatás "sava", majd oldja meg a kompatibilitást a szolgáltatás firefox alkalmazásával intelligens forgalom szűrés. 

> [!TIP] 
> Ebben a forgatókönyvben a Vamp egy DC/OS-fürtön fut, de a Vamp kubernetes az orchestrator is használhatja.
>

## <a name="about-canary-releases-and-vamp"></a>A kanárikiadásokról és a Vampről


[A Canary felszabadító](https://martinfowler.com/bliki/CanaryRelease.html) intelligens telepítési stratégia, amelyet olyan innovatív szervezetek fogadnak el, mint a Netflix, a Facebook és a Spotify. Ez egy olyan megközelítés, amelynek van értelme, mert csökkenti a problémákat, bevezeti a biztonsági hálókat, és növeli az innovációt. Akkor miért nem minden vállalat használja? A CI/CD-folyamat kanáristratégiákra való kiterjesztése összetettséget jelent, és széles körű devops tudást és tapasztalatot igényel. Ez elég ahhoz, hogy blokkolja a kisebb vállalatok és vállalkozások számára egyaránt, mielőtt még az induláshoz. 

[A Vamp](https://vamp.io/) egy nyílt forráskódú rendszer, amelymegkönnyíti ezt az átmenetet, és kanárifelszabadító funkciókat hoz a kívánt tárolóütembe. A Vamp kanárifunkciói túlmutatnak a százalékos alapú bevezetésen. A forgalom szűrhető és felosztható a feltételek széles skáláján, például adott felhasználók, IP-tartományok vagy eszközök megcélzására. A Vamp nyomon követi és elemzi a teljesítménymutatókat, lehetővé téve a valós adatokon alapuló automatizálást. Beállíthatja a hibák automatikus visszaállítását, vagy méretezheti az egyes szolgáltatásváltozatokat a terhelés vagy a késés alapján.

## <a name="set-up-azure-container-service-with-dcos"></a>Az Azure Container Service beállítása tartományvezérlővel/operációs rendszerrel



1. [Telepítsen egy dc/os fürtöt](container-service-deployment.md) egy főkiszolgálóval és két alapértelmezett méretű ügynökkel. 

2. [Hozzon létre egy SSH-alagutat](../container-service-connect.md) a DC/OS fürthöz való csatlakozáshoz. Ez a cikk feltételezi, hogy a 80-as helyi porton alagutat ad a fürthöz.


## <a name="set-up-vamp"></a>A Vámpírok beállítása

Most, hogy rendelkezik egy futó DC/OS-fürttel, telepítheti a Vamp-ot a DC/OS felhasználói felületéről (http:\//localhost:80). 

![A DC/OS UI felhasználói felülete](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

A telepítés két szakaszban történik:

1. **Rugalmas keresés telepítése.**

2. Ezután **telepítse a Vamp-ot** a Vamp DC/OS univerzum csomag telepítésével.

### <a name="deploy-elasticsearch"></a>Rugalmas keresés telepítése

A Vamp rugalmas keresésre van szükség a metrikák gyűjtéséhez és összesítéséhez. A [magneticio Docker-rendszerképek](https://hub.docker.com/r/magneticio/elastic/) segítségével üzembe helyezhet egy kompatibilis Vamp Elasticsearch verem.

1. A DC/OS felhasználói felületén nyissa meg a **Szolgáltatásokat,** és kattintson **a Szolgáltatás telepítése gombra.**

2. Válassza a **JSON módot** az **Új szolgáltatás központi telepítéséhez.**

   ![JSON mód kiválasztása](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Illessze be a következő JSON-t. Ez a konfiguráció 1 GB RAM-mal és egy alapvető állapot-ellenőrzéssel rendelkező tárolót futtat az Elasticsearch porton.
  
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
  

3. Kattintson **a Telepítés gombra.**

   A DC/OS telepíti az Elasticsearch tárolót. A haladást a Szolgáltatások lapon **követheti** nyomon.  

   ![telepíteni e? Rugalmaskeresés](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Vamp telepítése

Egyszer Elasticsearch jelentések **futtatása,** hozzáadhatja a Vamp DC/OS Universe csomagot. 

1. Menj a **Világegyetemés** keressen **vámpír**. 
   ![Vámpír a DC/OS univerzumban](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Kattintson a **telepítés** gombra a virtuális csomag mellett, és válassza a **Speciális telepítés**lehetőséget.

3. Görgessen le, és adja `http://elasticsearch.marathon.mesos:9200`meg a következő elasticsearch-url: . 

   ![Rugalmas keresési URL megadása](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Kattintson **a Véleményezés és telepítés**gombra, majd a **Telepítés** gombra a telepítés elindításához.  

   A DC/OS telepíti az összes szükséges Vamp-összetevőt. A haladást a Szolgáltatások lapon **követheti** nyomon.
  
   ![Vamp telepítése univerzumcsomagként](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. A telepítés befejezése után elérheti a Vamp felhasználói felületet:

   ![Vamp szolgáltatás DC/OS rendszeren](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Vámpír felhasználói felület](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Az első szolgáltatás üzembe helyezése

Most, hogy a Vamp működik, üzembe helyezegy szolgáltatást egy tervrajzból. 

A legegyszerűbb formában a [vamp-tervezet](https://docs.vamp.io/how-vamp-works/vamp-and-kubernetes#vamp-deployments) ismerteti a végpontok (átjárók), fürtök és üzembe helyezhető szolgáltatások. A Vamp fürtöket használ ugyanazon szolgáltatás különböző változatainak logikai csoportokba csoportosítására a kanári-felszabadításhoz vagy az A/B teszteléshez.  

Ez a forgatókönyv egy sava nevű monolitikus [**mintaalkalmazást**](https://github.com/magneticio/sava)használ, amely az 1.0-s verziójú. A monolit egy Docker-tárolóba van csomagolva, amely a Docker Hubban van magneticio/sava:1.0.0 alatt. Az alkalmazás általában a 8080-as porton fut, de ebben az esetben a 9050-es port alatt szeretné elérhetővé tenni. Az alkalmazás üzembe helyezése a Vamp segítségével egy egyszerű tervrajz használatával.

1. Nyissa meg a **Központi telepítések**.

2. Kattintson a **Hozzáadás** gombra.

3. Illessze be a következő tervezet YAML. Ez a tervezet egy fürtöt tartalmaz, amelycsak egy szolgáltatásváltozattal van, amelyet egy későbbi lépésben módosítunk:

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

4. Kattintson a **Mentés** gombra. A Vamp kezdeményezi a telepítést.

A központi telepítés a **Központi telepítések** lapon jelenik meg. Kattintson a központi telepítés állapotának figyeléséhez.

![Vamp UI - száva telepítése](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![sava szolgáltatás a Vamp UI-ban](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Két átjáró jön létre, amelyek az **Átjárók** lapon jelennek meg:

* stabil végpont a futó szolgáltatás eléréséhez (9050-es port) 
* egy vamp által felügyelt belső átjáró (erről az átjáróról később). 

![Vamp UI - sava átjárók](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

A Sava-szolgáltatás már üzembe, de nem tudja elérni külsőleg, mert az Azure Load Balancer még nem tudja, hogy továbbítsa a forgalmat. A szolgáltatás eléréséhez frissítse az Azure hálózati konfigurációját.


## <a name="update-the-azure-network-configuration"></a>Az Azure hálózati konfigurációjának frissítése

A Vamp a Száva-szolgáltatást a DC/OS ügynök csomópontokra telepítette, és stabil végpontot tett ki a 9050-es porton. A szolgáltatás dc/os fürtön kívülről való eléréséhez hajtsa végre a következő módosításokat az Azure hálózati konfigurációján a fürt központi telepítésében: 

1. **Konfigurálja az Azure Load Balancer** az ügynökök (az erőforrás nevű **dcos-agent-lb-xxxx)** egy állapotminta és egy szabály továbbítja a forgalmat a 9050-es porton a Sava-példányok. 

2. Frissítse a nyilvános ügynökök (az **XXXX-agent-public-nsg-XXXX)** **hálózati biztonsági csoportját** a 9050-es porton való forgalom engedélyezéséhez.

A feladatok Azure Portalon való elvégzéséhez részletes lépéseket az [Azure Container Service-alkalmazáshoz való nyilvános hozzáférés engedélyezése című témakörben.](container-service-enable-public-access.md) Adja meg a 9050-es portot az összes portbeállításhoz.


Miután minden létrejött, lépjen a DC/OS ügynök terheléselosztó **(dcos-agent-lb-xxxx) Áttekintés paneljéhez.** **Overview** Keresse meg a **nyilvános IP-címet**, és használja a címet a száva eléréséhez a 9050-es porton.

![Azure Portal – nyilvános IP-cím bekéselése](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![Sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Kanári-kioldás futtatása

Tegyük fel, hogy az alkalmazás egy új verzióját, amely et szeretne kanári kiadás éles környezetben. Van ez konténeres, mint magneticio / sa: 1.1.0, és készen áll. A Vamp segítségével egyszerűen hozzáadhat új szolgáltatásokat a futó központi telepítéshez. Ezek az "egyesített" szolgáltatások a fürt meglévő szolgáltatásaival együtt vannak telepítve, és 0%-os súlyhoz vannak rendelve. A forgalom nem lesz irányítva egy újonnan egyesített szolgáltatáshoz, amíg nem módosítja a forgalom eloszlását. A Vamp felhasználói felület súlycsúszkája teljes ellenőrzést biztosít az elosztás felett, lehetővé téve a növekményes beállításokat (kanári-kioldás) vagy az azonnali visszaállítást.

### <a name="merge-a-new-service-variant"></a>Új szolgáltatásváltozat egyesítése

Az új Sava 1.1 szolgáltatás egyesítése a futó központi telepítéssel:

1. A vamp felhasználói felületen kattintson a **Tervrajzok gombra.**

2. Kattintson a **Hozzáadás** és beillesztés gombra a következő tervezet YAML: Ez a tervezet egy új szolgáltatásváltozatot (sa:1.1.0) ír le a meglévő fürtön belüli üzembe helyezéshez (sava_cluster).

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
  
3. Kattintson a **Mentés** gombra. A tervrajz tárolása és a **Tervrajzok** oldalon található.

4. Nyissa meg a műveletmenüt a Sava:1.1 tervrajzon, és kattintson az **Egyesítés a ponthoz**parancsra.

   ![Vamp UI - tervrajzok](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Válassza ki a **száva** telepítését, és kattintson **az Egyesítés gombra.**

   ![Vamp UI - egyesítése terv a telepítés](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

A Vamp a száva:1.1.0 szolgáltatásváltozatot a száva:1.0.0 mellett telepíti a futó üzembe helyezés **sava_cluster.** 

![Vamp UI - frissített sava telepítés](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

A **sava/sava_cluster/webport** átjáró (a fürt végpontja) is frissül, hozzáadva egy útvonalat az újonnan telepített sava:1.1.0. Ezen a ponton, nincs forgalom irányítva itt (a **SÚLY** van beállítva 0%).

![Vamp UI - fürtátjáró](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Kanári-kiadás

Ha a SAVA mindkét verziója ugyanabban a fürtben van telepítve, állítsa be a forgalom eloszlását közöttük a **WEIGHT** csúszka mozgatásával.

1. Kattintson ![a Vamp](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) UI gombra - szerkesztés a **SÚLY**mellett.

2. Állítsa a súlyeloszlást 50%/50%-ra, majd kattintson a **Mentés gombra.**

   ![Vamp UI - átjáró súlycsúszka](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Lépjen vissza a böngészőbe, és frissítse a sava oldalt még néhányszor. A Sava alkalmazás most vált a Sava:1.0 oldal és a Sava:1.1 oldal között.

   ![váltakozó sava1.0 és sava1.1 szolgáltatások](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Az oldal ezen váltakozása a statikus eszközök gyorsítótárazása miatt a legjobban a böngésző "Inkognitó" vagy "Névtelen" módjával működik.
  >

### <a name="filter-traffic"></a>Forgalom szűrése

Tegyük fel, hogy a telepítés után a Száva:1.1.0-ban olyan inkompatibilitást fedezett fel, amely megjelenítési problémákat okoz a Firefox böngészőkben. Beállíthatja, hogy a Vamp szűrje a bejövő forgalmat, és az összes Firefox felhasználót visszairányítsa az ismert stabil sava:1.0.0-ra. Ez a szűrő azonnal megoldja a Firefox felhasználók zavarát, miközben mindenki más továbbra is élvezi a továbbfejlesztett sava:1.1.0 előnyeit.

A Vamp **feltételeket** használ az átjáró útvonalai közötti forgalom szűrésére. A forgalom először szűrve és irányítva az egyes útvonalakra alkalmazott feltételek szerint történik. Az összes fennmaradó forgalom az átjáró súlyának beállítása szerint kerül elosztásra.

Létrehozhat egy feltételt, hogy kiszűrje az összes Firefox felhasználót, és irányítsa őket a régi szávára:1.0.0:

1. A Sava/sava_cluster/webport **Gateways** lapon ![kattintson a Vamp UI - szerkesztés elemre,](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) hogy **feltételt** adjon az útvonalhoz sava/sava_cluster/sava:1.0.0/webport. 

2. Adja meg a feltétel **user-agent == Firefox** és kattintson ![a Vamp UI - save](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

   A Vamp 0%-os alapértelmezett erősségű állapotot ad hozzá. A forgalom szűrésének megkezdéséhez módosítania kell a feltétel erősségét.

3. Kattintson a Vamp UI - szerkesztés gombra ![a feltételre alkalmazott STRENGTH](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) **módosításához.**
 
4. Állítsa be az **ERŐSSÉG** 100%-ra, és kattintson ![a Vamp UI gombra - mentés](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) a mentéshez.

   Vamp most küld minden forgalom megfelelő állapot (minden Firefox felhasználók) a Sava:1.0.0.

   ![Vamp UI - állapot alkalmazása az átjáróra](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Végül állítsa be az átjáró súlyát, hogy az összes fennmaradó forgalmat (az összes nem Firefox felhasználó) az új sava:1.1.0-ra küldje. Kattintson a ![Vamp](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) UI gombra - a **SÚLY** gomb melletti szerkesztés gombra kattintva állítsa be a súlyeloszlást úgy, hogy a 100% a sava/sava_cluster/sava:1.1.0/webport útvonalra irányuljon.

   Minden forgalom nem szűrt a feltétel most irányítja az új sava:1.1.0.

6. A szűrő működés közbeni megtekintéséhez nyisson meg két különböző böngészőt (egy Firefoxot és egy másik böngészőt), és érje el a sava szolgáltatást mindkettőből. Minden Firefox kérés a Sava:1.0.0-ra, míg az összes többi böngésző a Sava:1.1.0-ra irányul.

   ![Vamp UI - szűrő forgalom](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Összegzés

Ez a cikk egy gyors bevezetés a virtuális és virtuális, dc/os fürtvén lévő vampszámára. Kezdetnek, a Vamp az Azure Container Service DC/OS-fürt, üzembe helyezett egy szolgáltatást egy Vamp-tervezet, és a kitett végpont (átjáró) elérése.

A Vamp néhány hatékony funkcióját is érintettük: egy új szolgáltatásváltozat egyesítése a futó üzembe helyezéssel és fokozatos bevezetésével, majd a forgalom szűrésével egy ismert inkompatibilitás feloldásához.


## <a name="next-steps"></a>További lépések

* Ismerje meg a vamp műveletek kezelését a [Vamp REST API-n](https://docs.vamp.io/how-vamp-works/events-and-metrics#events)keresztül.

* Vamp-automatizálási parancsfájlok létrehozása a Node.js-ben, és [futtassa őket vamp munkafolyamatként.](https://docs.vamp.io/how-vamp-works/concepts-and-components#workflows)

* Lásd a további [VAMP oktatóanyagokat](https://docs.vamp.io/tutorials/).

