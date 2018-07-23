---
title: Azure Stack-eszközök letöltése a githubról |} A Microsoft Docs
description: Ismerje meg, hogyan töltse le az Azure Stack működéséhez szükséges eszközöket.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: mabrigg
ms.openlocfilehash: 9c1e4abe50b06db58a4ca05a99e1ae4a531b2294
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187451"
---
# <a name="download-azure-stack-tools-from-github"></a>Azure Stack-eszközök letöltése a githubról

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

1.2.9-es-eszközökkel egy GitHub-adattár, amely futtatja a PowerShell-modulokat, amelyek segítségével kezelheti és erőforrások üzembe helyezése az Azure Stack.

## <a name="download-targets"></a>Töltse le a célok

Letöltheti és használhatja ezeket az Azure Stack Development Kit PowerShell modulok, vagy egy Windows-alapú külső ügyfél használó VPN-kapcsolatot.

## <a name="how-to-get-the-tools"></a>Az eszközök beszerzése

Ezek az eszközök a AzureStack-eszközök GitHub-tárház klónozásához, vagy töltse le az AzureStack-eszközök mappát a következő szkript futtatásával:

```PowerShell
# Change directory to the root directory
cd \

# Download the tools archive
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>A modulok által biztosított funkciók

A AzureStack-Tools-tárház, amely támogatja a következő funkciók az Azure Stack PowerShell-modulokat tartalmaz:

| Funkció | Leírás | Ez a modul ki tudja használni? |
| --- | --- | --- |
| [A felhőalapú képességek](https://github.com/Azure/AzureStack-Tools/tree/master/CloudCapabilities) | Ez a modul használatával a felhő felhő képességeit. Például az Azure Stack, és ez a modul használatával Azure-felhők a felhőalapú képességek, például az API-verzió, az Azure Resource Manager-erőforrásokkal, a Virtuálisgép-bővítmények stb. is kap. | Felhőszolgáltatás-rendszergazdák és felhasználók. |
| [Az Azure Stack Resource Manager-házirend](azure-stack-policy-module.md) | Ez a modul használatával Azure-előfizetésre vagy Azure-erőforrás konfigurálása az Azure Stack, azonos verziókezelés és szolgáltatás rendelkezésre. | Felhőszolgáltatás-rendszergazdák és felhasználók |
| [Csatlakozás az Azure Stackhez](azure-stack-connect-azure-stack.md) | Ez a modul használja, egy PowerShell-lel az Azure Stack-példányhoz való csatlakozáshoz, és az Azure Stackhez VPN-kapcsolat konfigurálása. | Felhőszolgáltatás-rendszergazdák és felhasználók |
| [Sablon-érvényesítő](azure-stack-validate-templates.md) | Ez a modul segítségével ellenőrizheti, ha egy meglévő vagy új sablont is üzembe helyezhetők az Azure Stackhez. | Felhőszolgáltatás-rendszergazdák és felhasználók |

## <a name="next-steps"></a>További lépések

- [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md)
- [Csatlakozás az Azure Stack Development Kit egy VPN-kapcsolaton keresztül](azure-stack-connect-azure-stack.md)
