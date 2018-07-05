---
title: Az Azure Backup-jelentések konfigurálása
description: A Power BI-jelentések konfigurálása az Azure Backup helyreállítási tár használatával.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: adigan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81653f9125b9cc4411e5cfe358bd602f92c5bf89
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448366"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-jelentések konfigurálása
Ez a cikk ismerteti lépések jelentések konfigurálása az Azure Backup helyreállítási tár használatával, és ezeket a jelentéseket a Power BI segítségével történő eléréséhez. Után hajt végre ezeket a lépéseket, a Power BI összes jelentés megtekintését, testre szabhatja, és jelentéseket hozhat létre közvetlenül léphet. 

## <a name="supported-scenarios"></a>Támogatott esetek
1. Az Azure Backup-jelentések az Azure virtuális gépek biztonsági mentése és a fájl/mappa biztonsági mentése a felhőbe az Azure Recovery Services Agent használata esetén támogatottak.
2. Azure SQL, a DPM és az Azure Backup Server jelentések jelenleg nem támogatottak.
3. -Jelentések megtekintéséhez tárolók és között előfizetéseket, ha ugyanazt a tárfiókot az egyes a tárolók van konfigurálva. Kiválasztott tárfiók és recovery services-tárolónak ugyanabban a régióban kell lennie.
4. A jelentések ütemezett frissítésének gyakoriságát a Power BI 24 óra. Egy ad-hoc frissítése a jelentéseket a Power bi-ban, amely megkülönbözteti a kis legfrissebb adatokat az ügyfél tárfiókja szolgál jelentések megjelenítése is elvégezheti. 
5. Az Azure Backup-jelentések jelenleg nem támogatottak a nemzeti felhőkben.

