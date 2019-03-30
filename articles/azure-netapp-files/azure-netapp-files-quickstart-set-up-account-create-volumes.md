---
title: Állítsa be az Azure NetApp fájljait, és hozzon létre egy kötetet |} A Microsoft Docs
description: Ismerteti, hogyan lehet gyorsan Azure NetApp fájlok beállítását, és hozzon létre egy kötetet.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstarts
ms.date: 2/20/2019
ms.author: b-juche
ms.openlocfilehash: e2b9b3cdcb712fcf6c415f574dc687e80ae9ee3b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660510"
---
# <a name="set-up-azure-netapp-files-and-create-a-volume"></a>Az Azure NetApp Files beállítása és kötet létrehozása 

Ez a cikk bemutatja, hogyan gyorsan Azure NetApp fájlok beállítását, és hozzon létre egy kötetet. 

## <a name="before-you-begin"></a>Előkészületek 

Kell tartoznia a nyilvános előzetes program és az engedélyezési listához hozzáadni kívánt Microsoft.NetApp erőforrás-szolgáltató elérésére. A nyilvános előzetes verzió programjához való csatlakozással kapcsolatos részletekért lásd az [Azure NetApp Files nyilvános előzetes verzió regisztrációs lapját](https://aka.ms/nfspublicpreview). 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>A Azure NetApp-fájlok és a NetApp erőforrás-szolgáltató regisztrálása

1. Az Azure Portalon kattintson a jobb felső sarokban található Azure Cloud Shell ikonjára.

      ![Az Azure Cloud Shell ikon](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Adja meg az előfizetést, amely már szerepel az engedélyezési listán NetApp Azure-fájlok:
    
        az account set --subscription <subscriptionId>

3. Az Azure-erőforrás-szolgáltató regisztrálásához: 
    
        az provider register --namespace Microsoft.NetApp --wait  

    A regisztrációs folyamat befejezése hosszabb időt is igénybe vehet.

## <a name="create-a-netapp-account"></a>NetApp-fiók létrehozása

1. Az Azure Portalon a keresőmezőbe írja be a **Azure NetApp fájlok** majd **Azure NetApp fájlok (előzetes verzió)** a listából, amely akkor jelenik meg.

      ![Válassza ki a Azure NetApp-fájlok](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Új NetApp-fiók létrehozásához kattintson a **+ Hozzáadás** gombra.

     ![NetApp új fiók létrehozása](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Az új NetApp ablakról adja meg a következő információkat: 
   1. Adja meg **myaccount1** a fiók neve. 
   2. Válassza ki előfizetését.
   3. Válassza ki **új létrehozása** új erőforráscsoport létrehozásához. Adja meg **myRG1** az erőforráscsoport neveként. Kattintson az **OK** gombra. 
   4. Válassza ki a fiók helye.  

      ![Új NetApp fiókokat felsoroló ablakról](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Erőforrás-csoport ablak](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Kattintson a **létrehozás** NetApp új fiók létrehozásához.

## <a name="set-up-a-capacity-pool"></a>Kapacitáskészlet beállítása

1. Az Azure Files-NetApp felügyeleti panelen válassza ki a NetApp fiókját (**myaccount1**).

    ![NetApp fiók kiválasztása](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. NetApp fiókja az Azure Files-NetApp felügyeleti panelen kattintson az **kapacitás készletek**.

    ![Kattintson a kapacitás-készletek](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Kattintson a **+ készletek hozzáadása**. 

    ![Kattintson az Add-készletek](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Adja meg a kapacitás készlet adatait: 
    1. Adja meg **mypool1** a készlet neveként.
    2. Válassza ki **prémium** a szolgáltatási szint. 
    3. Adja meg **4 (TiB)** , a készlet méretét. 

5. Kattintson az **OK** gombra.

## <a name="create-a-volume-for-azure-netapp-files"></a>Kötet létrehozása az Azure NetApp Files számára

1. NetApp fiókja az Azure Files-NetApp felügyeleti panelen kattintson az **kötetek**.

    ![Kattintson a kötetek elemre](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Kattintson a **+ Kötet hozzáadása** gombra.

    ![Kattintson a kötetek hozzáadása](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. A létrehozását egy kötet ablakot adja meg a kötet adatait: 
   1. Adja meg **myvol1** a kötet neveként. 
   2. Adja meg **myfilepath1** , a fájl elérési útját az exportálási útvonal a kötet létrehozásához használt.
   3. Válassza ki a kapacitás-készletet (**mypool1**).
   4. Használja az alapértelmezett érték a kvótát. 
   5. Kattintson a virtuális hálózat **új létrehozása** hozhat létre egy új Azure virtuális hálózat (Vnet).  Ezután adja meg a következő információkat:
       * Adja meg **myvnet1** a virtuális hálózat neveként.
       * A beállítás, például 10.7.0.0/16-címtereket határozhat meg
       * Adja meg **myANFsubnet** alhálózati nevet.
       * Adja meg az alhálózat címtartományának, például 10.7.0.0/24. Vegye figyelembe, hogy nem oszthat meg a kijelölt alhálózatot más erőforrásokkal.
       * Válassza ki **Microsoft.NetApp/volumes** alhálózati delegáláshoz.
       * Kattintson a **OK** a virtuális hálózat létrehozásához.
   6. Része az alhálózatnak, válassza ki az újonnan létrehozott virtuális hálózat (**myvnet1**) egy delegált alhálózata.

      ![Hozzon létre egy kötetet ablak](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Virtuális hálózat időszak létrehozása](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Kattintson az **Áttekintés + létrehozás** elemre.

    ![Tekintse át és időszak létrehozása](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. Tekintse át a kötet adatait, majd kattintson a **létrehozás**.  
    A kötet létrehozása a kötetek panel jelenik meg.

    ![Kötet létrehozása](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="next-steps"></a>További lépések  

* [Megismerheti a NetApp Azure-fájlok hierarchiája](azure-netapp-files-understand-storage-hierarchy.md)
* [Kötetek kezelése az Azure Files-NetApp használatával](azure-netapp-files-manage-volumes.md) 
