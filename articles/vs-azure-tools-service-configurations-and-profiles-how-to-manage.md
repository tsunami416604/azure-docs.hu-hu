---
title: Szolgáltatáskonfiguráció és profilok kezelése |} Microsoft Docs
description: Szolgáltatás-konfigurációk és profilok konfigurációs fájlok útmutató |} amely tárolja a központi telepítési környezetekben-beállításokat, és a közzétételi beállítások felhőszolgáltatásai számára.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 7da8c551-fb06-4057-b5c7-c77f4b39d803
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 8/11/2017
ms.author: ghogen
ms.openlocfilehash: 4f2dce7cdbf81718f9ccf0c1b7d52c6678cc847f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-manage-service-configurations-and-profiles"></a>Szolgáltatáskonfiguráció és profilok kezelése
## <a name="overview"></a>Áttekintés
Amikor közzétesz egy felhőalapú szolgáltatás, a Visual Studio kétféle konfigurációs fájlok konfigurációs információkat tárolja: konfigurációk és profilok. A szolgáltatáskonfiguráció (.cscfg-fájlok) a központi telepítési környezetekben az Azure-felhőszolgáltatás beállítások tárolásához. Azure használja ezeket a konfigurációs fájlokat, amikor a cloud services csomag kezeli. Profilok (.azurePubxml fájlok) tároló, másrészt közzététele a felhőalapú szolgáltatások beállításai. Ezek a beállítások esetén beállításoktól a Közzététel varázsló használatakor, és helyben használják a Visual Studio álló rekord. Ez a témakör ismerteti, hogyan működnek és konfigurációs fájlok mindkét típusú.

## <a name="service-configurations"></a>Szolgáltatás-konfigurációk
Létrehozhat több szolgáltatás használandó konfigurációk, az egyes a telepítési környezetekben. Például előfordulhat, hogy hozzon létre a helyi környezetben futtathatja és tesztelheti az Azure-alkalmazások és egy másik szolgáltatás beállítása az éles környezetben használt service konfigurációját.

Hozzáadhat, törlése, nevezze át, és ezek a követelmények alapján szolgáltatáskonfiguráció módosítása. A szolgáltatáskonfiguráció kezelheti a Visual Studio, a következő ábrán látható módon.

