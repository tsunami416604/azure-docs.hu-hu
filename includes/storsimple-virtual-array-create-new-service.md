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
ms.openlocfilehash: 4285ee94d2f2a9b5fff9d1896cdf7013f88801ab
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45979558"
---
#### <a name="to-create-a-new-service"></a>Új szolgáltatás létrehozása

1.  A Microsoft-fiók hitelesítő adatokkal jelentkezzen be az Azure Portalra az URL-címen: <https://portal.azure.com/>. Ha az eszköz a Government Portalon üzembe, jelentkezzen be a következő: <https://portal.azure.us/>

2.  Az Azure Portalon kattintson a **+ erőforrás létrehozása** &gt; **tárolási** &gt; **StorSimple Virtual Series**.

    ![Új szolgáltatás létrehozása](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  Az a **StorSimple-Eszközkezelő** , megnyíló panelen tegye a következőket:

    1.  Adjon egy egyedi **Erőforrásnevet** a szolgáltatásnak. Az erőforrás nevét kötelező a szolgáltatás azonosítására szolgáló felhasználóbarát nevét. A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat. A névnek betűvel vagy számmal kell kezdődnie és végződnie.

    2.  A legördülő listából válasszon egy **előfizetést**. Az előfizetés az Ön számlázási fiókjához lesz társítva. Ez a mező nem jelenik meg abban az esetben, ha csak egy előfizetéssel rendelkezik.

    3.  A **erőforráscsoport**, válasszon egy meglévő, vagy hozzon létre egy új csoportot. További információk: [Azure-erőforráscsoportok](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4.  Adjon meg egy **helyet** a szolgáltatáshoz. Lásd: [Azure-régiók](https://azure.microsoft.com/regions/#services) további információt arról, hogy mely szolgáltatások érhetők el melyik régióban. Általában egy **hely** legközelebb a földrajzi régióban, ahol szeretné az eszköz üzembe helyezése. Ezenkívül az alábbiakat is érdemes figyelembe vennie:

        -   Ha a számítási feladatokat az Azure-ban, amely is szeretné telepíteni a StorSimple-eszközre, azt javasoljuk, hogy azt az adatközpontot használ.

        -   A StorSimple-Eszközkezelő és az Azure tárolási helye eltérő is lehet. Ilyen esetben a StorSimple-eszközkezelő fiókot és az Azure-tárfiókot külön kell létrehoznia. Hozzon létre egy Azure storage-fiókot, az Azure Storage az Azure Portalon keresse meg és kövesse a leírt lépéseket követve [hozzon létre egy tárfiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). Ha létrehozta a fiókot, az [Új tárfiók konfigurálása a szolgáltatáshoz](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service) című szakasz lépéseit követve adja hozzá a StorSimple-eszközkezelő szolgáltatáshoz.

        -   Ha a virtuális eszköz a Government Portalon üzembe, a StorSimple-Eszközkezelő szolgáltatás helyeken érhető el Nekünk Iowa és USA Virginia.

    5.  Válassza ki **hozzon létre egy új Azure storage-fiók** , automatikusan hozzon létre egy tárfiókot a szolgáltatással. Adjon meg egy **tárfióknevet**. Ha máshová szeretné menteni az adatokat, akkor törölje a pipát a jelölőnégyzetből.

    6.  Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, ha egy, a szolgáltatásra mutató gyorshivatkozást szeretne létrehozni az irányítópulton.

    7.  A StorSimple-eszközkezelő létrehozásához kattintson a **Létrehozás** gombra.

        ![Új szolgáltatás létrehozása](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

A rendszer átirányítja a **szolgáltatás** kezdőlapja. A szolgáltatás létrehozása eltarthat néhány percig. A szolgáltatás sikeres létrehozásáról a rendszer értesítést küld, a szolgáltatás pedig **Aktív** állapotú lesz.


