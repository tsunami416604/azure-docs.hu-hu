

Ha regisztrálni szeretné az alkalmazást leküldéses értesítésekhez az Apple Push Notification szolgáltatásban (APNS), létre kell hoznia a projekthez egy új leküldéses tanúsítványt, egy alkalmazásazonosítót, és egy üzembe helyezési profilt az Apple fejlesztői portálján. Az alkalmazásazonosító tartalmazni fogja a konfigurációs beállításokat, amelyek lehetővé teszik az alkalmazás számára, hogy leküldéses értesítéseket küldjön és fogadjon. Ezek a beállítások tartalmazni fogják azt a leküldéses értesítési tanúsítványt, amelyre a leküldéses értesítések küldésekor és fogadásakor van szükség az Apple Push Notification szolgáltatással (APNS) való hitelesítéshez. További információért lásd az [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584) (Apple Push Notification szolgáltatás) hivatalos dokumentációját.

#### <a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>A tanúsítvány-aláírási kérelem fájljának létrehozása a leküldéses tanúsítványhoz
Ezek a lépések végigvezetik a tanúsítvány-aláírási kérelem létrehozásán. Ez alapján a rendszer létrehoz egy leküldéses tanúsítványt az APNS-sel való használatra.

1. Futtassa a Kulcskarika-elérés eszközt Mac számítógépén. Ez a **Segédprogramok** vagy az **Egyéb** mappából nyitható meg a Launchpadről.
2. Kattintson a **Kulcskarika-elérés ** menüpontra, bontsa ki a **Tanúsítványasszisztens** almenü, majd kattintson a **Tanúsítvány igénylése egy tanúsítványkiadó központból...** lehetőségre.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Töltse ki a **Felhasználói e-mail-cím** és az **Általános név** mezőket, ügyeljen arra, hogy a **Mentve lemezre** lehetőség legyen kiválasztva, majd kattintson a **Folytatás** gombra. Hagyja üresen a **CA e-mail-cím** mezőt, ennek kitöltése nem kötelező.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. Adjon meg egy nevet a tanúsítvány-aláírási kérelem (CSR) fájljához a **Mentés mint** mezőben, válassza ki a helyet a **Hely** mezőben, majd kattintson a **Mentés** gombra.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      Ez menti a CSR-fájlt a megadott helyre; az alapértelmezett hely az Íróasztal. Jegyezze meg a fájlhoz választott helyet.

#### <a name="register-your-app-for-push-notifications"></a>Alkalmazás regisztrálása leküldéses értesítésekhez
Hozzon létre új explicit alkalmazásazonosítót az alkalmazásához az Apple-lel, és konfigurálja azt leküldéses értesítésekhez.  

1. Lépjen az [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456) (iOS üzembe helyezési portál) felületére az Apple Developer Center központban, jelentkezzen be Apple ID-jával, kattintson az **Identifiers** (Azonosítók), majd az **App IDs** (Alkalmazásazonosítók) lehetőségre, végül kattintson a **+** jelre az új alkalmazás regisztrálásához.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)
2. Frissítse a következő három mezőt az új alkalmazásában, és kattintson a **Continue** (Folytatás) gombra:
   
   * **Name** (Név): Adjon meg egy leíró nevet az alkalmazáshoz a **Name** (Név) mezőben az **App ID Description** (Alkalmazásazonosító leírása) szakaszban.
   * **Bundle Identifier** (Csomagazonosító): Az **Explicit App ID** (Explicit alkalmazásazonosító) szakasz alatt adjon meg egy csomagazonosítót (**Bundle Identifier**) abban a formában, `<Organization Identifier>.<Product Name>` ahogy azt az [App Distribution Guide](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8) (Alkalmazásterjesztési útmutató) írja. Ennek egyeznie kell azzal, amelyet az Xcode, Xamarin, vagy Cordova projektben használ az alkalmazáshoz.
   * **Push Notifications** (Leküldéses értesítések): Jelölje be a **Push Notifications** (Leküldéses értesítések) beállítást az **App Services** (Alkalmazásszolgáltatások) szakaszban.
     
     ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
