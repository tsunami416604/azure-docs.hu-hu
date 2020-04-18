---
title: Azure Event Hubs névtér áthelyezése másik régióba | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan helyezhet át egy Azure Event Hubs névteret az aktuális régióból egy másik régióba.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 2dfc9c517605bbb48bee0b306fb275464cfebe39
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606807"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Azure Event Hubs névterének áthelyezése másik régióba
Vannak különböző forgatókönyvek, amelyekben szeretné áthelyezni a meglévő Event Hubs névtér egyik régióból a másikba. Előfordulhat például, hogy ugyanazzal a konfigurációval rendelkező névteret szeretne létrehozni a teszteléshez. Előfordulhat, hogy egy másodlagos névteret is létre szeretne hozni egy másik régióban a [vész-helyreállítási tervezés](event-hubs-geo-dr.md#setup-and-failover-flow)részeként.

> [!NOTE]
> Ez a cikk bemutatja, hogyan exportálhat egy Azure Resource Manager-sablont egy meglévő Event Hubs névtérhez, majd a sablon használatával hozzon létre egy névteret ugyanazzal a konfigurációs beállításokkal egy másik régióban. Ez a folyamat azonban nem mozgatja a még fel nem dolgozott eseményeket. A törlés előtt fel kell dolgoznia az eseményeket az eredeti névtérből.

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy a fiók által használt szolgáltatások és funkciók támogatottak a célrégióban.
- Az előzetes verziójú funkciók hoz győződjön meg arról, hogy az előfizetés a célrégióban szerepel.
- Ha a névtérben engedélyezve van a **rögzítési funkció** az eseményközpontokhoz, helyezze át az Azure Storage vagy az [Azure Data Lake Store Gen 2](../storage/common/storage-account-move.md) vagy az Azure Data Lake Store Gen [1](../data-lake-store/data-lake-store-migration-cross-region.md) fiókokat az Event Hubs névtér áthelyezése előtt. A storage- és az Event Hubs-névtereket is tartalmazó erőforráscsoportot áthelyezheti a másik régióba a jelen cikkben ismertetettekhez hasonló lépések végrehajtásával. 
- Ha az Event Hubs névtér egy **Eseményközpontok-fürtben**található, [hozzon létre egy dedikált fürtöt](event-hubs-dedicated-cluster-create-portal.md) a **célrégióban,** mielőtt a cikk lépéseit végigmenne. 

## <a name="prepare"></a>Előkészítés
Első lépésekhez exportáljon egy Erőforrás-kezelő sablont. Ez a sablon az Event Hubs névterét leíró beállításokat tartalmazza.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza **az Összes erőforrás** lehetőséget, majd válassza ki az Eseményközpontok névterét.

3. Válassza > **Beállítások** > **exportálása sablont.**

4. Válassza a **Letöltés** gombot a **Sablon exportálása** lapon.

    ![Erőforrás-kezelő sablon letöltése](./media/move-across-regions/download-template.png)

5. Keresse meg a portálról letöltött .zip fájlt, és csomagolja ki a fájlt egy ön által választott mappába.

   Ez a zip-fájl tartalmazza a sablont és a sablon tüzembe helyezéséhez szükséges parancsfájlokat tartalmazó .json fájlokat.


## <a name="move"></a>Áthelyezés

Telepítse a sablont egy Eseményközpont-névtér létrehozásához a célrégióban. 


1. Az Azure Portalon válassza az **Erőforrás létrehozása** lehetőséget.

2. A **Keresés a Marketplace-en** mezőbe írja be a **template deployment** kifejezést, majd nyomja le az **ENTER** billentyűt.

3. Válassza a **Template deployment** lehetőséget.

4. Kattintson a **Létrehozás** gombra.

5. Válassza a **Saját sablon készítése a szerkesztőben** lehetőséget.

6. Válassza **a Fájl betöltése**lehetőséget, majd kövesse az utasításokat az utolsó szakaszban letöltött **template.json** fájl betöltéséhez.

7. A sablon mentéséhez válassza a **Mentés** gombot. 

8. Az **Egyéni telepítés** lapon hajtsa végre az alábbi lépéseket: 

    1. Válasszon egy **Azure-előfizetést.** 

    2. Jelöljön ki egy meglévő **erőforráscsoportot,** vagy hozzon létre egyet. Ha a forrásnévtér egy Eseményközpont-fürtben volt, jelölje ki a célrégióban fürtöt tartalmazó erőforráscsoportot. 

    3. Válassza ki a **célhelyet** vagy -régiót. Ha meglévő erőforráscsoportot választott, ez a beállítás írásvédett. 

    4. A **BEÁLLÍTÁSOK szakaszban** tegye a következő lépéseket:
    
        1. adja meg az új **névtér nevét**. 

            ![Erőforrás-kezelő sablon telepítése](./media/move-across-regions/deploy-template.png)

        2. Ha a forrásnévtér **egy Eseményközpont-fürtben**volt, adja meg az **erőforráscsoport** és az **Eseményközpontok fürt** nevét **a külső azonosító**részeként. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Ha a névtérben lévő eseményközpont egy storage-fiókot használ az események `StorageAccounts_<original storage account name>_external` rögzítéséhez, adja meg az erőforráscsoport nevét és a mező tárfiókját. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Válassza az **Elfogadom a fenti feltételeket** jelölőnégyzetet. 
    
    6. Most válassza **a Vásárlás lehetőséget** a telepítési folyamat elindításához. 

## <a name="discard-or-clean-up"></a>Selejtezés vagy tisztítás
A telepítés után, ha szeretné elölről kezdeni, törölheti a **cél eseményközpontok névterét,** és megismételheti a cikk [Előkészítés](#prepare) és [áthelyezés](#move) szakaszában ismertetett lépéseket.

A módosítások véglegesítéséhez és az Event Hubs névtér áthelyezésének befejezéséhez törölje a **forráseseményközpontok névterét.** A névtér törlése előtt győződjön meg arról, hogy a névtér összes eseményét feldolgozta. 

Az Event Hubs-névtér (forrás vagy cél) törlése az Azure Portal használatával:

1. Az Azure Portal tetején található keresési ablakban írja be az **Event Hubs parancsot,** és a keresési eredmények közül válassza **az Eseményközpontok lehetőséget.** Az Event Hubs névterek egy listában jelennek meg.

2. Jelölje ki a törölni kívánt célnévteret, és válassza az eszköztár **Törlés parancsát.** 

    ![Névtér törlése - gomb](./media/move-across-regions/delete-namespace-button.png)

3. Az **Erőforrások törlése*** lapon ellenőrizze a kijelölt erőforrásokat, és erősítse meg a törlést az **igen**beírással, majd kattintson a **Törlés gombra.** 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban áthelyezett egy Azure Event Hubs névteret egyik régióból a másikba, és megtisztította a forráserőforrásokat.  Ha többet szeretne tudni az erőforrások régiók közötti áthelyezéséről és az Azure-beli vészhelyreállításról:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
