
1. Kattintson az **App Services** gombra, válassza a Mobile Apps-háttéralkalmazást, majd a **Gyorsindítás** lehetőséget, végül pedig válassza ki az ügyfélplatformot (iOS, Android, Xamarin, Cordova).

    ![Azure Portal a kiemelt Mobile Apps Quickstarttal][quickstart]

2. Ha nincs konfigurált adatbázis-kapcsolat, hozzon létre egyet a következő módon:

    ![Azure Portal Mobile Appsszel – csatlakozás adatbázishoz][connect]

    a. Hozzon létre egy új SQL-adatbázist és -kiszolgálót.

    ![Azure Portal Mobile Appsszel – új adatbázis és kiszolgáló létrehozása][server]

    b. Várjon az adatkapcsolat sikeres létrehozásáig.

    ![Az Azure Portal értesítése az adatkapcsolat sikeres létrejöttéről][notification]

    c. Az adatkapcsolatnak sikeresnek kell lennie.

    ![Az Azure Portal értesítése: „Már van adatkapcsolata”][already-connection]

3. A **2. Tábla API létrehozása** elemnél a **Háttérrendszer nyelveként** válassza a Node.js-t. 
 
4. Fogadja el a nyugtázást, majd válassza a **TodoItem tábla létrehozása** elemet.  
    Ez a művelet létrehoz egy új teendőtáblát az adatbázisban. 

    >[!IMPORTANT]
    > A létező háttérrendszerek Node.js nyelvűre történő átállítása minden tartalmat felülír. Ehelyett egy .NET-háttérrendszer létrehozásához lásd: [A Mobile Appshez készült .NET háttérkiszolgálói SDK használata][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
