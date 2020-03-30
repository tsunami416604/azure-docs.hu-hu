---
title: Azure Spring Cloud-alkalmazásnaplók streamelése valós időben
description: A naplóstreamelés használata az alkalmazásnaplók azonnali megtekintéséhez
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fc208a3542528fb4554a365a02e13c2da3055cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192200"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Azure Spring Cloud-alkalmazásnaplók streamelése valós időben
Az Azure Spring Cloud lehetővé teszi a naplózási streamelést az Azure CLI-ben, hogy valós idejű alkalmazáskonzolnaplókat kapjon a hibaelhárításhoz. [A diagnosztikai beállításokkal naplónaplókat és metrikákat is elemezhet.](./diagnostic-services.md)

## <a name="prerequisites"></a>Előfeltételek

* Telepítse [az Azure CLI-bővítményt](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) a Spring Cloud szolgáltatásra, minimum 0.2.0-s verzióhoz.
* Az **Azure Spring Cloud** egy futó alkalmazással, például spring cloud [alkalmazással.](./spring-cloud-quickstart-launch-app-cli.md)

> [!NOTE]
>  Az ASC CLI bővítmény 0.2.0-ról 0.2.1-re frissül. Ez a módosítás a naplóstreamelési `az spring-cloud app log tail`parancs szintaxisát érinti: . . `az spring-cloud app logs` A parancs: `az spring-cloud app log tail` egy későbbi kiadásban elavult. Ha a 0.2.0-s verziót használta, frissíthet a 0.2.1-es verzióra. Először távolítsa el a régi `az extension remove -n spring-cloud`verziót a paranccsal: .  Ezután telepítse a 0.2.1-et a következő paranccsal: `az extension add -n spring-cloud`.

## <a name="use-cli-to-tail-logs"></a>Cli használata a faroknaplókhoz

Az erőforráscsoport és a szolgáltatáspéldány nevének ismételt megadásához adja meg az alapértelmezett erőforráscsoport- és fürtnevet.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
A következő példákban az erőforráscsoport és a szolgáltatás neve kimarad a parancsokból.

### <a name="tail-log-for-app-with-single-instance"></a>Tail log alkalmazás egyetlen példányban
Ha egy auth-service nevű alkalmazás csak egy példánysal rendelkezik, az alkalmazáspéldány naplóját a következő paranccsal tekintheti meg:
```
az spring-cloud app logs -n auth-service
```
Ez a következő naplókat adja vissza:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Faroknapló több példányt tartalmazó alkalmazáshoz
Ha több példány létezik `auth-service`a nevű alkalmazáshoz, a `-i/--instance` beállítással megtekintheti a példánynaplót. 

Először a következő paranccsal kaphatja le az alkalmazáspéldányok nevét.

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
Ezt követően streamelheti egy alkalmazáspéldány `-i/--instance` naplóit a következő lehetőséggel:

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Az alkalmazáspéldányok részleteit az Azure Portalon is beszerezheti.  Miután kiválasztotta az **Alkalmazások** lehetőséget az Azure Spring Cloud szolgáltatás bal oldali navigációs ablakában, válassza **az Alkalmazáspéldányok**lehetőséget.

### <a name="continuously-stream-new-logs"></a>Új naplók folyamatos streamelése
Alapértelmezés szerint `az spring-cloud ap log tail` csak az alkalmazáskonzolra streamelt meglévő naplókat nyomtatja ki, majd kilép. Ha új naplókat szeretne streamelni, adja hozzá a -f (--follow) értéket:  

```
az spring-cloud app logs -n auth-service -f
``` 
Az összes támogatott naplózási beállítás ellenőrzése:
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>További lépések

* [Naplók és metrikák elemzése diagnosztikai beállításokkal](./diagnostic-services.md)

 





