1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com), majd kattintson az **+ÚJ** gombra a képernyő alján.
2. Kattintson az **App Services**, majd a **Mobile Engagement**, végül a **Létrehozás** gombra.
   
       ![](./media/mobile-engagement-create-app-in-portal/create-mobile-engagement-app.png)
3. A megjelenő felugró ablakban adja meg az alábbi adatokat:
   
       ![](./media/mobile-engagement-create-app-in-portal/create-azme-popup.png)
   
   * **Alkalmazás neve**: az alkalmazás neve. 
   * **Platform**: az alkalmazás célplatformja. Minden egyes megcélzott platform esetében létre kell hoznia egy Mobile Engagement-alkalmazást. 
   * **Alkalmazás-erőfforás neve**: a név, amelyen az alkalmazás elérhető lesz az API-ken és URL-címeken keresztül. 
   * **Hely**: a régió/adatközpont, ahol az alkalmazás és alkalmazásgyűjtemény tárolva lesz.
   * **Gyűjtemény**: válasszon ki egy előzőleg létrehozott gyűjteményt, vagy válassza az „Új gyűjtemény” lehetőséget.
   * **Gyűjtemény neve**: az alkalmazáscsoportot jelöli. Arról is gondoskodik, hogy az összes alkalmazás egy csoportban legyen, ami lehetővé teszi a mérőszámok összesített számításait. Itt érdemes a vállalat vagy a részleg nevét használni, ha van.
4. Válassza ki az imént létrehozott alkalmazást az **Alkalmazások** lapon.
5. Kattintson a **KAPCSOLATINFORMÁCIÓ** gombra azon kapcsolatbeállítások megjelenítéséhez, amelyeket a mobilalkalmazás SDK-integrációjában meg szeretne adni.
6. Másolja ki a **KAPCSOLATI karakterláncot**. Ezzel azonosíthatja majd az alkalmazást az alkalmazáskódban, és ezzel csatlakozhat a Mobile Engagement szolgáltatáshoz az alkalmazásból.
   
       ![](./media/mobile-engagement-create-app-in-portal/app-connection-info-page.png)



<!--HONumber=Jan17_HO3-->


