---
title: Microsoft Azure Data Box Gateway használati esetek | Microsoft Docs
description: Ismerteti az Azure-ba való adatátvitelt lehetővé Azure Data Box Gateway virtuális készülék tárolási megoldásának használati eseteit.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/02/2019
ms.author: alkohli
ms.openlocfilehash: dde84f0973cc7e21e57574bbabe398b38581358f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "82562396"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Azure Data Box Gateway használati esetei

Azure Data Box Gateway egy felhőalapú Storage Gateway-eszköz, amely a helyszínen található, és a képet, adathordozót és más adatokat elküldi az Azure-nak. Ez a felhőalapú tároló-átjáró egy virtuális gép, amely a hypervisorban van kiépítve. A virtuális eszközre az NFS-és SMB-protokollok használatával írhat adatokat, amelyeket aztán az Azure-nak küld. Ez a cikk részletes leírást nyújt a forgatókönyvekről, amelyekkel telepítheti az eszközt.

Használja Data Box Gateway a következő esetekben:

- Nagy mennyiségű adatot folyamatosan kell betölteni.
- Az adatfelhő-archiválás biztonságos és hatékony módon.
- A növekményes adatátvitel a hálózaton keresztül, miután a kezdeti csoportos átvitel a Data Box használatával történik.

A következő szakaszokban részletesen ismertetjük ezeket a forgatókönyveket.


## <a name="continuous-data-ingestion"></a>Folyamatos adatfeldolgozás

A Data Box Gateway egyik elsődleges előnye, hogy az adatmérettől függetlenül folyamatosan betöltheti az eszközről a felhőbe való másoláshoz szükséges adatmennyiséget.

