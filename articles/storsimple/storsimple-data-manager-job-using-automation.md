---
title: "Azure Automation segítségével indítsa el a feladat a StorSimple adatkezelő |} Microsoft Docs"
description: "Azure Automation indítására, a StorSimple adatkezelő feladatok használata"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 1e5fcbee664271058ac1c7fa80bb285e09b8579a
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Azure Automation segítségével elindítható egy feladat

Ez a cikk azt ismerteti, hogyan használhatja a StorSimple adatkezelő szolgáltatáson belül az adatok átalakítása szolgáltatás StorSimple eszközön tárolt adatok átalakítását. Egy átalakítási feladatot két módon indíthatja el: 

 - A .NET SDK használata
 - Azure Automation-forgatókönyv használata
 
Ez a cikk részletesen egy Azure Automation-runbook létrehozása, és majd a használatával egy átalakítási feladatot kezdeményez. Adatok átalakítása .NET SDK használatával indítása kapcsolatos további tudnivalókért keresse fel [használata .NET SDK eseményindító adatok átalakítási feladat](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

*   Az Azure PowerShell telepítve legyen az ügyfélszámítógépen. [Töltse le az Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).
*   A StorSimple adatkezelő szolgáltatás erőforráscsoporton belül megfelelően konfigurált feladatdefiníció.
*   Töltse le [ `DataTransformationApp.zip` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) a GitHub-tárházban a fájlt. 
*   Töltse le [ `Trigger-DataTransformation-Job.ps1` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) a GitHub-tárházban parancsfájlt.

## <a name="step-by-step-procedure"></a>Lépésről lépésre

### <a name="set-up-the-automation-account"></a>Az Automation-fiók beállítása

1. Egy Azure-beli futtató automation-fiók létrehozása az Azure portálon. Ehhez nyissa meg a **Azure piactér > minden** majd keresse meg a **Automation**. Válassza ki **Automation-fiók**.

    ![Futtató automation-fiók létrehozása](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Új automation-fiók hozzáadásához kattintson **+ Hozzáadás**.

    ![Futtató automation-fiók létrehozása](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. Az a **Automation hozzáadása**:

    1. Adja meg a **neve** az automation-fiók.
    2. Válassza ki a **előfizetés** a StorSimple adatkezelő szolgáltatás csatolva.
    3. Hozzon létre egy új erőforráscsoportot, vagy válasszon egy meglévő erőforráscsoportot a.
    4. Válasszon ki egy **helyet**.
    5. Hagyja meg az alapértelmezett **létrehozása Futtatás mint fiók** kiválasztott beállítás.
    6. Szerezzen be egy hivatkozást a gyors elérés érdekében az irányítópulton, ellenőrizze a következőket **rögzítés az irányítópulton**. Kattintson a **Create** (Létrehozás) gombra.

    ![Futtató automation-fiók létrehozása](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
    Miután az automation-fiók sikeresen létrejött, értesítés jelenik meg.
    
    ![Értesítés az automation-fiókhoz történő telepítéséhez](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

    További információkért látogasson el [hozzon létre egy futtató fiókot](../automation/automation-create-runas-account.md).

3. Az újonnan létrehozott fiókjához, keresse meg **megosztott erőforrások > modulok** kattintson **+ Hozzáadás modul**.

    ![1 modul importálása](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Keresse meg a helyet a `DataTransformationApp.zip` fájlt a helyi számítógépről, és válassza ki és nyissa meg a modult. Kattintson a **OK** a modul importálásához.

    ![2 modul importálása](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Azure Automation importálja a modult, a fiókjához, ha a modul metaadatai bontja ki. Ez a művelet eltarthat néhány percig.

   ![4 modul importálása](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Ha a folyamat befejeződik, hogy a modul telepítése értesítés és egy másik értesítést kapni.  Az állapot **modulok** vált **elérhető**.

    ![5 modul importálása](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importálja, közzétételét és Automation-forgatókönyv futtatása

A következő lépésekkel importálhatja, közzététele, és feladatdefiníció elindítása a runbook futtatásához.

1. Az Azure Portalon nyissa meg az Automation-fiókját. Ugrás **folyamat > Runbookok** kattintson **+ Hozzáadás egy runbook**.

    ![Vegye fel a runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. A **vegye fel a runbook**, kattintson a **meglévő forgatókönyv importálása**.

3. Az Azure PowerShell parancsfájl mutasson `Trigger-DataTransformation-Job.ps1` a a **Runbook fájl**. A runbook típusa automatikusan ki van jelölve. Adjon meg egy nevet és egy leírást a runbook. Kattintson a **Create** (Létrehozás) gombra.

    ![Adja hozzá a 2. forgatókönyv](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Az új runbook megjelenik a forgatókönyvek az Automation-fiók közül. Jelölje ki, majd kattintson a runbookot.

    ![Adja hozzá a 3. forgatókönyv](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. A runbook szerkesztése, és kattintson a **teszt** ablaktáblán.

    ![4. runbook hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Adja meg a paraméterek, például a nevét a StorSimple adatokat kezelő szolgáltatás, a társított alkalmazásátjáróhoz csoport és a feladat-definíció nevét. **Start** a vizsgálatot. A jelentés jön létre, amikor a Futtatás befejeződött. További információkért lépjen be, hogyan [egy runbook tesztelése](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Vegye fel a runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Vizsgálja meg a ablaktáblán a runbook kimenete. Ha mindent megfelelőnek talált, zárja be a panelen. Kattintson a **közzététel** és amikor felszólítja a megerősítésre, erősítse meg, közzé a runbookot.

    ![Runbook 6 hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Lépjen vissza a **Runbookok** , és válassza ki az újonnan létrehozott runbookot.

    ![Vegye fel a runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Start** a runbookot. A **indítsa el a runbook**, adja meg a paramétereket. Kattintson a **OK** küldje el, és az átalakítási feladat elindítása.

10. Azure-portálon a feladatok előrehaladásának figyeléséhez, keresse fel **feladatok** a StorSimple Data Manager szolgáltatásban. Jelölje ki, majd kattintson a feladat a feladat részleteinek megtekintéséhez.

    ![Runbook 10 hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>További lépések

[Használja a StorSimple adatokat kezelő felhasználói felületén, az adatok átalakítására](storsimple-data-manager-ui.md).