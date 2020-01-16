---
title: Azure Migrate berendezés beállítása a Hyper-V-hez
description: Ismerje meg, hogyan állíthat be egy Azure Migrate berendezést a Hyper-V virtuális gépek felméréséhez és áttelepítéséhez.
ms.topic: article
ms.date: 11/19/2019
ms.openlocfilehash: 0704adda314b94736b01fe114c3643ef8bd83753
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029085"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Berendezés beállítása Hyper-V virtuális gépekhez

Ez a cikk azt ismerteti, hogyan állíthatja be a Azure Migrate berendezést, ha a Hyper-V virtuális gépeket a Azure Migrate Server Assessment eszközzel értékeli, vagy VMware virtuális gépeket telepít át az Azure-ba az Azure Migrate Server áttelepítési eszköz használatával.

A Hyper-V virtuálisgép-berendezés egy olyan, a Azure Migrate Server Assessment/Migration által használt, könnyű berendezés, amely a következőket teszi:

- Helyszíni Hyper-V virtuális gépek felderítése.
- Metaadatok és teljesítményadatok elküldése a felderített virtuális gépek számára a kiszolgálók felmérése és áttelepítése Azure Migrate.

[További](migrate-appliance.md) információ az Azure Migrate készülékről.


## <a name="appliance-deployment-steps"></a>Berendezések üzembe helyezésének lépései

A készülék beállítása:
- Töltsön le egy tömörített Hyper-V virtuális merevlemezt a Azure Portal.
- Hozza létre a készüléket, és győződjön meg róla, hogy tud kapcsolódni Azure Migrate Server Assessmenthez.
- Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel.

## <a name="download-the-vhd"></a>A VHD letöltése

Töltse le a készülék tömörített VHD-sablonját.

