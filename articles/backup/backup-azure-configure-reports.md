---
title: Power BI-jelentések konfigurálása
description: Recovery Services-tároló használatával konfigurálhatja Azure Backup Power BI jelentéseit.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 4f8a2dd927f996ea09e40c7db2e43b46c17f6258
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978372"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-jelentések konfigurálása

Ez a cikk azokat a lépéseket ismerteti, amelyekkel Recovery Services-tároló használatával konfigurálhatja a Azure Backup jelentéseit. Azt is bemutatja, hogyan férhet hozzá a jelentésekhez Power BI használatával. A lépések elvégzése után közvetlenül a Power BI megtekintheti, testreszabhatja és létrehozhatja a jelentéseket.

> [!IMPORTANT]
> 2018. november 1-jét követően egyes ügyfelek problémákat tapasztalhatnak a Power BI Azure Backup alkalmazásában tárolt adatok betöltésekor, amikor is ez a hibaüzenet jelenik meg: „Felesleges karaktereket észleltünk a JSON-bevitel végén. A kivételt az IDataReader-felület jelezte.”
A problémát az adatok a tárfiókba való betöltéséhez használt formátum változása okozza.
A probléma elkerüléséhez töltse le a legújabb alkalmazást (1,8-es verzió).
>
>

## <a name="supported-scenarios"></a>Támogatott esetek

- Azure Backup jelentéseket az Azure-beli virtuális gépek biztonsági mentése, valamint a fájlok és mappák biztonsági mentése a felhőbe az Azure Recovery Services Agent használatával támogatott.
- A Azure SQL Database, az Azure-fájlmegosztás, a Data Protection Manager és a Azure Backup-kiszolgáló jelentései jelenleg nem támogatottak.
- A jelentéseket megtekintheti a tárolók és az előfizetések között, ha ugyanaz a Storage-fiók van konfigurálva az egyes tárakhoz. A kiválasztott Storage-fióknak ugyanabban a régióban kell lennie, mint a Recovery Services-tárolónak.
- A jelentések ütemezett frissítésének gyakorisága 24 óra Power BI. A jelentések igény szerinti frissítését Power BI is végrehajthatja. Ebben az esetben a rendszer a felhasználói Storage-fiók legfrissebb adatfájljait használja a jelentések megjelenítéséhez.

## <a name="prerequisites"></a>Előfeltételek

