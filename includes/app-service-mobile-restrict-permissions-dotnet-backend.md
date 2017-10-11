
Alapértelmezés szerint a API-k a Mobile Apps háttérből névtelenül meghívható. Ezt követően kell csak hitelesített ügyfelek korlátozza a hozzáférést.  

* **NODE.js vissza (az Azure portálon) end** :  

    A Mobile Apps beállításaiban kattintson **könnyen táblák** és ki kell jelölnie a táblázatot. Kattintson a **engedélyeinek módosítása**, jelölje be **hitelesített hozzáférés csak** összes engedélyeket, és kattintson a **mentése**.
* **Háttér .NET (C#)**:  

    A projekt, lépjen a **tartományvezérlők** > **TodoItemController.cs**. Adja hozzá a `[Authorize]` attribútumot a **TodoItemController** osztály, az alábbiak szerint. Csak a megadott metódusok való hozzáférés korlátozása, is alkalmazhat Ez az attribútum csak azokat a módszereket az osztály helyett. A projekt közzé.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **NODE.js-háttéralkalmazáshoz (Node.js-kódot) keresztül** :  

    A tábla hozzáféréshez hitelesítés szükséges, vegye fel a következő sort a Node.js server-parancsfájlt:

        table.access = 'authenticated';

    További részletekért lásd: [Útmutató: hitelesítés megkövetelése a hozzáféréshez táblákra](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). A kód gyorsútmutató-projekt letöltése a hely további tudnivalókért lásd: [hogyan: Töltse le a Node.js háttérrendszer gyors üzembe helyezés kód projekt Git használatával](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).
