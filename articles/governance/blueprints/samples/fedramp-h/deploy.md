---
title: Minta – FedRAMP magas tervrajz – a lépések üzembe helyezése
description: A FedRAMP magas tervrajzi mintájának üzembe helyezése, beleértve a tervrajz-összetevő paraméterének részleteit.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: baa9f419a9f15e84eced322c8f7095606c7a24c4
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76941366"
---
# <a name="deploy-the-fedramp-high-blueprint-sample"></a>A FedRAMP High Blueprint minta üzembe helyezése

Az Azure-tervezetek nagy FedRAMP való üzembe helyezéséhez a következő lépéseket kell végrehajtani:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **közzétettként**
> - A terv másolatának kiosztása meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először is implementálja a terv mintáját úgy, hogy létrehoz egy új tervet a környezetben a minta kezdőként való használatával.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. A bal oldali **első lépések** lapon kattintson a **Létrehozás** gombra a _terv létrehozása_területen.

1. Keresse meg a **FedRAMP High** Blueprint mintát _más minták_ alatt, és válassza a **minta használata**lehetőséget.

1. Adja meg a tervezet mintájának _alapjait_ :

   - **Terv neve**: adjon meg egy nevet a FedRAMP High Blueprint minta példányának.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza ki az oldal tetején található _összetevők fület_ , vagy a **következőt:** összetevők az oldal alján.

1. Tekintse át a terv mintáját alkotó összetevők listáját. Számos összetevőhöz vannak olyan paraméterek, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor befejezte a tervezet mintájának áttekintését.

## <a name="publish-the-sample-copy"></a>A minta másolatának közzététele

A terv mintájának másolata már létre lett hozva a környezetében. A rendszer **Piszkozat** módban jön létre, és **közzé** kell tenni ahhoz, hogy hozzá lehessen rendelni és telepíteni lehessen. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás a FedRAMP magas vezérlőkkel való összehangolását is áthelyezheti.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza a bal oldali **terv-definíciók** lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a **terv közzététele** lehetőséget az oldal tetején. A jobb oldalon található új lapon adjon meg egy **verziót** a tervezet mintájának másolatához. Ez a tulajdonság akkor hasznos, ha később módosítja a módosítást. Adjon meg olyan **módosítási megjegyzéseket** , mint például az "első verzió, amely a FedRAMP High Blueprint mintából lett közzétéve." Ezután válassza a **Közzététel** elemet az oldal alján.

## <a name="assign-the-sample-copy"></a>A minta másolatának kiosztása

Miután a tervezet mintájának **közzététele**sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ezzel a lépéssel megadhatja, hogy az egyes központi telepítések egyediek legyenek-e.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza a bal oldali **terv-definíciók** lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a terv **kiosztása** elemet a terv definíciója oldal tetején.

1. Adja meg a tervrajz-hozzárendelés paramétereinek értékét:

   - Alapvető beállítások

     - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, és a terv mintájának másolatát mentette. Ha egynél több előfizetést választ ki, a rendszer minden megadott paraméterrel létrehoz egy hozzárendelést.
     - **Hozzárendelés neve**: a név előre ki van töltve a terv neve alapján.
       Szükség szerint módosítsa a változást, vagy hagyja a következőt:.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: válasszon egy **közzétett** verziót a terv mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezethez tartozó terv zárolási beállítását. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás beállítást.

   - Összetevő paramétereinek

     Az ebben a szakaszban meghatározott paraméterek a definiált összetevőre vonatkoznak. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza a lap alján található **hozzárendelés** elemet. A terv-hozzárendelés létrejött, és az összetevő üzembe helyezése megkezdődik. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának megtekintéséhez nyissa meg a terv-hozzárendelést.

