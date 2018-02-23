
1. A Visual Studio Solution Explorerben kattintson a jobb gombbal a Windows Áruházbeli alkalmazás projektjére. Válassza ki **tároló** > **az áruház alkalmazás társítása**.

    ![Windows Áruházbeli alkalmazás társítása](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. A varázslóban válassza **következő**. Majd jelentkezzen be Microsoft-fiókjával. A **lefoglalni egy új alkalmazásnév**, írja be az alkalmazás nevét, majd válassza ki **tartalék**.
3. Az alkalmazás-regisztráció sikeres létrehozása után válassza ki az új alkalmazás nevét. Válassza ki **következő**, majd válassza ki **társítása**. Ez a folyamat hozzáadja a szükséges Windows Áruházbeli regisztrációs adatokat az alkalmazás jegyzékében.
4. A Windows Áruházbeli alkalmazás használatával korábban létrehozott regisztrálás ismételje meg a Windows Phone Áruházbeli alkalmazás projektjére 1. és 3.  
5. Lépjen a [Windows fejlesztői központ](https://dev.windows.com/en-us/overview), majd jelentkezzen be Microsoft-fiókjával. A **alkalmazásaimat**, válassza ki az új alkalmazás regisztrációt. Majd **szolgáltatások** > **leküldéses értesítések**.
6. Az a **leküldéses értesítések** lap **Windows leküldéses értesítési szolgáltatások (WNS) és a Microsoft Azure Mobile Apps**, jelölje be **Live Services webhely**.  Jegyezze fel a közül a **CSOMAGAZONOSÍTÓT** és a *aktuális* értéket **Alkalmazáskulcsot**. 

    ![A fejlesztői központban Alkalmazásbeállítás](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Az alkalmazáskulcs és a csomag biztonsági azonosítója fontos biztonsági hitelesítő adatok. Ne ossza meg senkivel ezeket az értékeket, vagy ossza ki őket az alkalmazással.
   >
   >
