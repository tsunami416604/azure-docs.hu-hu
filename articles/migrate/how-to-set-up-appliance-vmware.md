---
title: Azure Migrate készülék beállítása a VMware-hez
description: Ismerje meg, hogyan állíthat be egy Azure Migrate készüléket a VMware virtuális gépek felméréséhez és áttelepítéséhez.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: c9a9f1567f984fc5770b47d3998610cb69643360
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923616"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Készülék beállítása VMware virtuális gépekhez

Ebből a cikkből megtudhatja, hogyan állíthatja be az Azure Migrate készüléket a [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool és az ügynök nélküli áttelepítés számára a [Azure Migrate: Server áttelepítési](migrate-services-overview.md#azure-migrate-server-migration-tool) eszköz használatával.

Az [Azure Migrate készülék](migrate-appliance.md) egy könnyű berendezés, amelyet a Azure Migrate használ: a kiszolgáló-Értékelés és a kiszolgáló áttelepítése a helyszíni VMWare virtuális gépek felderítésére, a virtuális gépek metaadatait/teljesítményadatait az Azure-ba, valamint a VMWare virtuális gépeknek az ügynök nélküli Migrálás során történő replikálásához.

A készüléket néhány módszerrel is üzembe helyezheti:

- A VMware virtuális gépen egy letöltött PETESEJT-sablonnal állíthatja be a szolgáltatást. Ez a cikkben ismertetett módszer.
- Állítsa be a VMware virtuális gépen vagy fizikai gépen egy PowerShell-telepítő parancsfájl használatával. [Ezt a módszert](deploy-appliance-script.md) akkor kell használni, ha nem állít be egy virtuális GÉPET egy petesejt-sablon használatával, vagy ha az Azure governmentben van.

A berendezés létrehozása után győződjön meg róla, hogy tud csatlakozni Azure Migrate: kiszolgáló-értékeléshez, először konfigurálja, majd regisztrálja azt a Azure Migrate projektben.


## <a name="appliance-deployment-ova"></a>Berendezések üzembe helyezése (petesejtek)

Ha a készüléket a következő PETESEJT-sablonnal szeretné beállítani:
- Adja meg a készülék nevét, és állítson elő egy Azure Migrate Project-kulcsot a portálon
- Töltse le a petesejtek sablon fájlját, és importálja vCenter Serverba.
- Hozza létre a készüléket, és győződjön meg róla, hogy tud kapcsolódni Azure Migrate Server Assessmenthez.
- Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel a Azure Migrate Project Key használatával.

### <a name="generate-the-azure-migrate-project-key"></a>A Azure Migrate projekt kulcsának előállítása

1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**területen válassza a **felderítés**lehetőséget.
2. A **felderítési gépeken**a  >  **gépek virtualizáltak?** területen válassza **az igen, VMware vSphere Hypervisort**.
3. **1.: Azure Migrate projekt kulcsának létrehozásakor**adja meg a VMWare virtuális gépek felderítéséhez beállított Azure Migrate berendezés nevét. a névnek alfanumerikusnak kell lennie 14 karakternél vagy kevesebb értékkel.
1. Kattintson a **kulcs létrehozása** lehetőségre a szükséges Azure-erőforrások létrehozásának elindításához. Az erőforrások létrehozásakor ne zárja be a gépek felderítése lapot.
1. Az Azure-erőforrások sikeres létrehozása után létrejön egy **Azure Migrate projekt kulcsa** .
1. Másolja a kulcsot, mert szüksége lesz rá, hogy elvégezze a berendezés regisztrációját a konfiguráció során.

### <a name="download-the-ova-template"></a>A petesejtek sablon letöltése
**2.: töltse le Azure Migrate készüléket**, és válassza a (z) elemet. A petesejtek fájlt, majd kattintson a **Letöltés**gombra. 


   ![A felderítési gépek kiválasztása](./media/tutorial-assess-vmware/servers-discover.png)


   ![A kulcs létrehozásának kiválasztása](./media/tutorial-assess-vmware/generate-key-vmware.png)

### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a petesejtek fájlja biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a petesejtek kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A készülék legújabb verziójához a generált kivonatnak meg kell egyeznie ezekkel a [beállításokkal](./tutorial-assess-vmware.md#verify-security).



## <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és hozzon létre egy virtuális gépet.

1. A vSphere-ügyfél konzolján kattintson a **fájl**  >  **telepítése OVF sablon**elemre.
![OVF-sablon üzembe helyezéséhez használható menüparancsok](./media/tutorial-assess-vmware/deploy-ovf.png)

2. A OVF-sablon központi telepítése varázslóban > **forrás**mezőben határozza meg a petesejt-fájl helyét.
3. A **név** és **hely**mezőben adjon meg egy rövid nevet a virtuális gép számára. Válassza ki azt a leltár objektumot, amelyben a virtuális gép üzemeltetve lesz.
5. A **gazdagép/fürt**területen adja meg azt a gazdagépet vagy fürtöt, amelyen a virtuális gép futni fog.
6. A **tárterület**területen határozza meg a virtuális gép tárolási célját.
7. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
8. A **hálózati megfeleltetés**területen válassza ki azt a hálózatot, amelyhez a virtuális gép csatlakozni fog. A hálózatnak internetkapcsolatra van szüksége, hogy metaadatokat küldjön Azure Migrate kiszolgáló értékelésére.
9. Tekintse át és hagyja jóvá a beállításokat, majd kattintson a **Finish** (Befejezés) gombra.


## <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gépe tud csatlakozni az Azure URL-címekhez a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhők számára.


### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

> [!NOTE]
> Ha a letöltött petesejtek helyett [PowerShell-parancsfájl](deploy-appliance-script.md) használatával állítja be a készüléket, az ebben az eljárásban szereplő első két lépés nem releváns.

1. A vSphere-ügyfél konzolján kattintson a jobb gombbal a virtuális gépre, majd válassza a **konzol megnyitása**lehetőséget.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonjának kiválasztásával.
1. Fogadja el a **licencfeltételeket**, és olvassa el a harmadik féltől származó információkat.
1. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
   - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
     - A proxy **beállítása** elemre kattintva adja meg a proxy címe (az űrlapon http://ProxyIPAddress vagy a http://ProxyFQDN) figyelési porton.
     - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
     - Csak a HTTP-proxyk használata támogatott.
     - Ha hozzáadta a proxy részleteit, vagy letiltotta a proxyt és/vagy a hitelesítést, kattintson a **Save (Mentés** ) gombra a kapcsolat ismételt elindításához.
   - **Időszinkronizálás**: a készüléken az idő a megfelelő működés érdekében szinkronban kell lennie az internettel.
   - **Frissítések telepítése**: a készülék gondoskodik a legújabb frissítések telepítéséről. Az ellenőrzések befejezése után a berendezés **megtekintése** lehetőségre kattintva megtekintheti a készüléken futó összetevők állapotát és verzióit.
   - A **VDDK telepítése**: a készülék ellenőrzi, hogy telepítve van-e a VMware vSphere Virtual Disk Development Kit (VDDK). Ha nincs telepítve, töltse le a VDDK 6,7-et a VMware-ből, és bontsa ki a letöltött zip-tartalmat a berendezés megadott helyére a **telepítési utasításokban**megadott módon.

     Azure Migrate kiszolgáló áttelepítése a VDDK használatával replikálja a gépeket az Azure-ba való áttelepítés során. 
1. Ha szeretné, bármikor újrafuttathatja az **előfeltételeket** a berendezés konfigurálása során annak ellenőrzéséhez, hogy a berendezés teljesíti-e az összes előfeltételt.

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Illessze be a portálról másolt **Azure Migrate Project kulcsot** . Ha nem rendelkezik a kulccsal, lépjen a **kiszolgáló értékelése> felderítés> a meglévő berendezések kezelése**lehetőségre, válassza ki a készüléknek a kulcs létrehozásakor megadott nevét, és másolja a megfelelő kulcsot.
1. Kattintson a **Bejelentkezés**elemre. Egy új böngésző lapon nyit meg egy Azure-beli bejelentkezési kérést. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
1. Az új lapon jelentkezzen be az Azure-beli felhasználónevével és jelszavával.
   
   A PIN-kóddal való bejelentkezés nem támogatott.
3. A sikeres bejelentkezést követően térjen vissza a webalkalmazáshoz. 
4. Ha a naplózáshoz használt Azure-beli felhasználói fiók rendelkezik a megfelelő [engedélyekkel](tutorial-prepare-vmware.md#prepare-azure) a kulcs létrehozása során létrehozott Azure-erőforrásokhoz, a készülék regisztrációja kezdeményezve lesz.
1. A készülék sikeres regisztrálása után a **részletek megtekintése**lehetőségre kattintva megtekintheti a regisztráció részleteit.


## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

A készüléknek csatlakoznia kell a vCenter Serverhoz a virtuális gépek konfigurációjának és teljesítményének felderítéséhez.

1. Az **1. lépés: vCenter Server hitelesítő adatok**megadása lapon kattintson a **hitelesítő adatok hozzáadása** lehetőségre a hitelesítő adatok rövid nevének megadásához, adja hozzá a **felhasználónevet** és a **jelszót** ahhoz a vCenter Server-fiókhoz, amelyet a berendezés a vCenter Server példányban található virtuális gépek felderítéséhez használ majd.
    - Be kell állítania egy fiókot a szükséges engedélyekkel az [előző oktatóanyagban](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Ha a hatókör-felderítést meghatározott VMware-objektumokra (vCenter Server adatközpontokra, fürtökre, fürtökre, gazdagépekre, gazdagépekre vagy egyéni virtuális gépekre) szeretné alkalmazni, tekintse át az [ebben a cikkben](set-discovery-scope.md) szereplő utasításokat a Azure Migrate által használt fiók korlátozásához.
1. A **2. lépés: adja meg a vCenter Server részleteket**lehetőségnél kattintson a **felderítési forrás hozzáadása** elemre, hogy a legördülő listából válassza ki a hitelesítő adatok rövid nevét, adja meg a vCenter Server példány **IP-címét/teljes tartománynevét** . A **portot** meghagyhatja az alapértelmezett értékre (443), vagy megadhat egy egyéni portot, amelyen vCenter Server figyeli, és kattintson a **Save (Mentés**) gombra.
1. A Save (Mentés) gombra kattintva a készülék megpróbálja ellenőrizni a vCenter Serverhoz való kapcsolódást a megadott hitelesítő adatokkal, és megjeleníti az **ellenőrzési állapotot** a táblában a vCenter Server IP-cím/FQDN használatával.
1. A felderítés elindítása előtt bármikor **újraérvényesítheti** vCenter Server a kapcsolatot.
1. A **3. lépés: adja meg a virtuális gép hitelesítő adatait a telepített alkalmazások felderítéséhez, valamint az ügynök nélküli függőségek leképezésének végrehajtásához**kattintson a **hitelesítő adatok hozzáadása**lehetőségre, és adja meg azt az operációs rendszert, amelyhez a hitelesítő adatokat megadja, a hitelesítő adatok rövid nevét, valamint a **felhasználónevet** és **jelszót** Ezután kattintson a **Save (Mentés**) gombra.

    - Itt opcionálisan adhat meg hitelesítő adatokat, ha létrehozott egy fiókot az [alkalmazás-felderítési szolgáltatáshoz](how-to-discover-applications.md), vagy az [ügynök nélküli függőségek elemzése funkciót](how-to-create-group-machine-dependencies-agentless.md).
    - Ha nem szeretné használni ezeket a funkciókat, kattintson a csúszkára a lépés kihagyásához. Később bármikor visszafordíthatja a szándékot.
    - Tekintse át az alkalmazás- [felderítéshez](migrate-support-matrix-vmware.md#application-discovery-requirements)szükséges hitelesítő adatokat, vagy az [ügynök nélküli függőségek elemzéséhez](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. A virtuális gép felderítésének indításához kattintson a **felderítés indítása**lehetőségre. A felderítés sikeres elindítását követően megtekintheti a felderítési állapotot a tábla vCenter Server IP-címe/teljes tartománynevével.

A felderítés a következőképpen működik:
- Körülbelül 15 percet vesz igénybe, hogy a felderített virtuális gépek metaadatai megjelenjenek a portálon.
- A telepített alkalmazások, szerepkörök és szolgáltatások felderítése hosszabb időt vesz igénybe. Az időtartam a felderített virtuális gépek számától függ. Az 500-es virtuális gépek esetében körülbelül egy óra telik, amíg az alkalmazás leltára megjelenik a Azure Migrate-portálon.

## <a name="next-steps"></a>További lépések

Tekintse át a [VMware Assessment](tutorial-assess-vmware.md) és az [ügynök nélküli áttelepítés](tutorial-migrate-vmware.md)oktatóanyagait.
