---
title: Üzembe helyezés – áttekintés – Avere vFXT az Azure-hoz
description: Avere vFXT telepítése az Azure áttekintése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 88305e5d877f5bc3cc7ebb116f69b0f49f162b79
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268381"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT az Azure-ban – üzembe helyezés – áttekintés

Ez a cikk áttekintést be Azure-fürtön, és fut egy Avere vFXT lekéréséhez szükséges lépéseket.

Több feladat előtt, és az Azure Marketplace-ről a vFXT fürt létrehozása után van szükség. A start-Befejezés folyamat törlése bizonyos értelemben kellene segít a hatókör részéről az erőfeszítés szükséges. 

## <a name="deployment-steps"></a>A központi telepítés lépései

Miután [tervezési a rendszer](avere-vfxt-deploy-plan.md), elkezdheti a Avere vFXT fürt létrehozásához. 

Az Azure Resource Manager-sablonok, az Azure Marketplace-en a szükséges információkat gyűjt, és automatikusan üzembe helyezi a teljes fürtöt. 

A vFXT fürt működik és elérhető, miután érdemes tudni, hogyan kell az ügyfelek csatlakozni, és ha szükséges, az adatok áthelyezése az új Blob storage-tárolóba.  

Itt látható az összes lépést áttekintése.

1. Előfeltételek konfigurálása 

   Virtuális gép létrehozása előtt hozzon létre egy új előfizetést a Avere vFXT projekt, konfigurálja az előfizetés tulajdonjogának, vegye kvóták és növelésére, ha szükséges, és fogadja el a Avere vFXT szoftver használatára vonatkozó feltételeket. Olvasási [a Avere vFXT létrehozásának előkészítéséhez](avere-vfxt-prereqs.md) részletes útmutatást.

1. A Avere vFXT fürt létrehozása 

   Az Azure-fürtön a Avere vFXT létrehozásához használja az Azure piactéren. Sablon a szükséges adatokat gyűjt, és végrehajtja a parancsfájlokat a végső termék létrehozásához.

   A fürtlétrehozás ezt a lépést, hogy az összes végezzék el a marketplace-sablon foglalja magában: 

   * Új hálózati infrastruktúra és az erőforrás csoportok létrehozása, ha szükséges
   * Létrehozás egy *fürt vezérlő*  

     A fürt vezérlő egy egyszerű virtuális Gépet, amely a Avere vFXT fürt ugyanazon a virtuális hálózaton található, és az egyéni szoftver szükséges a fürt létrehozása és kezelése. A vezérlő létrehozza a vFXT és űrlapok a fürtöt, és a fürt kezelésére és teljes élettartama alatt olyan parancssori felületet is biztosít.

     Ha az üzembe helyezés során létrehoz egy új virtuális hálózat, a vezérlő kell egy nyilvános IP-címet. Ez azt jelenti, hogy a vezérlő a jump host alapul szolgálhat a fürt a virtuális hálózaton kívülről való csatlakozáshoz.

   * A fürt csomópont virtuális gépek létrehozása

   * A fürtcsomópont virtuális gépein a fürt konfigurálása

   * Igény szerint egy új Blob-tároló létrehozása és konfigurálása, a fürt háttér-tárolóként

1. A fürt konfigurálása 

   Csatlakozás a Avere vFXT konfigurációs felület (Avere Vezérlőpult) a fürt a beállításokat. Részvétel a támogatást figyelésre, és adja hozzá a tárolórendszeren, ha egy helyszíni adatközpontot használ.

   * [Hozzáférés a vFXT fürt](avere-vfxt-cluster-gui.md)
   * [Támogatásának engedélyezése](avere-vfxt-enable-support.md)
   * [Tárolás konfigurálása](avere-vfxt-add-storage.md) (ha szükséges)

1. Csatlakoztassa az ügyfelek számára

   Kövesse az útmutatásokat a [a Avere vFXT fürt csatlakoztatási](avere-vfxt-mount-clients.md) terheléselosztás, és hogyan csatlakoztathatja a ügyfélgépek kell a fürt ismerteti.

1. Adatok hozzáadása (ha szükséges)

   Mivel a Avere vFXT skálázható több ügyfél-gyorsítótár, a legjobb módszer az adatok áthelyezése egy új háttér-storage-tárolóba van, több ügyfél, a többszálú fájl másolása stratégiát. Olvasási [adatok áthelyezése a vFXT fürthöz](avere-vfxt-data-ingest.md) részleteiről.

## <a name="next-steps"></a>További lépések

Továbbra is [a Avere vFXT létrehozásának előkészítéséhez](avere-vfxt-prereqs.md) üzembe helyezéséhez a Avere vFXT for Azure előzetes feladatok végrehajtásához. 