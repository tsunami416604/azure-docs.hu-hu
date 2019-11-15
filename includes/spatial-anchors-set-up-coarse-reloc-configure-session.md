---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 7ac98f608cb1ca5bab590714623bc78036d46b81
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093138"
---
## <a name="configure-the-cloud-anchor-session"></a>A Felhőbeli horgony munkamenetének konfigurálása

A következő lépésekkel gondoskodunk a Felhőbeli horgony-munkamenet konfigurálásáról. Az első sorban beállítjuk az érzékelő szolgáltatóját a munkamenetben. Mostantól a munkamenet során létrehozott összes horgony érzékelő-olvasási művelettel lesz társítva. Ezután létrehozunk egy közeli eszközre vonatkozó keresési feltételeket, és inicializáljuk az alkalmazás követelményeinek megfelelően. Végezetül arra utasítja a munkamenetet, hogy a horgonyok megkeresése során az érzékelő adatait használja a közeli eszközre vonatkozó feltételekből származó figyelők létrehozásával.