- Hozzon létre egy [Azure Storage-fiókot](../storage/common/storage-account-create.md) a jelentések konfigurálásához. Ez a Storage-fiók a jelentések kapcsolódó adatainak tárolására szolgál.
- [Hozzon létre egy Power bi fiókot](https://powerbi.microsoft.com/landing/signin/) a saját jelentések megtekintéséhez, testreszabásához és létrehozásához a Power bi portál használatával.
- Regisztrálja az erőforrás-szolgáltatót a **Microsoft. ininsights**szolgáltatásban, ha még nincs regisztrálva. Használja a Storage-fiók és a Recovery Services-tároló előfizetéseit, hogy a jelentéskészítési adatforgalom a Storage-fiókba. Ehhez a lépéshez lépjen a Azure Portal, válassza az **előfizetés** > erőforrás- **szolgáltatók**lehetőséget, és keresse meg a szolgáltatót a regisztráláshoz.

## <a name="configure-storage-account-for-reports"></a>Tárolási fiók konfigurálása a jelentésekhez

Az alábbi lépéseket követve konfigurálhatja a Recovery Services-tárolóhoz tartozó Storage-fiókot a Azure Portal használatával. Ez egy egyszeri konfiguráció. A Storage-fiók konfigurálása után közvetlenül a Power BI megtekintheti a sablon alkalmazást, és jelentéseket is használhat.

1. Ha már nyitva van egy Recovery Services-tároló, folytassa a következő lépéssel. Ha nincs megnyitva Recovery Services-tároló, a Azure Portal válassza a **minden szolgáltatás**lehetőséget.

   - Az erőforrások listájában adja meg a **Recovery Services**.
   - Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Ha **Recovery Services**-tárolót lát, válassza ki.
   - A Recovery Services-tárolók listája megjelenik. A Recovery Services-tárolók listájából válasszon ki egy tárolót.

     Megnyílik a kiválasztott tároló irányítópultja.
2. A tároló alatt megjelenő elemek listájából válassza a **figyelés és jelentések** szakaszban a **biztonsági mentési jelentések** lehetőséget a tárolási fiók jelentésekhez való konfigurálásához.

      ![Biztonsági mentési jelentések kiválasztása menüelem 2. lépés](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. A **biztonsági mentési jelentések** panelen válassza a **diagnosztikai beállítások** hivatkozást. Ez a hivatkozás megnyitja a **diagnosztikai beállítások** felhasználói felületét, amellyel leküldheti az adatait egy ügyfél-Storage-fiókba.

      ![Diagnosztika engedélyezése 3. lépés](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Kattintson a **diagnosztika bekapcsolása** elemre a Storage-fiók konfigurálásához használni kívánt felhasználói felület megnyitásához.

      ![Diagnosztika bekapcsolása – 4. lépés](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. A **név** mezőbe írja be a beállítás nevét. Jelölje be az **archiválás a Storage-fiókba** jelölőnégyzetet, hogy a jelentéskészítési adatok elindulnak a Storage-fiókba.

      ![Diagnosztika engedélyezése 5. lépés](./media/backup-azure-configure-reports/select-setting-name.png)
6. Válassza ki a **Storage-fiók**lehetőséget, válassza ki a megfelelő előfizetést és a Storage-fiókot a listából a jelentéskészítési adatok tárolásához, majd kattintson **az OK gombra**.

      ![Válassza ki a Storage-fiók 6. lépését](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. A **napló** szakaszban jelölje be a **AzureBackupReport** jelölőnégyzetet. A csúszka mozgatásával válassza ki a jelentési adatok megőrzési idejét. A Storage-fiókban tárolt jelentéskészítési adat a csúszka használatával megadott időszakra érvényes.

      ![A Storage-fiók mentése 7. lépés](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Tekintse át az összes módosítást, majd kattintson a **Mentés**gombra. Ez a művelet biztosítja, hogy a módosítások mentése megtörténjen, és a Storage-fiók mostantól a jelentési információk tárolására van konfigurálva.

9. A **diagnosztikai beállítások** táblázat most már megjeleníti az új beállítást, amely engedélyezve van a tárolón. Ha nem jelenik meg, frissítse a táblázatot a frissített beállítás megtekintéséhez.

      ![Diagnosztikai beállítások megtekintése 9. lépés](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Miután a Storage-fiók mentésével konfigurálta a jelentéseket, *várjon 24 órát, amíg* a kezdeti adattovábbítás befejeződik. Importálja a Azure Backup alkalmazást Power BI csak ezt követően. További információt a [Gyakori kérdések című szakaszban](backup-azure-monitor-alert-faq.md)talál.
>
>

## <a name="view-reports-in-power-bi"></a>Jelentések megtekintése Power BI

Miután egy Recovery Services-tároló használatával konfigurálta a jelentésekhez tartozó Storage-fiókot, körülbelül 24 órát vesz igénybe, hogy a jelentéskészítési folyamat elinduljon. A Storage-fiók beállítása után 24 órával a következő lépésekkel tekintheti meg a Power BI jelentéseit.
Ha testre szeretné szabni és megosztani a jelentést, hozzon létre egy munkaterületet, és hajtsa végre a következő lépéseket

1. [Jelentkezzen](https://powerbi.microsoft.com/landing/signin/) be Power BIba.
2. Navigáljon az **alkalmazásokhoz > további alkalmazásokat kaphat a Microsoft Appsource**. A [szolgáltatáshoz való kapcsolódáshoz kövesse az Power bi dokumentációjának](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/)lépéseit.

3. A **keresősáv** mezőbe írja be **Azure Backup** , majd válassza a **Letöltés most**lehetőséget.

      ![Sablon alkalmazásának beolvasása](./media/backup-azure-configure-reports/template-app-get.png)
4. Adja meg az előző lépésben konfigurált Storage-fiók nevét, majd kattintson a **tovább**gombra.

    ![A tárfiók nevének megadása](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)
5. A "Key" hitelesítési módszer használatával adja meg a tárolási fiókhoz tartozó Storage-fiók kulcsát. A Storage-fiók hozzáférési kulcsait a Azure Portal találja. További információ: a [Storage-fiók elérési kulcsainak kezelése](../storage/common/storage-account-keys-manage.md).

     ![Adja meg a Storage-fiókot](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>

6. Válassza **a bejelentkezés**lehetőséget. Miután a bejelentkezés sikeres volt, megjelenik egy adatimportálási értesítés.

    ![Content Pack importálása](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>

    Az importálás befejezése után megjelenik egy **sikeres** értesítés. Ha a Storage-fiókban lévő adatmennyiség nagy, akkor a sablon alkalmazás importálása valamivel hosszabb időt is igénybe vehet.

    ![Sikeres importálási csomag](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. Az adatimportálás sikeres elvégzése után a **Azure Backup** sablon alkalmazás látható az **alkalmazásokban** a navigációs ablaktáblán. Az **irányítópultok**, **jelentések**és **adatkészletek**területen a lista most megjeleníti a Azure Backup.

8. Az **irányítópultok**területen válassza a **Azure Backup**lehetőséget, amely rögzített kulcsú jelentések készletét jeleníti meg.

      ![Azure Backup irányítópult](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. A jelentések teljes készletének megtekintéséhez válasszon ki egy jelentést az irányítópulton.

      ![Azure Backup feladatok állapota](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Válassza ki a jelentések egyes lapjait az adott területen található jelentések megtekintéséhez.

      ![Azure Backup jelentések lapja](./media/backup-azure-configure-reports/reports-tab-view.png)

## <a name="troubleshooting-errors"></a>Hibaelhárítás

| A hiba adatai | Felbontás |
| --- | --- |
| Miután beállította a Storage-fiókot a biztonsági mentési jelentésekhez, a **Storage-fiók** továbbra **sincs konfigurálva**. | Ha sikeresen konfigurálta a Storage-fiókot, a jelentéskészítési folyamat a probléma ellenére meghalad. A probléma megoldásához nyissa meg a Azure Portal, és válassza a **minden szolgáltatás** > **diagnosztikai beállítások** > **Recovery Services** tároló > **szerkesztési beállítás**elemet. Törölje az előzőleg konfigurált beállítást, és hozzon létre egy új beállítást ugyanazon a panelen. Ezúttal a **név** mezőben válassza a **szolgáltatás**elemet. Ekkor megjelenik a konfigurált Storage-fiók. |
|A Azure Backup sablon alkalmazásnak a Power BIban való importálása után a "404-Container nem található" hibaüzenet jelenik meg. | Ahogy azt korábban említettük, 24 órát kell várnia, miután konfigurálta a jelentéseket a Recovery Services-tárolóban, hogy megfelelően megjelenjenek a Power BIban. Ha 24 óra előtt próbál hozzáférni a jelentésekhez, ez a hibaüzenet jelenik meg, mivel a teljes adathalmazok még nem jelennek meg az érvényes jelentések megjelenítéséhez. |

## <a name="next-steps"></a>Következő lépések

A Storage-fiók konfigurálása és a Azure Backup sablon alkalmazás importálása után a következő lépés a jelentések testreszabása és a jelentéskészítési adatmodell használata jelentések létrehozásához. További információt a következő cikkekben talál.

- [Azure Backup jelentéskészítési adatmodell használata](backup-azure-reports-data-model.md)
- [Jelentések szűrése Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
- [Jelentések létrehozása a Power BIban](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
