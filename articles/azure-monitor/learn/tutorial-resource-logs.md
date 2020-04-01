---
title: Erőforrásnaplók gyűjtése azure-erőforrásból és elemzés az Azure Monitorsegítségével
description: Oktatóanyag konfigurálása diagnosztikai beállításokat, hogy összegyűjtse az erőforrás-naplók egy Azure-erőforrás egy Log Analytics-munkaterület, ahol elemezhetők egy naplólekérdezés.
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: d356042d65c419163de4951e64a635a22ea90e6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78269196"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Oktatóanyag: Erőforrásnaplók gyűjtése és elemzése egy Azure-erőforrásból

Az erőforrásnaplók betekintést nyújtanak egy Azure-erőforrás részletes működésébe, és hasznosak állapotuk és elérhetőségük figyeléséhez. Az Azure-erőforrások automatikusan erőforrásnaplókat hoznak létre, de konfigurálnia kell, hogy hol kell gyűjteni őket. Ez az oktatóanyag végigvezeti a diagnosztikai beállítás létrehozásának folyamatán, amely során az Azure-előfizetésében lévő erőforrásnaplókat összegyűjtheti, és naplólekérdezéssel elemezheti azt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Log Analytics-munkaterület létrehozása az Azure Monitorban
> * Diagnosztikai beállítás létrehozása az erőforrásnaplók gyűjtéséhez 
> * Egyszerű naplólekérdezés létrehozása a naplók elemzéséhez


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez egy Azure-erőforrást kell figyelnie. Az Azure-előfizetésben bármilyen erőforrást használhat, amely támogatja a diagnosztikai beállításokat. Annak megállapításához, hogy egy erőforrás támogatja-e a diagnosztikai beállításokat, nyissa meg az Azure Portal menüjét, és ellenőrizze, hogy van-e **diagnosztikai beállítások** lehetőség a menü **Figyelés** szakaszában.


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a rendszerbe.


## <a name="create-a-workspace"></a>Munkaterület létrehozása
Az Azure Monitor log analytics-munkaterülete különböző forrásokból gyűjti és indexeli a naplóadatokat, és lehetővé teszi a speciális elemzést egy hatékony lekérdezési nyelv használatával. A Log Analytics munkaterület et kell léteznie, mielőtt létrehoz egy diagnosztikai beállítást adatokat küldeni. Használhat egy meglévő munkaterületet az Azure-előfizetésében, vagy létrehozhat egyet az alábbi eljárással. 

> [!NOTE]
> Miközben az **Azure Monitor** menü Log Analytics-munkaterületeiben dolgozhat az adatokkal, a **Log Analytics-munkaterületek** menüjében hozhat létre és kezelhet munkaterületeket.

1. A **Minden szolgáltatás ban**válassza a Log **Analytics-munkaterületek**lehetőséget.
2. Kattintson a képernyő tetején a **Hozzáadás** gombra, és adja meg a munkaterület alábbi részleteit:
   - **Log Analytics-munkaterület:** Az új munkaterület neve. Ennek a névnek globálisan egyedinek kell lennie az Összes Azure Monitor-előfizetésben.
   - **Előfizetés**: Válassza ki a munkaterületet tároló előfizetést. Ennek nem kell ugyanazt az előfizetést, mint a figyelt erőforrás.
   - **Erőforráscsoport**: Jelöljön ki egy meglévő erőforráscsoportot, vagy kattintson az **Új létrehozása** gombra új létrehozása érdekében. Ennek nem kell ugyanazt az erőforráscsoportot, mint a figyelt erőforrás.
   - **Hely**: Válasszon ki egy Azure-régiót, vagy hozzon létre egy újat. Ennek nem kell megegyeznie a figyelt erőforrással.
   - **Tarifacsomag:** Válassza ki *a felosztó-kiosztó* díjat a tarifacsomagként. Ezt a tarifacsomagot később módosíthatja. Kattintson a **Log Analytics díjszabási** hivatkozásra, ha többet szeretne megtudni a különböző tarifacsomagokról.

    ![Új munkaterület](media/tutorial-resource-logs/new-workspace.png)

3. A munkaterület létrehozásához kattintson az **OK** gombra.

