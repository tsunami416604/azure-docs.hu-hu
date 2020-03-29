---
title: Hibaelhárítási útmutató az Azure Spring Cloud szolgáltatáshoz | Microsoft dokumentumok
description: Hibaelhárítási útmutató az Azure Spring Cloud szolgáltatáshoz
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277580"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Gyakori Azure Spring Cloud-problémák elhárítása

Ez a cikk az Azure Spring Cloud fejlesztési problémáinak elhárítására vonatkozó utasításokat tartalmazza. További információ: [Azure Spring Cloud GYAKORI KÉRDÉSEK](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Rendelkezésre állási, teljesítménybeli és alkalmazásproblémák

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Az alkalmazás nem indítható el (például a végpont nem csatlakoztatható, vagy néhány újrapróbálkozás után 502-es értéket ad vissza)

Exportálja a naplókat az Azure Log Analytics szolgáltatásba. A tavaszi alkalmazásnaplók táblájának neve *AppPlatformLogsforSpring.* További információ: [Naplók és metrikák elemzése diagnosztikai beállításokkal című témakörben.](diagnostic-services.md)

A következő hibaüzenet jelenhet meg a naplókban:

> "org.springframework.context.ApplicationContextException: Nem lehet elindítani a webkiszolgálót"

Az üzenet két valószínű probléma egyikét jelzi: 
* Az egyik bab vagy az egyik függősége hiányzik.
* A bean egyik tulajdonsága hiányzik vagy érvénytelen. Ebben az esetben valószínűleg a "java.lang.IllegalArgumentException" jelenik meg.

A szolgáltatáskötések az alkalmazások indítási hibáit is okozhatják. A naplók lekérdezéséhez használjon a kötött szolgáltatásokhoz kapcsolódó kulcsszavakat. Például tegyük fel, hogy az alkalmazás rendelkezik egy kötést egy MySQL-példány, amely a helyi rendszer idő beállítása. Ha az alkalmazás nem indul el, a következő hibaüzenet jelenhet meg a naplóban:

> "java.sql.SQLException: A kiszolgáló "Koordinált világidő" időzóna-értéke ismeretlen, vagy egynél több időzónát jelöl."

A hiba kijavításához `server parameters` nyissa meg a MySQL-példányát, és módosítsa az `time_zone` értéket *SYSTEM* értékről *+0:00-ra.*


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Az alkalmazás összeomlik, vagy váratlan hibát ad vissza

Amikor hibakeresési alkalmazás összeomlik, először ellenőrizze az alkalmazás futó állapotát és felderítési állapotát. Ehhez nyissa meg az _Alkalmazáskezelés_ az Azure Portalon annak érdekében, hogy az összes alkalmazás állapota _fut,_ és _up._

* Ha az állapot _fut,_ de a felderítés állapota nem _UP,_ lépjen a ["Saját alkalmazás nem regisztrálható"](#my-application-cant-be-registered) szakaszra.

* Ha a felderítési állapot _UP,_ tekintse meg a Metrikák az alkalmazás állapotának ellenőrzéséhez. Vizsgálja meg a következő mutatókat:


  - `TomcatErrorCount`_(tomcat.global.error):_ Az összes tavaszi alkalmazáskivétel itt van számítva. Ha ez a szám nagy, az Azure Log Analytics az alkalmazásnaplók vizsgálatához.

  - `AppMemoryMax`(_jvm.memory.max):_ Az alkalmazás számára rendelkezésre álló maximális memóriamennyiség. Lehet, hogy az összeg nincs definiálva, vagy idővel változhat, ha meg van határozva. Ha meg van adva, a felhasznált és a lekötött memória mennyisége mindig kisebb vagy egyenlő, mint max. A memórialefoglalás azonban sikertelen `OutOfMemoryError` lehet egy üzenettel, ha a foglalás megpróbálja növelni a felhasznált memóriát, > *a véglegesítést,* még akkor is, ha *a használt <= max* még mindig igaz. Ilyen esetben próbálja meg növelni a maximális halomméret `-Xmx` a paraméter használatával.

  - `AppMemoryUsed`(_jvm.memory.used_): Az alkalmazás által jelenleg használt memória bájtban lévő memóriája. Egy normál terhelés Java alkalmazás, ez a metrikus sorozat alkot *egy fűrészfog* mintát, ahol a memória használat folyamatosan növekszik és csökken a kis lépésekben, és hirtelen csökken sokat, majd a minta ismétlődik. Ez a metrikus sorozat a Jávai virtuális gépen belüli szemétgyűjtés miatt következik be, ahol a gyűjtési műveletek a fűrészfogminta cseppjeit jelölik.
    
    Ez a mérőszám fontos a memóriaproblémák azonosításához, például:
    * Egy memória robbanás a legelején.
    * Egy adott logikai útvonal túlfeszültség-memória-lefoglalása.
    * Fokozatos memóriavesztés.

  További információ: [Metrics](spring-cloud-concept-metrics.md).

Ha többet szeretne megtudni az Azure Log Analytics szolgáltatásról, olvassa el a [Log Analytics első lépéseit az Azure Monitorban.](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Az alkalmazás CPU- vagy memóriahasználata magas

Ha az alkalmazás magas processzor- vagy memóriahasználatot tapasztal, két dolog egyike igaz:
* Az összes alkalmazáspéldány magas processzor- vagy memóriahasználatot tapasztal.
* Az alkalmazáspéldányok egy része magas processzor- vagy memóriahasználatot tapasztal.

Annak megállapításához, hogy melyik helyzet alkalmazandó, tegye a következőket:

1. Nyissa meg **a Metrikák**lehetőséget, majd válassza a **Szolgáltatás cpu-használati százaléka** vagy a használt **szolgáltatásmemória lehetőséget.**
2. Adjon hozzá egy **App=** szűrőt, amely meghatározza, hogy melyik alkalmazást szeretné figyelni.
3. A mérőszámok felosztása **példány**szerint .

Ha *az összes példány* magas processzor- vagy memóriahasználatot tapasztal, vagy horizontálisfelskálázást kell végeznie, vagy fel kell skáláznia a processzor- vagy memóriahasználatot. További információ: [Oktatóanyag: Alkalmazás méretezése az Azure Spring Cloud ban.](spring-cloud-tutorial-scale-manual.md)

Ha *egyes példányok* nagy processzor- vagy memóriahasználatot tapasztalnak, ellenőrizze a példány állapotát és felderítési állapotát.

További információ: [Metrics for Azure Spring Cloud](spring-cloud-concept-metrics.md).

Ha az összes példány működik, az Azure Log Analytics az alkalmazásnaplók lekérdezéséhez és a kódlogika áttekintéséhez. Ez segít annak megtekintésében, hogy ezek bármelyike hatással lehet-e a méretezési particionálásra. További információt a [Naplók és metrikák elemzése diagnosztikai beállításokkal](diagnostic-services.md)című témakörben talál.

Ha többet szeretne megtudni az Azure Log Analytics szolgáltatásról, olvassa el a [Log Analytics első lépéseit az Azure Monitorban.](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) A naplók lekérdezése a [Kusto lekérdezési nyelv](https://docs.microsoft.com/azure/kusto/query/)használatával.

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Ellenőrzőlista a tavaszi alkalmazás Azure Spring Cloud szolgáltatásba való üzembe helyezéséhez

Az alkalmazás bedeszkázása előtt győződjön meg arról, hogy az megfelel az alábbi feltételeknek:

* Az alkalmazás helyileg futtatható a megadott Java futásidejű verzióval.
* A környezeti konfiguráció (CPU/RAM/Instances) megfelel az alkalmazásszolgáltató által meghatározott minimális követelménynek.
* A konfigurációs elemek a várt értékekkel rendelkeznek. További információt a [Config Server című témakörben talál.](spring-cloud-tutorial-config-server.md)
* A környezeti változók rendelkeznek a várt értékekkel.
* A JVM-paraméterek nek meg vannak a várt értékei.
* Azt javasoljuk, hogy tiltsa le vagy távolítsa el a beágyazott _Config Server_ és _a Spring Service Registry_ szolgáltatásokat az alkalmazáscsomagból.
* Ha vannak _szolgáltatáskötéssel_ kötni kívánt Azure-erőforrások, ellenőrizze, hogy a célerőforrások működnek-e.

## <a name="configuration-and-management"></a>Konfigurálás és felügyelet

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Probléma merült fel az Azure Spring Cloud szolgáltatáspéldány létrehozásával kapcsolatban

Amikor az Azure Spring Cloud szolgáltatáspéldányát az Azure Portal használatával állítja be, az Azure Spring Cloud elvégzi az ellenőrzést.

Ha azonban az Azure Spring Cloud szolgáltatáspéldányt az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) vagy az [Azure Resource Manager sablon](https://docs.microsoft.com/azure/azure-resource-manager/)használatával próbálja beállítani, ellenőrizze, hogy:

* Az előfizetés aktív.
* A helyet az Azure Spring Cloud [támogatja.](spring-cloud-faq.md)
* A példány erőforráscsoportja már létrejött.
* Az erőforrás neve megfelel az elnevezési szabálynak. Csak kisbetűket, számokat és kötőjeleket tartalmazhat. Az első karakternek betűnek kell lennie. Az utolsó karakternek betűnek vagy számnak kell lennie. Az értéknek 2 és 32 karakter között kell lennie.

Ha az Azure Spring Cloud szolgáltatáspéldányt az Erőforrás-kezelő sablon használatával szeretné beállítani, először olvassa el [az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)című részét.

Az Azure Spring Cloud szolgáltatáspéldány nevét fogja használni egy altartománynév kéréséhez a alatt, `azureapps.io`így a beállítás sikertelen lesz, ha a név ütközik egy meglévővel. További részleteket a tevékenységnaplókban találhat.

### <a name="i-cant-deploy-a-jar-package"></a>Nem tudok JAR-csomagot telepíteni

Java archív fájl (JAR)/forráscsomag nem tölthető fel az Azure Portalon vagy az Erőforrás-kezelő sablonhasználatával.

Amikor az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)használatával telepíti az alkalmazáscsomagot, az Azure CLI rendszeres időközönként lekérdezi a központi telepítés előrehaladását, és végül megjeleníti a központi telepítés eredményét.

Ha a lekérdezés megszakad, továbbra is használhatja a következő parancsot az üzembehelyezési naplók lekéréséhez:

`az spring-cloud app show-deploy-log -n <app-name>`

Győződjön meg arról, hogy az alkalmazás a megfelelő [végrehajtható JAR formátumban](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)van csomagolva. Ha nincs megfelelően csomagolva, az alábbihoz hasonló hibaüzenet jelenik meg:

> "Hiba: Érvénytelen vagy sérült jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Nem tudok forráscsomagot telepíteni

Jar/source csomagot nem tölthet fel az Azure Portal vagy az Erőforrás-kezelő sablon használatával.

Amikor az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)használatával telepíti az alkalmazáscsomagot, az Azure CLI rendszeres időközönként lekérdezi a központi telepítés előrehaladását, és végül megjeleníti a központi telepítés eredményét.

Ha a lekérdezés megszakad, továbbra is használhatja a következő parancsot a buildelési és üzembehelyezési naplók lekéréséhez:

`az spring-cloud app show-deploy-log -n <app-name>`

Azonban vegye figyelembe, hogy egy Azure Spring Cloud-szolgáltatáspéldány egyszerre csak egy buildelési feladatot indíthat el egy forráscsomaghoz. További információ: [Deploy an application](spring-cloud-quickstart-launch-app-portal.md) and [Set up a staging environment in Azure Spring Cloud](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cant-be-registered"></a>A jelentkezésem nem regisztrálható

A legtöbb esetben ez a helyzet akkor fordul elő, ha *a szükséges függőségek* és *a szolgáltatásfelderítés* nincs megfelelően konfigurálva a PROJEKTobjektum-modell (POM) fájlban. Konfigurálása után a beépített Service Registry kiszolgáló végpont javitott környezet változóként az alkalmazással. Az alkalmazások ezután regisztrálják magukat a Szolgáltatás beállításjegyzék-kiszolgálón, és felderítenek más függő mikroszolgáltatásokat.

Várjon legalább két percet, mielőtt egy újonnan regisztrált példány megkezdi a forgalom fogadását.

Ha egy meglévő tavaszi felhőalapú megoldást telepít át az Azure-ba, győződjön meg arról, hogy az ad hoc _szolgáltatásbeállítási_ és _a Config_ Server-példányokat eltávolítja (vagy letiltja), hogy elkerülje az Azure Spring Cloud által biztosított felügyelt példányok ütközését.

A _Service Registry_ ügyfélnaplók az Azure Log Analytics is ellenőrizheti. További információ: [Naplók és metrikák elemzése diagnosztikai beállításokkal](diagnostic-services.md)

Ha többet szeretne megtudni az Azure Log Analytics szolgáltatásról, olvassa el a [Log Analytics első lépéseit az Azure Monitorban.](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) A naplók lekérdezése a [Kusto lekérdezési nyelv](https://docs.microsoft.com/azure/kusto/query/)használatával.

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Meg szeretném vizsgálni az alkalmazás környezeti változóit

A környezeti változók tájékoztatják az Azure Spring Cloud keretrendszert, biztosítva, hogy az Azure megértse, hol és hogyan konfigurálhatja az alkalmazást kikészítő szolgáltatásokat. A lehetséges problémák elhárításának első lépése annak ellenőrzése, hogy a környezeti változók helyesek-e.  A tavaszi rendszerindítási aktuátor végpont segítségével áttekintheti a környezeti változókat.  

> [!WARNING]
> Ez az eljárás a tesztvégpont használatával teszi elérhetővé a környezeti változókat.  Ne folytassa, ha a tesztvégpont nyilvánosan elérhető, vagy ha tartománynevet rendelt az alkalmazáshoz.

1. Nyissa meg a következőt: `https://<your application test endpoint>/actuator/health`.  
    - A válasz `{"status":"UP"}` hasonló azt jelzi, hogy a végpont engedélyezve van.
    - Ha a válasz negatív, a *POM.xml* fájlba adja meg a következő függőséget:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Ha a tavaszi rendszerindítási aktuátor végpont engedélyezve van, nyissa meg az Azure Portalon, és keresse meg az alkalmazás konfigurációs lapját.  Adjon hozzá egy környezeti `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` változót `*` a nevével és értékével. 

1. Indítsa újra az alkalmazást.

1. Menjen `https://<your application test endpoint>/actuator/env` és vizsgálja meg a választ.  A listának így kell kinéznie:

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

Keresse meg a gyermekcsomópontot. `systemEnvironment`  Ez a csomópont az alkalmazás környezeti változóit tartalmazza.

> [!IMPORTANT]
> Ne feledje, hogy fordított az expozíció a környezeti változók, mielőtt az alkalmazás nyilvánosan hozzáférhetővé.  Nyissa meg az Azure Portalon, keresse meg az alkalmazás konfigurációs lapját, és törölje ezt a környezeti változót: `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`.

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Nem találom az alkalmazáshoz vezető mutatókat és naplókat

Nyissa meg **az Alkalmazáskezelés** lapot, és győződjön meg arról, hogy az alkalmazásállapotok _futnak,_ és _a UP_.

Ha a _JVM-ből_ származó metrikákat lát, de a `spring-boot-actuator` _Tomcat_metrikáit, ellenőrizze, hogy a függőség engedélyezve van-e az alkalmazáscsomagban, és hogy sikeresen elindul-e.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Ha az alkalmazásnaplók archiválhatók egy tárfiókba, de nem küldhetők el az Azure Log Analytics szolgáltatásba, ellenőrizze, hogy [megfelelően állította-e be a munkaterületet.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) Ha az Azure Log Analytics ingyenes szintjét használja, vegye figyelembe, hogy [az ingyenes szint nem biztosít szolgáltatásiszint-szerződést (SLA).](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)
