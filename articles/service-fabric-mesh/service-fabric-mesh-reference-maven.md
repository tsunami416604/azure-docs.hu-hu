---
title: Azure Service Fabric Mesh Maven-dokumentáció
description: A Maven beépülő modul Service Fabric rácsvonalhoz való használatának referenciáját tartalmazza
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.openlocfilehash: bcc3fb7c6c3adce0997d0960c4d98227089b048b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75459017"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Maven beépülő modul a Service Fabric Meshhoz

## <a name="prerequisites"></a>Előfeltételek

- Java SDK
- Maven
- Azure CLI Mesh bővítménnyel
- Service Fabric parancssori felület

## <a name="goals"></a>Célok

### `azure-sfmesh:init`
- Létrehoz egy `servicefabric` mappát, amely tartalmazza `appresources` a `application.yaml` fájlt tartalmazó mappát. 

### `azure-sfmesh:addservice`
- Létrehoz egy mappát a `servicefabric` mappán belül a szolgáltatás nevével, és létrehozza a szolgáltatás YAML-fájlját. 

### `azure-sfmesh:addnetwork`
- Létrehoz egy `network` YAML a megadott hálózati névvel a `appresources` mappában. 

### `azure-sfmesh:addgateway`
- Létrehoz egy `gateway` YAML a megadott átjáró nevével a `appresources` mappában. 

#### `azure-sfmesh:addvolume`
- Létrehoz egy `volume` YAML a megadott kötet nevével a `appresources` mappában.

### `azure-sfmesh:addsecret`
- Létrehoz egy `secret` YAML a megadott titkos névvel a `appresources` mappában. 

### `azure-sfmesh:addsecretvalue`
- Létrehoz egy `secretvalue` YAML a megadott titkos és titkos érték nevével a `appresources` mappában. 

### `azure-sfmesh:deploy`
- Egyesíti a yamls a `servicefabric` mappából, és létrehoz egy Azure Resource Manager sablon JSON-t az aktuális mappában.
- Az összes erőforrás üzembe helyezése az Azure Service Fabric Mesh-környezetben 

### `azure-sfmesh:deploytocluster`
- Létrehoz egy mappát (`meshDeploy`), amely tartalmazza a yamls létrehozott telepítési JSON-ket, amelyek Service Fabric fürtökre alkalmazhatók
- Az összes erőforrás üzembe helyezése a Service Fabric-fürtön
 

## <a name="usage"></a>Használat

Ha a Maven Java-alkalmazásban szeretné használni a Maven beépülő modult, adja hozzá a következő kódrészletet a Pom. xml fájlhoz:

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

## <a name="common-configuration"></a>Közös konfiguráció

A Maven beépülő modul jelenleg nem támogatja az Azure-hoz készült Maven beépülő modulok általános konfigurációit.

## <a name="how-to"></a>Útmutató

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Maven-projekt inicializálása az Azure Service Fabric Meshhoz
Futtassa az alábbi parancsot az alkalmazás-erőforrás YAML fájl létrehozásához.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Létrehoz egy nevű mappát `servicefabric->appresources` a gyökérkönyvtárban, amely egy nevű alkalmazás-YAML tartalmaz.`app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Erőforrás hozzáadása az alkalmazáshoz

#### <a name="add-a-new-network-to-your-application"></a>Új hálózat hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy hálózati erőforrás YAML létrehozásához. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Létrehoz egy hálózati YAML a ( `servicefabric->appresources` z) nevű mappában.`network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Új szolgáltatás hozzáadása az alkalmazáshoz
A szolgáltatás YAML létrehozásához futtassa az alábbi parancsot. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- `servicefabric->helloworldservice` Létrehoz egy YAML a nevű `service_helloworldservice` mappában, amely & `helloworldservicenetwork` az `helloworldserver` alkalmazásra hivatkozik
- A szolgáltatás a 8080-es portot figyeli.
- A szolgáltatás a ***helloworldserver: Latest*** használja, mivel a tároló képe.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Új átjáró-erőforrás hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy átjáró-erőforrás YAML létrehozásához. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Új átjáró YAML létrehozása a (z `servicefabric->appresources` ) nevű mappában`gateway_helloworldgateway`
- Az `helloworldservicelistener` átjárótól érkező hívásokat figyelő szolgáltatás-figyelőre mutató hivatkozások. A a szolgáltatásra, `helloworldservice` `helloworldservicenetwork` a hálózatra és `helloworldserver` az alkalmazásra is hivatkozik. 
- Az 80-es porton érkező kérések figyelése

#### <a name="add-a-new-volume-to-your-application"></a>Új kötet hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy mennyiségi erőforrás YAML létrehozásához. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Létrehoz egy kötet YAML a ( `servicefabric->appresources` z) nevű mappában.`volume_vol1`
- Beállítja a szükséges paraméterek tulajdonságait, `volumeAccountKey`és `volumeShareName` a fentiek szerint
- A létrehozott kötetre való hivatkozással kapcsolatos további információkért tekintse meg a következőt, és [telepítse az alkalmazást Azure Files kötet használatával](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Új titkos erőforrás hozzáadása az alkalmazáshoz
Az alábbi parancs futtatásával hozzon létre egy titkos erőforrás-YAML. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Létrehoz egy titkos YAML a ( `servicefabric->appresources` z) nevű mappában.`secret_secret1`
- A létrehozott titokra való hivatkozással kapcsolatos további információkért tekintse meg a következőt: [titkok kezelése](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Új secretvalue-erőforrás hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy secretvalue erőforrás-YAML létrehozásához. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Hozzon létre egy secretvalue- `servicefabric->appresources` YAML a (z) nevű mappában`secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása

A cél `azure-sfmesh:deploytocluster`segítségével helyileg is futtathatja az alkalmazást az alábbi parancs használatával:

```cmd
mvn azure-sfmesh:deploytocluster
```

Alapértelmezés szerint ez a cél telepíti az erőforrásokat a helyi fürtre. Ha helyi fürtre telepíti a szolgáltatást, feltételezi, hogy rendelkezik egy helyi Service Fabric-fürttel. Az erőforrásokhoz tartozó helyi Service Fabric-fürt jelenleg csak [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md)rendszeren támogatott.

- A yamls származó JSON-ket hoz létre, amelyek Service Fabric fürtökre alkalmazhatók
- Üzembe helyezés a fürt végpontján

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Alkalmazás üzembe helyezése az Azure Service Fabric Meshban

A cél `azure-sfmesh:deploy`segítségével az alábbi parancs futtatásával üzembe helyezheti Service Fabric Mesh-környezetet:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Létrehoz egy nevű `todoapprg` erőforráscsoportot, ha az nem létezik.
- Létrehoz egy Azure Resource Manager sablon JSON-t a YAMLs egyesítésével. 
- Üzembe helyezi a JSON-t az Azure Service Fabric Mesh-környezetben.
