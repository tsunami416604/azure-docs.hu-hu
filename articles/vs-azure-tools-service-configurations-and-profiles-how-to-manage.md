---
title: Szolgáltatáskonfiguráció és profilok kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan használható a konfigurációk és profilok konfigurációs fájljai |} amely tárolja az alkalmazásbeállításokat az üzembe helyezési környezetekhez, és tegye közzé a cloud services beállításai.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 7da8c551-fb06-4057-b5c7-c77f4b39d803
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/11/2017
ms.author: ghogen
ms.openlocfilehash: 1acb13f8bdcb7f5a6f214a7425e13c72e21bd29d
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054307"
---
# <a name="how-to-manage-service-configurations-and-profiles"></a>Szolgáltatáskonfiguráció és profilok kezelése
## <a name="overview"></a>Áttekintés
Amikor közzétesz egy felhőalapú szolgáltatás, a Visual Studio konfigurációs fájlok kétféle konfigurációs információkat tárolja: szolgáltatás konfigurációk és profilok. Szolgáltatáskonfiguráció (.cscfg-fájlok) az üzembehelyezési környezet az Azure cloud Services számára a beállítások tárolásához. A cloud Services szolgáltatások által kezelt Azure használja ezeket a konfigurációs fájlokat. Másrészről profilokat (.azurePubxml fájlokat) tároló nastavení publikování Pro felhőszolgáltatások. Ezek a beállítások olyan beállításoktól a közzétételi varázsló használatakor, és helyben használják a Visual Studio egy rekordot. Ez a témakör ismerteti, hogyan használható a konfigurációs fájlok mindkét típusú.

## <a name="service-configurations"></a>Szolgáltatás-konfigurációk
Több szolgáltatás használandó konfigurációk, az egyes telepítési környezetekben hozhat létre. Például előfordulhat, hogy hozzon létre a helyi környezetben futtathatja és tesztelheti az Azure-alkalmazások és a egy másik szolgáltatás konfigurációja, az éles környezetben használt service konfigurációját.

Hozzáadása, törlése, nevezze át, és ezek a követelmények alapján szolgáltatáskonfiguráció módosítása. Kezelheti a szolgáltatáskonfiguráció a Visual Studióból, az alábbi ábrán látható módon.

