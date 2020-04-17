---
title: Azure Migrate-berendezés beállítása fizikai kiszolgálókhoz
description: Ismerje meg, hogyan állíthat be egy Azure Migrate-berendezést fizikai kiszolgáló-felméréshez.
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: ddc70ee9430d3a767ce01191824c150a4dbd5e6f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538273"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Készülék beállítása fizikai kiszolgálókhoz

Ez a cikk ismerteti, hogyan állíthatja be az Azure Migrate készüléket, ha fizikai kiszolgálókat értékel az Azure Migrate: Server Assessment eszközzel.

Az Azure Migrate készülék egy könnyű berendezés, amelyet az Azure Migrate Server Assessment a következőkre használ:

- Fedezze fel a helyszíni kiszolgálókat.
- A felderített kiszolgálók metaadatait és teljesítményadatait küldheti el az Azure Migrate Server Assessment szolgáltatásba.

[További információ](migrate-appliance.md) az Azure Migrate appliance.


## <a name="appliance-deployment-steps"></a>A berendezés üzembe helyezésének lépései

A készülék beállításához:
- Töltsön le egy tömörített fájlt az Azure Migrate telepítőparancsfájljával az Azure Portalról.
- Bontsa ki a tartalmát a tömörített fájlból. Indítsa el a PowerShell-konzolrendszergazdai jogosultságokkal.
- Hajtsa végre a PowerShell-parancsfájlt a készülék webalkalmazás elindításához.
- Konfigurálja a készüléket az első alkalommal, és regisztrálja az Azure Migrate projekt.

## <a name="download-the-installer-script"></a>A telepítőparancsfájl letöltése

Töltse le a készülék tömörített fájlját.

1. Az Azure Migrate: Server Assessment **(Áttelepítés:** > **Kiszolgálófelmérés)****alkalmazásban** > kattintson a **Felderítés**gombra.
2. A **Discover machines** > **Are your machines virtualizált?**, kattintson a Nem **virtualizált/ Egyéb gombra.**
3. A **tömörített** fájl letöltéséhez kattintson a Letöltés gombra.

    ![Virtuális gép letöltése](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>A biztonság ellenőrzése

A tömörített fájl telepítése előtt ellenőrizze, hogy a tömörített fájl biztonságos-e.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. A tömörített fájl kivonatának létrehozásához futtassa a következő parancsot:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Példa nyilvános felhőhasználatára:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Példa használati kormányzati felhő:```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Kivonatértékek ellenőrzése:
 
    - A nyilvános felhőhöz (a készülék legújabb verziójához):

        **Algoritmus** | **Kivonat értéke**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - Az Azure-kormány (a legújabb készülékverzió):

        **Algoritmus** | **Kivonat értéke**
          --- | ---
          MD5 | f81c155fc4a1409901caea948713913f


## <a name="run-the-azure-migrate-installer-script"></a>Az Azure Migrate telepítőparancsfájljának futtatása
A telepítő parancsfájl jaa következő:

- Ügynökök és egy webalkalmazás telepítése a fizikai kiszolgáló felderítéséhez és értékeléséhez.
- Telepítse a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Az IIS újraírható moduljának letöltése és telepítése. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a beállításkulcsot (HKLM) az Azure Migrate állandó beállításrészleteivel.
- A következő fájlokat hozza létre az elérési út alatt:
    - **Konfigurációs fájlok**: %Programdata%\Microsoft Azure\Config
    - **Naplófájlok**: %Programdata%\Microsoft Azure\Logs

Futtassa a parancsfájlt az alábbiak szerint:

1. Bontsa ki a tömörített fájlt a kiszolgálón lévő, a készüléket fogadó mappába.  Győződjön meg arról, hogy nem futtatja a parancsfájlt egy számítógépen egy meglévő Azure Migrate-berendezésen.
2. Indítsa el a PowerShellt a fenti kiszolgálón rendszergazdai (emelt szintű) jogosultsággal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, ahol a tartalom kilett nyerve a letöltött tömörített fájlból.
4. Futtassa az **AzureMigrateInstaller.ps1** nevű parancsfájlt a következő parancs futtatásával:

    - A nyilvános felhő esetében:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Az Azure Government esetében:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    A parancsfájl elindítja a készülék webalkalmazását, amint az sikeresen befejeződik.

Ha bármilyen probléma merül fel, a parancsfájlnaplókat a C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log oldalon érheti el hibaelhárítási célokra.



### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-hoz való hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gép csatlakozhat az Azure-URL-címek [nyilvános](migrate-appliance.md#public-cloud-urls) és [kormányzati](migrate-appliance.md#government-cloud-urls) felhők.

## <a name="configure-the-appliance"></a>A készülék konfigurálása

Először állítsa be a készüléket.

1. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozni tud a virtuális géphez, és nyissa meg a készülék webalkalmazásÁNAK URL-címét: **https:// készülék nevét vagy*IP-címét:* 44368**.

   Az alkalmazás parancsikonjára kattintva az alkalmazás parancsikonjára kattintva is megnyithatja az alkalmazást.
2. A webalkalmazásban > **Az előfeltételek beállítása**beállításhoz tegye a következőket:
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


## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Csatlakozzon a készülékről a fizikai kiszolgálókhoz, és indítsa el a felderítést.

1. Kattintson a **Hitelesítő adatok hozzáadása gombra** a kiszolgálók felderítéséhez használt fiókhitelesítő adatok megadásához.  
2. Adja meg az **operációs rendszert**, a hitelesítő adatok rövid nevét, valamint a felhasználónevet és a jelszót. Ezután kattintson az **Add** (Hozzáadás) gombra.
Windows- és Linux-kiszolgálókhoz egy-egy hitelesítő adatot adhat hozzá.
4. Kattintson **a Kiszolgáló hozzáadása**gombra, és adja meg a kiszolgáló adatait – FQDN/IP-cím és a hitelesítő adatok rövid neve (soronként egy bejegyzés) a kiszolgálóhoz való csatlakozáshoz.
3. Kattintson a **Validate** (Érvényesítés) elemre. Az ellenőrzés után megjelenik a felderíthető kiszolgálók listája.
    - Ha egy kiszolgáló érvényesítése sikertelen, tekintse át a hibát úgy, hogy az **Állapot** oszlopban az ikonra viszi az egérmutatót. Javítsa ki a problémákat, és ellenőrizze újra.
    - Kiszolgáló eltávolításához válassza a > **törlés**lehetőséget.
4. Az ellenőrzés után kattintson a Mentés gombra, és indítsa el a **felderítést** a felderítési folyamat elindításához.

Ez elindítja a felderítést. Körülbelül 15 percet vesz igénybe, amíg a felderített virtuális gépek metaadatai megjelennek az Azure Portalon.

## <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

A felderítés befejezése után ellenőrizheti, hogy a kiszolgálók megjelennek-e a portálon.

1. Nyissa meg az Azure Migrate irányítópultot.
2. Az **Azure Migrate – Servers** > **Azure Migrate: Server Assessment (Kiszolgálói értékelés)** lapon kattintson a **felderített kiszolgálók**számát megjelenítő ikonra.


## <a name="next-steps"></a>További lépések

Próbálja ki [a fizikai kiszolgálók értékelését az](tutorial-assess-physical.md) Azure Migrate Server Assessment segítségével.
