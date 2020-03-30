---
title: Azure Migrate-készülék beállítása a VMware számára
description: Ismerje meg, hogyan állíthat be egy Azure Migrate-berendezést a VMware virtuális gépek felméréséhez és áttelepítéséhez.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 7a7d0007d2824abc781411f9529f9fa4ac89e55c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336795"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Készülék beállítása VMware virtuális gépekhez

Ez a cikk ismerteti, hogyan állíthatja be az Azure Migrate készülék et az [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszközzel, és az [Azure Migrate:Server Áttelepítés](migrate-services-overview.md#azure-migrate-server-migration-tool) eszköz használatával ügynök nélküli áttelepítés.

Az [Azure Migrate készülék](migrate-appliance.md) egy könnyű készülék, amelyet az Azure Migrate:Server Assessment and Server Migration használ a helyszíni VMware virtuális gépek felderítésére, a vm metaadat-/teljesítményadatok küldésére az Azure-ba, valamint a VMware virtuális gépek ügynök nélküli áttelepítés során történő replikációjához.

Beállíthatja az Azure Migrate appliance vmware vm-felmérés egy letöltött OVA sablon használatával, vagy egy PowerShell-telepítési parancsfájl használatával. Ez a cikk bemutatja, hogyan állíthatja be a készüléket a OVA sablon használatával. Ha a készüléket a parancsfájl használatával szeretné beállítani, kövesse a [cikkben](deploy-appliance-script.md)található utasításokat.


## <a name="appliance-deployment-ova"></a>A berendezés üzembe helyezése (OVA)

A készülék OVA sablonnal történő beállítása:
- Töltsön le egy OVA-sablonfájlt, és importálja azt a vCenter Server kiszolgálóra.
- Hozza létre a készüléket, és ellenőrizze, hogy tud-e csatlakozni az Azure Áttelepítési kiszolgáló értékelése.
- Konfigurálja a készüléket az első alkalommal, és regisztrálja az Azure Migrate projekt.

## <a name="download-the-ova-template"></a>Az OVA sablon letöltése

1. Az Azure Migrate: Server Assessment **(Áttelepítés:** > **Kiszolgálófelmérés)****alkalmazásban** > kattintson a **Felderítés**gombra.
2. A **Discover machines** > **Are your machines virtualizált?**, kattintson **igen, a VMWare vSphere hipervizor**.
3. Kattintson a **Letöltés** gombra az .OVA sablonfájl letöltéséhez.

  ![OVA fájl letöltésének kiválasztása](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>A biztonság ellenőrzése

Mielőtt üzembe helyezne OVA fájlt, ellenőrizze, hogy a OVA fájl biztonságos-e.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot az OVA kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A készülék legújabb verziójában a létrehozott kivonatnak meg kell egyeznie ezekkel a [beállításokkal.](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security)



## <a name="create-the-appliance-vm"></a>A készülék virtuális gépének létrehozása

Importálja a letöltött fájlt, és hozzon létre egy virtuális gép.

1. A vSphere ügyfélkonzolon kattintson az > **OVF-sablon fájltelepítése elemre.** **File**
![Menüparancs OVF-sablon telepítéséhez](./media/tutorial-assess-vmware/deploy-ovf.png)

2. Az OVF sablon telepítése varázslóban > **forrásból,** adja meg a OVA fájl helyét.
3. A **Név** és **a Hely területen**adja meg a virtuális gép rövid nevét. Válassza ki azt a készletobjektumot, amelyben a virtuális gép lesz tárolva.
5. Az **állomás/fürt**ben adja meg azt az állomást vagy fürtöt, amelyen a virtuális gép futni fog.
6. A **Storage alkalmazásban**adja meg a virtuális gép tárolási célját.
7. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
8. A **Hálózati leképezés**alkalmazásban adja meg azt a hálózatot, amelyhez a virtuális gép csatlakozni fog. A hálózatnak internetkapcsolatra van szüksége ahhoz, hogy metaadatokat küldjön az Azure Migrate Server Assessment szolgáltatásba.
9. Tekintse át és hagyja jóvá a beállításokat, majd kattintson a **Finish** (Befejezés) gombra.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-hoz való hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gép [e-alapú URL-címekhez.](migrate-appliance.md#url-access)


## <a name="configure-the-appliance"></a>A készülék konfigurálása

Először állítsa be a készüléket. Ha a készüléket ovasablon helyett parancsfájlhasználatával telepíti, az eljárás első két lépése nem alkalmazható.

1. A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gépre, majd kattintson az **Open Console** (Konzol megnyitása) elemre.
2. Adja meg a készülék nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozni tud a virtuális géphez, és nyissa meg a készülék webalkalmazásÁNAK URL-címét: **https:// készülék nevét vagy*IP-címét:* 44368**.

   Másik lehetőségként az alkalmazás parancsikonjára kattintva is megnyithatja az alkalmazást a készülék asztaláról.
4. A webalkalmazásban > **Az előfeltételek beállítása**beállításhoz tegye a következőket:
    - **Licenc**: Fogadja el a licencfeltételeket, és olvassa el a harmadik fél től származó információkat.
    - **Kapcsolat:** Az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
        - Kattintson **a Proxy beállításai gombra,** és adja http://ProxyIPAddress http://ProxyFQDNmeg a proxycímet és a figyelőportot az űrlapon vagy a ban.
        - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
        - Csak a HTTP-proxyk használata támogatott.
    - **Időszinkronizálás**: Az idő ellenőrizve van. A készüléken töltött időnek szinkronban kell lennie az internetidővel, hogy a felderítés megfelelően működjön.
    - **Frissítések telepítése**: Az Azure Migrate ellenőrzi, hogy a legújabb készülékfrissítések telepítve vannak-e.
    - **VDDK telepítése**: Az Azure Migrate ellenőrzi, hogy a VMWare vSphere virtuálislemez-fejlesztő készlet (VDDK) telepítve van-e.
        - Az Azure Migrates a VDDK-t használja a gépek replikálásához az Azure-ba való migrálás során.
        - Töltse le a VDDK 6.7-et a VMware-ből, és bontsa ki a letöltött zip tartalmat a készülék megadott helyére.

## <a name="register-the-appliance-with-azure-migrate"></a>Regisztrálja a készüléket az Azure Migrate szolgáltatással

1. Kattintson **a Bejelentkezés gombra.** Ha nem jelenik meg, győződjön meg arról, hogy letiltotta az előugró ablakok blokkolását a böngészőben.
2. Az új lapon jelentkezzen be az Azure-hitelesítő adatokkal.
    - Jelentkezzen be felhasználónevével és jelszavával.
    - A PIN-kóddal való bejelentkezés nem támogatott.
3. Sikeres bejelentkezés után lépjen vissza a webalkalmazásba.
2. Válassza ki azt az előfizetést, amelyben az Azure Migrate projekt jött létre. Ezután válassza ki a projektet.
3. Adja meg a készülék nevét. A névnek alfanumerikusnak kell lennie, legkevesebb karakterrel.
4. Kattintson a **Regisztráció gombra.**


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Folyamatos felderítés indítása vCenter Server és virtuális gép hitelesítő adatainak megadásával

A készüléknek csatlakoznia kell a vCenter Server szolgáltatáshoz a virtuális gépek konfigurációs és teljesítményadatainak felderítéséhez.

### <a name="specify-vcenter-server-details"></a>A vCenter Server adatainak megadása
1. A **VCenter-kiszolgáló részleteinek megadása**területen adja meg a vCenter-kiszolgáló nevét (FQDN) vagy IP-címét. Meghagyhatja az alapértelmezett portot, vagy megadhat egy egyéni portot, amelyen a vCenter-kiszolgáló figyel.
2. A **Felhasználónév** és **jelszó**csoportban adja meg azokat az írásvédett fiókhitelesítő adatokat, amelyeket a készülék a vCenter-kiszolgálón lévő virtuális gépek felderítéséhez használ. A felderítés hatóköre a vCenter-fiókhoz való hozzáférés korlátozásával. [További információ](set-discovery-scope.md).
3. Kattintson **a Kapcsolat ellenőrzése** elemre, és győződjön meg arról, hogy a készülék csatlakozni tud a vCenter Server kiszolgálóhoz.

### <a name="specify-vm-credentials"></a>Virtuálisgép-hitelesítő adatok megadása
Az alkalmazások, szerepkörök és szolgáltatások felderítése és a virtuális gépek függőségeinek megjelenítéséhez biztosíthat egy virtuális gép hitelesítő adatait, amely hozzáfér a VMware virtuális gépekhez. Hozzáadhat egy hitelesítő adatot a Windows virtuális gépekhez és egy hitelesítő adatot a Linux-virtuális gépekhez. [További információ](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) a szükséges hozzáférési jogosultságokról.

> [!NOTE]
> Ez a bemenet nem kötelező, és az alkalmazásfelderítés és az ügynök nélküli függőségi vizualizáció engedélyezéséhez szükséges.

1. A **Virtuális gépeken lévő alkalmazások és függőségek felderítése**területen kattintson **a Hitelesítő adatok hozzáadása gombra.**
2. Válassza ki az **operációs rendszert**.
3. Adjon meg egy rövid nevet a hitelesítő adatoknak.
4. A **Felhasználónév** és **jelszó**csoportban adjon meg egy olyan fiókot, amely legalább vendéghozzáféréssel rendelkezik a virtuális gépeken.
5. Kattintson a **Hozzáadás** gombra.

Miután megadta a vCenter Server és a Virtuálisgép hitelesítő adatait (nem kötelező), kattintson a **Mentés gombra, és indítsa el** a felderítést a helyszíni környezet felderítésének megkezdéséhez.

Körülbelül 15 percet vesz igénybe, amíg a felderített virtuális gépek metaadatai megjelennek a portálon. A telepített alkalmazások, szerepkörök és szolgáltatások felderítése némi időt vesz igénybe, az időtartam a felderített virtuális gépek számától függ. 500 virtuális gép esetén körülbelül 1 órát vesz igénybe, amíg az alkalmazáskészlet megjelenik az Azure Migrate portalon.

## <a name="next-steps"></a>További lépések

Tekintse át a [VMware-értékelésés](tutorial-assess-vmware.md) [ügynök nélküli áttelepítés oktatóanyagait.](tutorial-migrate-vmware.md)
