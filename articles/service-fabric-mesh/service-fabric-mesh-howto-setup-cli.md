---
title: Az Azure Service Fabric Mesh CLI beállítása
description: A Service Fabric Mesh parancssori felülete (CLI) szükséges az erőforrások helyi és Azure Service Fabric Mesh üzembe helyezéséhez és kezeléséhez. Itt van, hogyan kell beállítani.
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 0fb65ceeabca9331130083f8ec5b3fe8acce13b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259187"
---
# <a name="set-up-service-fabric-mesh-cli"></a>A Service Fabric Mesh parancssori felületének beállítása
A Service Fabric Mesh parancssori felülete (CLI) szükséges az erőforrások helyi és Azure Service Fabric Mesh üzembe helyezéséhez és kezeléséhez. Itt van, hogyan kell beállítani.

A CLI-knek három típusa használható, és az alábbi táblázatban összesítendő.

| CLI modul | Célkörnyezet |  Leírás | 
|---|---|---|
| az háló | Azure service fabric háló | Az elsődleges CLI, amely lehetővé teszi az alkalmazások üzembe helyezését és az erőforrások kezelését az Azure Service Fabric Mesh környezetben. 
| sfctl között | Helyi fürtök | Service Fabric CLI, amely lehetővé teszi a Service Fabric-erőforrások helyi fürtök elleni üzembe helyezését és tesztelését.  
| Maven CLI | Helyi fürtök & Azure Service Fabric Mesh | A wrapper `az mesh` `sfctl` körül, és amely lehetővé teszi a Java fejlesztők számára, hogy egy ismerős parancssori élményt a helyi és az Azure fejlesztési élményt.  

Az előzetes verzióban az Azure Service Fabric Mesh parancssori felülete az Azure CLI bővítményeként van megírva. Telepítheti az Azure Cloud Shellbe vagy az Azure CLI helyileg telepített példányába. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Az Azure Service Fabric Mesh CLI telepítése
1. Telepítenie kell az Azure CLI 2.0.67-es vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `az --version`. A parancssori felület (CLI) telepítéséhez vagy legújabb verzióra történő frissítéséhez lásd: [Az Azure CLI telepítése][azure-cli-install].

2. Telepítse az Azure Service Fabric Mesh CLI bővítménymodult a következő paranccsal. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Frissítsen egy meglévő Azure Service Fabric Mesh CLI modult a következő paranccsal.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>A Service Fabric CLI (sfctl) telepítése 

Kövesse a [Service Fabric CLI beállítása parancsra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)vonatkozó utasításokat. Az **sfctl** modul használható alkalmazások üzembe helyezéséhez az erőforrás-modell alapján a service fabric-fürtök a helyi gépen. 

## <a name="install-the-maven-cli"></a>Telepítse a Maven CLI-t 

A Maven CLI használatához a következőket kell telepíteni a gépre: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure Mesh CLI (az mesh) – Az Azure Service Fabric hálójának megcélzása 
* SFCTL (sfctl) – Helyi fürtök célzása 

A Maven CLI service fabric továbbra is előzetes verzióban. 

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
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

A részletes használatról a [Maven CLI referenciaszakaszban](service-fabric-mesh-reference-maven.md) olvashat.

## <a name="next-steps"></a>További lépések

A [Windows fejlesztési környezetet](service-fabric-mesh-howto-setup-developer-environment-sdk.md) is beállíthatja.

Válaszok a [gyakori kérdésekre és problémákra](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