> [!WARNING]
> Az Azure BluePrints szolgáltatás és a beépített tervrajzi minták **díjmentesek**. Az Azure-erőforrások [díjszabása termékenként](https://azure.microsoft.com/pricing/)történik. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával megbecsülheti a tervrajzi minta által üzembe helyezett erőforrások futtatásának költségeit.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a tervrajz-összetevő paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|Log Analytics munkaterület-azonosító, amelyhez a virtuális gépeket konfigurálni kell|Ez a Log Analytics munkaterület azonosítója (GUID), amelyhez a virtuális gépeket konfigurálni kell.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|Azon erőforrástípusok listája, amelyeknek engedélyezve kell lennie a diagnosztikai naplóknak|A naplózni kívánt erőforrástípusok listája, ha a diagnosztikai napló beállítása nincs engedélyezve. Elfogadható értékek találhatók [Azure monitor diagnosztikai naplók sémái](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)között.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A Windows rendszerű virtuális gépek rendszergazdái csoportból kizárandó felhasználók listája|A rendszergazdák helyi csoportba kizárandó tagok pontosvesszővel tagolt listája. Pl.: rendszergazda; myUser1; myUser2|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A Windows rendszerű virtuális gépek rendszergazdái csoportjának részét képező felhasználók listája|A rendszergazdák helyi csoportba foglalandó tagok pontosvesszővel tagolt listája. Pl.: rendszergazda; myUser1; myUser2|
|\[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux VM Scale Sets (VMSS)|Szabályzat-hozzárendelés|Log Analytics a Linux VM Scale Sets (VMSS) munkaterülete|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux VM Scale Sets (VMSS)|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Linux operációs rendszert futtató virtuálisgép-rendszerképek listája|Üres tömb felhasználható a nem kötelező paraméterek jelölésére: \[\]|
|\[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken|Szabályzat-hozzárendelés|A Linux rendszerű virtuális gépek Log Analytics munkaterülete|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Linux operációs rendszert futtató virtuálisgép-rendszerképek listája|Üres tömb felhasználható a nem kötelező paraméterek jelölésére: \[\]|
|\[előzetes verzió\]: Log Analytics Agent telepítése Windows VM Scale Sets (VMSS)|Szabályzat-hozzárendelés|Log Analytics munkaterület a Windows VM Scale Setshoz (VMSS)|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[előzetes verzió\]: Log Analytics Agent telepítése Windows VM Scale Sets (VMSS)|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Windows operációs rendszert futtató virtuálisgép-rendszerképek listája|Üres tömb felhasználható a nem kötelező paraméterek jelölésére: \[\]|
|\[előzetes verzió\]: Log Analytics ügynök központi telepítése Windows rendszerű virtuális gépekre|Szabályzat-hozzárendelés|Log Analytics munkaterület a Windows rendszerű virtuális gépekhez|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[előzetes verzió\]: Log Analytics ügynök központi telepítése Windows rendszerű virtuális gépekre|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Windows operációs rendszert futtató virtuálisgép-rendszerképek listája|Üres tömb felhasználható a nem kötelező paraméterek jelölésére: \[\]|
|Komplex veszélyforrások elleni védelem üzembe helyezése a Storage-fiókokon|Szabályzat-hozzárendelés|Következmény|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|Naplózás üzembe helyezése SQL-kiszolgálókon|Szabályzat-hozzárendelés|A megőrzési időtartam napokban megadott értéke (a 0 korlátlan megőrzést jelez)|Megőrzési napok (nem kötelező, 180 nap, ha nincs megadva)|
|Naplózás üzembe helyezése SQL-kiszolgálókon|Szabályzat-hozzárendelés|Az SQL Server naplózásához használt Storage-fiók erőforráscsoport-neve|A naplózás az adatbázis-eseményeket egy naplóba írja az Azure Storage-fiókban (a Storage-fiók minden régióban létrejön, ahol létrejön egy SQL Server, amelyet az adott régióban lévő összes kiszolgáló megoszt majd). Fontos – a naplózás megfelelő működéséhez ne törölje vagy nevezze át az erőforráscsoportot vagy a Storage-fiókokat.|
|Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése|Szabályzat-hozzárendelés|A hálózati biztonsági csoport diagnosztika Storage-fiókjának előtagja|Ezt az előtagot a hálózati biztonsági csoport helyével együtt kell összekapcsolni a létrehozott Storage-fiók nevének létrehozásához.|
|Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése|Szabályzat-hozzárendelés|A hálózati biztonsági csoport diagnosztikát szolgáló Storage-fiók erőforráscsoport-neve (léteznie kell)|Az az erőforráscsoport, amelyben a Storage-fiók létre lesz hozva. Ez az erőforráscsoport már léteznie kell.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|Erőforrások és erőforráscsoportok engedélyezett helyei|Az erőforrások üzembe helyezése során a szervezet által megadható Azure-beli helyszínek listája. Ezt a megadott értéket az "engedélyezett helyszínek" házirend is használja a házirend kezdeményezésen belül.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A sebezhetőségi felmérést alkalmazni ajánlott az SQL-kiszolgálókon|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A sebezhetőségi felmérést engedélyezni kell Virtual Machines|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A Geo-redundáns tárterületet engedélyezni kell a Storage-fiókokhoz|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|Az internetre irányuló virtuális gépek hálózati biztonsági csoportjának szabályait meg kell szigorítani|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A webalkalmazás csak HTTPS protokollon keresztül érhető el|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|Az elavult fiókokat el kell távolítani az előfizetésből|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A CORS nem teszi lehetővé minden erőforrás számára a webalkalmazás elérését|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|Az MFA-t engedélyezni kell az előfizetés írási engedéllyel rendelkező fiókjain|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|
|\[előzetes verzió\]: a naplózási követelményeknek megfelelő virtuálisgép-bővítmények naplózása FedRAMP ellenőrzése és telepítése|Szabályzat-hozzárendelés|A hosszú távú geo-redundáns biztonsági mentést engedélyezni kell az Azure SQL Database-adatbázisokhoz|A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók.|


## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette a FedRAMP High Blueprint-minta üzembe helyezésének lépéseit, tekintse meg a következő cikkeket a terv és a vezérlés leképezésének megismeréséhez:

> [!div class="nextstepaction"]
> [FedRAMP High Blueprint – áttekintés](./index.md)
> [FedRAMP nagy terv – vezérlés leképezése](./control-mapping.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.