1. Az **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: kiszolgáló értékelése**területen kattintson a **felderítés**gombra.
2. A számítógépek **felderítése** > a **gépek virtualizáltak?** , kattintson **az igen, a Hyper-V**elemre.
3. A VHD-fájl letöltéséhez kattintson a **Letöltés** gombra.

    ![Virtuális gép letöltése](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a virtuális merevlemez kivonatának létrehozásához.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  A készülék verziójának 2.19.11.12 a generált kivonatnak meg kell egyeznie ezekkel a beállításokkal.

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31



## <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és hozza létre a virtuális gépet.

1. Bontsa ki a tömörített VHD-fájlt egy olyan mappába a Hyper-V-gazdagépen, amely a készülék virtuális gépét fogja üzemeltetni. Három mappa van kibontva.
2. Nyissa meg a Hyper-V kezelőjét. A **műveletek**területen kattintson a **virtuális gép importálása**elemre.

    ![VHD üzembe helyezése](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. A virtuális gép importálása varázslóban > a **Kezdés előtt**kattintson a **tovább**gombra.
3. A **mappában keresse**meg a kibontott virtuális merevlemezt tartalmazó mappát. Ezután kattintson a **Next** (Tovább) gombra.
1. A **virtuális gép kiválasztása lapon**kattintson a **tovább**gombra.
2. Az **importálási típus kiválasztása**területen kattintson **a virtuális gép másolása (új egyedi azonosító létrehozása)** elemre. Ezután kattintson a **Next** (Tovább) gombra.
3. A **cél kiválasztása**területen hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
4. A **tárolási mappák**területen hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
5. A **hálózat kiválasztása**területen adja meg azt a virtuális kapcsolót, amelyet a virtuális gép használni fog. A kapcsolónak internetkapcsolattal kell rendelkeznie az Azure-ba való adatküldéshez.
6. Az **Összefoglalás**területen tekintse át a beállításokat. Ezután kattintson a **Befejezés** gombra.
7. A Hyper-V kezelőjében > **Virtual Machines**indítsa el a virtuális gépet.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gépe tud csatlakozni az [Azure URL-címekhez](migrate-appliance.md#url-access).

## <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

1. A Hyper-V kezelőjében > **Virtual Machines**kattintson a jobb gombbal a virtuális gépre > a **kapcsolat**elemre.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonra kattintva.
1. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
    - **Licenc**: fogadja el a licencfeltételeket, és olvassa el a harmadik féltől származó információkat.
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
        - Kattintson a **Proxybeállítások**elemre, és határozza meg a proxy címe és a figyelő portját http://ProxyIPAddress vagy http://ProxyFQDN formában.
        - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
        - Csak a HTTP-proxyk használata támogatott.
    - **Idő szinkronizálása**: az idő ellenőrzése megtörtént. A készüléken az idő a virtuális gép felderítésének megfelelő működéséhez szinkronban kell lennie.
    - **Frissítések telepítése**: Azure Migrate Server Assessment ellenőrzi, hogy a készüléken telepítve vannak-e a legújabb frissítések.

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Kattintson **a bejelentkezés**elemre. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
2. Az új lapon jelentkezzen be az Azure-beli hitelesítő adataival.
    - Jelentkezzen be a felhasználónevével és jelszavával.
    - A PIN-kóddal való bejelentkezés nem támogatott.
3. A sikeres bejelentkezés után térjen vissza a webalkalmazáshoz.
4. Válassza ki azt az előfizetést, amelyben a Azure Migrate projektet létrehozták. Ezután válassza ki a projektet.
5. Adja meg a berendezés nevét. A névnek legalább 14 karakterből kell állnia.
6. Kattintson a **regisztrálás**gombra.


### <a name="delegate-credentials-for-smb-vhds"></a>Az SMB virtuális merevlemezek hitelesítő adatainak delegálása

Ha virtuális merevlemezeket futtat az SMB-n, engedélyeznie kell a hitelesítő adatok delegálását a készülékről a Hyper-V gazdagépekre. Ehhez a készülékről:

1. Futtassa ezt a parancsot a készülék virtuális gépén. A HyperVHost1/HyperVHost2 például állomásnevek.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Ezt is megteheti a berendezés Helyicsoportházirend-szerkesztőján:
    - A **helyi számítógép-házirend** > **Számítógép konfigurációja**területen kattintson **Felügyeleti sablonok** > **rendszer** > **hitelesítő adatok delegálása**elemre.
    - Kattintson duplán a **új hitelesítő adatok delegálásának engedélyezése**lehetőségre, és válassza az **engedélyezve**lehetőséget.
    - A **Beállítások**területen kattintson a **Megjelenítés**elemre, és adja hozzá a listában felderíteni kívánt Hyper-V-gazdagépeket a **wsman/** előtagként.
    - A **hitelesítő adatok delegálása**területen kattintson duplán a **friss hitelesítő adatok delegálása csak NTLM kiszolgálói hitelesítéssel**lehetőségre. Ismét adja hozzá a **wsman/** előtagként használni kívánt Hyper-V-gazdagépeket a listához.

## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Kapcsolódjon a készülékről a Hyper-V-gazdagépekhez vagy-fürtökhöz, és indítsa el a virtuális gépek felderítését.

1. A **Felhasználónév** és a **jelszó**mezőben adja meg a fiók hitelesítő adatait, amelyet a berendezés a virtuális gépek felderítéséhez használ majd. Adja meg a hitelesítő adatok rövid nevét, majd kattintson a **részletek mentése**gombra.
2. Kattintson a **gazdagép hozzáadása**lehetőségre, és adja meg a Hyper-V-gazdagép/-fürt részleteit.
3. Kattintson az **Érvényesítés**elemre. Az ellenőrzés után az egyes gazdagépeken vagy fürtökön felderíthető virtuális gépek száma látható.
    - Ha az érvényesítés sikertelen egy gazdagép esetében, tekintse át a hibát az **állapot** oszlopban látható ikon fölé helyezve. Javítsa ki a hibákat, és ismételje meg az érvényesítést.
    - Gazdagépek vagy fürtök eltávolításához válassza a > **Törlés**lehetőséget.
    - Egy adott gazdagép nem távolítható el fürtből. Csak a teljes fürtöt távolíthatja el.
    - Hozzáadhat egy fürtöt, még akkor is, ha a fürt adott gazdagépével problémák léptek fel.
4. Az ellenőrzés után kattintson a Mentés gombra, **és indítsa** el a felderítést a felderítési folyamat elindításához.

Ez elindítja a felderítést. Körülbelül 15 percet vesz igénybe, hogy a felderített virtuális gépek metaadatai megjelenjenek a Azure Portalban.

## <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítés befejezését követően ellenőrizheti, hogy a virtuális gépek megjelennek-e a portálon.

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-** servers > **Azure Migrate: kiszolgáló értékelése** lapon kattintson arra az ikonra, amely megjeleníti a **felderített kiszolgálók**darabszámát.


## <a name="next-steps"></a>Következő lépések

Próbálja ki a [Hyper-V értékelését](tutorial-assess-hyper-v.md) Azure Migrate kiszolgáló értékelésével.
