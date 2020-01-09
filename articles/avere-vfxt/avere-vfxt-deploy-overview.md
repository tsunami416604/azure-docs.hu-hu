---
title: Az üzembe helyezés áttekintése – avere vFXT for Azure
description: Az Azure-hoz készült avere-vFXT üzembe helyezésének áttekintése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 46e6828710c5951cdd7ec3a029272a0e3d68c477
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75415418"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT az Azure-ban – üzembe helyezés áttekintése

Ez a cikk áttekintést nyújt azokról a lépésekről, amelyekkel a avere-vFXT Azure-fürthöz való beszerzése folyamatban van.

A vFXT-fürt Azure piactéren való létrehozása előtt és után több feladat szükséges. A Kezdés és a Befejezés közötti folyamat egyértelművé teszi a szükséges erőfeszítés hatókörét.

## <a name="deployment-steps"></a>A központi telepítés lépései

[A System megtervezése](avere-vfxt-deploy-plan.md)után megkezdheti a avere vFXT-fürt létrehozását.

Az Azure Marketplace-en Azure Resource Manager-sablon gyűjti a szükséges információkat, és automatikusan telepíti a teljes fürtöt.

A vFXT-fürt működésének és futtatásának megkezdése után tudnia kell, hogyan kapcsolódhat hozzá az ügyfelekhez, és (opcionálisan) hogyan helyezheti át az adatait az új blob Storage-tárolóba. Ha NAS-tárolót használ, a fürt létrehozása után fel kell vennie azt.

Az alábbiakban áttekintheti az összes lépést.

1. Előfeltételek konfigurálása

   A virtuális gép létrehozása előtt létre kell hoznia egy új előfizetést a avere vFXT projekthez, meg kell adnia az előfizetés tulajdonjogát, ellenőriznie kell a kvótákat, és szükség esetén meg kell adnia a növelést, és el kell fogadnia az avere vFXT szoftver használatára Részletes utasításokért olvassa el [a felkészülés a avere vFXT létrehozásához](avere-vfxt-prereqs.md) című témakört.

1. A avere vFXT-fürt létrehozása

   Az Azure Marketplace használatával hozza létre az Azure-fürthöz készült avere-vFXT. A sablon gyűjti a szükséges információkat, és parancsfájlokat hajt végre a végső termék létrehozásához.

   A fürt létrehozása az alábbi lépéseket foglalja magában, amelyeket a piactér sablonja végez:

   * Szükség esetén hozzon létre új hálózati infrastruktúrát és erőforráscsoportot
   * *Fürt* létrehozása  

     A tartományvezérlő egy egyszerű virtuális gép, amely ugyanabban a virtuális hálózatban található, mint a avere vFXT-fürt, és rendelkezik a fürt létrehozásához és kezeléséhez szükséges egyéni szoftverekkel. A vezérlő létrehozza a vFXT-csomópontokat, és megalkotja a fürtöt, és egy parancssori felületet is biztosít a fürt felügyeletéhez az élettartama során.

     Ha az üzembe helyezés során új virtuális hálózatot vagy alhálózatot hoz létre, a vezérlő nyilvános IP-címmel fog rendelkezni. Ez azt jelenti, hogy a vezérlő Jump hostként szolgál, amely az alhálózaton kívülről csatlakozik a fürthöz.

   * A fürtcsomópont virtuális gépek létrehozása

   * Fürtözött virtuális gépek konfigurálása a fürt létrehozásához

   * Szükség esetén létrehozhat egy új BLOB-tárolót, és konfigurálhatja háttérbeli tárolóként a fürt számára.

1. A fürt konfigurálása

   Kapcsolódjon a avere vFXT konfigurációs felületéhez (avere Vezérlőpult) a fürt beállításainak testreszabásához. Ha helyszíni adatközpontot használ, vegye igénybe a támogatás figyelését, és adja hozzá a tárolási rendszerét.

   * [Hozzáférés a vFXT-fürthöz](avere-vfxt-cluster-gui.md)
   * [Támogatás engedélyezése](avere-vfxt-enable-support.md)
   * [Tároló konfigurálása](avere-vfxt-add-storage.md) (ha szükséges)

1. Ügyfelek csatlakoztatása

   A [avere vFXT-fürt csatlakoztatása](avere-vfxt-mount-clients.md) című témakör útmutatását követve megismerheti a terheléselosztást, valamint azt, hogy az ügyfélszámítógépek hogyan csatlakoztassa a fürtöt.

1. Adatbevitel (ha szükséges)

   Mivel a avere vFXT egy skálázható többügyféles gyorsítótár, a legjobb módszer az, hogy az új háttérbeli tárolóba helyezi át az adatátvitelt, és több ügyfelet tartalmazó, többszálas fájlmásolás-stratégiát is biztosít.
   
   Ha egy új blob-tárolóba vagy más háttérbeli tárolási rendszerbe kell helyeznie a munkakészletet, kövesse az [adatáthelyezés a vFXT-fürtbe](avere-vfxt-data-ingest.md)című témakör utasításait.

## <a name="next-steps"></a>Következő lépések

Folytassa a [felkészülést a avere-vFXT létrehozásához](avere-vfxt-prereqs.md) az előfeltételként szükséges feladatok elvégzéséhez.
