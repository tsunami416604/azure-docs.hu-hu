---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: e38cecfe206f21f9189493e7ed6e8f0cadda9cd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75463530"
---
Az Azure-ügyfelek havonta 25 000 ingyenes e-mailt oldhatnak fel. Ez a 25 000 ingyenes havi e-mail hozzáférést biztosít a speciális jelentésekhez és elemzésekhez, valamint [az összes API-hoz][all APIs] (web, SMTP, Esemény, Elemzés stb.). A SendGrid által biztosított további szolgáltatásokról a [SendGrid Solutions][SendGrid Solutions] oldalán talál információkat.

### <a name="to-sign-up-for-a-sendgrid-account"></a>SendGrid-fiók regisztrálása
1. Jelentkezzen be az [Azure Portalra.][Azure portal]
2. Az Azure Portal menüben vagy a kezdőlapon válassza **az Erőforrás létrehozása lehetőséget.**

    ![command-bar-new][command-bar-new]
3. Keresse meg és válassza a **SendGrid**lehetőséget.

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
13. A vásárlás megerősítése után megjelenik egy **sikeres központi telepítés,** és megjelenik a fiók listája.

    ![all-resources][all-resources]

    Miután befejezte a vásárlást, valamint a **Kezelés** gombra kattintva elindította az e-mail ellenőrzésének folyamatát, a SendGrid küld egy e-mailt, amelyben a fiókja megerősítésére kéri. Ha nem kapja meg ezt az e-mailt, vagy problémái vannak a fiókjának ellenőrzésével, kérjük, olvassa el a GYIK-et.

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

3. Kattintson az **API-kulcs létrehozása gombra.**

    ![general-api-key][general-api-key]
4. Adja meg legalább a **Kulcs nevét**, biztosítson teljes hozzáférést a **Levélküldés** számára, majd válassza a **Mentés** lehetőséget.

    ![hozzáférés][access]
5. Ekkor az API egyetlen alkalommal megjelenik. Ügyeljen arra, hogy biztonságosan tárolja.

### <a name="to-find-your-sendgrid-credentials"></a>A SendGrid hitelesítő adatainak megkeresése
1. Kattintson a kulcs ikonra a **Felhasználónév** megkereséséhez.

    ![kulcs][key]
2. A jelszó megegyezik azzal, amelyet a beállításkor megadott. Módosításokat a **Jelszó módosítása** vagy a **Jelszó alaphelyzetbe állítása** lehetőségek kiválasztásával eszközölhet.

Az e-mailek kézbesítési eredményessége beállításainak kezeléséhez kattintson a **Kezelés gombra**. Ez átirányítja a SendGrid irányítópultjára.

![kezelés][manage]

A SendGriden keresztül küldött e-mailekről az [E-mail API áttekintése című témakörben olvashat bővebben.][Email API Overview]

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

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
