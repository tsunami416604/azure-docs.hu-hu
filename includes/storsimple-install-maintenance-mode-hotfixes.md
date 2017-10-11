<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>A StorSimple karbantartási mód gyorsjavítások Windows PowerShell telepítése
> [!IMPORTANT]
> A karbantartási módban kell először telepítse a gyorsjavítást, egy tartományvezérlő és a többi tartományvezérlőn.
> 
> 

1. Karbantartási módban helyezze az eszközt. Lásd: [2. lépés: Adja meg a karbantartási mód](../articles/storsimple/storsimple-update-device.md#step2) adja meg a karbantartási mód útmutatást.
2. Telepítse a gyorsjavítást, írja be:
   
     `Start-HcsHotfix` 
3. Amikor a rendszer kéri, adja meg a gyorsjavítás fájlokat tartalmazó hálózati megosztott mappa elérési útját.
4. Megerősítést kér bekéri. Típus **Y** a gyorsjavítás telepítése gombra.
5. Egy tartományvezérlő a gyorsjavítás alkalmazása után jelentkezzen be a másik vezérlő. Telepítse a gyorsjavítást, mint az előző tartományvezérlő.
6. A gyorsjavítások alkalmazásakor kilép karbantartási módból. Lásd: [4. lépés: Kilépés a karbantartási mód](../articles/storsimple/storsimple-update-device.md#step4) utasításokat.

