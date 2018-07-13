
1. A Visual Studio Megoldáskezelőben kattintson a jobb gombbal a Windows Store-projektet. Válassza ki **Store** > **a Store-alkalmazás társításához**.

    ![Alkalmazás társítása a Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. A varázslóban válassza a **tovább**. Majd jelentkezzen be Microsoft-fiókjával. A **foglaljon le egy új nevet az alkalmazáshoz**, írja be az alkalmazás nevét, és válassza **tartalék**.
3. Az alkalmazásregisztráció sikeres létrehozása után válassza ki az új alkalmazás-neve. Válassza ki **tovább**, majd válassza ki **társítása**. Ez a folyamat hozzáadja a szükséges Windows Store regisztrációs adatokat az alkalmazásjegyzékhez.
4. Ismételje meg a Windows Phone Store-alkalmazás projekt 1. és 3 ugyanazt a korábban létrehozott regisztrációt a Windows Store-alkalmazás használatával.  
5. Nyissa meg a [Windows Dev Center](https://dev.windows.com/en-us/overview), majd jelentkezzen be Microsoft-fiókjával. A **saját alkalmazások**, válassza ki az új alkalmazás regisztrálása. Ezután bontsa ki a **szolgáltatások** > **leküldéses értesítések**.
6. Az a **leküldéses értesítések** lap **Windows leküldéses értesítési szolgáltatása (WNS) és a Microsoft Azure Mobile Apps**válassza **Live Services webhely**.  Jegyezze fel az értékét a **csomag biztonsági azonosítója** és a *aktuális* értékét **Application Secret**. 

    ![Alkalmazás-beállítás, a fejlesztői központban](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Az alkalmazáskulcs és a csomag biztonsági azonosítója fontos biztonsági hitelesítő adatok. Nem bárkivel megoszthatja ezeket az értékeket, vagy ossza ki őket az App-alkalmazással.
   >
   >
