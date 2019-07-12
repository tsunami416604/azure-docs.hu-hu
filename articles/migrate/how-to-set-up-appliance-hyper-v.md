---
title: Állítsa be a készülék az Azure Migrálása kiszolgáló értékelési és a Migrálás Hyper-V virtuális gépek |} A Microsoft Docs
description: Ismerteti, hogyan állítható be a felderítés, értékelési és ügynök nélküli migrálást Hyper-V virtuális gépek Azure Migrate értékelés /-áttelepítés használatával telepíthetőek.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: c531fe49ebff6c021547c2d1c2f382bcd6c9caef
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811764"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Egy készülék beállítása a Hyper-V virtuális gépek

Ez a cikk ismerteti, hogyan állítható be az Azure Migrate berendezés, ha a Hyper-V virtuális gépek értékelése az Azure Migrate Server Assessment eszközzel, vagy a VMware virtuális gépek áttelepítése az Azure-bA az Azure Migrate Server áttelepítési eszköz használata.

A Hyper-V virtuális berendezés egy egyszerűsített berendezés által Azure Migrate Server Assessment és a Migrálás használt tegye a következőket:

- A helyszíni Hyper-V virtuális gépek felderítése.
- Metaadatok és a teljesítmény az adatok elküldése a felderített virtuális gépek az Azure Migrálása kiszolgáló értékelési és a Migrálás.

[További](migrate-appliance.md) tudnivalók az Azure Migrate berendezés.


## <a name="appliance-deployment-steps"></a>Berendezés üzembe helyezés lépései

Beállítása a készülék meg:
- Töltse le a tömörített Hyper-V virtuális merevlemez az Azure Portalról.
- A berendezés létrehozásához, és ellenőrizze, hogy képes csatlakozni az Azure Migrate Server Assessment. 
- Konfigurálja a berendezést először, és regisztrálja az Azure Migrate-projektben.

## <a name="download-the-vhd"></a>Töltse le a VHD-t

Töltse le a Tömörített VHD-sablon a berendezéshez.

