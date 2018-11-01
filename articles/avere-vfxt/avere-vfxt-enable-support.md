---
title: Avere vFXT – Azure-támogatás engedélyezése
description: Az Azure-ban Avere vFXT származó feltöltések támogatás engedélyezése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: bc21e06b704bfe3d25132092efbbf23f342acb14
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634048"
---
# <a name="enable-support-uploads"></a>Támogatási feltöltések engedélyezése

Az Azure-hoz a Avere vFXT automatikusan feltöltheti a fürttel kapcsolatos támogatási adatok. Ezek lehetővé teszik a támogatási személyzet feltöltések adja meg az ajánlott ügyfélszolgálat.

## <a name="steps-to-enable-uploads"></a>További lépések elvégzésével feltöltések

Kövesse az alábbi lépéseket a Avere Vezérlőpult támogatás aktiválásához. (Olvasás [a vFXT fürt eléréséhez](avere-vfxt-cluster-gui.md) megismeréséhez nyissa meg a Vezérlőpultot Avere.)

1. Keresse meg a **beállítások** a felső fülön.
1. Kattintson a **támogatási** a bal oldali hivatkozásra, és fogadja el az adatvédelmi nyilatkozatát.

   ![Képernyőkép: Ellenőrizze az adatvédelmi szabályzat elfogadása](media/avere-vfxt-privacy-policy.png)
1. Kattintson a háromszögre balra **Customer Info** a szakasz kibontásához.
1. Állítsa be a fürt támogatási nevét **egyedi fürtnév** – ellenőrizze, hogy a fürt a támogató személyzet egyedileg azonosítja.
1. Jelölje be a **statisztikák figyelése**, **általános információkat feltöltése**, és **összeomlási adatokat feltölteni**.
1. Kattintson a **ellenőrzése feltöltési információk** gombra.
1. Kattintson a **elküldése**.
1. Kattintson a háromszögre balra **biztonságos proaktív támogatási (Szervizcsomagok)** a szakasz kibontásához.
1. Jelölje be a **Szervizcsomagok hivatkozás engedélyezése**.
1. Kattintson a **elküldése**.

   ![Támogatás engedélyezéséhez szükséges összes lépést tartalmazó képernyőképe](media/avere-vfxt-support-info-steps.png)


## <a name="next-steps"></a>További lépések

Ha szeretne egy helyi tárolórendszer hozzáadni a fürthöz, kövesse a [konfigurálta a tárterületet](avere-vfxt-add-storage.md). 

Ha készen áll a fürt, olvassa el az ügyfelek csatolása [a Avere vFXT fürt csatlakoztatási](avere-vfxt-mount-clients.md).