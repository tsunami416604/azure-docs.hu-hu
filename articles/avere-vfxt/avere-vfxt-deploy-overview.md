---
title: A telepítés áttekintése – Avere vFXT for Azure
description: Az Avere vFXT azure-hoz történő üzembe helyezésének áttekintése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78140fea74272dff6056bebfbd44ed9d55b0e1db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153683"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure – telepítés áttekintése

Ez a cikk áttekintést nyújt az Avere vFXT Azure-fürthöz való be- és működéséhez szükséges lépésekről.

A vFXT-fürt létrehozása előtt és után számos feladatra van szükség az Azure Piactérről. Miután egyértelmű értelemben a start-to-finish folyamat segít hatálya szükséges erőfeszítést.

## <a name="deployment-steps"></a>A központi telepítés lépései

A [rendszer megtervezése](avere-vfxt-deploy-plan.md)után elkezdheti létrehozni az Avere vFXT-fürtöt.

Az Azure Resource Manager-sablon az Azure Marketplace-en összegyűjti a szükséges információkat, és automatikusan telepíti a teljes fürtöt.

A vFXT-fürt működése után még mindig van néhány konfigurációs lépés, amelyet meg kell tennie a használata előtt. Ha létrehozott egy új Blob storage tárolót, érdemes áthelyezni az adatokat. Ha NAS tárolórendszert használ, a fürt létrehozása után hozzá kell adnia. Az ügyfeleket a fürthöz kell csatlakoztatnia.

Íme egy áttekintés az összes lépésről.

1. Előfeltételek konfigurálása

   Virtuális gép létrehozása előtt létre kell hoznia egy új előfizetést az Avere vFXT projekthez, konfigurálnia kell az előfizetés tulajdonjogát, ellenőriznie kell a kvótákat, és szükség esetén növelnie kell magát, és el kell fogadnia az Avere vFXT szoftver használatára vonatkozó feltételeket. Olvassa [el A Felkészülés az Avere vFXT létrehozásához](avere-vfxt-prereqs.md) részletes utasításokért.

1. Az Avere vFXT-fürt létrehozása

   Az Azure Marketplace használatával hozza létre az Avere vFXT azure-fürthöz. A sablon összegyűjti a szükséges információkat, és parancsfájlokat hajt végre a végtermék létrehozásához.

   A fürt létrehozása a következő lépéseket foglalja magában, amelyeket a piactérsablon végez:

   * Szükség esetén új hálózati infrastruktúra- és erőforráscsoportok létrehozása
   * Fürtvezérlő létrehozása

     A fürtvezérlő egy egyszerű virtuális gép, amely ugyanabban a virtuális hálózatban található, mint az Avere vFXT-fürt, és rendelkezik a fürt létrehozásához és kezeléséhez szükséges egyéni szoftverrel. A vezérlő létrehozza a vFXT-csomópontokat, és alkotja a fürtöt, és egy parancssori felületet is biztosít a fürt élettartama alatt történő kezeléséhez.

     Ha a központi telepítés során új virtuális hálózatot vagy alhálózatot hoz létre, a vezérlő nyilvános IP-címmel fog rendelkezni. Ez azt jelenti, hogy a vezérlő ugróállomásként szolgálhat a fürthöz a virtuális hálózaton kívülről való csatlakozáshoz.

   * A fürtcsomópont virtuális gépeinek létrehozása

   * A fürt létrehozása az egyes csomópontokból

   * Szükség esetén hozzon létre egy új Blob-tárolót, és konfigurálja a fürt háttértárolójaként

   A fürt létrehozását részletesen a [vFXT-fürt telepítése](avere-vfxt-deploy.md)ismerteti.

1. A fürt konfigurálása

   Csatlakozzon az Avere vFXT konfigurációs felületéhez (Avere Vezérlőpult) a fürt beállításainak testreszabásához. Engedélyezheti a támogatás figyelését, és adja hozzá a tárolórendszert, ha hardveres tárolót vagy további Blob-tárolókat használ.

   * [Hozzáférés a vFXT-fürthöz](avere-vfxt-cluster-gui.md)
   * [Támogatás engedélyezése](avere-vfxt-enable-support.md)
   * [Tároló konfigurálása](avere-vfxt-add-storage.md) (ha szükséges)

1. Ügyfelek csatlakoztatása

   Kövesse az [Avere vFXT-fürt csatlakoztatása](avere-vfxt-mount-clients.md) című útmutatót a terheléselosztásról és afürt csatlakoztatásának módjáról.

1. Adatok hozzáadása (ha szükséges)

   Mivel az Avere vFXT egy skálázható többügyféles gyorsítótár, az adatok új háttértárolóba való áthelyezésének legjobb módja egy többügyféles, többszálas fájlmásolási stratégia.

   Ha a munkahalmaz-adatokat át kell helyeznie egy új Blob-tárolóba vagy más háttértárolórendszerbe, kövesse az [Adatok áthelyezése a vFXT-fürtbe](avere-vfxt-data-ingest.md)című útmutatóutasításait.

## <a name="next-steps"></a>További lépések

Folytassa [az Avere vFXT létrehozásának előkészítését](avere-vfxt-prereqs.md) az előfeltételként szolgáló feladatok elvégzéséhez.
