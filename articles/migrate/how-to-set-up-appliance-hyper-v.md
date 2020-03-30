---
title: Azure Migrate-berendezés beállítása a Hyper-V-hez
description: Ismerje meg, hogyan állíthat be egy Azure Migrate-berendezést a Hyper-V virtuális gépek felméréséhez és áttelepítéséhez.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 80db2c1d4f5482604ca1507174b127c150f76044
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336812"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Készülék beállítása hyper-V virtuális gépekhez

Ez a cikk ismerteti, hogyan állíthatja be az Azure Migrate készüléket a Hyper-V virtuális gépek értékelésére az [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszközzel.

Az [Azure Migrate készülék](migrate-appliance.md) egy könnyű készülék, amelyet az Azure Migrate:Server Assessment/Migration használ a helyszíni Hyper-V virtuális gépek felderítésére, és virtuális gép metaadat-/teljesítményadatokat küld az Azure-nak.

Beállíthatja az Azure Migrate appliance hyper-V VM felmérés egy virtuális merevlemez-sablon, amely letölti, vagy egy PowerShell telepítőparancsfájl használatával. Ez a cikk ismerteti, hogyan állíthatja be a készüléket a VHD sablon használatával. Ha a készüléket a parancsfájl használatával szeretné beállítani, kövesse a [cikkben](deploy-appliance-script.md)található utasításokat.


## <a name="appliance-deployment-vhd"></a>A berendezés üzembe helyezése (VHD)

A készülék beállítása VHD-sablon használatával:

- Töltsön le egy tömörített Hyper-V HD-t az Azure Portalról.
- Hozza létre a készüléket, és ellenőrizze, hogy tud-e csatlakozni az Azure Áttelepítési kiszolgáló értékelése.
- Konfigurálja a készüléket az első alkalommal, és regisztrálja az Azure Migrate projekt.

## <a name="download-the-vhd"></a>A virtuális merevlemez letöltése

Töltse le a készülék tömörített VHD sablonját.

1. Az Azure Migrate: Server Assessment **(Áttelepítés:** > **Kiszolgálófelmérés)****alkalmazásban** > kattintson a **Felderítés**gombra.
2. A **Discover machines** > **Are your machines virtualizált?**, kattintson **igen, a Hyper-V**.
3. A **Virtuális merevlemez-fájl** letöltéséhez kattintson a Letöltés gombra.

    ![Virtuális gép letöltése](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>A biztonság ellenőrzése

A tömörített fájl telepítése előtt ellenőrizze, hogy a tömörített fájl biztonságos-e.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a virtuális merevlemez kivonatának létrehozásához
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  A készülék 2.19.11.12-es verziójában a létrehozott kivonatnak meg kell egyeznie ezekkel a [beállításokkal.](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v#verify-security)




## <a name="create-the-appliance-vm"></a>A készülék virtuális gépének létrehozása

Importálja a letöltött fájlt, és hozza létre a virtuális gép.

1. Bontsa ki a tömörített VHD-fájlt a Hyper-V gazdagépen lévő olyan mappába, amely a készülék virtuális gépét fogja üzemeltetni. Három mappa kibontása.
2. Nyissa meg a Hyper-V kezelőjét. A **Műveletek csoportban**kattintson **a Virtuális gép importálása gombra.**

    ![Virtuális merevlemez telepítése](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. A Virtuális gép importálása varázsló> **Mielőtt elkezdené,** kattintson a **Tovább**gombra.
3. A **Mappa megkeresése területen**adja meg a kibontott virtuális merevlemezt tartalmazó mappát. Kattintson a **Tovább** gombra.
1. A **Virtuális gép kiválasztása csoportban**kattintson a **Tovább**gombra.
2. A **Típus kiválasztása csoportban**kattintson **a Virtuális gép másolása gombra (hozzon létre egy új egyedi azonosítót).** Kattintson a **Tovább** gombra.
3. A **Cél kiválasztása területen**hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
4. A **Tárolómappák mezőben**hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
5. A **Hálózat kiválasztása területen**adja meg a virtuális gép által használni kívánt virtuális kapcsolót. A kapcsolónak internetkapcsolatra van szüksége az Azure-ba való adatküldéshez.
6. Az **Összegzés alkalmazásban**tekintse át a beállításokat. Ezután kattintson a **Befejezés** gombra.
7. A Hyper-V Manager > **virtuális gépek,** indítsa el a virtuális gépet.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-hoz való hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gép [e-alapú URL-címekhez.](migrate-appliance.md#url-access)

## <a name="configure-the-appliance"></a>A készülék konfigurálása

Először állítsa be a készüléket. Ha a készüléket egy virtuális merevlemez helyett parancsfájl használatával telepíti, az eljárás első két lépése nem alkalmazható.

1. A Hyper-V Manager > **a virtuális gépek**ben kattintson a jobb gombbal a Virtuálisgép > Csatlakozás **parancsra.**
2. Adja meg a készülék nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozni tud a virtuális géphez, és nyissa meg a készülék webalkalmazásÁNAK URL-címét: **https:// készülék nevét vagy*IP-címét:* 44368**.

   Másik lehetőségként az alkalmazás parancsikonjára kattintva is megnyithatja az alkalmazást a készülék asztaláról.
1. A webalkalmazásban > **Az előfeltételek beállítása**beállításhoz tegye a következőket:
    - **Licenc**: Fogadja el a licencfeltételeket, és olvassa el a harmadik fél től származó információkat.
    - **Kapcsolat:** Az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
        - Kattintson **a Proxy beállításai gombra,** és adja http://ProxyIPAddress http://ProxyFQDNmeg a proxycímet és a figyelőportot az űrlapon vagy a ban.
        - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
        - Csak a HTTP-proxyk használata támogatott.
    - **Időszinkronizálás**: Az idő ellenőrizve van. A készüléken töltött időnek szinkronban kell lennie az internetidővel, hogy a virtuális gép felderítése megfelelően működjön.
    - **Frissítések telepítése**: Az Azure Migrate Server Assessment ellenőrzi, hogy a készüléken telepítve vannak-e a legújabb frissítések.

### <a name="register-the-appliance-with-azure-migrate"></a>Regisztrálja a készüléket az Azure Migrate szolgáltatással

1. Kattintson **a Bejelentkezés gombra.** Ha nem jelenik meg, győződjön meg arról, hogy letiltotta az előugró ablakok blokkolását a böngészőben.
2. Az új lapon jelentkezzen be az Azure-hitelesítő adatokkal.
    - Jelentkezzen be felhasználónevével és jelszavával.
    - A PIN-kóddal való bejelentkezés nem támogatott.
3. Sikeres bejelentkezés után lépjen vissza a webalkalmazásba.
4. Válassza ki azt az előfizetést, amelyben az Azure Migrate projekt jött létre. Ezután válassza ki a projektet.
5. Adja meg a készülék nevét. A névnek alfanumerikusnak kell lennie, legkevesebb karakterrel.
6. Kattintson a **Regisztráció gombra.**


### <a name="delegate-credentials-for-smb-vhds"></a>SMB VHD-k hitelesítő adatainak delegálása

Ha virtuális gépeken fut, engedélyeznie kell a hitelesítő adatok delegálását a készülékről a Hyper-V gazdagépekre. Ehhez a készülék:

1. A virtuális gép a készüléken futtassa ezt a parancsot. HyperVHost1/HyperVHost2 példa állomásnevek.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Ezt a készülék helyi csoportházirend-szerkesztőjében is megteheti:
    - A **Helyi számítógépházirend-konfiguráció** > **párbeszédpanelen**kattintson **a Felügyeleti sablonok** > **rendszerhitelesítő** > **adatok delegálása**elemre.
    - Kattintson duplán **a Friss hitelesítő adatok delegálásának engedélyezése**elemre, és válassza az **Engedélyezve**lehetőséget.
    - A **Beállítások párbeszédpanelen**kattintson a **Megjelenítés**gombra, és adja hozzá a felderíteni kívánt Hyper-V-állomásokat a listához, **a wsman/** előtaggal.
    - A **Hitelesítő adatok delegálása**mezőben kattintson duplán a **Friss hitelesítő adatok delegálásának engedélyezése csak NTLM kiszolgálóhitelesítéssel**elemre. Ismét adja hozzá az egyes Hyper-V gazdagépeket, amelyeket fel szeretne deríteni a listához, **a wsman/** előtaggal.

## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Csatlakozzon a készülékről a Hyper-V gazdagépekhez vagy fürtökhöz, és indítsa el a virtuális gép felderítését.

1. A **Felhasználónév** és **jelszó**csoportban adja meg a fiók hitelesítő adatait, amelyeket a készülék a virtuális gépek felderítéséhez használ. Adja meg a hitelesítő adatok rövid nevét, majd kattintson a **Részletek mentése gombra.**
2. Kattintson **az Állomás hozzáadása**gombra, és adja meg a Hyper-V állomás/fürt adatait.
3. Kattintson a **Validate** (Érvényesítés) elemre. Az ellenőrzés után az egyes gazdagépeken/fürtökön felderíthető virtuális gépek száma látható.
    - Ha egy állomás érvényesítése sikertelen, tekintse át a hibát úgy, hogy az Állapot oszlopban az ikonra viszi az **egérmutatót.** Javítsa ki a problémákat, és ellenőrizze újra.
    - Az állomások vagy fürtök eltávolításához válassza a > **Törlés**lehetőséget.
    - Egy adott állomás nem távolítható el a fürtből. Csak a teljes fürtet távolíthatja el.
    - Fürt hozzáadása akkor is, ha problémák vannak a fürt adott állomásaival.
4. Az ellenőrzés után kattintson a Mentés gombra, és indítsa el a **felderítést** a felderítési folyamat elindításához.

Ez elindítja a felderítést. Körülbelül 15 percet vesz igénybe, amíg a felderített virtuális gépek metaadatai megjelennek az Azure Portalon.

## <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítés befejezése után ellenőrizheti, hogy a virtuális gépek megjelennek-e a portálon.

1. Nyissa meg az Azure Migrate irányítópultot.
2. Az **Azure Migrate – Servers** > **Azure Migrate: Server Assessment (Kiszolgálói értékelés)** lapon kattintson a **felderített kiszolgálók**számát megjelenítő ikonra.


## <a name="next-steps"></a>További lépések

Próbálja ki a [Hyper-V felmérést](tutorial-assess-hyper-v.md) az Azure Áttelepítési kiszolgáló értékeléssegítségével.
