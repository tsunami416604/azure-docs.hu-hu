<!--author=alkohli last changed: 12/01/15-->


#### <a name="to-configure-and-register-the-device"></a>Az eszköz konfigurálása és regisztrálása
1. Nyissa meg a Windows PowerShell felületet a StorSimple-eszköz soros konzoljában. Az erre vonatkozó utasítások [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](#use-putty-to-connect-to-the-device-serial-console) című szakaszban találhatók. **Pontosan kövesse az ismertetett eljárást, különben nem fog tudni hozzáférni a konzolhoz.**
2. A megnyitott munkamenetben nyomja le egyszer az Enter billentyűt a parancssor megjelenítéséhez. 
3. Ekkor a rendszer arra kéri, hogy válassza ki az eszközhöz beállítani kívánt nyelvet. Válassza ki a nyelvet, majd nyomja le az Enter billentyűt. 
   
    ![StorSimple-eszköz konfigurálása és regisztrálása, 1](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)
4. A soros konzol megjelenő menüjében válassza az 1. lehetőséget a teljes körű hozzáféréssel való bejelentkezéshez. 
   
    ![StorSimple-eszköz regisztrálása, 2](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
   
     Végezze le az 5–12. lépést az eszköz minimálisan szükséges hálózati beállításainak konfigurálásához. **Ezeket a konfigurálási lépéseket az eszköz aktív vezérlőjén kell elvégezni.** A vezérlő állapota a soros konzol menüjének címsorában látható. Ha nincs kapcsolat az aktív vezérlővel, akkor bontsa a kapcsolatot, és csatlakozzon újra az aktív vezérlőhöz.
5. Írja be a jelszót a parancssorba. Az eszköz alapértelmezett jelszava: **Password1**.
6. Írja be a következő parancsot:
   
     `Invoke-HcsSetupWizard` 
7. Ekkor megjelenik egy telepítővarázsló az eszköz hálózati beállításainak konfigurálásához. Adja meg a következő információkat: 
   
   * A DATA 0 hálózati adapter IP-címe
   * Alhálózati maszk
   * Átjáró
   * Az elsődleges DNS-kiszolgáló IP-címe
   * Az elsődleges NTP-kiszolgáló IP-címe
     
     > [!NOTE]
     > Az alhálózati maszk és a DNS-beállítások alkalmazása néhány percig is eltarthat. Ha „Az eszköz nem áll készen” hibaüzenetet kapja, ellenőrizze a fizikai hálózati kapcsolatot az aktív vezérlő DATA 0 hálózati adapterén.
     > 
     > 
8. (Nem kötelező) konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, **vegye figyelembe, hogy ha webproxyt használ, azt csak itt tudja beállítani**. További információ: [Configure web proxy for your device](../articles/storsimple/storsimple-configure-web-proxy.md) (Webproxy beállítása az eszközhöz). Ha ennél a lépésnél bármilyen problémába ütközik, tekintse át [A webproxy konfigurálásakor felmerülő problémák](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings) című hibaelhárítási útmutatót.

     > [!NOTE]
     > A Ctrl + C billentyűkombinációval bármikor kiléphet a varázslóból. A parancs kiadása előtt alkalmazott beállításokat megőrzi a rendszer.

1. Biztonsági okokból az eszköz rendszergazdai jelszava az első munkamenet végeztével lejár, így a további munkamenetekhez meg kell változtatnia azt. Amikor a rendszer erre kéri, adjon meg az eszközhöz egy rendszergazdai jelszót. Az eszköz rendszergazdai jelszavának 8–15 karakter hosszúságúnak kell lennie. A jelszónak kisbetűk, nagybetűk, számok és speciális karakterek kombinációjából kell állnia.
2. A StorSimple Snapshot Manager jelszavát is itt állíthatja be. Ezt a jelszót az eszköz StorSimple Snapshot Manager szolgáltatást futtató Windows-gazdagépen való hitelesítéséhez használhatja. Amikor a rendszer kéri, adjon meg egy 14–15 karakterből álló jelszót. A jelszónak az alábbiak közül hármat tartalmaznia kell: kisbetűk, nagybetűk, számok és speciális karakterek. 
   
   ![StorSimple-eszköz regisztrálása, 4](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)
   
   A StorSimple Snapshot Manager jelszavát a StorSimple Manager szolgáltatási felületén tudja alaphelyzetbe állítani. Részletes utasítások: [A StorSimple-jelszavak megváltoztatása a StorSimple Manager szolgáltatással](../articles/storsimple/storsimple-change-passwords.md).
   
   A lépés során fellépő hibák elhárítására vonatkozó utasításokat a [Jelszavakkal kapcsolatos hibák](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords) című hibaelhárítási útmutatóban találja.
3. A telepítővarázsló utolsó lépésként regisztrálja az eszközt a StorSimple Manager szolgáltatásban. Ehhez szükség van a 2. lépésben kapott szolgáltatásregisztrációs kulcsra. Előfordulhat, hogy a regisztrációs kulcs megadása után 2–3 percet várni kell az eszköz regisztrálására.
   
   A lehetséges eszközregisztrációs hibák elhárítására vonatkozó utasításokat [Az eszközök regisztrációja során felmerülő hibák](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-device-registration) című szakaszban találja. Részletes hibaelhárítási útmutatás a [Részletes hibaelhárítási példa](../articles/storsimple/storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example) című szakaszban található.
4. Az eszköz regisztrálása után megjelenik egy szolgáltatásadat-titkosítási kulcs. Másolja ki a kulcsot, és mentse egy biztonságos helyre.
   
   > [!WARNING]
   > Szüksége lesz rá a szolgáltatás regisztrációs kulcsával együtt, ha további eszközöket szeretne regisztrálni a StorSimple Manager szolgáltatásban. További információk a kulccsal kapcsolatban: [A StorSimple biztonsági megoldásai](../articles/storsimple/storsimple-security.md).
   > 
   > 
   
    ![StorSimple-eszköz regisztrálása, 6](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)
   
    Ha ki szeretné másolni a szöveget a soros konzol ablakából, egyszerűen jelölje ki azt. A szöveget ezután a vágólapra vagy bármilyen szövegszerkesztőbe beillesztheti. NE használja a Ctrl + C billentyűparancsot a szolgáltatás adattitkosítási kulcsának másolásához. A Ctrl + C billentyűkombinációval kilép a varázslóból. Ennek eredményeképpen az eszköz rendszergazdai jelszava és a StorSimple Snapshot Manager jelszava nem változik meg, és az eszköz visszaállítja az alapértelmezett jelszót.
5. Lépjen ki a soros konzolból.
6. Jelentkezzen be a klasszikus Azure portálra, és hajtsa végre a következő lépéseket:
   
   1. Kattintson duplán a StorSimple Manager szolgáltatásra a **Gyors üzembe helyezés** lap megnyitásához.
   2. Kattintson a **Csatlakoztatott eszközök megtekintése** lehetőségre.
   3. Az **Eszközök** lapon tekintse meg az eszközök állapotát, és ellenőrizze, hogy az eszköz sikeresen csatlakozott-e a szolgáltatáshoz. Az eszköznek **Online** állapotúnak kell lennie. Ha az eszköz állapota **Offline**, akkor várjon néhány percet, amíg az eszköz online állapotú nem lesz.
   
   ![A StorSimple Eszközök lapja](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png) 
   
   > [!IMPORTANT]
   > Ha az eszköz online állapotban van, csatlakoztassa újra a lépés elején kihúzott hálózati kábeleket.
   > 
   > 

Ha az eszköz regisztrálása sikeres volt, viszont mégsem kerül online állapotba, akkor a `Test-HcsmConnection -Verbose` futtatásával ellenőrizheti a hálózat csatlakozási állapotát. A parancsmag használatának részletes leírása a [Test-HcsmConnection parancsmag használatának ismertetésében](https://technet.microsoft.com/library/dn715782.aspx) található.

![Videó elérhető](./media/storsimple-configure-and-register-device/Video_icon.png)**Videó elérhető**

Az eszköz StorSimple szolgáltatásban való, a Windows PowerShell segítségével történő konfigurálását és regisztrálását bemutató videó megtekintéséhez kattintson [ide](https://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/).

