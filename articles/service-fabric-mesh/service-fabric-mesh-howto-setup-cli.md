---
title: Az Azure Service Fabric Mesh parancssori felületének beállítása | Microsoft Docs
description: Ez a dokumentum az Azure Service Fabric Mesh parancssori felület beállítását ismerteti.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: d28776b8501a1467a77cd0ad4225d1267d768052
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767816"
---
# <a name="set-up-service-fabric-mesh-cli"></a>A Service Fabric Mesh parancssori felületének beállítása
Service Fabric-háló parancssori felület (CLI) telepítése és a helyileg és az Azure Service Fabric-háló-erőforrások kezelése szükséges. 

Használható CLI három típusa van, és azokat az alábbi táblázat foglalja össze. 

| CLI-modul | Célkörnyezet |  Leírás | 
|---|---|---|
| az háló | Az Azure Service Fabric háló | Az elsődleges CLI, amely lehetővé teszi, hogy az alkalmazások üzembe helyezése és kezelése az Azure Service Fabric-háló környezetre leselkedő erőforrások. 
| sfctl | Helyi fürt | Service Fabric parancssori felület, amely lehetővé teszi a központi telepítési és tesztelése a Service Fabric-erőforrások helyi fürtök ellen.  
| Maven parancssori felület | Helyi fürt és az Azure Service Fabric-háló | "Az háló" és "sfctl" burkolója, amely lehetővé teszi a Java fejlesztőknek, hogy egy ismerős parancssori élmény a helyi és az Azure fejlesztési környezetet biztosít.  

Az előzetes verzióban az Azure Service Fabric Mesh parancssori felülete az Azure CLI bővítményeként van megírva. Telepítheti az Azure Cloud Shellbe vagy az Azure CLI helyileg telepített példányába. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Az Azure Service Fabric háló parancssori felület telepítése
1. Telepítenie kell az Azure CLI 2.0.43 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. A parancssori felület (CLI) telepítéséhez vagy legújabb verzióra történő frissítéséhez lásd: [Az Azure CLI telepítése][azure-cli-install].

2. Az Azure Service Fabric háló parancssori bővítmény modul a következő paranccsal telepítse. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. A következő parancsot meglévő Azure Service Fabric háló parancssori modulok frissítésére.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Telepítse a Service Fabric parancssori felület (sfctl) 

Kövesse az utasításokat [Service Fabric parancssori felület beállítása](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli). A **sfctl** modul szemben a Service Fabric-fürtök a helyi gépen az erőforrás-modellen alapuló alkalmazások telepítéséhez is használható. 

## <a name="install-the-maven-cli"></a>A Maven parancssori felület telepítése 

A Maven parancssori Felületet, a következő kell telepítve lennie a gépen használatához: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven 3](http://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure háló CLI (az háló) – az Azure Service Fabric-háló cél 
* SFCTL (sfctl) –, amelyekre a helyi fürt 

A Maven parancssori felület a Service fabric továbbra is előzetes verzióban van. 

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
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Olvassa el a [Maven CLI-referenciáját](service-fabric-mesh-reference-maven.md) című szakaszt a részletes használati adatok ismertetése.

## <a name="next-steps"></a>További lépések

A [Windows fejlesztési környezetet](service-fabric-mesh-howto-setup-developer-environment-sdk.md) is beállíthatja.

Válaszok a [gyakori kérdésekre és problémákra](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
