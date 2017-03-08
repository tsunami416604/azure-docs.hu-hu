Az Azure-ügyfelek havonta 25 000 ingyenes e-mailt oldhatnak fel. Ez a havi 25 000 ingyenes e-mail hozzáférést biztosít a fejlett jelentéskészítési és elemzési szolgáltatásokhoz, valamint [az API-k mindegyikéhez][all APIs] (többek között Web, SMTP, Event, Parse). A SendGrid által biztosított további szolgáltatásokról a [SendGrid Solutions][SendGrid Solutions] oldalán talál információkat.

### <a name="to-sign-up-for-a-sendgrid-account"></a>SendGrid-fiók regisztrálása
1. Jelentkezzen be az [Azure felügyeleti portálra][Azure Management Portal].
2. A bal oldali menüben kattintson az **Új** lehetőségre.

    ![command-bar-new][command-bar-new]
3. Kattintson a **Bővítmények**, majd pedig a **SendGrid Email Delivery** lehetőségre.

    ![sendgrid-store][sendgrid-store]
4. Töltse ki a regisztrációs űrlapot, majd válassza a **Létrehozás** lehetőséget.

    ![sendgrid-create][sendgrid-create]
5. Adjon meg egy **Nevet** a SendGrid-szolgáltatás azonosításához az Azure beállításaiban. A neveknek 1–100 karakter hosszúságúnak kell lenniük, és kizárólag alfanumerikus karaktereket, kötőjeleket, pontokat és aláhúzásjeleket tartalmazhatnak. A névnek egyedinek kell lennie az előfizetett Azure Store-elemek listájában.
6. Adja meg és erősítse meg a **Jelszót**.
7. Válassza ki az **Előfizetést**.
8. Hozzon létre egy új **Erőforráscsoportot** vagy használjon egy meglévőt.
9. A **Tarifacsomag** szakaszban válassza ki azt a SendGrid-csomagot, amelyre regisztrálni kíván.

    ![sendgrid-pricing][sendgrid-pricing]
10. Adjon meg egy **Promóciókódot**, ha rendelkezik ilyennel.
11. Adja meg a **Kapcsolattartási adatait**.
12. Tekintse át és fogadja el a **Jogi feltételeket**.
13. A vásárlás megerősítését követően egy **Sikeres üzembe helyezés** szöveggel ellátott előugró ablakot fog látni, a fiókja pedig megjelenik a **Minden erőforrás** szakaszban.

    ![all-resources][all-resources]

    Miután befejezte a vásárlást, valamint a **Kezelés** gombra kattintva elindította az e-mail ellenőrzésének folyamatát, a SendGrid küld egy e-mailt, amelyben a fiókja megerősítésére kéri. Ha nem kapja meg ezt az e-mailt, vagy problémába ütközik a fiókja megerősítésével kapcsolatban, tekintse meg a Gyakori kérdéseket.

    ![kezelés][manage]

    **Fiókja megerősítéséig naponta legfeljebb 100 e-mailt tud elküldeni.**

    Az előfizetési csomag módosításához vagy a SendGrid kapcsolattartási beállításainak megtekintéséhez kattintson a SendGrid-szolgáltatás nevére a SendGrid Marketplace irányítópultjának megnyitásához.

    ![beállítások][settings]

    Ha a SendGrid használatával szeretne e-mailt küldeni, meg kell adnia API-kulcsát.

### <a name="to-find-your-sendgrid-api-key"></a>A SendGrid API-kulcsának megkeresése
1. Kattintson a **Kezelés** gombra.

    ![kezelés][manage]
2. A SendGrid irányítópultján válassza a **Beállítások**, majd az **API-kulcsok** lehetőséget a bal oldali menüben.

    ![api-keys][api-keys]

3. Kattintson az **API-kulcs létrehozása** legördülő listára, és válassza az **Általános API-kulcs** lehetőséget.

    ![general-api-key][general-api-key]
4. Adja meg legalább a **Kulcs nevét**, biztosítson teljes hozzáférést a **Levélküldés** számára, majd válassza a **Mentés** lehetőséget.

    ![hozzáférés][access]
5. Ekkor az API egyetlen alkalommal megjelenik. Ügyeljen arra, hogy biztonságosan tárolja.

### <a name="to-find-your-sendgrid-credentials"></a>A SendGrid hitelesítő adatainak megkeresése
1. Kattintson a kulcs ikonra a **Felhasználónév** megkereséséhez.

    ![kulcs][key]
2. A jelszó megegyezik azzal, amelyet a beállításkor megadott. Módosításokat a **Jelszó módosítása** vagy a **Jelszó alaphelyzetbe állítása** lehetőségek kiválasztásával eszközölhet.

Az e-mailek kézbesítési eredményessége beállításainak kezeléséhez kattintson a **Kezelés gombra**. A rendszer ekkor átirányítja a [SendGrid irányítópultjára][SendGrid dashboard]

    ![manage][manage]

    For more information on sending email through SendGrid, visit the [Email API Overview][Email API Overview].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png
[SendGrid dashboard]: ./media/sendgrid-sign-up/dashboard.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure Management Portal]: https://manage.windowsazure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html

