---
title: Hogyan lehet leállítani a Microsoft Azure FXT Edge Filer egység
description: Indítási és a egy Azure FXT Edge Filer csomópont biztonságos leállítás eljárásai
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 6921e7a52e43a63055b59242c02cc6ca3b8c5313
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620101"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Hogyan lehet Azure FXT Edge Filer hardver biztonságosan kikapcsolása

Bár a fizikai főkapcsoló váltani egy adott csomóponton, akkor nem használja, a normál körülmények egység leállítása.

Miután az Azure FXT Edge Filer csomópont egy fürt részeként használja, a fürt panel szoftver állítsa le a hardvert kell használnia. 

> [!NOTE] 
> Esetleges adatvesztés vagy -sérülés elkerülése érdekében mindig használja a Vezérlőpult szoftvert egy Azure FXT Edge Filer leállítása. Ne használja a fizikai főkapcsoló leállítási hacsak ezt a Microsoft ügyfélszolgálatát, és kéri erre.
> 
> Elektromos vészhelyzet tápkábelek leválasztása, vagy használja az adatok center áram leválasztása mechanizmus.

## <a name="shut-down-a-node-from-the-control-panel"></a>Csomópont eltávolítása a Vezérlőpult leállítása

Kövesse ezeket az utasításokat az Azure FXT Edge Filer csomópont biztonságosan kikapcsolásához:

1. Jelentkezzen be a fürt Vezérlőpult. (Utasításait [nyissa meg a beállításlapok](fxt-cluster-create.md#open-the-settings-pages))
1. Kattintson a **beállítások** lapra, majd feltöltheti a **fürt** > **FXT csomópontok** lapot.
1. Keresse meg a megfelelőt, állítsa le a fürtcsomópontok listájának. Kattintson a **kapcsolja ki** gombra annak **műveletek** oszlop. 
1. Várjon egy kis ideig. A csomópont leállítja, majd magát kikapcsolásához.

## <a name="next-steps"></a>További lépések

* Ismerje meg az állapot LED-EK és egyéb jeleket a [figyelő Azure FXT Edge Filer hardverállapot](fxt-monitor.md).
* További információt az Azure FXT Edge Filer power választékát kínálja a [tápkábelek csatlakoztatása](fxt-network-power.md#connect-power-cables).
