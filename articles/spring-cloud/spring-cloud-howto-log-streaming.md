---
title: Azure Spring Cloud-alkalmazás naplók valós idejű továbbítása
description: Az alkalmazás naplófájljainak azonnali megtekintése a log streaming használatával
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fa2e7af51ff681da0bfdac928cc08bf75126a3b8
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156420"
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
Ha a `auth-service`nevű alkalmazáshoz több példány létezik, a `-i/--instance` lehetőség használatával megtekintheti a példány naplóját. Például az alkalmazás nevének és példánynév megadásával továbbíthatja egy alkalmazás egy példányának naplóját:

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```
Az alkalmazás példányai a Azure Portalból is beszerezhetők. 
1. Navigáljon az erőforráscsoporthoz, és válassza ki az Azure Spring Cloud-példányát.
1. Az Azure Spring Cloud instance áttekintése lapon válassza az **alkalmazások** lehetőséget a bal oldali navigációs ablaktáblán.
1. Válassza ki az alkalmazást, majd kattintson a bal oldali navigációs ablaktábla **alkalmazások példányai** elemére. 
1. Ekkor megjelenik az alkalmazás példányai.

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

 





