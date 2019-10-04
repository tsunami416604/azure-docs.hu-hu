---
title: A StorSimple 8000 sorozatú eszköz a ház cseréje |} A Microsoft Docs
description: Távolítsa el, és a StorSimple elsődleges ház vagy EBOD ház a ház cseréje módját ismerteti.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61312445"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>A StorSimple eszközön a ház cseréje
## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan távolítsa el, és a egy StorSimple 8000 sorozatú eszköz a ház cseréje. A StorSimple 8100-as modell (egy házban csoportosítva), egyetlen lemezház eszköz, mivel a 8600-as kettős ház eszköz (két váz). Egy 8600-as modellhez sikertelen lehet, az eszközt a vélhetően két váz vannak: a váz, az elsődleges ház vagy a váz a EBOD ház számára.

Mindkét esetben a helyettesítő váz, amely tartalmazza a szükséges a Microsoft je prázdná. Nincs Power és hűtéssel modulok (PCMs), vezérlő-modulok szemben – tartós állapotú meghajtók (SSD-kkel), merevlemezes (HDD) meghajtók vagy EBOD modulok fognak szerepelni.

> [!IMPORTANT]
> Mielőtt eltávolítása és cseréje a váz, tekintse át a biztonsági információk [StorSimple összetevő hardvercseréhez](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Távolítsa el a váz
A következő lépésekkel távolítsa el a váz a StorSimple eszközön.

#### <a name="to-remove-a-chassis"></a>A váz eltávolítása
1. Győződjön meg arról, hogy a StorSimple-eszköz leállítja, majd az áramforrásokból kapcsolódik.
2. Távolítsa el a hálózat és a SAS-kábel, ha van ilyen.
3. Távolítsa el a egység az állványra szerelt.
4. Távolítsa el az egyes meghajtókat, és jegyezze fel a tárolóhelyek, amelyből el lesznek távolítva. További információkért lásd: [távolítsa el a lemezmeghajtó](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. (Ha ez a váz sikertelenül) a EBOD tárolóeszközön távolítsa el az EBOD-vezérlő modulokat. További információkért lásd: [távolítsa el az EBOD-vezérlő](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    A elsődleges tárolóeszközön (Ha ez a váz sikertelenül) távolítsa el a tartományvezérlők, és jegyezze fel a tárolóhelyek, amelyből el lesznek távolítva. További információkért lásd: [egy vezérlő eltávolítása](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Telepítse a váz
A következő lépésekkel telepítse a váz a StorSimple-eszköz.

#### <a name="to-install-a-chassis"></a>A váz telepítése
1. Csatlakoztassa a váz, az állvány egyik kiszolgálója. További információkért lásd: [Rack csatlakoztatást a StorSimple 8100-as eszköz](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) vagy [Rack csatlakoztatást a StorSimple 8600-as eszköz](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Miután a váz csatlakoztatva van, az állvány egyik kiszolgálója, a tartományvezérlő-modulok telepítéséhez helyzetben, amelyeket korábban telepítettek.
3. Telepítse a meghajtók azonos pozíciók és tárolóhelyei, amelyeket korábban telepítettek.
   
   > [!NOTE]
   > Azt javasoljuk, hogy először telepítse az SSD-k tárolóhelyben, és telepítheti őket a HDD.
  
4. Az eszköz csatlakoztatva van, az állvány és a telepített összetevők az eszköz csatlakoztatása a megfelelő áramforrásokból, és kapcsolja be az eszközt. További információkért lásd: [a StorSimple 8100 sorozatú eszköz kábelezése](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) vagy [a StorSimple 8600 sorozatú eszköz kábelezése](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>További lépések
Tudjon meg többet [StorSimple összetevő hardvercseréhez](storsimple-8000-hardware-component-replacement.md).

