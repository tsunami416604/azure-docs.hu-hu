---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: b7bdeeedaac65f67a3224e824c19e8cad794682b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87507223"
---
#### <a name="to-create-a-new-service"></a>Új szolgáltatás létrehozása

1.  A Microsoft-fiók hitelesítő adataival jelentkezzen be a Azure Portalba ezen az URL-címen: <https://portal.azure.com/> . Ha az eszközt a Government Portalon helyezi üzembe, jelentkezzen be a következő címen:<https://portal.azure.us/>

2.  A Azure Portal kattintson az **+ erőforrás létrehozása** &gt; **tároló** &gt; **StorSimple virtuális sorozat**elemre.

    ![Új szolgáltatás létrehozása](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  A megnyíló **StorSimple Eszközkezelő** panelen tegye a következőket:

    1.  Adjon egy egyedi **Erőforrásnevet** a szolgáltatásnak. Az erőforrás neve egy felhasználóbarát név, amelyet a szolgáltatás azonosítására lehet használni. A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat. A névnek betűvel vagy számmal kell kezdődnie és végződnie.

    2.  A legördülő listából válasszon egy **előfizetést**. Az előfizetés az Ön számlázási fiókjához lesz társítva. Ez a mező nem jelenik meg abban az esetben, ha csak egy előfizetéssel rendelkezik.

    3.  Az **erőforráscsoport**területen válasszon ki egy meglévőt, vagy hozzon létre egy új csoportot. További információk: [Azure-erőforráscsoportok](/azure/azure-resource-manager/management/manage-resource-groups-portal).

    4.  Adjon meg egy **helyet** a szolgáltatáshoz. Az [Azure-régiókról](https://azure.microsoft.com/regions/#services) további információt talál arról, hogy mely szolgáltatások érhetők el az adott régióban. Általánosságban válassza ki a földrajzi régióhoz legközelebb eső **helyet** , ahová az eszközt telepíteni szeretné. Ezenkívül az alábbiakat is érdemes figyelembe vennie:

        -   Ha az Azure-ban már van olyan számítási feladat, amelyet a StorSimple-eszközön is szeretne üzembe helyezni, javasoljuk, hogy használja azt az adatközpontot.

        -   A StorSimple Eszközkezelő és az Azure Storage két különálló helyen lehet. Ilyen esetben a StorSimple-eszközkezelő fiókot és az Azure-tárfiókot külön kell létrehoznia. Azure Storage-fiók létrehozásához navigáljon a Azure Portal Azure Storage-hoz, és kövesse a Storage- [fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)című témakörben leírt lépéseket. Ha létrehozta a fiókot, az [Új tárfiók konfigurálása a szolgáltatáshoz](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service) című szakasz lépéseit követve adja hozzá a StorSimple-eszközkezelő szolgáltatáshoz.

        -   Ha a virtuális eszközt a kormányzati portálon helyezi üzembe, a StorSimple Eszközkezelő szolgáltatás az USA Iowa-beli és az Egyesült államokbeli Virginia-telephelyeken érhető el.

    5.  Válassza az **új Azure Storage-fiók létrehozása** lehetőséget, hogy automatikusan hozzon létre egy Storage-fiókot a szolgáltatással. Adja meg a **Storage-fiók nevét**. Ha máshová szeretné menteni az adatokat, akkor törölje a pipát a jelölőnégyzetből.

    6.  Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, ha egy, a szolgáltatásra mutató gyorshivatkozást szeretne létrehozni az irányítópulton.

    7.  A StorSimple-eszközkezelő létrehozásához kattintson a **Létrehozás** gombra.

        ![Új szolgáltatás létrehozása](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

A **szolgáltatás** kezdőlapjának van átirányítva. A szolgáltatás létrehozása eltarthat néhány percig. A szolgáltatás sikeres létrehozásáról a rendszer értesítést küld, a szolgáltatás pedig **Aktív** állapotú lesz.


