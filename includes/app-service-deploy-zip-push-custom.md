## <a name="deployment-customization"></a>Telepítés testreszabása

A telepítési folyamat azt feltételezi, hogy a leküldéses .zip fájl tartalmazza-e a futásra kész alkalmazások. Alapértelmezés szerint nincs testreszabások futnak. A következő hozzá az alkalmazás beállításait folyamatos integrációt az beszerzése azonos build folyamatok engedélyezéséhez:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

.Zip ügyfélleküldéses telepítés használata esetén ez a beállítás akkor **hamis** alapértelmezés szerint. Az alapértelmezett érték **igaz** folyamatos integrációt telepítésekhez. Ha beállítása **igaz**, a telepítéssel kapcsolatos beállítások központi telepítése során használt. Ezek a beállítások alkalmazás beállításai vagy a állítható be egy `.deployment` a zip-fájl gyökérkönyvtárában elhelyezett konfigurációs fájlban. További információkért lásd: [tárház és a telepítéssel kapcsolatos beállítások](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) a telepítési útmutatóban.