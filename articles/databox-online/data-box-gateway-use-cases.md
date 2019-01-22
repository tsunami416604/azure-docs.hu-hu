---
title: A Microsoft Azure Data Box Gateway usecases |} A Microsoft Docs
description: A témakör ismerteti a usecases Azure Data Box Gateway, egy virtuális készülék tárolási megoldás, amely lehetővé teszi az adatok átviteléhez az Azure-bA
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 01/17/2019
ms.author: alkohli
ms.openlocfilehash: d1367504182eb8d8335796dc37800c30e0a563b8
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438592"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Az Azure Data Box Gateway alkalmazási helyzetei

Az Azure Data Box átjáró egy felhőalapú tárolási átjáróeszköz, amely a helyszínen található, és a rendszerkép, média és egyéb adatokat küld az Azure-bA. A felhőalapú tárolási átjáróra a hipervizor kiépített virtuális gépet. A virtuális eszköz, amely ezután elküldi az Azure-bA az NFS és az SMB protokoll használatával történő adatírást. Ez a cikk a forgatókönyvek, ahol telepítheti az eszköz részletes leírását.

Data Box-átjáró a következő esetekben használja:

- Folyamatosan a nagy mennyiségű adat betöltését.
- A felhőbe archiválási adatok biztonságos és hatékony módon.
- Növekményes adatátvitel után a kezdeti tömeges a hálózaton keresztül történik, átviteli Data Box használatával.

Az ezt követő szakaszokban részletesen ismertetett egyes forgatókönyvek esetében.

