<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>A StorSimple karbantartási mód frissítések Windows PowerShell telepítése
1. Ha még nem tette meg, hozzáférést biztosít az eszköz soros konzoljához és select 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**. 
2. Írja be a jelszót. Az alapértelmezett jelszó **jelszó1**.
3. A parancssorba írja be:
   
     `Get-HcsUpdateAvailability` 
4. Ha frissítések érhetők el, és hogy a frissítések zavaró vagy nem zavaró, értesítést fog kapni. Zavaró frissítések alkalmazásához szeretné karbantartási módba helyezni az eszközt. Lásd: [2. lépés: Adja meg a karbantartási mód](../articles/storsimple/storsimple-update-device.md#step2) utasításokat.
5. Ha az eszköz karbantartási módban van, a parancssorba írja be a:`Start-HcsUpdate`
6. Megerősítést kér bekéri. Miután meggyőződött róla, hogy a frissítéseket, a vezérlő jelenleg elérő lesz telepíthető. A frissítések telepítése után újraindul a tartományvezérlő. 
7. A frissítések állapotának figyelése. Típus:
   
    `Get-HcsUpdateStatus`
   
    Ha a `RunInProgress` van `True`, a frissítés még folyamatban van. Ha `RunInProgress` van `False`, azt jelzi, hogy a frissítés befejeződött.  
8. Ha a frissítés telepítve van az aktuális történik, és azt újraindult, a többi tartományvezérlő csatlakozhat, és hajtsa végre az 1 – 6. lépéseket.
9. Miután mindkét tartományvezérlők frissítése, lépjen ki a karbantartási módban. Lásd: [4. lépés: Kilépés a karbantartási mód](../articles/storsimple/storsimple-update-device.md#step4) utasításokat.

