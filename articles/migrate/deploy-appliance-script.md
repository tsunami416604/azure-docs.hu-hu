---
title: Azure Migrate berendezés beállítása parancsfájlokkal
description: Megtudhatja, hogyan állíthat be egy Azure Migrate készüléket parancsfájl használatával
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 0c4d85909bbfa623b5ad8590e973250474d9d95a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676315"
---
# <a name="set-up-an-appliance-with-a-script"></a>Berendezés beállítása parancsfájlsal

Ez a cikk a VMware virtuális gépek és a Hyper-V virtuális gépek értékeléséhez/áttelepítéséhez [Azure Migrate berendezés](deploy-appliance.md) létrehozásához nyújt segítséget. Parancsfájl futtatásával létrehozhat egy berendezést, és ellenőrizheti, hogy tud-e csatlakozni az Azure-hoz. 

A készüléket a VMware és a Hyper-V virtuális gépekhez parancsfájl használatával, vagy a Azure Portalból letöltött sablon használatával telepítheti. A szkriptek használata akkor hasznos, ha nem tud virtuális gépet létrehozni a letöltött sablonnal.

- A sablonok használatához kövesse a [VMware](tutorial-prepare-vmware.md) vagy a [Hyper-V](tutorial-prepare-hyper-v.md)oktatóanyagokat.
- Ha fizikai kiszolgálókon szeretné beállítani a készüléket, csak szkripteket használhat. Kövesse [ezt a cikket](how-to-set-up-appliance-physical.md).
- Ha Azure Government felhőben szeretné beállítani a készüléket, kövesse [ezt a cikket](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Előfeltételek

A szkript beállítja a Azure Migrate készüléket egy meglévő fizikai gépre vagy virtuális gépre.

- A készülékként működni kívánó gépnek Windows Server 2016, 32 GB memóriával, nyolc vCPU, körülbelül 80 GB lemezterülettel és külső virtuális kapcsolóval kell futnia. Statikus vagy dinamikus IP-címet igényel, és hozzáférést biztosít az internethez.
- A készülék központi telepítése előtt tekintse át a [VMWare virtuális gépek](migrate-appliance.md#appliance---vmware), a [Hyper-V virtuális gépek](migrate-appliance.md#appliance---hyper-v)és a [fizikai kiszolgálók](migrate-appliance.md#appliance---physical)részletes készülékre vonatkozó követelményeit.
- Ne futtassa a parancsfájlt egy meglévő Azure Migrate berendezésen.

## <a name="set-up-the-appliance-for-vmware"></a>A készülék beállítása a VMware-hez

A készülék VMware-hez való beállításához töltse le a tömörített fájlt a Azure Portalból, és bontsa ki a tartalmat. Futtatja a PowerShell-szkriptet a készülék webalkalmazásának elindításához. Állítsa be a készüléket, és konfigurálja az első alkalommal. Ezután regisztrálja a készüléket a Azure Migrate projekttel.

### <a name="download-the-script"></a>A parancsfájl letöltése

1.  Az **áttelepítési célok** > **kiszolgálói** > **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderítés**gombra.
2.  A **felderítési gépek** > a**gépek virtualizáltak?** területen válassza **az igen, a VMware vSphere hypervisor**lehetőséget.
3.  A tömörített fájl letöltéséhez kattintson a **Letöltés**gombra. 


### <a name="verify-file-security"></a>A fájl biztonságának ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Például: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Ellenőrizze a generált kivonatoló értéket. A készülék legújabb verziójához:

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c



### <a name="run-the-script"></a>A szkript futtatása

A szkript a következőket teszi:

- Ügynököket és webalkalmazásokat telepít.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letölti és telepíti az IIS újraírható modulját. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Egy beállításkulcs (HKLM) frissítése a Azure Migrate állandó beállításaival.
- A a következőképpen hozza létre a naplófájlokat és a konfigurációs fájlokat:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt egy olyan mappába a gépen, amely a készüléket fogja üzemeltetni. Győződjön meg arról, hogy nem futtatja a parancsfájlt egy meglévő Azure Migrate berendezésen lévő gépen.
2. Indítsa el a PowerShellt a gépen, rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kinyert tartalmat tartalmazza.
4. Futtassa a **AzureMigrateInstaller. ps1**parancsfájlt a következő módon:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. A szkript sikeres futtatása után elindítja a berendezés webalkalmazását, hogy beállítsa a készüléket. Ha bármilyen problémába ütközik, tekintse át a szkriptek naplóit a következő helyen: C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a készülék képes csatlakozni a [nyilvános](migrate-appliance.md#public-cloud-urls) felhőhöz tartozó Azure URL-címekhez.

## <a name="set-up-the-appliance-for-hyper-v"></a>A készülék beállítása a Hyper-V-hez

A Hyper-V berendezésének beállításához töltse le a tömörített fájlt a Azure Portalból, és bontsa ki a tartalmat. Futtatja a PowerShell-szkriptet a készülék webalkalmazásának elindításához. Állítsa be a készüléket, és konfigurálja az első alkalommal. Ezután regisztrálja a készüléket a Azure Migrate projekttel.

### <a name="download-the-script"></a>A parancsfájl letöltése

1.  Az **áttelepítési célok** > **kiszolgálói** > **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderítés**gombra.
2.  A **felderítési gépek** > a**gépek virtualizáltak?** területen válassza **az igen, a Hyper-V**lehetőséget.
3.  A tömörített fájl letöltéséhez kattintson a **Letöltés**gombra. 


### <a name="verify-file-security"></a>A fájl biztonságának ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Például: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Ellenőrizze a generált kivonatoló értékeket. A készülék legújabb verziójához:

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

### <a name="run-the-script"></a>A szkript futtatása

A szkript a következőket teszi:

- Ügynököket és webalkalmazásokat telepít.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letölti és telepíti az IIS újraírható modulját. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Egy beállításkulcs (HKLM) frissítése a Azure Migrate állandó beállításaival.
- A a következőképpen hozza létre a naplófájlokat és a konfigurációs fájlokat:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt egy olyan mappába a gépen, amely a készüléket fogja üzemeltetni. Győződjön meg arról, hogy nem futtatja a parancsfájlt egy meglévő Azure Migrate berendezésen lévő gépen.
2. Indítsa el a PowerShellt a gépen, rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kinyert tartalmat tartalmazza.
4. Futtassa a **AzureMigrateInstaller. ps1**parancsfájlt a következő módon:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. A szkript sikeres futtatása után elindítja a berendezés webalkalmazását, hogy beállítsa a készüléket. Ha bármilyen problémába ütközik, tekintse át a szkriptek naplóit a következő helyen: C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a készülék képes csatlakozni a [nyilvános](migrate-appliance.md#public-cloud-urls) felhőhöz tartozó Azure URL-címekhez.

## <a name="next-steps"></a>További lépések

A készülék üzembe helyezése után először be kell állítania, majd regisztrálnia kell a Azure Migrate projektben.

- Állítsa be a készüléket a [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance)-hez.
- Állítsa be a készüléket a [Hyper-V-](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)hez.
