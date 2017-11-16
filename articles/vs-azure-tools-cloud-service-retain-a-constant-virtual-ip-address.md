---
title: "Hogyan szeretné megőrizni az Azure-felhőszolgáltatás állandó virtuális IP-cím |} Microsoft Docs"
description: "Útmutató: Győződjön meg arról, hogy a virtuális IP-cím (VIP) az Azure felhőalapú szolgáltatás nem változik."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 96c4ed88db5e24f439825aee5afe457a1e8c81d7
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Tartsa meg az Azure-felhőszolgáltatás állandó virtuális IP-cím
Amikor frissíti egy felhőszolgáltatás, amely az Azure szolgáltatásban üzemeltetett, szükség lehet győződjön meg arról, hogy a virtuális IP-cím (VIP) a szolgáltatás nem változik. Sok tartományi szolgáltatások a tartománynévrendszer (DNS) tartománynevek a regisztrációhoz használja. DNS működését csak akkor, ha a VIP változatlan marad. Használhatja a **közzététele varázsló** Azure eszközök annak érdekében, hogy a felhőszolgáltatás VIP nem változik mikor végezzen frissítést. A felhőszolgáltatások DNS-tartományok használatával kapcsolatos további információkért lásd: [egy egyéni tartománynevet, az Azure-felhőszolgáltatás konfigurálása](cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="publish-a-cloud-service-without-changing-its-vip"></a>Egy felhőalapú szolgáltatás közzététele a VIP módosítása nélkül
A VIP felhőalapú szolgáltatások üzembe helyezésekor az Azure-bA az adott környezetben, például az éles környezetben van lefoglalva. A VIP csak változik, ha explicit módon törölheti a központi telepítést, vagy a központi telepítés implicit módon törölte a frissítési folyamat. A VIP megőrzéséhez nem törölnie kell a központi telepítés, és meg kell győződnie arról, hogy a Visual Studio nem automatikusan törli a központi telepítés. 

A központi telepítési beállításokat adhat meg a **közzététele varázsló**, amely támogatja több központi telepítési beállítások. A friss telepítés vagy frissítés központi telepítés, amely lehet növekményes vagy egyidejű adhat meg. Mindkét típusú központi telepítést a VIP megőrzése mellett. Különböző központi telepítési típusainak-definíciók, lásd: [Azure alkalmazás közzététele varázsló](vs-azure-tools-publish-azure-application-wizard.md). Ezenkívül azt is szabályozhatja, hogy a korábbi központi telepítés egy felhőalapú szolgáltatás törölni, ha a hiba akkor fordul elő. Ha ez a lehetőség nincs megfelelően beállítva, a VIP váratlanul változhatnak.

## <a name="update-a-cloud-service-without-changing-its-vip"></a>Egy felhőalapú szolgáltatás frissítése a VIP módosítása nélkül
1. Hozzon létre, vagy a Visual Studio Azure cloud service projekt megnyitása. 

2. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre. Válassza a helyi menü **közzététel**.

    ![Közzététel menü](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

3. Az a **Azure-alkalmazás közzététele** párbeszédpanelen jelölje ki az Azure-előfizetés, amelyre telepíteni szeretné. Jelentkezzen be, ha szükséges, és jelölje be **következő**.

    ![Azure alkalmazás bejelentkezési oldal közzététele](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

4. Az a **azokat a beállításokat** lapra, győződjön meg arról, hogy a felhő neve szolgáltatás, amely telepítése esetén pedig, a **környezet**, a **Build konfigurációját**, és a **szolgáltatáskonfiguráció** helyességét.

    ![Azure-alkalmazás általános beállítások lapon közzététele](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

5. Az a **speciális beállítások** lapon ellenőrizze, hogy a **üzemelő példány címkéje** és a **tárfiók** helyes-e. Ellenőrizze, hogy a **törölje a központi telepítési hiba esetén** jelölőnégyzet nincs bejelölve, és ellenőrizze, hogy a **üzemelő példány frissítése** jelölőnégyzet be van jelölve. Törölje a jelet a **törölje a központi telepítési hiba esetén** jelölőnégyzetet, akkor győződjön meg arról, hogy a VIP akkor nem vesznek el, ha hiba történik a telepítés során. Kiválasztásával a **üzemelő példány frissítése** jelölőnégyzetet, akkor győződjön meg arról, hogy a telepítés nem törlődik, és a VIP nem vesznek el, amikor újra közzé az alkalmazást. 

    ![Közzététele az Azure alkalmazás Speciális beállítások lap](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

6. További, adja meg, hogyan szeretné frissíteni a szerepkörök **beállítások** melletti **üzemelő példány frissítése**. Válassza ki vagy **növekményes frissítés** vagy **egyidejű frissítése**, és válassza ki **OK**. Válasszon **növekményes frissítés** minden példányának frissítése az alkalmazás egymás után, így az alkalmazás mindig elérhető. Válasszon **egyidejű frissítése** frissítése az alkalmazás egyszerre az összes példányát. Egyidejű frissítése gyorsabb, de a szolgáltatás nem feltétlenül érhető el a frissítési folyamat alatt. Ha elkészült, válassza ki a **következő**.

    ![Azure-alkalmazás központi telepítési beállítások lapon közzététele](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

7. Az a **Azure-alkalmazás közzététele** párbeszédpanelen jelölje ki **következő** mindaddig, amíg a **összegzés** lap is megjelenik. Ellenőrizze a beállításokat, majd válassza ki **közzététel**.
   
    ![Azure alkalmazáshoz összegzése lap közzététele](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>Következő lépések
- [A Visual Studio használatával Azure varázsló közzététele](vs-azure-tools-publish-azure-application-wizard.md)

