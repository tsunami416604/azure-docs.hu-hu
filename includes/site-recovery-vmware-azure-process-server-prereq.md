---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 96cba4e077be8b7658c270b09b177a845e16c8b0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66169986"
---
A cikk a következőket feltételezi:

1. Már létrejött egy **helyek közötti VPN** vagy egy **Express Route-kapcsolat** a helyszíni hálózat és az Azure Virtual Network között.
2. A felhasználói fióknak jogosult új virtuális gép létrehozására abban az Azure-előfizetésben, amelybe a virtuális gépek feladatátadása megtörtént.
3. Az előfizetés számára legalább 4 mag érhető el egy új folyamatkiszolgáló virtuális gép indításához.
4. Ön rendelkezik a **konfigurációs kiszolgáló hozzáférési kódjával**.

> [!TIP]
> Ellenőrizze, hogy tud-e csatlakozni a (helyszínen futó) konfigurációs kiszolgáló 443-as portjához abból az Azure Virtual Networkből, amelybe a virtuális gépek feladatátadása megtörtént.
