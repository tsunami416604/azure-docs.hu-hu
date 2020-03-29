---
title: Az Avere vFXT – Azure támogatásának engedélyezése
description: Az Avere vFXT for Azure támogatási feltöltésének engedélyezése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: d12bbd1708ceb948aea982f9ed1ab36879e3751c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75415369"
---
# <a name="enable-support-uploads"></a>Támogatási feltöltések engedélyezése

Az Avere vFXT for Azure automatikusan feltöltheti a fürttámogatási adatokat. Ezek a feltöltések lehetővé teszik a támogatási személyzet számára, hogy a lehető legjobb ügyfélszolgálatot nyújtsa.

## <a name="steps-to-enable-uploads"></a>A feltöltések engedélyezésének lépései

A támogatás aktiválásához kövesse az Avere vezérlőpult alábbi lépéseit. (Olvassa [el a vFXT-fürt elérését](avere-vfxt-cluster-gui.md) a vezérlőpult megnyitásának megismeréséhez.)

1. Keresse meg a **beállítások** lapot a tetején.
1. Kattintson a bal oldalon található **Támogatás** hivatkozásra, és fogadja el az adatvédelmi szabályzatot.

   ![Képernyőkép az Avere Vezérlőpultról és a Megerősítés gombbal ellátott előugró ablakról az adatvédelmi politika elfogadásához](media/avere-vfxt-privacy-policy.png)

1. A támogatási konfigurációlapon nyissa meg az **Ügyféladatok** szakaszt a bal oldali háromszögre kattintva.
1. Kattintson a **feltöltési adatok újraérvényesítése** gombra.
1. Állítsa be a fürt támogatási nevét az **Egyedi fürtnév**ben. Győződjön meg arról, hogy ez a név egyedileg azonosítja a fürtöt a támogató személyzet számára.
1. Jelölje be a **statisztikai figyelés,** az **általános információfeltöltés**és az **összeomlási adatok feltöltése jelölőnégyzetet.**
1. Kattintson a **Küldés gombra.**

   ![Képernyőkép a támogatási beállítások lap kitöltött ügyféladatok at tartalmazó szakaszáról](media/avere-vfxt-support-info.png)

1. Kattintson a biztonságos **proaktív támogatás (SPS)** bal oldalán lévő háromszögre a szakasz kibontásához.
1. Jelölje be az **SPS-kapcsolat engedélyezése jelölőnégyzetet.**
1. Kattintson a **Küldés gombra.**

   ![A támogatási beállítások lapján a biztonságos proaktív támogatás szakaszt tartalmazó képernyőkép](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>További lépések

Ha helyszíni vagy meglévő felhőalapú tárolórendszert kell hozzáadnia a fürthöz, kövesse a [Tároló konfigurálása](avere-vfxt-add-storage.md)című részben található utasításokat.

Ha készen áll az ügyfelek fürthöz csatolásának megkezdésére, olvassa el [az Avere vFXT-fürt csatlakoztatása](avere-vfxt-mount-clients.md)című olvasni.
