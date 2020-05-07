---
title: Azure Event Hubs-névtér áthelyezése másik régióba | Microsoft Docs
description: Ez a cikk bemutatja, hogyan helyezhet át egy Azure Event Hubs-névteret az aktuális régióból egy másik régióba.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 5b96bf1c538b3c5589a1993a0353292fadd0936d
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690490"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Azure Event Hubs-névtér áthelyezése másik régióba
Különböző helyzetekben érdemes áthelyezni a meglévő Event Hubs névteret az egyik régióból a másikba. Előfordulhat például, hogy létre szeretne hozni egy névteret ugyanazzal a konfigurációval a teszteléshez. A vész- [helyreállítási tervezés](event-hubs-geo-dr.md#setup-and-failover-flow)részeként másodlagos névteret is létre kell hoznia egy másik régióban.

> [!NOTE]
> Ez a cikk bemutatja, hogyan exportálhat egy Azure Resource Manager sablont egy meglévő Event Hubs névtérbe, majd a sablonnal létrehozhat egy másik régióban azonos konfigurációs beállításokkal rendelkező névteret. Azonban ez a folyamat nem helyezi át a még nem feldolgozott eseményeket. A törlés előtt fel kell dolgoznia az eseményeket az eredeti névtérből.

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy a fiók által használt szolgáltatások és szolgáltatások támogatottak a célként megadott régióban.
- Az előzetes verziójú funkciók esetében győződjön meg arról, hogy az előfizetése engedélyezett a célként megadott régióban.
- Ha az Event hubok esetében engedélyezve van a **rögzítési funkció** a névtérben, helyezze át az [Azure Storage-t vagy Azure Data Lake Store gen 2](../storage/common/storage-account-move.md) vagy [Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) fiókot az Event Hubs névtér áthelyezése előtt. A tárolót és a Event Hubs névtereket is tartalmazó erőforráscsoportot áthelyezheti a másik régióba a jelen cikkben ismertetett lépésekhez hasonló módon. 
- Ha a Event Hubs névtér egy Event Hubs- **fürtben**található, [hozzon létre egy dedikált fürtöt](event-hubs-dedicated-cluster-create-portal.md) a **célként megadott régióban** , mielőtt átugorja a jelen cikkben ismertetett lépéseket. Event Hubs-fürt létrehozásához használhatja a [githubon a Gyorsindítás sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-cluster-namespace-eventhub/) is. A sablonban távolítsa el a JSON névtér részét, hogy csak a fürtöt hozza létre. 

## <a name="prepare"></a>Előkészítés
Első lépésként exportáljon egy Resource Manager-sablont. Ez a sablon a Event Hubs névteret leíró beállításokat tartalmaz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza a **minden erőforrás** lehetőséget, majd válassza ki a Event Hubs névteret.

3. Válassza > **Beállítások** > **Exportálás sablon**lehetőséget.

4. A **sablon exportálása** lapon kattintson a **Letöltés** elemre.

    ![Resource Manager-sablon letöltése](./media/move-across-regions/download-template.png)

5. Keresse meg a portálról letöltött. zip fájlt, és bontsa ki a fájlt egy tetszőleges mappába.

   Ez a zip-fájl tartalmazza azokat a. JSON fájlokat, amelyek tartalmazzák a sablont és a parancsfájlokat a sablon telepítéséhez.


## <a name="move"></a>Áthelyezés

A sablon üzembe helyezésével hozzon létre egy Event Hubs névteret a célként megadott régióban. 


1. Az Azure Portalon válassza az **Erőforrás létrehozása** lehetőséget.

2. A **Keresés a Marketplace-en** mezőbe írja be a **template deployment** kifejezést, majd nyomja le az **ENTER** billentyűt.

3. Válassza a **Template deployment** lehetőséget.

4. Kattintson a **Létrehozás** gombra.

5. Válassza a **Saját sablon készítése a szerkesztőben** lehetőséget.

6. Válassza a **fájl betöltése**lehetőséget, majd kövesse az utasításokat az utolsó szakaszban letöltött **template. JSON** fájl betöltéséhez.

7. A sablon mentéséhez válassza a **Mentés** lehetőséget. 

8. Az **Egyéni telepítés** lapon kövesse az alábbi lépéseket: 

    1. Válasszon ki egy Azure- **előfizetést**. 

    2. Válasszon ki egy meglévő **erőforráscsoportot** , vagy hozzon létre egyet. Ha a forrás névtere egy Event Hubs fürtben volt, válassza ki azt az erőforráscsoportot, amely a célként megadott régióban található fürtöt tartalmazza. 

    3. Válassza ki a **célhelyet vagy** régiót. Ha kiválasztott egy meglévő erőforráscsoportot, ez a beállítás csak olvasható. 

    4. A **Beállítások** szakaszban hajtsa végre a következő lépéseket:
    
        1. adja meg az új **névtér nevét**. 

            ![Resource Manager-sablon üzembe helyezése](./media/move-across-regions/deploy-template.png)

        2. Ha a forrás névtere egy **Event Hubs fürtben**volt, írja be az **erőforráscsoport** nevét és a **Event Hubs-fürtöt** a **külső azonosító**részeként. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Ha a névtérben az Event hub Storage-fiókot használ az események rögzítéséhez, adja meg az erőforráscsoport nevét és a Storage `StorageAccounts_<original storage account name>_external` -fiókot a (z) mezőben. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Jelölje be az **Elfogadom a fenti feltételeket és kikötéseket** jelölőnégyzetet. 
    
    6. Most válassza a **vásárlás lehetőséget** a telepítési folyamat elindításához. 

## <a name="discard-or-clean-up"></a>Elvetés vagy tisztítás
Ha az üzembe helyezést követően el szeretné indítani a műveletet, törölheti a **cél Event Hubs névteret**, és megismételheti a cikk [előkészítés](#prepare) és [Áthelyezés](#move) szakaszában ismertetett lépéseket.

Ha véglegesíteni szeretné a módosításokat, és egy Event Hubs névtér áthelyezését hajtja végre, törölje a **forrás Event Hubs névteret**. A névtér törlése előtt győződjön meg arról, hogy a névtérben lévő összes eseményt feldolgozta. 

Event Hubs névtér (forrás vagy cél) törlése a Azure Portal használatával:

1. A Azure Portal tetején található Keresés ablakban írja be a **Event Hubs**kifejezést, és válassza a **Event Hubs** lehetőséget a keresési eredmények közül. A listában megjelenik a Event Hubs névterek listája.

2. Válassza ki a törölni kívánt cél névteret, és válassza a **Törlés** lehetőséget az eszköztárból. 

    ![Névtér törlése – gomb](./media/move-across-regions/delete-namespace-button.png)

3. Az **erőforrások törlése*** lapon ellenőrizze a kiválasztott erőforrásokat, és erősítse meg a törlést az **Igen**érték beírásával, majd válassza a **Törlés**lehetőséget. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure Event Hubs névteret helyezett át egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
