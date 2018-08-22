<!--author=alkohli last changed:02/10/2017-->

---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 08/20/2018
ms.author: alkohli
ms.custom: include file
---

#### <a name="to-create-a-new-service"></a>Új szolgáltatás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a Microsoft-fiók hitelesítő adataival.

2. Az Azure Portalon kattintson az **Erőforrás létrehozása** elemre, majd a piactéren az **Összes megjelenítése** gombra.

    ![StorSimple-eszközkezelő létrehozása](./media/storsimple-8000-create-new-service/createssdevman1.png)

    _StorSimple fizikai eszközök_ keresése. Válassza ki, majd kattintson a **StorSimple fizikaieszköz-sorozat** lehetőségre, majd kattintson a **Létrehozás** gombra. Vagy az Azure Portalon kattintson a **+**, majd a **Storage** részen a **StorSimple Physical Device Series** elemre.

    ![StorSimple-eszközkezelő létrehozása](./media/storsimple-8000-create-new-service/createssdevman11.png)

3. Hajtsa végre a következő lépéseket a **StorSimple-eszközkezelő** paneljén:
   
   1. Adjon egy egyedi **Erőforrásnevet** a szolgáltatásnak. Ez a szolgáltatás azonosítására szolgáló valódi név. A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat. A névnek betűvel vagy számmal kell kezdődnie és végződnie.

   2. A legördülő listából válasszon egy **előfizetést**. Az előfizetés az Ön számlázási fiókjához lesz társítva. Ez a mező nem jelenik meg abban az esetben, ha csak egy előfizetéssel rendelkezik.

   3. Egy **Erőforráscsoporthoz** választhatja a **Meglévő használata** vagy az **Új létrehozása** lehetőséget. További információk: [Azure-erőforráscsoportok](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).
   
   4. Adjon meg egy **helyet** a szolgáltatáshoz. Általában az eszköz telepítésének földrajzi helyéhez legközelebb eső hely a legoptimálisabb választás. Ezenkívül az alábbi szempontokat is érdemes figyelembe vennie: 
      
      * Ha olyan folyamatban lévő számítási feladatai vannak az Azure rendszerben, amelyeket a StorSimple-eszközre is telepíteni szeretne, akkor válassza azt az adatközpontot.
      * A StorSimple-eszközkezelő és az Azure Storage szolgáltatás helye eltérő is lehet. Ilyen esetben a StorSimple-eszközkezelő fiókot és az Azure-tárfiókot külön kell létrehoznia. Azure-tárfiók létrehozásához keresse meg az Azure Storage szolgáltatást az Azure Portalon, és kövesse az [Azure-tárfiók létrehozása](../articles/storage/common/storage-quickstart-create-account.md) című szakasz lépéseit. Ha létrehozta a fiókot, az [Új tárfiók konfigurálása a szolgáltatáshoz](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#configure-a-new-storage-account-for-the-service) című szakasz lépéseit követve adja hozzá a StorSimple-eszközkezelő szolgáltatáshoz.

   5. A szolgáltatáshoz tartozó tárfiók automatikus létrehozásához válassza az **Új tárfiók létrehozása** lehetőséget. Adja meg a tárfiók nevét. Ha máshová szeretné menteni az adatokat, akkor törölje a pipát a jelölőnégyzetből.

   6. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, ha egy, a szolgáltatásra mutató gyorshivatkozást szeretne létrehozni az irányítópulton.
      
   7. A StorSimple-eszközkezelő létrehozásához kattintson a **Létrehozás** gombra.

       ![StorSimple-eszközkezelő létrehozása](./media/storsimple-8000-create-new-service/createssdevman2.png)
   
A szolgáltatás létrehozása eltarthat néhány percig. A szolgáltatás sikeres létrehozása után megjelenik egy értesítés, és megnyílik az új szolgáltatás panelje.
   
![StorSimple-eszközkezelő létrehozása](./media/storsimple-8000-create-new-service/createssdevman5.png)


