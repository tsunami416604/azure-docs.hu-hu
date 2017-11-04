---
title: "Az Azure Analysis Services-adatbázis biztonsági mentése és visszaállítása |} Microsoft Docs"
description: "Ismerteti, hogyan biztonsági mentése és visszaállítása az Azure Analysis Services-adatbázis."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: f96e72e4decd475e7859eb7f70046a277b718ac8
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Az Azure Analysis Services táblázatos modell adatbázisainak biztonsági mentése sokkal ugyanúgy történik a helyszíni Analysis Services. Az elsődleges különbség az, ahol a biztonsági mentési fájljait tárolja. Biztonságimásolat-fájlokat menteni kell a tárolót egy [Azure storage-fiók](../storage/common/storage-create-storage-account.md). Használhatja a tárfiók és tároló már rendelkezik, vagy létrehozása a kiszolgáló tárolási beállításainak konfigurálásakor.

> [!NOTE]
> A storage-fiók létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti. További tudnivalókért lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Biztonsági másolatok abf-fájl kiterjesztéssel együtt. A memóriában táblázatos modellek esetében a modell adatai és a metaadatok tárolja. DirectQuery táblázatos modellek esetében csak a modell metaadatait tárolja. Biztonsági mentések tömörített, és titkosíthatók, a kiválasztott lehetőségektől függően. 



## <a name="configure-storage-settings"></a>A tárolási beállítások konfigurálása
A biztonsági másolatot, előtt kell a kiszolgáló a tárolási beállítások konfigurálása.


### <a name="to-configure-storage-settings"></a>Tárolási beállítások konfigurálása
1.  Az Azure portál > **beállítások**, kattintson a **biztonsági mentés**.

    ![Beállítások a biztonsági másolatok](./media/analysis-services-backup/aas-backup-backups.png)

2.  Kattintson a **engedélyezve**, majd kattintson a **tárolási beállítások**.

    ![Bekapcsolás](./media/analysis-services-backup/aas-backup-enable.png)

3. Válassza ki a tárfiók, vagy hozzon létre egy újat.

4. Jelöljön ki egy tárolót, vagy hozzon létre egy újat.

    ![Válassza ki a tárolót](./media/analysis-services-backup/aas-backup-container.png)

5. A biztonsági mentési beállítások mentéséhez.

    ![Biztonsági mentési beállításainak mentése](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Biztonsági mentés

### <a name="to-backup-by-using-ssms"></a>A biztonsági mentési szolgáltatáshoz az SSMS használatával

1. Az SSMS, kattintson a jobb gombbal egy adatbázis > **készítsen biztonsági másolatot**.

2. A **adatbázis biztonsági másolata** > **biztonságimásolat-fájl**, kattintson a **Tallózás**.

3. Az a **mentés fájlt** párbeszédpanelen ellenőrizze a mappa elérési útját, és írja be a biztonságimásolat-fájl nevét. 

4. Az a **adatbázis biztonsági másolata** párbeszédpanelen válassza a beállítások.

    **Engedélyezi a fájl felülírása** – ezzel a beállítással felülírja az azonos nevű biztonsági mentési fájljait. Ha ez a beállítás nincs bejelölve, a menteni kívánt fájl neve nem lehet ugyanazon a helyen már létezik egy fájl.

    **Tömörítés alkalmazása** – válassza ezt a beállítást, a másolat tömörítéséhez. Tömörített biztonságimásolat-fájlok helymegtakarítás, de valamivel nagyobb processzorhasználatot igényel. 

    **Biztonsági másolat titkosításához** – válassza ezt a beállítást, a biztonsági fájl titkosításához. Ez a beállítás a biztonságimásolat-fájlt biztonságos felhasználó által megadott jelszót igényel. A jelszó megakadályozza, hogy a biztonsági mentési adatok olvasása a visszaállítási művelet mint bármilyen más módon. Biztonsági mentések titkosításához válassza ki, ha a jelszót biztonságos helyen tárolja.

5. Kattintson a **OK** létrehozása és mentése a biztonságimásolat-fájlt.


### <a name="powershell"></a>PowerShell
Használjon [Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) parancsmag.

## <a name="restore"></a>Visszaállítás
Visszaállításakor, a biztonságimásolat-fájlt a kiszolgálóra történő konfigurálását tárfiókban kell lennie. Ha a biztonságimásolat-fájl áthelyezése a tárfiók a helyszíni helyről van szüksége, [Microsoft Azure Tártallózó](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) vagy a [AzCopy](../storage/common/storage-use-azcopy.md) parancssori segédprogram. 



> [!NOTE]
> Ha most állítja vissza egy helyi kiszolgálóról, akkor távolítsa el a tartományi felhasználók a modell szerepkörök, és adja hozzá újra az Azure Active Directory-felhasználók, szerepkörök.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Visszaállítása szolgáltatáshoz az SSMS használatával

1. Az SSMS, kattintson a jobb gombbal egy adatbázis > **visszaállítása**.

2. Az a **adatbázis biztonsági másolata** párbeszédpanelen, a **biztonságimásolat-fájl**, kattintson a **Tallózás**.

3. Az a **található adatbázisfájlok** párbeszédpanelen jelölje ki a visszaállítani kívánt fájlt.

4. A **vissza az adatbázist a**, válassza ki az adatbázist.

5. Adja meg a beállításokat. Biztonsági beállítások egyeznie kell a biztonsági mentésekor használt biztonsági mentési beállítások.


### <a name="powershell"></a>PowerShell

Használjon [visszaállítási-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) parancsmag.


## <a name="related-information"></a>Kapcsolódó információk

[Az Azure storage-fiókok](../storage/common/storage-create-storage-account.md)  
[Magas rendelkezésre állás](analysis-services-bcdr.md)     
[Az Azure Analysis Services kezelése](analysis-services-manage.md)
