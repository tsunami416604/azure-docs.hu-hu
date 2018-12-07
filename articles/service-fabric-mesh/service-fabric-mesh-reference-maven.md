---
title: Azure Service Fabric-háló Maven-referencia |} A Microsoft Docs
description: Tartalmazza a Maven beépülő modul használata a Service Fabric-háló referenciája
services: service-fabric-mesh
keywords: maven, a java, a parancssori felület
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.service: service-fabric-mesh
manager: subramar
ms.openlocfilehash: 08e842f5b91bd0ca5f8e8b2a7866f3f9a689ac28
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998996"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>A Service Fabric háló maven bővítménnyel

## <a name="prerequisites"></a>Előfeltételek

- Java SDK
- Maven
- Az Azure CLI-t a háló bővítmény
- Service Fabric parancssori felület

## <a name="goals"></a>Célok

### `azure-sfmesh:init`
- Létrehoz egy `servicefabric` mappába, amelyben egy `appresources` mappába, amely rendelkezik a `application.yaml` fájlt. 

### `azure-sfmesh:addservice`
- Létrehoz egy mappát `servicefabric` service nevű mappa, és a szolgáltatás YAML-fájlt hoz létre. 

### `azure-sfmesh:addnetwork`
- Létrehoz egy `network` a megadott hálózati nevű YAML a `appresources` mappa 

### `azure-sfmesh:addgateway`
- Létrehoz egy `gateway` megadott átjáró nevű YAML a `appresources` mappa 

### `azure-sfmesh:addsecret`
- Létrehoz egy `secret` lévő megadott titkos nevű YAML a `appresources` mappa 

### `azure-sfmesh:addsecretvalue`
- Létrehoz egy `secretvalue` lévő megadott titkos és a titkos érték nevű YAML a `appresources` mappa 

### `azure-sfmesh:deploy`
- A yamls a egyesíti a `servicefabric` mappa, és létrehoz egy Azure Resource Manager-sablon JSON-JÁT az aktuális mappában.
- Üzembe helyezi az összes erőforrást az Azure Service Fabric-háló környezetbe 

### `azure-sfmesh:deploytocluster`
- Létrehoz egy mappát (`meshDeploy`), amely tartalmazza a központi telepítés által létrehozott yamls, amelyek alkalmazhatók a Service Fabric-fürtök JSONs
- Telepíti a Service Fabric-fürt összes erőforrás
 

## <a name="usage"></a>Használat

A Maven-Java-alkalmazást a Maven beépülő modul használatához adja meg a következő kódrészletet a pom.xml fájlba:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>Gyakran alkalmazott konfiguráció

A Maven bővítménnyel jelenleg nem támogatja a Maven beépülő modulok általános konfigurációi az Azure-hoz.

## <a name="how-to"></a>Útmutató

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Maven-projektet inicializálása az Azure Service Fabric-háló
Futtassa a következő parancsot az erőforrás YAML-fájl létrehozásához.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Nevű mappát hoz létre `servicefabric->appresources` YAML nevű alkalmazás a gyökérmappa `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Erőforrás hozzáadása az alkalmazáshoz

#### <a name="add-a-new-network-to-your-application"></a>Egy új hálózati hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy hálózati erőforrás yaml létrehozásához. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.4/22
```

- Létrehoz egy hálózati YAML mappában `servicefabric->appresources` nevű `network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Egy új szolgáltatás hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy szolgáltatás yaml létrehozásához. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Létrehoz egy szolgáltatást YAML mappában `servicefabric->helloworldservice` nevű `service_helloworldservice` hivatkozik, amely `helloworldservicenetwork` & a `helloworldserver` alkalmazás
- A szolgáltatás akkor figyeljen a 8080-as porton
- A szolgáltatás szokott használni ***helloworldserver:latest*** van, mert a tároló rendszerképét.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Új átjáró-erőforrás hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy átjáró-erőforrás yaml létrehozásához. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Létrehoz egy új átjárót YAML mappában `servicefabric->appresources` nevű `gateway_helloworldgateway`
- Hivatkozások `helloworldservicelistener` , mely a hívások erről az átjáróról figyelőként szolgáltatás. Is hivatkozik a `helloworldservice` a szolgáltatásként `helloworldservicenetwork` a hálózattal és `helloworldserver` megegyezik az alkalmazáséval. 
- Figyeli a kéréseket a 80-as porton

#### <a name="add-a-new-volume-to-your-application"></a>Új kötet hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy kötet erőforrás yaml létrehozásához. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Létrehoz egy kötetet YAML mappában `servicefabric->appresources` nevű `volume_vol1`
- Szükséges paraméterek tulajdonságait állítja be `volumeAccountKey`, és `volumeShareName` a fentiek szerint
- Hogyan lehet hivatkozni a létrehozott kötet további információkért látogasson el a következő [telepíthető alkalmazás használatával az Azure Files-kötet](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Új titkos erőforrás hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy titkos erőforrás yaml létrehozásához. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Létrehoz egy titkos YAML mappában `servicefabric->appresources` nevű `secret_secret1`
- Hivatkozik, ez a titkos kód létrehozása módjáról további információkért látogasson el a következő [titkos kódok kezelése](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Új secretvalue erőforrás hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy secretvalue erőforrás yaml létrehozásához. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Hozzon létre egy YAML secretvalue mappában `servicefabric->appresources` nevű `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása

Cél segítségével `azure-sfmesh:deploytocluster`, futtathatja az alkalmazást helyileg az alábbi paranccsal:

```cmd
mvn azure-sfmesh:deploytocluster
```

Alapértelmezés szerint a cél helyi fürtön erőforrásokat helyez üzembe. A helyi fürtöt helyez üzembe, azt feltételezi, hogy a helyi Service Fabric-fürt működőképességét. Az erőforrások a helyi Service Fabric-fürt jelenleg kizárólag a támogatott [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- JSONs számmá yamls, amelyek alkalmazhatók a Service Fabric-fürtök
- Ezután üzembe helyezi a fürt azon végpontján

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Az Azure Service Fabric-háló üzembe helyezése

Cél segítségével `azure-sfmesh:deploy`, központilag telepíthető a Service Fabric-háló környezet az alábbi parancs futtatásával:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Létrehoz egy erőforráscsoportot, nevű `todoapprg` Ha még nem létezik.
- A YAMLs egyesítésével hoz létre egy Azure Resource Manager-sablon JSON-JÁT. 
- Az Azure Service Fabric-háló környezetben üzembe helyezi a JSON.