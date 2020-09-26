---
title: A Azure NetApp Files pillanatkép-házirendjeinek hibáinak megoldása | Microsoft Docs
description: Azokat a hibaüzeneteket és megoldásokat ismerteti, amelyek segítséget nyújtanak a pillanatkép-házirendek kezelési problémáinak elhárításában Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 1d7a91de8fa505fe4c2b06eea59f3acc2ae1f7e8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343315"
---
# <a name="troubleshoot-snapshot-policies"></a>Pillanatkép-házirendek – problémamegoldás

Ez a cikk a Azure NetApp Files pillanatkép-szabályzatok kezelése során felmerülő hibákra vonatkozó forgatókönyveket ismerteti. Emellett olyan megoldásokat is biztosít, amelyek segíthetnek a problémák megoldásában.

## <a name="snapshot-policy-creation-failing-with-invalid-snapshot-policy-name"></a>A pillanatkép-szabályzat létrehozása nem sikerült – a pillanatkép-házirend neve érvénytelen

Hiba történik a pillanatkép-szabályzat létrehozásakor, ha a pillanatkép-házirend neve érvénytelen. A pillanatkép-szabályzatok neveire a következő irányelvek érvényesek:  

* A pillanatkép-szabályzat neve nem tartalmazhat nem ASCII vagy speciális karaktereket.
* A pillanatkép-szabályzat nevének betűvel vagy számmal kell kezdődnie, és csak betűket, számokat, aláhúzást (_) és kötőjeleket (-) tartalmazhat.
* A pillanatkép-szabályzat nevének 1 és 64 karakter közöttinek kell lennie.  

A pillanatkép-szabályzat nevét az irányelvek alapján kell módosítani.  

## <a name="snapshot-policy-creation-failing-with-invalid-values"></a>A pillanatkép-szabályzat létrehozása nem sikerült – érvénytelen értékek 

A Azure NetApp Files nem tud pillanatkép-szabályzatot létrehozni, ha érvénytelen értéket ad meg egy mezőhöz, például `Number of snapshots to keep` vagy `Minute on the hour to take snapshot` .  
 
Az érvényes értékek a következők:   

* Az értéknek érvényes számnak kell lennie.
* Az értéknek 0 és 59 közöttinek kell lennie.

Győződjön meg arról, hogy a mezőkhöz érvényes érték van megadva.

## <a name="snapshot-policy-creation-failing-with-total-number-of-snapshots-to-keep-exceeds-255-error"></a>A pillanatkép-szabályzat létrehozása sikertelen "a pillanatképek teljes száma meghaladja a 255" hibát 

Minden kötet [legfeljebb 255 pillanatképet](azure-netapp-files-resource-limits.md)tartalmazhat. A maximális érték az óránkénti, napi, heti és havi Pillanatképek összegét tartalmazza. Csökkentse az `Snapshots to keep` értéket, és próbálkozzon újra.

## <a name="assigning-policy-to-a-volume-failing-with-total-snapshot-policy-is-over-the-max-255-error"></a>Házirend társítása egy kötethez – a pillanatkép-szabályzat teljes száma meghaladja a maximális "255" hibát

Minden kötet [legfeljebb 255 pillanatképet](azure-netapp-files-resource-limits.md)tartalmazhat. Ha az igény szerinti, óránkénti, napi, heti és havi Pillanatképek összege meghaladja a maximális értéket, hiba történik. Csökkentse az `snapshots to keep` értéket, vagy töröljön néhány igény szerinti pillanatképet, és próbálkozzon újra.

## <a name="next-steps"></a>Következő lépések  

* [Pillanatkép-házirendek kezelése](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
