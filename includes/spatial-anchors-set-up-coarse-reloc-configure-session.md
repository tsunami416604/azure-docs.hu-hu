---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 41c0a6292bf7369a30fbb1e4bd474e70ffaedcb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999744"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>A felhőalapú térbeli horgony munkamenetének konfigurálása

A következő lépésekkel gondoskodunk a felhőalapú térbeli horgony munkamenetének konfigurálásáról. Az első sorban beállítjuk az érzékelő szolgáltatóját a munkamenetben. Mostantól a munkamenet során létrehozott összes horgony érzékelő-olvasási művelettel lesz társítva. Ezután létrehozunk egy közeli eszközre vonatkozó keresési feltételeket, és inicializáljuk az alkalmazás követelményeinek megfelelően. Végezetül arra utasítja a munkamenetet, hogy a horgonyok megkeresése során az érzékelő adatait használja a közeli eszközre vonatkozó feltételekből származó figyelők létrehozásával.