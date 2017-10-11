
1. A Visual Studio Solution Explorerben kattintson a jobb gombbal a Windows Áruházbeli alkalmazás projektjére, majd kattintson **tároló** > **az áruház alkalmazás társítása**.

    ![Windows Áruházbeli alkalmazás társítása](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. A varázslóban kattintson **következő**, és jelentkezzen be Microsoft-fiókjával. Adjon meg egy nevet az alkalmazáshoz a **lefoglalni egy új alkalmazásnév**, és kattintson a **tartalék**.
3. Az alkalmazás-regisztráció sikeres létrehozása után válassza ki az új alkalmazás nevére, kattintson a **következő**, és kattintson a **társítása**. Ezzel hozzáadja a szükséges Windows Áruházbeli regisztrációs adatokat az alkalmazásjegyzékhez.
4. A Windows Áruházbeli alkalmazás használatával korábban létrehozott regisztrálás ismételje meg a Windows Phone Áruházbeli alkalmazás projektjére 1. és 3.  
5. Keresse meg a [Windows fejlesztői központ](https://dev.windows.com/en-us/overview), és jelentkezzen be Microsoft-fiókjával. Kattintson az új alkalmazás regisztrációs **alkalmazásaimat**, majd bontsa ki a **szolgáltatások** > **leküldéses értesítések**.
6. Az a **leküldéses értesítések** kattintson **Live Services webhely** alatt **Windows leküldéses értesítési szolgáltatások (WNS) és a Microsoft Azure Mobile Apps**. Jegyezze fel a közül a **CSOMAGAZONOSÍTÓT** és a *aktuális* értéket **Alkalmazáskulcsot**. 

    ![A fejlesztői központban Alkalmazásbeállítás](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Az alkalmazáskulcs és a csomag biztonsági azonosítója fontos biztonsági hitelesítő adatok. Ezeket az értékeket ne ossza meg senkivel, és ne terjessze az alkalmazással.
   >
   >
