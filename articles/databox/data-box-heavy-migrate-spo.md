---
title: Használja az Azure Data Box nehéz fájl megosztási tartalmainak migrálása a SharePoint online-hoz |} A Microsoft Docs
description: Ez az oktatóanyag segítségével megtudhatja, hogyan fájl megosztási tartalmainak migrálása a megosztás pont Online az Azure Data Box (nagy erőforrásigényű) használatával
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 06/05/2019
ms.author: alkohli
ms.openlocfilehash: 1c432ee5851115e029b55722b6b238b4672e8345
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446714"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>A fájl megosztási tartalmainak migrálása a SharePoint online-hoz az Azure Data Box (nagy erőforrásigényű) használata

Az Azure Data Box (nagy erőforrásigényű) és a SharePoint-áttelepítési eszköz (SPMT) használatával egyszerűen migrálhatja a SharePoint Online és onedrive vállalati verzió, a fájl megosztási tartalom. A Data Box nehéz, távolítsa el a függőségi a nagy kiterjedésű hálózaton (WAN) hivatkozásra az adatok átvitelét.

A Microsoft Azure Data Box szolgáltatása lehetővé teszi egy eszköz a Microsoft Azure Portalon, hogy. Terabájt adat a kiszolgálókról az eszközhöz majd másolja. Szállítási azt a Microsoftnak, miután az adatokat másolja az Azure-bA. Átvinni kívánt adatok méretétől függően a közül választhat:

