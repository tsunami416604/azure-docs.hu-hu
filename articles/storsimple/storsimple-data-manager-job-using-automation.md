---
title: "Azure Automation segítségével elindítható egy feladat |} Microsoft Docs"
description: "Azure Automation használata indítására, a StorSimple adatkezelő feladatok (magán előnézetben)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 9691408bcd80afb6eba534f26749b76dd3bfe315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-automation-to-trigger-a-job-private-preview"></a>Azure Automation segítségével elindítható egy feladat (magán előnézetben)

Ez a cikk ismerteti a StorSimple adatkezelő feladatot indít az Azure Automation segítségével.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

*   Az Azure Powershell telepítése. [Töltse le az Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   A Data Transformation feladat inicializálni a konfigurációs beállításokat (a beállítások átvételéhez utasítások ide tartoznak).
*   A feladat definíciójához erőforráscsoporton belül egy hibrid adatforrás, melyhez a megfelelően konfigurált.
*   Töltse le `DataTransformationApp.zip` [zip](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) a github-tárházban a fájlt.
*   Töltse le `Get-ConfigurationParams.ps1` [parancsfájl](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Get-ConfigurationParams.ps1) a github-tárházban lévő.
*   Töltse le `Trigger-DataTransformation-Job.ps1` [parancsfájl](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) a github-tárházban lévő.

## <a name="step-by-step"></a>Lépésről lépésre

### <a name="get-azure-active-directory-permissions-for-the-automation-job-to-run-the-job-definition"></a>Azure Active Directory engedélyeinek az automation-feladat futtatásához a feladat definíciójának beolvasása

1. A konfigurációs paraméterek beolvasása az Active Directory, tegye a következőket:

    1. Nyissa meg a Windows PowerShell a helyi gépen. Győződjön meg arról, hogy [Azure PowerShell](https://azure.microsoft.com/downloads/) telepítve van.
    1. Futtassa a `Get-ConfigurationParams.ps1` parancsfájl (a fent letöltött mappában). A PowerShell-ablakban írja be a következő parancsot:

        ```
        .\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```

        A ActiveDirectoryKey egy jelszót, amely későbbi használatra. Adjon meg egy tetszőleges jelszót. Alkalmazásnév karakterlánc lehet.

2. Ez a parancsfájl kimenete a következő értékeket kell használni a rendszer az automation-runbook indítására. Jegyezze fel ezeket az értékeket.

    - Ügyfél-azonosító
    - Bérlőazonosító
    - Active Directory-kulcs (ugyanaz, mint a fent megadott)
    - Előfizetés azonosítója

### <a name="set-up-the-automation-account"></a>Az Automation-fiók beállítása

1. Jelentkezzen be az Azure-ba, és nyissa meg az Automation-fiók.
2. Kattintson a **eszközök** csempére kattintva nyissa meg az eszközök listája.
3. Kattintson a **modulok** csempére kattintva nyissa meg a modulok listáját.
4. Kattintson a **+ a modul hozzá lesz adva** gombra, majd a Hozzáadás modul panel nincs elindítva.

    ![Automation-fiók beállításai](./media/storsimple-data-manager-job-using-automation/add-module1m.png)

5. Miután kiválasztotta a `DataTransformationApp.zip` fájlt a helyi számítógépről, kattintson **OK** a modul importálásához.

   Azure Automation importálja a modult, a fiókjához, ha a modul metaadatai bontja ki. Ez a művelet eltarthat néhány percig.

   ![Automation-fiók beállításai](./media/storsimple-data-manager-job-using-automation/add-module2m.png)

   

6. Ha a folyamat befejeződik, hogy a modul telepítése értesítés és egy másik értesítést kapni.  Is ellenőrizheti az állapotát **modulok** csempére.

### <a name="to-import-the-runbook-that-triggers-the-job-definition"></a>A runbook, amely elindítja a feladat definíciójának importálása

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Kattintson a **Runbookok** csempére kattintva nyissa meg a runbookok listáját.
3. Kattintson a **+ Hozzáadás runbook** , majd **meglévő forgatókönyv importálása**.

   ![Meglévő forgatókönyv importálása](./media/storsimple-data-manager-job-using-automation/import-a-runbook.png)

4. Kattintson a **Runbook fájl** válassza ki az importálandó fájl `Trigger-DataTransformation-Job.ps1`.
5. Kattintson a **létrehozása** a runbook importálásához. Az új runbook megjelenik a forgatókönyvek az Automation-fiók közül.
7. Kattintson a **eseményindító-DataTransformation-feladat** runbookot, majd **szerkesztése**.
8. Kattintson a **közzététel** , majd **Igen** során a rendszer megerősítést kér.


### <a name="to-run-the-runbook"></a>A runbook futtatása:
1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Kattintson a **Runbookok** csempére a forgatókönyvek listájának megnyitásához.
3. Kattintson a **eseményindító-DataTransformation-feladat**.
4. Kattintson az **Indítás** gombra a forgatókönyv elindításához.

   ![Forgatókönyv indítása](./media/storsimple-data-manager-job-using-automation/run-runbook1m.png)

5. Az a **indítsa el a runbook** panelen adja meg a paramétereket. Kattintson a **OK** elküldeni a Data Transformation feladatot.

   ![Forgatókönyv indítása](./media/storsimple-data-manager-job-using-automation/run-runbook2m.png)


## <a name="next-steps"></a>Következő lépések

[Használja a StorSimple adatokat kezelő felhasználói felületén, az adatok átalakítására](storsimple-data-manager-ui.md).