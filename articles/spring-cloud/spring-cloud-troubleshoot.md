---
title: Az Azure Spring Cloud hibaelhárítási útmutatója | Microsoft Docs
description: Az Azure Spring Cloud hibaelhárítási útmutatója
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: ee51841046962a6896b4c16e651f85ff761a69fc
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592481"
---
# <a name="troubleshooting-guide-for-common-problems"></a>Gyakori problémák hibaelhárítási útmutatója

Ez a cikk néhány gyakori problémát és hibaelhárítási lépést részletez az Azure Spring Cloud-ban dolgozó fejlesztők számára. Javasoljuk továbbá a GYIK- [cikk](spring-cloud-faq.md)elolvasását.

## <a name="availability-performance-and-application-issues"></a>Rendelkezésre állással, teljesítménnyel és alkalmazásokkal kapcsolatos problémák

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>Az alkalmazás nem indítható el (például a végpont nem csatlakoztatható, vagy a 502 értéket adja vissza néhány újrapróbálkozás után)

Exportálja a naplókat az _Azure log Analyticsba_. A Spring-alkalmazásnaplók táblázatának neve a következő: `AppPlatformLogsforSpring`. További tudnivalókért tekintse meg [a naplók és a mérőszámok elemzése a diagnosztikai beállításokkal](diagnostic-services.md) című témakört.

A következő hiba megkeresése a naplókban két valószínű probléma egyikét jelenti:

`org.springframework.context.ApplicationContextException: Unable to start web server`

* Hiányzik az egyik bab vagy annak valamelyik függősége.
* A bean egyik tulajdonsága hiányzik vagy érvénytelen. Ebben az esetben valószínűleg `java.lang.IllegalArgumentException` fog megjelenni.

A szolgáltatási kötések az alkalmazás indítási hibáját is okozhatják. A naplók lekérdezéséhez használjon a kötött szolgáltatásokhoz kapcsolódó kulcsszavakat.  Tegyük fel például, hogy az alkalmazáshoz kötés tartozik egy helyi rendszeridőre beállított MySQL-példányhoz. Ha az alkalmazás nem indul el, a következő hibaüzenet jelenhet meg a naplóban:

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

A hiba kijavításához nyissa meg a MySql-példány `server parameters`ét, és módosítsa a `time_zone`t `SYSTEM`ról `+0:00`ra.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Az alkalmazás összeomlik, vagy váratlan hibát ad vissza

Az alkalmazások hibakeresése során először ellenőrizze az alkalmazás futási állapotát és felderítési állapotát. Nyissa meg az Azure Portal az alkalmazások _kezelése_ lehetőséget, és győződjön meg arról, hogy az összes alkalmazás _fut_ és _fel_van indítva.

