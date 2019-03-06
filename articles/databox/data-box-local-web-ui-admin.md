---
title: A Microsoft Azure Data Box felügyelete a helyi webes felhasználói felülettel | Microsoft Docs in data
description: A cikk azt ismerteti, hogyan használhatja a helyi webes felhasználói felületet a Data Box-eszköz felügyeletére.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 02/01/2019
ms.author: alkohli
ms.openlocfilehash: d5b6949930161f78c6edf4213df98471e96b1d42
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404042"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box"></a>A Data Box felügyelete a helyi webes felhasználói felülettel

A cikk a Data Boxon végrehajtható konfigurációs és felügyeleti feladatokat ismerteti. A Data Box az Azure Portal felhasználói felületével és az eszköz helyi webes felhasználói felületével felügyelhető. A cikk a helyi webes felhasználói felülettel végrehajtható feladatokra összpontosít.

A Data Box helyi webes felhasználói felülete az eszköz kezdeti konfigurálására használható. A helyi webes felhasználói felülettel emellett leállíthatja és újraindíthatja a Data Boxot, diagnosztikai teszteket futtathat, szoftvereket frissíthet, másolási naplókat tekinthet meg, valamint naplócsomagot hozhat létre a Microsoft támogatási szolgálata számára.

A cikk az alábbi oktatóanyagokat tartalmazza:

- Támogatási csomag létrehozása
- Az eszköz leállítása és újraindítása
- Töltse le a AJ vagy fájlok manifest
- Az eszköz rendelkezésre álló kapacitásának megtekintése
- Ellenőrzőösszeg-érvényesítés kihagyása 

## <a name="generate-support-package"></a>Támogatási csomag létrehozása

Ha eszközproblémákat tapasztal, létrehozhat egy támogatási csomagot a rendszernaplókból. A Microsoft támogatási szolgálata a csomag segítségével hárítja el a problémát. Támogatási csomag létrehozásához hajtsa végre az alábbi lépéseket:

