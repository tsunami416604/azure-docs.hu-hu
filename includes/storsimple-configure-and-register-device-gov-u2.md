<!--author=SharS last changed: 02/22/2016-->

### <a name="to-configure-and-register-the-device"></a>Az eszköz konfigurálása és regisztrálása
1. Nyissa meg a Windows PowerShell felületet a StorSimple-eszköz soros konzoljában. Az erre vonatkozó utasítások [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](../articles/storsimple/storsimple-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console) című szakaszban találhatók. **Pontosan kövesse az ismertetett eljárást, különben nem fog tudni hozzáférni a konzolhoz.**
2. A megnyitott munkamenetben nyomja le egyszer az Enter billentyűt a parancssor megjelenítéséhez.
3. Ekkor a rendszer arra kéri, hogy válassza ki az eszközhöz beállítani kívánt nyelvet. Válassza ki a nyelvet, majd nyomja le az Enter billentyűt.
   
    ![StorSimple-eszköz konfigurálása és regisztrálása, 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. A soros konzol megjelenő menüjében válassza az 1. lehetőséget a teljes körű hozzáféréssel való bejelentkezéshez.
   
    ![StorSimple-eszköz regisztrálása, 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. A következő lépésekkel adja meg az eszköz minimális szükséges hálózati beállításait.
   
   > [!IMPORTANT]
   > Ezeket a konfigurálási lépéseket az eszköz aktív vezérlőjén kell elvégezni. A vezérlő állapota a soros konzol menüjének címsorában látható. Ha nem csatlakozik az aktív vezérlővel, válassza le, és csatlakozzon újra az aktív vezérlőhöz.
   > 
   > 
   
   1. Írja be a jelszót a parancssorba. Az eszköz alapértelmezett jelszava: **Password1**.
   2. Írja be a következő parancsot:
      
        `Invoke-HcsSetupWizard`
   3. Ekkor megjelenik egy telepítővarázsló az eszköz hálózati beállításainak konfigurálásához. Adja meg a következő információkat:
      
      * DATA 0 hálózati adapterén IP-címe
      * Alhálózati maszk
      * Átjáró
      * Az elsődleges DNS-kiszolgáló IP-címe
      * Az elsődleges NTP-kiszolgáló IP-címe
      
      > [!NOTE]
      > Előfordulhat, hogy az alhálózati maszk és a DNS-beállítások alkalmazása néhány percet vár.
      > 
      > 
   4. Igény szerint állítsa be a proxy-webkiszolgáló.
      
      > [!IMPORTANT]
      > Bár a webproxy konfigurálása nem kötelező, vegye figyelembe, hogy ha olyan webproxyt használ, csak konfigurálhatja azt itt. További információ: [Configure web proxy for your device](../articles/storsimple/storsimple-configure-web-proxy.md) (Webproxy beállítása az eszközhöz).
      > 
      > 
6. Használja a Ctrl + C billentyűket a telepítővarázslóból való kilépéshez.
7. A frissítések telepítése az alábbiak szerint:
   
   1. IP-címeket mindkét vezérlőhöz beállításához használja a következő parancsmagot:
      
      `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`
   2. A parancssorban futtassa `Get-HcsUpdateAvailability`. Meg kell értesítés frissítések érhetők el.
   3. Futtassa az `Start-HcsUpdate` parancsot. Ez a parancs futtatható bármely csomópontján. Az első tartományvezérlő alkalmazandó frissítések, a tartományvezérlő hajt végre feladatátvételt, és majd a frissítések a többi tartományvezérlőn érvényesíthetők.
      
      Futtassa a figyelheti a frissítési folyamat `Get-HcsUpdateStatus`.    
      
      Az alábbi kimeneti példa azt mutatja, hogy a frissítés még folyamatban van.
      
      ````
      Controller0>Get-HcsUpdateStatus
      RunInprogress       : True
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      ````
      
      Az alábbi kimeneti példa azt mutatja, hogy a frissítés befejeződött.
      
      ```
      Controller1>Get-HcsUpdateStatus
      
      RunInprogress       : False
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      ```
      
      Minden a frissítések alkalmazása, beleértve a Windows-frissítések 11 órát is igénybe vehet.
8. Futtassa az alábbi parancsmagot, mutasson az eszköz a Microsoft Azure Government Portal (mert alapértelmezés szerint a nyilvános klasszikus Azure portálra mutat). Ez mindkét tartományvezérlők újraindul. Azt javasoljuk, hogy egyszerre kapcsolódó mindkét vezérlőket, hogy a tartományvezérlők újraindításakor láthatóvá két PuTTY munkamenet használja.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   Egy megerősítő üzenet jelenik meg. Fogadja el az alapértelmezett (**Y**).
9. Futtassa a telepítő folytatja a következő parancsmagot:
   
    `Invoke-HcsSetupWizard`
   
    ![A telepítővarázsló folytatása](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
   Amikor a telepítő folytatja, a varázsló 2. frissítés verziója lesz.
10. Fogadja el a hálózati beállításokat. Egy érvényesítési üzenet jelenik meg, ha elfogadja, hogy egyes beállítások.
11. Biztonsági okokból az eszköz rendszergazdai jelszava az első munkamenet végeztével lejár, így ezt most meg kell változtatnia. Amikor a rendszer erre kéri, adjon meg az eszközhöz egy rendszergazdai jelszót. Az eszköz rendszergazdai jelszavának 8–15 karakter hosszúságúnak kell lennie. A jelszónak az alábbiak közül hármat tartalmaznia kell: kisbetűk, nagybetűk, számok és speciális karakterek.
    
    <br/>![StorSimple-eszköz regisztrálása, 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. A telepítővarázsló utolsó lépésként regisztrálja az eszközt a StorSimple Manager szolgáltatásban. Ehhez szüksége lesz a szolgáltatás regisztrációs kulcsának beolvasott [2. lépés: Szolgáltatásregisztrációs kulcs lekérése](../articles/storsimple/storsimple-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key). Előfordulhat, hogy a regisztrációs kulcs megadása után 2–3 percet várni kell az eszköz regisztrálására.
    
    > [!NOTE]
    > A Ctrl + C billentyűkombinációval bármikor kiléphet a varázslóból. Ha minden hálózati beállítást megadott (a Data 0 IP-címe, alhálózati maszk és átjáró), akkor a bejegyzéseket megőrzi a rendszer.
    > 
    > 
    
    ![A StorSimple regisztrációs folyamat](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. Az eszköz regisztrálása után megjelenik egy szolgáltatásadat-titkosítási kulcs. Másolja ki ezt a kulcsot, és mentse egy biztonságos helyre. **Szüksége lesz erre a kulcsra a szolgáltatás regisztrációs kulcsával együtt, ha további eszközöket szeretne regisztrálni a StorSimple Manager szolgáltatásban.** További információk a kulccsal kapcsolatban: [A StorSimple biztonsági megoldásai](../articles/storsimple/storsimple-security.md).
    
    ![StorSimple-eszköz regisztrálása, 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > Ha ki szeretné másolni a szöveget a soros konzol ablakából, egyszerűen jelölje ki azt. A szöveget ezután a vágólapra vagy bármilyen szövegszerkesztőbe beillesztheti.
    > 
    > NE használja a Ctrl + C billentyűparancsot a szolgáltatás adattitkosítási kulcsának másolásához. A Ctrl + C billentyűkombinációval kilép a varázslóból. Ennek eredményeképpen az eszköz rendszergazdai jelszava nem változik meg, és az eszköz visszaállítja az alapértelmezett jelszót.
    > 
    > 
14. Lépjen ki a soros konzolból.
15. Térjen vissza az Azure Government portálra, és kövesse az alábbi lépéseket:
    
    1. Kattintson duplán a StorSimple Manager szolgáltatásra a **Gyors üzembe helyezés** lap megnyitásához.
    2. Kattintson a **Csatlakoztatott eszközök megtekintése** lehetőségre.
    3. Az **Eszközök** lapon tekintse meg az eszközök állapotát, és ellenőrizze, hogy az eszköz sikeresen csatlakozott-e a szolgáltatáshoz. Az eszköznek **Online** állapotúnak kell lennie.
       
        ![A StorSimple Eszközök lapja](./media/storsimple-configure-and-register-device-gov-u2/HCS_DeviceOnline-gov-include.png)
       
        Ha az eszköz állapota **Offline**, akkor várjon néhány percet, amíg az eszköz online állapotú nem lesz.
       
        Ha az eszköz néhány perc eltelte után is offline állapotú, akkor ellenőrizze, hogy a tűzfallal védett hálózat [a StorSimple-eszköz hálózati követelményeiben](../articles/storsimple/storsimple-system-requirements.md) leírtak szerint van-e beállítva.
       
        Ellenőrizze, hogy a 9354-es port kimenő kommunikációra nyitva van-e, mivel az a StorSimple Manager szolgáltatás és eszköz közötti kommunikációt lebonyolító Service Bus működéséhez szükséges.

