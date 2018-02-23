

1. Indítsa el a Mac gépen **Keychain Access**. A bal oldali navigációs sávon a **kategória**, nyissa meg **saját tanúsítványok**. Keresse meg az SSL-tanúsítványt az előző szakaszban letöltött, és majd fedjük fel annak tartalmát. Csak a tanúsítvány kiválasztása (nem engedélyezi a titkos kulcs). Majd [exportálni kell](https://support.apple.com/kb/PH20122?locale=en_US).
2. Az a [Azure-portálon](https://portal.azure.com/), jelölje be **összes tallózása** > **alkalmazásszolgáltatások**. Ezután válassza ki a Mobile Apps háttér. 
3. A **beállítások**, jelölje be **App Service leküldéses**. Ezután válassza ki az értesítési központ nevére. 
3. Ugrás a **Apple leküldéses értesítési szolgáltatások** > **-tanúsítvány feltöltése**. Töltse fel a .p12 fájlt, válassza a megfelelő **mód** (attól függően, hogy az SSL-ügyféltanúsítvány a korábban az éles vagy védőfal). A módosítások mentéséhez.

A szolgáltatás konfigurálva van a leküldéses értesítések IOS rendszerű eszközökön működik.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
