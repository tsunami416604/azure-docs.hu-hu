---
title: Fájlmegosztási tartalom áthelyezése a SharePoint Online-ba az Azure Data Box Heavy segítségével
description: Ebből az oktatóanyagból megtudhatja, hogyan telepítheti át a fájlmegosztási tartalmakat a Share Point Online-ba az Azure Data Box Heavy használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: f97ea17551d4415f7ed6371853172cfde30fe4b6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77560048"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Az Azure Data Box Heavy segítségével migrálhatja fájlmegosztási tartalmait a SharePoint Online-ba

Az Azure Data Box Heavy és a SharePoint Migration Tool (SPMT) segítségével könnyedén áttelepítheti a fájlmegosztási tartalmakat a SharePoint Online-ba és a OneDrive-ra. A Data Box Heavy használatával eltávolíthatja a nagykiterjedésű hálózati (WAN) kapcsolatfüggőségét az adatok átviteléhez.

A Microsoft Azure Data Box egy olyan szolgáltatás, amely lehetővé teszi, hogy rendeljen egy eszközt a Microsoft Azure portalon. Ezután átmásolhat több terabájtnyi adatot a kiszolgálókról az eszközre. Miután visszaszállította a Microsoftnak, az adatok at az Azure-ba másolja. Az átvinni kívánt adatok méretétől függően a következők közül választhat:

