
A letölthető mobilszolgáltatás-projekt lehetővé teszi az új mobilszolgáltatás futtatását a helyi számítógépén vagy virtuális gépén. Így egyszerűen kijavíthatja a szolgáltatáskód hibáit, mielőtt közzétenné az Azure-on.

Ebben a szakaszban tesztelni fogja az új alkalmazást a helyileg futó mobilszolgáltatáson.

1. Keresse meg a helyet, ahová a tömörített projektfájlokat mentette, tömörítse ki a fájlokat a számítógépre, és nyissa meg a megoldásfájlt a Visual Studióban.

2. A Visual Studio Solution Explorerben (Megoldáskezelőben) kattintson a jobb egérgombbal szolgáltatásprojektre, kattintson a **Set as StartUp Project** (Beállítás indítási projektként) lehetőségre, majd nyomja le az **F5** billentyűt a projekt felépítéséhez és a mobilszolgáltatás helyi indításához.

    ![](./media/mobile-services-dotnet-backend-test-local-service-dotnet/mobile-service-startup.png)

    Miután a mobilszolgáltatás sikeresen elindul, megjelenik egy weboldal.

3. Az áruházbeli alkalmazás teszteléséhez kattintson a jobb egérgombbal az ügyfélalkalmazás-projektjére, kattintson a **Set as StartUp Project** (Beállítás indítási projektként) lehetőségre, majd nyomja le az **F5** billentyűt a projekt újraépítéséhez és az alkalmazás indításához.

    Ekkor elindul az alkalmazás, amely a helyi mobilszolgáltatás-példányhoz csatlakozik.   

4. Az alkalmazásban írjon be egy jelentéssel bíró szöveget az **Insert a TodoItem** (Tennivaló beszúrása) mezőbe, például _Az oktatóanyag befejezése_, majd kattintson a **Save** (Mentés) gombra.

    Ez egy POST kérést küld a helyi mobilszolgáltatásnak. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A mobilszolgáltatás visszaadja a táblában tárolt elemeket, amelyek az alkalmazás második oszlopában jelennek meg.


<!--HONumber=Jun16_HO2-->


