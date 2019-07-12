---
title: fájl belefoglalása
description: fájl belefoglalása
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 05/24/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 8aa6cb3f10b86a6821cd93190ecc2135508739cb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593981"
---
## <a name="create-a-new-cdn-profile"></a>Új CDN-profil létrehozása

A CDN-profil a CDN-végpontok egy olyan tárolója, amely meghatároz egy tarifacsomagot.

1. Válassza az Azure Portal bal felső sarkában az **Erőforrás létrehozás** lehetőséget. 
    
    Ekkor megnyílik az **Új** panel.
   
2. Válassza a **Web + mobil**, majd a **CDN** lehetőséget.
   
    ![CDN erőforrás kiválasztása](./media/cdn-create-profile/cdn-new-resource.png)

    Ekkor megnyílik a **CDN-profil** panel.

3. A CDN-profil beállításaihoz a következő táblában megadott értékeket használja:
   
    | Beállítás  | Value |
    | -------- | ----- |
    | **Name** | Írja be a *my-cdn-profile-123* értéket a profil neveként. Ennek a névnek globálisan egyedinek kell lennie. Ha már használatban van, megadhat egy másik nevet. |
    | **Előfizetés** | Válasszon egy Azure-előfizetést a legördülő listából. |
    | **Erőforráscsoport** | Válassza az **Új létrehozása** lehetőséget, és írja be a *my-resource-group-123* értéket az erőforráscsoport neveként. Ha már használatban van, megadhat egy másik nevet, vagy válassza ki a **Meglévő használata** lehetőséget, majd a **my-resource-group-123** elemet a legördülő listából. | 
    | **Erőforráscsoport helye** | Válassza az **USA középső régiója** értéket a legördülő listából. |
    | **Tarifacsomag** | Válassza a **Standard – Verizon** értéket a legördülő listából. |
    | **Új CDN-végpont létrehozása** | Hagyja kiválasztatlanul. |  
   
    ![Új CDN-profil](./media/cdn-create-profile/cdn-new-profile.png)

4. Válassza a **Rögzítés az irányítópulton** lehetőséget, ha a profilt a létrehozása után az irányítópultra szeretné rögzíteni.
    
5. Válassza a **Létrehozás** gombot a profil létrehozásához. 

    A profilok létrehozása általában két óra alatt megtörténik (csak a **Microsoft Azure CDN Standard**-profilok esetén). 

