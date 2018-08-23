---
title: Az Azure cloud Services számára állandó virtuális IP-cím megőrzése |} A Microsoft Docs
description: Ismerje meg, hogyan győződjön meg arról, hogy a virtuális IP-címét (VIP) az Azure-felhőszolgáltatás nem változik.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 2f82663f6b53c6d4e32b8d655dcbd67a321d91ed
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42059484"
---
# <a name="retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Az Azure cloud Services számára állandó virtuális IP-cím megőrzése
Amikor frissít egy felhőszolgáltatás, amely az Azure-ban üzemeltetett, szükség lehet annak érdekében, hogy a virtuális IP-cím (VIP) szolgáltatás nem változik. Tartományi szolgáltatások számos tartománynevek regisztrálásához használt a tartománynévrendszer (DNS). DNS működik, csak akkor, ha a virtuális IP-cím ugyanaz marad. Használhatja a **közzététele varázsló** az Azure-eszközöket, győződjön meg arról, hogy a felhőszolgáltatás virtuális IP-cím nem változik mikor frissítést. DNS-tartományok kezelése a cloud services használatával kapcsolatos további információkért lásd: [Azure cloud Services számára egyéni tartománynév beállítása](cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="publish-a-cloud-service-without-changing-its-vip"></a>Egy felhőalapú szolgáltatás közzététele a virtuális IP-cím megváltoztatása nélkül
Felhőszolgáltatás virtuális IP-cím van lefoglalva, először üzembe helyezésekor az Azure-bA az adott környezetben, például az éles környezetbe. A virtuális IP-cím csak akkor, ha explicit módon törölni az üzemelő példányt, vagy a központi telepítés implicit módon törlődött a frissítés üzembe helyezési folyamat módosítja. A virtuális IP-cím megőrzése, nem törölnie kell az üzembe helyezés, és meg kell győződnie arról, hogy a Visual Studio nem automatikusan törli a központi telepítés. 

Megadhatja a központi telepítési beállítások a **közzététele varázsló**, amely támogatja több központi telepítési lehetőség. Friss telepítését vagy egy központi telepítést, amely lehet növekményes vagy egyidejű is megadhat. Mindkét típusú központi telepítését a virtuális IP-cím megőrzése. Ezek a különböző típusú központi definícióit, lásd: [Azure alkalmazás közzététele varázsló](vs-azure-tools-publish-azure-application-wizard.md). Továbbá szabályozhatja e felhőalapú szolgáltatások a korábbi központi telepítés törlődik, ha hiba történik. Ha ez a beállítás nincs megfelelően beállítva, a virtuális IP-CÍMEK váratlanul módosulhat.

## <a name="update-a-cloud-service-without-changing-its-vip"></a>Felhőszolgáltatások frissítése a virtuális IP-cím megváltoztatása nélkül
1. Hozzon létre vagy nyisson meg egy Azure-felhőszolgáltatás-projekt a Visual Studióban. 

2. A **Megoldáskezelőben**, kattintson a jobb gombbal a projektre. A helyi menüben válassza ki a **közzététel**.

    ![Közzététel menü](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

3. Az a **Azure-alkalmazások közzététele** párbeszédpanelen jelölje ki az Azure-előfizetést, amely számára telepíteni kívánja. Jelentkezzen be, ha szükséges, és válassza ki **tovább**.

    ![Közzététel az Azure alkalmazás bejelentkezési oldal](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

4. Az a **közös beállítások** lapra, győződjön meg arról, hogy az a felhő nevére, amelyre telepíti, a **környezet**, a **buildkonfigurációhoz**, és a **Szolgáltatáskonfiguráció** helyességét.

    ![Az Azure-alkalmazás általános beállítások lapon közzététele](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

5. Az a **speciális beállítások** lapon ellenőrizze, hogy a **üzemelő példány címkéje** és a **tárfiók** helyes-e. Ellenőrizze, hogy a **hiba esetén a telepítés törlése** jelölőnégyzet nincs bejelölve, és ellenőrizze, hogy a **aktualizace Nasazení** jelölőnégyzet be van jelölve. Törölje a jelet a **hiba esetén a telepítés törlése** jelölőnégyzet, akkor győződjön meg arról, hogy a virtuális IP-cím akkor nem vesznek el, ha hiba történik az üzembe helyezés során. Válassza a **aktualizace Nasazení** jelölőnégyzet, akkor győződjön meg arról, hogy az üzemelő példány nem törlődik, és a virtuális IP-cím nem vesznek el, ha ismét közzéteszi az alkalmazást. 

    ![Közzététel az Azure Application speciális beállítások lap](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

6. További, adja meg, hogyan szeretné frissíteni kell a szerepkörök **beállítások** melletti **aktualizace Nasazení**. Ezek közül bármelyikre **növekményes frissítés** vagy **egyidejű frissítése**, és válassza ki **OK**. Válasszon **növekményes frissítés** minden egyes példányának frissítése az alkalmazás egymás után, így az alkalmazás mindig elérhető. Válasszon **egyidejű frissítése** frissíteni egy időben az alkalmazás összes példányáról. Egyidejű frissítése gyorsabb, de a szolgáltatás nem feltétlenül érhető el a frissítési folyamat alatt. Ha elkészült, válassza ki a **tovább**.

    ![Az Azure-alkalmazás központi telepítési beállítások lapon közzététele](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

7. Az a **Azure-alkalmazások közzététele** párbeszédpanelen jelölje ki **tovább** mindaddig, amíg a **összefoglalás** lap jelenik meg. Ellenőrizze a beállításokat, és válassza ki **közzététel**.
   
    ![Az Azure-alkalmazások – összegzés lapján közzététele](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>További lépések
- [A Visual Studio közzététel az Azure-alkalmazás varázsló](vs-azure-tools-publish-azure-application-wizard.md)

