---
title: Azure rövid útmutató – Batch-feladat futtatása – portál
description: Rövid áttekintést kaphat arról, hogyan futtathat Batch-feladatokat az Azure Portalon.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: na
ms.topic: quickstart
ms.date: 01/19/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f6b2cc8f3e27b65f225014ec92a7e99851eac743
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Rövid útmutató: Az első Batch-feladat futtatása az Azure Portalon

Ez a rövid útmutató ismerteti, hogyan lehet létrehozni az Azure Portalon Batch-fiókot, számítási csomópontok (virtuális gépek) *készletét*, valamint egy olyan *feladatot*, amely alapszintű *tevékenységeket* futtat a készleten. A rövid útmutatóból megismerheti a Batch szolgáltatás fő fogalmait, és készen áll majd a Batch szolgáltatás használatára realisztikusabb számítási feladatokkal, nagyobb léptékben.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-batch-account"></a>Batch-fiók létrehozása

Kövesse az alábbi lépéseket egy minta Batch-fiók tesztelési céllal történő létrehozásához. Készletek és feladatok létrehozásához Batch-fiók szükséges. Ahogyan az az alábbi ábrán is látható, a Batch-fiók összekapcsolható egy Azure Storage-fiókkal. Bár a rövid útmutatóhoz nem kötelező, a Storage-fiók hasznos az alkalmazások üzembe helyezéséhez, valamint a legtöbb valós számítási feladat be- és kimeneti adatainak tárolásához.


1. Kattintson az **Új** > **Számítás** > **Batch-szolgáltatás** elemre. 

  ![Batch a Piactéren][marketplace_portal]

2. Adjon meg értéket a **Fiók neve** és az **Erőforráscsoport** mezőben. A fiók nevének egyedinek kell lennie a kiválasztott Azure-**helyen**, csak kisbetűket vagy számokat, illetve 3–24 karaktert tartalmazhat. 

3. A **Tárfiókok** területen válasszon egy meglévő tárfiókot, vagy hozzon létre egy újat.

4. Hagyja meg az alapértelmezett értékeket a többi beállításnál, és kattintson a **Létrehozás** gombra a fiók létrehozásához.

  ![Batch-fiók létrehozása][account_portal]  

Amikor megjelenik az **Üzembe helyezés sikeres** üzenet, lépjen a Batch-fiókba a portálon.

## <a name="create-a-pool-of-compute-nodes"></a>Számításicsomópont-készlet létrehozása

Most, hogy már rendelkezik Batch-fiókkal, hozzon létre egy windowsos számítási csomópontokból álló mintakészletet tesztelési célra. A jelen rövid példában a készlet 2 csomópontból áll, amelyek Windows Server 2012 R2-rendszerképet futtatnak az Azure Marketplace-ről.


1. A Batch-fiókban kattintson a **Készletek** > **Hozzáadás** lehetőségre.

2. Adja meg a következő **készletazonosítót**: *mypool*. 

3. Az **Operációs rendszer** területen válassza ki az alábbi beállításokat (más beállításokat is kipróbálhat).
  
  |Beállítás  |Érték  |
  |---------|---------|
  |**Rendszerkép típusa**|Marketplace (Linux/Windows)|
  |**Közzétevő**     |MicrosoftWindowsServer|
  |**Ajánlat**     |WindowsServer|
  |**Termékváltozat**     |2012-R2-Datacenter-smalldisk|

  ![Készlet operációs rendszerének kiválasztása][pool_os] 

4. Görgessen le a **Csomópontméret** és a **Méretezés** beállítás megadásához. A javasolt csomópontméret jó teljesítmény/költség arányt kínál a jelen rövid példában.
  
  |Beállítás  |Érték  |
  |---------|---------|
  |**Csomópont tarifacsomagja**     |Standard_A1|
  |**Dedikált célcsomópontok**     |2|

  ![Készletméret kiválasztása][pool_size] 

5. Hagyja meg az alapértelmezett értéket a többi beállításnál, és kattintson az **OK** gombra a készlet létrehozásához.

A Batch azonnal létrehozza a készletet, de a számítási csomópontok lefoglalása és elindítása igénybe vesz néhány percet. Eközben a készlet **Lefoglalási állapota** **Átméretezés**. A készlet átméretezése közben létrehozhat egy feladatot és tevékenységeket. 

![A készlet átméretezés állapotban van][pool_resizing]

Néhány perc elteltével a készlet állapota **Stabil** értékre változik, és elindulnak a csomópontok. Kattintson a **Csomópontok** lehetőségre a csomópontok állapotának megtekintéséhez. Ha egy csomópont állapota **Tétlen**, készen áll a tevékenységek futtatására. 

## <a name="create-a-job"></a>Feladat létrehozása

