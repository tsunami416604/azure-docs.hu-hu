---
title: "App Service egy Azure verem frissítési |} Microsoft Docs"
description: "A frissítés a veremben Azure App Service megismerése az ismert problémákról, valamint a frissítés letöltése helyét."
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 0c33c8fdefbb27ba8414e58bed1b42ee7aaba88a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="app-service-on-azure-stack-update-one-release-notes"></a>App Service Azure veremben frissítése egy kibocsátási megjegyzései

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A kibocsátási megjegyzések a fejlesztései és a javításokat az Azure App Service Azure verem Update 1 és olyan ismert problémákat ismertetik. Ismert problémák közvetlenül a telepítés, a frissítési folyamat és a problémákat a build (telepítés utáni) kapcsolatos problémák vannak felosztva.

> [!IMPORTANT]
> A 1802 frissítés alkalmazásához a integrált Azure verem rendszerre, vagy telepítheti a legújabb Azure verem szoftverfejlesztői készlet Azure App Service üzembe helyezése előtt.
>
>

## <a name="build-reference"></a>Hivatkozás létrehozása

Az App Service az Azure verem Update 1 buildszáma **69.0.13698.9**

### <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Az Azure App Service Azure veremben megköveteli a [három-tulajdonos helyettesítő tanúsítvány](azure-stack-app-service-before-you-get-started.md#get-certificates) , amelyben az Azure App Service-ben mostantól kezelése a Kudu SSO fejlesztései miatt.  Az új tulajdonos ** *. sso.appservice.<region>. <domainname>.<extension>**
>
>

Tekintse meg a [mielőtt hozzáfogna dokumentáció](azure-stack-app-service-before-you-get-started.md) központi telepítésének megkezdése előtt.

### <a name="new-features-and-fixes"></a>Új szolgáltatásokat és javításokat

Az Azure App Service Azure verem Update 1 tartalmazza a következő fejlesztéseket és javításokat:

- **Magas rendelkezésre állás az Azure App Service** – az Azure-verem 1802 update szolgáltatást munkaterhelések történő központi fault tartományok.  Ezért App Service-infrastruktúrát, képes lehet a hibatűrő, mivel a tartalék tartományok lesznek telepítve.  Alapértelmezés szerint minden új központi telepítéséhez, az Azure App Service kell ezt a képességet azonban a központi telepítés előtt Azure verem 1802 befejeződött a frissítés alkalmazva tekintse meg a [App Service tartalék tartomány dokumentáció](azure-stack-app-service-fault-domain-update.md)

- **Meglévő virtuális hálózat üzembe** -ügyfelek most már telepítheti a verem Azure App Service egy meglévő virtuális hálózaton belül.  A meglévő virtuális hálózat telepítése lehetővé teszi az ügyfelek csatlakozni az SQL Server és a fájlkiszolgáló személyes portokon keresztül az Azure App Service-ben szükséges.  Központi telepítése során az ügyfelek központi telepítése egy meglévő virtuális hálózatot, azonban kiválaszthatja [kell létrehozni, használja az App Service-alhálózatok](azure-stack-app-service-before-you-get-started.md#virtual-network) telepítését megelőzően.

- Szoftverfrissítések **App Services-bérlő, a rendszergazda, Funkciók portálok és a Kudu eszközök**.  Konzisztens Azure verem portál SDK-verzió.

- **A következő alkalmazás-keretrendszerbeli és eszközök frissítések**:
    - Hozzáadott **.Net 2.0 alapvető** támogatja
    - Hozzáadott **Node.JS** verziók:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Hozzáadott **NPM** verziók:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Hozzáadott **PHP** frissítések:
        - 5.6.32
        - 7.0.26 (x86 és x64)
        - 7.1.12 (x86 és x64)
    - Frissített **Git for Windows** v 2.14.1
    - Frissített **Mercurial** v4.5.0 számára

  - Támogatása az **csak HTTPS** egyéni tartományi szolgáltatásban a bérlői portálon App Service szolgáltatást. 

  - Az egyéni tárolási választó tárolási kapcsolat az Azure Functions hozzáadott érvényesítése 

#### <a name="fixes"></a>Javítások

- Amikor egy offline központi telepítési csomagot hoz létre, az ügyfelek többé nem kapja meg hozzáférés megtagadásáról szóló hibaüzenetet, amikor megnyitja a mappa az App Service-telepítő

- Az alkalmazás bérlői portál az egyéni tartományok szolgáltatás használata során meg a problémát.

- Rendszergazda fenntartott nevek használatával a telepítés során az ügyfelek megakadályozása

- Engedélyezve van az App Service telepítési **tartományhoz csatlakoztatott** fájlkiszolgáló

- Azure verem legfelső szintű továbbfejlesztett beolvasása a parancsfájl-tanúsítványt, és most ellenőrzi a legfelső szintű tanúsítvány az App Service telepítőjét a.

- Az Azure Resource Manager alatt adja vissza, ha egy előfizetés rögzített helytelen állapot Microsoft.Web névtér a benne lévő erőforrások törlése.

### <a name="known-issues-with-the-deployment-process"></a>A telepítési folyamat szolgáltatással kapcsolatos ismert problémák

- Nincsenek ismert problémák a központi telepítés az Azure App Service Azure verem Update 1.

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat szolgáltatással kapcsolatos ismert problémák

- Nincsenek ismert problémák a frissítés az Azure App Service Azure verem frissítése 1.

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

- Nincsenek ismert problémák az Azure App Service Azure verem Update 1. a telepítéshez.

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure App Service Azure veremben működő felhő rendszergazdák kapcsolatos ismert problémák

A dokumentációban találja a [Azure verem 1802 kibocsátási megjegyzései](azure-stack-update-1802.md)

## <a name="see-also"></a>Lásd még

- Az Azure App Service áttekintéséért lásd: [Azure verem – áttekintés az Azure App Service](azure-stack-app-service-overview.md).
- App Service Azure veremben telepítendő előkészítése további információkért lásd: [az App Service Azure veremben megkezdése előtt](azure-stack-app-service-before-you-get-started.md).