> [!IMPORTANT]
> A Data Box Gateway előzetes verzióban érhető el. A megoldás üzembe helyezése előtt kérjük tekintse át az [előzetes verziókra vonatkozó használati feltételeket](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="continuous-data-ingestion"></a>Folyamatos adatbetöltés

A Data Box Gateway elsődleges előnyeit egyik folyamatosan betölteni az adatokat az az eszköz másolása a felhőbe, az adatok méretétől függetlenül lehetővé teszi.

Mivel az adatok írása az átjáróeszközhöz, az eszköz feltölti az adatokat az Azure Storage. Az eszköz automatikusan kezeli a storage, a fájlokat helyileg eltávolítja a metaadatok megőrzése, ha elér egy bizonyos küszöbértéket. Helyi megőrizni a metaadatok lehetővé teszi, hogy az átjáró eszköz csak töltse fel a módosításokat, a fájl frissítésekor. Az átjáró eszköz feltöltött adatok kell lennie a irányelveknek megfelelően [adatfeltöltés figyelmeztetések](data-box-gateway-limits.md#data-upload-caveats).

Adatok megtelik az eszközt, az elkezdi az szabályozás a bejövő forgalom (igény szerint) a sebesség, amellyel adatokat a felhőbe feltöltött megfelelően. Az eszközön a folyamatos bevitelt monitorozásához riasztásokat használja. Ezek a riasztások aktiválódnak a szabályozás elindul, és törlődik, miután a szabályozás leállt.

## <a name="cloud-archival-of-data"></a>A felhőbe archiválási adatok

Használja a Data Box-átjáró, ha szeretné megőrizni az adatokat hosszú távon a felhőben. Használhatja a **archív** hosszú távú megőrzésének tárolási szint.

Archív tárolási szintről legalább 180 napig store ritkán hozzáfért adatok van optimalizálva. A **archív** szint a legalacsonyabb tárolási költségeket biztosít, de a legmagasabb hozzáférési költségekkel rendelkezik. További információért ugorjon [archivált adatok hozzáférési szintje](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier).

### <a name="move-data-to-archive-tier"></a>Adatok áthelyezése az archív szinten

Mielőtt elkezdené, győződjön meg arról, hogy a Data Box futó átjáróeszköz. Kövesse a lépéseket, a részletes [oktatóanyag: Az üzembe helyezés az Azure Data Box Gateway előkészítése](data-box-gateway-deploy-prep.md) és tartsa használható a következő oktatóanyaggal, mindaddig, amíg a egy működési eszközzel rendelkezik.

- Adatok feltöltése az Azure-bA a szokásos átviteli eljáráson keresztül leírtak szerint használja a Data Box átjáróeszköz [data Transfer Data Box-átjárón keresztül](data-box-gateway-deploy-add-shares.md).
- Az adatok követően kell helyezze át az archív tárolási szintről. A blob szintjének két módon állíthatja be: Az Azure PowerShell-parancsfájl vagy egy Azure Storage életciklus-felügyeleti szabályzat.  
    - Ha az Azure powershellel, kövesse az alábbi [lépéseket](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) az adatok áthelyezése az archív tárolási szintről.
    - Ha használja az Azure-életciklus-felügyelet, kövesse az alábbi lépéseket az adatok áthelyezése az archív tárolási szintről.
        - [Regisztráljon](/azure/storage/common/storage-lifecycle-management-concepts#register-for-preview) előzetes verziójának Blob életciklus management szolgáltatás használatához az archív szinten.
        - A következő házirendet, amellyel [az archívum az adatok betöltését](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-at-ingest).
- A blobok archív vannak megjelölve, ha azok már nem módosítható az átjáró, kivéve, ha gyakori vagy ritka elérésű szintre áthelyezett. Ha a fájl a helyi tárolóban, a helyi példány (beleértve a törlések) végzett módosítások nem frissíti a rendszer archív szint.
- A blob szintről gyakori vagy ritka elérésű módosításával rehydrated kell lennie az archív tárolóban lévő adatok olvasása. [A megosztás frissítése](data-box-gateway-manage-shares.md#refresh-shares) az átjáró nem rehidratálási a blobot.

További információt tudjon meg többet a [kezelése az Azure Blob Storage életciklus](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Kezdeti tömeges adatátviteli növekményes átviteli követ

Data Box és a Data Box-Gateway használja együtt, ha szeretne egy tömeges feltöltés egy nagy mennyiségű adat növekményes átvitelt követ. Használja a Data Boxot egy kapcsolat nélküli módban (kezdeti kezdőérték) és a Data Box Gateway tömeges átviteli a növekményes átvitelt (folyamatban lévő hírcsatorna) a hálózaton keresztül.

### <a name="seed-the-data-with-data-box"></a>Rendezze az adatokat a Data Box

Kövesse az alábbi lépéseket az adatok másolásához a Data Box-és feltöltése az Azure Storage.

1. [A Data Box ORDER](/azure/databox/data-box-deploy-ordered).
2. [Állítsa be a Data Box](/azure/databox/data-box-deploy-set-up).
3. [Adatmásolás a Data Boxba SMB-n keresztül](/azure/databox/data-box-deploy-copy-data).
4. [A Data Box visszaadása, ellenőrizze az adatok feltöltése az Azure-bA](/azure/databox/data-box-deploy-picked-up).
5. Miután befejeződött az adatok feltöltése az Azure-ba, az Azure storage-tárolók összes adatot kell lennie. A storage-fiókban lévő Data Box nyissa meg a Blob (és fájl) tároló, győződjön meg arról, hogy a rendszer az összes az adatokat másolja. Jegyezze fel a tároló neve, ugyanis ez a név később fogja használni. Például a következő képernyőképen `databox` tároló a növekményes átviteli használható.

    ![Az adatok Data Box-tárolóban](media/data-box-gateway-use-cases/data-container1.png)

A tömeges átvitele akkor ér véget a kezdeti kiindulási fázisban.

### <a name="ongoing-feed-with-data-box-gateway"></a>Folyamatban lévő mezőbe adatátjáróval hírcsatorna

Kövesse az alábbi lépéseket a folyamatban lévő Adatbetöltési Data Box-átjáró.

1. Felhőalapú megosztás létrehozása a Data Box-átjárón. Ez a megosztás automatikusan feltölti az adatokat az Azure Storage-fiókba. Lépjen a **megosztások** a Data Box-Gateway-erőforráshoz, és kattintson a **+ Hozzáadás megosztás**.

    ![Kattintson a + Megosztás hozzáadása](media/data-box-gateway-use-cases/add-share1.png)

2. Ellenőrizze, hogy a megosztás képez le a tároló, amely az előkészített adatokat tartalmaz. A **válassza blob-tároló**, válassza a **meglévő** , és keresse meg a tároló, amelyben az adatok Data Box átruházták.

    ![Megosztás beállításai](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. A megosztás létrehozása után frissítse a megosztást. Ez a művelet frissíti a tartalmat az Azure-ból a helyszíni megosztás.

    ![Megosztás frissítése](media/data-box-gateway-use-cases/refresh-share1.png)

    Ha a megosztás szinkronizálva van, a Box átjáró a fájlokat az ügyfél sikeresen módosítva. Ha a növekményes változásokat feltöltése.

## <a name="next-steps"></a>További lépések

- A [Data Box Gateway rendszerkövetelményeinek](data-box-gateway-system-requirements.md) áttekintése.
- A [Data Box Gateway korlátainak](data-box-gateway-limits.md) megismerése.
- Az [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) üzembe helyezése az Azure Portalon.