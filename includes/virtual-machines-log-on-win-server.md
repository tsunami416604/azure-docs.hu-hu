1. A **Csatlakozás** elemre kattintva létrehoz és letölt egy Remote Desktop Protocol fájlt (.rdp-fájlt). A fájl használatához kattintson a **Megnyitás** elemre.
2. Egy figyelmeztetés fog megjelenni arról, hogy az RDP-fájl közzétevője ismeretlen. Ez nem jelent problémát. A Távoli asztal ablakában kattintson a **Csatlakozás** gombra a folytatáshoz.
   
    ![Képernyőkép az ismeretlen közzétevőre vonatkozó figyelmeztetésről.](./media/virtual-machines-log-on-win-server/rdp-warn.png)
3. A **Windows rendszerbiztonsági** ablakban írja be a virtuális gép egy fiókjának a hitelesítő adatait, majd kattintson az **OK** gombra.
   
     **Helyi fiók** – ez általában a helyi fiók felhasználójának neve és jelszava, amelyet a virtuális gép létrehozásakor adott meg. Ebben az esetben a tartomány a virtuális gép neve, és a következő módon van megadva: *vmname*&#92;*username*.  
   
    **Tartományhoz csatlakoztatott virtuális gép** – ha a virtuális gép egy tartományhoz tartozik, a felhasználó nevét a következő formátumban adja meg: *Domain*&amp;#92;*Username*. A fióknak szerepelnie kell a Rendszergazdák csoportban, vagy távoli hozzáférési jogosultságokkal kell rendelkeznie a virtuális géphez.
   
    **Tartományvezérlő** – ha a virtuális gép egy tartományvezérlő, írja be egy, a tartományhoz tartozó tartományi rendszergazdai fiók felhasználónevét és jelszavát.
4. Kattintson az **Igen** gombra a virtuális gép identitásának ellenőrzéséhez és a bejelentkezés befejezéséhez.
   
   ![Képernyőkép a virtuális gép identitásának ellenőrzésére vonatkozó üzenetről.](./media/virtual-machines-log-on-win-server/cert-warning.png)

