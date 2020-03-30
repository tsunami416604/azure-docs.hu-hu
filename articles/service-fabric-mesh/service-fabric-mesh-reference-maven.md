---
title: Az Azure Service Fabric Mesh Maven hivatkozás
description: A Service Fabric Mesh Maven beépülő moduljának használatára vonatkozó hivatkozást tartalmazza
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.openlocfilehash: bcc3fb7c6c3adce0997d0960c4d98227089b048b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75459017"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Maven beépülő modul a szolgáltatásháló hálójához

## <a name="prerequisites"></a>Előfeltételek

- Java SDK
- Maven
- Azure CLI hálós kiterjesztéssel
- Service Fabric parancssori felület

## <a name="goals"></a>Célok

### `azure-sfmesh:init`
- Létrehoz `servicefabric` egy mappát, amely egy mappát `appresources` tartalmaz, amely tartalmazza a `application.yaml` fájlt. 

### `azure-sfmesh:addservice`
- Létrehoz egy `servicefabric` mappát a szolgáltatás nevével a mappában, és létrehozza a szolgáltatás YAML-fájlját. 

### `azure-sfmesh:addnetwork`
- `network` YAML létrehozása a `appresources` mappában megadott hálózati névvel 

### `azure-sfmesh:addgateway`
- `gateway` YAML létrehozása a `appresources` mappában megadott átjárónévvel 

#### `azure-sfmesh:addvolume`
- Létrehoz egy `volume` YAML-t a `appresources` mappában megadott kötetnévvel.

### `azure-sfmesh:addsecret`
- `secret` YAML létrehozása a `appresources` mappában megadott titkos névvel 

### `azure-sfmesh:addsecretvalue`
- `secretvalue` YAML létrehozása a megadott titkos és titkos `appresources` értéknévvel a mappában 

### `azure-sfmesh:deploy`
- Egyesíti a yamls `servicefabric` a mappából, és létrehoz egy Azure Resource Manager sablon JSON az aktuális mappában.
- Az összes erőforrás üzembe helyezése az Azure Service Fabric Mesh környezetben 

### `azure-sfmesh:deploytocluster`
- Létrehoz egy`meshDeploy`mappát ( ), amely tartalmazza a Service Fabric-fürtökre vonatkozó yamls-ből létrehozott telepítési JSON-okat
- Az összes erőforrás központi telepítése a Service Fabric-fürtre
 

## <a name="usage"></a>Használat

Ha a Maven Java alkalmazásban szeretné használni a Maven beépülő modult, adja hozzá a következő részletet a pom.xml fájlhoz:

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

## <a name="common-configuration"></a>Gyakori konfiguráció

A Maven plugin jelenleg nem támogatja a Maven Plugins for Azure közös konfigurációit.

## <a name="how-to"></a>Útmutató

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Maven-projekt inicializálása az Azure Service Fabric Mesh számára
Futtassa a következő parancsot az alkalmazáserőforrás YAML-fájljának létrehozásához.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Létrehoz egy `servicefabric->appresources` mappát, amelyet a gyökérmappában hívnak meg, és amely egy YAML nevű alkalmazást tartalmaz`app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Erőforrás hozzáadása az alkalmazáshoz

#### <a name="add-a-new-network-to-your-application"></a>Új hálózat hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot, hogy hozzon létre egy hálózati erőforrás yaml. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Hálózati YAML létrehozása `servicefabric->appresources` a nevű mappában`network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Új szolgáltatás hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot, hogy hozzon létre egy szolgáltatást yaml. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Létrehoz egy szolgáltatás `servicefabric->helloworldservice` YAML `service_helloworldservice` nevű `helloworldservicenetwork` mappában, amely az `helloworldserver` alkalmazás & hivatkozik
- A szolgáltatás a 8080-as porton hallgatná
- A szolgáltatás a ***helloworldserver:latest-t*** használja, mivel az a tárolórendszerkép.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Új átjáróerőforrás hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy átjáró-erőforrás létrehozásához yaml. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Új yaml átjáró létrehozása `servicefabric->appresources` a névalatt elnevezett mappában`gateway_helloworldgateway`
- `helloworldservicelistener` Hivatkozások, mint a szolgáltatás figyelője, amely figyeli az átjáróról érkező hívásokat. Is hivatkozik `helloworldservice` a szolgáltatás, `helloworldservicenetwork` mint `helloworldserver` a hálózat és az alkalmazás. 
- A 80-as porton érkező kérések figyelése

#### <a name="add-a-new-volume-to-your-application"></a>Új kötet hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot, hogy hozzon létre egy kötet erőforrás yaml. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Kötet YAML létrehozása `servicefabric->appresources` a nevű mappában`volume_vol1`
- A szükséges paraméterek tulajdonságainak `volumeAccountKey` `volumeShareName` beállítása a megfelelő módon
- A létrehozott kötetre való hivatkozással kapcsolatos további információkért látogasson el a következő, [Alkalmazás telepítése az Azure Files Volume használatával című alkalmazásra.](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Új titkos erőforrás hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy titkos erőforrás létrehozásához yaml. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Titkos YAML létrehozása `servicefabric->appresources` a nevű mappában`secret_secret1`
- Ha többet szeretne tudni arról, hogyan hivatkozhat erre a létrehozott titokra, látogasson el a következő, [Titkok kezelése](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Új titkos értékerőforrás hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy secretvalue erőforrás létrehozásához yaml. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Secretvalue YAML létrehozása `servicefabric->appresources` a nevű mappában`secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása

A cél `azure-sfmesh:deploytocluster`segítségével az alábbi paranccsal helyileg futtathatja az alkalmazást:

```cmd
mvn azure-sfmesh:deploytocluster
```

Alapértelmezés szerint ez a cél erőforrásokat telepít a helyi fürtre. Ha helyi fürtre telepít, feltételezi, hogy egy helyi Service Fabric-fürt ötössel működik. Az erőforrások helyi szolgáltatásháló-fürtje jelenleg csak a [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md)rendszeren támogatott.

- JSON-okat hoz létre a Service Fabric-fürtökre vonatkozó yamls-ből
- Ezután telepíti a fürtvégpontot

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Alkalmazás üzembe helyezése az Azure Service Fabric Mesh szolgáltatásban

A cél `azure-sfmesh:deploy`segítségével az alábbi parancs futtatásával telepítheti a Service Fabric Hálókörnyezetbe:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Létrehoz egy erőforráscsoportot, amelyet akkor hívnak meg, `todoapprg` ha nem létezik.
- Létrehoz egy Azure Resource Manager sablon JSON a YALl-ek egyesítésével. 
- A JSON üzembe helyezése az Azure Service Fabric Mesh környezetben.
