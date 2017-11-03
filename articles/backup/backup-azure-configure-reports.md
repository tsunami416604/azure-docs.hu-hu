---
title: "Jelentések konfigurálása az Azure Backup szolgáltatásra"
description: "Ez a cikk beszél Power BI-jelentések konfigurálása az Azure Backup használatával Recovery Services-tároló."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 86e465f1-8996-4a40-b582-ccf75c58ab87
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/13/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e817e327b8890c91bd7db640b083fd6c5c11aa14
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-jelentések konfigurálása
Ez a cikk beszél lépéseket jelentések konfigurálása az Azure Backup használatával Recovery Services-tároló, és ezek a jelentések használata a Power BI eléréséhez. A lépések elvégzése után közvetlenül nyissa meg a jelentéseket a Power bi-ba, testreszabása és jelentések létrehozása. 

## <a name="supported-scenarios"></a>Támogatott esetek
1. Az Azure biztonsági mentés jelentések Azure virtuális gépek biztonsági mentéséhez és a fájl vagy mappa biztonsági mentését, a felhőalapú Azure Recovery Services Agent használatával támogatottak.
2. Az Azure SQL, a DPM és az Azure Backup Server jelentések jelenleg nem támogatottak.
3. Jelentések megtekintéséhez tárolók és között előfizetések, ha ugyanazt a tárfiókot az egyes a tárolók van konfigurálva. Kiválasztott tárfiók és recovery services-tárolónak ugyanabban a régióban kell lennie.
4. A jelentések az ütemezett frissítési gyakoriságának a Power BI 24 óra. Egy ad hoc frissítése a jelentést a Power BI, amelyek az ügyfél tárfiókja eset legújabb adatait használják a jelentések megjelenítése is elvégezheti. 