Most, hogy már rendelkezik készlettel, hozzon létre egy feladatot, amelyet azon futtat. A Batch-feladat egy vagy több tevékenység logikai csoportja. Egy Batch-feladat magában foglalja a tevékenységek közös beállításait, mint a prioritást, illetve a készletet, amelyeken a tevékenységeket futtatni szeretné. A feladat kezdetben nem tartalmaz tevékenységeket. 

1. A Batch-fiók nézetben kattintson a **Feladatok** > **Hozzáadás** lehetőségre. 

2. Adja meg a következő **feladatazonosítót**: *myjob*. A **Készlet** mezőben válassza a *mypool* lehetőséget. Hagyja meg az alapértelmezett értékeket a többi beállításnál, és kattintson az **OK** gombra.

  ![Feladat létrehozása][job_create]

A feladat létrehozása után megnyílik a **Tevékenységek** lap.

## <a name="create-tasks"></a>Tevékenységek létrehozása

Most hozzon létre a feladatban futtatni kívánt mintatevékenységeket. Általában több tevékenységet hozunk létre, amelyeket a Batch sorba állít és szétoszt a számítási csomópontokra. Ebben a példában két azonos tevékenységet hoz létre. Minden tevékenység egy parancssort futtat, és megjeleníti a Batch környezeti változóit a számítási csomóponton, majd vár 90 másodpercet. 

Batch használata esetén a parancssorban adhatja meg az alkalmazást vagy szkriptet. A Batch számos módszert kínál az alkalmazások és szkriptek számítási csomópontokon történő üzembe helyezésére. 

Az első tevékenység létrehozása:

1. Kattintson a **Hozzáadás** parancsra.

2. Adja meg a következő **tevékenységazonosítót**: *mytask*. 

3. A **parancssorban** adja meg a következő parancsot: `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Hagyja meg az alapértelmezett értékeket a többi beállításnál, és kattintson az **OK** gombra.

  ![Tevékenység létrehozása][task_create]

Tevékenység létrehozása után a Batch várólistára helyezi azt a készleten való futtatáshoz. Amint egy csomópont készen áll a futtatásra, a rendszer futtatja a tevékenységet.

Egy második tevékenység létrehozásához menjen vissza az 1. lépésre. Adjon meg egy másik **tevékenység-azonosítót**, de a parancssor legyen ugyanaz. Ha az első tevékenység még fut, a Batch a készlet egy másik csomópontján indítja el a második tevékenységet.

## <a name="view-task-output"></a>A tevékenység kimenetének megtekintése

A fenti példatevékenységek néhány percen belül befejeződnek. A befejezett tevékenységek kimenetének megtekintéséhez kattintson a **Fájlok a csomóponton** lehetőségre, és válassza az `stdout.txt` fájlt. A fájl a tevékenység szabványos kimenetét jeleníti meg. A tartalma a következőhöz hasonló lesz:

![A tevékenység kimenetének megtekintése][task_output]

A tartalom a csomóponton beállított Azure Batch környezeti változókat jeleníti meg. Saját Batch-feladatok és -tevékenységek létrehozása során hivatkozhat ezekre a környezeti változókra a tevékenységek parancssorában, illetve a parancssorok által futtatott alkalmazásokban és szkriptekben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatni szeretné a Batch-oktatóanyagok és -minták használatát, használja az ebben a rövid útmutatóban létrehozott Batch-fiókot és az ahhoz kapcsolt Storage-fiókot. A Batch-fiók használata díjmentes.

A készletért díjat számítunk fel, amíg a csomópontok futnak, még akkor is, ha nincsenek feladatok ütemezve. Ha már nincs szüksége a készletre, törölje azt. A fióknézetben kattintson a **Készletek** lehetőségre, majd a készlet nevére. Ezután kattintson a **Törlés** gombra.  A készlet törlésekor a rendszer a csomópont összes tevékenységének kimenetét is törli. 

Törölje az erőforráscsoportot, a Batch-fiókot és az összes kapcsolódó erőforrást, ha már nincs rá szüksége. Ehhez válassza ki a Batch-fiókhoz tartozó erőforráscsoportot, és kattintson az **Erőforráscsoport törlése** lehetőségre.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Batch-fiókot, egy Batch-készletet és egy Batch-feladatot hozott létre. A feladat mintatevékenységeket futtatott, Ön pedig megtekintette az egyik csomóponton létrejött kimenetet. Most, hogy megismerkedett a Batch szolgáltatás fő fogalmaival, készen áll a Batch szolgáltatás realisztikusabb számítási feladatokkal, nagyobb léptékben történő kipróbálására. Az Azure Batchről további információt az Azure Batch-oktatóanyagokban találhat. 

> [!div class="nextstepaction"]
> [Azure Batch-oktatóanyagok](./tutorial-parallel-dotnet.md)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png