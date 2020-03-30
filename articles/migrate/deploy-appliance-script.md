---
title: Azure Migrate-berendezés beállítása parancsfájllal
description: Ismerje meg, hogyan állíthat be egy Azure Migrate-berendezést parancsfájllal
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: bf8d7148f685d4ac6a5f33603020a0503b0c62e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337687"
---
# <a name="set-up-an-appliance-with-a-script"></a>Készülék beállítása parancsfájllal

Ez a cikk ismerteti, hogyan állíthatja be az [Azure Migrate-készülék](deploy-appliance.md) et egy PowerShell telepítő parancsfájl használatával.

A szkript a következőket tartalmazza:
- A készülék OVA sablon használatával történő beállításának alternatívája a VMware virtuális gépek értékeléséhez és ügynök nélküli áttelepítéséhez.
- A készülék virtuális merevlemez-sablon használatával történő beállításának alternatívája a Hyper-V virtuális gépek értékeléséhez és áttelepítéséhez.
- Fizikai kiszolgálók (vagy fizikai kiszolgálóként áttelepíteni kívánt virtuális gépek) értékeléséhez a parancsfájl az egyetlen módszer a készülék beállításához.

## <a name="prerequisites"></a>Előfeltételek

A parancsfájl beállítja az Azure Migrate-berendezés egy meglévő fizikai gép vagy virtuális gép.

- A készülékként működő gépnek Windows Server 2016-ot kell futtatnia, 32 GB memóriával, nyolc vCPU-val, körülbelül 80 GB lemeztárolóval és egy külső virtuális kapcsolóval. Statikus vagy dinamikus IP-címet és internet-hozzáférést igényel.
- A készülék üzembe helyezése előtt tekintse át a [vmware virtuális gépekre,](migrate-appliance.md#appliance---vmware) [a hyper-v-vm-ekre](migrate-appliance.md#appliance---hyper-v)és a [fizikai kiszolgálókra](migrate-appliance.md#appliance---physical)vonatkozó részletes berendezési követelményeket.
- Ne futtassa a parancsfájlt egy meglévő Azure Migrate-berendezésen.


## <a name="download-the-script"></a>A szkript letöltése

1. Keresse meg a gép/virtuális gép, amely az Azure Migrate készülékként fog működni.
2. A gépen tegye a következőket:

    - Ha vmware virtuális gépekkel vagy Hyper-V vM-ekkel szeretné használni a készüléket, [töltse le](https://go.microsoft.com/fwlink/?linkid=2105112) a telepítő parancsfájlt és az MsI-ket tartalmazó tömörített mappát.
    - Ha fizikai kiszolgálókkal szeretné használni a készüléket, töltse le a parancsfájlt az Azure Áttelepítési portálról, az [oktatóanyagban leírtak szerint.](tutorial-assess-physical.md#set-up-the-appliance)

## <a name="verify-file-security"></a>A fájlbiztonság ellenőrzése

A tömörített fájl telepítése előtt ellenőrizze, hogy a tömörített fájl biztonságos-e.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. A következő parancs futtatása a tömörített fájl kivonatának létrehozásához
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Ellenőrizze, hogy a létrehozott kivonatoló értékek megfelelnek-e ezeknek a beállításoknak (a készülék legújabb verziójához):

    **Algoritmus** | **Kivonat értéke**
      --- | ---
      MD5 | 1e92ede3e87c03bd148e56a708cdd33f
      SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

## <a name="run-the-script"></a>A szkript futtatása

Itt van, amit a forgatókönyvet csinál:

- Ügynökök és egy webalkalmazás telepítése.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letöltése és telepítése egy IIS újraírható modul. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a beállításkulcsot (HKLM), az Azure Migrate állandó beállításaival.
- Napló- és konfigurációs fájlokat hoz létre az alábbiak szerint:
    - **Konfigurációs fájlok**: %ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**: %ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt a készüléket tartalmazó mappába.
2. Indítsa el a PowerShellt a számítógépen rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kinyert tartalmat tartalmazza.
4. Futtassa az **AzureMigrateInstaller.ps1** parancsfájlt az alábbiak szerint:

    - VMware esetén: 
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware
        ```
    - Hyper-V esetén:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv
        ```
    - Fizikai kiszolgálók esetén:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
        ```      
5. Miután a parancsfájl sikeresen fut, elindítja a készülék webalkalmazást, így beállíthatja a készüléket. Ha bármilyen problémát tapasztal, a parancsfájlnaplókat a C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log helyen tekintheti meg.

## <a name="next-steps"></a>További lépések

Miután beállította a készüléket a szkript használatával, kövesse az alábbi utasításokat:

- Állítsa be a készüléket a [VMware számára](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Állítsa be a készüléket a [Hyper-V-hez.](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)
- Állítsa be a készüléket [fizikai kiszolgálókhoz](how-to-set-up-appliance-physical.md).