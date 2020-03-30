---
title: Microsoft Azure FXT Edge Filer egység leállítása
description: Eljárások az Azure FXT Edge Filer csomópont indításához és biztonságos leállításához
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72256006"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Az Azure FXT Edge Filer hardverbiztonságos kikapcsolása

Bár a fizikai bekapcsológombbal bekapcsolhatja az egyes csomópontokat, normál körülmények között nem szabad a készüléket leállítani.

Miután egy Azure FXT Edge Filer csomópont ot használ egy fürt részeként, a fürt vezérlőpult szoftverét kell használnia a hardver leállításához. 

> [!NOTE] 
> Az esetleges adatvesztés vagy sérülés elkerülése érdekében mindig használja a Vezérlőpult szoftverét egy Azure FXT Edge Filer leállításához. A fizikai bekapcsológombot csak akkor használja leállításhoz, ha erre a Microsoft ügyfélszolgálata utasítja.
> 
> Elektromos vészhelyzet esetén húzza ki a tápkábeleket, vagy használja az adatközpont elektromos leválasztó mechanizmusát.

## <a name="shut-down-a-node-from-the-control-panel"></a>Csomópont leállítása a Vezérlőpultról

Kövesse az alábbi utasításokat az Azure FXT Edge Filer-csomópont biztonságos kikapcsolásához:

1. Jelentkezzen be a Fürt vezérlőpultjára. (Irányok a [Beállítások oldalak megnyitása párbeszédpanelen)](fxt-cluster-create.md#open-the-settings-pages)
1. Kattintson a **Beállítások** fülre, majd töltse be a **Fürt** > **FXT-csomópontok lapját.**
1. A fürtcsomópontok listájában keresse meg a leállítani kívánt csomópontot. Kattintson a **Kikapcsolás** gombra a **Műveletek** oszlopban. 
1. Várjon néhány percet. A csomópont kikapcsol, és kikapcsol.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet az állapotjelzőkről és egyéb mutatókról az [Azure FXT Edge Filer hardverállapotának monitorozása című](fxt-monitor.md)részben.
* További információ az Azure FXT Edge Filer tápegységekről a [Connect tápkábelekben.](fxt-network-power.md#connect-power-cables)
