---
title: A Vault diagnosztika beállításainak konfigurálása méretarányosan
description: Log Analytics-diagnosztikai beállítások konfigurálása egy adott hatókör összes tárolójához az Azure-szabályzat használatával
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: c92957cab3e1ed745e7031e3c6f32e7ecda550a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584506"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>A Vault diagnosztika beállításainak konfigurálása méretarányosan

Az Azure Backup által biztosított jelentéskészítési megoldás a Log Analytics (LA) szolgáltatást használja. Ahhoz, hogy egy adott tároló adatait la-be lehessen küldeni, létre kell hozni egy [diagnosztikai beállítást](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events) a tárolóhoz.

Gyakran a diagnosztikai beállítás hozzáadása manuálisan tárolónként lehet nehézkes feladat. Emellett minden új trezor létrehozott is rendelkeznie kell diagnosztikai beállítások engedélyezve kell ahhoz, hogy a tároló jelentések megtekintéséhez. 

A diagnosztikai beállítások nagy méretekben történő létrehozásának egyszerűsítése érdekében (la célként) az Azure Backup beépített [Azure-szabályzatot](https://docs.microsoft.com/azure/governance/policy/)biztosít. Ez a szabályzat egy LA diagnosztikai beállítást ad hozzá egy adott előfizetés vagy erőforráscsoport összes tárolójának. A következő szakaszok a házirend használatával kapcsolatos útmutatást tartalmaznak.

## <a name="supported-scenarios"></a>Támogatott helyzetek

* A szabályzat egy adott előfizetés (vagy az előfizetésen belüli erőforráscsoport) összes Recovery Services-tárolójára egyszerre alkalmazható. A házirendet hozzárendelő felhasználónak "Tulajdonos" hozzáféréssel kell rendelkeznie ahhoz az előfizetéshez, amelyhez a szabályzat hozzá van rendelve.

* A felhasználó által megadott LA-munkaterület (amelyhez diagnosztikai adatokat fog küldeni) lehet egy másik előfizetést a tárolók, amelyekhez a szabályzat van rendelve. A felhasználónak "Reader", "Contributor" vagy "Owner" hozzáféréssel kell rendelkeznie ahhoz az előfizetéshez, amelyben a megadott LA-munkaterület létezik.

* A felügyeleti csoport hatóköre jelenleg nem támogatott.

* A beépített szabályzat jelenleg nem érhető el a nemzeti felhőkben.

## <a name="assigning-the-built-in-policy-to-a-scope"></a>A beépített házirend hozzárendelése hatókörhöz

A szükséges hatókörben lévő tárolók házirendjének hozzárendeléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalon, és keresse meg a **szabályzat** irányítópultját.
2. Válassza **a definíciók a** bal oldali menüben az Azure Resources összes beépített szabályzatának listájának leéséhez.
3. A Category=Monitoring listájának **szűrése.** Keresse meg a **[Preview] nevű házirendet: Telepítse a Recovery Services Vault diagnosztikai beállításait a Log Analytics-munkaterületre az erőforrás-specifikus kategóriákhoz.**

![Házirend-definíció panel](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Kattintson a szabályzat nevére. A házirend részletes definíciója lesz átirányítva.

![Részletes házirend-definíció](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Kattintson a panel tetején található **Hozzárendelés** gombra. Ez átirányítja a **Házirend hozzárendelése** panelre.

6. Az **Alapok csoportban**kattintson a **Hatókör** mező melletti három elemre. Ez megnyit egy megfelelő környezetet, ahol kiválaszthatja a szabályzat hoz a házirendet. Szükség esetén kijelölhet egy erőforráscsoportot is, így a házirend csak egy adott erőforráscsoport tárolóira vonatkozik.

![A házirend-hozzárendelés alapjai](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. A **Paraméterek csoportban**adja meg a következő adatokat:

* **Profilnév** – A házirend által létrehozott diagnosztikai beállításokhoz rendelt név.
* **Log Analytics-munkaterület** – Az a Log Analytics-munkaterület, amelyhez a diagnosztikai beállítást társkell társozni. A házirend-hozzárendelés hatókörében lévő összes tároló diagnosztikai adatai a megadott LA-munkaterületi munkaterületre kerülnek.

* **Kizárási címke neve (nem kötelező) és kizárási címke értéke (nem kötelező)** – Kizárhatja a házirend-hozzárendelésből egy bizonyos címkenevet és -értéket tartalmazó tárolókat. Ha például **nem** szeretné, hogy diagnosztikai beállítás kerülhessen hozzáadásra azokhoz a tárolókhoz, amelyek "isTest" címkéje "igen" értékre van állítva, akkor a **Kizárási címke neve** mezőbe be kell írnia az "isTest" értéket, és az "igen" értéket a **Kizárási címke értéke** mezőbe. Ha a két mező bármelyike (vagy mindkettő) üresen marad, a házirend az összes megfelelő tárolóra vonatkozik, függetlenül a ttól, hogy milyen címkéket tartalmaznak.

![Házirend-hozzárendelési paraméterek](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Szervizelési feladat létrehozása** – Miután a szabályzat egy hatókörhöz van rendelve, az adott hatókörben létrehozott új tárolók automatikusan beszerzik az LA diagnosztikai beállításokat (a tároló létrehozásától számított 30 percen belül). Diagnosztikai beállítás hozzáadása a hatókör meglévő tárolóihoz, a házirend-hozzárendelés időpontjában elindíthatja a szervizelési feladatot. Szervizelési feladat indításához jelölje be a **Szervizelési feladat létrehozása jelölőnégyzetet.** 

![Házirend-hozzárendelés helyreállítása](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Nyissa meg a **Véleményezés+Létrehozás** lapot, és kattintson a **Létrehozás gombra.**

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Milyen feltételek mellett vonatkozik a helyreállítási feladat a tárolóra?

A szervizelési feladat olyan tárolókra vonatkozik, amelyek a szabályzat definíciójának megfelelően nem megfelelőek. A tároló nem megfelelő, ha az alábbi feltételek valamelyikének megfelel:

* Nincs diagnosztikai beállítás a tárolóhoz.
* Diagnosztikai beállítások vannak jelen a tároló, de egyik a beállítások nem rendelkezik az **összes** erőforrás-specifikus események engedélyezve LA célként, és **erőforrás-specifikus** kiválasztott a váltóban. 

Így még akkor is, ha a felhasználó rendelkezik egy tároló az AzureBackupReport esemény engedélyezve azurediagnostics módban (amely támogatja a Biztonsági mentési jelentések), a javítási feladat továbbra is vonatkozik erre a tárolóra, mivel az erőforrás-specifikus mód az ajánlott módja a diagnosztikai beállítások létrehozásának, [megy előre.](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event)

Továbbá, ha a felhasználó rendelkezik egy tároló, amely csak egy részhalmaza a hat erőforrás-specifikus események engedélyezve van, a szervizelési feladat vonatkozik erre a tárolóra, mivel a biztonsági mentési jelentések a várt módon fog működni, ha mind a hat erőforrás-specifikus események engedélyezve vannak.

> [!NOTE]
>
> Ha egy tároló rendelkezik egy meglévő diagnosztikai beállítással, **amelyaz erőforrás-specifikus kategóriák egy részhalmazát** engedélyezve van, és úgy van beállítva, hogy adatokat küldjön egy adott LA-munkaterületre, mondja ki a "Workspace X" parancsot, akkor a javítási feladat sikertelen lesz (csak a tárolóesetében), ha a házirend-hozzárendelésben megadott LA-munkaterület **ugyanaz** a "Workspace X". 
>
>Ennek az az oka, hogy ha az ugyanazon erőforráson lévő két különböző diagnosztikai beállítás által engedélyezett események valamilyen formában **átfedik egymást,** akkor a beállítások nem rendelkezhetnek a célterülettel megegyező LA-munkaterülettel. Ezt a hibát manuálisan kell megoldania, ha a megfelelő tárolóra navigál, és egy másik LA-munkaterülettel rendelkező diagnosztikai beállítást konfigurál a célként.
>
> Vegye figyelembe, hogy a szervizelési feladat **nem** fog meghiúsulni, ha a meglévő diagnosztikai beállítás csak Az AzureBackupReport csak az X munkaterület tel van a cél, mivel ebben az esetben nem lesz átfedés a meglévő beállítás által engedélyezett események és a szervizelési feladat által létrehozott beállítás által engedélyezett események között.

## <a name="next-steps"></a>Következő lépések

* [További információ a Biztonsági másolat készítési jelentések használatáról](https://docs.microsoft.com/azure/backup/configure-reports)
* [További információ az Azure-szabályzatról](https://docs.microsoft.com/azure/governance/policy/)
* [Az Azure Policy használatával automatikusan engedélyezheti a biztonsági mentést az összes virtuális gépszámára egy adott hatókörben](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)
