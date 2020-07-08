---
title: Azure Migrate készülék beállítása a VMware-hez
description: Ismerje meg, hogyan állíthat be egy Azure Migrate készüléket a VMware virtuális gépek felméréséhez és áttelepítéséhez.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: b32c6a9b703e4d341fe353d6b472ea7a18adadf3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81538256"
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
- Töltse le a petesejtek sablon fájlját, és importálja vCenter Serverba.
- Hozza létre a készüléket, és győződjön meg róla, hogy tud kapcsolódni Azure Migrate Server Assessmenthez.
- Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel.

## <a name="download-the-ova-template"></a>A petesejtek sablon letöltése

1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderítés**gombra.
2. A **felderítési gépeken**a  >  **gépek virtualizáltak?**, kattintson **az igen, a VMware vSphere hypervisor**lehetőségre.
3. Kattintson a **Letöltés** gombra az .OVA sablonfájl letöltéséhez.

  ![A PETESEJT-fájlok letöltésének kiválasztása](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a petesejtek fájlja biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a petesejtek kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A készülék legújabb verziójához a generált kivonatnak meg kell egyeznie ezekkel a [beállításokkal](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security).



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


## <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal. Ha a berendezést egy, a PETESEJT-sablon helyett parancsfájl használatával telepíti, akkor az eljárás első két lépése nem alkalmazható.

1. A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gépre, majd kattintson az **Open Console** (Konzol megnyitása) elemre.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonra kattintva.
4. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
    - **Licenc**: fogadja el a licencfeltételeket, és olvassa el a harmadik féltől származó információkat.
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
        - Kattintson a proxybeállítások elemre, és írja be a proxy címe és a figyelő portját az űrlap vagy a **értékre** http://ProxyIPAddress http://ProxyFQDN .
        - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
        - Csak a HTTP-proxyk használata támogatott.
    - **Idő szinkronizálása**: az idő ellenőrzése megtörtént. A készüléken az idő, hogy a felderítés megfelelően működjön, szinkronizálva kell lennie az internettel.
    - **Frissítések telepítése**: Azure Migrate ellenőrzi, hogy telepítve vannak-e a legújabb készülék frissítései.
    - A **VDDK telepítése**: Azure Migrate ellenőrzi, hogy telepítve van-e a VMware vSphere Virtual Disk Development Kit (VDDK).
        - Az Azure áttelepítése a VDDK használatával replikálja a gépeket az Azure-ba való áttelepítés során.
        - Töltse le a VDDK 6,7-et a VMware-ből, és bontsa ki a letöltött zip-tartalmat a készülék megadott helyére.

## <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Kattintson **a bejelentkezés**elemre. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
2. Az új lapon jelentkezzen be az Azure-beli hitelesítő adataival.
    - Jelentkezzen be a felhasználónevével és jelszavával.
    - A PIN-kóddal való bejelentkezés nem támogatott.
3. A sikeres bejelentkezés után térjen vissza a webalkalmazáshoz.
2. Válassza ki azt az előfizetést, amelyben a Azure Migrate projektet létrehozták. Ezután válassza ki a projektet.
3. Adja meg a berendezés nevét. A névnek legalább 14 karakterből kell állnia.
4. Kattintson a **Regisztrálás** parancsra.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>A folyamatos felderítés elindítása a vCenter Server és a virtuális gép hitelesítő adatainak megadásával

A készüléknek csatlakoznia kell a vCenter Serverhoz a virtuális gépek konfigurációjának és teljesítményének felderítéséhez.

### <a name="specify-vcenter-server-details"></a>A vCenter Server adatainak megadása
1. A **vCenter Server részletek megadása lapon**adja meg a vCenter Server nevét (FQDN) vagy IP-címét. Meghagyhatja az alapértelmezett portot, vagy megadhat egy egyéni portot, amelyen a vCenter Server figyeli.
2. A **Felhasználónév** és a **jelszó**mezőben adja meg a írásvédett fiók hitelesítő adatait, amelyet a berendezés a vCenter-kiszolgálón lévő virtuális gépek felderítéséhez használ majd. A felderítést a vCenter-fiókhoz való hozzáférés korlátozásával szűkítheti. [További információk](set-discovery-scope.md).
3. Kattintson a **kapcsolat ellenőrzése** elemre, és győződjön meg arról, hogy a készülék csatlakozhat vCenter Serverhoz.

### <a name="specify-vm-credentials"></a>Virtuális gép hitelesítő adatainak megadása
Az alkalmazások, szerepkörök és szolgáltatások felderítéséhez, valamint a virtuális gépek függőségeinek megjelenítéséhez megadhat egy virtuális gép hitelesítő adatait, amely hozzáfér a VMware virtuális gépekhez. Hozzáadhat egy hitelesítő adatot a Windows rendszerű virtuális gépekhez, és egy hitelesítő adatot a linuxos virtuális gépekhez. [További](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) információ a szükséges hozzáférési jogosultságokról.

> [!NOTE]
> Ez a bemenet nem kötelező, és az alkalmazás-felderítés és az ügynök nélküli függőségi vizualizáció engedélyezéséhez szükséges.

1. Az **alkalmazások és a virtuális gépek függőségeinek felderítése**területen kattintson a **hitelesítő adatok hozzáadása**lehetőségre.
2. Válassza ki az **operációs rendszert**.
3. Adjon meg egy rövid nevet a hitelesítő adatokhoz.
4. A **Felhasználónév** és a **jelszó**mezőben olyan fiókot válasszon, amely legalább vendég hozzáféréssel rendelkezik a virtuális gépeken.
5. Kattintson a **Hozzáadás** parancsra.

Miután megadta a vCenter Server és a virtuális gép hitelesítő adatait (nem kötelező), kattintson a Save (Mentés) gombra, **és indítsa el a felderítést** a helyszíni környezet felderítésének megkezdéséhez.

Körülbelül 15 percet vesz igénybe, hogy a felderített virtuális gépek metaadatai megjelenjenek a portálon. A telepített alkalmazások, szerepkörök és szolgáltatások felderítése hosszabb időt vesz igénybe, az időtartam a felderített virtuális gépek számától függ. 500 virtuális gépek esetében körülbelül 1 órát vesz igénybe, hogy az alkalmazás leltára megjelenjen a Azure Migrate-portálon.

## <a name="next-steps"></a>További lépések

Tekintse át a [VMware Assessment](tutorial-assess-vmware.md) és az [ügynök nélküli áttelepítés](tutorial-migrate-vmware.md)oktatóanyagait.
