---
title: App Service Azure veremben frissítése 2 kibocsátási megjegyzései |} Microsoft Docs
description: Ismerje meg a frissítés két az App Service Azure veremben, az ismert problémákról, valamint a frissítés letöltése helyét.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 8e1790b7d0b3a210a9142fc8580ff8ed4d64311c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>App Service az Azure-verem update 2 kibocsátási megjegyzései

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A kibocsátási megjegyzések a fejlesztései és a javításokat az Azure App Service Azure verem Update 2 és olyan ismert problémákat ismertetik. Ismert problémák közvetlenül a telepítés, a frissítési folyamat és a problémákat a build (telepítés utáni) kapcsolatos problémák vannak felosztva.

> [!IMPORTANT]
> A 1804 frissítés alkalmazásához a integrált Azure verem rendszerre, vagy telepítheti a legújabb Azure verem szoftverfejlesztői készlet Azure App Service 1.2 telepítése előtt.
>
>

## <a name="build-reference"></a>Hivatkozás létrehozása

Az App Service az Azure verem Update 2 buildszáma **72.0.13698.10**

### <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Az Azure App Service Azure veremben új telepítés most igényel a [három-tulajdonos helyettesítő tanúsítvány](azure-stack-app-service-before-you-get-started.md#get-certificates) , amelyben az Azure App Service-ben mostantól kezelése a Kudu SSO fejlesztései miatt. Az új tulajdonos  **\*. sso.appservice.\< a régióban\>.\< tartománynév\>.\< bővítmény\>**
>
>

Tekintse meg a [mielőtt hozzáfogna dokumentáció](azure-stack-app-service-before-you-get-started.md) központi telepítésének megkezdése előtt.

### <a name="new-features-and-fixes"></a>Új szolgáltatásokat és javításokat

Az Azure App Service Azure verem Update 2 tartalmazza a következő fejlesztéseket és javításokat:

- Szoftverfrissítések **App Services-bérlő, a rendszergazda, Funkciók portálok és a Kudu eszközök**. Konzisztens Azure verem portál SDK-verzió.

- Alapvető szolgáltatásához megbízhatóságának és engedélyezésével kapcsolatos gyakori hibák könnyebb diagnosztizálására üzenetküldési hiba javítása frissítése.

- **A következő alkalmazás-keretrendszerbeli és eszközök frissítések**:
  - A hozzáadott .net-keretrendszer 4.7.1.
  - Hozzáadott **Node.JS** verziók:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Hozzáadott **NPM** verziók:
    - 5.6.0
  - .Net frissítése alapvető konzisztens az Azure App Service nyilvános felhőben lévő összetevők.
  - Frissített Kudu

- A központi telepítés az automatikus felcserélés tárolóhely szolgáltatás engedélyezve van - [automatikus felcserélés konfigurálása](https://docs.microsoft.com/azure/app-service/web-sites-staged-publishing#configure-auto-swap)

- Teszteli az éles funkció engedélyezve van - [tesztelése éles környezetben bemutatása](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Az Azure Functions proxyk engedélyezve van - [Azure Functions proxyk használata](https://docs.microsoft.com/en-us/azure/azure-functions/functions-proxies)

- App Service felügyeleti bővítmény UX támogatja hozzá:
  - Titkos Elforgatás
  - Tanúsítvány-Elforgatás
  - Rendszer hitelesítőadat-Elforgatás
  - Kapcsolati karakterlánc Elforgatás

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

- Munkavállalók nem érhető el a fájlkiszolgáló, ha az App Service egy meglévő virtuális hálózat és a fájlkiszolgáló csak érhető el a privát hálózaton.

Ha úgy dönt, hogy az üzembe helyezés meglévő virtuális hálózat és a fájl kiszolgálóhoz való csatlakozás belső IP-címet, hozzá kell adnia egy kimenő biztonsági szabály engedélyezése az SMB adatforgalmát. a munkavégző és a fájlkiszolgáló között. Ehhez nyissa meg a felügyeleti portál WorkersNsg, és a következő tulajdonságokkal kimenő biztonsági szabály felvétele:
 * Forrás: bármely
 * Forrás-porttartomány: *
 * Cél: IP-címek
 * Cél IP-címtartomány: IP-címtartományra a fájlkiszolgáló
 * Célporttartomány: 445-ös
 * Protokoll: TCP
 * Művelet: engedélyezése
 * Prioritás: 700
 * Name: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure App Service Azure veremben működő felhő rendszergazdák kapcsolatos ismert problémák

A dokumentációban találja a [Azure verem 1804 kibocsátási megjegyzései](azure-stack-update-1804.md)

## <a name="next-steps"></a>További lépések

- Az Azure App Service áttekintéséért lásd: [Azure verem – áttekintés az Azure App Service](azure-stack-app-service-overview.md).
- App Service Azure veremben telepítendő előkészítése további információkért lásd: [az App Service Azure veremben megkezdése előtt](azure-stack-app-service-before-you-get-started.md).