## <a name="prerequisites"></a>Előfeltételek
1. Hozzon létre egy [Azure storage-fiók](../storage/common/storage-create-storage-account.md#create-a-storage-account) jelentések konfigurálásához. Ez a tárfiók jelentések kapcsolódó adatok tárolására szolgál.
2. [Hozzon létre egy Power BI-fiókkal](https://powerbi.microsoft.com/landing/signin/) megtekintése, testre szabhatja, és létrehozhatja saját jelentéseit a Power BI-portál használatával.
3. Az erőforrás-szolgáltató regisztrálása **Microsoft.insights** nincs regisztrálva a már, ha az előfizetéshez a storage-fiók és a helyreállítási tár engedélyezése az előfizetés jelentési adatok áramlását a storage fiók. Ugyanez a teendője, akkor be kell lépnie az Azure portal > előfizetés > erőforrás-szolgáltatókat és ellenőrizze a szolgáltató való bejegyzéséhez. 

## <a name="configure-storage-account-for-reports"></a>Tárfiók jelentések konfigurálása
A következő lépéseket követve konfigurálja a tárfiók a recovery services-tárolót az Azure portal használatával. Az egyszeri, és Miután konfigurálta a tárfiókot, megnyithatja közvetlenül a tartalomcsomag megjelenítése, és használja ki a jelentéseket a Power bi-ban.
1. Ha már rendelkezik nyissa meg a Recovery Services-tároló, folytassa a következő lépéssel. Ha nem rendelkezik egy Recovery Services-tároló nyílt, de az Azure Portal, kattintson a **minden szolgáltatás**.

   * Az erőforrások listájába írja be a következőt: **Recovery Services**.
   * Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor meglátja a **Recovery Services-tárolót**, kattintson rá.

      ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     A Recovery Services-tárolók listája megjelenik. A Recovery Services-tárolók listájából válasszon ki egy tárolót.

     Megnyílik a kiválasztott tároló irányítópultja.
2. A tároló alatt megjelenő elemek listában, kattintson az **Backup-jelentések** jelentések tartozó tárfiók konfigurálása a figyelés és jelentéskészítés szakasz alatt.

      ![Select Backup-jelentések menüben cikk 2. lépés](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. A Backup-jelentések panelen kattintson a **diagnosztikai beállítások** hivatkozásra. Ekkor megnyílik a diagnosztikai beállítások felhasználói felület, amely adatok leküldése az ügyfél tárfiókja használható.

      ![Engedélyezze a diagnosztikát a 3. lépés](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Kattintson a hivatkozásra **diagnosztika bekapcsolása**. Ekkor megnyílik a felhasználói felület tárfiók konfigurálásához. 

      ![4. lépés diagnosztika bekapcsolása](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. A mezőben adja meg a beállítás neve **neve** válassza **Archive Storage-fiókba** jelölőnégyzetet, hogy a jelentési adatok megkezdheti a tárfiókba áramlanak.

      ![Engedélyezze a diagnosztikát az 5. lépés](./media/backup-azure-configure-reports/select-setting-name.png)
6. Kattintson a Tárfiók-választó, és válassza ki a megfelelő előfizetést és a storage-fiók tárolására a jelentési adatokat, majd kattintson a listában **OK**.

      ![Válassza ki a tárolási fiók 6 lépés](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Válassza ki **AzureBackupReport** jelölje be a jelölőnégyzetet a napló szakasz alatt, és húzza a csúszkát válassza megőrzési időszak a jelentés adatainak. A tárfiókban lévő adatokról szóló jelentéseket küldeni a csúszkával kiválasztott ideig megőrizni.

      ![Tárolási fiók lépést, 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Tekintse át az összes módosítást, és kattintson a **mentése** gombra a képernyő felső részén, a fenti ábrán látható módon. Ez a művelet biztosítja, hogy minden a módosítások mentése és a jelentési adatok tárolására szolgáló most már konfigurálta a tárfiókot.

9. A diagnosztikai beállítások táblázat ekkor látnia kell az új beállítás engedélyezve van a tárolóhoz. Ha ez nem jelenik meg, frissítse a frissített beállítást a tábla.

      ![Diagnosztikai beállítás 9. lépés.](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Ha jelentéseket menti a storage-fiókot konfigurál, meg kell **Várjon 24 órán keresztül** kezdeti adatok leküldés végrehajtásához. Csak az adott időpont után importálnia kell az Azure Backup-tartalomcsomaghoz a Power bi-ban. Tekintse meg [gyakori kérdésekkel foglalkozó szakaszban](#frequently-asked-questions) további tájékoztatást talál. 
>
>

## <a name="view-reports-in-power-bi"></a>Jelentések megtekintése a Power bi-ban 
Miután a tárfiók konfigurálása a jelentéseket a helyreállítási tár használatával, vesz igénybe az adatok körülbelül 24 óra indítsa el a tárfiókba az. Storage-fiók beállításának 24 óra elteltével használja a Power BI-jelentések megtekintéséhez a következő lépéseket:
1. [Jelentkezzen be a](https://powerbi.microsoft.com/landing/signin/) a Power bi-bA.
2. Kattintson a **adatok lekérése** kattintson **első** alatt **szolgáltatások** a tartalomtár csomag. Az említett lépésekkel [hozzá a tartalomcsomag a Power BI dokumentációja](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-packs-services/).

     ![Csomag importálása](./media/backup-azure-configure-reports/content-pack-import.png)
3. Típus **Azure Backup** a keresési sávon kattintson **Letöltés most**.

      ![Tartalomcsomag beszerzése](./media/backup-azure-configure-reports/content-pack-get.png)
4. Adja meg a tárfiók nevét a fenti 5. lépésben konfigurált, és kattintson a **tovább** gombra.

    ![A tárfiók nevének megadása](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Adja meg a tárfiókhoz a tárfiók-kulcsot. Is [megtekintése és másolása a tárelérési kulcsok](../storage/common/storage-create-storage-account.md#manage-your-storage-account) lépjen a tárfiókhoz az Azure Portalon. 

     ![Adja meg a storage-fiók](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Kattintson a **jelentkezzen be a** gombra. Jelentkezzen be a sikeres után kap **adatok importálása** értesítést.

    ![Csomag importálása](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    Némi várakozás után kap **sikeres** az importálás befejezése után értesítést. Most már importálhatja a tartalomcsomag eltarthat, ha nagy mennyiségű adatot a storage-fiókban.
    
    ![Sikeres tartalomcsomag importálása](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. Adatok importálása sikeresen megtörtént, miután **Azure Backup** tartalomcsomag jelenik meg a **alkalmazások** a navigációs ablaktáblán. A listán megjelenik egy sárga csillag jelzi az újonnan importált jelentések az Azure Backup-irányítópultot, jelentéseket és adatkészletet most. 

     ![Az Azure Backup-tartalomcsomaghoz](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. Kattintson a **Azure Backup** alatt irányítópultokat, amelyek mutat be rögzített kulcs jelentések.

      ![Az Azure Backup-irányítópult](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Jelentések teljes készletének megtekintéséhez kattintson az irányítópult jelentéseket.

      ![Az Azure biztonsági mentési feladat állapota](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Kattintson minden lapon a Jelentések területen található jelentések megtekintéséhez.

      ![Az Azure Backup-jelentések lap](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Gyakori kérdések
1. **Hogyan ellenőrizhető, ha jelentési adatok a storage-fiókra beérkező megkezdődött?**
    
    Nyissa meg a tárfiók konfigurálva, és válassza ki a tárolókat. Ha a container insights-logs-azurebackupreport bejegyzést, azt jelzi, hogy vonatkozó adatokról szóló jelentéseket indult tárfiókba.

2. **Mi az a tárfiók és az Azure Backup-tartalomcsomaghoz a Power bi-ban történő adatleküldés gyakorisága?**

   A felhasználók nap 0 vesz igénybe a körülbelül 24 óra adatok leküldése a storage-fiókba. A kezdeti leküldéses befejeződése után az adatok frissülnek a következő gyakorisággal, az alábbi ábrán látható. 
      * Kapcsolódó adatok **feladatok, a riasztások, a biztonsági másolati elemek, a tárolók, a védett kiszolgálók és a szabályzatok** ügyfél tárfiókja, és mikor naplózza a rendszer továbbítja.
      * Kapcsolódó adatok **tárolási** leküldött 24 óránként ügyfél tárfiókja.
   
    ![Az Azure Backup-jelentések adatok leküldéses gyakorisága](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  A Power BI rendelkezik egy [az ütemezett frissítés naponta egyszer](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Az adatok manuális frissítés a tartalomcsomag a Power BI is végrehajthatja.

3. **Mennyi ideig őrizheti meg a jelentéseket?** 

   Storage-fiók beállításakor válassza ki megőrzési időszak jelentési adatok a storage-fiókban (a tárfiók konfigurálása a 6. lépés használata a jelentések szakasz fenti). Amellett, hogy is [jelentések elemzés az excel-](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) , és mentse őket a hosszabb adatmegőrzési idő, az igényeinek megfelelően. 

4. **Látható a jelentésekben lévő adatok a storage-fiók beállítása után?**

   Az összes után létrehozott adatok **"konfigurálása a storage-fiók"** lesz küldve a storage-fiókot, és a jelentésekben használható. Azonban **nem kerülnek a folyamatban lévő feladatok** a jelentéskészítéshez. Miután a feladat befejeződik, vagy nem sikerül, a jelentések zajlik.

5. **Ha már konfigurálta a tárfiókot,-jelentések megtekintéséhez, I módosítható a konfigurációt egy másik tárfiók használatára?** 

   Igen, módosíthatja a konfigurációt, hogy egy másik tárfiók mutasson. Az újonnan beállított storage-fiókot kell használnia az Azure Backup-tartalomcsomaghoz való kapcsolódás közben. Is ha egy másik tárfiók már konfigurálva van, az új adatok lenne a flow a ezt a tárfiókot. Azonban a régebbi adatokat (a konfiguráció módosítása) előtt továbbra is a korábbi tárfiókban.

6. **Tekinthetem jelentések, tárolók és az előfizetések között?** 

   Igen, cross-tároló jelentések megtekintéséhez a különböző tárolók ugyanazt a tárfiókot is konfigurálhatók. Konfigurálhatja a tárolók esetében ugyanazt a tárfiókot is, előfizetések között. Ezután használhatja ezt a tárfiókot az Azure Backup-tartalomcsomaghoz a Power bi-ban való kapcsolódás során a jelentések megtekintéséhez. A kiválasztott tárfiók azonban és recovery services-tárolónak ugyanabban a régióban kell lennie.
   
## <a name="troubleshooting-errors"></a>Hibaelhárítás
| Hiba részletei | Megoldás: |
| --- | --- |
| A Backup-jelentések, a storage-fiók beállítása után **Tárfiók** még mindig **nincs konfigurálva**. | Ha sikeresen konfigurálta a tárfiókot, a jelentési adatok átkerülnek a annak ellenére, hogy a probléma. A probléma megoldásához nyissa meg az Azure portal > minden szolgáltatás > diagnosztikai beállítások > r-tároló > beállítás szerkesztése. Törli a korábban konfigurált beállítást, és létrehozhat egy új beállítás ugyanaz a panel. Most állítsa a mező **neve** való **szolgáltatás**. Ez meg kell jelennie a konfigurált tárfiók. |
|Az Azure Backup az importálás után tartalomcsomaghoz a Power BI, a hiba **404-tároló nem található** betölt. | Javasolt ebben a dokumentumban meg kell várnia a Recovery Services-tároló megfelelően megjelenik a Power BI jelentések konfigurálását követő 24 órában. Ha megpróbálja elérni a jelentések előtt 24 óra, ezt a hibát kap, mivel mindazok az adatok még nem található érvényes jelentések megjelenítéséhez. |

## <a name="next-steps"></a>További lépések
Most, hogy konfigurálta a tárfiókot és az importált az Azure Backup-tartalomcsomaghoz, a következő lépésre, ezek a jelentések testreszabásához, és a jelentések létrehozásához használja a jelentéskészítési adatmodellt. További részletekért tekintse meg a következő cikkekben talál.

* [Adatmodell jelentéskészítés az Azure Backup használatával](backup-azure-reports-data-model.md)
* [A Power BI-jelentések szűrése](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Jelentések létrehozása a Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