![Szolgáltatás-konfigurációk kezelése](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

Megnyithatja a **konfigurációk kezelése** párbeszédpanelt, a szerepkör tulajdonságait tartalmazó oldalakon. Az Azure-projektben nyissa meg a szerepkör tulajdonságait, ahhoz a szerepkörhöz tartozó a helyi menü megnyitásához, és válassza **tulajdonságok**. Az a **beállítások** lapon, bontsa ki a **szolgáltatáskonfiguráció** listában, és válassza ki **kezelés** megnyitásához a **konfigurációk kezelése** párbeszédpanel bezárásához.

### <a name="to-add-a-service-configuration"></a>A szolgáltatás konfigurációjának hozzáadása
1. A Megoldáskezelőben nyissa meg az Azure-projekt helyi menüjének, és válassza ki **konfigurációk kezelése**.
   
    A **szolgáltatáskonfiguráció kezelése** párbeszédpanel jelenik meg.
2. Szeretne hozzáadni a szolgáltatás konfigurációja, létre kell hoznia egy másolatot egy meglévő konfigurációt. Ehhez válassza ki a név listából másolja, majd kattintson a kívánt konfigurációját **másolatot**.
3. (Nem kötelező) Adjon meg más nevet a szolgáltatás konfigurációját, a szolgáltatás új konfigurációt választhat a nevét, majd **átnevezése**. Az a **neve** szöveg írja be a nevét, a szolgáltatás konfigurációját használja, és válassza ki a kívánt **OK**.
   
    Egy új konfigurációs fájlban a szolgáltatáskonfiguráció nevű. [Új neve] .cscfg kerül az Azure projektre a Megoldáskezelőben.

### <a name="to-delete-a-service-configuration"></a>A szolgáltatás konfigurációjának törlése
1. A Megoldáskezelőben nyissa meg az Azure-projekt helyi menüjének, és válassza ki **konfigurációk kezelése**.
   
    A **szolgáltatáskonfiguráció kezelése** párbeszédpanel jelenik meg.
2. Szolgáltatáskonfiguráció törléséhez válassza ki a törölni kívánt konfigurációját a **neve** listában, majd **eltávolítása**. Megjelenik egy párbeszédpanel, győződjön meg arról, hogy szeretné-e ez a konfiguráció törlése.
3. Válassza a **Törlés** elemet.
   
     A szolgáltatás konfigurációs fájlja távolítja el az Azure projektre a Megoldáskezelőben.

### <a name="to-rename-a-service-configuration"></a>A szolgáltatás konfigurációs átnevezése
1. A Megoldáskezelőben nyissa meg az Azure-projekt helyi menüjének, és válassza ki **konfigurációk kezelése**.
   
    A **szolgáltatáskonfiguráció kezelése** párbeszédpanel jelenik meg.
2. Nevezze át a szolgáltatás konfigurációja, válassza az új szolgáltatás-konfiguráció a **neve** listában, és válassza ki **átnevezése**. Az a **neve** szöveg írja be a nevét, a szolgáltatás konfigurációját használja, és válassza ki a kívánt **OK**.
   
    A szolgáltatás konfigurációs fájl nevét a az Azure-projektre a Solution Explorer módosul.

### <a name="to-change-a-service-configuration"></a>A szolgáltatás konfigurációjának módosítása
* Ha azt szeretné, módosíthatja a szolgáltatás konfigurációja, nyissa meg az adott szerepkörhöz szeretné módosítani az Azure-projektben, és válassza a helyi menü **tulajdonságok**. Lásd: [hogyan: a szerepkörök konfigurálása a Visual Studio használatával Azure Cloud Services számára](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) további információt.

## <a name="make-different-setting-combinations-by-using-profiles"></a>Győződjön meg arról, különböző beállítás kombinációk-profilok használatával
A profil használatával is automatikusan kitölti a **közzététele varázsló** beállítások különböző felhasználási célokra eltérő kombinációja. Például rendelkezhet egy profilt a hibakereséshez és összeállít egy másik kiadás. Ebben az esetben a **hibakeresése** profilt kell **IntelliTrace** engedélyezve van, és a **hibakeresése** kiválasztott, konfiguráció és a **kiadási** profil kellene **IntelliTrace** le van tiltva, és a **kiadási** kiválasztott konfiguráció. Egy másik tárfiók használatával szolgáltatás telepítése különböző profilok is használhatja.

Amikor első alkalommal futtatja a varázslót, létrejön egy alapértelmezett profilt. A Visual Studio a profilt, amely rendelkezik egy .azurePubXml bővítmény, az Azure projekt alatt felvett fájlban tárolja a **profilok** mappát. Ha manuálisan ad meg más lehetőségek a varázsló későbbi futtatásakor, a fájl automatikusan frissíti. Mielőtt futtatná az alábbi eljárást, kell már közzétette a felhőszolgáltatás legalább egyszer.

### <a name="to-add-a-profile"></a>Profil hozzáadása
1. Az Azure-projekt helyi menüjének megnyitásához, és válassza ki **közzététel**.
2. Mellett a **célprofilt** listáról válassza ki a **profil mentése** gombra az alábbi ábrán látható módon. Ezzel létrehoz egy profilt az Ön számára.
   
    ![Hozzon létre egy új profilt](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)
3. A profil létrehozása után válassza ki a **< kezelés... >** a a **célprofilt** listája.
   
    A **Spravovat Profily** párbeszédpanel jelenik meg, az alábbi ábrán látható módon.
   
    ![Profilok párbeszédpanel kezelése](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)
4. Az a **neve** listában válasszon egy profilt, és válassza ki **másolat létrehozása**.
5. Válassza ki a **Bezárás** gombra.
   
    Az új profil megjelenik a profilok listáját.
6. Az a **célprofilt** listában, válassza ki az imént létrehozott profilt. A közzétételi varázsló beállításainak a választási lehetőségek a kiválasztott profil megjelenik a rendszer.
7. Válassza ki a **előző** és **tovább** gombok választva jelenítheti meg a közzétételi varázsló egyes lapjain elérhető, majd testre szabhatja a beállításokat ehhez a profilhoz. Lásd: [Azure alkalmazás közzététele varázsló](http://go.microsoft.com/fwlink/p/?LinkID=623085) információt.
8. Miután végzett a beállítások testreszabása, válassza ki a **tovább** a beállítások lapra való visszatéréshez. Amikor közzéteszi a szolgáltatást ezek a beállítások használatával, vagy ha a profil mentett **mentése** mellett a profilok listáját.

### <a name="to-rename-or-delete-a-profile"></a>Nevezze át vagy töröl egy profilt
1. Az Azure-projekt helyi menüjének megnyitásához, és válassza ki **közzététel**.
2. Az a **célprofilt** listáról válassza ki **kezelés**.
3. Az a **Spravovat Profily** párbeszédpanel, válassza ki a törölni kívánt profilt, és válassza ki **eltávolítása**.
4. A megjelenő megerősítő párbeszédpanelen válassza ki a **OK**.
5. Válassza ki **Bezárás**.

### <a name="to-change-a-profile"></a>Profil módosítása
1. Az Azure-projekt helyi menüjének megnyitásához, és válassza ki **közzététel**.
2. Az a **célprofilt** listájához, válassza ki a módosítani kívánt profilt.
3. Válassza ki a **előző** és **tovább** gomb megjelenítéséhez a közzétételi varázsló egyes lapjain elérhető, és módosítsa a kívánt beállításokat. Lásd: [Azure alkalmazás közzététele varázsló](http://go.microsoft.com/fwlink/p/?LinkID=623085) információt.
4. Miután végzett a beállítások módosítása, válassza ki a **tovább** való visszatéréshez a **beállítások** lap.
5. (Választható) válassza **közzététel** közzététele a felhőalapú szolgáltatás, az új beállításokkal. Ha nem szeretné közzétenni a cloud service jelenleg, és a közzétételi varázsló bezárása, a Visual Studio rákérdez, hogy szeretné-e a módosítások mentése a profilba.

## <a name="next-steps"></a>További lépések
Az Azure projektet a Visual Studio más részein konfigurálásával kapcsolatos tudnivalókért lásd: [egy Azure-projekt konfigurálása](http://go.microsoft.com/fwlink/p/?LinkID=623075)

