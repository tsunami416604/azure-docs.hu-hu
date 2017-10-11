<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>A StorSimple Windows PowerShell segítségével rendszeres gyorsjavítások telepítése
1. Az eszköz soros konzoljához való csatlakozáshoz. További információkért lásd: [1. lépés: csatlakozás soros konzolon](../articles/storsimple/storsimple-update-device.md#step1).
2. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**. Írja be a jelszót. Az alapértelmezett jelszó **jelszó1**.
3. A parancssorba írja be:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Ez a parancs csak a rendszeres gyorsjavítások vonatkozik. A parancs futtatása csak egy tartományvezérlőn, de mindkét tartományvezérlők frissülni fog.
    > A vezérlő feladatátvétel Észreveheti a frissítés során; azonban a feladatátvétel nem érinti a rendszer rendelkezésre állás vagy a műveletet.

4. Amikor a rendszer kéri, adja meg a gyorsjavítás fájlokat tartalmazó hálózati megosztott mappa elérési útját.
5. Megerősítést kér bekéri. Típus **Y** a gyorsjavítás telepítése gombra.

