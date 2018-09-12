---
title: Cserélje le a skálázási egység csomópont az Azure Stackkel integrált rendszerek |} A Microsoft Docs
description: Ismerje meg, hogyan cserélje le az Azure Stackkel integrált rendszerek fizikai skálázási egység csomópontjait.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.openlocfilehash: 1b37b150dad4951a4ade81f226b515ce9cae9053
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377054"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Cserélje le az Azure Stackkel integrált rendszerek a skálázási egység csomópont

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Ez a cikk ismerteti az általános folyamat helyett egy fizikai számítógépen (más néven egy *skálázási egység csomópont*) a az Azure Stackkel integrált rendszer. Tényleges skálázási egység csomópont helyettesítő lépései eltérőek lesznek a számítógépgyártó (OEM) hardver szállítójával alapján. Konkrétan a rendszer a részletes lépéseket a gyártója által biztosított mező telepen cserélhető egység (FRU) dokumentációjában talál.

Az alábbi folyamatábrája bemutatja cserélje le a teljes méretezési egység csomópont FRU általános folyamata.

![Cserélje le a csomópont folyamatot folyamatábra](media/azure-stack-replace-node/replacenodeflow.png)

* Ez a művelet nem lehet szükség a fizikai hardverek feltétel alapján.

## <a name="review-alert-information"></a>Tekintse át a riasztás adatai

Ha egy méretezési egység csomópont nem működik, kapni fog a következő kritikus riasztások:

- Nem csatlakozik a hálózati vezérlő csomópont
- A csomópont nem érhető el a virtuálisgép-elhelyezés
- Skálázási egység csomópont offline állapotban.

![A skálázási egység le riasztások listája](media/azure-stack-replace-node/nodedownalerts.png)

Ha megnyitja a **skálázási egység csomópont offline állapotban** riasztást, a riasztás leírásában a skálázási egység csomópontot tartalmaz, amely nem érhető el. Az OEM-specifikus figyelési megoldás, amely a hardver életciklus gazdagépen fut is kaphat további riasztásokat.

![A csomópont offline riasztás részletei](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Skálázási egység csomópont cseréjét.

A következő lépések szolgálnak, a skálázási egység csomópont cseréjét magas szintű áttekintését. A részletes lépéseket, amelyek az adott rendszerhez a OEM hardver szállítójával FRU dokumentációjában talál. Ne kövesse ezeket a lépéseket az OEM által biztosított dokumentációt hivatkozó nélkül.

1. Használja a [kiürítési](azure-stack-node-actions.md#scale-unit-node-actions) műveletet a skálázási egység csomópont karbantartási módba. Ez a művelet nem lehet szükség a fizikai hardverek feltétel alapján.

   > [!NOTE]
   > Minden esetben csak egy csomópont lehet ürítve és ki van kapcsolva egy időben az S2D megszakítása nélkül (a közvetlen tárolóhelyek).

2. Ha a csomópont továbbra is be van kapcsolva, használja a [kikapcsolásához](azure-stack-node-actions.md#scale-unit-node-actions) művelet. Ez a művelet nem lehet szükség a fizikai hardverek feltétel alapján.
 
   > [!NOTE]
   > A valószínűtlen eset, hogy a művelet kikapcsolás nem működik használja helyette a alaplapi felügyeleti vezérlőnek (BMC) webes felületén.

1. Cserélje le a fizikai számítógép. Általában ez történik a számítógép-Gyártói hardver gyártója által.
2. Használja a [javítási](azure-stack-node-actions.md#scale-unit-node-actions) műveleteket kíván hozzáadni az új fizikai számítógépet a skálázási egység.
3. Használja a kiemelt végponthoz [Virtuálislemez-javítási állapotának ellenőrzéséhez](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Az új adatok meghajtókat egy teljes helyreállítási feladat rendszerterheléstől függően több órát is igénybe vehet, és a felhasznált lemezterület.
4. A javítási művelet befejezése után, ellenőrizze, hogy az összes aktív riasztás automatikusan lezárták.

## <a name="next-steps"></a>További lépések

- További információ a gyakran használt adatok rétegére – cserélhető fizikai lemez cseréje: [olyan lemezt cserél ki](azure-stack-replace-disk.md). 
- További információ a nem ritkáról gyakori elérésű-cserélhető hardverkomponensek cseréje: [hardverkomponensek cseréje](azure-stack-replace-component.md).
