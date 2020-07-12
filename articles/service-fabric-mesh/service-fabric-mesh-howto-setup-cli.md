---
title: Az Azure Service Fabric Mesh parancssori felületének beállítása
description: Service Fabric Mesh parancssori felület (CLI) szükséges az erőforrások helyi és Azure-beli Service Fabric Mesh-beli üzembe helyezéséhez és kezeléséhez. Itt állíthatja be.
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 3ce2dbcefa63cc55a66e712664d1436ca24d3a1d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248526"
---
# <a name="set-up-service-fabric-mesh-cli"></a>A Service Fabric Mesh parancssori felületének beállítása
Service Fabric Mesh parancssori felület (CLI) szükséges az erőforrások helyi és Azure-beli Service Fabric Mesh-beli üzembe helyezéséhez és kezeléséhez. Itt állíthatja be.

A CLI három típusa használható, és az alábbi táblázatban láthatók.

| CLI-modul | Cél környezet |  Leírás | 
|---|---|---|
| az Mesh | Azure Service Fabric Mesh | Az elsődleges CLI, amely lehetővé teszi az alkalmazások üzembe helyezését és az erőforrások kezelését az Azure Service Fabric Mesh-környezettel. 
| sfctl | Helyi fürtök | Service Fabric parancssori felület, amely lehetővé teszi Service Fabric erőforrások üzembe helyezését és tesztelését helyi fürtökön.  
| Maven parancssori felület | Helyi fürtök & Azure Service Fabric Mesh | Egy burkoló, `az mesh` `sfctl` amely lehetővé teszi a Java-fejlesztők számára, hogy ismerős parancssori felületet használjanak a helyi és az Azure-beli fejlesztési élményhez.  

Az előzetes verzióban az Azure Service Fabric Mesh parancssori felülete az Azure CLI bővítményeként van megírva. Telepítheti az Azure Cloud Shellbe vagy az Azure CLI helyileg telepített példányába. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Az Azure Service Fabric Mesh parancssori felületének telepítése
1. Telepítenie kell az Azure CLI 2.0.67 vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `az --version`. A parancssori felület (CLI) telepítéséhez vagy legújabb verzióra történő frissítéséhez lásd: [Az Azure CLI telepítése][azure-cli-install].

2. Telepítse az Azure Service Fabric Mesh CLI bővítmény modulját a következő parancs használatával. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Frissítsen egy meglévő Azure Service Fabric Mesh CLI-modult az alábbi parancs használatával.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>A Service Fabric parancssori felület (sfctl) telepítése 

Kövesse az [Service FABRIC parancssori felület beállításának](../service-fabric/service-fabric-cli.md)utasításait. A **sfctl** modul a helyi gépen lévő Service Fabric-fürtökön az erőforrás-modellen alapuló alkalmazások telepítéséhez használható. 

## <a name="install-the-maven-cli"></a>A Maven parancssori felületének telepítése 

A Maven parancssori felület használatához a következőket kell telepíteni a gépre: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure Mesh CLI (az mesh) – az Azure Service Fabric Mesh megcélzása 
* SFCTL (SFCTL) – a helyi fürtök célzása 

A Service Fabric Maven parancssori felülete még előzetes verzióban érhető el. 

Ha a Maven Java-alkalmazásban szeretné használni a Maven beépülő modult, adja hozzá a következő kódrészletet a pom.xml fájlhoz:

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
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

A részletes használatról a [MAVEN CLI-referenciáját](service-fabric-mesh-reference-maven.md) ismertető szakaszban olvashat.

## <a name="next-steps"></a>Következő lépések

A [Windows fejlesztési környezetet](service-fabric-mesh-howto-setup-developer-environment-sdk.md) is beállíthatja.

Válaszok a [gyakori kérdésekre és problémákra](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
