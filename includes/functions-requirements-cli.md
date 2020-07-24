---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3f5c7021461b407e90d61ed045c112aab3c1c4e9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87055994"
---
## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java"  
+ A [Azure functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) verziója 2.7.1846 vagy újabb 2. x verzió.
::: zone-end  
::: zone pivot="programming-language-python"
+ Az Azure Functions Core Tools verziója, amely megfelel a telepített Python-verziónak:

   | Python-verzió | A Core Tools verziója |
   | -------------- | ------------------ |
   | Python 3,8     | [3. x verzió](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3,6<br/>Python 3.7 | [2.7.1846 vagy újabb verzió](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ Az [Azure CLI](/cli/azure/install-azure-cli) 2,4-es vagy újabb verziója. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), aktív LTS-és karbantartási LTS-verziók (8.11.1 és 10.14.1 ajánlott).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3,8 (64 bites)](https://www.python.org/downloads/release/python-382/), [python 3,7 (64-bit)](https://www.python.org/downloads/release/python-375/), [Python 3,6 (64-bit)](https://www.python.org/downloads/release/python-368/), amelyet a Azure functions támogat. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ A [.net Core SDK 2.2 +](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ A [Java fejlesztői csomag](https://aka.ms/azure-jdks)8-as verziója. 

    > [!IMPORTANT]
    > + A Java 11 funkcióinak támogatása jelenleg előzetes verzióban érhető el, a Maven archetípus pedig alapértelmezés szerint egy Java 8 központi telepítést hoz létre. Ha ehelyett Java 11 rendszeren szeretné futtatni a Function alkalmazást, manuálisan kell frissítenie az pom.xml fájlt Java 11 értékkel. További információ: Java- [verziók](../articles/azure-functions/functions-reference-java.md#java-versions). 
    > + A rövid útmutató `JAVA_HOME` elvégzéséhez a környezeti változót a JDK megfelelő verziójának telepítési helyére kell beállítani.

+ [Apache Maven](https://maven.apache.org), 3,0-es vagy újabb verzió.

::: zone-end
