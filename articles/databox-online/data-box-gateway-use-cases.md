---
title: A Microsoft Azure Data Box Gateway használati esetei | Microsoft dokumentumok
description: Az Azure Data Box Gateway, egy virtuális berendezés-tárolási megoldás használati esetének ismertetése, amely lehetővé teszi az adatok átvitelét az Azure-ba
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/02/2019
ms.author: alkohli
ms.openlocfilehash: e72113313e27949819db567c550401b1f051473f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022681"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Esetek használata az Azure Data Box Gateway-hez

Az Azure Data Box Gateway egy felhőalapú tárolóátjáró-eszköz, amely az Ön telephelyén található, és elküldi a képet, a médiát és egyéb adatokat az Azure-ba. Ez a felhőalapú tárolási átjáró egy virtuális gép a hipervizorban kiépítve. Adatokat ír a virtuális eszközre az NFS és az SMB protokollok használatával, amelyeket ezután az Azure-nak küld. Ez a cikk részletes leírást ad az eszközök üzembe helyezéséhez.

A Data Box Gateway használata a következő esetekben:

- Nagy mennyiségű adat folyamatos betöltéséhez.
- Az adatok felhőalapú archiválására biztonságos és hatékony módon.
- Növekményes adatátvitel a hálózaton keresztül a kezdeti tömeges átvitel után történik a Data Box használatával.

Ezeket a forgatókönyveket részletesen ismertetjük a következő szakaszokban.


## <a name="continuous-data-ingestion"></a>Folyamatos adatbetöltés

A Data Box Gateway egyik elsődleges előnye, hogy az adatok folyamatos betöltése az eszközre a felhőbe másoláshoz, az adatok méretétől függetlenül.

