---
title: "Töltse le az Azure-verem eszközök a Githubról |} Microsoft Docs"
description: "Útmutató az Azure veremnek megfelelő működéséhez szükséges eszközök."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: 69ca030d1b7601df424fa9446e1d194a3f6bd50a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="download-azure-stack-tools-from-github"></a>Töltse le az Azure-verem eszközök a Githubról

AzureStack-eszközök kezelésének és központi telepítésének erőforrások Azure verem használható PowerShell-modulok üzemeltető GitHub-tárházban. Töltse le, és a PowerShell-modulok az Azure verem szoftverfejlesztői készlet, vagy a windows-alapú külső ügyfél használja, ha azt tervezi, hogy a VPN-kapcsolatot létrehozni. Ezek az eszközök klónozza a GitHub-tárházban, vagy töltse le a AzureStack-eszközök mappában. 

Klónozza a tárházat, le kell töltenie [Git](https://git-scm.com/download/win) Windows, nyisson meg egy parancssori ablakot, és futtassa a következő parancsfájlt:

```PowerShell
# Change directory to the root directory 
cd \

# clone the repository
git clone https://github.com/Azure/AzureStack-Tools.git --recursive

# Change to the tools directory
cd AzureStack-Tools
```

Az eszközök mappa letöltéséhez futtassa a következő parancsfájlt:

```PowerShell
# Change directory to the root directory 
cd \

# Download the tools archive
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

## <a name="functionalities-provided-by-the-modules"></a>A modulok által nyújtott funkciók

A AzureStack-eszközök tárház, amely támogatja a következő funkciók verem Azure PowerShell-modulok tartalmazza:  

| Funkció | Leírás | Ez a modul ki tudja használni? |
| --- | --- | --- |
| [Felhőalapú képességek](azure-stack-validate-templates.md) | Ez a modul segítségével felhő felhőalapú szolgáltatásokkal. A felhőalapú képességek, például az API-verzió, az Azure Resource Managerhez tartozó erőforrások, a Virtuálisgép-bővítmények stb. például Azure verem, és ez a modul Azure felhők kaphat. | Felhő rendszergazdák és felhasználók. |
| [Erőforrás-kezelő házirend Azure verem](azure-stack-policy-module.md) | Ez a modul segítségével konfigurálása Azure-előfizetés vagy egy Azure-erőforráscsoportot a azonos versioning és a szolgáltatás rendelkezésre állás mint Azure verem. | Felhő rendszergazdák és felhasználók |
| [Kapcsolódás Azure verem](azure-stack-connect-azure-stack.md) | Ez a modul használja, egy Azure veremben példányra Powershellen keresztül csatlakozni, és Azure verem VPN-kapcsolat konfigurálása. | Felhő rendszergazdák és felhasználók |
| [Sablon érvényesítő](azure-stack-validate-templates.md) | Ez a modul segítségével ellenőrizheti, ha egy meglévő vagy új sablon Azure verem számára telepíthető. | Felhő rendszergazdák és felhasználók |


## <a name="next-steps"></a>Következő lépések
* [Az Azure-verem felhasználói PowerShell környezet konfigurálása](azure-stack-powershell-configure-user.md)   
* [Csatlakozás Azure verem szoftverfejlesztői készlet egy VPN-kapcsolaton keresztül](azure-stack-connect-azure-stack.md)  
