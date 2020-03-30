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
ms.openlocfilehash: 4ba5c8b69776b39d8a6640744b0c24600f3a0d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179306"
---
#### <a name="to-create-a-new-service"></a>Új szolgáltatás létrehozása

1.  A Microsoft-fiók hitelesítő adataival jelentkezzen be az <https://portal.azure.com/>Azure Portalra ezen az URL-címen: . Ha az eszközt a Kormányzati portálon telepíti, jelentkezzen be a következő helyen:<https://portal.azure.us/>

2.  Az Azure Portalon kattintson &gt; a + **Erőforrás-tárolás** **+ Create a resource** &gt; létrehozása **StorSimple virtuális sorozat**elemre.

    ![Új szolgáltatás létrehozása](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  A **megnyíló StorSimple Eszközkezelő** panelen tegye a következőket:

    1.  Adjon egy egyedi **Erőforrásnevet** a szolgáltatásnak. Az erőforrás neve egy rövid név, amely a szolgáltatás azonosítására használható. A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat. A névnek betűvel vagy számmal kell kezdődnie és végződnie.

    2.  A legördülő listából válasszon egy **előfizetést**. Az előfizetés az Ön számlázási fiókjához lesz társítva. Ez a mező nem jelenik meg abban az esetben, ha csak egy előfizetéssel rendelkezik.

    3.  Az **Erőforráscsoport csoportban**jelöljön ki egy meglévőt, vagy hozzon létre egy új csoportot. További információk: [Azure-erőforráscsoportok](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4.  Adjon meg egy **helyet** a szolgáltatáshoz. Az [Azure-régiók című témakörben](https://azure.microsoft.com/regions/#services) további információt talál arról, hogy mely szolgáltatások érhetők el az egyes régiókban. Általában válassza ki azt a földrajzi régióhoz legközelebb eső **helyet,** ahol telepíteni szeretné az eszközt. Ezenkívül az alábbiakat is érdemes figyelembe vennie:

        -   Ha már rendelkezik az Azure-ban, amely a StorSimple-eszközzel is üzembe kívánja helyezni, azt javasoljuk, hogy használja az adatközpontot.

        -   A StorSimple Eszközkezelő és az Azure-tárhely két külön helyen lehet. Ilyen esetben a StorSimple-eszközkezelő fiókot és az Azure-tárfiókot külön kell létrehoznia. Azure-tárfiók létrehozásához keresse meg az Azure Storage-ot az Azure Portalon, és kövesse a [Tárfiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)című részben leírt lépéseket. Ha létrehozta a fiókot, az [Új tárfiók konfigurálása a szolgáltatáshoz](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service) című szakasz lépéseit követve adja hozzá a StorSimple-eszközkezelő szolgáltatáshoz.

        -   Ha a virtuális eszközt a kormányzati portálon telepíti, a StorSimple Eszközkezelő szolgáltatás az USA Iowa és az USA Virginia telephelyein érhető el.

    5.  Válassza **az Új Azure-tárfiók létrehozása** lehetőséget, ha automatikusan létrehozhat egy tárfiókot a szolgáltatással. Adja meg **a tárfiók nevét**. Ha máshová szeretné menteni az adatokat, akkor törölje a pipát a jelölőnégyzetből.

    6.  Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, ha egy, a szolgáltatásra mutató gyorshivatkozást szeretne létrehozni az irányítópulton.

    7.  A StorSimple-eszközkezelő létrehozásához kattintson a **Létrehozás** gombra.

        ![Új szolgáltatás létrehozása](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

A szolgáltatás **céloldalára** irányítjuk. A szolgáltatás létrehozása eltarthat néhány percig. A szolgáltatás sikeres létrehozásáról a rendszer értesítést küld, a szolgáltatás pedig **Aktív** állapotú lesz.


