---
title: Azure Spring Cloud-alkalmazásnaplók streamelése valós időben
description: Az alkalmazás naplófájljainak azonnali megtekintése a log streaming használatával
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.custom: devx-track-java
ms.openlocfilehash: 45f892064d0910029db37c56f731a072a6c7514e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299609"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Azure Spring Cloud-alkalmazásnaplók streamelése valós időben
Az Azure Spring Cloud lehetővé teszi a log streaming használatát az Azure CLI-ben, hogy valós idejű Application Console-naplókat kapjon a hibaelhárításhoz. [A naplók és a metrikák is elemezhetők a diagnosztikai beállítások](./diagnostic-services.md)használatával.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse az [Azure CLI-bővítményt](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) a Spring Cloud számára, a minimális verzió 0.2.0.
* Egy futó alkalmazást használó **Azure Spring Cloud** -példány, például a [Spring Cloud app](./spring-cloud-quickstart.md).

> [!NOTE]
>  Az ASC CLI-bővítmény frissítve lett a 0.2.0 verzióról a 0.2.1-re. Ez a változás hatással van a naplózási streaming parancs szintaxisára: `az spring-cloud app log tail` , amelyet a következő váltja fel: `az spring-cloud app logs` . A következő parancs `az spring-cloud app log tail` egy későbbi kiadásban lesz elavult:. Ha már használta a 0.2.0 verziót, a 0.2.1-re frissíthet. Először távolítsa el a régi verziót a paranccsal: `az extension remove -n spring-cloud` .  Ezután telepítse a 0.2.1 a parancs használatával: `az extension add -n spring-cloud` .

## <a name="use-cli-to-tail-logs"></a>A CLI használata a farok naplóihoz

Az erőforráscsoport és a szolgáltatási példány nevének ismételt megadásának elkerüléséhez állítsa be az erőforráscsoport és a fürt alapértelmezett nevét.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
A következő példákban az erőforráscsoport és a szolgáltatás neve kimarad a parancsokban.

### <a name="tail-log-for-app-with-single-instance"></a>A farok naplója az alkalmazáshoz egyetlen példánnyal
Ha egy Auth-Service nevű alkalmazásnak csak egy példánya van, megtekintheti az alkalmazás példányának naplóját a következő paranccsal:
```
az spring-cloud app logs -n auth-service
```
Ez a naplókat fogja visszaadni:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Az alkalmazáshoz tartozó farok naplója több példányban
Ha a nevű alkalmazáshoz több példány is létezik, a (z `auth-service` ) kapcsoló használatával megtekintheti a példány naplóját `-i/--instance` . 

Először az alábbi paranccsal kérheti le az alkalmazás-példányok nevét.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Eredményekkel:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Ezt követően a lehetőséggel az alkalmazás egy példányát is továbbíthatja a naplókba `-i/--instance` :

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Az Azure Portalból is kérheti az alkalmazás-példányok részleteit.  Miután kiválasztotta az **alkalmazásokat** az Azure Spring Cloud Service bal oldali navigációs paneljén, válassza az alkalmazások **példányai**lehetőséget.

### <a name="continuously-stream-new-logs"></a>Új naplók folyamatos továbbítása
Alapértelmezés szerint `az spring-cloud ap log tail` a csak az alkalmazás-konzolra áramló meglévő naplókat nyomtatja ki, majd kilép. Ha új naplókat szeretne továbbítani, adja hozzá a-f (----követés):  

```
az spring-cloud app logs -n auth-service -f
``` 
A támogatott naplózási beállítások megtekintéséhez:
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>Következő lépések
* [Gyors útmutató: Azure Spring Cloud-alkalmazások figyelése naplókkal, metrikákkal és nyomkövetéssel](spring-cloud-quickstart-logs-metrics-tracing.md)
* [Naplók és mérőszámok elemzése diagnosztikai beállításokkal](./diagnostic-services.md)

 





