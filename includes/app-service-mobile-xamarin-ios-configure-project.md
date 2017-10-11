#### <a name="configure-the-ios-project-in-xamarin-studio"></a>A Xamarin Studióban az iOS-projekt konfigurálása
1. Xamarin.Studio, nyissa meg **Info.plist**, és frissítse a **Bundle Identifier** az új alkalmazást. a korábban létrehozott csomagot-azonosítóval

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Görgessen le a **Háttérmódok**. Válassza ki a **Háttérmódok engedélyezése** bezárásához és a **távoli értesítések** mezőbe.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Kattintson duplán a projektet, nyissa meg a megoldást panelen **Projektbeállítások**.
4. A **Build**, válassza a **iOS alkalmazáscsomag aláírása**, és válassza ki a megfelelő identitás létesítési profil és akkor imént beállított fel ebben a projektben.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Ez biztosítja, hogy használja-e a projektet az új profil tartozó kód aláírása. A hivatalos Xamarin eszköz-üzembehelyezési dokumentáció, lásd: [Xamarin eszköz kiépítése].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Az iOS-projektre a Visual Studio konfigurálása
1. A Visual Studióban, kattintson jobb gombbal a projektre, és kattintson **tulajdonságok**.
2. A Tulajdonságok lapon kattintson a **iOS alkalmazás** lapot, és frissítse a **azonosító** a korábban létrehozott Azonosítóval rendelkező.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. Az a **iOS alkalmazáscsomag aláírása** lapon válassza ki a megfelelő identitás és létesítési profil imént beállította ebben a projektben.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Ez biztosítja, hogy használja-e a projektet az új profil tartozó kód aláírása. A hivatalos Xamarin eszköz-üzembehelyezési dokumentáció, lásd: [Xamarin eszköz kiépítése].
4. Kattintson duplán az Info.plist-ben nyissa meg azt, és engedélyezze a **RemoteNotifications** alatt **Háttérmódok**.

[Xamarin eszköz kiépítése]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
