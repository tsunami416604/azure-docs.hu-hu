---
title: A StorSimple 8000 Series Update 5 kibocsátási megjegyzései |} A Microsoft Docs
description: Az új funkciók, problémák és megoldások ismerteti a StorSimple 8000 Series Update 5.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 462cbd6261723aa91bbfd23292611e758a800ed2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60844091"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>A StorSimple 8000 Series Update 5 kiadási megjegyzései

## <a name="overview"></a>Áttekintés

A következő kiadási megjegyzések az új funkciók ismertetik, és a StorSimple 8000 Series Update 5 a kritikus fontosságú megoldatlan problémák azonosításához. Ebben a kiadásban a StorSimple szoftver frissítések listáját is tartalmaznak.

5\. frissítése bármilyen futó 0.1-es frissítés – Update 4 StorSimple-eszközre alkalmazhatók. Az eszköz Update 5 társított verziószáma 6.3.9600.17845.

Tekintse át a kibocsátási megjegyzések a StorSimple-megoldásban a frissítés telepítése előtt található információkat.

> [!IMPORTANT]
> * 5\. frissítése egy kötelező frissítés, és közvetlenül kell telepíteni. További információkért lásd: hogyan [Update 5 alkalmazása](storsimple-8000-install-update-5.md).
> * 5\. frissítése az eszközhöz, lemezfirmware, az operációs rendszer biztonsági és más operációs rendszer frissítése rendelkezik. Ez a frissítés körülbelül 4 óra vesz igénybe. Lemezfirmware-frissítés zavart okozó frissítést, és az eszköz egy állásidőt eredményez. Azt javasoljuk, hogy Update 5 és az eszköz naprakész állapotban tarthatja alkalmazza.
> * Az új kiadásokhoz, akkor előfordulhat, hogy nem jelenik meg frissítések azonnal, mivel egy fázisokra bontva történő bevezetéséhez a frissítések végzünk. Néhány napot várni, és újra ezeket a frissítéseket, majd frissítéskeresés hamarosan elérhetővé válik.

## <a name="whats-new-in-update-5"></a>5\. frissítés újdonságai

A következő kulcsfontosságú fejlesztések és hibajavítások a frissítés 5-ben került sor.

* **Használja az Azure Active Directory (AAD) hitelesíteni a StorSimple-Eszközkezelő szolgáltatással** – az 5. frissítésétől kezdve, az Azure Active Directory segítségével hitelesíteni a StorSimple-Eszközkezelő szolgáltatással. A régi hitelesítési mechanizmust 2017 December elavulttá válik. Minden felhasználó be a tűzfalszabályok tartalmaznia kell az új hitelesítési URL-címeket. További információért ugorjon [hitelesítési URL-címek szerepelnek a StorSimple eszköz hálózatkezelési követelményei](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    A hitelesítési URL-cím nem szerepel a tűzfalszabályokat, ha a felhasználók, amely a StorSimple-eszköz nem sikerült hitelesíteni a szolgáltatás kritikus riasztás jelenik meg. A felhasználó ezt a riasztást lát, szükség esetén az új hitelesítési URL-címét. További információért ugorjon [riasztások hálózatkezelés StorSimple](storsimple-8000-manage-alerts.md#networking-alerts).

* **Új verzió a StorSimple Snapshot Manager** – új verzióját a StorSimple Snapshot Manager alkalmazás Update 5 akkor szabadul fel, és kompatibilis 4-es frissítést futtatja a StorSimple-eszközökkel vagy újabb. Azt javasoljuk, hogy erre a verzióra frissíteni. A StorSimple Snapshot Manager korábbi verziója-e a StorSimple Update 3 rendszerű eszközök vagy korábbi. [Töltse le a megfelelő verzióját a StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) , és hivatkozzon [üzembe helyezése a StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>A frissítés 5-ös megoldott problémák

Az alábbi táblázat a problémákat, amelyek javítva lett a frissítés 5-ös összegzését tartalmazza.

| Nem | Funkció | Probléma | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Windows PowerShell távoli eljáráshívás |A korábbi kiadásában egy jelenít meg a felhasználót hiba történt a StorSimple felhőalapú készülék Windows PowerShell távoli kapcsolatot létesíteni tett kísérlet során. Ez a probléma alapvető oka volt, és javítva a kiadásban. |Nem |Igen |
| 2 |Sávszélességsablonok |A korábbi kiadásokban hiba történt a sávszélesség-sablonokkal, melyek kiváltották, mire az eszköz használatára konfigurált, mint az alacsonyabb sávszélességet. Ebben a kiadásban a probléma megoldásához. |Igen |Igen |
| 3 |Feladatátvétel |Az előző verzió, a feladatátvételkor kötetek nagy számú eszköz lett egy másik eszközre Update 4-es, futtatja a folyamat sikertelen lesz a alkalmazni a hozzáférés-vezérlési rekordok tett kísérlet során. Ebben a kiadásban a probléma megoldódik. |Igen |Igen |



## <a name="known-issues-in-update-5-from-previous-releases"></a>A korábbi kiadások Update 5 ismert problémái

Nincsenek új ismert problémák a frissítés 5-ben. Vihetők át Update 5 korábbi kiadásokban a problémák listáját, Ugrás [Update 3 kibocsátási megjegyzések](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Soros csatlakozású SCSI (SAS) vezérlő és belsővezérlőprogram-frissítések a frissítés 5-ben

Ebben a kiadásban a SAS-vezérlő és LSI illesztőprogram és belső vezérlőprogramja frissítéseinek rendelkezik. Ezek a frissítések telepítésével kapcsolatos további információkért lásd: [Update 5 telepítése](storsimple-8000-install-update-5.md) a StorSimple eszközön.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>A StorSimple Cloud Appliance frissítések a frissítés 5-ben

A StorSimple Cloud Appliance eszköz (más néven a virtuális eszköz) nem lehet alkalmazni ezt a frissítést. Új felhőalapú berendezések kell létrehozni a frissítés 5-rendszerkép használatával. StorSimple Cloud Appliance létrehozásával kapcsolatos információért, [üzembe helyezése és kezelése a StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Következő lépés

Ismerje meg, hogyan [Update 5 telepítése](storsimple-8000-install-update-5.md) a StorSimple eszközön.

