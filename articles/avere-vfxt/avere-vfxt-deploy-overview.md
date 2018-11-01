---
title: Üzembe helyezés – áttekintés – Avere vFXT az Azure-hoz
description: Avere vFXT telepítése az Azure áttekintése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: aa5737d67ea2c9cb8cc7c7098764ae67fc91137d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634153"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT az Azure-ban – üzembe helyezés – áttekintés

Ez a cikk áttekintést be Azure-fürtön, és fut egy Avere vFXT lekéréséhez szükséges lépéseket.

Az Avere vFXT rendszer telepítése az első alkalommal azt tapasztalhatja, hogy, mint a legtöbb más Azure-eszközök üzembe helyezése több lépésből áll. A start-Befejezés folyamat törlése bizonyos értelemben kellene segít a hatókör részéről az erőfeszítés szükséges. Miután a rendszer működik és elérhető, gyorsíthatja fel a felhőalapú számítási feladatok a power teszi megéri.

## <a name="deployment-steps"></a>A központi telepítés lépései

Miután [tervezési a rendszer](avere-vfxt-deploy-plan.md), elkezdheti a Avere vFXT fürt létrehozásához. 

Első lépésként hozza létre a fürt tartományvezérlő virtuális Gépet, a vFXT fürt létrehozásához használt.

A vFXT fürt működik és elérhető, miután érdemes tudni, hogyan kell az ügyfelek csatlakozni, és ha szükséges, az adatok áthelyezése az új Blob storage-tárolóba.  

Itt látható az összes lépést áttekintése.

1. Előfeltételek konfigurálása 

   Virtuális gép létrehozása előtt hozzon létre egy új előfizetést a Avere vFXT projekt, konfigurálja az előfizetés tulajdonjogának, vegye kvóták és növelésére, ha szükséges, és fogadja el a Avere vFXT szoftver használatára vonatkozó feltételeket. Olvasási [a Avere vFXT létrehozásának előkészítéséhez](avere-vfxt-prereqs.md) részletes útmutatást.

1. A fürt vezérlő létrehozása

   A *fürt vezérlő* egy egyszerű virtuális gép, amely a Avere vFXT fürt ugyanazon a virtuális hálózaton található. A vezérlő létrehozza a vFXT és űrlapok a fürtöt, és a fürt kezelésére és teljes élettartama alatt olyan parancssori felületet is biztosít.

   Ha a tartományvezérlő egy nyilvános IP-címmel konfigurálja, is szolgálhasson jump-gazdagépként a fürt a virtuális hálózaton kívülről való csatlakozáshoz.

   Az összes szükséges a vFXT fürt létrehozása és kezelése a csomópontokat a szoftver telepítve van a fürt vezérlőn.

   Olvasási [a fürt vezérlő virtuális gép létrehozása](avere-vfxt-deploy.md#create-the-cluster-controller-vm) részleteiről.

1. A fürtcsomópontok modul szerepkör létrehozása 

   Azure az [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) történő hitelesítéséhez, a fürtcsomópont virtuális gépein bizonyos feladatokat. Például a fürtcsomópontok kell tudni hozzárendelése vagy egyéb fürtcsomópontok IP-cím ismételt hozzárendelése. A fürt létrehozása előtt meg kell határoznia egy szerepkört biztosít a számukra megfelelő engedélyekkel.

   A fürt vezérlő előre telepített szoftvereket testre szabhatja prototípus szerepkört tartalmaz. Olvasási [hozza létre a fürt csomópont hozzáférés szerepkört](avere-vfxt-deploy.md#create-the-cluster-node-access-role) útmutatást.

1. A Avere vFXT fürt létrehozása 

   A tartományvezérlőre szerkessze a megfelelő fürt létrehozási parancsprogrammal, és hajtsa végre, hogy a fürt létrehozásához. [Az üzembe helyezési parancsfájl szerkesztése](avere-vfxt-deploy.md#edit-the-deployment-script) tartalmaz részletes útmutatást. 

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