1. A **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: Server Assessment**, kattintson a **felderítési**.
2. A **gépek felderítése** > **virtualizáltak a gépek?** , kattintson a **Igen, a Hyper-V**.
3. Kattintson a **letöltése** a VHD-fájl letöltéséhez.

    ![Töltse le a virtuális gép](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Biztonsági ellenőrzése

Ellenőrizze, hogy a zip-fájl biztonságos, az üzembe helyezés előtt.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a kivonat létrehozásához a virtuális merevlemez
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  A berendezés 1.19.05.10 esetén a létrehozott kivonatnak egyeznie kell az ezeket a beállításokat.

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és a virtuális gép létrehozásához.

1. Bontsa ki a Tömörített VHD-fájlt egy mappába a berendezés virtuális Gépet futtató Hyper-V gazdagépen. Három mappa ki kell olvasni.
2. Nyissa meg a Hyper-V kezelőjét. A **műveletek**, kattintson a **virtuális gép importálása**.

    ![Virtuális merevlemez központi telepítése](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. A virtuális gép varázslóban > **megkezdése előtt**, kattintson a **tovább**.
3. A **mappa keresése**, adja meg a kibontott VHD-t tartalmazó mappát. Ezután kattintson a **Next** (Tovább) gombra.
1. A **válassza ki a virtuális gép**, kattintson a **tovább**.
2. A **importálási típus kiválasztása**, kattintson a **virtuális gép másolása (új egyedi azonosító létrehozása)** . Ezután kattintson a **Next** (Tovább) gombra.
3. A **válasszon cél**, ne módosítsa az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
4. A **tárolási mappák**, ne módosítsa az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
5. A **hálózat kiválasztása**, adja meg a virtuális kapcsolót, amelyet a virtuális gép használni fog. A kapcsoló adatok küldése az Azure-bA internetkapcsolattal kell rendelkeznie.
6. A **összefoglalás**, tekintse át a beállításokat. Kattintson a **Befejezés**.
7. A Hyper-V kezelőjében > **virtuális gépek**, indítsa el a virtuális Gépet.


### <a name="verify-appliance-access-to-azure"></a>Az Azure-bA készülék-hozzáférés ellenőrzése

Győződjön meg arról, hogy a berendezés virtuális gép kapcsolódik- [Azure URL-címeinek](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Konfigurálja a berendezést

A berendezés beállítása az első alkalommal.

1. A Hyper-V kezelőjében > **virtuális gépek**, kattintson a jobb gombbal a virtuális gép > **Connect**.
2. Adja meg a berendezés a nyelv, az időzóna és a jelszót.
3. Nyisson meg egy böngészőt, minden olyan gépen, amely a virtuális gép csatlakozhat, és nyissa meg a webalkalmazás URL-címét a készülék: **https://*berendezés neve vagy IP-cím*: 44368**.

   Azt is megteheti megnyithatja az alkalmazás a készülék asztalról az alkalmazás parancsikonjára kattintva.
1. A webalkalmazásban > **Előfeltételek beállítása**, tegye a következőket:
    - **Licenc**: Fogadja el a licencfeltételeket, és olvassa el a külső szolgáltatóval kapcsolatos információkat.
    - **Kapcsolat**: Az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép egy proxyt használja:
        - Kattintson a **proxybeállítások**, és adja meg a proxykiszolgáló címét és figyelőportját, a képernyőn http://ProxyIPAddress vagy http://ProxyFQDN.
        - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
        - Csak a HTTP-proxyk használata támogatott.
    - **Szinkronizálási idő**: Idő ellenőrizve. Lehet, hogy az idő, a készüléken szinkronban tartani az internetes idővel a virtuális gép felderítése megfelelően működjön.
    - **Telepítse a frissítéseket**: Az Azure Migrate Server Assessment ellenőrzi, hogy a berendezés rendelkezik-e a legújabb frissítések.

### <a name="register-the-appliance-with-azure-migrate"></a>A berendezés regisztráljon az Azure Migrate

1. Kattintson a **bejelentkezés**. Ha nem jelenik meg, győződjön meg arról, akkor le lett tiltva az előugró ablakok a böngészőben.
2. Az új lapon jelentkezzen be Azure hitelesítő adatait. 
    - Jelentkezzen be a felhasználónevét és jelszavát.
    - Jelentkezzen be a PIN-kód nem támogatott.
3. Miután sikeresen bejelentkezett, lépjen vissza a webalkalmazás.
4. Válassza ki az előfizetést, amelyben az Azure Migrate-projekt létrehozása. Ezután válassza ki a projektet.
5. Adjon meg egy nevet a berendezéshez. A névnek alfanumerikus karakterek és 14 karakter vagy kevesebb kell lennie.
6. Kattintson a **regisztrálása**.


### <a name="delegate-credentials-for-smb-vhds"></a>Hitelesítő adatok delegálása SMB virtuális merevlemezek

Ha virtuális merevlemezek futtatja az SMB-kben, engedélyeznie kell a Hyper-V-gazdagépek a készülék hitelesítő adatok delegálását. Ehhez a készülék:

1. A virtuális berendezésen a következő parancs futtatásával. HyperVHost1/HyperVHost2 példa állomásnevek.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Azt is megteheti ehhez a a Helyicsoportházirend-szerkesztő a készüléken:
    - A **helyi számítógép-házirend** > **számítógép konfigurációja**, kattintson a **felügyeleti sablonok** > **rendszer**  >  **Hitelesítő adatok delegálása**.
    - Kattintson duplán a **friss hitelesítő adatok delegálásának engedélyezése**, és válassza ki **engedélyezve**.
    - A **beállítások**, kattintson a **megjelenítése**, és adja hozzá a listához, a felderíteni kívánt minden egyes Hyper-V-gazdagép **wsman /** előtagjaként.
    - A **hitelesítő adatok delegálása**, kattintson duplán a **csak NTLM-server-hitelesítéssel friss hitelesítő adatok delegálásának engedélyezése**. Újra, adja hozzá a listához, a felderíteni kívánt minden egyes Hyper-V-gazdagép **wsman /** előtagjaként.

## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

A készülék Hyper-V-gazdagépekhez vagy fürtökhöz csatlakozzon, és indítsa el a virtuális gépek felderítésének.

1. A **felhasználónév** és **jelszó**, adja meg a fiók hitelesítő adatait, hogy a berendezés virtuális gépek felderítéséhez használ majd. Adjon meg egy rövid nevet a hitelesítő adatokat, és kattintson a **részletek mentése**.
2. Kattintson a **Hozzáadás gazdagép**, és adja meg a Hyper-V gazdagép vagy fürt részletes adatai.
3. Kattintson a **ellenőrzése**. Ellenőrzést követően könnyen megtalálhatók legyenek az egyes gazdagépfürtön található virtuális gépek száma látható.
    - Ha az érvényesítés meghiúsul, a gazdagép, tekintse át a hiba az egérmutatót a ikonra a **állapot** oszlop. Hárítsa el a problémákat, és újra ellenőrzi.
    - Gazdagépek és fürtök eltávolításához jelölje ki > **törlése**.
    - Egy adott gazdagép fürtből nem távolítható el. Csak eltávolíthatja a teljes fürtöt.
    - Hozzáadhat egy fürthöz, még akkor is, ha a fürt adott gazdagépeire probléma adódik.
4. Kattintson az ellenőrzést követően **mentéséhez és a felderítés megkezdése** a felderítési folyamat elindításához.

Ez elindítja a felderítést. Az Azure Portalon megjelenő felderített virtuális gépek metaadatait hozzávetőlegesen 15 percet vesz igénybe. 

## <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

Felderítés befejeződése után ellenőrizheti, hogy a virtuális gépek megjelennek-e a portálon.

1. Nyissa meg az Azure Migrate irányítópultot.
2. A **Azure Migrate - kiszolgálók** > **Azure Migrate: Server Assessment** lap, kattintson az ikont, amely megjeleníti a számát, a **felderített kiszolgálók**. 


## <a name="next-steps"></a>További lépések

Próbálja ki [Hyper-V-értékelés](tutorial-assess-hyper-v.md) az Azure Migrate Server Assessment.