- [Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) 35 TB-os felhasználható kapacitással rendelésenként a kis és közepes adatkészletek számára.
- [80](https://docs.microsoft.com/azure/databox/data-box-overview) TB-os használható kapacitással rendelkező adatdoboz eszközönként a közepes és nagy méretű adatkészletek számára.
- [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) 770 TB-os használható kapacitással eszközönként nagy adatkészletek esetén.

Ez a cikk kifejezetten arról szól, hogy a Data Box Heavy segítségével hogyan telepítheti át a fájlmegosztási tartalmakat a SharePoint Online-ba.

## <a name="requirements-and-costs"></a>Követelmények és költségek

### <a name="for-data-box-heavy"></a>A Data Box Heavyhez

- A Data Box Heavy csak nagyvállalati szerződés (EA), felhőalapú megoldásszolgáltató (CSP) vagy Azure-szponzorálási ajánlatok esetén érhető el. Ha előfizetése nem tartozik a fenti típusok egyikébe sem, lépjen kapcsolatba a Microsoft támogatási szolgálatával az előfizetés frissítéséhez, vagy tekintse meg az [Azure-előfizetésdíj-díjakat.](https://azure.microsoft.com/pricing/)
- A Data Box Heavy használata díjköteles. Győződjön meg róla, hogy ellenőrizze a [Data Box Heavy árképzést.](https://azure.microsoft.com/pricing/details/databox/heavy/)


### <a name="for-sharepoint-online"></a>SharePoint Online esetén

- Tekintse át [a SharePoint áttelepítési eszköz (SPMT) minimális követelményeit.](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool)

## <a name="workflow-overview"></a>Munkafolyamat – áttekintés

Ehhez a munkafolyamathoz az Azure Data Box Heavy és a SharePoint Online-on is lépéseket kell végrehajtania.
A következő lépések az Azure Data Box Heavy-re vonatkoznak.

1. Rendelje meg az Azure Data Box Heavy-t.
2. Fogadja és állítsa be az eszközt.
3. Adatok másolása a helyszíni fájlmegosztásból az Azure Files mappájába az eszközön.
4. A másolat befejezése után az utasításokat a készüléket visszaszállítsa.
5. Várja meg, amíg az adatok teljesen feltöltésre vannak az Azure-ba.

Az alábbi lépések a SharePoint Online-ra vonatkoznak.

6. Hozzon létre egy virtuális gép az Azure Portalon, és csatlakoztassa az Azure-fájlmegosztás rajta.
7. Telepítse az SPMT eszközt az Azure virtuális gépre.
8. Futtassa az SPMT eszközt az Azure-fájlmegosztás sal *forrásként.*
9. Végezze el az eszköz utolsó lépéseit.
10. Ellenőrizze és erősítse meg az adatokat.

## <a name="use-data-box-heavy-to-copy-data"></a>Adatok másolása a Data Box Heavy mezőben

Az adatok adatmásolásához tegye a következő lépéseket.

1. [Rendelje meg data box heavy](data-box-heavy-deploy-ordered.md).
2. Miután megkapta a Data Box [Heavy,Set up the Data Box Heavy](data-box-heavy-deploy-set-up.md). Az eszköz mindkét csomópontját kábelezheti és konfigurálhatja.
3. [Adatok másolása az Azure Data Box Heavy szolgáltatásba.](data-box-heavy-deploy-copy-data.md) Másolás közben győződjön meg arról, hogy:

    - Az adatok másolásához csak a Data Box Heavy *StorageAccountName_AzFile* mappáját használja. Ennek az az oka, hogy azt szeretné, hogy az adatok egy Azure-fájlmegosztásba kerüljenek, ne blokkblobokban vagy lapblobokban.
    - Fájlok másolása *StorageAccountName_AzFile* mappába. Az *StorageAccountName_AzFile* mappában lévő almappa fájlmegosztást hoz létre. A közvetlenül *StorageAccountName_AzFile* mappába másolt fájlok nem sikerülnek, és blokkblobként kerülnek feltöltésre. Ez az a fájlmegosztás, amelyet a következő lépésben csatlakoztatni fog a virtuális géphez.
    - Adatok másolása a Data Box Heavy mindkét csomópontjára.
3. Fuss [Felkészülés a hajóra](data-box-heavy-deploy-picked-up.md#prepare-to-ship) az eszközön. A sikeres szállítás biztosítja a fájlok sikeres feltöltését az Azure-ba.
4. [Adja vissza a készüléket](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Ellenőrizze az Azure-ba való adatfeltöltést.](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure)

## <a name="use-spmt-to-migrate-data"></a>Adatok áttelepítése az SPMT segítségével

Miután megerősítést kapott az Azure-adatcsapattól az adatmásolás befejezéséről, folytassa az adatok áttelepítésével a SharePoint Online-ba.

A legjobb teljesítmény és a kapcsolat érdekében azt javasoljuk, hogy hozzon létre egy Azure virtuális gép (VM).

1. Jelentkezzen be az Azure Portalon, és [hozzon létre egy virtuális gépet.](../virtual-machines/windows/quick-create-portal.md)
2. [Csatlakoztassa az Azure-fájlmegosztást a virtuális gépre.](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer)
3. [Töltse le a SharePoint-áttelepítési eszközt,](https://spmtreleasescus.blob.core.windows.net/install/default.htm) és telepítse az Azure-beli virtuális gépre.
4. Indítsa el a SharePoint áttelepítési eszközét. Kattintson **a Bejelentkezés** gombra, és adja meg office 365-ös felhasználónevét és jelszavát.
5. Amikor a program **megkérdezi, hogy hol vannak az adatok?** lehetőséget válassza **a Fájlmegosztás**lehetőséget. Adja meg az Azure-fájlmegosztás elérési útját, ahol az adatok találhatók.
6. Kövesse a többi kérdést a szokásos módon, beleértve a célhelyet is. További információt a [SharePoint áttelepítési eszköz használata című részben talál.](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool)

> [!IMPORTANT]
> - Az adatok SharePoint Online-ba történő betöltésének sebességét számos tényező befolyásolja, függetlenül attól, hogy az adatok már az Azure-ban vannak-e. Ezeknek a tényezőknek a megértése segít megtervezni és maximalizálni az áttelepítés hatékonyságát.  További információt a [SharePoint Online és a OneDrive áttelepítési sebessége című](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed)rész ben talál.
> - Az adatok SharePoint Online-ba való áttelepítésekor előfordulhat, hogy elveszíti a fájlokra vonatkozó meglévő engedélyeket. Bizonyos metaadatok is elveszhetnek, például a *Létrehozva* és *a Dátum által módosítva.*

## <a name="next-steps"></a>További lépések

[Rendelje meg data box nehéz](./data-box-heavy-deploy-ordered.md)