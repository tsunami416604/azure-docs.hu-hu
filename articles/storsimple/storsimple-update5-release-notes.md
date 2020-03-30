---
title: StorSimple 8000 sorozat 5.
description: A cikk a StorSimple 8000 series 5.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275173"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>StorSimple 8000 sorozat 5.

## <a name="overview"></a>Áttekintés

A következő kibocsátási megjegyzések ismertetik az új funkciókat, és azonosítják a StorSimple 8000 sorozat 5. Tartalmaznak továbbá egy listát a StorSimple szoftverfrissítések szerepelnek ebben a kiadásban.

Az 5-ös frissítés bármely StorSimple eszközre alkalmazható, amely a 0.1-es frissítést a 4-es frissítésen keresztül futtatja. Az 5.frissítéshez tartozó eszközverzió a 6.3.9600.17845.

Tekintse át a kiadási megjegyzésekben található információkat, mielőtt telepítené a frissítést a StorSimple-megoldásban.

> [!IMPORTANT]
> * Az 5-ös frissítés kötelező frissítés, amelyet azonnal telepíteni kell. További információt az [5.](storsimple-8000-install-update-5.md)
> * Az 5-ös frissítés eszközszoftverrel, lemezes firmware-rel, operációs rendszer biztonságával és egyéb operációs rendszer-frissítésekkel rendelkezik. A frissítés telepítése körülbelül 4 órát vesz igénybe. A lemez belső vezérlőprogramjának frissítése zavaró frissítés, amely az eszköz leállását eredményezi. Javasoljuk, hogy az 5.frissítés telepítésével tartsa naprakészen az eszközt.
> * Az új kiadások esetében előfordulhat, hogy nem jelennek meg azonnal a frissítések, mert a frissítések fokozatos bevezetését tesszük. Várjon néhány napot, majd keresse meg újra a frissítéseket, mivel ezek a frissítések hamarosan elérhetővé válnak.

## <a name="whats-new-in-update-5"></a>Az 5.

Az 5-ös frissítésben a következő kulcsfontosságú fejlesztések és hibajavítások történtek.

* **Az Azure Active Directory (AAD) használata a StorSimple Eszközkezelő szolgáltatással való hitelesítéshez** – az 5.frissítéstől kezdve az Azure Active Directory a StorSimple Eszközkezelő szolgáltatással való hitelesítéshez használható. A régi hitelesítési mechanizmus 2017 decemberére elavult. Minden felhasználónak tartalmaznia kell az új hitelesítési URL-eket a tűzfalszabályokban. További információért látogasson el [a StorSimple-eszköz hálózati követelményeiben felsorolt hitelesítési URL-címekre.](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal)

    Ha a hitelesítési URL nem szerepel a tűzfalszabályokban, a felhasználók egy kritikus riasztást fognak látni, amely szerint a StorSimple-eszközük nem tudta hitelesíteni magát a szolgáltatással. Ha a felhasználók látják ezt a riasztást, meg kell adniuk az új hitelesítési URL-címet. További információ: [StorSimple hálózati riasztások](storsimple-8000-manage-alerts.md#networking-alerts).

* **A StorSimple Snapshot Manager új verziója** – A StorSimple Snapshot Manager új verziója az 5- es frissítéssel érhető el, és kompatibilis a 4. Javasoljuk, hogy frissítsen erre a verzióra. A StorSimple Snapshot Manager korábbi verziója a 3. [Töltse le a StorSimple Snapshot Manager megfelelő verzióját,](https://www.microsoft.com/en-us/download/details.aspx?id=44220) és olvassa el a [StorSimple Snapshot Manager telepítését.](storsimple-snapshot-manager-deployment.md)


## <a name="issues-fixed-in-update-5"></a>Az 5.

Az alábbi táblázat az 5.

| Nem | Szolgáltatás | Probléma | Fizikai eszközre vonatkozik | Virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Windows PowerShell-távszolgáltatás |Az előző kiadásban a felhasználó hibaüzenetet kap, miközben távoli kapcsolatot próbál létrehozni a StorSimple felhőalapú berendezéssel a Windows PowerShellen keresztül. A probléma ebben a kiadásban gyökér okozta és kijavított. |Nem |Igen |
| 2 |Sávszélesség-sablonok |A korábbi kiadásban probléma volt a sávszélesség-sablonokkal, amelyek alacsonyabb sávszélességet eredményeztek, mint amire az eszközt konfigurálták. Ez a probléma ebben a kiadásban megoldódott. |Igen |Igen |
| 3 |Feladatátvétel |Az előző kiadásban, amikor egy nagy számú kötetet tartalmazó eszközt átadott a 4. Ez a probléma ebben a kiadásban kivan javítva. |Igen |Igen |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Ismert problémák az 5.

Nincsenek új ismert problémák az 5. A korábbi kiadások ból az 5. [Update 3 release notes](storsimple-update3-release-notes.md#known-issues-in-update-3)

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Sorosscsi-vezérlő (SAS) vezérlő és belső vezérlőprogram-frissítések az 5.

Ez a kiadás SAS-vezérlővel, lsi illesztőprogram- és belsővezérlő-frissítésekkel rendelkezik. A frissítések telepítéséről az [5.](storsimple-8000-install-update-5.md)

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>A StorSimple Cloud Appliance frissítései az 5.

Ez a frissítés nem alkalmazható a StorSimple cloud appliance (más néven a virtuális eszköz). Új felhőalapú készülékeket kell létrehozni az 5-ös frissítés i. A StorSimple felhőalapú berendezés létrehozásáról a [StorSimple felhőalapú berendezés telepítése és kezelése](storsimple-8000-cloud-appliance-u2.md)című oldalon talál.

## <a name="next-step"></a>Következő lépés

További információ az [5.](storsimple-8000-install-update-5.md)

