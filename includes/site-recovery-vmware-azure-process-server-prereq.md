---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008478"
---
A cikk a következőket feltételezi:

1. Már létrejött egy **helyek közötti VPN** vagy egy **Express Route-kapcsolat** a helyszíni hálózat és az Azure Virtual Network között.
2. A felhasználói fióknak jogosult új virtuális gép létrehozására abban az Azure-előfizetésben, amelybe a virtuális gépek feladatátadása megtörtént.
3. Az előfizetés legalább 8 magot tartalmaz egy új Process Server virtuális gép üzembe helyezéséhez.
4. Ön rendelkezik a **konfigurációs kiszolgáló hozzáférési kódjával**.

> [!TIP]
> Ellenőrizze, hogy tud-e csatlakozni a (helyszínen futó) konfigurációs kiszolgáló 443-as portjához abból az Azure Virtual Networkből, amelybe a virtuális gépek feladatátadása megtörtént.
