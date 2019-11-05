---
title: Berendezés beállítása a fizikai kiszolgálók értékeléséhez Azure Migrate Server Assessment
description: Ismerteti, hogyan állíthat be egy készüléket a fizikai kiszolgálók értékeléséhez Azure Migrate Server Assessment használatával.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/21/2019
ms.author: raynew
ms.openlocfilehash: ddd659e8cbcb54a36868848d0c6327f294d531b1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512625"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Berendezés beállítása fizikai kiszolgálókhoz

Ez a cikk azt ismerteti, hogyan állíthatja be a Azure Migrate berendezést, ha a fizikai kiszolgálókat a Azure Migrate: Server Assessment Tool eszközzel értékeli.

A Azure Migrate berendezés egy könnyű berendezés, amelyet Azure Migrate Server Assessment használ a következők elvégzéséhez:

- Helyszíni kiszolgálók felderítése.
- Metaadatok és teljesítményadatok küldése a felderített kiszolgálókhoz Azure Migrate Server Assessment.

[További](migrate-appliance.md) információ az Azure Migrate készülékről.


## <a name="appliance-deployment-steps"></a>Berendezések üzembe helyezésének lépései

A készülék beállítása:
- Töltse le a Azure Migrate telepítő parancsfájlt tartalmazó tömörített fájlt a Azure Portal.
- Bontsa ki a tömörített fájl tartalmát. Indítsa el a PowerShell-konzolt rendszergazdai jogosultságokkal.
- Futtassa a PowerShell-szkriptet a berendezés webalkalmazásának elindításához.
- Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel.

## <a name="download-the-installer-script"></a>A telepítő parancsfájl letöltése

Töltse le a készülék tömörített fájlját.

1. Az **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: kiszolgáló értékelése**területen kattintson a **felderítés**gombra.
2. A számítógépek **felderítése** > a **gépek virtualizáltak?** kattintson a **nem virtualizált/egyéb**elemre.
3. A tömörített fájl letöltéséhez kattintson a **Letöltés** gombra.

    ![Virtuális gép letöltése](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a virtuális merevlemez kivonatának létrehozásához.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  A készülék verziójának 1.19.05.10 a generált kivonatnak meg kell egyeznie ezekkel a beállításokkal.

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="run-the-azure-migrate-installer-script"></a>A Azure Migrate telepítő parancsfájl futtatása
= A telepítő parancsfájl a következő műveleteket végzi el:

- Ügynököket és webalkalmazásokat telepít a fizikai kiszolgálók felderítéséhez és értékeléséhez.
- Telepítse a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Töltse le és telepítse az IIS újraírható modulját. [Részletek](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a (HKLM) beállításkulcsot a Azure Migrate állandó beállítási részleteivel.
- A következő fájlokat hozza létre az elérési út alatt:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

Futtassa a szkriptet a következő módon:

1. Bontsa ki a tömörített fájlt egy olyan mappába a kiszolgálón, amely a készüléket fogja üzemeltetni.
2. Indítsa el a PowerShellt a fenti kiszolgálón rendszergazdai (emelt szintű) jogosultsággal.
3. Módosítsa a PowerShell könyvtárat arra a mappára, ahol a rendszer kibontotta a tartalmat a letöltött tömörített fájlból.
4. Futtassa a szkriptet a következő parancs futtatásával:
    ```
    PS C:\Users\Administrators\Desktop> AzureMigrateInstaller-physical.ps1
    ```
A szkript a készülék webalkalmazásának sikeres befejeződése után elindítja a készüléket.



### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gépe csatlakozni tud a szükséges [Azure URL-címekhez](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

1. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást az asztalról az alkalmazás parancsikonra kattintva.
2. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
    - **Licenc**: fogadja el a licencfeltételeket, és olvassa el a harmadik féltől származó információkat.
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
        - Kattintson a **Proxybeállítások**elemre, és határozza meg a proxy címe és a figyelő portját http://ProxyIPAddress vagy http://ProxyFQDNformában.
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


## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Kapcsolódjon a készülékről a fizikai kiszolgálókhoz, és indítsa el a felderítést.

1. Kattintson a **hitelesítő adatok hozzáadása** elemre azon fiók hitelesítő adatainak megadásához, amelyet a berendezés a kiszolgálók felderítéséhez használ majd.  
2. Adja meg az **operációs rendszert**, a hitelesítő adatok, a **Felhasználónév** és a **jelszó** felhasználóbarát nevét, majd kattintson a **Hozzáadás**gombra.
A Windows-és Linux-kiszolgálókhoz is hozzáadhat egy hitelesítő adatot.
4. Kattintson a **kiszolgáló hozzáadása**elemre, és adja meg a kiszolgáló adatait – FQDN/IP-cím és a hitelesítő adatok rövid neve (egy sor, soronként egy bejegyzés) a kiszolgálóhoz való kapcsolódáshoz.
3. Kattintson az **Érvényesítés**elemre. Az ellenőrzés után megjelenik a felderíthető kiszolgálók listája.
    - Ha egy kiszolgáló érvényesítése meghiúsul, tekintse át a hibát az **állapot** oszlopban látható ikon fölé helyezve. Javítsa ki a hibákat, és ismételje meg az érvényesítést.
    - Kiszolgáló eltávolításához válassza a > **Törlés**lehetőséget.
4. Az ellenőrzés után kattintson a Mentés gombra, **és indítsa** el a felderítést a felderítési folyamat elindításához.

Ez elindítja a felderítést. Körülbelül 15 percet vesz igénybe, hogy a felderített virtuális gépek metaadatai megjelenjenek a Azure Portalban. 

## <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

A felderítés befejeződése után ellenőrizheti, hogy a kiszolgálók megjelennek-e a portálon.

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-** servers > **Azure Migrate: kiszolgáló értékelése** lapon kattintson arra az ikonra, amely megjeleníti a **felderített kiszolgálók**darabszámát. 


## <a name="next-steps"></a>További lépések

Próbálja ki a Azure Migrate Server Assessment [fizikai kiszolgálók értékelését](tutorial-assess-physical.md) .
