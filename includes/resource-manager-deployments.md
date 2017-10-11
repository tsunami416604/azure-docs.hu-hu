## <a name="incremental-and-complete-deployments"></a>Növekményes és teljes központi telepítések
Az erőforrások telepítésekor Megadja, hogy a központi telepítés egy növekményes frissítés vagy egy teljes frissítés. E két mód között az elsődleges különbség az, hogyan kezeli az erőforrás-kezelő a meglévő erőforrások az erőforráscsoportban, amelyek nincsenek a sablonban:

* Teljes módban, erőforrás-kezelő **törli** erőforrásokat, az erőforráscsoport szerepel, de nincs megadva a sablonban. 
* Növekményes módban, erőforrás-kezelő **hagyja változatlanul** erőforrásokat, az erőforráscsoport szerepel, de nincs megadva a sablonban.

Mindkét módnál az erőforrás-kezelő megpróbálja a sablonban megadott összes erőforrások biztosításához. Ha az erőforrás már létezik az erőforráscsoporthoz tartozik, és a beállítások nem változnak, a művelet nincs változás eredményezi. Ha egy erőforrás beállításait módosítja, az erőforrás ki van építve a új beállítások. Ha úgy próbálja frissíteni a helyet vagy a meglévő erőforrás típusa, a telepítés sikertelen, a hibaüzenet. Ehelyett egy új erőforrást a hely telepítése, vagy adjon meg, hogy van szüksége.

Alapértelmezés szerint az erőforrás-kezelő a növekményes módot használ.

Növekményes és teljes mód közötti különbséget mutatja be, a következő eset.

**Meglévő erőforráscsoport** tartalmazza:

* Erőforrás
* B erőforrás
* Erőforrás C

**Sablon** határozza meg:

* Erőforrás
* B erőforrás
* D erőforrás

Ha telepítve **növekményes** módot, az erőforráscsoport tartalmazza:

* Erőforrás
* B erőforrás
* Erőforrás C
* D erőforrás

Ha telepítve **teljes** mód, erőforrás-C törlődik. Az erőforráscsoport tartalmazza:

* Erőforrás
* B erőforrás
* D erőforrás
