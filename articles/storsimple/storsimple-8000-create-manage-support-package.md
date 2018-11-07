---
title: A StorSimple 8000 sorozat támogatási csomag létrehozása |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre, visszafejtése, és szerkessze a támogatási csomagot, a StorSimple 8000 sorozatú eszköz esetében.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: dfc2d8d763a1eb64a37af73e03992f2d948a6856
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254366"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Létrehozása és kezelése a StorSimple 8000 sorozat támogatási csomag

## <a name="overview"></a>Áttekintés

A StorSimple-támogatási csomagot egy könnyen használható mechanizmust, amely gyűjti az összes releváns napló Support nyújtanak hibaelhárítást végezhetnek a StorSimple eszköz. Az összegyűjtött naplók titkosított és tömörített.

Az oktatóanyag részletes utasításokat követve létrehozása és kezelése a StorSimple 8000 sorozatú eszköz esetében a támogatási csomagot tartalmazza. Ha a StorSimple Virtual Array dolgozik, lépjen a [hozzon létre egy log csomagot](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Hozzon létre egy támogatási csomagot

Bizonyos esetekben szüksége manuálisan létrehozni a storsimple-höz készült Windows PowerShell-lel a támogatási csomagot. Példa:

* Ha a bizalmas adatokat a naplófájlokban a Microsoft Support megosztása előtt szüksége.
* Ha problémái vannak kapcsolódási problémák miatt a csomag feltöltése.

A manuálisan létrehozott támogatási csomag megoszthatja Support, e-mailben. A következő lépésekkel hozzon létre egy támogatási csomagot a Windows PowerShell storsimple-höz készült.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>A támogatási csomag létrehozása a Windows PowerShell storsimple-höz készült

1. Egy Windows PowerShell-munkamenetet rendszergazdaként azon a távoli számítógép, amellyel csatlakozni a StorSimple-eszköz elindításához írja be a következő parancsot:
   
    `Start PowerShell`
2. A Windows PowerShell-munkamenetben kapcsolódjon a SSAdmin konzol, az eszköz:
   
   1. A parancssorba írja be a következőt:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. A megnyíló párbeszédpanelen adja meg az eszköz rendszergazdai jelszavát. Az alapértelmezett jelszó az _jelszó1_.
     
      ![PowerShell-hitelesítő adatok párbeszédpanel](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Kattintson az **OK** gombra.
   4. A parancssorba írja be a következőt:
     
      `Enter-PSSession $MS`
3. A megnyitott munkamenetben adja meg a megfelelő parancsot.
   
   * Jelszóval védett hálózati megosztások adja meg:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Kérni fogja a jelszó és a egy titkosítási jelszó (mivel titkosítva van a támogatási csomag). A támogatási csomagot létrejön az alapértelmezett mappában (eszköznév hozzáfűzi az aktuális dátum és idő).
   * Amelyek nem jelszóval védett megosztások esetén nem kell a `-Credential` paraméter. Adja meg a következőket:
     
       `Export-HcsSupportPackage`
     
       A támogatási csomag mindkét vezérlőn az alapértelmezett mappában jön létre. A csomag nincs egy titkosított, tömörített fájlt, amely hibaelhárításhoz Support lehet küldeni. További információkért lásd: [forduljon a Microsoft támogatási](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Az Export-HcsSupportPackage parancsmag-paraméterek

Az Export-HcsSupportPackage parancsmag a következő paramétereket is használhatja.

| Paraméter | Kötelező/választható | Leírás |
| --- | --- | --- |
| `-Path` |Szükséges |Használatával a megosztott hálózati mappát, amelyben a támogatási csomag kerül helyének megadására. |
| `-EncryptionPassphrase` |Szükséges |Adjon meg egy jelszót a segítségével titkosítja a támogatási csomag használatával. |
| `-Credential` |Optional |Adja meg a hálózati megosztott mappában a hozzáférési hitelesítő adatok használatával. |
| `-Force` |Optional |Használja a titkosítási jelszót megerősítési lépés kihagyásához. |
| `-PackageTag` |Optional |Megadhatja egy könyvtárat *elérési út* található, amely a támogatási csomagot el van helyezve. Az alapértelmezett érték a [device name]-[beállított aktuális dátum és time:yyyy-MM-dd-HH-mm-ss]. |
| `-Scope` |Optional |Adja meg a **fürt** (alapértelmezett), hogy mindkét vezérlő támogatási csomag létrehozása. Ha azt szeretné, csak az aktuális vezérlő a csomagot szeretne létrehozni, adja meg a **vezérlő**. |

## <a name="edit-a-support-package"></a>A támogatási csomag szerkesztése

Miután létrehozta a támogatási csomagot, szüksége lehet a bizalmas adatokat a csomag szerkesztése. Ez magában foglalhatja kötetnevek eszköz IP-címek és a naplófájlok biztonsági mentési nevet.

> [!IMPORTANT]
> Csak akkor szerkeszthető egy támogatási csomagot, amely a storsimple-höz készült Windows PowerShell-lel jött létre. Az Azure Portalon a StorSimple-Eszközkezelő szolgáltatással létrehozott csomagot nem szerkeszthető.

Szerkesztheti a támogatási csomagot a Microsoft Support helyen, először vissza a támogatási csomagot, a fájlok szerkesztésére és újbóli titkosítása a azt. Hajtsa végre a következő lépéseket.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Szerkesztheti a támogatási csomagot, a Windows PowerShell storsimple-höz készült

1. Hozzon létre egy támogatási csomagot, mint korábban, az itt ismertetett [, hozzon létre egy támogatási csomagot a Windows PowerShell storsimple-höz készült](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Töltse le a parancsfájlt](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) helyileg, az ügyfél.
3. A Windows PowerShell-modul importálásához. Adja meg a helyi mappát, amelybe letöltötte a parancsfájl elérési útját. Importálja a modult, írja be:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Összes fájl *.aes* titkosított és tömörített fájlok. Kibontása és fejti vissza a fájlokat, írja be:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Vegye figyelembe, hogy a fájl tényleges bővítmények most megjelenik az összes fájlt.
   
    ![Támogatási csomag szerkesztése](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Ha a titkosítási jelszót kér, adja meg a jelszót, amelyet a támogatási csomag létrehozásakor használt.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Keresse meg a naplófájlokat tartalmazó mappát. Most kibontása és visszafejteni a naplófájlokat, mert ezek lesz az eredeti fájlkiterjesztés. Módosíthatja bármely, a specifikus információkat, például kötetnevek és eszközök IP-címét, távolítsa el ezeket a fájlokat, és mentse a fájlokat.
7. Zárja be a fájlok tömörítése a gzip használatával történik, és titkosítani az AES-256. Ez a sebesség és a biztonság a támogatási csomag átvitele a hálózaton keresztül. Tömörítését, és a fájlok titkosítását, adja meg a következőket:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Támogatási csomag szerkesztése](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Amikor a rendszer kéri, adja meg a módosított támogatási csomag szükséges jelszót.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Jegyezze fel az új jelszót, úgy, hogy a Microsoft Support kért megoszthatja azt.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Példa: Egy támogatási csomagot a jelszóval védett megosztáson található fájlok szerkesztése

Az alábbi példa bemutatja, hogyan visszafejteni, szerkesztheti és újra titkosítja egy támogatási csomagot.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>További lépések

* További információ a [által gyűjtött információk a támogatási csomag](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Ismerje meg, hogyan [támogatási csomagok és eszköznaplók hibaelhárítás eszköz segítségével](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Ismerje meg, hogyan [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

