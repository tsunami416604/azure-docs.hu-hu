
1. Kattintson az **App Services** elemre > válassza ki a Mobile Apps-háttéralkalmazást > kattintson a **Gyorsindítás** elemre > kattintson az ügyfélplatformra (iOS, Android, Xamarin, Cordova).

![Azure Portal a kiemelt Mobile Apps Quickstarttal][quickstart]

2. Ha nincs konfigurálva kapcsolat az adatbázissal, létre kell hoznia egy adatkapcsolatot.

![Azure Portal Mobile Appsszel – csatlakozás a BD-hez][connect]

  * Hozzon létre egy új SQL-adatbázist és -kiszolgálót.

  ![Azure Portal Mobile Appsszel – új BD és kiszolgáló létrehozása][server]

  * Várjon az adatkapcsolat sikeres létrehozásáig.

  ![Azure Portal Mobile Appsszel – értesítés az adatkapcsolat létrehozásakor][notification]

  * Az adatkapcsolatnak sikeresnek kell lennie.

  ![Azure Portal Mobile Appsszel – értesítés az adatkapcsolat létrehozásakor][already-connection]

3. A **2. Tábla API létrehozása** elemnél a **Háttérrendszer nyelveként** válassza a Node.js-t. Fogadja el a nyugtázást, majd kattintson a **TodoItem tábla létrehozása** elemre. Ez létrehoz egy új *TodoItem* táblát az adatbázisban. Ne feledje, hogy a létező háttérrendszerek Node.js nyelvűre történő átállítása minden tartalmat felülír. Ha inkább egy .NET-hátteret szeretne létrehozni, [kövesse ezeket az utasításokat][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