Az adatok írása az átjáróeszközre, az eszköz feltölti az adatokat az Azure Storage-ba. Az eszköz automatikusan kezeli a tárhelyet azáltal, hogy helyileg eltávolítja a fájlokat, miközben megőrzi a metaadatokat, amikor elér egy bizonyos küszöbértéket. A metaadatok helyi másolatának megőrzése lehetővé teszi, hogy az átjáróeszköz csak a fájl frissítésekor töltse fel a módosításokat. Az átjáróeszközre feltöltött adatoknak meg kell egyeznie az [adatfeltöltési kikötések irányelveinek megfelelően.](data-box-gateway-limits.md#data-upload-caveats)

Ahogy az eszköz megtelik adatokkal, elkezdi a be- ésécsi sebesség szabályozását (szükség szerint), hogy megfeleljen az adatok felhőbe való feltöltési sebességének. Az eszközön a folyamatos betöltés figyeléséhez riasztásokat kell használnia. Ezek a riasztások akkor jelennek meg, amikor a szabályozás elindul, és törlődik, ha a szabályozás leállt.

## <a name="cloud-archival-of-data"></a>Az adatok felhőbeli archiválása

Akkor használja a Data Box Gateway-t, ha hosszú távon meg szeretné őrizni az adatokat a felhőben. Használhatja az **archív** tárolási szint a hosszú távú megőrzése.

Az archív szint úgy van optimalizálva, hogy legalább 180 napig tárolja a ritkán elért adatokat. Az **Archív** szint kínálja a legalacsonyabb tárolási költségeket, de a legmagasabb hozzáférési költségekkel rendelkezik. További információ: [Archív hozzáférési szint.](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier)

### <a name="move-data-to-archive-tier"></a>Adatok áthelyezése az Archív szintre

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik egy futó Data Box Gateway eszközzel. Kövesse az oktatóanyagban ismertetett [lépéseket: Készüljön fel az Azure Data Box Gateway üzembe helyezésére,](data-box-gateway-deploy-prep.md) és folytassa a következő oktatóanyagra való továbblépést, amíg nem rendelkezik egy működő eszközzel.

- A Data Box Gateway eszközzel adatokat tölthet fel az Azure-ba a [Data Box Gateway-en keresztül](data-box-gateway-deploy-add-shares.md)történő adatátviteli eljárásban leírtak szerint.
- Az adatok feltöltése után át kell helyeznie az archívumszintre. A blobréteget kétféleképpen állíthatja be: Az Azure PowerShell-parancsfájl vagy egy Azure Storage Lifecycle Management-szabályzat.  
    - Ha az Azure PowerShell, kövesse az alábbi [lépéseket](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) az adatok áthelyezése az archív szintre.
    - Ha az Azure Lifecycle Management használatával, kövesse az alábbi lépéseket az adatok áthelyezése az archív szintre.
        - [Regisztráljon](/azure/storage/common/storage-lifecycle-management-concepts) a Blob életciklus-kezelési szolgáltatás előzetes verziójához az archív szint használatához.
        - A [betöltési adatok archiválása](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-after-ingest)az alábbi házirend segítségével.
- Miután a blobok archívként vannak megjelölve, az átjáró már nem módosíthatja őket, kivéve, ha a ritka elérésű szintre kerülnek. Ha a fájl a helyi tárolóban található, a helyi példányon végrehajtott módosítások (beleértve a törléseket is) nem kerülnek feltöltésre az archív szintre.
- Adatok olvasásához az archív tárolóban, azt újra kell hidratálni a blob szint gyakori vagy ritka elérésű módosítása. [Az átjárón](data-box-gateway-manage-shares.md#refresh-shares) lévő megosztás frissítése nem hidratálja a blobot.

További információ az [Azure Blob Storage életciklusának kezeléséről.](/azure/storage/common/storage-lifecycle-management-concepts)

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Kezdeti tömeges átvitel, majd növekményes átvitel

A Data Box és a Data Box Gateway együttes használatával nagy mennyiségű adatot szeretne tömegesen feltölteni, majd növekményes átvitelt szeretne végezni. Használja a Data Box-ot az offline módban (kezdőmag) és a Data Box Gateway-t a hálózaton keresztüli növekményes átvitelhez (folyamatos hírcsatorna).

### <a name="seed-the-data-with-data-box"></a>Az adatok elmagvetése a Data Box segítségével

Az alábbi lépésekkel másolja az adatokat a Data Boxba, és töltse fel az Azure Storage-ba.

1. [Rendelje meg adatdobozát.](/azure/databox/data-box-deploy-ordered)
2. [Állítsa be az Adatdobozt.](/azure/databox/data-box-deploy-set-up)
3. [Adatok másolása a Data Box ba SMB segítségével.](/azure/databox/data-box-deploy-copy-data)
4. [Adja vissza az adatmezőt, ellenőrizze az adatok feltöltését az Azure-ba.](/azure/databox/data-box-deploy-picked-up)
5. Miután az adatok feltöltése az Azure-ba befejeződött, az összes adatot az Azure storage-tárolókban kell lennie. A Data Box tárfiókjában nyissa meg a Blob (és a Fájl) tárolót, és győződjön meg arról, hogy az összes adat másolása. Jegyezze fel a tároló nevét, mivel később ezt a nevet fogja használni. Például a következő képernyőképen a `databox` tároló a növekményes átvitelhez lesz használva.

    ![Tároló adatokkal a Data Box](media/data-box-gateway-use-cases/data-container1.png)

Ez a tömeges átvitel befejezi a kezdeti vetési fázist.

### <a name="ongoing-feed-with-data-box-gateway"></a>Folyamatos hírcsatorna a Data Box Gateway-szel

Kövesse az alábbi lépéseket a Data Box Gateway folyamatos betöltéséhez.

1. Hozzon létre egy felhőmegosztást a Data Box Gateway-en. Ez a megosztás automatikusan feltölti az adatokat az Azure Storage-fiókba. Nyissa meg **a Megosztások** a Data Box Gateway erőforrásban, és kattintson **a + Megosztás hozzáadása gombra.**

    ![Kattintson a +Megosztás hozzáadása gombra](media/data-box-gateway-use-cases/add-share1.png)

2. Győződjön meg arról, hogy ez a megosztás leképezi a tárolót, amely tartalmazza a beültetett adatokat. A **Blob-tároló kiválasztása csoportban**válassza **a Meglévő használata** lehetőséget, és keresse meg azt a tárolót, amelyben az adatok átvitele történt.

    ![Megosztási beállítások](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. A megosztás létrehozása után frissítse a megosztást. Ez a művelet frissíti a helyszíni megosztást az Azure-ból származó tartalommal.

    ![Megosztás frissítése](media/data-box-gateway-use-cases/refresh-share1.png)

    A megosztás szinkronizálásakor a Data Box Gateway feltölti a növekményes módosításokat, ha a fájlokat módosították az ügyfélen.

## <a name="next-steps"></a>További lépések

- A [Data Box Gateway rendszerkövetelményeinek](data-box-gateway-system-requirements.md) áttekintése.
- A [Data Box Gateway korlátainak](data-box-gateway-limits.md) megismerése.
- Az [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) üzembe helyezése az Azure Portalon.