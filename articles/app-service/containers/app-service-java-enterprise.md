---
title: A Java Enterprise támogatási linuxon – az Azure App Service |} A Microsoft Docs
description: Fejlesztői útmutató a Java Enterprise alkalmazások Wildfly használata a Linuxon futó Azure App Service üzembe helyezése.
keywords: azure app service, web app, linux, oss, java, wildfly, enterprise, java ee, jee, javaee
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 8db65fd9a1f271aea4ceb345f4d9dfbb6b9ff8a6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877380"
---
# <a name="java-enterprise-guide-for-app-service-on-linux"></a>A linuxon futó App Service-ben a Java Enterprise útmutatója

> [!NOTE] 
> A Java Enterprise Edition App Service Linux rendszeren jelenleg előzetes verzióban érhető el. Ez a verem **nem** javasolt az éles néző munka. Tekintse át a [Java developer's guide](app-service-linux-java.md) információk a Java SE- és Tomcat implementációt.

Linuxon futó Azure App Service lehetővé teszi, hogy elkészítheti, telepítheti és méretezheti a Java Enterprise (Java EE-alapú) alkalmazásait egy teljes körűen felügyelt Linux-alapú szolgáltatás a Java-fejlesztőknek.  A Java Enterprise alapul szolgáló futtatókörnyezethez a nyílt forráskódú [Wildfly](https://wildfly.org/) alkalmazáskiszolgáló.

Ez az útmutató főbb fogalmakat és utasításokat a Java vállalati fejlesztők, az App Service linuxos használatával. Soha nem telepítette a Java-alkalmazások az Azure App Service Linux rendszeren, ha, hajtsa végre a [Java rövid](quickstart-java.md) első. A Linux App Service-ről, amely nem elég konkrét, a Java Enterprise kérdéseire a a [Java developer's guide](app-service-linux-java.md) és a [App Service Linux – gyakori kérdések](app-service-linux-faq.md).

## <a name="scale-with-app-service"></a>Méretezés App Service-ben 

A linuxon futó App Service-ben WildFly kiszolgáló önálló módban, nem tartományi konfigurációban futtatja. Horizontális felskálázás az App Service-csomag, az egyes WildFly példányok önálló kiszolgálóként van konfigurálva.

 Az alkalmazás méretezése vízszintesen vagy függőlegesen a [szabályok méretezése](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started?toc=%2Fazure%2Fapp-service%2Fcontainers%2Ftoc.json) , illetve [a példányszám növelése](https://docs.microsoft.com/azure/app-service/web-sites-scale?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json). 

## <a name="customize-application-server-configuration"></a>Alkalmazás-kiszolgálókonfiguráció testreszabása

Webes alkalmazás példányai állapot nélküli, így minden egyes új példány indítása kell konfigurálni az alkalmazás számára szükséges Wildfly konfiguráció támogatásához indításakor.
Írhat egy indítási Bash-szkript a WildFly parancssori felület meghívásához:

- Adatforrás létrehozása
- Üzenetkezelési-szolgáltatók konfigurálása
- Más modulok és a függőségek hozzáadása a Wildfly kiszolgáló konfigurációját.

  A parancsfájl Wildfly működik-e, de az alkalmazás indítása előtt lefut. A parancsfájlt kell használnia a [JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) volat z `/opt/jboss/wildfly/bin/jboss-cli.sh` bármely konfigurációs vagy a kiszolgáló újraindítása után szükséges változtatásokat a kiszolgáló konfigurálása. 

Ne használja az interaktív mód a parancssori felület Wildfly konfigurálásához. Helyette megadhat egy parancsfájlt a parancsok JBoss CLI használatával az `--file` parancsot, például:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Töltse fel az indítási parancsfájl `/home/site/deployments/tools` App Service-példányában. Lásd: [ebben a dokumentumban](/azure/app-service/deploy-configure-credentials#userscope) hitelesítő adatait az FTP beszerzésével kapcsolatban. 

Állítsa be a **indítási parancsfájl** mezőben az Azure Portalon, az indítási parancsfájl helyét például `/home/site/deployments/tools/your-startup-script.sh`.

Adja meg [Alkalmazásbeállítások](/azure/app-service/web-sites-configure#application-settings) átadandó környezeti változók a parancsfájl az alkalmazás konfigurációjában. Alkalmazásbeállítások tartsa meg a kapcsolati karakterláncok és egyéb titkok verziókövetés kívül az alkalmazás konfigurálásához szükséges.

## <a name="modules-and-dependencies"></a>Modulok és a függőségek

Modulok és azok függőségeit a Wildfly osztályútvonal JBoss parancssori felületén történő telepítéséhez szüksége lesz a következő fájlok létrehozása a saját címtárban.  Egyes modulok és a függőségek előfordulhat, hogy kell az például JNDI elnevezési további konfigurációs vagy más API-specifikus konfigurációs, így ez a lista a következőkre lesz szüksége a legtöbb esetben egy függőségi konfigurálása minimális készletét.

- Egy [XML modul leíró](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Az XML-fájl nevét, attribútumokat és függőségeit, a modul határozza meg. Ez [module.xml példafájl](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) Postgres modul, a JAR-fájl JDBC függőség és egyéb szükséges modul függőségek meghatározása.
- Minden szükséges JAR csomagfájlfüggőségekről a modul.
- A JBoss CLI-parancsokkal, az új modul konfigurálása parancsprogram. Ez a fájl tartalmazza a parancsok a JBoss parancssori felület beállítása a függőségi használni kívánt kiszolgálót. A parancs használatával adja hozzá a modulok, az adatforrások és a üzenetkezelési szolgáltatók dokumentációért lásd [ebben a dokumentumban](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
-  A Bash indítási szkript meghívása a JBoss parancssori Felületet, és hajtsa végre a parancsfájlt az előző lépésben. Ez a fájl lesz hajtható végre, ha az App Service-példány újraindítása, vagy ha új példányok horizontális felskálázás során felhasznált.  Az indítási szkript, ahol hajthatja végre minden egyéb konfigurációt az alkalmazáshoz, a JBoss parancsokat a rendszer átadja a JBoss CLI. Legalább Ez a fájl lehet az JBoss CLI parancshoz készített parancsfájl átadása a JBoss CLI egyetlen paranccsal: 
   
```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli` 
``` 

Ha a fájlok és tartalmak a modul, az alábbi lépésekkel, a modul hozzáadása a Wildfly kiszolgáló. 

1. FTP-fájlok `/home/site/deployments/tools` App Service-példányában. Ez a dokumentum útmutatást talál első FTP hitelesítő adatait. 
2. Az alkalmazás beállítások paneljén látható az Azure Portalon, a "Indítási parancsfájl" mezőt állítsa az indítási parancsfájl helyét például `/home/site/deployments/tools/your-startup-script.sh` .
3. Az App Service-példány újraindítása billentyűkombináció lenyomásával a **indítsa újra a** gombra a **áttekintése** szakaszában a portálon vagy az Azure CLI használatával.

## <a name="data-sources"></a>Adatforrások

Az adatforrás-kapcsolat Wildfly konfigurálásához kövesse a modulok telepítése és a függőségek szakaszban fent leírt eljárást. Követheti ugyanezeket a lépéseket minden olyan Azure-adatbázis szolgáltatás.

1. Az adatbázis íz a JDBC-illesztőprogram letöltése. Az egyszerűség kedvéért az alábbiakban a illesztőprogramokat [Postgres](https://jdbc.postgresql.org/download.html) és [MySQL](https://dev.mysql.com/downloads/connector/j/). A letöltés beolvasni a .jar-fájl kicsomagolása.
2. Kövesse a lépéseket vázlat "Modul és függőségei" létrehoznia és feltöltenie az XML-modul leíró, JBoss CLI-példaszkript, indítási szkriptet és .jar JDBC-függőséget a.


A Wildfly konfigurálásáról [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7) , [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource), és [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) érhető el. Ezeket testre szabott útmutatót, valamint a fent az általános megközelítés segítségével adatforrás-definíciók ad hozzá a kiszolgálóhoz.

## <a name="messaging-providers"></a>Üzenetkezelési szolgáltatók

Service Bus üzenetkezelési módszerként használatával készített üzenet bab engedélyezése:

1. Használja a [Apache QPId JMS üzenetküldési kódtárat](https://qpid.apache.org/proton/index.html). Az alkalmazás ezt a függőséget a pom.xml (vagy más build-fájl) részeként.

2.  Hozzon létre [Service Bus-erőforrások](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Hozzon létre egy Azure Service Bus-névtér és üzenetsor belül az adott névtérben és a egy megosztott elérési házirendet küldési, és a szolgáltatásokat.

3. Adja át a megosztott elérési házirend kulcsa a programkód ehhez az URL-Címének kódolása vagy a szabályzat az elsődleges kulcs vagy [a Service Bus SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. A JMS szolgáltató a modul XML leíró, .jar függőségek, a JBoss CLI-parancsok és indítási szkriptet a modulok telepítése és a függőségek szakaszban leírt lépésekkel. A négy fájlok mellett is szüksége lesz a JMS üzenetsorokkal és JNDI nevét definiáló XML-fájl létrehozásához. Lásd: [tárházhoz](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) referencia konfigurációs fájlok.


## <a name="configure-session-management-caching"></a>Munkamenet-kezelés gyorsítótár konfigurálása

Alapértelmezés szerint a linuxon futó App Service használja munkamenet-affinitási cookie-kat, hogy az, hogy a meglévő munkameneteket ügyfélkérelmek vannak irányítva az alkalmazás példányát. Ez az alapértelmezett viselkedés nem igényel konfigurálást, de vannak bizonyos korlátai:

- Ha egy alkalmazáspéldány újraindítják, vagy horizontálisan, a kiszolgáló a felhasználói munkamenet-állapot elvesznek.
- Ha alkalmazások hosszú munkamenet időtúllépés beállításai vagy a felhasználók rögzített számú, új példányok terhelést kap, mivel csak az új munkamenetek a rendszer átirányítja az újonnan elindított példányok maximumára egy kis ideig is eltarthat.

Beállíthatja, hogy egy külső állapotszolgáltatót tároló használandó Wildfly [Azure Cache redis](/azure/azure-cache-for-redis/). Kell [tiltsa le a meglévő ARR-példány affinitás](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) konfigurációját, és kapcsolja ki a munkamenetek cookie-alapú útválasztást, és beavatkozás nélkül konfigurált Wildfly munkamenet áruház engedélyezése.

## <a name="enable-web-sockets"></a>Webes szoftvercsatornák engedélyezése

Alapértelmezés szerint a web sockets engedélyezve vannak az App Service-ben. Első lépések a websockets protokoll az alkalmazásban, tekintse meg [ebben a rövid útmutatóban](https://github.com/wildfly/quickstart/tree/master/websocket-hello).

## <a name="logs-and-troubleshooting"></a>Naplók és hibaelhárítás

Az App Service biztosítja az eszközöket, az alkalmazással kapcsolatos problémák elhárításához.

-   Kapcsolja be a naplózást kattintva **diagnosztikai naplók** a bal oldali navigációs ablaktáblán. Kattintson a **fájlrendszer** a storage-kvóták és -megőrzési időszak beállítása, és mentse a módosításokat. Ezek a naplók alatt található `/home/LogFiles/`.
-   [Az SSH használata a az alkalmazás-példányhoz való csatlakozáshoz](app-service-linux-ssh-support.md) alkalmazások futtatásához a naplók megtekintéséhez.
-   Ellenőrizze a diagnosztikai naplók az a **diagnosztikai naplók** panel a portálon, vagy az Azure CLI-paranccsal: `az webapp log tail --name <your-app-name> --resource-group <your-apps-resource-group>`
