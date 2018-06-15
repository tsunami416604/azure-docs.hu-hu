---
title: Cserélje le a StorSimple 8000 series eszközön váz |} Microsoft Docs
description: Távolítsa el, és cserélje le a StorSimple elsődleges ház vagy EBOD ház váz ismerteti.
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
ms.openlocfilehash: 073fcf0064f1d1482f4683d733f00cf918ff2f38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874800"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Cserélje le a StorSimple eszköz készülékház
## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan cserélje ki a StorSimple 8000 series eszköz eszközök befogadására szolgáló vázat. A StorSimple 8100 modell történik egyetlen ház (egy házban csoportosítva), mivel a 8600 kettős ház eszköz (két váz). Egy 8600 modell nincsenek esetleg sikertelen lehet, az eszköz két váz: az elsődleges ház vagy a EBOD ház a váz váz.

Mindkét esetben a helyettesítő váz van a Microsoft által szállított mező üres. Nincs energia- és hűtési modulok (PCMs), a tartományvezérlő modulok, tartós állapotú meghajtók (SSD), a merevlemezes (HDD) meghajtók vagy a EBOD modulokat is fog szerepelni.

> [!IMPORTANT]
> Mielőtt eltávolítása és cseréje a váz, tekintse át a biztonsági információk [StorSimple hardver összetevő cseréje](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>A váz eltávolítása
A következő lépésekkel távolítsa el a StorSimple eszköz váz.

#### <a name="to-remove-a-chassis"></a>A váz eltávolítása
1. Győződjön meg arról, hogy a StorSimple eszköz leállítása és bontotta a kapcsolatot a áramforrásokból.
2. Távolítsa el a hálózat és a SAS-kábel, ha van ilyen.
3. Távolítsa el az egység a állvány.
4. Távolítsa el a meghajtókhoz, és jegyezze fel a tárhelyek, amelyről el. További információkért lásd: [távolítsa el a merevlemez](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. A (Ha ez a váz sikertelen) EBOD tárolóeszközön távolítsa el a EBOD vezérlő modulok. További információkért lásd: [távolítsa el az EBOD vezérlőhöz](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    A elsődleges tárolóeszközön (Ha ez a váz sikertelenül) távolítsa el a tartományvezérlők, és jegyezze fel a tárhelyek, amelyről el. További információkért lásd: [távolítsa el a tartományvezérlő](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>A váz telepítése
Telepítéséhez hajtsa végre a következő lépéseket a váz a StorSimple eszköz.

#### <a name="to-install-a-chassis"></a>A váz telepítése
1. Csatlakoztassa az állvány váz. További információkért lásd: [állvány csatlakoztatást a StorSimple 8100 eszköz](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) vagy [állvány csatlakoztatást a StorSimple 8600 eszköz](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. A váz csatlakoztatva van, az állvány, miután a tartományvezérlő-modulok telepítése azokat korábban telepített azonos pozícióra.
3. Telepítse a meghajtók azonos pozíciók és üzembe helyezési ponti, akkor a korábban telepített.
   
   > [!NOTE]
   > Azt javasoljuk, hogy a tárolóhelyek az SSD-k először telepíteni, és telepítse a HDD-k.
  
4. Az eszköz, az állvány és a telepített összetevők-e csatlakoztatva csatlakoztassa az eszközt a megfelelő áramforrásokból, és kapcsolja be az eszközt. További információkért lásd: [StorSimple 8100 Bekábelezésére](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) vagy [StorSimple 8600 Bekábelezésére](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Következő lépések
További információ [StorSimple hardver összetevő cseréje](storsimple-8000-hardware-component-replacement.md).

