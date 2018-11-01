---
title: Az Azure Backup-jelentések konfigurálása
description: Power BI-jelentések az Azure Backup konfigurálása a Recovery Services-tároló használatával.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: adigan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 493a8881975e6b7568a7823bfc86fc97b4389378
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418279"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-jelentések konfigurálása
Ez a cikk bemutatja a lépést kell végrehajtania a jelentések az Azure Backup konfigurálása a Recovery Services-tároló használatával. Azt is bemutatja, hogyan lehet hozzáférni a jelentésekhez a Power BI használatával. Miután elvégezte ezeket a lépéseket, megnyithatja közvetlenül a Power BI segítségével megtekintheti, testreszabása és jelentéseket hozhat létre.

> [!IMPORTANT]
> Az 1. November, 2018 egyes ügyfelek jelenhetnek problémák egy részét a Power bi-ban, amely arról tájékoztat, "felesleges karaktereket észleltünk a JSON-bevitel végén az Azure Backup alkalmazásban az adatok betöltése. A kivétel lett kiváltva az IDataReader felület okozta."
Ez az a következő formátumban, ahol-adatok betöltése a tárfiókba megváltozása miatt.
Töltse le a legújabb alkalmazást (1.8-as) a probléma elkerülése érdekében.
>
>

## <a name="supported-scenarios"></a>Támogatott esetek
- Az Azure Backup-jelentések támogatottak az Azure virtuális gépek biztonsági mentésének és a fájl és mappa biztonsági mentés a felhőbe az Azure Recovery Services Agent használatával.
- Az Azure SQL Database, Azure-beli fájlmegosztásokat, a Data Protection Manager és az Azure Backup server jelentések jelenleg nem támogatottak.
- Tárolók és az előfizetések között megtekintheti jelentéseket, ha ugyanazt a tárfiókot az egyes a tárolók van konfigurálva. A kiválasztott tárfiók és a Recovery Services-tárolónak ugyanabban a régióban kell lennie.
- A jelentések ütemezett frissítésének gyakoriságát a Power BI 24 óra. Egy ad-hoc frissítése a jelentéseket a Power bi-ban hajthat végre. Ebben az esetben az ügyfél tárfiókja a legfrissebb adatok jelentések előállítására szolgál.

