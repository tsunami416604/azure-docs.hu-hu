---
title: Parancsfájl hozzáadása az Azure Site Recovery helyreállítási terv |} Microsoft Docs
description: További információk a System Center Virtual Machine Manager (VMM) új parancsfájl hozzáadása az Azure-ban a helyreállítási terv előfeltételeinek.
services: site-recovery
documentationcenter: ''
author: ruturaj
manager: shons
editor: ''
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/13/2017
ms.author: ruturaj
ms.openlocfilehash: 2e00f812fb35ac9a0cb390fc6a3ba40a8678f8dd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29402432"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>A VMM-parancsfájl hozzáadása egy helyreállítási terv

Ez a cikk ismerteti, hogyan hozhat létre a System Center Virtual Machine Manager (VMM) parancsfájl, és adja hozzá a helyreállítási terv [Azure Site Recovery](site-recovery-overview.md).

Ez a cikk, vagy az alsó megjegyzések vagy kérdéseket küldje a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Előfeltételek

Is használhatja a PowerShell-parancsfájlok a helyreállítási terv. Elérhetőnek kell lennie a helyreállítási tervből, kell hozhatnak létre a parancsfájlt, és helyezze el a parancsfájl a VMM-könyvtárban. A parancsprogram írása közben vegye figyelembe a következőket kell figyelembe venni:

* Győződjön meg arról, parancsfájlok try-catch blokkok, így kivételek szabályosan kezeli.
    - Kivétel lép fel a parancsfájlt, ha a parancsfájl leáll, és a feladat jeleníti meg, mert nem sikerült.
    - Ha hiba lép fel, a parancsfájl további része nem fut.
    - Ha hiba lép fel, ha futtatja a nem tervezett feladatátvétel, a helyreállítási terv továbbra is.
    - Ha hiba történik, amikor a tervezett feladatátvételt, leállítja a helyreállítási terv. Javítsa ki a parancsprogramot, ellenőrizze, hogy a várt módon fut és a helyreállítási terv újra, majd futtassa.
        - A `Write-Host` parancs egy helyreállítási tervhez tartozó parancsfájl nem működik. Ha használja a `Write-Host` parancsot egy parancsfájlban a parancsfájl futása sikertelen. Kimeneti, hozzon létre egy proxyparancsfájl, amely viszont fut a fő parancsfájlba. Győződjön meg arról, hogy az összes kimeneti adatcsatornán-e ki, használja a  **\> \>**  parancsot.
        - A parancsfájl végrehajtásának időkorlátja, ha nem 600 másodpercen belül ad vissza.
        - Ha bármi STDERR nevével, a parancsfájl besorolásának sikertelen. Ezt az információt a parancsfájl végrehajtásának részletes adatai jelennek meg.

