---
title: Azure Migrate-berendezés beállítása parancsfájllal
description: Ismerje meg, hogyan állíthat be egy Azure Migrate-berendezést parancsfájllal
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 0c4d85909bbfa623b5ad8590e973250474d9d95a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676315"
---
# <a name="set-up-an-appliance-with-a-script"></a>Készülék beállítása parancsfájllal

Ebből a cikkből hozzon létre egy [Azure Migrate-berendezést](deploy-appliance.md) a VMware virtuális gépek és a Hyper-V virtuális gépek értékeléséhez/áttelepítéséhez. Egy berendezés létrehozásához futtategy parancsfájlt, és ellenőrizze, hogy képes-e csatlakozni az Azure-hoz. 

A vmware-hez és a Hyper-V virtuális gépekhez készült berendezést parancsfájl vagy az Azure Portalról letöltött sablon használatával telepítheti. Parancsfájl használata akkor hasznos, ha nem tudja létrehozni a virtuális gép a letöltött sablon használatával.

- Sablon használatához kövesse a [VMware](tutorial-prepare-vmware.md) vagy a [Hyper-V](tutorial-prepare-hyper-v.md)oktatóanyagait.
- Ha fizikai kiszolgálókhoz szeretne beállítani egy készüléket, csak parancsfájlt használhat. Kövesse [ezt a cikket](how-to-set-up-appliance-physical.md).
- Ha egy azure-beli felhőben szeretne beállítani egy készüléket, kövesse [ezt a cikket.](deploy-appliance-script-government.md)

## <a name="prerequisites"></a>Előfeltételek

A parancsfájl beállítja az Azure Migrate-berendezés egy meglévő fizikai gép vagy virtuális gép.

- A készülékként működő gépnek Windows Server 2016-ot kell futtatnia, 32 GB memóriával, nyolc vCPU-val, körülbelül 80 GB lemeztárolóval és egy külső virtuális kapcsolóval. Statikus vagy dinamikus IP-címet és internet-hozzáférést igényel.
- A készülék üzembe helyezése előtt tekintse át a [vmware virtuális gépekre,](migrate-appliance.md#appliance---vmware) [a hyper-v-vm-ekre](migrate-appliance.md#appliance---hyper-v)és a [fizikai kiszolgálókra](migrate-appliance.md#appliance---physical)vonatkozó részletes berendezési követelményeket.
- Ne futtassa a parancsfájlt egy meglévő Azure Migrate-berendezésen.

## <a name="set-up-the-appliance-for-vmware"></a>A készülék beállítása vmware-hez

A készülék beállítása a VMware letöltegy tömörített fájlt az Azure Portalon, és kibontja a tartalmát. A PowerShell-parancsfájl futtatásával indítsa el a készülék webapp. Először állíthatja be és konfigurálja a készüléket. Ezután regisztrálja a készüléket az Azure Migrate projekttel.

### <a name="download-the-script"></a>A szkript letöltése

1.  Az Azure Migrate: Server Assessment **(Áttelepítés:** > **Kiszolgálófelmérés)****alkalmazásban** > kattintson a **Felderítés**gombra.
2.  A **Discover machines** > **Are Your machines virtualizált?**, válassza az **Igen, a VMWare vSphere hipervizor**.
3.  A tömörített fájl letöltéséhez kattintson a **Letöltés**gombra. 


### <a name="verify-file-security"></a>A fájlbiztonság ellenőrzése

A tömörített fájl telepítése előtt ellenőrizze, hogy a tömörített fájl biztonságos-e.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. A következő parancs futtatása a tömörített fájl kivonatának létrehozásához
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Például: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Ellenőrizze a létrehozott kivonatoló értéket. A készülék legújabb verziójához:

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c



### <a name="run-the-script"></a>A szkript futtatása

Itt van, amit a forgatókönyvet csinál:

- Ügynökök és egy webalkalmazás telepítése.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letöltése és telepítése egy IIS újraírható modul. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a beállításkulcsot (HKLM), az Azure Migrate állandó beállításaival.
- Napló- és konfigurációs fájlokat hoz létre az alábbiak szerint:
    - **Konfigurációs fájlok**: %ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**: %ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt a készüléket tartalmazó mappába. Győződjön meg arról, hogy nem futtatja a parancsfájlt egy számítógépen egy meglévő Azure Migrate-berendezésen.
2. Indítsa el a PowerShellt a számítógépen rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kinyert tartalmat tartalmazza.
4. Futtassa az **AzureMigrateInstaller.ps1**parancsfájlt az alábbiak szerint:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. Miután a parancsfájl sikeresen fut, elindítja a készülék webalkalmazást, így beállíthatja a készüléket. Ha bármilyen problémát tapasztal, tekintse át a parancsfájlnaplókat a C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log mappában.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a készülék csatlakozhat az Azure-URL-ek a [nyilvános](migrate-appliance.md#public-cloud-urls) felhőben.

## <a name="set-up-the-appliance-for-hyper-v"></a>Állítsa be a készüléket a Hyper-V-hez

A hyper-V-hez a készülék beállítása egy tömörített fájlt tölt le az Azure Portalról, és bontsa ki a tartalmat. A PowerShell-parancsfájl futtatásával indítsa el a készülék webapp. Először állíthatja be és konfigurálja a készüléket. Ezután regisztrálja a készüléket az Azure Migrate projekttel.

### <a name="download-the-script"></a>A szkript letöltése

1.  Az Azure Migrate: Server Assessment **(Áttelepítés:** > **Kiszolgálófelmérés)****alkalmazásban** > kattintson a **Felderítés**gombra.
2.  A **Discover machines** > **Are your machines virtualizált?** lehetőséget **Igen, a Hyper-V**.
3.  A tömörített fájl letöltéséhez kattintson a **Letöltés**gombra. 


### <a name="verify-file-security"></a>A fájlbiztonság ellenőrzése

A tömörített fájl telepítése előtt ellenőrizze, hogy a tömörített fájl biztonságos-e.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. A következő parancs futtatása a tömörített fájl kivonatának létrehozásához
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Például: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Ellenőrizze a létrehozott kivonatoló értékeket. A készülék legújabb verziójához:

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

### <a name="run-the-script"></a>A szkript futtatása

Itt van, amit a forgatókönyvet csinál:

- Ügynökök és egy webalkalmazás telepítése.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letöltése és telepítése egy IIS újraírható modul. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a beállításkulcsot (HKLM), az Azure Migrate állandó beállításaival.
- Napló- és konfigurációs fájlokat hoz létre az alábbiak szerint:
    - **Konfigurációs fájlok**: %ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**: %ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt a készüléket tartalmazó mappába. Győződjön meg arról, hogy nem futtatja a parancsfájlt egy számítógépen egy meglévő Azure Migrate-berendezésen.
2. Indítsa el a PowerShellt a számítógépen rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kinyert tartalmat tartalmazza.
4. Futtassa az **AzureMigrateInstaller.ps1**parancsfájlt az alábbiak szerint:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Miután a parancsfájl sikeresen fut, elindítja a készülék webalkalmazást, így beállíthatja a készüléket. Ha bármilyen problémát tapasztal, tekintse át a parancsfájlnaplókat a C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log mappában.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a készülék csatlakozhat az Azure-URL-ek a [nyilvános](migrate-appliance.md#public-cloud-urls) felhőben.

## <a name="next-steps"></a>További lépések

A készülék üzembe helyezése után először konfigurálnia kell, és regisztrálnia kell az Azure Migrate projekttel.

- Állítsa be a készüléket a [VMware számára](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Állítsa be a készüléket a [Hyper-V-hez.](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)
