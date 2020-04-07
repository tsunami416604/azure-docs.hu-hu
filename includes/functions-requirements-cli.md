---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a84f0a92703d1b626710cfc4dcfa2820bc58bda6
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673205"
---
## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt elkezdené, rendelkeznie kell a következőkkel:

+ Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ Az [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846-os vagy egy későbbi 2.x-es verziója.
::: zone-end  
::: zone pivot="programming-language-python"
+ A Python 3.6-os és 3.7-es verziójához az [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846-os vagy egy későbbi 2.x-es verziója szükséges. A Python 3.8 használatához a Core Tools [3.x verziója](../articles/azure-functions/functions-run-local.md#v2) szükséges.
::: zone-end

+ Az [Azure CLI](/cli/azure/install-azure-cli) 2.0.76-os vagy újabb verziója. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Aktív LTS és karbantartási LTS verziók (8.11.1 és 10.14.1 ajánlott).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64 bites)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 bites)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64 bites)](https://www.python.org/downloads/release/python-368/), amelyeket az Azure Functions támogat. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ A [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ A [Java Developer Kit,](https://aka.ms/azure-jdks)8-as verzió.

+ [Apache Maven](https://maven.apache.org), 3.0-s vagy újabb verzió.

> [!IMPORTANT]
> A rövid útmutató befejezéséhez a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.
::: zone-end