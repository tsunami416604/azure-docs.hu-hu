---
title: "Traffic Manager-profil létrehozása az Azure-ban |} Microsoft Docs"
description: "Ez a cikk ismerteti a Traffic Manager-profil létrehozása"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: kumud
ms.openlocfilehash: e9ff7947e7801a9f352a7a947b09893b8f615d88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Ez a cikk ismerteti, hogyan profil **prioritás** útvonal felhasználóknak, hogy két Azure Web Apps végpontok útválasztási típust lehet létrehozni. Használatával a **prioritás** útválasztás típusa, az összes továbbítódik az első végpontnak míg a második biztonsági másolatok. Ennek eredményeképpen felhasználók továbbíthatók a második végpontnak, ha az első végpontnak akkor kerül sérült állapotba.

Ebben a cikkben korábban létrehozott Azure Web Apps végpontokat társítva az újonnan létrehozott Traffic Manager-profil. Azure Web Apps végpontok létrehozásával kapcsolatos további tudnivalókért keresse fel a [Azure Web Apps dokumentációs oldal](https://docs.microsoft.com/azure/app-service/). Hozzáadhat a tetszőleges végpontot, amely rendelkezik egy DNS-nevet, és elérhető-e a nyilvános interneten keresztül, és, hogy használjuk Azure Web Apps végpontok példaként.

### <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása
1. Egy böngészőben jelentkezzen be az [Azure Portalra](http://portal.azure.com). Ha még nem rendelkezik fiókkal, akkor az egy [ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/free/). 
2. A **Hub** menüben kattintson az **Új** > **Hálózatkezelés** > **Az összes megjelenítése** elemre, kattintson a **Traffic Manager**-profilra a **Traffic Manager-profil létrehozása** panel megnyitásához, majd kattintson a **Létrehozás** gombra.
3. A **Traffic Manager-profil létrehozása** panelen adja meg a következőket:
    1. A **Név** területen adja meg a profil nevét. Ez a név a trafficmanager.net zónán belül egyedinek kell lennie, és a DNS-név eredményez <name>, amely a Traffic Manager-profil eléréséhez használt trafficmanager.net.
    2. Az **Útválasztási módszer** területen válassza a **Prioritás** útválasztási módszert.
    3. Az **Előfizetés** területen válassza ki azt az előfizetést, amely alatt létre szeretné hozni ezt a profilt
    4. Az **Erőforráscsoport** mezőben hozzon létre egy új erőforráscsoportot, amely alá ezt a profilt helyezi.
    5. Az **Erőforráscsoport helye** területen válassza ki az erőforráscsoport helyét. Ez a beállítás az erőforráscsoport helyére vonatkozik, és nincs hatással a globálisan üzembe helyezendő Traffic Manager-profilra.
    6. Kattintson a **Létrehozás** gombra.
    7. Amikor befejeződött a Traffic Manager-profil globális üzembe helyezése, az egyik erőforrásként szerepel majd a megfelelő erőforráscsoportban.

    ![Traffic Manager-profil létrehozása](./media/traffic-manager-create-profile/Create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>A Traffic Manager-végpont-hozzáadáshoz

1. A portál keresősávban, keresse meg a **Traffic Manager-profil** nevét, amelyet hozott létre az előző szakaszt, és kattintson az eredményeket a traffic manager-profilt, amely a megjelenített.
2. A **Traffic Manager-profil** panel **Beállítások** szakaszában kattintson a **Végpontok** elemre.
3. A megjelenő **Végpontok** panelen kattintson a **Hozzáadás** gombra.
4. A **Végpont hozzáadása** panelt töltse ki az alábbiak szerint:
    1. A **Típusnál** kattintson az **Azure-végpont** lehetőségre.
    2. Adjon meg egy **Nevet**, amelyről felismeri majd a végpontot.
    3. A **céloz erőforrástípus**, kattintson a **App Service**.
    4. A **célerőforrás**, kattintson a **alkalmazásszolgáltatás kiválasztása** megjelenítése a Web Apps, az ugyanazon előfizetésben listája. Az a **erőforrás** panel, amelyen megjelenik, válassza ki az App service első hozzáadni kívánt.
    5. A **Prioritásnál** válassza az **1-es** értéket. Ennek eredményeképpen a teljes forgalom erre a végpontra irányul, ha ez nem befolyásolja a rendszer megfelelő működését.
    6. A **Beállítás letiltottként** jelölőnégyzetet ne jelölje ki.
    7. Kattintson az **OK** gombra
5.  A következő Azure Web Apps végpont ismételje meg a 3. és 4. Ennek a **Prioritása** mindenképpen **2-es** legyen.
6.  Miután mindkét végpontot hozzáadta, azok megjelennek a **Traffic Manager-profil** panelen, **Online** figyelési állapottal.

    ![A Traffic Manager-végpont hozzáadása](./media/traffic-manager-create-profile/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>A Traffic Manager-profil használata
1.  A portál keresősávban, keresse meg a **Traffic Manager-profil** az előző szakaszban létrehozott nevét. A megjelenített eredmények kattintson a traffic manager-profil.
2. Az a **Traffic Manager-profil** panelen kattintson a **áttekintése**.
3. A **Traffic Manager-profil** csempe megjeleníti az újonnan létrehozott Traffic Manager-profil DNS-nevét. Ez használhatja olyan ügyfelek (például úgy, hogy keresse meg webböngészővel) az beszerzése irányítja át a megfelelő végpont, határozza meg az Útválasztás típusa. Ebben az esetben összes kérelem első legyenek átirányítva, és ha Traffic Manager azt észleli, hogy nyilvánítva, a forgalom automatikusan átadja a feladatokat a következő végpontot.

## <a name="delete-the-traffic-manager-profile"></a>A Traffic Manager-profil törlése
Már nincs szükség, ha az erőforráscsoport és az Ön által létrehozott Traffic Manager-profil törlése. Ehhez az szükséges, válassza ki az erőforráscsoport a **Traffic Manager-profil** panel megnyitásához, és kattintson **törlése**.

## <a name="next-steps"></a>Következő lépések

- További információ [útválasztási típusok](traffic-manager-routing-methods.md).
- További tudnivalók típusú végpontok esetében [típusú végpontok esetében](traffic-manager-endpoint-types.md).
- További információ [végpontmonitoring kijelző](traffic-manager-monitoring.md).



