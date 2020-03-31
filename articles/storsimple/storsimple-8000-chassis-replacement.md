---
title: Cserélje ki a vázat a StorSimple 8000 sorozatú eszközön | Microsoft dokumentumok
description: Bemutatja, hogyan távolíthatja el és cserélheti le a StorSimple elsődleges ház vagy Az EBOD ház alvázát.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 7dfc39f4d08c8a49d1564a0a5bd7e3ef4156e3fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61312445"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Cserélje ki a storSimple eszköz házát
## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan távolíthatja el és cserélheti le a storSimple 8000 sorozatú eszközök alvázát. A StorSimple 8100 modell egyetlen beépített eszköz (egy ház), míg a 8600 egy kettős ház eszköz (két ház). Egy 8600-as modell esetében potenciálisan két ház lehet, amelyek meghibásodhatnak az eszközben: az elsődleges ház háza vagy az EBOD ház házának háza.

A Microsoft által szállított csereház mindkét esetben üres. Nem power and Cooling modules (PCMs), vezérlő modulok, ssds (SSD), merevlemez-meghajtók (HDD), vagy EBOD modulok at.

> [!IMPORTANT]
> A ház eltávolítása és cseréje előtt tekintse át a [StorSimple hardverösszetevő-csere](storsimple-8000-hardware-component-replacement.md)biztonsági tudnivalóit.


## <a name="remove-the-chassis"></a>Az alváz eltávolítása
Hajtsa végre az alábbi lépéseket, hogy távolítsa el a ház a StorSimple eszköz.

#### <a name="to-remove-a-chassis"></a>Alváz eltávolítása
1. Győződjön meg arról, hogy a StorSimple eszköz le van kapcsolva, és le van választva az összes áramforrásról.
2. Távolítsa el az összes hálózati és SAS-kábelt, ha van ilyen.
3. Vegye ki a készüléket a rackből.
4. Távolítsa el az egyes meghajtókat, és jegyezze fel azokat a bővítőhelyeket, amelyekről eltávolították őket. További információt [a Lemezmeghajtó eltávolítása](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive)című témakörben talál.
5. Az EBOD ház (ha ez a ház, amely nem sikerült), távolítsa el az EBOD vezérlő modulokat. További információ: [EBOD-vezérlő eltávolítása.](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller)
   
    Az elsődleges ház (ha ez a ház, hogy nem sikerült), távolítsa el a vezérlőket, és vegye figyelembe a bővítőhely, amelyből eltávolították. További információt a Vezérlő eltávolítása című témakörben [talál.](storsimple-8000-controller-replacement.md#remove-a-controller)

## <a name="install-the-chassis"></a>A ház felszerelése
Hajtsa végre az alábbi lépéseket a ház StorSimple eszközre való telepítéséhez.

#### <a name="to-install-a-chassis"></a>Ház telepítése
1. Szerelje fel az alvázat az állványra. További információ: [Rack-mount a StorSimple 8100 eszköz](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) vagy [Rack-mount a StorSimple 8600 eszköz](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Miután a ház az állványra van szerelve, telepítse a vezérlőmodulokat ugyanabban a pozícióban, mint korábban.
3. Telepítse a meghajtókat ugyanabban a pozícióban és bővítőhelyen, amelybe korábban telepítették őket.
   
   > [!NOTE]
   > Javasoljuk, hogy először telepítse az SSD-ket a tárolóhelyekre, majd telepítse a HDD-ket.
  
4. Ha az eszköz az állványra van szerelve, és az összetevők telepítve vannak, csatlakoztassa az eszközt a megfelelő áramforráshoz, és kapcsolja be az eszközt. További információt [a StorSimple 8100-as eszköz kábelezése](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) vagy [a StorSimple 8600-as eszköz kábelezése.](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)

## <a name="next-steps"></a>További lépések
További információ a [StorSimple hardverösszetevő-cseréjéről.](storsimple-8000-hardware-component-replacement.md)

