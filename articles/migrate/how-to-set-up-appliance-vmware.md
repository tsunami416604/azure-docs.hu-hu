---
title: Azure Migrate készülék beállítása a VMware-hez
description: Ismerje meg, hogyan állíthat be egy Azure Migrate készüléket a VMware virtuális gépek felméréséhez és áttelepítéséhez.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 1489d29f854b02cf493493fe022c73dc050e2615
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185857"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Készülék beállítása VMware virtuális gépekhez

Ez a cikk azt ismerteti, hogyan állíthatja be a Azure Migrate berendezést, ha a VMware virtuális gépeket a Azure Migrate Server Assessment eszközzel értékeli, vagy a VMware virtuális gépeket az Azure-ba telepíti az ügynök nélküli Migrálás használatával az Azure Migrate Server áttelepítési eszközzel.

A VMware VM-készülék egy olyan, a Azure Migrate Server Assessment/Migration által használt, könnyű berendezés, amely a következő műveleteket végzi el:

- Derítse fel a helyszíni VMware virtuális gépeket.
- Metaadatok és teljesítményadatok elküldése a felderített virtuális gépek számára a kiszolgálók felmérése és áttelepítése Azure Migrate.

[További](migrate-appliance.md) információ az Azure Migrate készülékről.


## <a name="appliance-deployment-steps"></a>Berendezések üzembe helyezésének lépései

A készülék beállítása:
- Töltse le a petesejtek sablon fájlját, és importálja vCenter Serverba.
- Hozza létre a készüléket, és győződjön meg róla, hogy tud kapcsolódni Azure Migrate Server Assessmenthez.
- Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel.

## <a name="download-the-ova-template"></a>A petesejtek sablon letöltése

1. Az **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: kiszolgáló értékelése**területen kattintson a **felderítés**gombra.
2. A **Gépek felderítése** > **Virtualizáltak a gépek?** területen kattintson az **Igen, a VMware vSphere hipervizorral** lehetőségre.
3. Kattintson a **Letöltés** gombra az .OVA sablonfájl letöltéséhez.



### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a petesejtek fájlja biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a petesejtek kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A készülék legújabb verziójához a generált kivonatnak meg kell egyeznie ezekkel a beállításokkal.

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
  SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c


## <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és hozzon létre egy virtuális gépet.

1. A vSphere Client-konzolon kattintson a **Fájl** > **OVF-sablon telepítése** elemre.
2. A OVF-sablon központi telepítése varázslóban > **forrás**mezőben határozza meg a petesejt-fájl helyét.
3. A **név** és **hely**mezőben adjon meg egy rövid nevet a virtuális gép számára. Válassza ki azt a leltár objektumot, amelyben a virtuális gép üzemeltetve lesz.
5. A **gazdagép/fürt**területen adja meg azt a gazdagépet vagy fürtöt, amelyen a virtuális gép futni fog.
6. A **tárterület**területen határozza meg a virtuális gép tárolási célját.
7. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
8. A **hálózati megfeleltetés**területen válassza ki azt a hálózatot, amelyhez a virtuális gép csatlakozni fog. A hálózatnak internetkapcsolatra van szüksége, hogy metaadatokat küldjön Azure Migrate kiszolgáló értékelésére.
9. Tekintse át és hagyja jóvá a beállításokat, majd kattintson a **Finish** (Befejezés) gombra.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gépe tud csatlakozni az [Azure URL-címekhez](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

1. A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gépre, majd kattintson az **Open Console** (Konzol megnyitása) elemre.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonra kattintva.
4. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
    - **Licenc**: fogadja el a licencfeltételeket, és olvassa el a harmadik féltől származó információkat.
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
        - Kattintson a **Proxybeállítások**elemre, és határozza meg a proxy címe és a figyelő portját http://ProxyIPAddress vagy http://ProxyFQDNformában.
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
4. Kattintson a **regisztrálás**gombra.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>A folyamatos felderítés elindítása a vCenter Server és a virtuális gép hitelesítő adatainak megadásával

A készüléknek csatlakoznia kell a vCenter Serverhoz a virtuális gépek konfigurációjának és teljesítményének felderítéséhez.

### <a name="specify-vcenter-server-details"></a>A vCenter Server adatainak megadása
1. A **vCenter Server részletek megadása lapon**adja meg a vCenter Server nevét (FQDN) vagy IP-címét. Meghagyhatja az alapértelmezett portot, vagy megadhat egy egyéni portot, amelyen a vCenter Server figyeli.
2. A **Felhasználónév** és a **jelszó**mezőben adja meg a írásvédett fiók hitelesítő adatait, amelyet a berendezés a vCenter-kiszolgálón lévő virtuális gépek felderítéséhez használ majd. Győződjön meg arról, hogy a fiók rendelkezik a [felderítéshez szükséges engedélyekkel](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). A felderítés hatókörét a vCenter-fiókhoz való hozzáférés korlátozásával teheti meg. További információk a hatókör-felderítésről [itt](tutorial-assess-vmware.md#scoping-discovery).
3. Kattintson a **kapcsolat ellenőrzése** elemre, és győződjön meg arról, hogy a készülék csatlakozhat vCenter Serverhoz.

### <a name="specify-vm-credentials"></a>Virtuális gép hitelesítő adatainak megadása
Az alkalmazások, szerepkörök és szolgáltatások felderítéséhez, valamint a virtuális gépek függőségeinek megjelenítéséhez megadhat egy virtuális gép hitelesítő adatait, amely hozzáfér a VMware virtuális gépekhez. Hozzáadhat egy hitelesítő adatot a Windows rendszerű virtuális gépekhez, és egy hitelesítő adatot a linuxos virtuális gépekhez. [További](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) információ a szükséges hozzáférési jogosultságokról.

> [!NOTE]
> Ez a bemenet nem kötelező, és az alkalmazás-felderítés és az ügynök nélküli függőségi vizualizáció engedélyezéséhez szükséges.

1. Az **alkalmazások és a virtuális gépek függőségeinek felderítése**területen kattintson a **hitelesítő adatok hozzáadása**lehetőségre.
2. Válassza ki az **operációs rendszert**.
3. Adjon meg egy rövid nevet a hitelesítő adatokhoz.
4. A **Felhasználónév** és a **jelszó**mezőben olyan fiókot válasszon, amely legalább vendég hozzáféréssel rendelkezik a virtuális gépeken.
5. Kattintson az **Hozzáadás** parancsra.

Miután megadta a vCenter Server és a virtuális gép hitelesítő adatait (nem kötelező), kattintson a Save (Mentés) gombra, **és indítsa el a felderítést** a helyszíni környezet felderítésének megkezdéséhez.

Körülbelül 15 percet vesz igénybe, hogy a felderített virtuális gépek metaadatai megjelenjenek a portálon. A telepített alkalmazások, szerepkörök és szolgáltatások felderítése hosszabb időt vesz igénybe, az időtartam a felderített virtuális gépek számától függ. 500 virtuális gépek esetében körülbelül 1 órát vesz igénybe, hogy az alkalmazás leltára megjelenjen a Azure Migrate-portálon.

## <a name="next-steps"></a>Következő lépések

Tekintse át a [VMware Assessment](tutorial-assess-vmware.md) és az [ügynök nélküli áttelepítés](tutorial-migrate-vmware.md)oktatóanyagait.
