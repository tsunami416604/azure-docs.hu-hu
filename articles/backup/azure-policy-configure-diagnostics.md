---
title: Tár diagnosztikai beállításainak konfigurálása nagy méretekben
description: Log Analytics diagnosztikai beállítások konfigurálása egy adott hatókörben lévő összes tárolóhoz a Azure Policy használatával
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: e6ba8eb98ca1d6af9fc745d9baf3840ccd1ac224
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82195706"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Tár diagnosztikai beállításainak konfigurálása nagy méretekben

A Azure Backup által biztosított jelentéskészítési megoldás Log Analytics (LA). Ahhoz, hogy egy adott tároló adatait el lehessen küldeni a LA-nek, az adott tár számára létre kell hozni egy [diagnosztikai beállítást](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events) .

Gyakran előfordulhat, hogy a diagnosztikai beállítások manuális hozzáadásával a tárolóban nehézkes feladat lehet. Emellett a létrehozott új tárolónak is engedélyeznie kell a diagnosztikai beállításokat ahhoz, hogy meg tudja jeleníteni a tár jelentéseit.

A diagnosztikai beállítások méretezésének leegyszerűsítése érdekében a Azure Backup beépített [Azure Policy](https://docs.microsoft.com/azure/governance/policy/)biztosít. Ez a házirend egy LA Diagnostics-beállítást hoz létre egy adott előfizetés vagy erőforráscsoport összes tárolóján. A következő szakasz útmutatást nyújt ennek a szabályzatnak a használatáról.

## <a name="supported-scenarios"></a>Támogatott helyzetek

* A szabályzat egyszerre alkalmazható egy adott előfizetésben található összes Recovery Services-tárolóra (vagy az előfizetésben található erőforráscsoporthoz). A szabályzatot hozzárendelő felhasználónak tulajdonosi hozzáféréssel kell rendelkeznie ahhoz az előfizetéshez, amelyhez a szabályzat hozzá van rendelve.

* A felhasználó által megadott LA munkaterület (amelybe a rendszer a diagnosztikai adatokhoz küldi a-t) a tárolók eltérő előfizetésében lehet, amelyhez a szabályzat hozzá van rendelve. A felhasználónak az "olvasó", a "közreműködő" vagy a "tulajdonos" jogosultsággal kell rendelkeznie ahhoz az előfizetéshez, amelyben a megadott LA munkaterület létezik.

* A felügyeleti csoport hatóköre jelenleg nem támogatott.

* A beépített szabályzat jelenleg nem érhető el az országos felhőkben.

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Beépített házirend társítása hatókörhöz

Az alábbi lépéseket követve rendelheti hozzá a tárolók szabályzatát a szükséges hatókörben:

1. Jelentkezzen be a Azure Portalba, és navigáljon a **szabályzat** irányítópultra.
2. A bal oldali menüben válassza a **definíciók** lehetőséget, hogy lekérje az Azure-erőforrások összes beépített szabályzatának listáját.
3. A **Kategória = figyelés**listájának szűrése Keresse meg az [előzetes verzió] nevű szabályzatot **: Recovery Services-tároló diagnosztikai beállításainak központi telepítése log Analytics munkaterületre erőforrás-specifikus kategóriákhoz**.

    ![Házirend-definíció panel](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Kattintson a szabályzat nevére. A rendszer átirányítja a szabályzat részletes definícióját.

    ![Részletes szabályzat-definíció](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Kattintson a panel tetején található **hozzárendelés** gombra. Ez átirányítja a szabályzat- **hozzárendelési** panelre.

6. Az **alapbeállítások**területen kattintson a **hatókör** mező melletti három pontra. Ekkor megnyílik a megfelelő környezeti panel, ahol kiválaszthatja az előfizetést, amelyre alkalmazni kívánja a szabályzatot. Kiválaszthat egy erőforráscsoportot is, hogy a házirend csak egy adott erőforráscsoport tárolói esetében legyen alkalmazva.

    ![Szabályzat-hozzárendelés alapjai](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. A **Paraméterek**területen adja meg a következő adatokat:

    * **Profilnév** – a házirend által létrehozott diagnosztikai beállításokhoz hozzárendelni kívánt név.
    * **Log Analytics munkaterület** – az a log Analytics munkaterület, amelyhez a diagnosztikai beállítást társítani kell. A szabályzat-hozzárendelés hatókörében lévő összes tároló diagnosztikai adatait a rendszer leküldi a megadott LA munkaterületre.

    * **Kizárási címke neve (nem kötelező) és a kizárási címke értéke (nem kötelező)** – kiválaszthatja, hogy ki kell zárnia egy bizonyos címke nevét és értékét tartalmazó tárolókat a szabályzat-hozzárendelésből. Ha például **nem** szeretné, hogy a rendszer a diagnosztikai beállítást olyan tárolók számára adja hozzá, amelyek "isTest" címkéje "yes" értékre van állítva, akkor a kizárási címke **neve** mezőben a "isTest" karakterláncot kell megadnia, a kizárási címke **értéke** mezőben pedig az "igen" értéket kell megadnia. Ha a két mező bármelyike (vagy mindkettő) üresen marad, a szabályzat az összes érintett tárolóra érvényes lesz, függetlenül az általuk tartalmazott címkéktől.

    ![Szabályzat-hozzárendelési paraméterek](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Szervizelési feladat létrehozása** – ha a szabályzatot egy hatókörhöz rendeli hozzá, a hatókörben létrehozott új tárolók automatikusan beolvassák a beállított La diagnosztikai beállításokat (a tár létrehozásának idejétől számított 30 percen belül). A hatókörben lévő meglévő tárolók diagnosztikai beállításainak hozzáadásához a házirend-hozzárendelési időpontban aktiválhat egy szervizelési feladatot. A Szervizelési feladatok elindításához jelölje be a **szervizelési feladat létrehozása**jelölőnégyzetet.

    ![Szabályzat-hozzárendelés szervizelése](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Keresse meg a **felülvizsgálat + létrehozás** lapot, és kattintson a **Létrehozás**gombra.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Milyen feltételek esetén érvényes a Szervizelési feladat egy tárolóra?

A Szervizelési feladatot a szabályzat definíciója szerint meg nem felelő tárakra alkalmazza a rendszer. A tár nem megfelelő, ha megfelel a következő feltételek valamelyikének:

* Nem létezik diagnosztikai beállítás a tárolóhoz.
* A rendszer diagnosztikai beállításokat tartalmaz a tárolóhoz, de egyik beállítás sem rendelkezik az **összes** olyan erőforrás-specifikus eseménysel, amely a La as célként van engedélyezve, és az **erőforrás-specifikus** elem van kiválasztva a váltásban.

Tehát akkor is, ha a felhasználó rendelkezik egy olyan tárolóval, amelyen engedélyezve van a AzureBackupReport esemény a AzureDiagnostics módban (amelyet a biztonsági mentési jelentések is támogatnak), a Szervizelési feladat továbbra is érvényes lesz erre a tárolóra, mivel az erőforrás [-specifikus](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event)mód a diagnosztikai beállítások létrehozásának ajánlott módja.

Ha a felhasználó rendelkezik egy olyan tárolóval, amely a hat erőforrás-specifikus eseménynek csak egy részhalmazát engedélyezi, a Szervizelési feladat érvényes lesz erre a tárolóra, mivel a biztonsági mentési jelentések csak a várt módon fognak működni, ha az összes hat erőforrás-specifikus esemény engedélyezve van.

> [!NOTE]
>
> Ha egy tároló rendelkezik egy meglévő diagnosztikai beállítással, amelynek az erőforrás-specifikus kategóriák egy **részhalmaza** engedélyezve van, úgy van konfigurálva, hogy adatokat küldjön egy adott La munkaterületre, azaz a "munkaterület x" értéket, a Szervizelési feladat meghiúsul (csak az adott tár esetében), ha a házirend-hozzárendelésben megadott cél La munkaterület **ugyanaz** a "munkaterület x".
>
>Ennek az az oka, hogy ha az ugyanazon az erőforráson két különböző diagnosztikai beállítás által engedélyezett események valamilyen formában **átfedésben** vannak, akkor a beállításoknak nem lehet ugyanaz a La munkaterülete, mint a célhely. Ennek a hibának a manuális feloldásához navigáljon a megfelelő tárolóhoz, és konfigurálja a diagnosztikai beállításokat egy másik LA-munkaterülettel a célhelyként.
>
> Vegye figyelembe, hogy a Szervizelési feladat **nem** fog sikerülni, ha a meglévő diagnosztikai beállítás csak az X munkaterület AzureBackupReport engedélyezett, mert ebben az esetben a meglévő beállítás által engedélyezett események és a Szervizelési feladat által létrehozott események között nem lesz átfedés.

## <a name="next-steps"></a>Következő lépések

* [Tudnivalók a biztonsági mentési jelentések használatáról](https://docs.microsoft.com/azure/backup/configure-reports)
* [További információ a Azure Policy](https://docs.microsoft.com/azure/governance/policy/)
* [A Azure Policy használata az adott hatókörben lévő összes virtuális gép biztonsági mentésének automatikus engedélyezéséhez](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)