Ahogy az adatok az átjáró eszközre íródnak, az eszköz feltölti az Azure Storage-ba. Az eszköz automatikusan kezeli a tárolót a fájlok helyi eltávolításával, miközben a metaadatok megmaradnak egy bizonyos küszöbérték elérésekor. A metaadatok helyi másolatának megőrzése lehetővé teszi, hogy az átjáró eszköz csak a fájl frissítésekor töltse fel a módosításokat. Az átjáró-eszközre feltöltött adatoknak az [adatfeltöltési kikötésekkel](data-box-gateway-limits.md#data-upload-caveats)kapcsolatos irányelvek szerint kell szerepelniük.

Ahogy az eszköz feltölti az adatforgalmat, megkezdi a bejövő forgalom arányának szabályozását (szükség szerint), hogy megfeleljen az adatfelhőbe való feltöltési aránynak. Az eszköz folyamatos betöltésének figyeléséhez riasztásokat kell használni. Ezek a riasztások a szabályozás megkezdése után következnek be, és törlődnek a szabályozás leállítása után.

## <a name="cloud-archival-of-data"></a>Az adatfelhő archiválása

Akkor használja a Data Box Gateway, ha hosszú távon szeretné megőrizni az adatait a felhőben. A hosszú távú megőrzéshez használhatja a tárterület **archiválási** rétegét.

Az archiválási szint legalább 180 napig a ritkán használt adatok tárolására van optimalizálva. Az **archiválási** szint a legalacsonyabb tárolási költségeket kínálja, de a legmagasabb hozzáférési költségekkel rendelkezik. További információ: [archív hozzáférési szint](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier).

### <a name="move-data-to-archive-tier"></a>Adatok áthelyezése az archiválási szintre

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik egy futó Data Box Gateway eszközzel. Kövesse az [oktatóanyag: Felkészülés a Azure Data Box Gateway üzembe helyezése](data-box-gateway-deploy-prep.md) című témakörben leírt lépéseket, és folytassa a következő oktatóanyaggal, amíg nem rendelkezik operatív eszközzel.

- Az Data Box Gateway eszköz használatával az adatok az Azure- [on Data Box Gateway keresztül történő átvitele](data-box-gateway-deploy-add-shares.md)című témakörben leírtak szerint tölthetők fel az Azure-ba.
- Az adatok feltöltése után át kell helyeznie az archiválási szintre. A blob szintjét kétféleképpen állíthatja be: Azure PowerShell parancsfájl vagy egy Azure Storage életciklus-kezelési házirend.  
    - Azure PowerShell használata esetén kövesse az alábbi [lépéseket](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) az adatok archiválási szintre való áthelyezéséhez.
    - Ha az Azure Lifecycle managementet használja, kövesse az alábbi lépéseket az adatok archiválási szintre való áthelyezéséhez.
        - [Regisztráljon](/azure/storage/common/storage-lifecycle-management-concepts) a blob Lifecycle Management szolgáltatás előzetes verziójára az archiválási szint használatához.
        - A következő szabályzat használatával [archiválhatja az adatok](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-after-ingest)betöltését.
- Ha a Blobok archiválásra vannak megjelölve, a továbbiakban nem módosíthatók az átjárók, kivéve, ha a gyors vagy a hideg szintre vannak helyezve. Ha a fájl a helyi tárolóban található, a helyi másolaton (beleértve a törléseket is) végrehajtott módosítások nem lesznek feltöltve az archiválási szintre.
- Az archív tárolóban lévő adatok olvasásához a blob rétegének gyors vagy ritka elérésű értékre történő módosításával kell kiszáradni. Az átjárón lévő [megosztás frissítése](data-box-gateway-manage-shares.md#refresh-shares) nem történik meg a blob újraszárítása.

További információkért tekintse meg az [Azure Blob Storage életciklusának felügyeletét](/azure/storage/common/storage-lifecycle-management-concepts)ismertető témakört.

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Kezdeti tömeges átvitel, növekményes átvitel után

A Data Box és a Data Box Gateway együttes használata, ha nagy mennyiségű adatok tömeges feltöltését szeretné elvégezni, majd növekményes átviteleket hajt végre. Használjon Data Box a tömeges átvitelhez kapcsolat nélküli módban (kezdeti mag), és Data Box Gateway a növekményes átvitelek (folyamatos adatcsatorna) számára a hálózaton keresztül.

### <a name="seed-the-data-with-data-box"></a>Az adatmag Data Box

Az alábbi lépéseket követve másolja az adatok Data Box és töltse fel az Azure Storage-ba.

1. [Rendeljen Data Box](/azure/databox/data-box-deploy-ordered).
2. [A Data Box beállítása](/azure/databox/data-box-deploy-set-up).
3. [Adatmásolás Data Box SMB-n keresztül](/azure/databox/data-box-deploy-copy-data).
4. [A Data Box visszaadása, az adatok feltöltése az Azure](/azure/databox/data-box-deploy-picked-up)-ba.
5. Miután az adatok feltöltése az Azure-ba befejeződött, az összes adattal az Azure Storage-tárolókban kell lennie. A Data Box Storage-fiókjában nyissa meg a blob (és file) tárolót, és győződjön meg arról, hogy az összes fájl másolása megtörtént. Jegyezze fel a tároló nevét, mert később ezt a nevet fogja használni. A következő képernyőképen például a `databox` növekményes átvitelhez a tárolót fogja használni.

    ![Adattároló Data Box](media/data-box-gateway-use-cases/data-container1.png)

Ez a tömeges átvitel befejezi a kezdeti előkészítési fázist.

### <a name="ongoing-feed-with-data-box-gateway"></a>Folyamatos adatcsatorna Data Box Gateway

A Data Box Gateway folyamatos betöltéséhez kövesse az alábbi lépéseket. 

1. Hozzon létre egy felhőalapú megosztást Data Box Gateway. Ez a megosztás automatikusan feltölti az összes adattárat az Azure Storage-fiókba. Nyissa meg a Data Box Gateway erőforrásban található **megosztásokat** , és kattintson a **+ megosztás hozzáadása**lehetőségre.

    ![Kattintson a + megosztás hozzáadása lehetőségre](media/data-box-gateway-use-cases/add-share1.png)

2. Győződjön meg arról, hogy ez a megosztás a magot tartalmazó tárolóra mutat. A **blob-tároló kiválasztása**területen válassza a **meglévő használata** lehetőséget, és tallózással keresse meg azt a tárolót, ahol a Data Box adatok átvitele megtörtént.

    ![Megosztási beállítások](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. A megosztás létrehozása után frissítse a megosztást. Ez a művelet frissíti a helyszíni megosztást az Azure-ból származó tartalommal.

    ![Megosztás frissítése](media/data-box-gateway-use-cases/refresh-share1.png)

    A megosztás szinkronizálása után a Data Box Gateway feltölti a növekményes módosításokat, ha a fájlokat módosították az ügyfélen.

## <a name="next-steps"></a>További lépések

- A [Data Box Gateway rendszerkövetelményeinek](data-box-gateway-system-requirements.md) áttekintése.
- A [Data Box Gateway korlátainak](data-box-gateway-limits.md) megismerése.
- Az [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) üzembe helyezése az Azure Portalon.