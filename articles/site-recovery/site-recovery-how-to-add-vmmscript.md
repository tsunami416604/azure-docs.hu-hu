---
title: "Parancsfájlok felvétele az Azure Site Recovery helyreállítási terv |} Microsoft Docs"
description: "Ismerteti, milyen követelményeket támaszt a helyreállítási tervet a VMM Alkalmazásban, az Azure ad hozzá egy új parancsfájlt"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: shons
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/13/2017
ms.author: ruturaj
ms.openlocfilehash: 60c6eebd9323028c63f42bd8a0996e3c0a2a1cf1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="add-vmm-scripts-to-a-recovery-plan"></a>A VMM-parancsfájlok hozzáadása a helyreállítási terv


Ez a cikk tájékoztatást ad azokról a létrehozása és a helyreállítási tervek VMM parancsfájlokat [Azure Site Recovery](site-recovery-overview.md).

Megjegyzéseit vagy kérdéseit a cikk alján, vagy az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.

## <a name="prerequisites-of-adding-a-script-to-recovery-plan"></a>A helyreállítási terv hozzáadása egy parancsfájl Előfeltételek

Is használhatja a PowerShell-parancsfájlok a helyreállítási terv. Szerzői őket, és helyezze el őket a VMM-könyvtárban, a helyreállítási tervből elérhetőnek kell lennie kell. Az alábbiakban néhány szempontot a parancsfájl írása közben.

* Győződjön meg arról, hogy parancsfájlok try-catch blokkok, hogy a kivételek szabályosan kezeli.
    - Ha a parancsfájl-kivételt, akkor leáll, és a feladat jeleníti meg, mert nem sikerült.
    - Ha hiba lép fel, a parancsfájl minden fennmaradó részét nem fut.
    - Ha hiba lép fel, ha futtatja a nem tervezett feladatátvétel, a helyreállítási terv továbbra is.
    - Ha hiba történik, amikor a tervezett feladatátvételt, leállítja a helyreállítási terv. Javítsa ki a parancsprogramot, ellenőrizze, hogy a várt módon fut és a helyreállítási terv újra, majd futtassa kell.
        - A Write-Host parancs egy helyreállítási tervhez tartozó parancsfájl nem működik, és a parancsfájl futtatása sikertelen lesz. Kimeneti, hozzon létre egy proxyparancsfájl, amely viszont fut a fő parancsfájlba. Győződjön meg arról, hogy az összes kimeneti adatcsatornán-e használni a >> parancsot.
        - A parancsfájl végrehajtásának időkorlátja, ha nem 600 másodpercen belül ad vissza.
        - Ha bármi STDERR ír, a parancsfájl besorolásának sikertelenként. Ezt az információt a parancsfájl végrehajtásának részletes adatai jelennek meg.

