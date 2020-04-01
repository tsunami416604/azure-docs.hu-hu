---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76545208"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>A felhőtérbeli horgonymunkamenet konfigurálása

Mi gondoskodunk a felhőtéri horgony munkamenet konfigurálásáról. Az első sorban beállítjuk az érzékelő szolgáltatót a munkamenetre. Mostantól minden olyan horgony, amelyet a munkamenet során hozunk létre, egy sor érzékelő leolvasással lesz társítva. Ezután egy eszközközeli helytkeresési feltételt hozunk, és inicializáljuk, hogy megfeleljen az alkalmazás követelményeinek. Végül utasítjuk a munkamenetet, hogy használja az érzékelő adatokat a horgonyok keresésekor azáltal, hogy létrehoz egy figyelőt a közeli eszköz feltételeiből.