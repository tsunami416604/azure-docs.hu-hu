---
title: Az üzembe helyezés áttekintése – avere vFXT for Azure
description: Az Azure-hoz készült avere-vFXT üzembe helyezésének áttekintése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 9684b230b8790e36cd7442c65481c0c71ce185d6
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255423"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT az Azure-ban – üzembe helyezés áttekintése

Ez a cikk áttekintést nyújt azokról a lépésekről, amelyekkel a avere-vFXT Azure-fürthöz való beszerzése folyamatban van.

A vFXT-fürt Azure piactéren való létrehozása előtt és után több feladat szükséges. A Kezdés és a Befejezés közötti folyamat egyértelművé teszi a szükséges erőfeszítés hatókörét. 

## <a name="deployment-steps"></a>A központi telepítés lépései

[A System megtervezése](avere-vfxt-deploy-plan.md)után megkezdheti a avere vFXT-fürt létrehozását. 

Az Azure Marketplace-en Azure Resource Manager-sablon gyűjti a szükséges információkat, és automatikusan telepíti a teljes fürtöt. 

Ha a vFXT-fürt már működik, érdemes tudnia, hogyan csatlakozhat az ügyfelekhez, és szükség esetén hogyan helyezheti át az adatait az új blob Storage-tárolóba.  

Az alábbiakban áttekintheti az összes lépést.

1. Előfeltételek konfigurálása 

   A virtuális gép létrehozása előtt létre kell hoznia egy új előfizetést a avere vFXT projekthez, meg kell adnia az előfizetés tulajdonjogát, ellenőriznie kell a kvótákat, és szükség esetén meg kell adnia a növelést, és el kell fogadnia az avere vFXT szoftver használatára Részletes utasításokért olvassa el [a felkészülés a avere vFXT létrehozásához](avere-vfxt-prereqs.md) című témakört.

1. A avere vFXT-fürt létrehozása 

   Az Azure Marketplace használatával hozza létre az Azure-fürthöz készült avere-vFXT. A sablon gyűjti a szükséges információkat, és parancsfájlokat hajt végre a végső termék létrehozásához.

   A fürt létrehozása az alábbi lépéseket foglalja magában, amelyeket a piactér sablonja végez: 

   * Új hálózati infrastruktúra és erőforráscsoportok létrehozása szükség esetén
   * *Fürt* létrehozása  

     A tartományvezérlő egy egyszerű virtuális gép, amely ugyanabban a virtuális hálózatban található, mint a avere vFXT-fürt, és rendelkezik a fürt létrehozásához és kezeléséhez szükséges egyéni szoftverekkel. A vezérlő létrehozza a vFXT-csomópontokat, és megalkotja a fürtöt, és egy parancssori felületet is biztosít a fürt felügyeletéhez az élettartama során.

     Ha az üzembe helyezés során új vnet hoz létre, a vezérlő nyilvános IP-címmel fog rendelkezni. Ez azt jelenti, hogy a vezérlő Jump hostként szolgálhat a fürthöz a vnet kívülről való csatlakozáshoz.

   * A fürtcsomópont virtuális gépek létrehozása

   * A fürtcsomópont virtuális gépek konfigurálása a fürt létrehozásához

   * Szükség esetén létrehozhat egy új BLOB-tárolót, és konfigurálhatja háttérbeli tárolóként a fürt számára.

1. A fürt konfigurálása 

   Kapcsolódjon a avere vFXT konfigurációs felületéhez (avere Vezérlőpult) a fürt beállításainak testreszabásához. Ha helyszíni adatközpontot használ, vegye igénybe a támogatás figyelését, és adja hozzá a tárolási rendszerét.

   * [Hozzáférés a vFXT-fürthöz](avere-vfxt-cluster-gui.md)
   * [Támogatás engedélyezése](avere-vfxt-enable-support.md)
   * [Tároló konfigurálása](avere-vfxt-add-storage.md) (ha szükséges)

1. Ügyfelek csatlakoztatása

   A [avere vFXT-fürt csatlakoztatása](avere-vfxt-mount-clients.md) című témakör útmutatását követve megismerheti a terheléselosztást, valamint azt, hogy az ügyfélszámítógépek hogyan csatlakoztassa a fürtöt.

1. Adatbevitel (ha szükséges)

   Mivel a avere vFXT egy méretezhető többügyféles gyorsítótár, a legjobb módszer az, ha az új háttérbeli tárolóba helyezi át az adatátvitelt, több ügyfélből álló, többszálas fájlmásolás-stratégiával. A részletekért olvassa el [az adatok áthelyezése a vFXT-fürtbe](avere-vfxt-data-ingest.md) című leírást.

## <a name="next-steps"></a>Következő lépések

Folytassa a [felkészülést a avere-vFXT létrehozásához](avere-vfxt-prereqs.md) , hogy elvégezze az Azure-beli avere-vFXT üzembe helyezésének előzetes feladatait. 