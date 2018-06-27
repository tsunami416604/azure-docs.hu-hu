<!--author=SharS last changed: 12/01/15-->

### <a name="step-1-authorize-a-device-to-change-the-service-data-encryption-key-in-the-azure-classic-portal"></a>1. lépés: Engedélyezze a szolgáltatásadat-titkosítási kulcs a klasszikus Azure portálon módosíthatja az eszköz
Általában az eszköz-rendszergazdai kérni fogja, hogy a szolgáltatás-rendszergazda engedélyezi-e az eszközöket, hogy módosítsa a szolgáltatás adattitkosítási kulcsokat. A szolgáltatás-rendszergazda fog majd engedélyezni az eszközt, hogy a kulcs módosítása.

Ez a lépést a klasszikus Azure portálon. A szolgáltatás-rendszergazda is jelöljön ki egy eszközt az eszközöket, amelyek jogosultak kell megjelenített listáját. Az eszköz majd engedélyezve az szolgáltatás titkosítási kulcsváltozás folyamat elindításához.

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Mely eszközök is engedélyezni kell, hogy módosítsa a szolgáltatás adattitkosítási kulcsokat?
Egy eszköz a következő feltételeknek kell megfelelnie, mielőtt szolgáltatás titkosítási kulcs adatváltozásokat kezdeményezésére is engedélyezhető:

* Lehet, hogy az eszköz online jogosult legyen a szolgáltatás adatokat titkosítási kulcsváltozás engedélyezése.
* 30 perc elteltével újra ugyanarra az eszközre engedélyezhető, ha a kulcs változás nem lett inicializálva.
* Egy másik eszközön, feltéve, hogy a kulcsváltozás nem kezdeményezte a korábban meghatalmazott eszköz engedélyezhető. Után az új eszköz engedélyezve van-e, a régi eszköz nem indítható el a módosítást.
* Egy eszköz nem lehet engedélyezni, amíg folyamatban van az a szolgáltatás adattitkosítási kulcshoz kapcsolódó kulcsváltás.
* Ha egyes a szolgáltatásban regisztrált eszközöket rendelkezik tanúsítványváltást a titkosítási míg mások számára nem engedélyezhető a eszköz. Ezekben az esetekben a jogosult eszközökkel befejezte a szolgáltatásadat-titkosítási kulcs megfelelően módosítani.

> [!NOTE]
> A klasszikus Azure portálon a StorSimple virtuális eszköz nem jelennek meg is engedélyezhető a kulcsváltozás start eszközök listáját.
> 
> 

A következő lépésekkel válassza ki, és a szolgáltatás titkosítási kulcs változását kezdeményezheti az eszköz engedélyezéséhez.

#### <a name="to-authorize-a-device-to-change-the-key"></a>Módosítsa a kulcsot az eszköz hitelesítése
1. A szolgáltatás irányítópult-oldalon, kattintson **módosítás szolgáltatásadat-titkosítási kulcs**.
   
    ![Szolgáltatás-titkosítási kulcs módosítása](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)
2. Az a **módosítás szolgáltatásadat-titkosítási kulcs** párbeszédpanelen válassza ki, és a szolgáltatás titkosítási kulcs változását kezdeményezheti az eszköz hitelesítéséhez. A legördülő listából válassza ki az engedélyezni lehet az összes jogosult eszközre rendelkezik.
3. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png).

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>2. lépés: Használja a Windows PowerShell-lel a szolgáltatás titkosítási kulcs változását kezdeményezése
Ez a lépés StorSimple felületet a jogosult StorSimple eszközön a Windows PowerShell történik.

> [!NOTE]
> Nincsenek műveletek is elvégezhetők a StorSimple Manager szolgáltatás a klasszikus Azure portálon mindaddig, amíg befejeződik a kulcsváltás.
> 
> 

Ha az eszköz soros konzoljához való csatlakozáshoz a Windows PowerShell-felületet használnak, a következő lépésekkel.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>A szolgáltatás titkosítási kulcs változását kezdeményezése
1. Válassza ki az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**.
2. A parancssorba írja be:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Miután a parancsmag sikeresen befejeződött, kap egy új szolgáltatásadat-titkosítási kulcs. Másolja ki és mentse a kulcs használható a 3. lépésben a folyamat során. Ezt a kulcsot a StorSimple Manager szolgáltatásban regisztrált összes fennmaradó eszköz frissítéséhez használható.
   
   > [!NOTE]
   > Ez a folyamat engedélyezése a StorSimple eszköz négy órán belül kell kezdeményezni.
   > 
   > 
   
   Ez az új kulcs majd kerül a szolgáltatás kerül át a szolgáltatásban regisztrált összes eszközt. A szolgáltatás irányítópultján egy figyelmeztetés fog megjelenni. A szolgáltatás le fogja tiltani a regisztrált eszközökön a műveleteket, és az eszköz-rendszergazdai majd a szolgáltatásadat-titkosítási kulcs az egyéb eszközein futó frissítenie kell. Azonban az i/o műveletek (gazdagép adatokat küldeni a felhőben) fog nem működni.
   
   Ha a szolgáltatáshoz regisztrált egyetlen eszközt, a helyettesítő folyamat befejeződött, és ugorjon a következő lépéssel. Ha a szolgáltatáshoz regisztrált több eszközön, ugorjon a 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>3. lépés: A más StorSimple eszközökhöz a szolgáltatásadat-titkosítási kulcs frissítése
A Windows PowerShell-felületet a StorSimple eszköz ezeket a lépéseket kell végrehajtani, ha több eszközt regisztrálni a StorSimple Manager szolgáltatásban. A 2. lépésben beszerzett kulcs: használja a Windows PowerShell-lel kezdeményezheti a szolgáltatás titkosítási kulcs változását kell használni minden a fennmaradó StorSimple eszköz frissítése regisztrálni a StorSimple Manager szolgáltatással.

Frissítéséhez hajtsa végre a következő lépéseket a szolgáltatásadat-titkosítási az eszközön.

#### <a name="to-update-the-service-data-encryption-key"></a>A szolgáltatásadat-titkosítási kulcs frissítése
1. Használja a Windows PowerShell-lel a konzolhoz való csatlakozáshoz. Válassza ki az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**.
2. A parancssorba írja be:
   
    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Adja meg a szolgáltatásadat-titkosítási kulcs beolvasott [2. lépés: használja a Windows PowerShell-lel kezdeményezheti a szolgáltatás titkosítási kulcs változását](#to-initiate-the-service-data-encryption-key-change).

