---
title: Az Azure Analysis Services-adatbázis biztonsági mentése és visszaállítása |} A Microsoft Docs
description: Ismerteti, hogyan lehet biztonsági mentése és visszaállítása egy Azure Analysis Services-adatbázisból.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b88660548feaa1d3a8e83e633f798212c80ac6dc
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190306"
---
# <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Az Azure Analysis Services táblázatos modellű adatbázisainak biztonsági mentése sokkal ugyanaz, mint a helyszíni Analysis Services. Az elsődleges különbség a biztonságimásolat-fájlok tárolására. Biztonságimásolat-fájlokat menteni kell a tároló egy [Azure storage-fiók](../storage/common/storage-create-storage-account.md). Már rendelkezik egy tárfiókot és tárolót is használhatja, vagy létre tárolási beállítások a kiszolgáló konfigurálása során.

> [!NOTE]
> Tárfiók létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti. További tudnivalókért lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Biztonsági másolatok abf kiterjesztéssel együtt. A memóriában táblázatos modellek esetében is a modell adatait, és a metaadatok tárolódnak. DirectQuery táblázatos modellek esetében csak a modell metaadatait tárolja. Biztonsági másolatok is tömörítve és titkosítva kerüljenek, a beállításoktól függően. 



## <a name="configure-storage-settings"></a>A tárolási beállítások konfigurálása
Biztonsági másolatot, mielőtt, konfigurálnia kell a kiszolgáló-tárolási beállításai.


### <a name="to-configure-storage-settings"></a>Tárolási beállítások konfigurálása
1.  Az Azure portal > **beállítások**, kattintson a **Backup**.

    ![Biztonsági mentés beállításai](./media/analysis-services-backup/aas-backup-backups.png)

2.  Kattintson a **engedélyezve**, majd kattintson a **tárolási beállítások**.

    ![Bekapcsolás](./media/analysis-services-backup/aas-backup-enable.png)

3. Válassza ki a tárfiókot, vagy hozzon létre egy újat.

4. Jelöljön ki egy tárolót, vagy hozzon létre egy újat.

    ![Tároló kiválasztása](./media/analysis-services-backup/aas-backup-container.png)

5. A biztonsági mentési beállítások mentéséhez.

    ![Biztonsági mentés beállításainak mentése](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Biztonsági mentés SSMS használatával

1. Az ssms-ben, kattintson a jobb gombbal egy adatbázis > **biztonsági mentése**.

2. A **adatbázis biztonsági másolata** > **biztonságimásolat-fájl**, kattintson a **Tallózás**.

3. Az a **mentés fájlt** párbeszédpanelen ellenőrizze a mappa elérési útját, és írja be a biztonságimásolat-fájl nevét. 

4. Az a **adatbázis biztonsági másolata** párbeszédpanel, válassza a beállítások.

    **Lehetővé teszi a fájl felülírása** – ezzel a lehetőséggel felülírja az azonos nevű biztonsági mentési fájljait. Ha ez a beállítás nincs bejelölve, a fájl mentésekor ugyanazzal a névvel nem lehet egy fájl, amely ugyanazon a helyen már létezik.

    **A tömörítés alkalmazása** – ezt a beállítást, a biztonsági másolat tömörítéséhez válassza. Tömörített biztonságimásolat-fájlok lemezterület takarítható meg, de valamivel nagyobb CPU-kihasználtság szükséges. 

    **Biztonsági másolat titkosításához** – ezt a beállítást a biztonságimásolat-fájl titkosításához. Ez a beállítás a biztonságimásolat-fájl védelmét felhasználó által megadott jelszó szükséges. A jelszó megakadályozza, hogy a biztonsági mentési adatok olvasása a visszaállítási művelet, mint bármilyen más módon. Ha a biztonsági mentések titkosításához választja, a jelszó tárolásához egy biztonságos helyre.

5. Kattintson a **OK** hozhat létre, és mentse a biztonsági mentési fájlt.


### <a name="powershell"></a>PowerShell
Használat [Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) parancsmagot.

## <a name="restore"></a>Visszaállítás
Visszaállításakor, a biztonságimásolat-fájlt a kiszolgáló konfigurálása a tárfiókban kell lennie. Ha a biztonságimásolat-fájl áthelyezése a helyszíni helyről a tárfiók van szüksége, használja a [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) vagy a [AzCopy](../storage/common/storage-use-azcopy.md) parancssori segédprogramot. 



> [!NOTE]
> Van egy helyi kiszolgálóról állítja vissza, ha kell a tartományi felhasználók eltávolítása a szerepkörök a modell és a szerepköröket, az Azure Active Directory-felhasználók hozzáadására.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Visszaállítása az SSMS használatával

1. Az ssms-ben, kattintson a jobb gombbal egy adatbázis > **visszaállítása**.

2. Az a **adatbázis biztonsági másolata** párbeszédpanelen, a **biztonságimásolat-fájl**, kattintson a **Tallózás**.

3. Az a **keresse meg az adatbázisfájlok** párbeszédpanelen válassza ki a visszaállítani kívánt fájlt.

4. A **vissza az adatbázist a**, válassza ki az adatbázist.

5. Adja meg a beállításokat. Biztonsági beállítások biztonsági mentésekor használt biztonsági mentési beállításainak egyeznie kell.


### <a name="powershell"></a>PowerShell

Használat [Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) parancsmagot.


## <a name="related-information"></a>Kapcsolódó információk

[Az Azure storage-fiókok](../storage/common/storage-create-storage-account.md)  
[Magas rendelkezésre állás](analysis-services-bcdr.md)     
[Azure Analysis Services kezelése](analysis-services-manage.md)