## <a name="prerequisites"></a>Előfeltételek
- Hozzon létre egy [Azure storage-fiók](../storage/common/storage-quickstart-create-account.md) jelentések konfigurálásához. Ez a tárfiók jelentések kapcsolódó adatok tárolására szolgál.
- [Hozzon létre egy Power BI-fiókkal](https://powerbi.microsoft.com/landing/signin/) megtekintése, testre szabhatja, és létrehozhatja saját jelentéseit a Power BI portálon.
- Az erőforrás-szolgáltató regisztrálása **Microsoft.insights**, ha már nincs regisztrálva. Az előfizetések használja a tárfiókot és a Recovery Services-tárolót, hogy a jelentési adatok áramlását a tárfiók is. Ehhez a lépéshez, lépjen az Azure Portalon, válassza a **előfizetés** > **erőforrás-szolgáltatók**, és ellenőrizze a szolgáltató való bejegyzéséhez.

## <a name="configure-storage-account-for-reports"></a>Tárfiók jelentések konfigurálása
Kövesse az alábbi lépéseket egy Recovery Services-tárolót a storage-fiók konfigurálása az Azure portal használatával. Ez az egyszeri. Miután konfigurálta a tárfiókot, megnyithatja közvetlenül a Power BI-tartalomcsomag megtekintése és jelentések használata.
1. Ha már rendelkezik nyissa meg a Recovery Services-tárolót, lépjen a következő lépéssel. Ha nem rendelkezik az Azure Portalon nyissa meg a Recovery Services-tároló kiválasztása **minden szolgáltatás**.

   * Az erőforrások listájába írja be a **Recovery Services**.
   * Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor látja **Recovery Services-tárolók**, jelölje ki azt.

      ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

   * A Recovery Services-tárolók listája megjelenik. A Recovery Services-tárolók listájából válasszon ki egy tárolót.

     Megnyílik a kiválasztott tároló irányítópultja.
2. Alatt a tárolóhoz, a megjelenő elemek listájában a **figyelés és jelentéskészítés** szakaszban jelölje be **Backup-jelentések** jelentések tartozó tárfiók konfigurálásához.

      ![Select Backup-jelentések menüben cikk 2. lépés](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Az a **Backup-jelentések** panelen válassza ki a **diagnosztikai beállítások** hivatkozásra. Ez a hivatkozás megnyitja a **diagnosztikai beállítások** felhasználói felület, amelyet adatok leküldése egy ügyfél tárfiókja.

      ![Engedélyezze a diagnosztikát a 3. lépés](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Válassza ki **diagnosztika bekapcsolása** használandó tárfiók konfigurálása a felhasználói felületének megnyitásához. 

      ![4. lépés diagnosztika bekapcsolása](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Az a **neve** adja meg a beállítás nevét. Válassza ki a **archiválás tárfiókba** jelölőnégyzetet, hogy az adatok megkezdheti a tárfiókba kerülnek a jelentéskészítés.

      ![Engedélyezze a diagnosztikát az 5. lépés](./media/backup-azure-configure-reports/select-setting-name.png)
6. Válassza ki **tárfiók**, a megfelelő előfizetést, és a storage-fiók jelentéskészítési adatok tárolására szolgáló a listából válassza ki és **OK**.

      ![Válassza ki a tárolási fiók 6 lépés](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Alatt a **Log** szakaszban jelölje be a **AzureBackupReport** jelölőnégyzetet. Mozgassa a csúszkát a jelentési adatok megőrzési időt. A tárfiókban lévő adatokról szóló jelentéseket küldeni a csúszka használatával kiválasztott ideig megőrizni.

      ![Tárolási fiók lépést, 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Tekintse át az összes módosítást, és válassza ki **mentése**. Ez a művelet biztosítja, hogy az összes módosítást a rendszer menti, és a storage-fiókot most már van konfigurálva a jelentési adatok tárolására.

9. A **diagnosztikai beállítások** a táblázat most már az új beállítás engedélyezve van a tárolóhoz. Ha nem jelenik meg, frissítse a táblázat frissített beállítást.

      ![Diagnosztikai beállítás 9. lépés.](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> A storage-fiókban mentésével jelentések konfigurálása után *Várjon 24 órát* a kezdeti adatok leküldés befejezéséhez. Importálja az Azure Backup-alkalmazás a Power BI csak az adott időpont után. További információkért lásd: a [gyakori kérdésekkel foglalkozó szakaszban](#frequently-asked-questions). 
>
>

## <a name="view-reports-in-power-bi"></a>Jelentések megtekintése a Power bi-ban 
Miután egy tárfiókot a jelentések egy Recovery Services-tároló használatával konfigurálta, indítsa el a beérkező jelentési adatok körülbelül 24 óra vesz igénybe. Storage-fiókot, 24 óra után kövesse az alábbi lépéseket a Power BI-jelentések megtekintéséhez.
Ha testre szabhatja, és megosztja a jelentést, hozzon létre egy munkaterületet, és tegye a következőket

1. [Jelentkezzen be a](https://powerbi.microsoft.com/landing/signin/) a Power bi-bA.
2. Válassza az **Adatok lekérése** lehetőséget. Az a **további lehetőségek a saját tartalmak létrehozására**válassza **szolgáltatás-tartalomcsomagok**. Kövesse a [kapcsolódni egy szolgáltatáshoz a Power BI dokumentációja](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

3. Az a **keresési** sáv, adja meg **Azure Backup** válassza **Letöltés most**.

      ![Tartalomcsomag beszerzése](./media/backup-azure-configure-reports/content-pack-get.png)
4. Adja meg a storage-fiókot, amelyet az előző lépésben 5 nevét, és válassza ki **tovább**.

    ![A tárfiók nevének megadása](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. "Key" hitelesítési módszert használ, adja meg a tárfiókhoz a tárfiók-kulcsot. A [megtekintése és másolása a tárelérési kulcsok](../storage/common/storage-account-manage.md#access-keys), lépjen a tárfiókhoz az Azure Portalon. 

     ![Adja meg a storage-fiók](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Válassza ki **jelentkezzen be a**. Jelentkezzen be a sikeres után megjelenik egy importáló adatok értesítés.

    ![Csomag importálása](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    Miután az importálás befejeződik, megjelenik egy **sikeres** értesítést. A storage-fiókban lévő adatok mennyisége túl nagy, ha a tartalomcsomag importálja egy kicsit tovább tarthat.
    
    ![Sikeres tartalomcsomag importálása](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. Miután sikeresen, importálja az adatokat a **Azure Backup** tartalomcsomag jelenik meg a **alkalmazások** a navigációs ablaktáblán. Alatt **irányítópultok**, **jelentések**, és **adatkészletek**, a lista most az Azure Backup látható.
     
8. Alatt **irányítópultok**válassza **Azure Backup**, amely mutat be rögzített kulcs jelentések.

      ![Az Azure Backup-irányítópult](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Jelentések teljes készletének megtekintéséhez jelölje ki bármelyik jelentésre az irányítópulton.

      ![Az Azure biztonsági mentési feladat állapota](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Válassza ki a jelentéseken megtekintheti a Jelentések területen található minden egyes lapot.

      ![Az Azure Backup-jelentések lap](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Hogyan tudom ellenőrizni, ha a jelentési adatok a tárfiókba kerülnek egy megkezdődött?**
    
   Nyissa meg a konfigurált tárfiók, és válassza ki a tárolók. Ha a container insights-logs-azurebackupreport bejegyzést, azt jelzi, hogy vonatkozó adatokról szóló jelentéseket indult tárfiókba.

**Mi az a gyakoriságát, adatleküldés egy storage-fiókot és az Azure Backup-tartalomcsomaghoz a Power bi-ban?**

   A felhasználók nap 0 vesz igénybe körülbelül 24 óra adatok leküldése egy storage-fiókot. A kezdeti leküldéses befejezése után az adatok frissülnek a gyakoriságú, az alábbi ábrán látható. 
      
* Kapcsolódó adatok **feladatok**, **riasztások**, **biztonsági másolati elemek**, **tárolók**, **védett kiszolgálók**, és  **Házirendek** át lett helyezve egy ügyfél tárfiókja, amikor azt a naplózására akkor kerül sor.
      
* Kapcsolódó adatok **tárolási** át lett helyezve egy ügyfél tárfiókja 24 óránként.
   
   ![Az Azure Backup-jelentések adatok leküldéses gyakorisága](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

* A Power BI rendelkezik egy [az ütemezett frissítés naponta egyszer](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Az adatok manuális frissítés a tartalomcsomag a Power BI is végrehajthatja.

**Mennyi ideig őrizheti meg a jelentéseket?**

   Amikor konfigurál egy tárfiókot, kiválaszthatja a jelentés adatai olyan megőrzési időszakot a storage-fiókban. Kövesse az előző szakaszban "Jelentések Configure storage account" 6. lépés. Emellett képes [elemzése az Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) , és mentse őket a hosszabb megőrzési időt, az igényei szerint.

**A jelentésekben lévő adatok után a tárfiók konfigurálható látható?**

   Storage-fiók beállítása után létrehozott összes adatok a storage-fiók át lett helyezve, és jelentésekben érhető el. Folyamatban lévő feladatok rendszer nem küldi le a jelentéskészítéshez. Miután a feladat befejeződik, vagy nem sikerül, a jelentések zajlik.

**Ha már konfigurálta a tárfiókot,-jelentések megtekintéséhez, módosítható a konfigurációt egy másik tárfiók használatára?**

   Igen, módosíthatja a konfigurációt, hogy egy másik tárfiók mutasson. Az újonnan beállított storage-fiókot használja, amíg csatlakozik az Azure Backup-tartalomcsomaghoz. Miután egy másik tárfiók van konfigurálva, az új adatok folyamatok ezt a tárfiókot. Régebbi adatokat (mielőtt módosítaná a konfigurációt) továbbra is a korábbi tárfiókban maradnak.

**Jelentések tekinthetem tárolók és -előfizetések között?**

   Igen, cross-tároló jelentések megtekintéséhez a különböző tárolók ugyanazt a tárfiókot is konfigurálhatók. Konfigurálhatja a tárolók esetében ugyanazt a tárfiókot is, előfizetések között. Ezt követően használhatja ezt a tárfiókot, csatlakozik az Azure Backup-tartalomcsomaghoz a Power BI-jelentések megtekintése közben. A kiválasztott tárfiók és a Recovery Services-tárolónak ugyanabban a régióban kell lennie.
   
## <a name="troubleshooting-errors"></a>Hibaelhárítás
| A hiba részletei | Megoldás: |
| --- | --- |
| A Backup-jelentések, a storage-fiók beállítása után **tárfiók** még mindig **nincs konfigurálva**. | Ha sikeresen konfigurálta a tárfiókot, a jelentési adatok folyásirányával kapcsolatban, annak ellenére, hogy a probléma. A probléma megoldásához lépjen az Azure Portalon, és válassza a **minden szolgáltatás** > **diagnosztikai beállítások** > **Recovery Services-tároló**  >  **Beállítás szerkesztése**. Törli a korábban konfigurált beállítást, és a egy új beállítás létrehozásához ugyanezen paneljén. Ezúttal a a **neve** jelölje ki **szolgáltatás**. Most már megjelenik a konfigurált tárfiók. |
|Miután importált az Azure Backup-tartalomcsomaghoz a Power BI, a "404-tároló nem található" hiba jelenik meg. | Ahogy korábban említettük 24 órában a Recovery Services-tároló megfelelően megjelenik a Power bi-ban a jelentések beállítása után meg kell várnia. Ha megpróbálja elérni a jelentések előtt 24 óra, ez a hibaüzenet jelenik meg, mert mindazok az adatok még nem található érvényes jelentések megjelenítéséhez. |

## <a name="next-steps"></a>További lépések
Miután a tárfiók konfigurálása és importálása az Azure Backup-tartalomcsomaghoz, a következő lépések, testre szabhatja a jelentéseket és a egy olyan jelentés-modell segítségével jelentéseket hozhat létre. További információkért tekintse meg a következő cikkeket.

* [Az adatmodell jelentéskészítés az Azure Backup használata](backup-azure-reports-data-model.md)
* [Szűrő jelentéseket a Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Jelentések létrehozása a Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

