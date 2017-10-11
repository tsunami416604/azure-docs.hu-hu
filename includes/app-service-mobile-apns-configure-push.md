

1. Indítsa el a Mac gépen **Keychain Access**. A bal oldali navigációs sávon a **kategória**, nyissa meg **saját tanúsítványok**. Keresse meg az SSL-tanúsítványt az előző szakaszban letöltött, és a Microsoftot annak tartalmát. Csak a tanúsítvány kiválasztása (nem engedélyezi a titkos kulcs), és [exportálni kell](https://support.apple.com/kb/PH20122?locale=en_US).
2. A a [Azure-portálon](https://portal.azure.com/), kattintson a **összes tallózása** > **alkalmazásszolgáltatások**, és kattintson a Mobile Apps háttér. A **beállítások**, kattintson a **App Service leküldéses**, majd kattintson az értesítési központ nevére. Ugrás a **Apple leküldéses értesítési szolgáltatások** > **-tanúsítvány feltöltése**. Töltse fel a .p12 fájlt, válassza a megfelelő **mód** (attól függően, hogy az SSL-ügyféltanúsítvány a korábban az éles vagy védőfal). A módosítások mentéséhez.

A szolgáltatás konfigurálva van a leküldéses értesítések IOS rendszerű eszközökön működik.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
