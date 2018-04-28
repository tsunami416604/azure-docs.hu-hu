---
title: fájl belefoglalása
description: fájl belefoglalása
services: cdn
author: SyntaxC4
ms.service: cdn
ms.topic: include
ms.date: 04/13/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 0db5f571324694f0518ffc4e92af985e5125d755
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
## <a name="create-a-new-cdn-profile"></a>Új CDN-profil létrehozása

A CDN-profil a CDN-végpontok egy olyan tárolója, amely meghatároz egy tarifacsomagot.

1. Válassza az Azure Portal bal felső sarkában az **Erőforrás létrehozás** lehetőséget.
    
    Ekkor megnyílik az **Új** panel.
   
2. Válassza a **Web + mobil**, majd a **CDN** lehetőséget.
   
    ![CDN erőforrás kiválasztása](./media/cdn-create-profile/cdn-new-resource.png)

    Ekkor megnyílik a **CDN-profil** panel.

    Ezután használja a képet követő táblázatban megadott beállításokat.
   
    ![Új CDN-profil](./media/cdn-create-profile/cdn-new-profile.png)

    | Beállítás  | Érték |
    | -------- | ----- |
    | **Name (Név)** | Írja be a *my-cdn-profile-123* értéket a profil neveként. Ennek a névnek globálisan egyedinek kell lennie. Ha már használatban van, megadhat egy másikat. |
    | **Előfizetés** | Válasszon egy Azure-előfizetést a legördülő listából.|
    | **Erőforráscsoport** | Válassza az **Új létrehozása** lehetőséget, és írja be a *my-resource-group-123* értéket az erőforráscsoport neveként. Ennek a névnek globálisan egyedinek kell lennie. Ha már használatban van, megadhat egy másikat. | 
    | **Erőforráscsoport helye** | Válassza az **USA középső régiója** értéket a legördülő listából. |
    | **Tarifacsomag** | Válassza a **Standard – Verizon** értéket a legördülő listából. |
    | **Új CDN-végpont létrehozása** | Hagyja kiválasztatlanul. |  
   
3. Válassza a **Rögzítés az irányítópulton** lehetőséget, ha a profilt a létrehozása után az irányítópultra szeretné rögzíteni.
    
4. Válassza a **Létrehozás** gombot a profil létrehozásához. 

