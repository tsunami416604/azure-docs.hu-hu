

Kövesse az alábbi lépéseket egy új mobilszolgáltatás létrehozásához.

1.  Jelentkezzen be a [klasszikus Azure portálon](https://manage.windowsazure.com/). A navigációs ablaktábla alján kattintson a **+NEW** (+ÚJ) gombra. Bontsa ki a **Compute** (Számítás) és a **Mobile Service** (Mobilszolgáltatás) csomópontokat, majd kattintson a **Create** (Létrehozás) pontra.

    ![](./media/mobile-services-create-new-service/mobile-create.png)

    Ekkor megjelenik a **Create a Mobile Service** (Mobilszolgáltatás létrehozása) párbeszédablak.

2.  A **Create a Mobile Service** (Mobilszolgáltatás létrehozása) párbeszédablakban válassza az **Create a free 20 MB SQL Database** (Ingyenes 20 MB-os SQL-adatbázis létrehozása) lehetőséget, majd a **JavaScript** futtatókörnyezetet, és az **URL** mezőben adjon meg egy nevet az új mobilszolgáltatás altartományához. Kattintson a jobbra mutató nyílra a következő oldalra lépéshez.

    ![](./media/mobile-services-create-new-service/mobile-create-page1.png)

    Ekkor megjelenik az **Specify database settings** (Adatbázis beállításainak megadása) oldal.
    
    >[AZURE.NOTE]Ebben az oktatóprogramban létre fog hozni egy új SQL Database-példányt és -kiszolgálót. Ezt az új adatbázist újra használhatja, és felügyelheti úgy, mint akármelyik SQL Database-példányt. Ha már rendelkezik adatbázissal az új mobilszolgáltatással megegyező régióban, választhatja a **Use existing Database** (Létező adatbázis használata) lehetőséget, miután kiválaszthatja azt. Más régióban található adatbázis használata nem ajánlott, mert ez további sávszélességi költségekkel és nagyobb késleltetéssel jár.

3.  A **Name** (Név) mezőben adja meg az új adatbázis nevét, adja meg a bejelentkezési nevet (**Login name**), ez a rendszergazda bejelentkezési neve az új SQL Database-kiszolgálón, erősítse meg a jelszót, végül kattintson a pipa gombra a folyamat befejezéséhez.
    ![](./media/mobile-services-create-new-service/mobile-create-page2.png)

Ezzel létrehozott egy új mobilszolgáltatást, amelyet használhat a mobilalkalmazásaival.




<!--HONumber=Jun16_HO2-->


