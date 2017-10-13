<!--author=alkohli last changed: 02/22/2016-->


### <a name="to-configure-and-register-the-device"></a>Az eszköz konfigurálása és regisztrálása
1. Nyissa meg a Windows PowerShell felületet a StorSimple-eszköz soros konzoljában. Az erre vonatkozó utasítások [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](#use-putty-to-connect-to-the-device-serial-console) című szakaszban találhatók. **Pontosan kövesse az ismertetett eljárást, különben nem fog tudni hozzáférni a konzolhoz.**
2. A megnyitott munkamenetben nyomja le egyszer az Enter billentyűt a parancssor megjelenítéséhez. 
3. Ekkor a rendszer arra kéri, hogy válassza ki az eszközhöz beállítani kívánt nyelvet. Válassza ki a nyelvet, majd nyomja le az Enter billentyűt. 
   
    ![StorSimple-eszköz konfigurálása és regisztrálása, 1](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice1-U1-include.png)
4. A soros konzol megjelenő menüjében válassza az 1. lehetőséget a teljes körű hozzáféréssel való bejelentkezéshez. 
   
    ![StorSimple-eszköz regisztrálása, 2](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice2_U1-include.png)
   
     Végezze le az 5–12. lépést az eszköz minimálisan szükséges hálózati beállításainak konfigurálásához. **Ezeket a konfigurálási lépéseket az eszköz aktív vezérlőjén kell elvégezni.** A vezérlő állapota a soros konzol menüjének címsorában látható. Ha nincs kapcsolat az aktív vezérlővel, akkor bontsa a kapcsolatot, és csatlakozzon újra az aktív vezérlőhöz.
5. Írja be a jelszót a parancssorba. Az eszköz alapértelmezett jelszava: **Password1**.
6. Írja be a következő parancsot: `Invoke-HcsSetupWizard`. 
7. Ekkor megjelenik egy telepítővarázsló az eszköz hálózati beállításainak konfigurálásához. Adja meg az alábbi adatokat: 
   
   * A DATA 0 hálózati adapter IP-címe
   * Alhálózati maszk
   * Átjáró
   * Az elsődleges DNS-kiszolgáló IP-címe
     
        Vegye figyelembe, hogy a rendszer a folyamat minden lépése után érvényesíti a hálózati beállításokat.
     
     > [!NOTE]
     > Az alhálózati maszk és a DNS-beállítások alkalmazása néhány percig is eltarthat. Ha az alábbi hibaüzenetet kapja: „Ellenőrizze a Data 0 hálózati kapcsolatát”, akkor ellenőrizze a fizikai hálózati kapcsolatot az aktív vezérlő DATA 0 hálózati adapterén.
     > 
     > 
8. (Nem kötelező) konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, **vegye figyelembe, hogy ha webproxyt használ, azt csak itt tudja beállítani**. További információ: [Configure web proxy for your device](../articles/storsimple/storsimple-configure-web-proxy.md) (Webproxy beállítása az eszközhöz).
9. Állítson be egy elsődleges NTP-kiszolgálót az eszközhöz. Az NTP-kiszolgálókra azért van szükség, mert az eszköznek szinkronizálnia kell az időt ahhoz, hogy a felhőszolgáltatókkal hitelesítést végezhessen. Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat. Ha ez nem lehetséges, akkor adjon meg egy belső NTP-kiszolgálót. 
10. Biztonsági okokból az eszköz rendszergazdai jelszava az első munkamenet végeztével lejár, így ezt most meg kell változtatnia. Amikor a rendszer erre kéri, adjon meg az eszközhöz egy rendszergazdai jelszót. Az eszköz rendszergazdai jelszavának 8–15 karakter hosszúságúnak kell lennie. A jelszónak az alábbiak közül hármat tartalmaznia kell: kisbetűk, nagybetűk, számok és speciális karakterek.
    
    <br/>![StorSimple-eszköz regisztrálása, 5](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice5_U1-include.png)
11. A telepítővarázsló utolsó lépésként regisztrálja az eszközt a StorSimple Manager szolgáltatásban. Ehhez szükség van a 2. lépésben kapott szolgáltatásregisztrációs kulcsra. Előfordulhat, hogy a regisztrációs kulcs megadása után 2–3 percet várni kell az eszköz regisztrálására.
    
    > [!NOTE]
    > A Ctrl + C billentyűkombinációval bármikor kiléphet a varázslóból. Ha minden hálózati beállítást megadott (a Data 0 IP-címe, alhálózati maszk és átjáró), akkor a bejegyzéseket megőrzi a rendszer.
    > 
    > 
    
    ![StorSimple-eszköz regisztrálása, 6](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice6_U1-include.png)
12. Az eszköz regisztrálása után megjelenik egy szolgáltatásadat-titkosítási kulcs. Másolja ki ezt a kulcsot, és mentse egy biztonságos helyre. **Szüksége lesz erre a kulcsra a szolgáltatás regisztrációs kulcsával együtt, ha további eszközöket szeretne regisztrálni a StorSimple Manager szolgáltatásban.** További információk a kulccsal kapcsolatban: [A StorSimple biztonsági megoldásai](../articles/storsimple/storsimple-security.md).
    
    ![StorSimple-eszköz regisztrálása, 7](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice7_U1-include.png)    
    
    > [!NOTE]
    > Ha ki szeretné másolni a szöveget a soros konzol ablakából, egyszerűen jelölje ki azt. A szöveget ezután a vágólapra vagy bármilyen szövegszerkesztőbe beillesztheti. NE használja a Ctrl + C billentyűparancsot a szolgáltatás adattitkosítási kulcsának másolásához. A Ctrl + C billentyűkombinációval kilép a varázslóból. Ennek eredményeképpen az eszköz rendszergazdai jelszava nem változik meg, és az eszköz visszaállítja az alapértelmezett jelszót.
    > 
    > 
13. Lépjen ki a soros konzolból.
14. Jelentkezzen be a klasszikus Azure portálra, és hajtsa végre a következő lépéseket:
    
    1. Kattintson duplán a StorSimple Manager szolgáltatásra a **Gyors üzembe helyezés** lap megnyitásához.
    2. Kattintson a **Csatlakoztatott eszközök megtekintése** lehetőségre.
    3. Az **Eszközök** lapon tekintse meg az eszközök állapotát, és ellenőrizze, hogy az eszköz sikeresen csatlakozott-e a szolgáltatáshoz. Az eszköznek **Online** állapotúnak kell lennie.
       
        ![A StorSimple Eszközök lapja](./media/storsimple-configure-and-register-device-u1/HCS_DevicesPageM_U1-include.png) 
       
        Ha az eszköz állapota **Offline**, akkor várjon néhány percet, amíg az eszköz online állapotú nem lesz. 
       
        Ha az eszköz néhány perc eltelte után is offline állapotú, akkor ellenőrizze, hogy a tűzfallal védett hálózat [a StorSimple-eszköz hálózati követelményeiben](../articles/storsimple/storsimple-system-requirements.md) leírtak szerint van-e beállítva. 
       
        Ellenőrizze, hogy a 9354-es port kimenő kommunikációra nyitva van-e, mivel az a StorSimple Manager szolgáltatás és eszköz közötti kommunikációt lebonyolító Service Bus működéséhez szükséges.

