Az oktatóanyag utolsó szakasza az új alkalmazás felépítéséből és futtatásából áll.

### Projekt betöltése az Android Studióba és szinkronizálása a Gradle-lel

1. Keresse meg a helyet, ahová a tömörített projektfájlokat mentette, és tömörítse ki a fájlokat a számítógépre az Android Studio projekteket tartalmazó mappába.

2. Nyissa meg az Android Studiót. Ha megjelenik egy projekt, amin dolgozik, zárja be azt (File => Close Project (Fájl => Projekt bezárása)).

3. Válassza az **Open an existing Android Studio project** (Létező Android Studio projekt megnyitása) lehetőséget, tallózzon a projekt helyére, majd kattintson az **OK** gombra. Ekkor megnyílik a projekt és megkezdődik a szinkronizálás a Gradle-lel.

    ![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. Várjon, amíg a Gradle-szinkronizálás befejeződik. Ha „failed to find target” (a cél nem található) hiba jelenik meg, az azt jelenti, hogy az Android Studióban használt verzió nem egyezik meg a minta verziójával. Ezt a legegyszerűbben úgy lehet kijavítani, ha az **Install missing platform(s) and sync project** (Hiányzó platform(ok) telepítése és a projekt szinkronizálása) hivatkozásra kattint a hibaüzenetben. Ekkor további verzióhibák jelenhetnek meg. Ismételje meg a fenti lépést, amíg az összes hiba meg nem oldódik.
    - Másik módja is van az ilyen hibák kijavításának, ha az Android „legújabb és legjobb” verzióját szeretné használni. Frissítheti a **targetSdkVersion** tulajdonságot az *app* mappában található *build.gradle* fájlban úgy, hogy egyezzen a számítógépen már telepített verzióval. Utóbbit úgy azonosíthatja, ha az **SDK Manager** ikonra kattint, és megnézi, melyik verzió van listázva. Ezután kattintson a **Sync Project with Gradle Files** (Projekt szinkronizálása a Gradle-fájlokkal) lehetőségre. Ekkor hibaüzenetet kaphat a Build Tools verziójával kapcsolatban – ezt a már ismert módon javítsa ki.

### Az alkalmazás futtatása

Az alkalmazást futtathatja emulátorral, vagy tényleges eszközön.

1. Az eszközön való futtatáshoz csatlakoztassa azt egy USB-kábellel. [Az eszközt be kell állítani fejlesztéshez](https://developer.android.com/training/basics/firstapp/running-app.html). Ha Windows-gépen fejleszt, emellett töltsön le és telepítsen egy USB-illesztőprogramot is.

2. Az Android-emulátorban való futtatáshoz meg kell határoznia legalább egy Android virtuális eszközt (AVD). Kattintson az AVD Manager ikonra az eszközök létrehozásához és kezeléséhez.

3. A projekt elindításához a **Run** (Futtatás) menüben kattintson a **Run** (Futtatás) gombra. Ezután válasszon egy eszközt vagy emulátort a megjelenő párbeszédablakban.

4. Miután megjelent az alkalmazás, írjon be egy értelmes szöveget, például _Az oktatóanyag befejezése_, majd kattintson az **Add** (Hozzáadás) gombra.

    ![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

    Ez egy POST kérést küld az Azure-ban futtatott új mobilszolgáltatásnak. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A mobilszolgáltatás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.

    > [AZURE.NOTE] A mobilszolgáltatáshoz az adatok lekérdezése és beszúrása céljából hozzáférő kódot át is tekintheti a ToDoActivity.java fájlban.

8. A klasszikus Azure portálra visszatérve kattintson a **Data** (Adatok) fülre, majd a **TodoItems** táblára.

    ![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

    Így tallózással kiválaszthatja az alkalmazás által a táblába beszúrt adatokat.

    ![](./media/mobile-services-android-get-started/mobile-data-browse.png)



<!--HONumber=Jun16_HO2-->


