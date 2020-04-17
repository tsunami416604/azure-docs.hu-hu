---
title: Azure Migrate-berendezés beállítása parancsfájllal
description: Ismerje meg, hogyan állíthat be egy Azure Migrate-berendezést parancsfájllal
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: faed7f96ea8c1850af5523d35f9f891011a48df8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537712"
---
# <a name="set-up-an-appliance-with-a-script"></a>Készülék beállítása parancsfájllal

Ez a cikk ismerteti, hogyan állíthatja be az [Azure Migrate-készülék](deploy-appliance.md) et egy PowerShell telepítő parancsfájl, a VMware virtuális gépek és a Hyper-V virtuális gépek használatával. Ha fizikai kiszolgálókra szeretné beállítani a készüléket, [tekintse át ezt a cikket.](how-to-set-up-appliance-physical.md)


A készüléket néhány módszerrel telepítheti:


- VMware virtuális gépek (OVA) vagy Hyper-V vMs (VHD) sablon használata.
- Egy szkriptet használ. Ez a cikkben ismertetett módszer. A szkript a következőket tartalmazza:
    - A készülék OVA sablon használatával történő beállításának alternatívája a VMware virtuális gépek értékeléséhez és ügynök nélküli áttelepítéséhez.
    - A készülék virtuális merevlemez-sablon használatával történő beállításának alternatívája a Hyper-V virtuális gépek értékeléséhez és áttelepítéséhez.
    - Fizikai kiszolgálók (vagy fizikai kiszolgálóként áttelepíteni kívánt virtuális gépek) értékeléséhez a parancsfájl az egyetlen módszer a készülék beállításához.
    - A készülék üzembe helyezésének egyik módja az Azure Governmentben.


A készülék létrehozása után ellenőrizze, hogy tud-e csatlakozni az Azure Migrate.After creating the appliance, you verify that it can connect to Azure Migrate. Ezután konfigurálja a készüléket az első alkalommal, és regisztrálja azt az Azure Migrate projekt.


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
    - Példa nyilvános felhőhasználatára:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
    - Példa használati kormányzati felhő:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip```

3. Ellenőrizze a létrehozott kivonatoló értékeket:

    - A nyilvános felhőhöz (a készülék legújabb verziójához):

        **Algoritmus** | **Kivonat értéke**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - Az Azure-kormány (a legújabb készülékverzió):

        **Algoritmus** | **Kivonat értéke**
          --- | ---
          MD5 | 6316bcc8bc932204295bfe33f4be3949
          

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
    - A nyilvános felhő esetében:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
    - Az Azure Government esetében:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
   
5. Miután a parancsfájl sikeresen fut, elindítja a készülék webalkalmazást, így beállíthatja a készüléket. Ha bármilyen problémát tapasztal, tekintse át a parancsfájlnaplókat a C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log mappában.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a készülék csatlakozhat az Azure URL-címek [nyilvános](migrate-appliance.md#public-cloud-urls) és [kormányzati felhők](migrate-appliance.md#government-cloud-url-ek.


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
    - Példa nyilvános felhőhasználatára:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
    - Példa használati kormányzati felhő:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. Ellenőrizze a létrehozott kivonatoló értékeket:

    - A nyilvános felhőhöz (a készülék legújabb verziójához):

        **Algoritmus** | **Kivonat értéke**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - Az Azure-kormány (a legújabb készülékverzió):

        **Algoritmus** | **Kivonat értéke**
          --- | ---
          MD5 | 717f8b9185f565006b5aff0215ecadac
          

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
    - A nyilvános felhő esetében:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
    - Az Azure Government esetében:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ```
   
5. Miután a parancsfájl sikeresen fut, elindítja a készülék webalkalmazást, így beállíthatja a készüléket. Ha bármilyen problémát tapasztal, tekintse át a parancsfájlnaplókat a C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log mappában.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a készülék csatlakozhat az Azure URL-címek [nyilvános](migrate-appliance.md#public-cloud-urls) és [kormányzati felhők](migrate-appliance.md#government-cloud-url-ek.



## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni arról, hogy hogyan kell sablonnal vagy fizikai kiszolgálókkal felállításra, tekintse át az alábbi cikkeket:

- Állítsa be a készüléket a [VMware számára](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Állítsa be a készüléket a [Hyper-V-hez.](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)
- Állítsa be a készüléket [fizikai kiszolgálókhoz](how-to-set-up-appliance-physical.md).