## <a name="pausing-resetting-or-stopping-the-session"></a>Szüneteltetés, alaphelyzetbe állítását vagy a munkamenet leállítása

A munkamenet leállításához átmenetileg, hívhat a Stop metódus. Így leáll minden olyan figyelők és a környezet feldolgozó, akkor is, ha ProcessFrame() indít el. Ezután hívhat Start() folytatására. Amikor folytatása, a munkamenet már rögzített környezeti adatok változatlan marad.
