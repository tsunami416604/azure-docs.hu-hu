---
title: Valós felhasználói mérések weboldalakkal - Azure Traffic Manager
description: Ebből a cikkből megtudhatja, hogyan állíthatja be a weblapokat, hogy valós felhasználói méréseket küldjön az Azure Traffic Managernek.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 927d774ee30a291607a8a47fc2fd6878c1bc6fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938694"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Valódi felhasználói mérések küldése az Azure Traffic Manager nek weboldalak használatával

Beállíthatja, hogy a weboldalak valós felhasználói méréseket küldjenek a Traffic Managernek egy valós felhasználói mérések (RUM) kulcs beszerzésével és a generált kód beágyazásával a weboldalba.

## <a name="obtain-a-real-user-measurements-key"></a>Valós felhasználói mérések kulcs beszerzése

Az ügyfélalkalmazásból a Traffic Managernek küldött méréseket a szolgáltatás egy egyedi karakterlánc, a **Valós felhasználói mérések (RUM) kulcs**segítségével azonosítja. Beszerezhet egy RUM-kulcsot az Azure Portalon, egy REST API-t, vagy a PowerShell vagy az Azure CLI használatával.

A RUM-kulcs beszerzése az Azure Portal használatával:
1. Egy böngészőben jelentkezzen be az Azure Portalra. Ha még nincs fiókja, regisztrálhat egy egy hónapos ingyenes próbaverzióra.
2. A portál keresősávjában keressen rá a módosítani kívánt Traffic Manager-profil nevére, majd kattintson a Traffic Manager-profilra a megjelenített eredmények között.
3. A Traffic Manager profilpanelen kattintson a **Valós felhasználói mérések** elemre a Beállítások **csoportban.**
4. Új RUM-kulcs létrehozásához kattintson a **Kulcs létrehozása** gombra.
 
   ![Valós felhasználói mérések kulcs létrehozása](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **1. ábra: Valós felhasználói mérések kulcsgenerálása**

5. A panel most megjeleníti a létrehozott RUM-kulcsot és egy JavaScript-kódrészletet, amelyet be kell ágyazni a HTML-lapba.
 
    ![Javascript kód a valós felhasználói mérések kulcshoz](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **2. ábra: Valós felhasználói mérések kulcs és mérés JavaScript**
 
6. A **JavaScript-kód** másolásához kattintson a Másolás gombra. 

>[!IMPORTANT]
> Használja a generált JavaScript valós felhasználói mérések funkció megfelelő működéséhez. A parancsfájl vagy a valós felhasználói mérések által használt parancsfájlok módosításai kiszámíthatatlan viselkedéshez vezethetnek.

## <a name="embed-the-code-to-an-html-web-page"></a>A kód beágyazása HTML-weblapra

Miután beszerezte a RUM kulcsot, a következő lépés a másolt JavaScript beágyazása egy HTML-lapba, amelyet a végfelhasználók meglátogatnak. A HTML szerkesztése sokféleképpen és különböző eszközökkel és munkafolyamatokkal végezhető el. Ez a példa bemutatja, hogyan frissíthető egy HTML-lap a parancsfájl hozzáadásához. Ezzel az útmutatóval a HTML-forráskezelési munkafolyamathoz igazíthatja.

1.  A HTML-lap megnyitása szövegszerkesztőben
2.  Illessze be a korábbi lépésben másolt JavaScript-kódot a HTML BODY részébe (a másolt kód a 8& 9-es sorban van, lásd a 3. ábrát).
 
    ![Javascript kód beágyazása a weboldalba a valós felhasználói mérésekhez](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **3. ábra: Egyszerű HTML beágyazott valós felhasználói mérések JavaScript**

3.  Mentse a HTML-fájlt, és az internethez csatlakoztatott webkiszolgálón őrizze meg. 
4. Legközelebb, amikor ezt az oldalt egy webböngészőben teszi meg, a hivatkozott JavaScript letöltődik, és a parancsfájl végrehajtja a mérési és jelentési műveleteket.


## <a name="next-steps"></a>További lépések
- További információ a [valós felhasználói mérésekről](traffic-manager-rum-overview.md)
- A [Traffic Manager működésének megismerése](traffic-manager-overview.md)
- További információ a Traffic Manager által támogatott [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)
- További információ a [Traffic Manager-profil létrehozásáról](traffic-manager-create-profile.md)

