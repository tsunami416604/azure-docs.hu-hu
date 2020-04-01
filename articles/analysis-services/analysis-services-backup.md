---
title: Az Azure Analysis Services adatbázisának biztonsági mentése és visszaállítása | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan biztonsági másolatot készíthet és állíthat vissza a modell metaadatait és adatait egy Azure Analysis Services-adatbázisból.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 83da2024ab74b705b45a5891f6b40251020dad31
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408656"
---
# <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

A táblázatos modelladatbázisok biztonsági mentése az Azure Analysis Servicesben nagyjából megegyezik a helyszíni analysis services-ekkel. Az elsődleges különbség az, hogy hol tárolja a biztonsági másolat fájljait. A biztonsági másolat fájljait [egy Azure-tárfiók](../storage/common/storage-create-storage-account.md)tárolóba kell menteni. Használhatja a már meglévő tárfiókot és tárolót, vagy létrehozható a kiszolgáló tárolási beállításainak konfigurálásakor.

> [!NOTE]
> A tárfiók létrehozása új számlázható szolgáltatást eredményezhet. További információ: [Azure Storage-díjszabás.](https://azure.microsoft.com/pricing/details/storage/blobs/)
> 
> 

A biztonsági mentések .abf kiterjesztéssel kerülnek mentésre. A memórián belüli táblázatos modellek esetében a modelladatok és a metaadatok is tárolódnak. DirectQuery táblázatos modellek esetén csak a modell metaadatai tárolódnak. A biztonsági mentések tömöríthetők és titkosíthatók, a választott beállításoktól függően.


## <a name="configure-storage-settings"></a>Tárolási beállítások konfigurálása
A biztonsági mentés előtt meg kell adnia a kiszolgáló tárolási beállításait.


### <a name="to-configure-storage-settings"></a>A tárolási beállítások megadása
1.  Az Azure Portal > **Beállítások menüben**kattintson a **Biztonsági másolat gombra.**

    ![Biztonsági mentések a Beállítások ban](./media/analysis-services-backup/aas-backup-backups.png)

2.  Kattintson **az Engedélyezve**, majd **a Tárolási beállítások gombra.**

    ![Bekapcsolás](./media/analysis-services-backup/aas-backup-enable.png)

3. Válassza ki a tárfiókot, vagy hozzon létre egy újat.

4. Jelöljön ki egy tárolót, vagy hozzon létre egy újat.

    ![Tároló kiválasztása](./media/analysis-services-backup/aas-backup-container.png)

5. Mentse a biztonsági mentési beállításokat.

    ![Biztonsági mentési beállítások mentése](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Biztonsági mentés az SSMS használatával

1. Az SSMS-ben kattintson a jobb gombbal egy adatbázisra > **Biztonsági másolatot.**

2. A **Biztonsági másolat segédprogram** > **fájljában**kattintson a **Tallózás gombra.**

3. A **Fájl mentése másként** párbeszédpanelen ellenőrizze a mappa elérési útját, majd írja be a biztonsági másolat nevét. 

4. A **Biztonsági másolat adatbázis** párbeszédpanelen adja meg a beállításokat.

    **Fájlfelülírás engedélyezése** – Ezzel a beállítással felülírja az azonos nevű biztonsági másolatfájlokat. Ha ez a beállítás nincs bejelölve, a menteni kívánt fájl neve nem egyezhet meg egy olyan fájl nevével, amely már létezik ugyanazon a helyen.

    **Tömörítés alkalmazása** – Ezzel a beállítással tömörítheti a biztonsági másolat fájlt. A tömörített biztonsági másolat fájlok lemezterületet takarítanak meg, de valamivel magasabb processzorkihasználtságot igényelnek. 

    **Biztonságimásolat-fájl titkosítása** – Ezzel a beállítással titkosíthatja a biztonsági másolat fájlt. Ehhez a beállításhoz a biztonsági másolat biztonságossá tétele érdekében a felhasználó által megadott jelszó szükséges. A jelszó megakadályozza a biztonsági mentési adatok olvasását a visszaállítási műveleten kívül. Ha úgy dönt, hogy titkosítja a biztonsági másolatokat, tárolja a jelszót biztonságos helyen.

5. A biztonságimásolat-fájl létrehozásához és mentéséhez kattintson az **OK** gombra.


### <a name="powershell"></a>PowerShell
Használja [a Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase) parancsmag.

## <a name="restore"></a>Visszaállítás
Visszaállításkor a biztonságimásolat-fájlnak a kiszolgálóhoz konfigurált tárfiókban kell lennie. Ha egy biztonsági másolat fájlt kell áthelyeznie egy helyszíni helyről a tárfiókba, használja a [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) t vagy az [AzCopy](../storage/common/storage-use-azcopy.md) parancssori segédprogramot. 



> [!NOTE]
> Ha egy helyszíni kiszolgálóról állít be, el kell távolítania az összes tartományi felhasználót a modell szerepköreiből, és hozzá kell adnia őket a szerepkörökhöz Azure Active Directory-felhasználókként.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Visszaállítás ssms használatával

1. Az SSMS-ben kattintson a jobb gombbal a Visszaállítás > **adatbázisra.**

2. A **Biztonsági másolat keresése** párbeszédpanel **Biztonsági másolat fájljában**kattintson a **Tallózás gombra.**

3. Az **Adatbázisfájlok megkeresése** párbeszédpanelen jelölje ki a visszaállítani kívánt fájlt.

4. Az **adatbázis visszaállítása**csoportban jelölje ki az adatbázist.

5. Adja meg a beállításokat. A biztonsági beállításoknak meg kell egyezniük a biztonsági mentés során használt biztonsági mentési beállításokkal.


### <a name="powershell"></a>PowerShell

Használja [a Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase) parancsmag használatát.


## <a name="related-information"></a>Kapcsolódó információk

[Azure Storage-fiókok](../storage/common/storage-create-storage-account.md)  
[Magas rendelkezésre állás](analysis-services-bcdr.md)     
[Az Azure Analysis Services kezelése](analysis-services-manage.md)
