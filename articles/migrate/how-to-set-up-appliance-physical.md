---
title: Azure Migrate berendezés beállítása fizikai kiszolgálókhoz
description: Megtudhatja, hogyan állíthat be Azure Migrate készüléket a fizikai kiszolgáló értékeléséhez.
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: 6d9cc071ad5d81a09a14b12fe2acdf564c2ea6c8
ms.sourcegitcommit: 79508e58c1f5c58554378497150ffd757d183f30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84331780"
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

1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderítés**gombra.
2. A **felderítési gépeken**a  >  **gépek virtualizáltak?** kattintson a **nem virtualizált/egyéb**elemre.
3. A tömörített fájl letöltéséhez kattintson a **Letöltés** gombra.

    ![Virtuális gép letöltése](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Példa a nyilvános felhő használatára:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Példa kormányzati felhő használatára:```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Ellenőrizze a készülék legújabb verzióját és a kivonatoló értékeket:
 
    - Nyilvános felhő esetén:

        **Forgatókönyv** | **Letöltés*** | **Kivonat értéke**
        --- | --- | ---
        Fizikai (63,1 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0

    - Azure Government esetén:

        **Forgatókönyv** | **Letöltés*** | **Kivonat értéke**
        --- | --- | ---
        Fizikai (63,1 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1


## <a name="run-the-azure-migrate-installer-script"></a>A Azure Migrate telepítő parancsfájl futtatása
A telepítő parancsfájl a következő műveleteket végzi el:

- Ügynököket és webalkalmazásokat telepít a fizikai kiszolgálók felderítéséhez és értékeléséhez.
- Telepítse a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Töltse le és telepítse az IIS újraírható modulját. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a (HKLM) beállításkulcsot a Azure Migrate állandó beállítási részleteivel.
- A következő fájlokat hozza létre az elérési út alatt:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

Futtassa a szkriptet a következő módon:

1. Bontsa ki a tömörített fájlt egy olyan mappába a kiszolgálón, amely a készüléket fogja üzemeltetni.  Győződjön meg arról, hogy nem futtatja a parancsfájlt egy meglévő Azure Migrate berendezésen lévő gépen.
2. Indítsa el a PowerShellt a fenti kiszolgálón rendszergazdai (emelt szintű) jogosultsággal.
3. Módosítsa a PowerShell könyvtárat arra a mappára, ahol a rendszer kibontotta a tartalmat a letöltött tömörített fájlból.
4. Futtassa a **AzureMigrateInstaller. ps1** nevű szkriptet a következő parancs futtatásával:

    - Nyilvános felhő esetén:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Azure Government esetén:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    A szkript a készülék webalkalmazásának sikeres befejeződése után elindítja a készüléket.

Ha bármilyen probléma merül fel, a parancsfájl-naplókat a C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log naplófájlban érheti el a hibaelhárításhoz.



### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gépe tud csatlakozni az Azure URL-címekhez a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhők számára.

## <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

1. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást az asztalról az alkalmazás parancsikonra kattintva.
2. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
    - **Licenc**: fogadja el a licencfeltételeket, és olvassa el a harmadik féltől származó információkat.
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
        - Kattintson a proxybeállítások elemre, és írja be a proxy címe és a figyelő portját az űrlap vagy a **értékre** http://ProxyIPAddress http://ProxyFQDN .
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
6. Kattintson a **Regisztrálás** parancsra.


## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Kapcsolódjon a készülékről a fizikai kiszolgálókhoz, és indítsa el a felderítést.

1. Kattintson a **hitelesítő adatok hozzáadása** elemre azon fiók hitelesítő adatainak megadásához, amelyet a berendezés a kiszolgálók felderítéséhez használ majd.  
2. Adja meg az **operációs rendszert**, a hitelesítő adatok rövid nevét, valamint a felhasználónevet és a jelszót. Ezután kattintson az **Add** (Hozzáadás) gombra.
A Windows-és Linux-kiszolgálókhoz is hozzáadhat egy hitelesítő adatot.
4. Kattintson a **kiszolgáló hozzáadása**elemre, és adja meg a kiszolgáló adatait – FQDN/IP-cím és a hitelesítő adatok rövid neve (egy sor, soronként egy bejegyzés) a kiszolgálóhoz való kapcsolódáshoz.
3. Kattintson a **Validate** (Érvényesítés) elemre. Az ellenőrzés után megjelenik a felderíthető kiszolgálók listája.
    - Ha egy kiszolgáló érvényesítése meghiúsul, tekintse át a hibát az **állapot** oszlopban látható ikon fölé helyezve. Javítsa ki a hibákat, és ismételje meg az érvényesítést.
    - Kiszolgáló eltávolításához válassza a > **Törlés**lehetőséget.
4. Az ellenőrzés után kattintson a Mentés gombra, **és indítsa** el a felderítést a felderítési folyamat elindításához.

Ez elindítja a felderítést. Körülbelül 15 percet vesz igénybe, hogy a felderített virtuális gépek metaadatai megjelenjenek a Azure Portalban.

## <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

A felderítés befejeződése után ellenőrizheti, hogy a kiszolgálók megjelennek-e a portálon.

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-Servers**  >  **Azure Migrate: kiszolgáló értékelése** lapon kattintson arra az ikonra, amely megjeleníti a **felderített kiszolgálók**darabszámát.


## <a name="next-steps"></a>Következő lépések

Próbálja ki a Azure Migrate Server Assessment [fizikai kiszolgálók értékelését](tutorial-assess-physical.md) .