## <a name="prerequisites"></a>Előfeltételek
1. Hozzon létre egy [Azure storage-fiók](../storage/common/storage-create-storage-account.md#create-a-storage-account) konfigurálását a jelentésekben. Ez a tárfiók jelentések kapcsolódó adatok tárolására szolgál.
2. [A Power BI-fiók létrehozása](https://powerbi.microsoft.com/landing/signin/) megtekintése, testreszabása és saját Power BI-portál használatával jelentéseket készíthet.
3. Az erőforrás-szolgáltató regisztrálása **Microsoft.insights** nincs regisztrálva már, ha a tárfiók-előfizetés és a Recovery Services-tároló engedélyezéséhez-előfizetésével jelentési adatok áramlását a tárolási felé fiók. Ehhez az azonos, akkor be kell lépnie az Azure portál > előfizetés > erőforrás-szolgáltatók és ellenőrizze a szolgáltató való bejegyzéséhez. 

## <a name="configure-storage-account-for-reports"></a>Tárfiók jelentések konfigurálása
A következő lépésekkel konfigurálhatja a tárfiók recovery services-tároló Azure-portál használatával. Ez az egyszeri, és ha konfigurálva van a tárfiók, nyissa meg a Power BI tartalomcsomag megtekintése, és kihasználhatják a jelentések és közvetlenül.
1. Ha már rendelkezik nyissa meg a Recovery Services-tároló, folytassa a következő lépéssel. Ha nincs megnyitva egy Recovery Services-tároló, de az Azure portál igen, a központi menüben kattintson a **Tallózás** elemre.

   * Az erőforrások listájába írja be a következőt: **Recovery Services**.
   * Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor meglátja a **Recovery Services-tárolót**, kattintson rá.

      ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     A Recovery Services-tárolók listája megjelenik. A Recovery Services-tárolók listájából válasszon ki egy tárolót.

     Megnyílik a kiválasztott tároló irányítópultja.
2. Az elemek listáját tároló alatt megjelenő, kattintson **biztonsági jelentések** figyelés és jelentéskészítés konfigurálása a storage-fiók a jelentések szakaszban.

      ![Válassza ki biztonsági jelentések menü cikk 2. lépés](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. A biztonsági mentés jelentések paneljén kattintson **konfigurálása** gombra. Ekkor megnyílik az adatok küldése az ügyfél tárfiókja használt Azure Application Insights panel.

      ![Konfigurálja a tárolási fiók lépés 3](./media/backup-azure-configure-reports/configure-storage-account.PNG)
4. Az állapot váltógomb beállítása **a** válassza **archív tárfiókba** jelölőnégyzetet, hogy a jelentési adatok elindíthatja a tárolási fiók folyik.

      ![4. lépés: a diagnosztika engedélyezésével](./media/backup-azure-configure-reports/set-status-on.png)
5. Kattintson a Tárfiók objektumválasztó, és válassza ki a tárfiók tárolásához a jelentési adatokat, és kattintson a listából **OK**.

      ![Válassza ki a tárolási fiók lépés 5](./media/backup-azure-configure-reports/select-storage-account.png)
6. Válassza ki **AzureBackupReport** négyzet jelölését, majd is a csúszkát jelölje be a megőrzési idő a jelentés adatainak. A tárfiókban lévő adatokat Reporting tartják a csúszkával kijelölt időszakra.

      ![Válassza ki a tárolási fiók lépés 6](./media/backup-azure-configure-reports/save-configuration.png)
7. Tekintse át a módosításokat, és kattintson a **mentése** gomb látható, a fenti ábrán látható módon. Ez a művelet biztosítja, hogy a módosítások mentése és a storage-fiók ezzel konfigurálva van a jelentéskészítési adatok tárolására.

> [!NOTE]
> Ha megfelelően konfigurált jelentések úgy, hogy elmenti storage-fiók, akkor **Várjon 24 órát** a kezdeti adatok leküldéses befejezéséhez. Azure biztonsági mentés a tartalomcsomag a Power BI csak az adott idő után kell importálni. Tekintse meg a [feltett](#frequently-asked-questions) további tájékoztatást talál. 
>
>

## <a name="view-reports-in-power-bi"></a>Jelentések megtekintése a Power bi-ban 
Tárfiók konfigurálása jelent a recovery services-tároló használatával, miután vesz igénybe a jelentési adatok körülbelül 24 óra áramló elindításához. A storage-fiók beállítása 24 óra múlva tegye a következőket a Power BI-jelentések megtekintéséhez:
1. [Jelentkezzen be a](https://powerbi.microsoft.com/landing/signin/) Power bi-bA.
2. Kattintson a **adatok beolvasása** , és kattintson a Get **szolgáltatások** tartalomtárban csomag. Az említett lépésekkel [tartalomcsomag eléréséhez a Power BI-dokumentáció](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-packs-services/).

     ![A tartalomcsomag importálása](./media/backup-azure-configure-reports/content-pack-import.png)
3. Típus **Azure biztonsági mentés** keresősávban, és kattintson a **most töltse le innen**.

      ![A tartalomcsomag beolvasása](./media/backup-azure-configure-reports/content-pack-get.png)
4. Adja meg a fenti 5. lépésben beállított a tárfiók nevét, és kattintson a **következő** gombra.

    ![Adja meg a tárfiók neve](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Adja meg a tárfiók hívóbetűjét a tárfiókhoz. Is [megtekintése és másolása a tárelérési kulcsok](../storage/common/storage-create-storage-account.md#manage-your-storage-account) lépjen a tárfiókhoz Azure-portálon. 

     ![Adja meg a storage-fiók](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Kattintson a **bejelentkezés** gombra. Bejelentkezési befejezését követően kap **adatimportálási** értesítést.

    ![Tartalom csomag importálása](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    Némi várakozás után kapott **sikeres** értesítési az importálás után. Azt a csomagot, importálja kissé tovább tarthat, ha nagy mennyiségű a tárfiókban lévő adatokat.
    
    ![Sikeres tartalom csomag importálása](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. Adatok importálása sikeresen megtörtént, miután **Azure biztonsági mentés** tartalomcsomag is elérhetővé válik a **alkalmazások** a navigációs ablaktáblán. A listán látható Azure biztonsági mentés irányítópult, jelentések és adatkészlet most az újonnan importált jelentések jelző sárga csillag látható. 

     ![Az Azure biztonsági mentés tartalomcsomag](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. Kattintson a **Azure biztonsági mentés** az irányítópultokat, amely mutatja a rögzített kulcs jelentések.

      ![Az Azure biztonsági mentés irányítópult](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Olyan jelentések megtekintéséhez kattintson a jelentés az irányítópulton.

      ![Az Azure biztonsági mentési feladat állapota](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Kattintson az egyes lapokon az adott területre jelentéseket szeretne megjeleníteni a jelentésekben.

      ![Az Azure biztonsági mentés jelentések lap](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Gyakori kérdések
1. **Hogyan állapítható meg, ha a jelentéskészítési adatok elindult, halad a tárfiók?**
    
    Nyissa meg a tárfiók konfigurálva, és válassza ki a tárolók. A tároló szerepel egy bejegyzés insights-logs-azurebackupreport, azt jelzi, hogy jelentésadatait indult továbbítására.

2. **Mi az az adatleküldés tárfiók és az Azure Backup a Power BI tartalomcsomag gyakoriságát?**

   A 0 nap felhasználók kellene elküldik az adatokat a tárfiók körülbelül 24 óra. Ha a kezdeti leküldéses compelete, az adatok az alábbi ábrán látható, a következő gyakorisággal frissülnek. 
      * Kapcsolódó adatok **feladatok, a riasztások, a biztonsági mentés elemek, a tárolók, a védett kiszolgálók és a házirendek** a rendszer előkészítésre továbbít ügyfél tárfiókja, és amikor a rendszer naplózza.
      * Kapcsolódó adatok **tárolási** ügyfél tárfiókja 24 óránként kerül.
   
    ![Az Azure biztonsági mentés jelentések adatok leküldéses gyakorisága](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  A Power BI rendelkezik egy [ütemezett frissítés naponta egyszer](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Az adatok manuális frissítési hajtható végre a tartalomcsomag a Power bi-ban.

3. **Mennyi ideig megőrizhetem a jelentéseket?** 

   Tárolási fiók konfigurálása során kiválaszthatja jelentési adatok megőrzési idővel a storage-fiókra (6. lépés a tárfiók konfigurálása a jelentések szakaszban). Amellett, hogy a is [elemzési jelentések az excel-](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) , és mentheti azokat hosszabb megőrzési időt, az igényeinek megfelelően. 

4. **Látható a jelentésekben lévő adatok a storage-fiók beállítása után?**

   Után létrehozott összes adatokat **"konfigurálása a storage-fiók"** leküldött értesítést a tárolási fiók és a jelentésekben lesznek. Azonban **nem elküldte azokat a folyamatban lévő feladatok** a jelentéskészítéshez. Ha a feladat befejeződik, vagy nem sikerül, jelentések továbbítja.

5. **Ha már konfigurált jelentéseket szeretne megjeleníteni a tárfiókot, I módosítható a konfigurációt szeretne használni a másik tárolási fiókot?** 

   Igen, módosíthatja a konfiguráció során eltérő tárfiók mutasson. Az újonnan konfigurált tárfiókot kell használni, Azure biztonsági mentés tartalomcsomag való kapcsolódás során. Is ha konfigurálva van egy másik tárolási fiókot, új lenne adatfolyam a ezt a tárfiókot. Azonban a régebbi adatokat (előtt a konfiguráció módosítása) továbbra is régebbi tárfiókban.

6. **Tekinthetők jelentések tárolók és előfizetések között?** 

   Igen, különböző tárolók kereszt-tároló-jelentéseket szeretne megjeleníteni a tárfiókon is konfigurálhatók. A tárolók ugyanazt a tárfiókot is, előfizetések is konfigurálhatók. Ezután használhatja ezt a tárfiókot Azure biztonsági mentés a tartalomcsomag a Power bi-ban való kapcsolódás során a jelentések megtekintéséhez. A kiválasztott tárolási fiók azonban és recovery services-tárolónak ugyanabban a régióban kell lennie.
   
## <a name="troubleshooting-errors"></a>Kapcsolatos hibák elhárítása
| Hiba legutolsó részletes adatai | Megoldás: |
| --- | --- |
| Biztonsági mentés a jelentéseknél a storage-fiók beállítása után **Tárfiók** továbbra is mutatja **nincs konfigurálva**. | Ha a tárfiók sikeresen konfigurálta, a jelentéskészítési adatok a erdőtől áramolnak annak ellenére, hogy a probléma. A probléma megoldásához keresse fel az Azure portál > több szolgáltatások > diagnosztikai beállítások > RS tároló > beállítás szerkesztése. Törli a korábban konfigurált beállítást, és hozzon létre egy új beállítás azonos paneljén. Most, állítsa be a mezőt **neve** való **szolgáltatás**. Ez a beállított tárfiókot kell megjelennie. |
|Azure biztonsági mentés importálása után content pack a Power bi-ban, a hiba **404-tároló nem található** ki. | Javasolt ebben a dokumentumban meg kell várni a jelentések Recovery Services-tároló megfelelően láthatók a Power bi-ban való beállítása után 24 óra. Ha megpróbál hozzáférni a jelentések előtt 24 óra, mivel teljes adatok még nem található érvényes jelentések megjelenítéséhez elérhetővé válik a hiba. |

## <a name="next-steps"></a>Következő lépések
Most, hogy a tárfiók és az importált Azure Backup-tartalomcsomag van beállítva, a következő lépésre, ezek a jelentések testreszabása és jelentések létrehozásához használja a jelentési adatokat az adatmodellbe. További részletekért tekintse meg a következő cikkekben talál.

* [Adatmodell reporting Azure Backup segítségével](backup-azure-reports-data-model.md)
* [Szűrés a jelentéseket a Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Jelentések létrehozása a Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