![Szolgáltatás-konfigurációk kezelése](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

Is megnyithatja a **konfigurációk kezelése** a szerepkör tulajdonságlapjain párbeszédpanelt. Az Azure-projekt szerepkör tulajdonságainak megnyitásához nyissa meg a helyi menü az adott szerepkörhöz, és válassza **tulajdonságok**. Az a **beállítások** lapján bontsa ki a **szolgáltatáskonfiguráció** listában, és válassza ki **kezelése** megnyitásához a **konfigurációk kezelése** a párbeszédpanel.

### <a name="to-add-a-service-configuration"></a>A szolgáltatás konfigurációjának hozzáadása
1. A Solution Explorerben nyissa meg az Azure-projekt helyi menüjének, és válassza ki **konfigurációk kezelése**.
   
    A **szolgáltatáskonfiguráció kezelése** párbeszédpanel jelenik meg.
2. Egy szolgáltatás-konfiguráció hozzáadásához egy meglévő konfigurációt másolatot kell létrehoznia. Ehhez az szükséges, válassza ki a használni kívánt név listában másolja, majd válassza ki a konfigurációs **készítsen másolatot**.
3. (Választható) Adjon meg más nevet a szolgáltatás konfigurációját, az új szolgáltatáskonfiguráció neve listájából válasszon ki, és válassza ki **átnevezése**. Az a **neve** szöveg mezőjébe írja be a nevet kíván használni a szolgáltatás konfigurációját, és válassza ki a kívánt **OK**.
   
    Egy új szolgáltatáskonfigurációs fájlt nevű ServiceConfiguration. [Új neve] .cscfg hozzáadódik az Azure projektben a Megoldáskezelőre.

### <a name="to-delete-a-service-configuration"></a>A szolgáltatás konfigurációjának törlése
1. A Solution Explorerben nyissa meg az Azure-projekt helyi menüjének, és válassza ki **konfigurációk kezelése**.
   
    A **szolgáltatáskonfiguráció kezelése** párbeszédpanel jelenik meg.
2. Szolgáltatáskonfiguráció törléséhez válassza ki a törölni kívánt konfigurációról a **neve** listában, és válassza ki **eltávolítása**. Győződjön meg arról, hogy ezt a konfigurációt törölni kívánja a megjelenik egy párbeszédpanel.
3. Válassza a **Törlés** elemet.
   
     A szolgáltatás konfigurációs fájlja a Azure projektben a Megoldáskezelőre törlődik.

### <a name="to-rename-a-service-configuration"></a>A szolgáltatáskonfiguráció átnevezése
1. A Megoldáskezelőben, nyissa meg az Azure-projekt helyi menüjének, és válassza **konfigurációk kezelése**.
   
    A **szolgáltatáskonfiguráció kezelése** párbeszédpanel jelenik meg.
2. Nevezze át a szolgáltatás konfigurációját, válassza ki az új szolgáltatás-konfiguráció a **neve** listában, és válassza ki **átnevezése**. Az a **neve** szöveg mezőbe írja be a nevét, a szolgáltatás konfigurációját, és válassza ki a kívánt **OK**.
   
    A szolgáltatás konfigurációs fájl nevét a Azure projektben a Megoldáskezelőre módosul.

### <a name="to-change-a-service-configuration"></a>A szolgáltatás konfigurációjának módosítása
* A szolgáltatás konfigurációjának módosítása, nyissa meg az adott szerepkörhöz szeretné módosítani az Azure-projekt, és válassza a helyi menü **tulajdonságok**. Lásd: [Útmutató: a szerepkörök konfigurálása a Visual Studio Azure Cloud Service](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) további információt.

## <a name="make-different-setting-combinations-by-using-profiles"></a>Ellenőrizze a különböző beállítás kombinációk profilok használatával
A profil segítségével automatikusan megadhatja az **közzététele varázsló** eltérő kombinációja a különböző célokra beállításait. Például rendelkezhet egy profil hibakeresési, és összeállít egy másik, a kiadáshoz. Ebben az esetben a **Debug** profil kellene **IntelliTrace** engedélyezve van és a **Debug** konfigurációs kiválasztva, és a **kiadás** profil kellene **IntelliTrace** le van tiltva, és a **kiadás** kiválasztott konfigurációs. Egy másik tárolóhelyre fiókkal szolgáltatás telepítése különböző profilok is használhatja.

Amikor első alkalommal futtatja a varázslót, létrejön egy alapértelmezett profilt. A Visual Studio a profilt tárol az Azure-projekt alatt felvett .azurePubXml kiterjesztéssel rendelkező fájlt a **profilok** mappát. Ha manuálisan ad meg más lehetőségek a varázsló későbbi futtatásakor, a fájl automatikusan frissíti. Az alábbi eljárás futtatása előtt kell már közzétett a felhőalapú szolgáltatáshoz legalább egyszer.

### <a name="to-add-a-profile"></a>Profil hozzáadása
1. Az Azure-projekt helyi menüjének megnyitásához, majd válassza ki **közzététel**.
2. Mellett a **céloz profil** listáról válassza ki a **profil mentése** gomb, az alábbi ábrán látható módon. Ez létrehoz egy profilt.
   
    ![Új profil létrehozása](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)
3. A profil létrehozása után válassza ki a **< kezelés... >** a a **céloz profil** listája.
   
    A **profilok kezelése** párbeszédpanel jelenik meg, az alábbi ábrán látható módon.
   
    ![Profilok párbeszédpanel kezelése](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)
4. Az a **neve** listában válassza ki a profilt, és válassza ki **másolat létrehozása**.
5. Válassza ki a **Bezárás** gombra.
   
    Az új profil megjelenik a profil listáját.
6. Az a **céloz profil** listára, válassza ki az imént létrehozott profil. A varázsló közzététele beállításai vannak felveszi a választási lehetőségek a kiválasztott profil.
7. Válassza ki a **előző** és **következő** gomb megjelenítése a közzététele varázsló minden lapján, és azután testre szabhatják a beállítások megadása ehhez a profilhoz. Lásd: [Azure alkalmazás közzététele varázsló](http://go.microsoft.com/fwlink/p/?LinkID=623085) információt.
8. Miután befejezte a beállítások testreszabása, válassza ki **tovább** a beállítások lapra való visszatéréshez. A szolgáltatás által ezekkel a beállításokkal közzétételekor, vagy ha a profil mentett **mentése** profilok listája mellett.

### <a name="to-rename-or-delete-a-profile"></a>Nevezze át vagy profil törlése
1. Az Azure-projekt helyi menüjének megnyitásához, majd válassza ki **közzététel**.
2. Az a **céloz profil** listáról válassza ki **kezelése**.
3. Az a **profilok kezelése** párbeszédpanel megnyitásához, válassza ki a törölni kívánt, és válassza **eltávolítása**.
4. A megjelenő megerősítő párbeszédpanelen válassza ki a **OK**.
5. Válassza ki **Bezárás**.

### <a name="to-change-a-profile"></a>A profil módosítása
1. Az Azure-projekt helyi menüjének megnyitásához, majd válassza ki **közzététel**.
2. Az a **céloz profil** listára, válassza ki a módosítani kívánt profilt.
3. Válassza ki a **előző** és **következő** gombokkal megjelenítése a közzététele varázsló minden lapján, és módosítsa a kívánt beállításokat. Lásd: [Azure alkalmazás közzététele varázsló](http://go.microsoft.com/fwlink/p/?LinkID=623085) információt.
4. Válassza a beállítások módosítása után **következő** térhet vissza a **beállítások** lap.
5. (Nem kötelező) jelölje ki **közzététel** közzététele a felhőalapú szolgáltatás, az új beállításokkal. Ha közzététele a felhőalapú szolgáltatás jelenleg nem kívánja, és a közzététel bezárása, Visual Studio kéri, ha szeretné-e a profil módosításainak mentéséhez.

## <a name="next-steps"></a>További lépések
Az Azure a Visual Studio-projekt többi részével konfigurálásával kapcsolatos további tudnivalókért lásd: [egy Azure-projekt konfigurálása](http://go.microsoft.com/fwlink/p/?LinkID=623075)

