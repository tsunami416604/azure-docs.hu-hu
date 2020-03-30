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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67325795"
---
1. Jelentkezzen be az [Azure Portalra.]

2. Kattintson az **Erőforrás létrehozása** gombra.

3. A keresőmezőbe írja be a **Web App kifejezést**.
    
4. Az eredménylistában válassza a **Web App** lehetőséget a Piactérről.

5. Válassza ki **az előfizetést** és **az erőforráscsoportot** (válasszon ki egy meglévő erőforráscsoportot, _vagy_ hozzon létre egy újat (az alkalmazás nevével megegyező néven)).

6. Válassza ki a webalkalmazás egyedi **nevét.**

7. Válassza az alapértelmezett **Közzététel** beállítást **kódként.**

8. A **Futásidejű veremben**ki kell választania egy verziót **ASP.NET** vagy **a Csomópont**csoportban. Ha .NET háttérverziót hoz fel, válasszon egy verziót ASP.NET. Ellenkező esetben, ha egy csomópontalapú alkalmazást céloz meg, válasszon egyet a csomópontból.

9. Válassza ki a megfelelő **operációs rendszert**, linuxot vagy Windowst. 

10. Válassza ki azt a **régiót,** ahol telepíteni szeretné ezt az alkalmazást. 

11. Válassza ki a megfelelő **App Service-csomagot,** és nyomja meg **a Véleményezés és létrehozás parancsot.** 

12. Az **Erőforráscsoport**csoportban jelöljön ki egy meglévő erőforráscsoportot, _vagy_ hozzon létre egy újat (az alkalmazás nevével megegyező néven).

13. Kattintson **a Létrehozás gombra.** Várjon pár percet, amíg a szolgáltatás telepítése sikeresen befejeződik, mielőtt továbblépne. Az állapotfrissítéseket az Értesítések (harang) ikonja jelzi a portál fejlécén.

14. A telepítés befejezése után kattintson a **Központi telepítés részletei** szakaszra, majd a Microsoft típusú erőforrásra.Web/webhelyek . **Microsoft.Web/sites** Az imént létrehozott App Service Web App alkalmazáshoz navigál. 

15. Kattintson a **Konfiguráció** panelre a **Beállítások** csoportban, majd az **Alkalmazás beállításai**ban kattintson az Új **alkalmazás beállításgombjára.**

16. Az **Alkalmazás hozzáadása/szerkesztése lapon** írja be **a Név** **MobileAppsManagement_EXTENSION_VERSION** és az Érték értéket **a legújabbként,** és nyomja le az OK gombot.

Minden készen áll arra, hogy ezt az újonnan létrehozott App Service Web alkalmazást mobilalkalmazásként használja.

<!-- URLs. -->
[Azure-portál]: https://portal.azure.com/