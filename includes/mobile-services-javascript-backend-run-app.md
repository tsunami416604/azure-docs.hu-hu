
Az oktatóanyag utolsó szakasza az új alkalmazás felépítéséből és futtatásából áll.

1. Keresse meg a helyet, ahová a tömörített projektfájlokat mentette, tömörítse ki a fájlokat a számítógépre, és nyissa meg a megoldásfájlt a Visual Studióban.

2. Nyomja le az **F5** billentyűt a projekt újraépítéséhez és az alkalmazás indításához.

3. Az alkalmazásban írjon be egy jelentéssel bíró szöveget az **Insert a TodoItem** (Tennivaló beszúrása) mezőbe, például *Az oktatóanyag befejezése*, majd kattintson a **Save** (Mentés) gombra.

    Ez egy POST kérést küld az Azure-ban futtatott új mobilszolgáltatásnak. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A mobilszolgáltatás visszaadja a táblában tárolt elemeket, amelyek az alkalmazás második oszlopában jelennek meg.

4. (Nem kötelező) Egy univerzális Windows-megoldásban cserélje le az alapértelmezett kezdő projektet a másik alkalmazásra, és indítsa el újra az alkalmazást.

    Figyelje meg, hogy az előző lépésben mentett adatok betöltődnek a mobilszolgáltatásból az alkalmazás indítása után.
 
4. A [klasszikus Azure portálra](https://manage.windowsazure.com/) visszatérve kattintson a **Data** (Adatok) fülre, majd a **TodoItems** táblára.

    Így tallózással kiválaszthatja az alkalmazás által a táblába beszúrt adatokat.

    ![](./media/mobile-services-javascript-backend-run-app/mobile-data-browse.png)


<!--HONumber=Jun16_HO2-->


