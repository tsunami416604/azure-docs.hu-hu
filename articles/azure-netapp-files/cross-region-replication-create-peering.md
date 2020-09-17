---
title: Replikációs társítás létrehozása a Azure NetApp Fileshoz | Microsoft Docs
description: Ismerteti, hogyan lehet kötet-replikációs társítást létrehozni a Azure NetApp Files számára a régiók közötti replikáció beállításához.
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
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: df70f8a37a7223119068afd323583ea6126ca542
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708827"
---
# <a name="create-replication-peering-for-azure-netapp-files"></a>Replikációs társítás létrehozása a Azure NetApp Fileshoz

> [!IMPORTANT]
> A régiók közötti replikációs szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. Be kell küldenie egy várólistára vonatkozó kérelmet a szolgáltatás eléréséhez a [Azure NetApp Files régiók közötti replikációs várólista-küldési lapon](https://aka.ms/anfcrrpreviewsignup). Várjon egy hivatalos visszaigazoló e-mailt a Azure NetApp Files csapattól a régiók közötti replikációs szolgáltatás használata előtt.

Ez a cikk bemutatja, hogyan állíthatja be a régiók közötti replikációt a replikálási társítás létrehozásával. 

A replikációs társítás beállítása lehetővé teszi, hogy aszinkron módon replikálja az adatok Azure NetApp Files kötetről (forrás) egy másik Azure NetApp Files kötetre (cél). A forrás kötetet és a célként megadott kötetet külön régiókban kell telepíteni. A célként megadott kapacitási készlet szolgáltatási szintje megegyezik a forrás-kapacitás készletével, vagy kijelölhet egy másik szolgáltatási szintet is.   

Azure NetApp Files replikáció jelenleg nem támogatja több előfizetést; az összes replikációt egyetlen előfizetés alatt kell végrehajtani.

Mielőtt elkezdené, ellenőrizze, hogy áttekintette [-e a régiók közötti replikációra vonatkozó követelményeket és szempontokat](cross-region-replication-requirements-considerations.md).  

## <a name="locate-the-source-volume-resource-id"></a>A forrás kötet erőforrás-AZONOSÍTÓjának megkeresése  

Be kell szereznie a replikálni kívánt forrásfájl erőforrás-AZONOSÍTÓját. 

1. Nyissa meg a forrás kötetet, és a beállítások területen kattintson a **Tulajdonságok** elemre a forrás kötet erőforrás-azonosítójának megjelenítéséhez.   
    ![Forrás kötet erőforrás-AZONOSÍTÓjának megkeresése](../media/azure-netapp-files/cross-region-replication-source-volume-resource-id.png)
 
2. Másolja az erőforrás-azonosítót a vágólapra.  Erre később még szüksége lesz.

## <a name="create-the-data-replication-volume-the-destination-volume"></a>Az adatreplikálási kötet (a cél kötet) létrehozása

Létre kell hoznia egy célként megadott kötetet, amelybe a forrás kötet adatait replikálni kívánja.  A cél kötet létrehozása előtt egy NetApp-fiókkal és egy, a céltartományban található kapacitási készlettel kell rendelkeznie. 

1. A célként megadott fióknak a forrás kötet régiójától eltérő régióban kell lennie. Ha szükséges, hozzon létre egy NetApp-fiókot az Azure-régióban, amelyet a rendszer a NetApp- [fiók létrehozása](azure-netapp-files-create-netapp-account.md)című témakör lépéseit követve a replikáláshoz használ.   
Másik régióban is kiválaszthat egy meglévő NetApp-fiókot.  

2. Ha szükséges, hozzon létre egy kapacitási készletet az újonnan létrehozott NetApp-fiókban a kapacitás-készlet [beállítása](azure-netapp-files-set-up-capacity-pool.md)című témakörben leírtak szerint.   

    Kiválaszthat egy meglévő kapacitási készletet is a replikálási cél kötetének üzemeltetéséhez.  

    A célként megadott kapacitási készlet szolgáltatási szintje megegyezik a forrás-kapacitás készletével, vagy kijelölhet egy másik szolgáltatási szintet is.

3. Delegáljon egy alhálózatot a régióban a replikáláshoz. Ehhez kövesse az [alhálózat delegálása Azure NetApp Filesre](azure-netapp-files-delegate-subnet.md)című témakör lépéseit.

4. Hozza létre az adatreplikálási kötetet úgy, hogy kijelöli a **kötetek** a Storage szolgáltatásban elemet a cél NetApp-fiókban. Ezután kattintson az **+ adatreplikálás hozzáadása** gombra.  

    ![Adatreplikálás hozzáadása](../media/azure-netapp-files/cross-region-replication-add-data-replication.png)
 
5. A megjelenő kötet létrehozása oldalon hajtsa végre a következő mezőket az **alapok** lapon:
    * Kötet neve
    * Kapacitási készlet
    * Mennyiségi kvóta
        > [!NOTE] 
        > Javasoljuk, hogy a kötet kvótájának mérete tükrözze a forrás kötetet.
    * Virtuális hálózat 
    * Alhálózat

    A mezők részletes ismertetését az [NFS-kötet létrehozása](azure-netapp-files-create-volumes.md#create-an-nfs-volume)című témakörben tekintheti meg. 

6. A **protokoll** lapon válassza ki ugyanazt a protokollt, mint a forrás kötet.  
Az NFS protokoll esetében győződjön meg arról, hogy az exportálási házirend szabályai megfelelnek az exportáláshoz hozzáférő távoli hálózatban lévő gazdagépek követelményeinek.  

7. A **címkék** lapon szükség szerint hozzon létre kulcs/érték párokat.  

8. A **replikálás** lapon illessze be a forrás mennyiségi erőforrás-azonosítóját a [forrás kötet erőforrás-azonosítója](#locate-the-source-volume-resource-id)területen, majd válassza ki a kívánt replikálási ütemtervet. A replikálási ütemterv beállításai a következők: 10 percenként, óránként, naponta, hetente és havonta.  

    ![Kötet replikálásának létrehozása](../media/azure-netapp-files/cross-region-replication-create-volume-replication.png)

9. Kattintson a **felülvizsgálat + létrehozás**elemre, majd kattintson a **Létrehozás** gombra az adatreplikálási kötet létrehozásához.   

    ![Replikáció áttekintése és létrehozása](../media/azure-netapp-files/cross-region-replication-review-create-replication.png)

## <a name="authorize-replication-from-the-source-volume"></a>Replikáció engedélyezése a forrás kötetről  

A replikáció engedélyezéséhez be kell szereznie a replikálási cél kötetének erőforrás-AZONOSÍTÓját, és be kell illesztenie a replikálási forrás kötetének engedélyezés mezőjébe. 

1. A Azure Portal navigáljon a Azure NetApp Files.

2. Nyissa meg a cél NetApp-fiókot és a cél kapacitási készletet, ahol a replikációs cél kötet található.

3. Válassza ki a replikációs cél kötetet, lépjen a **Tulajdonságok** elemre a beállítások területen, és keresse meg a cél kötet **erőforrás-azonosítóját** . Másolja a cél kötet erőforrás-AZONOSÍTÓját a vágólapra.

    ![Tulajdonságok erőforrás-azonosítója](../media/azure-netapp-files/cross-region-replication-properties-resource-id.png) 
 
4. A Azure NetApp Filesban nyissa meg a replikációs forrás fiókját és a forrás kapacitása készletet. 

5. Keresse meg a replikálási forrás kötetét, és jelölje ki. Nyissa meg a **replikálás** a Storage szolgáltatás területen, majd kattintson az **Engedélyezés**elemre.

    ![Replikáció engedélyezése](../media/azure-netapp-files/cross-region-replication-authorize.png) 

6. Az engedélyezés mezőben illessze be a 3. lépésben beszerzett cél replikálási kötet erőforrás-AZONOSÍTÓját, majd kattintson **az OK**gombra.

## <a name="next-steps"></a>Következő lépések  

* [Régiók közötti replikáció](cross-region-replication-introduction.md)
* [A régiók közötti replikáció használatára vonatkozó követelmények és szempontok](cross-region-replication-requirements-considerations.md)
* [Replikációs kapcsolat állapotának megjelenítése](cross-region-replication-display-health-status.md)
* [Kötet replikációs metrikái](azure-netapp-files-metrics.md#replication)
* [Vészhelyreállítás kezelése](cross-region-replication-manage-disaster-recovery.md)
* [Régiók közötti replikáció – replikálás](troubleshoot-cross-region-replication.md)

