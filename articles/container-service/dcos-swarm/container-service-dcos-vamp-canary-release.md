---
title: Canary kiadás vamppel Azure DC/OS-fürtön
description: Canary kiadás szolgáltatások Vamp használatával, és intelligens forgalom szűrése az Azure Container Service DC/OS-fürtön alkalmazása
services: container-service
author: gggina
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 116f8ad1760f8e61c70b34d614a0265d11e3ae65
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975856"
---
# <a name="canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>Canary kiadás mikroszolgáltatások vamppel az Azure Container Service DC/OS-fürtön

Ez az útmutató beállítjuk Vamp az Azure Container Service DC/OS-fürttel. Canary azt a Vamp bemutató szolgáltatás "Száva" kiadási, és oldja meg a szolgáltatás a Firefox inkompatibilitás intelligens adatforgalom-szűrés alkalmazásával. 

> [!TIP] 
> Ez az útmutató Vamp DC/OS-fürtön fut, de használhatja Vamp Kubernetes-szel, az orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>Tudnivalók a tesztcsoportos kiadások és Vamp


[Canary felszabadítása](https://martinfowler.com/bliki/CanaryRelease.html) például a Netflix, a Facebook és a Spotify innovatív szervezetek által elfogadott intelligens központi telepítési stratégiát is. Egy megközelítést, amely így érthető, mivel csökkenti a problémákat, biztonsági-háló vezet be, és növeli az innovációt. Tehát miért nem minden vállalat használja azt? Canary stratégiákat CI/CD-folyamat kiterjesztése bonyolultabbá teszi, és széles körű fejlesztési és üzemeltetési tudásuk és tapasztalataik igényel. Ez elegendő letiltása kisebb cégek és vállalatok egyaránt, mielőtt azok még akkor is. 

[Vamp](http://vamp.io/) egy nyílt forráskódú rendszer kialakítva, hogy a váltás megkönnyítése érdekében, és bring Kanári ad ki az előnyben részesített tároló ütemezőnek funkciókat. Canary funkció vamp a százalékskálájú kibocsátások túllép. Forgalom is szűrve és feltételek, például a cél bizonyos felhasználók, IP-címtartományok és eszközök széles skáláját felosztással állít elő. Vamp nyomon követi, és elemzi a teljesítmény-mérőszámok, lehetővé téve az automation, a való életből vett adatai alapján. A hibák automatikus visszaállítási beállítása, vagy méretezheti a terhelés alapján vagy késés adott szolgáltatás variantní hodnoty.

## <a name="set-up-azure-container-service-with-dcos"></a>Állítsa be az Azure Container Service DC/OS használatával



1. [DC/OS fürt üzembe helyezése](container-service-deployment.md) és a egy főkiszolgálóval és két ügynökkel, alapértelmezett mérete. 

2. [SSH-alagút létrehozása](../container-service-connect.md) a DC/OS-fürthöz való csatlakozáshoz. Ez a cikk feltételezi, hogy a fürthöz, a 80-as helyi porton alagutat.


## <a name="set-up-vamp"></a>Vamp beállítása

Most, hogy egy futó DC/OS-fürt, Vamp telepítheti a DC/OS felhasználói felületen (http://localhost:80). 

![A DC/OS UI felhasználói felülete](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Telepítés két lépésben történik:

1. **Az Elasticsearch telepítése**.

2. Ezután **üzembe helyezése Vamp** a Vamp DC/OS universe rendszerben csomag telepítésével.

### <a name="deploy-elasticsearch"></a>Az Elasticsearch telepítése

Vamp igényel az Elasticsearch metrikák gyűjtési és összesítésének megadására szolgál. Használhatja a [magneticio Docker-rendszerképek](https://hub.docker.com/r/magneticio/elastic/) egy kompatibilis Vamp Elasticsearch-verem üzembe helyezéséhez.

1. A DC/OS felhasználói felület, lépjen a **szolgáltatások** kattintson **szolgáltatás telepítése**.

2. Válassza ki **JSON üzemmódot** származó a **új szolgáltatás telepítése** előugró.

  ![Válassza ki a JSON-mód](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Illessze be a következő JSON-fájlban. Ez a konfiguráció a tárolóban fut, 1 GB RAM és a egy alapszintű állapot-ellenőrzést az Elasticsearch-port.
  
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
  

3. Kattintson a **üzembe helyezése**.

  DC/OS az Elasticsearch-tárolót üzembe helyezi. Követheti a folyamat állapotát a **szolgáltatások** lapot.  

  ![e üzembe? Az Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Vamp üzembe helyezése

Miután az Elasticsearch kiderítheti **futtató**, a DC/OS Universe Vamp csomag is hozzáadhat. 

1. Lépjen a **Universe** és keressen rá a **vamp**. 
  ![A DC/OS universe rendszerben vamp](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Kattintson a **telepítése** mellett a vamp csomagot, majd válassza a **speciális telepítési**.

3. Görgessen lefelé, és adja meg a következő elasticsearch-url: `http://elasticsearch.marathon.mesos:9200`. 

  ![Adja meg az Elasticsearch URL-címe](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Kattintson a **áttekintés és telepítés**, majd kattintson a **telepítése** a üzembe helyezésének megkezdéséhez.  

  DC/OS Vamp összes szükséges összetevőt telepít. Követheti a folyamat állapotát a **szolgáltatások** lapot.
  
  ![Üzembe helyezése Vamp universe csomagként](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Üzembe helyezés befejeztével a Vamp felhasználói felület érhető el:

  ![A DC/OS vamp szolgáltatás](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
  ![Felhasználói felület vamp](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Az első szolgáltatás üzembe helyezése

Most, hogy Vamp működik és elérhető, a tervrajz szolgáltatás üzembe helyezése. 

Legegyszerűbb formájukban egy [Vamp tervezet](http://vamp.io/documentation/using-vamp/blueprints/) ismerteti a végpontok (átjárók), a fürtök és a szolgáltatások üzembe helyezéséhez. Vamp fürtök használja ugyanazt a szolgáltatást különböző változatai csoportot logikai csoportokba tesztcsoportos feloldása vagy A / B tesztelés.  

Ebben a példában egy monolitikus mintaalkalmazás nevű [ **Száva**](https://github.com/magneticio/sava), amely jelenleg az 1.0-s verziója. A monolit egy Docker-tároló, amely a Docker hubon alatt található magneticio/sava:1.0.0 van csomagolva. Az alkalmazás megfelelően fut, a 8080-as porton, de ebben az esetben közzé a port 9050 szeretné. Telepítse az alkalmazást egy egyszerű tervezet használatával Vamp keresztül.

1. Lépjen a **központi telepítések**.

2. Kattintson a **Hozzáadás** parancsra.

3. Illessze be a következő YAML tervezet. Ez a megoldás csak egy szolgáltatás másik változata, amely egy későbbi lépésben módosítjuk az egyik fürtről tartalmazza:

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

Megjelenik az üzembe helyezés a **központi telepítések** lapot. Kattintson az állapot figyelése a központi telepítés.

![Vamp UI - Száva üzembe helyezése](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![a felhasználói felületen Vamp Száva szolgáltatás](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Két átjárót hoz létre, amely szerepel a **átjárók** oldalon:

* egy stabil végpont elérésére a futó szolgáltatás (port 9050) 
* Vamp által felügyelt belső átjáró (több, a későbbiekben ezt az átjárót). 

![Felhasználói felület – Száva átjárók vamp](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

A Száva szolgáltatás már telepítve van, de nem lehet hozzáférni, külsőleg, mert az Azure Load Balancer továbbítja a forgalmat, még nem ismert. A szolgáltatás eléréséhez, az Azure hálózati konfigurációjának frissítése.


## <a name="update-the-azure-network-configuration"></a>Az Azure-beli hálózati konfigurációjának frissítése

Vamp üzembe a DC/OS ügynökcsomópontok, egy stabil végpont a következő port 9050 is közzéteheti a Száva szolgáltatást. A szolgáltatáshoz való hozzáféréshez, a DC/OS-fürtön kívül, hajtsa végre a fürt üzembe helyezése az Azure hálózati konfigurációját a következő módosításokat: 

1. **Az Azure Load Balancer konfigurálása** az ügynökök (nevű erőforrás **dcos-ügynök-lb-xxxx**) állapotadat-mintavétel és a egy szabályt, amely továbbítja a forgalmat a Száva példányok 9050 porton. 

2. **A hálózati biztonsági csoport frissítése** a nyilvános ügynökök (nevű erőforrás **XXXX-ügynök-public-nsg-XXXX**) port 9050 forgalom engedélyezéséhez.

Az Azure portal használatával a feladatok végrehajtásához részletes lépéseiért lásd: [egy Azure Container Service-alkalmazás nyilvános hozzáférésének engedélyezése](container-service-enable-public-access.md). Adja meg az összes portbeállítások 9050 port.


Ha mindent létrejött, nyissa meg a **áttekintése** panel terheléselosztó DC/OS-ügynök (nevű erőforrás **dcos-ügynök-lb-xxxx**). Keresse meg a **nyilvános IP-cím**, és a cím, port 9050 Száva eléréséhez használja.

![Az Azure portal - get-nyilvános IP-cím](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![Száva](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Canary kiadás futtatása

Tegyük fel, amelyeket szeretne az alkalmazást az éles környezetbe canary kiadás új verziója. Rendelkezik, mint magneticio/sava:1.1.0 tárolóba, és készen állnak. Vamp teszi lehetővé egyszerűen hozzáadhat új szolgáltatások üzemelő példányban. Ezek a szolgáltatások "egyesített" együtt a meglévő szolgáltatások a fürtben üzembe helyezett, és a súlyt 0 %-os hozzárendelve. Nincs forgalom irányítja a rendszer egy újonnan egyesített szolgáltatást mindaddig, amíg az adatforgalom eloszlása a módosítása. A súly csúszkát a Vamp felhasználói felület kínál teljes körű, a terjesztés, lehetővé téve a növekményes helyesbítések (canary kiadás) és a egy azonnali visszavonás.

### <a name="merge-a-new-service-variant"></a>Egy új szolgáltatás változatot egyesítése

Egyesíteni az új Száva 1.1 szolgáltatásába az üzemelő példányban:

1. A Vamp felhasználói felületén kattintson **tervezetek**.

2. Kattintson a **Hozzáadás** , és illessze be a következő YAML tervezetben: Ez a megoldás bemutatja egy új szolgáltatás variant (Száva: 1.1.0-s) telepítéséhez a meglévő fürtben (sava_cluster).

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
  
3. Kattintson a **Save** (Mentés) gombra. A tervezet tárolja, és felkerülhet a **tervezetek** lapot.

4. Nyissa meg a művelet menüjében a Száva: 1.1-es tervezet, és kattintson a **Egyesítés**.

  ![UI - tervek vamp](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Válassza ki a **Száva** üzembe helyezés, és kattintson **egyesítése**.

  ![UI - egyesítési tervezet üzemelő vamp](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp telepíti az új Száva: 1.1.0-s szolgáltatás változat Száva: 1.0.0 a mellett a tervezet ismertetett a **sava_cluster** az üzemelő példányban. 

![Vamp UI - Száva az üzemelő példány frissítése](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

A **Száva/sava_cluster/webport** átjáró (a fürt azon végpontján) is frissítve lett, az újonnan üzembe helyezett Száva: 1.1.0-s ad hozzá egy útvonalat. Ezen a ponton nincs adatforgalmat itt (a **súly** 0 %-os értékre van állítva).

![Felhasználói felület – fürt átjáró vamp](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Canary kiadás

Mindkét ugyanabban a fürtben üzembe helyezett Száva verzióival, módosítsa a váltással közöttük a forgalom eloszlását a **súly** csúszka.

1. Kattintson a ![Vamp felhasználói felület – Szerkesztés](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) melletti **súly**.

2. 50 % vagy 50 %-át, majd kattintson a súlyozások elosztása beállítása **mentése**.

  ![Felhasználói felület – átjáró súly csúszka vamp](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Lépjen vissza a böngészőhöz, és frissítse a Száva oldalt még néhányszor. Most már az Száva alkalmazás ekkor átvált egy Száva: 1.0 oldal és a egy Száva: 1.1-es oldal között.

  ![váltakozó sava1.0 és sava1.1 szolgáltatások](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Ez az oldal váltási működik a legjobban az "Inkognitó" vagy "Névtelen" mód, a böngésző miatt a statikus objektumokat gyorsítótárazását.
  >

### <a name="filter-traffic"></a>Forgalom szűrése

Tegyük fel, hogy üzembe helyezést követően, hogy a az oka, hogy a Firefox böngészőben megjelenítési problémák Száva: 1.1.0-s inkompatibilitás felderített. Bejövő forgalom szűrésére és a Firefox-felhasználók biztonsági az ismert stabil Száva: 1.0.0 közvetlen Vamp állíthatja be. Ez a szűrő azonnal mindenki más továbbra is élvezhetik annak az előnyeit, a továbbfejlesztett Száva: 1.1.0-s oldja fel a megszakítások időtartamát, a Firefox számára.

Felhasználási vamp **feltételek** útvonal az átjáró közötti forgalom szűrésére. Adatforgalom első szűrt és minden útvonal a alkalmazni feltételek alapján irányítja. Az összes többi forgalom az átjáró súly beállításnak megfelelően történik.

Olyan feltétellel, hogy minden Firefox felhasználók szűrése és a régi Száva: 1.0.0 azokat közvetlenül hozhat létre:

1. A Száva/sava_cluster/webport **átjárók** kattintson ![Vamp UI - szerkesztése](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) hozzáadása egy **feltétel** , az útvonal sava/sava_cluster/sava:1.0.0/webport. 

2. Adja meg a feltétel **felhasználói ügynök == Firefox** kattintson ![Vamp UI - mentése](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

  Vamp hozzáadja azt a feltételt, egy alapértelmezett erőssége a 0 %-os. A szűrési forgalom, a feltétel erőssége módosítani kell.

3. Kattintson a ![Vamp UI - szerkesztése](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) módosítása a **erőssége** a alkalmazni a feltételnek.
 
4. Állítsa be a **erőssége** 100 %-os, majd kattintson ![Vamp UI - mentése](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) mentéséhez.

  Vamp most már a (minden felhasználó a Firefox) Száva: 1.0.0 a feltételnek megfelelő összes forgalmat küld ki.

  ![Felhasználói felület vamp – átjáró feltétel alkalmazása](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Emellett módosíthatja az összes többi forgalmat (az összes a Firefox felhasználó) és az új Száva: 1.1.0-s küldendő átjáró súlyát. Kattintson a ![Vamp UI - szerkesztése](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) melletti **súly** és állítsa be a súlyozások elosztása, ezért az útvonal sava/sava_cluster/sava:1.1.0/webport 100 %-át van irányítva.

  A feltétel nem szűrt minden forgalmat az új Száva: 1.1.0-s most már van átirányítva.

6. A szűrő működés közben látni, nyissa meg a két különböző böngészők (egy a Firefox és a egy másik böngészőben), és a Száva szolgáltatás elérésére is. Összes Firefox kérés küldése a Száva: 1.0.0, míg minden más böngészők Száva: 1.1.0-s irányítja.

  ![UI - forgalom szűrésére vamp](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Összegzése

Ez a cikk röviden bemutatja a Vamp a DC/OS-fürtön volt. Első Vamp itt van és fut a az Azure Container Service DC/OS-fürt üzembe helyezett szolgáltatás, amely egy Vamp tervezet, és fért hozzá az elérhető végponton (átjáró).

Azt is érintőlegesen Vamp néhány hatékony funkcióját: egyesítése egy új szolgáltatás változat az üzemelő példányban, és bevezetéséről szóló Növekményesen, majd feloldani az ismert kompatibilitási forgalom.


## <a name="next-steps"></a>További lépések

* Keresztül Vamp műveletek kezelésével kapcsolatos a [Vamp a REST API-val](http://vamp.io/documentation/api/api-reference/).

* Node.js-ben Vamp automatizálási szkriptek létrehozásához és futtatásához őket [munkafolyamatok Vamp](https://vamp.io/documentation/using-vamp/v1.0.0/workflows/#create-a-workflow).

* További részletek [VAMP oktatóanyagok](http://vamp.io/documentation/tutorials/).

