---
title: Azure Spring Cloud-alkalmazás naplók valós idejű továbbítása
description: Az alkalmazás naplófájljainak azonnali megtekintése a log streaming használatával
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: 27978d367ded7a31d73949cd675ae9e6f8cb887c
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263999"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Azure Spring Cloud-alkalmazás naplók valós idejű továbbítása
Az Azure Spring Cloud lehetővé teszi a log streaming használatát az Azure CLI-ben, hogy valós idejű Application Console-naplókat kapjon a hibaelhárításhoz. [A naplók és a metrikák is elemezhetők a diagnosztikai beállítások](./diagnostic-services.md)használatával.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse az [Azure CLI-bővítményt](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) a Spring Cloud számára, a minimális verzió 0.2.0.
* Egy futó alkalmazást használó **Azure Spring Cloud** -példány, például a [Spring Cloud app](./spring-cloud-quickstart-launch-app-cli.md).

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
az spring-cloud app log tail -n auth-service
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
Ha a `auth-service`nevű alkalmazáshoz több példány létezik, a `-i/--instance` lehetőség használatával megtekintheti a példány naplóját. 

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
Ezután a `-i/--instance` kapcsolóval is elvégezheti az alkalmazás-példányok naplófájljainak továbbítását:

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Az Azure Portalból is kérheti az alkalmazás-példányok részleteit.  Miután kiválasztotta az **alkalmazásokat** az Azure Spring Cloud Service bal oldali navigációs paneljén, válassza az alkalmazások **példányai**lehetőséget.

### <a name="continuously-stream-new-logs"></a>Új naplók folyamatos továbbítása
Alapértelmezés szerint a `az spring-cloud ap log tail` csak az App Console-ba továbbított meglévő naplókat nyomtatja ki, majd kilép. Ha új naplókat szeretne továbbítani, adja hozzá a-f (----követés):  

```
az spring-cloud app log tail -n auth-service -f
``` 
A támogatott naplózási beállítások megtekintéséhez:
``` 
az spring-cloud app log tail -h 
```

## <a name="next-steps"></a>Következő lépések

* [Naplók és mérőszámok elemzése diagnosztikai beállításokkal](./diagnostic-services.md)

 





