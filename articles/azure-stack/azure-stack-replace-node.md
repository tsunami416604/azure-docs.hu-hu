---
title: "Cserélje le a skálázási egység csomópont az integrált Azure verem rendszeren |} Microsoft Docs"
description: "Cserélje le a fizikai skálázási egység csomópont az integrált Azure verem rendszeren itt olvashat."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: 4e5b1269e2bee31316cba99d69ea2a6d702faf05
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/23/2017
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Cserélje le a skálázási egység csomópont az integrált Azure verem rendszeren

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ez a cikk ismerteti az általános folyamat a fizikai számítógép helyett (más néven a *skálázási egység csomópont*) egy Azure veremben integrált a rendszer. Tényleges skálázási egység csomópont helyettesítő lépései eltérőek lesznek a számítógépgyártó (OEM) hardvergyártójához alapján. A rendszer vonatkozó részletes lépéseket a gyártója által biztosított mező cserélhető Cisco egységet (FRU) dokumentációjában talál.

A következő folyamatábra általános FRU folyamata lecseréli egy teljes skálázási egység csomópont jeleníti meg.

![A név felülírandó csomópont folyamat folyamatábrája](media/azure-stack-replace-node/ReplaceNodeFlow.PNG)

* Ez a művelet nem lehet szükség a fizikai hardver-feltételen alapszik.

## <a name="review-alert-information"></a>Riasztási információk áttekintése

Ha egy méretezési egység csomópont nem működik, az összes következő kritikus riasztás kap:

- A csomópont nem kapcsolódik a hálózati vezérlő
- A csomópont nem érhető el a virtuális gépek elhelyezése
- Skálázási egység csomópontja offline állapotban

![Skálázási egység le riasztások listája](media/azure-stack-replace-node/NodeDownAlerts.PNG)

Megnyitja a "skálázási egység csomópontja offline állapotban" riasztás, ha a riasztás leírásában a skálázási egység csomópont, amely nem érhető el. Az OEM-specifikus figyelési megoldást igényelnek, hogy a hardver életciklus gazdagépen fut a további riasztásokat is megjelenhet.

![Csomópont offline riasztás részletei](media/azure-stack-replace-node/NodeOffline.PNG)

## <a name="scale-unit-node-replacement-process"></a>Skálázási egység csomópont cseréjét.

A következő lépések vannak megadva, a méretezési egység csomópont cseréjét magas szintű áttekintését. A rendszer vonatkozó részletes lépéseket a OEM hardver gyártója által biztosított FRU dokumentációjában talál. Az OEM által biztosított dokumentációt utaló nélkül nem kövesse az alábbi lépéseket.

1. Használja a [kiürítésére](azure-stack-node-actions.md#scale-unit-node-actions) műveletet a skálázási egység csomópont állítható karbantartási üzemmódba. Ez a művelet nem lehet szükség a fizikai hardver-feltételen alapszik.

   > [!NOTE]
   > Minden esetben csak egy csomópont le is, és ki van kapcsolva egy időben (közvetlen tárolóhelyek) SSD megszakítása nélkül.

2. Ha a csomópont továbbra is be van kapcsolva, használja a [kikapcsolásához](azure-stack-node-actions.md#scale-unit-node-actions) művelet. Ez a művelet nem lehet szükség a fizikai hardver-feltételen alapszik.
 
   > [!NOTE]
   > Az valószínű esetében, amelyek a kikapcsolási művelet nem működik használja helyette az alaplapi felügyeleti vezérlővel (BMC) webes felülete.

1. A fizikai számítógép helyett. Általában ehhez a OEM hardver gyártójához.
2. Használja a [javítási](azure-stack-node-actions.md#scale-unit-node-actions) műveletet az új fizikai számítógép hozzáadása a méretezési egységhez.
3. Használja a rendszerjogosultságú végpontot [tekintse meg a virtuális lemezek javítási](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Új adatok meghajtók a teljes helyreállítási feladat rendszerterhelés függően több órát is igénybe vehet, és a felhasznált lemezterület.
4. A javítási művelet befejeződését követően ellenőrizze, hogy az összes aktív riasztás automatikusan bezárták.

## <a name="next-steps"></a>További lépések

- További információ a közbeni-cserélhető fizikai lemez cseréje: [olyan lemezt cserél ki](azure-stack-replace-disk.md). 
- További információ a nem közbeni-cserélhető hardverösszetevő cseréje: [cserélje ki egy hardverösszetevő](azure-stack-replace-component.md). 
