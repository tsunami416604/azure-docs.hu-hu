---
title: "Azure DC/OS-fürtön a Vamp Kanári kiadás"
description: "Vamp segítségével Kanári kiadás szolgáltatások, és egy Azure tároló szolgáltatás DC/OS-fürtről végez a intelligens forgalom alkalmazása"
services: container-service
author: gggina
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: e23b316311ff88d1587da8d5ef777b91bfe0a2e9
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>Egy Azure tároló szolgáltatás DC/OS-fürtön a Vamp Kanári kiadás mikroszolgáltatások

Ebben a bemutatóban beállítjuk Vamp Azure tárolószolgáltatás és a DC/OS-fürtről. Azt Kanári a Vamp bemutató szolgáltatás "Száva" kiadású, és oldja meg a Firefox szolgáltatást inkompatibilitás intelligens forgalomszűrést végez alkalmazásával. 

> [!TIP] 
> A forgatókönyv Vamp a DC/OS-fürtön fut, de akkor is használható Vamp rendelkező Kubernetes az orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>Feloldja a Kanári kapcsolatos és Vamp


[Kanári felszabadítása](https://martinfowler.com/bliki/CanaryRelease.html) például Netflix, a Facebook-on és a Spotify új szervezetek által elfogadott intelligens központi telepítési stratégiát is. Értelme, mivel csökkenti a problémákat, biztonsági-háló vezet be, és növeli az innováció egy megközelítést. Ezért miért nem minden vállalat használja azt? CI/CD csővezeték Kanári stratégiákat kiterjesztése hozzáadása összetettségét, valamint kiterjedt devops tudásuk és tapasztalataik igényel. Ez elég blokkolására kisebb cégek és vállalatok hasonló ahhoz, azok még első lépései. 

[Vamp](http://vamp.io/) ad egy nyílt forráskódú rendszer célja, hogy ez a változás megkönnyítik és Kanári kapcsolja ki az előnyben részesített tároló Feladatütemező szolgáltatások. Vamp tartozó Kanári funkció túllép százalék alapú exportálása. Forgalom szűrve, és azokat a feltételeket, például adott felhasználók megcélzása, IP-címtartományok vagy eszközök felosztással állít elő. Vamp nyomon követi, és elemzi a teljesítménymutatók, lehetővé teszi a automation valós adatok alapján. A hibák automatikus visszaállítási beállítása, vagy méretezni terhelés alapján vagy késés szolgáltatás Variant adattípusban.

## <a name="set-up-azure-container-service-with-dcos"></a>Azure Tárolószolgáltatási DC/OS rendelkező beállítása



1. [A DC/OS-fürt üzembe](container-service-deployment.md) egy fő és a két ügynök alapértelmezett mérete. 

2. [SSH-alagút létrehozása](../container-service-connect.md) a DC/OS-fürtön való kapcsolódáshoz. Ez a cikk azt feltételezi, hogy a 80-as portjához helyi fürthöz alagutat.


## <a name="set-up-vamp"></a>Vamp beállítása

Most, hogy a futó DC/OS-fürtről, a DC/OS felhasználói felületének (http://localhost:80) az telepíthető Vamp. 

![A DC/OS UI felhasználói felülete](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Telepítés két lépésben történik:

1. **Elasticsearch telepítése**.

2. Majd **Vamp telepítése** a Vamp DC/OS universe csomag telepítésével.

### <a name="deploy-elasticsearch"></a>Elasticsearch telepítése

Vamp Elasticsearch metrikák adatgyűjtési és -összesítési igényel. Használhatja a [magneticio Docker képek](https://hub.docker.com/r/magneticio/elastic/) egy kompatibilis Vamp Elasticsearch verem telepítéséhez.

1. A DC/OS felhasználói felületének Ugrás **szolgáltatások** kattintson **szolgáltatás telepítése**.

2. Válassza ki **JSON üzemmódot** a a **új szolgáltatás telepítése** előugró.

  ![Válassza ki a JSON üzemmód](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Illessze be a következő JSON-ban. Ez a konfiguráció a tárolóban fut, 1 GB RAM és a Elasticsearch port alapszintű állapotának ellenőrzése.
  
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
  

3. Kattintson a **telepítése**.

  A DC/OS telepíti a Elasticsearch tároló. A végrehajtás nyomon a **szolgáltatások** lap.  

  ![e telepítéséhez? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Vamp telepítése

Miután Elasticsearch jelzi **futtató**, a DC/OS Universe Vamp csomag is hozzáadhat. 

1. Ugrás a **Universe** keresse meg a **vamp**. 
  ![A DC/OS universe vamp](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Kattintson a **telepítése** mellett a vamp csomagot, majd válassza a **speciális telepítési**.

3. Görgessen lefelé, és írja be a következő elasticsearch-URL-cím: `http://elasticsearch.marathon.mesos:9200`. 

  ![Adja meg a Elasticsearch URL-címe](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Kattintson a **áttekintése és telepítése**, majd kattintson a **telepítése** a telepítés elindításához.  

  A DC/OS Vamp minden szükséges összetevőket telepíti. A végrehajtás nyomon a **szolgáltatások** lap.
  
  ![Vamp universe csomag telepítése](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Központi telepítés befejezése után a Vamp felhasználói felület érhető el:

  ![A DC/OS vamp szolgáltatás](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
  ![Felhasználói felület vamp](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Az első szolgáltatás üzembe helyezése

Most, hogy Vamp működik-e és fut, a tervezetének a szolgáltatás telepítése. 

Legegyszerűbb formájukban a [Vamp tervezetének](http://vamp.io/documentation/using-vamp/blueprints/) a végpontok (átjárók), a fürtök és a telepítendő szolgáltatások ismerteti. Vamp fürtök használja ugyanazt a szolgáltatást különböző változatai csoportot logikai csoportokba Kanári feloldása vagy A / B tesztelés.  

Ez a forgatókönyv használ nevű egységes mintaalkalmazás [ **Száva**](https://github.com/magneticio/sava), amely jelenleg 1.0-s verziója. A monolit egy Docker-tároló, amely Docker központban magneticio/sava:1.0.0 alatt van csomagolva. Az alkalmazás megfelelően fut a 8080-as porton, de azt szeretné, ebben az esetben tenni a port 9050. Telepítse az alkalmazást egy egyszerű tervezetének használatával Vamp keresztül.

1. Ugrás a **központi telepítések**.

2. Kattintson az **Add** (Hozzáadás) parancsra.

3. Illessze be a következő szerkezeti terve YAM. Ez tervezetének egy fürt csak egy szolgáltatás variant, amely egy későbbi lépésben módosítjuk tartalmazza:

  ```YAML
  name: sava                        # deployment name
  gateways:
    9050: sava_cluster/webport      # stable endpoint
  clusters:
    sava_cluster:               # cluster to create
     services:
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
  ```

4. Kattintson a **Save** (Mentés) gombra. Vamp kezdeményezi a központi telepítést.

A központi telepítés szerepel a **központi telepítések** lap. Kattintson a telepítés állapotának figyelésére.

![Vamp UI - Száva telepítése](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![a felhasználói felületen Vamp Száva szolgáltatás](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Két átjáró jönnek létre, amely szerepel a **átjárók** lap:

* egy stabil végponton keresztül biztosít hozzáférést a futó szolgáltatás (port 9050) 
* Vamp által kezelt belső átjáró (erről az átjáróról később további). 

![Felhasználói felület – Száva átjárók vamp](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

A Száva szolgáltatás már telepítve van, de nem férhet hozzá az külsőleg, mert az Azure Load Balancer hozzá forgalom továbbítására még nem ismert. A szolgáltatás eléréséhez Azure-alapú hálózati beállításainak frissítése.


## <a name="update-the-azure-network-configuration"></a>Az Azure-hálózat konfiguráció frissítése

Vamp üzembe a DC/OS ügynök csomópontok, egy stabil végpont-es porton 9050 kitettségének Száva szolgáltatást. A szolgáltatás a DC/OS-fürtön kívüli eléréséhez a következő módosításokat az Azure hálózati konfigurációhoz a fürtöt tartalmazó környezetben: 

1. **Konfigurálja az Azure Load Balancer** az ügynökök (nevű erőforrás **vezénylőtípusú-ügynök-lb-xxxx**) egy állapotmintáihoz és egy szabályt, amely továbbítsa a forgalmat a Száva példányokhoz 9050 porton. 

2. **A hálózati biztonsági csoport** a nyilvános ügynökök (nevű erőforrás **XXXX-ügynök – nyilvános-nsg-XXXX**) forgalmat engedélyezi a port 9050.

Az Azure portál használatával a feladatok elvégzéséhez részletes lépéseiért lásd: [Azure Tárolószolgáltatás alkalmazáshoz való nyilvános hozzáférés engedélyezésére](container-service-enable-public-access.md). Adja meg az összes portbeállítások 9050 portot.


Minden létrehozása, navigáljon a **áttekintése** panel terheléselosztó DC/OS-ügynök (nevű erőforrás **vezénylőtípusú-ügynök-lb-xxxx**). Keresés a **nyilvános IP-cím**, és a cím: port 9050 Száva eléréséhez használja.

![Azure portál – get nyilvános IP-cím](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![Száva](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Kanári kiadási futtatása

Tegyük fel egy új verziója, amelyet az alkalmazás Kanári kiadásához éles környezetben. Annak indexelése magneticio/sava:1.1.0, és készen állnak. Vamp lehetővé teszi, hogy könnyen vehet fel új szolgáltatások a futó központi telepítéshez. Ezek a szolgáltatások "egyesített" mellett a meglévő szolgáltatások a fürtben telepített, és egy 0 %-os értéket kapó. Nem továbbítódik újonnan egyesített szolgáltatás mindaddig, amíg a forgalom eloszlása módosíthatja. A Vamp felhasználói felületén súly csúszkája lehetővé teszi a terjesztési, lehetővé teszi a növekményes módosításának (Kanári kiadás) vagy egy azonnali visszavonás teljes ellenőrzését.

### <a name="merge-a-new-service-variant"></a>Egy új szolgáltatás variant egyesítése

Az új Száva 1.1-bA a futó telepítési egyesíteni:

1. A Vamp felhasználói felületén kattintson **tervrajzokat**.

2. Kattintson a **Hozzáadás** és illessze be a következő szerkezeti terve YAM: A tervezetének ismerteti a meglévő fürt (sava_cluster) telepítése egy új szolgáltatás variant (Száva: 1.1.0-ás).

  ```YAML
  name: sava:1.1.0      # blueprint name
  clusters:
    sava_cluster:       # cluster to update
      services:
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
  ```
  
3. Kattintson a **Save** (Mentés) gombra. Szerkezeti terve tárolja, és szerepel a **tervrajzokat** lap.

4. Nyissa meg a művelet menü Száva: 1.1 szerkezeti terve a, és kattintson a **egyesítése**.

  ![Felhasználói felület – tervrajzokat vamp](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Válassza ki a **Száva** üzembe helyezési és kattintson **egyesítése**.

  ![Felhasználói felület - telepítéshez egyesítési tervezetének vamp](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp telepíti az új Száva: 1.1.0-ás szolgáltatás variant mellett Száva: 1.0.0 a szerkezeti terve ismertetett a **sava_cluster** a futó központi telepítés. 

![Felhasználói felület – frissített Száva telepítési vamp](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

A **Száva/sava_cluster/webport** átjáró (a fürt végpontja) is frissül, egy útvonal hozzáadása az újonnan telepített Száva: 1.1.0-ás. Ezen a ponton nem továbbítódik itt (a **súly** 0 % értékre van állítva).

![Felhasználói felület - fürt átjáró vamp](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Kanári kiadás

Mindkét Száva ugyanabban a fürtben telepített verziója, a közöttük át forgalmat beállítása a **súly** csúszkát.

1. Kattintson a ![Vamp UI - szerkesztése](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) melletti **súly**.

2. 50 % vagy 50 %, majd kattintson a súlyozások elosztása beállítása **mentése**.

  ![Felhasználói felület – átjáró súly csúszkát vamp](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Lépjen vissza a böngészőt, és frissítse a Száva oldalt néhány alkalommal. A Száva alkalmazást vált egy Száva: 1.0 lap és Száva: 1.1 oldal között.

  ![váltakozó sava1.0 és sava1.1 szolgáltatások](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Ez a lap váltási működik a legjobban a "Incognito" vagy "Névtelen" módban, a böngésző statikus rendezett gyorsítótárazása miatt.
  >

### <a name="filter-traffic"></a>Szűrő forgalom

Tegyük fel, hogy a központi telepítést követően, hogy a inkompatibilitás Száva: 1.1.0-ás okok Firefox böngészőket problémákat jeleníti meg a felderíteni. Bejövő forgalom szűrésére, és az ismert stabil Száva: 1.0.0 biztonsági senki Firefox közvetlen Vamp állíthatja be. Ez a szűrő azonnal miközben mindenki más tovább teszik a továbbfejlesztett Száva: 1.1.0-ás az oldja fel a megszakítás a Firefox használó felhasználók számára.

Felhasználási vamp **feltételek** útvonal az átjáró közötti forgalom szűrésére. Adatforgalom először szűrt és irányítja a rendszer minden útvonal használt feltételek szerint. Az összes többi forgalom az átjáró súly beállításnak megfelelően történik.

Létrehozhat olyan állapotban, hogy senki Firefox szűrése forgalmazójával, és a régi Száva: 1.0.0:

1. A Száva/sava_cluster/webport **átjárók** lapján kattintson ![Vamp UI - szerkesztése](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) hozzáadása egy **feltétel** az útvonal sava/sava_cluster/sava:1.0.0/webport számára. 

2. Adja meg a feltétel **felhasználói ügynök == Firefox** kattintson ![Vamp UI - mentése](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

  Vamp felveszi a feltételt, amelynek alapértelmezett 0 %. Forgalom megkezdéséhez szüksége úgy, hogy a feltétel erőssége.

3. Kattintson a ![Vamp UI - szerkesztése](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) módosítása a **erőssége** alkalmazza a feltételnek.
 
4. Állítsa be a **erőssége** 100 %-os, majd kattintson ![Vamp UI - mentése](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) mentéséhez.

  Vamp most küld az összes forgalom megfelelő Száva: 1.0.0 megtagadásra (minden felhasználó Firefox).

  ![Felhasználói felület vamp - feltétel érvényes átjáró](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Végül módosítsa az átjáró súlyozást az összes többi forgalom (minden felhasználó nem Firefox) küldhet az új Száva: 1.1.0-ás. Kattintson a ![Vamp UI - szerkesztése](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) melletti **súly** és állítsa be a súlyozások elosztása, így a 100 %-át van irányítva az útvonal sava/sava_cluster/sava:1.1.0/webport.

  Az összes forgalom a feltétel nem szűrt most már az új Száva: 1.1.0-ás van átirányítva.

6. A művelet a szűrő megtekintéséhez nyissa meg két különböző böngészők (egy Firefox és egy másik böngészőben), és éri el a Száva szolgáltatást is. Összes Firefox kérelem küldése a Száva: 1.0.0, míg minden más böngészők Száva: 1.1.0-ás van irányítva.

  ![Felhasználói felület - forgalom szűrésére vamp](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Összegzése

Ez a cikk lett egy gyors bevezetés Vamp a DC/OS-fürtön. Első Vamp portáltól és fut a az Azure tároló szolgáltatás DC/OS fürt, telepített egy szolgáltatást a egy Vamp tervezetének, címen érhető el: a kitett végpont (átjáró).

Azt is az egyes hatékony szolgáltatásainak Vamp érint: egyesítése egy új szolgáltatás variant futó központi és bevezeti a Növekményesen, majd egy ismert kompatibilitási megoldásához forgalom.


## <a name="next-steps"></a>Következő lépések

* Vamp műveletek keresztül kezeléséről további információt a [Vamp a REST API-t](http://vamp.io/documentation/api/api-reference/).

* A node.js Vamp automatizálási parancsfájlokat létrehozása és futtatása azokat [munkafolyamatok Vamp](http://vamp.io/documentation/tutorials/create-a-workflow/).

* További részletek [VAMP oktatóanyagok](http://vamp.io/documentation/tutorials/overview/).