* Ha az állapot _fut_ , de a felderítési állapot nem áll fenn, lépjen a [saját alkalmazás nem regisztrálható](#my-application-cannot-be-registered) _elemre_.

* Ha a felderítés állapota _fel van állítva_, a _metrikák_ elemre kattintva ellenőrizze az alkalmazás állapotát. Vizsgálja meg a következő metrikákat:


  - `TomcatErrorCount` (_tomcat. Global. Error_): a program az összes tavaszi alkalmazás kivételét itt számítja fel. Ha ez a szám nagy, nyissa meg az _Azure log Analytics_ az alkalmazás naplófájljainak vizsgálatához.

  - `AppMemoryMax` (_JVM. Memory. max_): az alkalmazás számára rendelkezésre álló memória maximális mennyisége. Lehet, hogy nem definiált, vagy az idő múlásával módosul, ha meg van adva. A felhasznált és lefoglalt memória mennyisége mindig legfeljebb annyi lehet, mint a maximális érték, ha az meg van adva. Előfordulhat azonban, hogy a memóriafoglalás meghiúsul a(z) `OutOfMemoryError` hibával, ha megkísérli megnövelni a felhasznált memóriát úgy, hogy a felhasznált memória mennyisége > a lefoglalt memória mennyiség, még akkor is, ha továbbra is igaz, hogy a felhasznált memória mennyisége <= a maximális memóriamennyiség. Ilyen esetben próbálja megnövelni a maximális halommemória-méretet a(z) `-Xmx` paraméterrel.

  - `AppMemoryUsed` (_JVM. Memory. használt_): az alkalmazás által jelenleg használt memória mennyisége bájtban kifejezve. Egy normál terhelésű Java-alkalmazás esetében ez a metrikasorozat „fűrészfog” alakú mintát eredményez, amelyben a memóriahasználat kisebb növekményekben egyenletesen nő és csökken, gyakran hirtelen leesik, majd ez a minta ismétlődik. Ennek az az oka, hogy a Java virtuális gépen belül a szemetet gyűjti, ahol a gyűjtési műveletek a "sawteeth" cseppeket jelölik.
    Ez a mérőszám fontos a memória-problémák azonosításához, például: * memória alábontása a legelején * a nagy mennyiségű memória kiosztása egy adott logikai útvonalon * fokozatos memóriavesztés

  További részletekért tekintse meg a [metrikákat](spring-cloud-concept-metrics.md).

Tekintse meg [az első lépéseket ismertető cikket](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) az _Azure log Analytics_megismeréséhez.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Az alkalmazás CPU- vagy memóriahasználata magas

Ha az alkalmazás magas CPU-/memóriahasználat-használatot tapasztal, az egyik két dolog igaz:
* Az összes alkalmazás-példány magas CPU-/memóriahasználat-használatot, vagy
* Néhány alkalmazás-példány magas CPU-/memóriahasználat-használattal rendelkezik.

A helyzet megállapításához

1. Lépjen a _Metrikák_ területre, és válassza a(z) `Service CPU Usage Percentage` vagy a(z) `Service Memory Used` lehetőséget,
2. Adjon hozzá egy `App=` szűrőt, hogy megadja, melyik alkalmazást kívánja monitorozni.
3. A metrikák felosztása `Instance` alapján.

Ha az összes példány magas CPU-/memória-értékkel rendelkezik, akkor fel kell mérnie az alkalmazást, vagy fel kell mérnie a PROCESSZORt vagy a memóriát. További részletekért tekintse meg az [alkalmazások méretezése](spring-cloud-tutorial-scale-manual.md)

Ha a példányok némelyike magas PROCESSZORt vagy memóriát észlel, ellenőrizze a példány állapotát és a felderítési állapotát.

További részletekért látogasson el a [metrikák](spring-cloud-concept-metrics.md)oldalra.

Ha az összes példány működőképes, lépjen az _Azure log Analyticsra_ az alkalmazás naplófájljainak lekérdezéséhez, és tekintse át a kód logikáját, és ellenőrizze, hogy ezek befolyásolhatják-e a skálázási particionálást. További részletekért látogasson el a [naplók és a metrikák elemzésére a diagnosztikai beállításokkal](diagnostic-services.md).

Tekintse meg [az első lépéseket ismertető cikket](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) az _Azure log Analytics_megismeréséhez. A naplók lekérdezése [Kusto lekérdezési nyelv](https://docs.microsoft.com/azure/kusto/query/)használatával.

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>Ellenőrzőlista a Spring-alkalmazás Azure Spring Cloud-ba történő bevezetéséhez

* Az alkalmazás helyileg futtatható a Java futtatókörnyezet megadott verziójával.
* A környezeti konfiguráció (CPU/RAM/példányok) megfelel az alkalmazás szolgáltatója által beállított minimális követelménynek.
* A konfigurációs elemek a várt értékekkel rendelkeznek. További információ: [konfigurációs kiszolgáló](spring-cloud-tutorial-config-server.md).
* A környezeti változók várt értékekkel rendelkeznek.
* A JVM paraméterek várt értékekkel rendelkeznek.
* Javasoljuk, hogy tiltsa le/távolítsa el a beágyazott _konfigurációs kiszolgáló_ és a _Spring Service Registry_ szolgáltatást az alkalmazáscsomag alapján.
* Ha vannak _szolgáltatáskötéssel_ kötni kívánt Azure-erőforrások, ellenőrizze, hogy a célerőforrások működnek-e.

## <a name="configuration-and-management"></a>Konfigurálás és felügyelet

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>Probléma merült fel egy Azure Spring Cloud Service-példány létrehozásakor

Ha egy _Azure Spring Cloud_ Service-példányt próbál kiépíteni a portálon keresztül, az Azure Spring Cloud végrehajtja az érvényesítést.

Ha azonban az Azure _Spring Cloud_ Service-példányt az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) vagy a [Resource Manager-sablon](https://docs.microsoft.com/azure/azure-resource-manager/)segítségével próbálja meg kiépíteni, ellenőrizze a következőket:

* Az előfizetés aktív.
* A helyet az _Azure Spring Cloud_ [támogatja](spring-cloud-faq.md) .
* A példányhoz tartozó erőforráscsoport már létre van hozva.
* Az erőforrás neve megfelel az elnevezési szabálynak. (Csak kisbetűket, számokat és kötőjeleket tartalmazhat. Az első karakternek betűnek kell lennie. Az utolsó karakternek betűnek vagy számnak kell lennie. Az értéknek 2 – 32 karakter hosszúnak kell lennie.)

Ha a Resource Manager-sablon segítségével próbálja kiépíteni az _Azure Spring Cloud_ Service-példányt, látogasson el https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates a sablon szintaxisának megtekintéséhez.

A rendszer az _Azure Spring Cloud_ Service-példány nevét fogja használni a `azureapps.io` alatti altartomány nevének megadásához, ezért a kiépítés meghiúsul, ha a név ütközik egy meglévővel. További részleteket a tevékenységnaplókban talál.

### <a name="i-cannot-deploy-a-jar-package"></a>Nem lehet üzembe helyezni a JAR-csomagot

A JAR/Source csomag nem tölthető fel a portál vagy a Resource Manager-sablon segítségével.

Az alkalmazáscsomag [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)-vel történő üzembe helyezése során a rendszer rendszeres időközönként lekérdezi a telepítési folyamatot, és végül megjeleníti az üzembe helyezés eredményét.

Ha a lekérdezés megszakad, továbbra is használhatja a következő parancsot az üzembehelyezési naplók lekéréséhez:

`az spring-cloud app show-deploy-log -n <app-name>`

Győződjön meg arról, hogy az alkalmazás a megfelelő [végrehajtható jar-formátumban](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)van csomagolva. Ha nem, akkor az alábbihoz hasonló hiba jelenik meg:

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>Nem lehet üzembe helyezni a forráscsomagot

A JAR/Source csomag nem tölthető fel a portál vagy a Resource Manager-sablon segítségével.

Az alkalmazáscsomag [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) történő üzembe helyezésekor a rendszer rendszeres időközönként lekérdezi az üzembehelyezési folyamatot, és végül megjeleníti az üzembe helyezés eredményét.

Ha a lekérdezés megszakad, továbbra is használhatja a következő parancsot a buildelési és üzembehelyezési naplók lekéréséhez:

`az spring-cloud app show-deploy-log -n <app-name>`

Vegye figyelembe azonban, hogy egy _Azure Spring Cloud_ Service-példány egyszerre csak egy felépítési feladatot tud kiváltani egy adott csomagra. További részletekért tekintse meg az [alkalmazás üzembe helyezése](spring-cloud-quickstart-launch-app-portal.md) és az [átmeneti környezet útmutatója](spring-cloud-howto-staging-environment.md)című témakört.

### <a name="my-application-cannot-be-registered"></a>Az alkalmazást nem lehet regisztrálni

A legtöbb esetben ez akkor fordul elő, ha a szükséges függőségek/szolgáltatások felderítése nem megfelelően van konfigurálva a POM-fájlban. A konfigurálást követően a beépített szolgáltatás beállításjegyzék-kiszolgálói végpontja környezeti változóként lesz befecskendezve az alkalmazással. Az alkalmazások ezután regisztrálják magukat a szolgáltatás beállításjegyzék-kiszolgálójával, és felderítik a többi függő szolgáltatást is.

Várjon legalább 2 percet, mielőtt egy újonnan regisztrált példány megkezdi a forgalom fogadását.

Ha egy meglévő Spring Cloud-alapú megoldást telepít át az Azure-ba, győződjön meg róla, hogy az ad-hoc _szolgáltatás beállításjegyzék_ -és _konfigurációs kiszolgálói_ példányai el lesznek távolítva (vagy le vannak tiltva) az _Azure Spring Cloud által biztosított felügyelt példányok ütközésének elkerülése érdekében_ .

A _szolgáltatás beállításjegyzékbeli_ ügyfél-naplófájljait is megtekintheti az _Azure log Analyticsban_. További részletekért látogasson el a [naplók és a mérőszámok elemzése a diagnosztikai beállításokkal](diagnostic-services.md) című oldalon.

Tekintse meg [az első lépéseket ismertető cikket](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) az _Azure log Analytics_megismeréséhez. A naplók lekérdezése [Kusto lekérdezési nyelv](https://docs.microsoft.com/azure/kusto/query/)használatával.

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Meg szeretném vizsgálni az alkalmazás környezeti változóit

A környezeti változók tájékoztatják az Azure Spring Cloud Framework szolgáltatást, hogy az Azure tisztában legyen azzal, hol és hogyan konfigurálhatja az alkalmazást alkotó szolgáltatásokat.  A környezeti változók helyességének biztosításához szükséges első lépés a lehetséges problémák elhárítása.  A Spring boot indítószerkezet végpontja segítségével áttekintheti a környezeti változókat.  

> [!WARNING]
> Ez az eljárás a környezeti változókat a tesztelési végponton keresztül teszi elérhetővé.  Ne folytassa, ha a tesztelési végpont nyilvánosan elérhető, vagy ha tartománynevet rendelt hozzá az alkalmazáshoz.

1. Navigáljon a következő URL-címre: `https://<your application test endpoint>/actuator/health`.  
    - A `{"status":"UP"}`hoz hasonló válasz azt jelzi, hogy a végpont engedélyezve van.
    - Ha a válasz negatív, vegye fel a következő függőséget a `POM.xml`ba:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Ha engedélyezve van a Spring boot indítószerkezet végpontja, lépjen a Azure Portalra, és keresse meg az alkalmazás konfigurációs lapját.  Adja hozzá a (z) `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE' and the value ` * "nevű környezeti változót. 

1. Indítsa újra az alkalmazást.

1. Navigáljon a `https://<the test endpoint of your app>/actuator/env`ra, és vizsgálja meg a választ.  A listának így kell kinéznie:

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

Keresse meg `systemEnvironment` nevű gyermek csomópontot.  Ez a csomópont tartalmazza az alkalmazás környezeti változóit.

> [!IMPORTANT]
> Ne felejtse el megfordítani a környezeti változók expozícióját, mielőtt az alkalmazása elérhetővé váljon a nyilvánosság számára.  Nyissa meg a Azure Portal, keresse meg az alkalmazás konfigurációs lapját, és törölje a következő környezeti változót: `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`.

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>Nem találom az alkalmazás metrikáit vagy naplóit

Nyissa meg az _app Management_ szolgáltatást, és _ellenőrizze, hogy_ _fut_ -e az alkalmazás.

Ha a _JVM_ metrikák láthatók, de a _tomcat_nem rendelkezik metrikával, akkor ellenőrizze, hogy a `spring-boot-actuator` függőség engedélyezve van-e az alkalmazáscsomag, és hogy sikeresen elindul-e.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Ha az alkalmazásnaplók archiválhatóak egy tárfiókba, de nem küldhetők el az _Azure Log Analyticsnek_, akkor ellenőrizze, hogy [megfelelően állította-e be a munkaterületet](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). Ha az _Azure log Analytics_ingyenes szintjét használja, vegye figyelembe, hogy [az ingyenes szint nem biztosít SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)-t.