1. A helyi webes felhasználói felületen nyissa meg a **Contact Support** (Kapcsolatfelvétel a támogatási szolgálattal) lapot, és kattintson a **Create Support package** (Támogatási csomag létrehozása) elemre.

    ![Támogatási csomag létrehozása – 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. A rendszer összeállítja a támogatási csomagot. A művelet pár percet vesz igénybe.

    ![Támogatási csomag létrehozása – 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. A támogatási csomag létrehozásának befejezését követően kattintson a **Download Support package** (Támogatási csomag letöltése) elemre. 

    ![Támogatási csomag létrehozása – 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. Tallózással válassza ki a letöltés helyét. Nyissa meg a mappát a tartalmak megtekintéséhez.

    ![Támogatási csomag létrehozása – 5](media/data-box-local-web-ui-admin/create-support-package-5.png)


## <a name="shut-down-or-restart-your-device"></a>Az eszköz leállítása és újraindítása

A Data Box a helyi webes felhasználói felülettel állítható le vagy indítható újra. Az újraindítás előtt ajánlott offline állapotba állítani a gazdagépen található megosztásokat, majd magát az eszközt is. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét. Az eszköz leállításakor ellenőrizze, hogy nincs-e folyamatban adatmásolás.

A Data Box leállításához hajtsa végre az alábbi lépéseket.

1. A helyi webes felhasználói felületen lépjen a **Shut down or restart** (Leállítás vagy újraindítás) lapra.
2. Kattintson a **Shut down** (Leállítás) elemre.

    ![A Data Box leállítása – 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Ha a rendszer megerősítést kér, kattintson az **OK** gombra a folytatáshoz.

    ![A Data Box leállítása – 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Az eszköz leállítását követően az előlapon található bekapcsoló gombbal kapcsolhatja be az eszközt.

A Data Box újraindításához hajtsa végre az alábbi lépéseket.

1. A helyi webes felhasználói felületen lépjen a **Shut down or restart** (Leállítás vagy újraindítás) lapra.
2. Kattintson a **Restart** (Újraindítás) elemre.

    ![A Data Box újraindítása – 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Ha a rendszer megerősítést kér, kattintson az **OK** gombra a folytatáshoz.

   Az eszköz leáll, majd újraindul.

## <a name="download-bom-or-manifest-files"></a>Töltse le a AJ vagy fájlok manifest

Anyagot találhat az anyagjegyzék (AJ) vagy a jegyzékfájlok tartalmazzák az, hogy a Data boxra másolja a fájlokat. Ezek a fájlok jönnek létre a Data Box szállításra való előkészítésekor.

Mielőtt elkezdené, győződjön meg arról, hogy befejeződött-e a Data Box **szállításra való** . lépés. Kövesse az alábbi lépéseket, töltse le a AJ vagy fájlok manifest:

1. Nyissa meg a Data Box helyi webes felületén. Látni fogja, hogy a Data Box a szállításra való előkészítése befejeződött. Ha az eszköz előkészítése befejeződött, az eszköz állapota látható **szállításra kész**.

    ![Eszköz szállításra kész](media/data-box-portal-admin/ready-to-ship.png)

2. Kattintson a **fájlok listájának letöltése** a Data Box másolt fájlok listájának letöltése.

    ![Kattintson a letöltés azon fájlok listája](media/data-box-portal-admin/download-list-of-files.png)

3. A Fájlkezelőben, látni fogja, hogy külön listát, a fájlok jönnek létre, attól függően, csatlakozni az eszközhöz használt protokollt és a használt Azure Storage-típus.

    ![Fájljainak típusának és a kapcsolat tárolóprotokoll](media/data-box-portal-admin/files-storage-connection-type.png)

   Az alábbi táblázat a fájlnevek rendeli hozzá az Azure Storage típusát és a használt kapcsolati protokoll.

    |Fájlnév  |Az Azure tárolás típusa  |Használt kapcsolati protokoll |
    |---------|---------|---------|
    |databoxe2etest_BlockBlob.txt     |Blokkblobok         |AZ SMB ÉS NFS         |
    |databoxe2etest_PageBlob.txt     |Lapblobok         |AZ SMB ÉS NFS         |
    |databoxe2etest_AzFile-BOM.txt    |Azure Files         |AZ SMB ÉS NFS         |
    |databoxe2etest_PageBlock_Rest-BOM.txt     |Lapblobok         |REST        |
    |databoxe2etest_BlockBlock_Rest-BOM.txt    |Blokkblobok         |REST         |
    |mydbmdrg1_MDisk-BOM.txt    |Managed Disk         |AZ SMB ÉS NFS         |
    |mydbmdrg2_MDisk-BOM.txt     |Managed Disk         |AZ SMB ÉS NFS         |

Ez a lista után az Azure-adatközpontban és a Data Box ad vissza az Azure Storage-fiókhoz feltöltött fájlok ellenőrzésére használhatja. Az alábbiakban látható egy minta jegyzékfájl.

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

Ez a fájl lévő összes fájl, amely át lettek másolva a Data Box listáját tartalmazza. Ebben a fájlban *crc64* érték vonatkozik, a megfelelő fájlt hoz létre ellenőrzőösszeget.

## <a name="view-available-capacity-of-the-device"></a>Az eszköz rendelkezésre álló kapacitásának megtekintése

Az eszköz irányítópultján tekintheti meg az eszköz rendelkezésre álló és felhasznált kapacitását. 

1. A helyi webes felhasználói felületen lépjen a **View dashboard** (Irányítópult megtekintése) lapra.
2. A **Connect and copy** (Kapcsolódás és másolás) részen az eszköz szabad és felhasznált területe látható.

    ![Rendelkezésre álló kapacitás megtekintése](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)


## <a name="skip-checksum-validation"></a>Ellenőrzőösszeg-érvényesítés kihagyása

Ellenőrzőösszegek akkor jönnek létre az adatok alapértelmezés szerint szállításra való előkészítésekor. Attól függően, a következő adattípus (kis méretűek), egyes ritka esetekben a teljesítmény lassú lehet. Ilyen esetben kihagyhatja az ellenőrzőösszeg létrehozását. 

Határozottan javasoljuk, hogy csak akkor tiltsa le az ellenőrző létrehozását, ha komoly hatással lenne a teljesítményre.

1. A helyi webes felhasználói felületen, az eszköz jobb felső sarkában nyissa meg a beállításokat.

    ![Ellenőrzőösszeg letiltása](media/data-box-local-web-ui-admin/disable-checksum.png)

2. Tiltsa le az ellenőrzőösszeg-érvényesítést a **Disable** (Letiltás) paranccsal.
3. Kattintson az **Alkalmaz** gombra.

## <a name="next-steps"></a>További lépések

- Megismerheti, hogyan [kezelheti a Data Boxot az Azure Portalon keresztül](data-box-portal-admin.md).