* A helyreállítási terv parancsprogramokat futtatja a VMM-szolgáltatásfiók a környezetben. Győződjön meg arról, hogy ez a fiók rendelkezik olvasási engedéllyel a távoli megosztás, amelyen a parancsfájl helyezkedik. A parancsprogram futtatását, hogy a VMM szolgáltatás fiókja jogosultsági szint teszteléséhez.
* VMM-parancsmagok a Windows PowerShell-moduljának érkeznek. A modul telepítve van a VMM konzol telepítésekor. A parancsfájlba, az alábbi parancs segítségével a parancsfájl tölthetők:
   - Import-Module-Name virtualmachinemanager. [További információk](https://technet.microsoft.com/library/hh875013.aspx).
* Gondoskodjon legalább egy erőforrástár-kiszolgáló a VMM-környezetben. Alapértelmezés szerint a VMM-kiszolgáló könyvtármegosztásának elérési található helyileg a VMM-kiszolgáló, a mappa nevét MSCVMMLibrary.
    * Ha a Könyvtármegosztás elérési útjára távoli (vagy helyi azonban nem megosztott a MSCVMMLibrary), konfigurálja a megosztást az alábbiak szerint (használatával \\példaként libserver2.contoso.com\share\):
      * Nyissa meg a Beállításszerkesztőt, és keresse meg **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure hely Recovery\Registration**.
      * Az érték szerkesztése **ScriptLibraryPath** és helyezze el, mint a \\libserver2.contoso.com\share\. Adja meg a teljes Tartománynevet. Adja meg a megosztás helyét engedélyekkel. Vegye figyelembe, hogy ez a csomópont, a megosztáshoz. **Ennek ellenőrzéséhez megkeresheti a gyökércsomópontnál a VMM-könyvtárban. Az elérési utat, amely megnyitja az útvonal gyökerének lesz – a egy kell használni a változóban**.
      * Győződjön meg arról, hogy tesztelje a parancsfájl a VMM-szolgáltatásfiókként ugyanazokkal az engedélyekkel rendelkező felhasználói fiókkal. Ellenőrzi, hogy önálló parancsfájl futását ugyanúgy, mint ahogyan a helyreállítási terv fognak tesztelve. A VMM-kiszolgálón állítsa be a kihagyásához az alábbiak szerint:
        * Nyissa meg a **64 bites Windows PowerShell** konzolt emelt szintű jogosultságokkal.
        * Típus: **Set-executionpolicy áthidaló**. [További információk](https://technet.microsoft.com/library/ee176961.aspx).

> [!IMPORTANT]
> VPN-kapcsolat mellőzése csak a 64 bites PowerShell végrehajtási házirend akkor kell beállítania. Ha beállította a 32 bites PowerShell, a parancsfájlok fog nem exeute.

## <a name="add-the-script-to-the-vmm-library"></a>A parancsfájl hozzáadása a VMM-könyvtárban

Ha a VMM-forráshelyről, parancsfájl létrehozása a VMM-kiszolgálón, és adja hozzá a helyreállítási tervben.

1. Hozzon létre egy új mappát a könyvtármegosztáson. Például \<VMMServerName > \MSSCVMMLibrary\RPScripts. Helyezze el a forrás és cél VMM-kiszolgálók.
2. Hozzon létre a parancsfájlt (például RPScript), és ellenőrizze, hogy megfelelően működik-e.
3. A helyen helyezze el a parancsfájl \<VMMServerName > \MSSCVMMLibrary, a forrás és cél VMM-kiszolgálókon.

## <a name="add-the-script-to-a-recovery-plan"></a>A parancsfájl hozzáadása egy helyreállítási terv

A parancsfájl adhat hozzá a csoporthoz hozzáadott virtuális gépek vagy a replikációs csoport, és a csomag létrehozása után.

1. Nyissa meg a helyreállítási terv.
2. Bármely elemére a **lépés** listára, majd **parancsfájl** vagy **manuális művelet**.
3. Adja meg, hogy a parancsfájl vagy a művelet előtt vagy után a kijelölt elem hozzáadásához. Használja a **fel** és **le** gombok pozícióba való pozicionálására parancsfájl felfelé vagy lefelé.
4. Ha egy új VMM, válassza ki a **VMM parancsfájl feladatátvétel**. A **parancsfájl elérési útján**, írja be a relatív elérési utat a megosztáshoz. A VMM az alábbi példában, adja meg az elérési út: **\RPScripts\RPScript.PS1**.
5. Ha hozzáad egy Azure Automation szolgáltatásbeli könyv futtassa, adja meg, ahol a runbook is található Azure Automation-fiók, és válassza ki a megfelelő Azure-forgatókönyvek parancsfájlt.
6. Végezzen a helyreállítási terv, győződjön meg arról, hogy a parancsfájl megfelelően működik-e a feladatátvételi tesztet.


## <a name="next-steps"></a>További lépések

[További](site-recovery-failover.md) feladatátvételek futtatásával kapcsolatos.
