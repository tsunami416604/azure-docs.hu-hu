---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 49610c178b577c4d81376a535355d140e8a144cb
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2020
ms.locfileid: "84145004"
---
## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
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
+ [Node. js](https://nodejs.org/), aktív LTS és karbantartási LTS-verziók (8.11.1 és 10.14.1 ajánlott).
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

+ [Apache Maven](https://maven.apache.org), 3,0-es vagy újabb verzió.

> [!IMPORTANT]
> A rövid útmutató befejezéséhez a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.
::: zone-end
