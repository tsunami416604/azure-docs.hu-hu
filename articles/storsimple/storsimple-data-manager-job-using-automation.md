---
title: Azure Automation használatával indítsa el a feladatot a StorSimple Data Manager |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Automation indítására, a StorSimple Data Manager-feladatok
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b837aab871827c468295a365727a282f6c8a1a4b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58086020"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Az Azure Automationnel elindítható egy feladat

Ez a cikk bemutatja, hogyan használhatja a StorSimple Data Manager szolgáltatásban az adatok átalakítása funkció a StorSimple eszköz adatok átalakításához. Egy Adatátalakítási feladatot kétféle módon indíthatja el: 

 - A .NET SDK használata
 - Használja az Azure Automation-runbook
 
Ez a cikk részletesen bemutatja egy Azure Automation-runbook létrehozása, majd azt egy Adatátalakítási feladatot kezdeményezéséhez. Adatátalakítás a .NET SDK-n keresztül indítása kapcsolatos további információkért lépjen [használata .NET SDK-t eseményindító Adatátalakítási feladatok](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

*   Az Azure PowerShell telepítve van az ügyfélszámítógépen. [Az Azure PowerShell letöltése](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   Egy megfelelően konfigurált feladat definíciója egy erőforráscsoportban egy StorSimple Data Manager szolgáltatásban.
*   Töltse le [ `DataTransformationApp.zip` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) fájlt a GitHub-adattárból. 
*   Töltse le [ `Trigger-DataTransformation-Job.ps1` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) parancsfájlt a GitHub-adattárból.

## <a name="step-by-step-procedure"></a>Lépésről lépésre

### <a name="set-up-the-automation-account"></a>Az Automation-fiók beállítása

1. Azure-beli futtató automation-fiók létrehozása az Azure Portalon. Ehhez nyissa meg **az Azure marketplace > minden** és keressen **Automation**. Válassza ki **Automation-fiókok**.

    ![Futtató automation-fiók létrehozása](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Adjon hozzá egy új automation-fiókot, kattintson a **+ Hozzáadás**.

    ![Futtató automation-fiók létrehozása](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. Az a **Automation hozzáadása**:

   1. Adja meg a **neve** az automation-fiók.
   2. Válassza ki a **előfizetés** a StorSimple Data Manager szolgáltatáshoz csatolt.
   3. Hozzon létre egy új erőforráscsoportot, vagy jelöljön ki egy meglévő erőforráscsoportot.
   4. Válasszon ki egy **helyet**.
   5. Hagyja meg az alapértelmezett **futtató fiók létrehozása** lehetőség van kijelölve.
   6. Szerezzen be egy hivatkozást az irányítópulton a gyors hozzáférés céljából, ellenőrizze a következőket **rögzítés az irányítópulton**. Kattintson a **Create** (Létrehozás) gombra.

      ![Futtató automation-fiók létrehozása](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Miután az automation-fiók sikeresen létrejött, értesítést kap.
    
      ![Automation-fiók üzembe helyezéshez értesítés](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      További információért ugorjon [hozzon létre egy futtató fiókot](../automation/automation-create-runas-account.md).

3. Az újonnan létrehozott fiók, lépjen a **megosztott erőforrások > modulok** kattintson **+ Hozzáadás modul**.

    ![1 modul importálása](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Keresse meg a helyet, `DataTransformationApp.zip` fájlt a helyi számítógépen, és válassza ki, és nyissa meg a modulban. Kattintson a **OK** a modul importálásához.

    ![2 modul importálása](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Azure Automation-fiókjába importálja a modult, kinyeri a metaadatokat, a modul. Ez a művelet eltarthat néhány percig.

   ![4 modul importálása](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Ha a folyamat befejeződött, hogy a modul parancsfájlműveletekkel értesítést és a egy újabb értesítés kap.  Az állapot **modulok** vált **elérhető**.

    ![5 modul importálása](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importálja, közzétételét és Automation-runbook futtatása

A következő lépésekkel importálhatja, közzététel, és futtatja a runbookot elindítani a feladatdefiníció.

1. Az Azure Portalon nyissa meg az Automation-fiókját. Lépjen a **Folyamatautomatizálás > Runbookok** kattintson **+ forgatókönyv hozzáadása**.

    ![1. forgatókönyv hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. A **adja hozzá a runbook**, kattintson a **meglévő runbook importálása**.

3. Mutasson az Azure PowerShell-parancsfájlt a `Trigger-DataTransformation-Job.ps1` számára a **forgatókönyvfájl**. A runbook-típus automatikusan ki van jelölve. Adjon meg egy nevet és egy leírást a runbook. Kattintson a **Create** (Létrehozás) gombra.

    ![2. forgatókönyv hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Az új runbook megjelenik az Automation-fiók runbookok listája. Válassza ki, majd kattintson a runbook.

    ![3. forgatókönyv hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Szerkessze a runbookot, és kattintson a **teszt** ablaktáblán.

    ![4. runbook hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Adja meg a paraméterek, például nevét, a StorSimple Data Manager szolgáltatás, a társított erőforráscsoportot és a feladatdefiníció neve. **Indítsa el** a vizsgálatot. A jelentés jön létre, ha a Futtatás befejeződött. További információkért lépjen be, hogyan [egy runbook tesztelése](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Runbook 8 hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Vizsgálja meg a runbook a teszt panel kimenete. Ha elégedett az eredménnyel, zárja be a panelt. Kattintson a **közzététel** és amikor a rendszer megerősítést kér, erősítse meg, és tegye közzé a forgatókönyvet.

    ![Adja hozzá a runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Lépjen vissza a **Runbookok** , és válassza ki az újonnan létrehozott runbookot.

    ![Adja hozzá a runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Indítsa el** a runbookot. A **runbook indítása**, adja meg az összes paramétert. Kattintson a **OK** nyújt, és az átalakítási feladat elindításához.

10. Az Azure Portalon a feladatok előrehaladásának figyeléséhez, lépjen a **feladatok** a StorSimple Data Manager szolgáltatásban. Válassza ki, majd kattintson a feladat a feladat részleteinek megtekintéséhez.

    ![10-es forgatókönyv hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>További lépések

[Használja a StorSimple Data Manager felhasználói Felületét, hogy az adatok átalakítása](storsimple-data-manager-ui.md).