---
title: fájl belefoglalása
description: fájl belefoglalása
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: d73a8d743a948cbd94a3af81fe2e77c45c0eeb67
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996288"
---
## <a name="create-a-new-cdn-profile"></a>Új CDN-profil létrehozása

A CDN-profil a CDN-végpontok egy olyan tárolója, amely meghatároz egy tarifacsomagot.

1. A Azure Portal válassza az **erőforrás létrehozása** (a bal felső sarokban) lehetőséget. Ekkor megnyílik az **Új** panel.
   
1. Keresse meg és válassza a **CDN**elemet, majd kattintson a **Létrehozás**elemre:
   
    ![CDN erőforrás kiválasztása](./media/cdn-create-profile/cdn-new-resource.png)

    Megjelenik a **CDN-profil** panel.

1. Írja be a következő értékeket:
   
    | Beállítás  | Érték |
    | -------- | ----- |
    | **Név** | Írja be a *CDN-Profile-123* nevet a profil neveként. A névnek globálisan egyedinek kell lennie; Ha már használatban van, adjon meg másik nevet. |
    | **Előfizetés** | Válasszon egy Azure-előfizetést a legördülő listából. |
    | **Erőforráscsoport** | Válassza az **új létrehozása** elemet, és írja be a *CDNQuickstart-RG* nevet az erőforráscsoport neveként, vagy válassza a **meglévő használata** lehetőséget, és válassza a *CDNQuickstart – RG* lehetőséget, ha már rendelkezik a csoporttal. | 
    | **Erőforráscsoport helye** | Válasszon egy helyet a legördülő listából. |
    | **Díjszabási csomag** | Válassza ki a **szabványos Akamai** lehetőséget a legördülő listából. (A Akamai réteg üzembe helyezési ideje körülbelül egy percet tartalmaz. A Microsoft-szintek körülbelül 10 percet vesznek igénybe, a Verizon-rétegek körülbelül 90 percet vesznek igénybe.) |
    | **Új CDN-végpont létrehozása** | Hagyja kiválasztatlanul. |  
   
    ![Új CDN-profil](./media/cdn-create-profile/cdn-new-profile.png)

1. Válassza a **Létrehozás** gombot a profil létrehozásához.