- [Data Box-lemezek](https://docs.microsoft.com/azure/databox/data-box-disk-overview) 35 TB-os használható kapacitása megrendelésenként kis és közepes méretű adatkészletek esetében.
- [Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) a 80 TB felhasználható kapacitás eszközönként közepes nagyméretű adatkészletek esetében.
- [Data Box nehéz](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) 770-TB felhasználható kapacitás eszközönként nagy adatkészletek esetében a. Data Box nehéz jelenleg előzetes verzióban érhető el.

Ez a cikk kifejezetten a fájl megosztási tartalmainak migrálása a SharePoint online-hoz a Data Box (nagy erőforrásigényű) használatát ismerteti.

## <a name="requirements-and-costs"></a>Követelmények és költségek

### <a name="for-data-box-heavy"></a>A Data Box (nagy erőforrásigényű)

- Data Box nehéz csak akkor érhető el a nagyvállalati szerződés (EA), felhőszolgáltató (CSP), vagy az Azure sponsorship kínál. Ha az előfizetés nem esik a fenti típusok, forduljon a Microsoft Support frissítse előfizetését, vagy tekintse meg a [díjszabása Azure-előfizetés](https://azure.microsoft.com/pricing/).
- Nincs a Data Box nehéz használata előtt. Mindenképpen tekintse át a [Data Box nehéz díjszabás](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>A SharePoint Online

- Tekintse át a [minimális követelményei a SharePoint áttelepítési eszköz (SPMT)](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>A munkafolyamat áttekintése

Ez a munkafolyamat megköveteli, hogy hajtsa végre az Azure Data Box (nagy erőforrásigényű), valamint a SharePoint online.
Az alábbi lépéseket az Azure Data Box nehéz vonatkoznak.

1. Rendelés az Azure Data Box (nagy erőforrásigényű).
2. Kap, és konfigurálja az eszközt.
3. Adatok másolása a helyszíni fájl mappát ossza meg az Azure Files az eszközön.
4. A másolás után adjon ki kiváló vissza az utasításoknak az eszközön.
5. Várjon, amíg az adatok teljesen feltöltése az Azure-bA.

Az alábbi lépéseket vonatkozik a SharePoint online.

6. Virtuális gép létrehozása az Azure Portalon, és az Azure-fájlmegosztás csatlakoztatása rajta.
7. Telepítse a SPMT eszközt az Azure virtuális gépen.
8. A SPMT eszköz használata az Azure-fájlmegosztást, futtassa a *forrás*.
9. Az eszköz utolsó lépéseket.
10. Győződjön meg arról, és erősítse meg az adatokat.

## <a name="use-data-box-heavy-to-copy-data"></a>Használja a Data Box nehéz adatmásolás céljából

Az alábbi lépéseket az adatok másolásához a Data Box (nagy erőforrásigényű).

1. [A Data Box (nagy erőforrásigényű) Order](data-box-heavy-deploy-ordered.md).
2. Miután megkapta a Data Box bemenő adatforgalmat kezel, [állítsa be a Data Box nehéz](data-box-heavy-deploy-set-up.md). Fog kábelezése és mind a csomópontok beállítása az eszközön.
3. [Adatok másolása az Azure Data Box nehéz](data-box-heavy-deploy-copy-data.md). A másolás, közben ügyeljen arra, hogy:

    - Csak a *AzureFile* mappa a Data Box nehéz másolja az adatokat. Ennek az oka az adatokat, végül a Azure-fájlmegosztás, nem a blokkblobok és lapblobok.
    - Fájlok másolása egy mappába belül *AzureFile* mappát. Egy almappát *AzureFile* mappát egy fájlmegosztást hoz létre. Közvetlenül a másolt *AzureFile* mappában fellépő hibák és a feltöltött blokkblobok formájában. Ez az a fájlmegosztás, amely csatlakoztatja a virtuális Gépen a következő lépésben.
    - Másolja az adatokat a Data Box nehéz mindkét csomópontján.
3. Futtatás [szállításra való](data-box-heavy-deploy-picked-up.md#prepare-to-ship) az eszközön. Egy sikeres készítse elő szállításra való biztosítja, hogy egy sikeres feltöltése az Azure-fájlok.
4. [Az eszköz visszaadása](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Ellenőrizze az adatok feltöltése az Azure-bA](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Adatok áttelepítése SPMT használatával

Miután megkapta a megerősítést, hogy befejeződött az adatok másolása Azure-beli adat-csapattól, most már folytathatja az adatok áttelepítése a SharePoint online-hoz.

A legjobb teljesítmény és a kapcsolat azt javasoljuk, hogy létrehozott egy Azure virtuális gép (VM).

1. Jelentkezzen be az Azure Portalon, majd [hozzon létre egy virtuális gépet](../virtual-machines/windows/quick-create-portal.md).
2. [Az Azure-fájlmegosztást az alakzatot a virtuális gép csatlakoztatása](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Töltse le a SharePoint-áttelepítési eszköz](https://spmtreleasescus.blob.core.windows.net/install/default.htm) , és telepítse az Azure virtuális gép.
4. A SharePoint-áttelepítési eszköz elindításához. Kattintson a **jelentkezzen be a** , és adja meg az Office 365-felhasználónevével és jelszavával.
5. Amikor a rendszer kéri **hol van az adatok?** válassza **fájlmegosztás**. Az elérési útját adja meg az Azure-fájlmegosztás hol helyezkedik el az adatokat.
6. Kövesse a lépéseket, normál, beleértve a célhelyen. További információért ugorjon [a SharePoint-áttelepítési eszköz használata](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - A sebesség, amellyel adatokat a SharePoint Online szolgáltatásba betöltött által érintett számos tényező befolyásolja, függetlenül attól, ha az adatok már van Azure-ban. Ezek a tényezők megértéséhez segítséget, és a hatékonyság az áttelepítés megtervezése.  További információért ugorjon [a SharePoint Online és onedrive vállalati verzió áttelepítési sebesség](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Annak a kockázata, hogy elvesznek a meglévő fájlok engedélyeinek, ha az adatokat a SharePoint Online-ba való migrálás. Például bizonyos metaadatait is elveszhetnek *által létrehozott* és *módosítás dátuma szerint*.

## <a name="next-steps"></a>További lépések

[A Data Box (nagy erőforrásigényű) Order](./data-box-heavy-deploy-ordered.md)