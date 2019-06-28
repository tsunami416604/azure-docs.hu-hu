---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 06/20/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 72a69359d412a7560472fbb73ec525ab5d4a4fce
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67325795"
---
1. Jelentkezzen be az [Azure Portal].

2. Kattintson az **Erőforrás létrehozása** gombra.

3. A Keresés mezőbe írja be a **webalkalmazás**.
    
4. Az eredmények listájában válassza ki a **webalkalmazás** a Marketplace-ről.

5. Válassza ki a **előfizetés** és **erőforráscsoport** (válasszon ki egy meglévő erőforráscsoportot _vagy_ hozzon létre egy újat (az alkalmazás neve használatával)).

6. Válasszon egy egyedi **neve** a webalkalmazás.

7. Válassza ki az alapértelmezett **közzététel** lehetőséget igény **kód**.

8. Az a **futtatókörnyezeti verem**, ki kell választania egy verzió alatt **ASP.NET** vagy **csomópont**. Ha .NET-háttérrendszert fejleszt, válassza ki egy verzió alatt az ASP.NET. Ellenkező esetben, ha egy csomópont-alapú alkalmazást céloz meg, válassza ki azt a verzió csomópont.

9. Válassza ki a jobb oldalon **operációs rendszer**, Linux vagy Windows. 

10. Válassza ki a **régió** hova szeretné telepíteni az alkalmazást. 

11. Válassza ki a megfelelő **App Service-csomag** kattintok **tekintse át, és hozzon létre**. 

12. Az **Erőforráscsoport** beállításánál válasszon ki egy létező erőforráscsoportot, _vagy_ hozzon létre egy újat (az alkalmazás nevének használatával).

13. Kattintson a **Create** (Létrehozás) gombra. Várjon pár percet, amíg a szolgáltatás telepítése sikeresen befejeződik, mielőtt továbblépne. Az állapotfrissítéseket az Értesítések (harang) ikonja jelzi a portál fejlécén.

14. Az üzembe helyezés befejezése után kattintson a a **üzembe helyezési adatok** szakaszt, és kattintson az erőforrás típusát a **Microsoft.Web/sites**. Ez az App Service Web Appsban létrehozott jelenik meg. 

15. Kattintson a a **konfigurációs** panel **beállítások** és a a **Alkalmazásbeállítások**, kattintson a a **új Alkalmazásbeállítás** gomb.

16. Az a **Alkalmazásbeállítás hozzáadása/szerkesztése** lap, adja meg **neve** , **MobileAppsManagement_EXTENSION_VERSION** és az értéke **legújabb** és Kattintson az OK gombra.

Összes készen állunk az újonnan létrehozott App Service Web app, a mobilalkalmazás használatára.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/