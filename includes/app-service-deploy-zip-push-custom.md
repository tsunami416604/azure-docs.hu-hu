## <a name="deployment-customization"></a>Telepítés testreszabása

A telepítési folyamat azt feltételezi, hogy a zip-fájlt, amely leküldéses tartalmaz egy futtatásra kész alkalmazást. Alapértelmezés szerint nincs testreszabások futnak. Ahhoz, hogy az folyamatos integráció le azonos build folyamatait, adja hozzá az alkalmazás beállításait a következőket:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

.Zip ügyfélleküldéses telepítés használata esetén ez a beállítás akkor **hamis** alapértelmezés szerint. Az alapértelmezett érték **igaz** folyamatos integrációt telepítésekhez. Ha beállítása **igaz**, a telepítéssel kapcsolatos beállítások központi telepítése során használt. Ezeket a beállításokat konfigurálhatja Alkalmazásbeállítások vagy egy .deployment konfigurációs fájlban, amely a .zip fájl található. További információkért lásd: [tárház és a telepítéssel kapcsolatos beállítások](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) a telepítési útmutatóban.