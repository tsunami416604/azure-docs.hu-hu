#### <a name="to-create-a-new-service"></a>Új szolgáltatás létrehozása

1.  A Microsoft-fiók hitelesítő adatokkal jelentkezzen be az Azure portálra az URL-címen: <https://portal.azure.com/>. Ha az eszközt kormányzati portál telepítésével, jelentkezzen be a(z): <https://portal.azure.us/>

2.  Az Azure portálon kattintson **+ hozzon létre egy erőforrást** &gt; **tárolási** &gt; **StorSimple virtuális adatsorozat**.

    ![Új szolgáltatás létrehozása](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  Az a **StorSimple Device Manager** panel, amelyen megnyílik, tegye a következőket:

    1.  Adjon egy egyedi **Erőforrásnevet** a szolgáltatásnak. Az erőforrás nevének megadása a szolgáltatás azonosítására szolgáló rövid nevét. A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat. A névnek betűvel vagy számmal kell kezdődnie és végződnie.

    2.  A legördülő listából válasszon egy **előfizetést**. Az előfizetés az Ön számlázási fiókjához lesz társítva. Ez a mező nem jelenik meg abban az esetben, ha csak egy előfizetéssel rendelkezik.

    3.  A **erőforráscsoport**, válasszon egy meglévő, vagy hozzon létre egy új csoportot. További információk: [Azure-erőforráscsoportok](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4.  Adjon meg egy **helyet** a szolgáltatáshoz. Lásd: [Azure-régiókat](https://azure.microsoft.com/regions/#services) további információt arról, hogy mely szolgáltatások érhetőek melyik régióban. Válasszon általánosságban egy **hely** legközelebbi a földrajzi régióban, ahol az eszközt telepíteni szeretné. Ezenkívül az alábbiakat is érdemes figyelembe vennie:

        -   Ha meglévő alkalmazások, amelyek akkor is telepíteni szeretne a StorSimple eszközt az Azure-ban, azt javasoljuk, hogy használja-e azt az adatközpontot.

        -   A StorSimple Device Manager és az Azure storage két külön helyen lehet. Ilyen esetben a StorSimple-eszközkezelő fiókot és az Azure-tárfiókot külön kell létrehoznia. Azure-tárfiók létrehozásához keresse meg az Azure Storage szolgáltatást az Azure Portalon, és kövesse az [Azure-tárfiók létrehozása](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account) című szakasz lépéseit. Ha létrehozta a fiókot, az [Új tárfiók konfigurálása a szolgáltatáshoz](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service) című szakasz lépéseit követve adja hozzá a StorSimple-eszközkezelő szolgáltatáshoz.

        -   Ha a virtuális eszközt a kormányzati portál üzembe, a StorSimple Device Manager szolgáltatás Velünk Iowa és Velünk Virginia helyen érhető.

    5.  Válassza ki **hozzon létre egy új Azure-tárfiók** a szolgáltatással a tárfiók automatikus létrehozásához. Adjon meg egy **tárfióknév**. Ha máshová szeretné menteni az adatokat, akkor törölje a pipát a jelölőnégyzetből.

    6.  Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, ha egy, a szolgáltatásra mutató gyorshivatkozást szeretne létrehozni az irányítópulton.

    7.  A StorSimple-eszközkezelő létrehozásához kattintson a **Létrehozás** gombra.

        ![Új szolgáltatás létrehozása](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Irányítja a felhasználót a **szolgáltatás** kezdőlapja. A szolgáltatás létrehozása eltarthat néhány percig. A szolgáltatás sikeres létrehozásáról a rendszer értesítést küld, a szolgáltatás pedig **Aktív** állapotú lesz.


