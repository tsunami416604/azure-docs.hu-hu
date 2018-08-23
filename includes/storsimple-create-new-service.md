<!--author=alkohli last changed:01/14/2016-->

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
1. A Microsoft-fiók hitelesítő adatokkal jelentkezzen be a következő URL-címen a klasszikus Azure portálon: [ https://manage.windowsazure.com/ ](https://manage.windowsazure.com/).
2. A klasszikus Azure Portalon kattintson az **Új** > **Data Services** > **StorSimple Manager** > **Gyorslétrehozás** elemre.
3. A megjelenő adatlapon tegye a következőket:
   
   1. Adjon egy egyedi **nevet** a szolgáltatásnak. Ez a szolgáltatás azonosítására szolgáló rövid név. A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat. A névnek betűvel vagy számmal kell kezdődnie és végződnie.
   2. Adjon meg egy **helyet** a szolgáltatáshoz. Általában az eszköz telepítésének földrajzi helyéhez legközelebb eső hely a legoptimálisabb választás. Ezenkívül az alábbiakat is érdemes figyelembe vennie: 
      
      * Ha olyan folyamatban lévő számítási feladatai vannak az Azure rendszerben, amelyeket a StorSimple-eszközre is telepíteni szeretne, akkor válassza azt az adatközpontot.
      * A StorSimple Manager és az Azure Storage szolgáltatás helye eltérő is lehet. Ilyen esetben a StorSimple Manager-fiókot és az Azure-tárfiókot külön kell létrehoznia. Azure-tárfiók létrehozásához keresse meg az Azure Storage szolgáltatást a klasszikus Azure portálon, és kövesse az [Azure-tárfiók létrehozása](../articles/storage/common/storage-quickstart-create-account.md) című szakasz lépéseit. Ha létrehozta a fiókot, az [Új tárfiók konfigurálása a szolgáltatáshoz](../articles/storsimple/storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service) című szakasz lépéseit követve adja hozzá azt a StorSimple Manager szolgáltatáshoz.
   3. A legördülő listából válasszon egy **előfizetést**. Az előfizetés az Ön számlázási fiókjához lesz társítva. Ez a mező nem jelenik meg abban az esetben, ha csak egy előfizetéssel rendelkezik.
   4. A szolgáltatáshoz tartozó tárfiók automatikus létrehozásához válassza az **Új tárfiók létrehozása** lehetőséget. Ez a tárfiók egy speciális névvel jön létre, pl.: „storsimplebwv8c6dcnf”. Ha máshová szeretné menteni az adatokat, akkor törölje a pipát a jelölőnégyzetből. 
   5. A szolgáltatás létrehozásához kattintson a **StorSimple Manager létrehozása** elemre.
   
   ![StorSimple Manager létrehozása](./media/storsimple-create-new-service/HCS_CreateAService-include.png)
   
   Ekkor a **Szolgáltatás** kezdőlapjára kerül. A szolgáltatás létrehozása eltarthat néhány percig. A szolgáltatás sikeres létrehozásáról a rendszer értesítést küld, a szolgáltatás pedig **Aktív** állapotú lesz.
   
   ![Szolgáltatás létrehozása](./media/storsimple-create-new-service/HCS_StorSimpleManagerServicePage-include.png)

![Videó elérhető](./media/storsimple-create-new-service/Video_icon.png)**Videó elérhető**

Az új StorSimple Manager szolgáltatás létrehozását bemutató videó megtekintéséhez kattintson [ide](https://azure.microsoft.com/documentation/videos/create-a-storsimple-manager-service/).

