---
title: "Fizikai memória kapacitásának Azure verem kezelése |} Microsoft Docs"
description: "Megfigyelését és felügyeletét a rendelkezésre álló szabad hely Azure verem."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/13/2018
ms.author: mabrigg
ms.reviewer: Thomas.Roettinger
ms.openlocfilehash: b922790d51c7028c37bb5863d43e99e19790488c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Fizikai memória kapacitásának Azure verem kezelése

*A következőkre vonatkozik: Azure verem integrált rendszerek*

A teljes rendelkezésre álló memória kapacitásbővítés Azure verem, több memóriát is hozzáadhat. Azure-készletben a fizikai kiszolgáló is hivatkozunk egy *skálázási egység csomópont*. Összes méretezési egység tagcsomópontja egyetlen skálázási egységére azonos memóriamennyiség kell rendelkeznie.

> [!note]  
> A folytatás előtt tekintse át a hardver gyártó dokumentációjában, ha a memória frissíti a gyártó egy fizikai memória-frissítés támogatja. Az OEM hardver gyártójával támogatási szerződése szükség lehet, hogy a szállító hajtsa végre a fizikai kiszolgáló állvány elhelyezése és az eszköz belső vezérlőprogram frissítése.

A következő folyamatábra bemutatja, az általános folyamat egyes skálázási egység csomópont memóriáját.

![Vegyen fel minden skálázási egység csomópont memória](media\azure-stack-manage-storage-physical-capacity\process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Memória hozzáadása egy meglévő csomópont
Az alábbi lépéseket a Hozzáadás folyamatát magas szintű áttekintését adja meg. 

> [!Warning]  
Az OEM által biztosított dokumentációt utaló nélkül nem kövesse az alábbi lépéseket.

> [!Warning]  
A teljes skálázási egység nem támogatja a működés közbeni memória frissítés le kell állítani.

1. Állítsa le az Azure-verem leírt lépések alapján a [kezdési és befejezési Azure verem](azure-stack-start-and-stop.md) cikk.
2. A memória, a hardver gyártójával dokumentációnak fizikai számítógépekre frissítése.
3. Azure Stack a lépéseket követve indítsa el a [kezdési és befejezési Azure verem](azure-stack-start-and-stop.md) cikk.

## <a name="next-steps"></a>További lépések

 - Azure-készletben található, helyreállítására és az üzleti igények alapján tárolási kapacitás visszaigényléséhez storage-fiókok kezelése című témakörben talál [verem Azure storage-fiókok kezelése](azure-stack-manage-storage-accounts.md).
 - Az Azure-verem felhő üzemeltetője figyeli, és kezeli a saját Azure Alkalmazásveremben üzembe tárolókapacitását további tudnivalókért lásd: [kezelése az Azure-verem a tárolókapacitás](azure-stack-manage-storage-shares.md). 