* A helyreállítási terv a parancsfájlok futtatása a VMM-szolgáltatásfiók környezetében. Győződjön meg arról, hogy ez a fiók rendelkezik-e olvasási engedélyeit a távoli fájlmegosztás, amelyen a parancsfájl helyezkedik-e. A parancsfájl futtatásához a azonos szintű felhasználói jogosultságokkal a VMM-szolgáltatásfiókként tesztelése.
* VMM-parancsmagok a Windows PowerShell-moduljának érkeznek. A modul telepítve van a VMM konzol telepítésekor. A modul betöltése a parancsfájlba, a következő paranccsal a parancsfájlban: 

    `Import-Module -Name virtualmachinemanager`

    További információkért lásd: [Ismerkedés a Windows PowerShell és a VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Győződjön meg arról, hogy rendelkezik-e legalább egy erőforrástár-kiszolgáló a VMM-környezetben. Alapértelmezés szerint a VMM-kiszolgáló könyvtármegosztásának elérési található helyileg a VMM-kiszolgálón. A mappanév MSCVMMLibrary.

  Ha a Könyvtármegosztás elérési útjára távoli (vagy helyi azonban nem megosztott a MSCVMMLibrary esetén), konfigurálhatja a megosztást az alábbiak szerint segítségével \\libserver2.contoso.com\share\ példa:
  
  1. Nyissa meg a Beállításszerkesztőt, és folytassa a **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure hely Recovery\Registration**.

  2. Módosítsa az értéket a **ScriptLibraryPath** való  **\\\libserver2.contoso.com\share\\**. Adja meg a teljes Tartománynevet. Adja meg a megosztás helyét engedélyekkel. Ez az a csomópont, a megosztáshoz. A VMM-ben a gyökércsomópont kereséséhez nyissa meg a gyökércsomópont a könyvtárban. A megnyíló elérési út az útvonal gyökerének. Ez az az elérési utat, a változóban kell használnia.

  3. A parancsprogram teszteléséhez a azonos szintű jogosultságokkal a VMM-szolgáltatásfiókként rendelkező felhasználói fiók használatával. Ezek a felhasználói jogok használatával ellenőrzi, hogy önálló, tesztelt parancsfájlok ugyanúgy futtatja a helyreállítási terv. A VMM-kiszolgálón állítsa be a elkerülésére, az alábbiak szerint:

     a. Nyissa meg a **64 bites Windows PowerShell** konzolt rendszergazdaként.
     
     b. Adja meg **Set-executionpolicy áthidaló**. További információkért lásd: [a Set-ExecutionPolicy parancsmag használatával](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Állítsa be **Set-executionpolicy áthidaló** csak a 64 bites PowerShell konzolon. Ha megadta a 32 bites PowerShell konzol, a parancsfájlok nem futnak.

## <a name="add-the-script-to-the-vmm-library"></a>A parancsfájl hozzáadása a VMM-könyvtárban

Ha a VMM-forráshelyről, létrehozhat egy parancsfájlt a VMM-kiszolgálón. A parancsfájlt, majd vegye fel a helyreállítási terv.

1. A Könyvtármegosztás hozzon létre egy új mappát. Például \<VMM kiszolgáló neve > \MSSCVMMLibrary\RPScripts. A mappa helye a forrás és cél VMM-kiszolgálók.
2. Hozzon létre a parancsfájlt. Például nevezze el a parancsfájl RPScript. Győződjön meg arról, hogy a parancsfájl megfelelően működik-e.
3. A parancsfájlt a \<VMM kiszolgáló neve > \MSSCVMMLibrary mappa a forrás és cél VMM-kiszolgálókon.

## <a name="add-the-script-to-a-recovery-plan"></a>A parancsfájl hozzáadása egy helyreállítási terv

Miután hozzáadott virtuális gépek vagy a replikációs csoportokat a helyreállítási terv és a csomag létrehozása, a parancsfájl is hozzáadhat a csoporthoz.

1. Nyissa meg a helyreállítási terv.
2. Az a **lépés** listára, válassza ki egy elemet. Ezután válassza ki vagy **parancsfájl** vagy **manuális művelet**.
3. Adja meg, hogy a parancsfájl vagy a művelet előtt vagy után a kijelölt elem hozzáadásához. A parancsfájl helyét felfelé vagy lefelé áthelyezéséhez jelölje ki a **fel** és **le** gombokat.
4. Ha egy új VMM, válassza ki a **VMM parancsfájl feladatátvétel**. A **parancsfájl elérési útján**, írja be a relatív elérési utat a megosztáshoz. Adja meg például **\RPScripts\RPScript.PS1**.
5. Ha hozzáad egy Azure Automation-runbook, adja meg az Automation-fiók, ahol a runbook is található. Ezután jelölje ki a használni kívánt Azure-forgatókönyvek parancsfájlt.
6. Győződjön meg arról, hogy a parancsfájl megfelelően működik-e, tegye a helyreállítási terv feladatátvételi tesztet.


## <a name="next-steps"></a>További lépések
* További információ [feladatátvételek futtató](site-recovery-failover.md).

