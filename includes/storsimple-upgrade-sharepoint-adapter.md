<!--author=SharS last changed: 9/17/15-->

### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Frissítse a SharePoint 2010 SharePoint 2013-ba, és telepítse a StorSomple Adapter SharePoint
> [!IMPORTANT]
> Azokat a fájlokat, korábban került a külső tárhelyen RBS nem lesz elérhető, amíg a frissítés elkészült, és a RBS szolgáltatás ismét engedélyezve van-e. Hatással korlátozása érdekében hajtsa végre a bármely frissítése vagy újratelepítés tervezett karbantartási időszak alatt történjen.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>Frissítse a SharePoint 2010 SharePoint 2013-ba, és az adapter telepítése
1. A SharePoint 2010-farm vegye figyelembe a externalized Blobok és a tartalom-adatbázisokhoz, amelynek RBS engedélyezve van a BLOB-tároló elérési útja. 
2. Telepítse és konfigurálja az új SharePoint 2013-farmhoz. 
3. Helyezze át adatbázisok, alkalmazások és webhelycsoportok a SharePoint 2010 farmról. az új SharePoint 2013 farmhoz. Utasításokért ugorjon [a verziófrissítést, a SharePoint 2013 áttekintése](https://technet.microsoft.com/library/cc262483.aspx).
4. A StorSimple-Adapter telepítése az új farm SharePoint. Ugrás a [a StorSimple-Adapter telepítése a SharePoint](#install-the-storsimple-adapter-for-sharepoint) eljárásait.
5. Az 1. lépésben feljegyzett információk segítségével RBS engedélyezése ugyanazokat a tartalom-adatbázisokra, és adja meg, amelyet a SharePoint 2010 telepítési használt azonos BLOB tároló elérési. Ugrás a [konfigurálása RBS](#configure-rbs) eljárásait. Ez a lépés befejezése után korábban externalized fájlokat az új farmhoz elérhetőnek kell lennie. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>A StorSimple Adapter összetevőjének verziófrissítését, SharePoint
> [!IMPORTANT]
> Ez a frissítés a következő okok miatt fordulhat elő, a tervezett karbantartási időszak alatt kell ütemezése:
> 
> * Korábban externalized tartalom nem lesz elérhető, amíg az adapter újratelepítése után.
> * A SharePoint a StorSimple Adapter korábbi verziójának eltávolítása után, de az új verzió telepítése előtt a hely feltöltött tartalom a tartalom-adatbázist fogja tárolni. Szüksége lesz a StorSimple eszköz helyezze át ezt a tartalmat az új adapter telepítését követően. Használhatja a Microsoft` RBS Migrate()` részét képező a tartalom áttelepítésének SharePoint PowerShell-parancsmagot. További információkért lásd: [tartalmat át a virtuális gépbe vagy onnan RBS](https://technet.microsoft.com/library/ff628255.aspx). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>A SharePoint a StorSimple Adapter frissítése
1. Távolítsa el a StorSimple Adapter előző verzióját a SharePoint.
   
   > [!NOTE]
   > Ez automatikusan letiltja a RBS, a tartalom-adatbázisok a. Azonban a StorSimple eszköz meglévő Blobok változatlan marad. Mivel RBS le van tiltva, és a Blobok nem lett áttelepítve vissza a tartalom-adatbázisok, ezeket a Blobok irányuló sikertelen lesz. 
   > 
   > 
2. Az új StorSimple-Adapter telepítése a SharePoint. Az új adapter automatikusan felismeri a tartalom-adatbázisok korábban engedélyezve vagy tiltva RBS, és a korábbi beállításokat fogja használni.

