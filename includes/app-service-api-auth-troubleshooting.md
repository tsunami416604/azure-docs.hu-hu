Az idő hitelesítési hibák többségét eredménye nem megfelelő vagy nem következetes konfigurációs beállításokat. Az alábbiakban néhány dolog adott javaslatokat.

* Győződjön meg arról, hogy nem teljesíti a **mentése** gombra kattint, bármely részén. Ez gyakran könnyen elvégezhető, és az eredménye, hogy akkor lesz megtekintik helyes az értékük portál oldalon, de ténylegesen ezek még nincsenek mentve a Azure-alapú környezetben vagy az Azure AD-alkalmazást.
* A konfigurált beállítások a **Alkalmazásbeállítások** panel az Azure portál, győződjön meg arról, hogy a megfelelő API-alkalmazás vagy a web app kiválasztott a beállítások vannak megadva.  Emellett győződjön meg arról, hogy a beállításokat vannak-e megadva **Alkalmazásbeállítások** és nem **kapcsolati karakterláncok**, mert a két szakasz formátuma hasonló.
* A JavaScript előtér hitelesítést, töltse le újra annak ellenőrzésére, hogy a jegyzék `oauth2AllowImplicitFlow` sikeresen módosítva, `true`.
* Ellenőrizze, hogy HTTPS bárhol konfigurált URL-címek:
  
  * A projekt kódját
  * A CORS
  * Az alkalmazásbeállítások Azure-alapú környezetben az egyes API-alkalmazás és a webes alkalmazás
  * Az Azure AD alkalmazás-beállításokat.
    
    Vegye figyelembe, hogy ha egy API-alkalmazás URL-címet a portálról másolja, ez gyakran `http://` és módosítsa úgy, hogy manuálisan kell `https://`.
* Ellenőrizze, hogy a kód módosításokat sikeres volt-e telepítve. Például egy több-projekt megoldásban is lehet módosítani a projekt kódját, és véletlenül válasszon egyet a többi Ha szeretné telepíteni a módosítás.
* Győződjön meg arról, hogy szeretne HTTPS URL-címek nem HTTP URL-címek a böngészőben. Alapértelmezés szerint a Visual Studio létrehozza a HTTP URL-címek profilok közzététele, és mi megnyitása a böngészőben, a projekt telepítése után.
* A JavaScript előtér hitelesítést győződjön meg arról, hogy a CORS megfelelően van-e beállítva az API-alkalmazás, amely a JavaScript-kód hívja meg. Ha a arról, hogy a problémát a CORS-kapcsolódó kétséges, próbálja ki a "*" az engedélyezett forrás URL-címet. 
* JavaScript előtér nyissa meg a hiba további információkat a böngésző fejlesztői eszközök konzol lapon, és vizsgálja meg a HTTP-kérések a hálózaton. Előfordulhat azonban, konzol hibaüzenetek félrevezető. A CORS hibát jelző üzenet jelenik meg, ha a valós oka valószínűleg a hitelesítést. Ha ez helyzet a hitelesítéssel ideiglenesen átmenetileg le van tiltva az alkalmazás futtatásával ellenőrizheti.
* .NET API-alkalmazások esetén ellenőrizze, hogy kap, a rendszer mennyi információt látható a hibaüzenetekben a lehető beállításával [ki a customErrors módot](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* A .NET API-alkalmazást, indítsa el a [távoli hibakeresési munkamenetben](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug), és vizsgálja meg a kódot, amely egy tulajdonosi jogkivonatot szerezni az adal-t használ, vagy kódot, amely ellenőrzi az elvárt szolgáltatási egyszerű azonosító szemben átadott változók értékei Figyelje meg, hogy a kód is számos különböző forrásokból konfigurációs értékek átvételéhez, ezért található meglepetések számát, így lehetséges. Például, ha írunk `ida:ClientId` , `ida:ClientID` Azure App Service-környezet beállításainak konfigurálásakor a kód első előfordulhat, hogy a `ida:ClientId` érték, amely a Web.config fájlból, a rendszer figyelmen kívül hagyja az Azure App Service-beállítás keresi. 
* Ha egy normál Internet Explorer-ablakban probléma, akkor egy már létező napló a zavart; InPrivate próbálja, majd ismételje meg a Chrome, Firefox vagy.