## <a name="create-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása
[A diagnosztikai beállítások](../platform/diagnostic-settings.md) határozzák meg, hogy egy adott erőforráshoz hol kell erőforrásnaplókat küldeni. Egyetlen diagnosztikai beállítás több [célnak](../platform/diagnostic-settings.md#destinations)is lehet, de ebben az oktatóanyagban csak egy Log Analytics-munkaterületet használunk.

1. Az erőforrás menüjének **Figyelés** szakaszában válassza a **Diagnosztikai beállítások lehetőséget.**
2. A következő üzenettel kell rendelkeznie: "Nincs diagnosztikai beállítás definiálva". Kattintson **a Diagnosztikai beállítás hozzáadása gombra.**

    ![Diagnosztikai beállítások](media/tutorial-resource-logs/diagnostic-settings.png)

3. Minden diagnosztikai beállítás három alapvető részből áll:
 
   - **Név**: Ennek nincs jelentős hatása, és egyszerűen leíró jellegű az Ön számára.
   - **Úti célok**: Egy vagy több úti cél a naplók elküldéséhez. Minden Azure-szolgáltatás ugyanazt a három lehetséges célhelyet osztja meg. Minden diagnosztikai beállítás egy vagy több célt definiálhat, de egy adott típushoz egynél több célt. 
   - **Kategóriák**: Az egyes célállomásokra küldő naplók kategóriái. A kategóriák készlete az egyes Azure-szolgáltatásoktól függően eltérő lehet.

4. Válassza **a Küldés a Log Analytics-munkaterületnek lehetőséget,** majd válassza ki a létrehozott munkaterületet.
5. Jelölje ki az összegyűjteni kívánt kategóriákat. Az elérhető kategóriák meghatározásához tekintse meg az egyes szolgáltatások dokumentációját.

    ![Diagnosztikai beállítás](media/tutorial-resource-logs/diagnostic-setting.png)

6. A diagnosztikai beállítások mentéséhez kattintson a **Mentés** gombra.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Naplók lekérése naplólekérdezéssel
Az adatok beolvasása a Log Analytics-munkaterületről a Kusto Query Language (KQL) nyelven írt naplólekérdezés használatával történik. Az Azure Monitor insights és megoldások naplólekérdezéseket biztosítanak egy adott szolgáltatás adatainak lekéréséhez, de közvetlenül dolgozhat a naplólekérdezésekkel és azok eredményeivel az Azure Portalon a Log Analytics segítségével. 

1. Az erőforrás menüjének **Figyelés** szakaszában válassza a **Naplók**lehetőséget.
2. A Log Analytics egy üres lekérdezési ablakkal nyílik meg, amelynek hatóköre az erőforrásra van beállítva. A lekérdezések csak az adott erőforrás rekordjait tartalmazzák.

    > [!NOTE]
    > Ha megnyitotta a Naplók az Azure Monitor menüből, a hatókör a Log Analytics munkaterületre lesz beállítva. Ebben az esetben a lekérdezések a munkaterület összes rekordját tartalmazzák.
   
    ![Naplók](media/tutorial-resource-logs/logs.png)

4. A példában látható szolgáltatás erőforrásnaplókat ír az **AzureDiagnostics** táblába, de más szolgáltatások más táblákba is írhatnak. Lásd: [Támogatott szolgáltatások, sémák és kategóriák az Azure Resource Logs](../platform/diagnostic-logs-schema.md) a különböző Azure-szolgáltatások által használt táblák.

    > [!NOTE]
    > Több szolgáltatás erőforrásnaplókat ír az AzureDiagnostics táblába. Ha elindítja a Log Analytics az Azure Monitor menüből, majd hozzá kell adnia egy `where` nyilatkozatot az oszlopot az `ResourceProvider` adott szolgáltatás megadásához. Amikor elindítja a Log Analytics egy erőforrás menüjéből, majd a hatókör van beállítva, hogy csak rekordok ebből az erőforrásból, így ez az oszlop nem szükséges. Tekintse meg a szolgáltatás dokumentációját a minta lekérdezések.


5. Írjon be egy lekérdezést, és az eredmények vizsgálatához kattintson a **Futtatás** gombra. 
6. Az [Azure Monitor naplólekérdezéseinek első lépései](../log-query/get-started-queries.md) című témakörben a naplólekérdezések írásával kapcsolatos oktatóanyagról.

    ![Lekérdezés naplózása](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>További lépések
Most, hogy megtanulta, hogyan gyűjthet erőforrásnaplókat a Log Analytics-munkaterületre, töltsön ki egy oktatóanyagot a naplólekérdezések írásáról az adatok elemzéséhez.

> [!div class="nextstepaction"]
> [A naplólekérdezések első lépései az Azure Monitorban](../log-query/get-started-queries.md)
