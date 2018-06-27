### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Telepítse a StorSimple karbantartási mód frissítéseket a Windows PowerShell segítségével

Karbantartási mód frissítések StorSimple eszköz alkalmazásakor az összes i/o-kérelmek fel van függesztve. Például nem felejtő közvetlen elérésű memória (NVRAM) vagy a fürtözött szolgáltatás le van állítva. Mindkét tartományvezérlők adja meg, vagy lépjen ki az ebben a módban újraindul. Ebben a módban való kilépéskor a szolgáltatások folytatása, és a kifogástalan állapotú. (Ez eltarthat néhány percig.)

> [!IMPORTANT]
> * Mielőtt karbantartási módba, ellenőrizze, hogy mindkét eszközvezérlők kifogástalan az Azure portálon. Ha a tartományvezérlő nem működik megfelelően, [forduljon a Microsoft ügyfélszolgálatához](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) a következő lépéseket.
> * Ha karbantartási módban, módosítania először egy tartományvezérlő és a többi tartományvezérlő.

1. A PuTTY használata az soros konzoljához való csatlakozáshoz. Kövesse [a PuTTY a soros konzolhoz való csatlakozáshoz történő használatát](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console) ismertető részletes útmutatásokat. A parancssorban nyomja le az **Enter** billentyűt. Válassza az 1. lehetőség, **jelentkezzen be a teljes körű hozzáférési**.

2. Állítsa a tartományvezérlő karbantartási módba, írja be:
    
    `Enter-HcsMaintenanceMode`

    Mindkét vezérlőhöz indítsa újra a számítógépet karbantartási módba.

3. A karbantartási mód frissítéseinek telepítése. Típus:

    `Start-HcsUpdate`

    Kell megerősítést kérni. Miután meggyőződött róla, hogy a frissítéseket, a vezérlő jelenleg elérő telepítik őket. A frissítések telepítése után újraindul a tartományvezérlő.

4. A frissítések állapotának figyelése. Jelentkezzen be a partner-tartományvezérlő az aktuális vezérlő frissítése folyamatban van, és nem sikerült feldolgozni az egyéb parancsok. Típus:

    `Get-HcsUpdateStatus`

    Ha a `RunInProgress` van `True`, a frissítés még folyamatban van. Ha `RunInProgress` van `False`, azt jelzi, hogy a frissítés befejeződött.

5. Miután a lemez belső vezérlőprogram frissítése sikeresen telepítve van, és a frissített vezérlő újraindult, ellenőrizze a lemez belső vezérlőprogram-verziója. A frissített tartományvezérlő írja be:

    `Get-HcsFirmwareVersion`
   
    A várt lemez belsővezérlőprogram-verziók a következők:  `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Lépjen ki a karbantartási mód. Írja be a minden eszköz vezérlő a következő parancsot:

    `Exit-HcsMaintenanceMode`

    A vezérlők újraindulnak, miután kilépett a karbantartási módból.

7. Térjen vissza az Azure-portálon. A portál nem jelenik meg, hogy telepítette-e a karbantartási mód frissítései 24 óra.