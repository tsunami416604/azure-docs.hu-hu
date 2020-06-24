---
title: Valós felhasználómérés weblapok között – Azure Traffic Manager
description: Ebből a cikkből megtudhatja, hogyan állíthatja be a weblapjait valós felhasználómérés küldéséhez az Azure Traffic Managerba.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: f297057549263cd62783778b363665bfb4368c91
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84711646"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>valós felhasználómérés küldése az Azure-Traffic Manager weblapok használatával

A weblapokat úgy is beállíthatja, hogy valós felhasználómérés küldjön Traffic Manager egy valós felhasználómérés (RUM) kulcs beszerzésével és a generált kód weblapra való beágyazásával.

## <a name="obtain-a-real-user-measurements-key"></a>valós felhasználómérés kulcs beszerzése

Az ügyfélalkalmazás által Traffic Manager elküldött méréseket a szolgáltatás a **valós felhasználómérés (rum) kulcsnak**nevezett egyedi karakterlánc használatával azonosítja. Egy RUM-kulcsot a Azure Portal, egy REST API vagy a PowerShell vagy az Azure CLI használatával szerezhet be.

A RUM kulcs beszerzése Azure Portal használatával:
1. Egy böngészőben jelentkezzen be az Azure Portalra. Ha még nincs fiókja, regisztrálhat egy egy hónapos ingyenes próbaverzióra.
2. A portál keresősávjában keressen rá a módosítani kívánt Traffic Manager-profil nevére, majd kattintson a Traffic Manager-profilra a megjelenített eredmények között.
3. A Traffic Manager profil panelen kattintson a **valós felhasználómérés** elemre a **Beállítások**területen.
4. Kattintson a **kulcs létrehozása** lehetőségre egy új rum-kulcs létrehozásához.
 
   ![valós felhasználómérés kulcs előállítása](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **1. ábra: valós felhasználómérés kulcs létrehozása**

5. A panel most megjeleníti a generált RUM-kulcsot és egy JavaScript-kódrészletet, amelyet be kell ágyazni a HTML-lapra.
 
    ![valós felhasználómérés kulcs JavaScript-kódja](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **2. ábra: valós felhasználómérés kulcs-és mérési JavaScript**
 
6. A JavaScript-kód másolásához kattintson a **Másolás** gombra. 

>[!IMPORTANT]
> A valós felhasználómérés funkció megfelelő működéséhez használja a generált JavaScriptet. A parancsfájl vagy az valós felhasználómérés által használt parancsfájlok módosításai kiszámíthatatlan működéshez vezethetnek.

## <a name="embed-the-code-to-an-html-web-page"></a>A kód beágyazása egy HTML-weblapra

A RUM-kulcs beszerzése után a következő lépés a másolt JavaScript beágyazása egy olyan HTML-oldalra, amelyet a végfelhasználók meglátogatnak. A HTML-szerkesztés többféleképpen is elvégezhető, és különböző eszközöket és munkafolyamatokat használhat. Ebből a példából megtudhatja, hogyan frissítheti a HTML-lapokat a szkript hozzáadásához. Ezt az útmutatót használhatja a HTML-forrás felügyeleti munkafolyamathoz való igazításhoz.

1.  HTML-oldal megnyitása egy szövegszerkesztőben
2.  Illessze be azt a JavaScript-kódot, amelyet az előző lépésben másolt a HTML törzs szakaszába (a másolt kód a 8 & 9. sorban található, lásd a 3. ábrát).
 
    ![JavaScript-kód beágyazása a weblapra valós felhasználómérés](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **3. ábra: egyszerű HTML beágyazott valós felhasználómérés JavaScripttel**

3.  Mentse a HTML-fájlt, és az internethez csatlakozó webkiszolgálón tárolja. 
4. Amikor a lap legközelebb megjelenik egy webböngészőben, a rendszer letölti a JavaScriptet, és a parancsfájl végrehajtja a mérési és jelentéskészítési műveleteket.


## <a name="next-steps"></a>További lépések
- További információ a [valós felhasználómérés](traffic-manager-rum-overview.md)
- Tudnivalók a [Traffic Manager működéséről](traffic-manager-overview.md)
- További információ a Traffic Manager által támogatott [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)
- Megtudhatja, hogyan [hozhat létre Traffic Manager-profilt](traffic-manager-create-profile.md)

