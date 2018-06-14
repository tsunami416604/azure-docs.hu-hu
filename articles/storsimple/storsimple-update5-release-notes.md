---
title: A StorSimple 8000 Series Update 5 kibocsátási megjegyzései |} Microsoft Docs
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
ms.openlocfilehash: 672757e82bcf645b705f46a9975e09c9dc5eef92
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
ms.locfileid: "24518919"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>A StorSimple 8000 Series Update 5 kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés

Az alábbi kibocsátási megjegyzések új szolgáltatásait ismerteti, és a StorSimple 8000 Series Update 5 a kritikus megnyitott problémák azonosításához. Ebben a kiadásban szereplő StorSimple szoftverfrissítések listáját is tartalmaznak.

5. frissítésétől bármely 0,1 Update futtatása – Update 4 StorSimple eszközre alkalmazhatók. Az eszköz 5. frissítésétől társított verziója 6.3.9600.17845.

Tekintse át a kibocsátási megjegyzések a StorSimple megoldásban a frissítés telepítése előtt található információkat.

> [!IMPORTANT]
> * 5. frissítésétől egy kötelező frissítés, és azonnal telepítenie kell. További információkért lásd: hogyan [alkalmazása frissítés 5](storsimple-8000-install-update-5.md).
> * 5. frissítésétől eszközhöz, lemez belső vezérlőprogramok, operációs rendszer biztonsági és más operációs rendszer frissítése érdekében van. Ez a frissítés körülbelül 4 órát vesz igénybe. Vezérlőprogram-frissítés zavaró frissítésről, és az eszköz egy leállásának eredményez. Azt javasoljuk, hogy a frissítés 5 és az eszköz naprakész állapotban tarthatja alkalmazásra.
> * Új kiadásokban nem láthatók frissítések azonnal, mert a frissítések fázisokra bontva történő bevezetéséhez végezzük. Néhány napot várni, majd újra ezeket a frissítéseket a frissítések keresése hamarosan elérhető lesz.

## <a name="whats-new-in-update-5"></a>5. frissítésétől újdonságai

A következő fontos fejlesztést tartalmaz, és hibajavítások frissítés 5 végzett.

* **Használja az Azure Active Directory (AAD) a StorSimple eszköz Manager szolgáltatásban való hitelesítéshez szükséges** – az 5. frissítésétől kezdve, az Azure Active Directory segítségével hitelesíti a StorSimple eszköz Manager szolgáltatásban. A régi hitelesítési mechanizmus December 2017 elavulttá válik. A felhasználók tartalmaznia kell az új hitelesítési URL-címek be a tűzfalszabályok között. További információkért látogasson el [hitelesítési a StorSimple eszköz hálózatkezelési követelményei felsorolt URL-címek](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    A hitelesítés URL-címe nem szerepel a tűzfalszabályok, a felhasználók látják a kritikus riasztás, amely a StorSimple-eszköz nem tudta hitelesíteni a szolgáltatással. Ha a felhasználók ezt a figyelmeztetést, az új hitelesítési URL-cím szükséges. További információkért látogasson el [riasztások hálózati StorSimple](storsimple-8000-manage-alerts.md#networking-alerts).

* **Új verziót a StorSimple Snapshot Manager** -új verziójára a StorSimple Snapshot Manager alkalmazás Update 5 megjelenik, és kompatibilis az összes a StorSimple futtató eszközök Update 4 vagy újabb. Azt javasoljuk, hogy erre a verzióra frissíteni. A StorSimple Snapshot Manager korábbi verziója a frissítés 3 futtató StorSimple eszközökhöz használt vagy korábbi. [Töltse le a megfelelő verzióját a StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) , majd tekintse át [központi telepítése a StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>A frissítés 5 megoldott problémák

A következő táblázat összefoglalja a problémákat, amelyek a frissítés 5 javítva lett.

| Nem | Szolgáltatás | Probléma | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Windows PowerShell-távelérés |A korábbi változatban egy jelenít meg a felhasználót hiba történt egy távoli kapcsolatot létesíteni a Windows PowerShell segítségével a StorSimple felhő készülék tett kísérlet során. Ez a probléma volt legfelső szintű okozott, és ebben a kiadásban rögzített. |Nem |Igen |
| 2 |Sávszélesség-sablonok |Korábbi verzióban következne be, amelyek alacsony sávszélességű, mint mi az eszköz konfigurálása a sávszélesség-sablonok. Ebben a kiadásban a probléma megoldásához. |Igen |Igen |
| 3 |Feladatátvétel |Előző kiadásban Ha egy kötet nagy mennyiségű eszköz keresztül, egy másik futtató eszközre, az Update 4 sikertelen a folyamat sikertelen lesz a hozzáférés-vezérlési rekordokat alkalmazásának megkísérlésekor. Ez a probléma fennáll ebben a kiadásban. |Igen |Igen |



## <a name="known-issues-in-update-5-from-previous-releases"></a>A korábbi kiadásokból származó 5. frissítésétől kapcsolatos ismert problémák

Nincsenek új ismert problémák a frissítési 5. A korábbi verzióiról való frissítés 5 átvitt kapcsolatos problémák listáját itt [Update 3 kibocsátási megjegyzések](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Soros csatlakozású SCSI (SAS) vezérlő és a belső vezérlőprogram frissítések frissítés 5

Ebben a kiadásban a vezérlő és LSI illesztőprogram és a belső vezérlőprogram frissítések rendelkezik. Ezek a frissítések telepítéséről további információkért lásd: [frissítés 5 telepítése](storsimple-8000-install-update-5.md) a StorSimple eszköz.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>A frissítés 5 StorSimple felhő készülék frissítések

A frissítés nem alkalmazható a StorSimple felhő készülék (más néven a virtuális eszköz). Új felhőalapú készülékek kell létrehozni a frissítés 5-lemezkép használata. A StorSimple felhő készülék létrehozásával kapcsolatos információért, [központi telepítése és kezelése a StorSimple felhő készülék](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Következő lépés

Megtudhatja, hogyan [frissítés 5 telepítése](storsimple-8000-install-update-5.md) a StorSimple eszköz.