3. A Confirm your App ID (Alkalmazásazonosító jóváhagyása) képernyőn tekintse át a beállításokat, majd ha meggyőződött a helyességükről, kattintson a **Submit** (Elküldés) gombra.
4. Miután elküldte az új alkalmazásazonosítót, megjelenik a **Registration complete** (Regisztráció kész) képernyő. Kattintson a **Done** (Kész) gombra.
5. Keresse meg az előbb létrehozott alkalmazásazonosítót a fejlesztői központban az alkalmazásazonosítók alatt, és kattintson az azt tartalmazó sorra. Ha az alkalmazásazonosító sorra kattint, megjelennek az alkalmazás részletei. Kattintson az alul található **Edit** (Szerkesztés) gombra.
6. Görgessen a képernyő aljára, és kattintson a **Create Certificate...** (Tanúsítvány létrehozása...) gombra a **Development Push SSL Certificate** (Leküldéses fejlesztési SSL-tanúsítvány) szakasz alatt.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
       This will display the "Add iOS Certificate" assistant.
   
   > [!NOTE]
   > Ez az oktatóprogram fejlesztési tanúsítványt használ. Ugyanez a folyamat használatos a termelési tanúsítvány regisztrálásához is. Csak arra ügyeljen, hogy ugyanazt a tanúsítványtípust használja az értesítések küldéséhez.
   > 
   > 
7. Kattintson a **Choose File** (Fájl kiválasztása) lehetőségre, majd keresse meg a helyet, ahová mentette a leküldéses tanúsítványhoz létrehozott CSR-fájlt. Ezután kattintson a **Generate** (Létrehozás) gombra.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
8. Miután a portál létrehozza a tanúsítványt, kattintson a **Download** (Letöltés) gombra.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
       This downloads the signing certificate and saves it to your computer in your Downloads folder.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > Alapértelmezés szerint a letöltött fejlesztési tanúsítványfájl neve **aps_development.cer**.
   > 
   > 
9. Kattintson duplán a letöltött **aps_development.cer** leküldéses tanúsítványra. Ez telepíti az új tanúsítványt a kulcsláncba, az alábbiak szerint:
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > A tanúsítványban található név lehet különböző, de rendelkezni fog az **Apple Development iOS Push Services:** (Apple fejlesztési iOS leküldéses szolgáltatások:) előtagjával.
   > 
   > 
10. A kulcslánc-hozzáférési oldalon a Ctrl billentyű lenyomása mellett kattintson az új leküldéses tanúsítványra, amelyet a **Certificates** (Tanúsítványok) kategóriában létrehozott. Kattintson az **Export** (Exportálás) elemre, válassza a **.p12** formátumot, majd kattintson a **Save** (Mentés) gombra.
    
    Jegyezze meg az exportált .p12 leküldéses tanúsítvány fájlnevét és helyét. Ezek az APNS-sel való hitelesítéshez szükségesek, és fel kell töltenie őket a klasszikus Azure portálra.

#### <a name="create-a-provisioning-profile-for-the-app"></a>Üzembe helyezési profil létrehozása az alkalmazáshoz
1. Térjen vissza az <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> felületére, válassza a **Provisioning Profiles** (Üzembe helyezési profilok), majd az **All** (Összes) lehetőséget, és kattintson a **+** gombra az új profil létrehozásához. Ekkor elindul az **Add iOS Provisiong Profile** (iOS üzembe helyezési profil hozzáadása) varázsló
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. Az üzembe helyezési profil típusaként válassza az **iOS App Development** (iOS-alkalmazásfejlesztés) lehetőséget a **Development** (Fejlesztés) alatt, majd kattintson a **Continue** (Folytatás) gombra.
3. Ezután válassza ki az imént létrehozott azonosítót az **App ID** (Alkalmazásazonosító) legördülő listából, és kattintson a **Continue** (Folytatás) gombra
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. A **Select certificates** (Tanúsítványok kiválasztása) képernyőn válassza ki a kódaláíráshoz használt fejlesztési tanúsítványt, majd kattintson a **Continue** (Folytatás) gombra. Ez a nem a fentiekben létrehozott leküldéses tanúsítvány, hanem egy aláíró tanúsítvány.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. Ezután válassza ki a teszteléshez használt eszközöket a **Devices** (Eszközök) mezőben, és kattintson a **Continue** (Folytatás) gombra
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. Végül adjon meg egy nevet a profilnak a **Profile Name** (Profilnév) mezőben, és kattintson a **Generate** (Létrehozás) gombra.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)



<!--HONumber=Feb17_HO1-->


