---
title: Verziókövetés integrálása az Azure Automationben
description: Ez a cikk ismerteti a forráskezelés integrálása a GitHub az Azure Automationben.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c2d13a409d095bca64da781e5c5ca58553f9710c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047587"
---
# <a name="source-control-integration-in-azure-automation"></a>Verziókövetés integrálása az Azure Automation szolgáltatásban

Verziókövetés lehetővé teszi a runbookok az Automation-fiók vannak a parancsfájlok a GitHub vagy az Azure-fejlesztéshez verziókövetési tárházat naprakészen tartani. Verziókövetés együttműködhet a csapatával, követi a változásokat, és állítsa vissza a runbookok korábbi verzióira teszi lehetővé. Például verziókövetés lehetővé teszi, hogy a verziókövetés, a fejlesztési, tesztelési vagy éles Automation-fiókok, így könnyen előléptetni a kódot, amely a fejlesztési környezetben, az éles üzemben futó Automation tesztelve lett különböző ágak szinkronizálása fiók.

Az Azure Automation verziókövetés 3 típust támogat:

* GitHub
* A Visual Studio Team Services (Git)
* A Visual Studio Team Services (TFVC)

## <a name="pre-requisites"></a>Előfeltételek

* Verziókövetési adattár (a GitHub vagy a Visual Studio Team Services)
* A [Run-As fiók és -kapcsolat](manage-runas-account.md)

> [!NOTE]
> Verziókövetési szinkronizálási feladatok a felhasználók Automation-fiók alatt fusson, és ugyanez a díjszabás szerint más automatizálási feladatok számlázzuk.

## <a name="configure-source-control"></a>Verziókövetés beállítása

Válassza ki az Automation-fiókban lévő **Source-Control (előzetes verzió)** kattintson **+ Hozzáadás**

![Válassza ki a verziókövetés](./media/source-control-integration/select-source-control.png)

Válasszon **verziókövetési típus** , kattintson a **hitelesítés**.

A kérelem Alkalmazásengedélyek lapon tekintse át, és kattintson a **elfogadás**.

Az a **forrás vezérlő összefoglalás** lapon adja meg az adatokat, és kattintson **mentése**. Az alábbi táblázat a mezők leírását.

|Tulajdonság  |Leírás  |
|---------|---------|
|Forrás vezérlő neve     | Egy rövid nevet a verziókövetés        |
|Verziókövetési típusa     | A forrás-vezérlő típusa. Az elérhető lehetőségek:</br> GitHub</br>A Visual Studio Team Services (Git)</br>A Visual Studio Team Services (TFVC)        |
|Tárház     | A tárház vagy projekt nevét. Ez a vezérlő adattárból kéri le. Például: $/ ContosoFinanceTFVCExample         |
|Ág     | Az ág forrásfájlok lekérni. Ág célzó nem áll rendelkezésre a TFVC verziókövetési típusa számára.          |
|Mappa elérési útja     | A runbookok szinkronizálása tartalmazó mappát. Példa: /Runbooks         |
|Automatikus szinkronizálása     | Engedélyezi vagy letiltja az automatikus szinkronizálás, amikor a véglegesítés történik a verziókövetési tárházat a         |
|Runbook közzététele     | Ha beállítása **a**, miután a forráskezelőből azokat automatikusan közzéteszi a runbookok szinkronizálva lesznek.         |
|Leírás     | További információkat egy szövegmező        |

![Forrás vezérlő összegzése](./media/source-control-integration/source-control-summary.png)

## <a name="syncing"></a>Szinkronizálás…

Automatikus szinkronizálás konfigurálása a verziókövetés integrálása során adta meg, a kezdeti szinkronizálás automatikusan lép érvénybe. Ha automatikus szinkronizálása nem lett beállítva, válassza ki a forrás a tábla a a **verziókövetés (előzetes verzió)** lapot. Kattintson a **szinkronizálás indítása** a szinkronizálási folyamat elindításához.  

A jelenlegi szinkronizálási feladat vagy a korábbiakat kattintva állapotát is megtekintheti a **feladatok szinkronizálása** fülre. Az a **verziókövetés** legördülő menüben válassza ki a verziókövetési.

![Szinkronizálás állapota](./media/source-control-integration/sync-status.png)

Egy olyan feladatra kattintva lehetővé teszi a feladat kimenetének megtekintéséhez. Az alábbiakban látható egy példa a kimenetre a forrás vezérlő szinkronizálási feladat.

```output
========================================================================================================

Azure Automation Source Control Public Preview.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

## <a name="disconnecting-source-control"></a>Verziókezelő leválasztása

Verziókövetési adattár bontsa a kapcsolatot, nyissa meg a **verziókövetés (előzetes verzió)** alatt **fiókbeállításokat** az Automation-fiókban.

Válassza ki az eltávolítani kívánt verziókövetés. Az a **forrás vezérlő összefoglalás** kattintson **törlése**.

## <a name="next-steps"></a>További lépések

További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
