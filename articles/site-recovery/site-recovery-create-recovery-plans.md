---
title: "A feladatátvétel és helyreállítás a helyreállítási terv létrehozása az Azure Site Recovery |} Microsoft Docs"
description: "Ismerteti, hogyan hozhat létre és testreszabása az Azure Site Recovery szolgáltatásban a feladatátvétel és a virtuális gépek és fizikai kiszolgálók helyreállításához a helyreállítási terv"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/25/2017
ms.author: raynew
ms.openlocfilehash: 202e0ac8be36e9156ec16fadc1b722f4eb3d1432
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2017
---
# <a name="create-recovery-plans"></a>A helyreállítási terv létrehozása


Ez a cikk tájékoztatást ad azokról a létrehozása és testreszabása a helyreállítási terv [Azure Site Recovery](site-recovery-overview.md).

Megjegyzéseit vagy kérdéseit a cikk alján, vagy az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.

 Hozzon létre helyreállítási tervek a következő:

* Adja meg a csoportok a feladataikat együtt átadó, amely majd együtt elindításához gépek.
* Gépek, együtt csoportosításával őket egy helyreállítási modellre függőségeinek csoport megtervezése. Például a feladatátvétel, és jelenítse meg az adott alkalmazást, akkor csoportjához összes, a virtuális gépek az adott alkalmazáshoz, a helyreállítási terv azonos csoportjához.
* A feladatátvételt. A helyreállítási terv futtathatja a teszteket, tervezett, vagy nem tervezett feladatátvételt.


## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása

1. Kattintson a **helyreállítási tervek** > **helyreállítási terv létrehozása**.
   Adja meg a helyreállítási tervben és a forrás és cél nevét. A forráshely kell rendelkeznie, amelyek engedélyezve vannak a feladatátvétel és a helyreállítási virtuális gépek.

    - A VMM és a VMM replikációs, válassza ki a **forrástípus** > **VMM**, és a forrás és cél VMM-kiszolgálókon. Kattintson a **Hyper-V** védett felhő megjelenítéséhez.
    - A VMM az Azure-ba, válassza ki a **forrástípus** > **VMM**.  Válassza ki a forrás VMM-kiszolgálón, és **Azure** céljaként.
    - A Hyper-V replikáció az Azure-ba (VMM nélkül), válassza ki a **forrástípus** > **Hyper-V hely**. Válassza ki a helyet a forrásként és **Azure** céljaként.
    - VMware virtuális gép vagy egy helyszíni fizikai kiszolgáló Azure-ba, a konfigurációs kiszolgáló kijelölése a forrásként és **Azure** céljaként.
    - Azure-az Azure-bA helyreállítási tervet válasszon egy Azure-régió, a forrás-, és a célként egy másodlagos Azure-régiót. A másodlagos Azure-régiók csak azokat, amelyhez a virtuális gépeket védetté tenni.
2. A **válassza ki a virtuális gépek**, válassza ki a hozzáadni kívánt az alapértelmezett (csoportot % 1) a helyreállítási tervben szereplő kívánt virtuális gépek (vagy replikációs csoport).

## <a name="customize-and-extend-recovery-plans"></a>Testreszabására és kibővítésére helyreállítási tervek

Testre szabhatja és a helyreállítási terv kiterjesztése:

- **Új csoportok hozzáadása**– vegye fel a további helyreállítási terv csoportot (legfeljebb 7) az alapértelmezett csoporthoz, majd további gépeket vagy replikációs csoport helyreállítási terv csoportokhoz. Csoportok számozása a sorrendben, ahol hozzá őket. Egy virtuális géphez, vagy a replikációs csoport esetében is csak egy helyreállítási terv csoportba foglalandó.
- **Adja hozzá a manuális műveletet**– manuális műveletek előtt vagy a helyreállítási terv csoport után is hozzáadhat. A helyreállítási terv fut, leállítja a pontnál, amelyen a manuális műveletet beszúrni. Egy párbeszédpanelen kéri, hogy adja meg, hogy befejeződött-e a manuális műveletet.
- **A parancsprogramok hozzáadásához**– előtt vagy után egy helyreállítási terv csoport futtatott parancsfájlok, adhat hozzá. Amikor egy új, hozzáadja a csoport műveletek új készletét. Például a csoport 1 előtti ismertetett lépések jön létre a name: csoport 1: előtti lépéseket. Összes előtti lépés jelenik meg ebben a készletben található. Csak adhat hozzá parancsfájl az elsődleges helyen, ha a VMM-kiszolgáló telepítve van.
- **Adja hozzá az Azure runbookok**– kiterjesztheti a helyreállítási terv Azure runbookok. Például feladatok automatizálásához, vagy hozzon létre egylépéses helyreállítási. [További információk](site-recovery-runbook-automation.md).

## <a name="add-scripts"></a>Parancsfájlok hozzáadása

Is használhatja a PowerShell-parancsfájlok a helyreállítási terv.

 - Győződjön meg arról, hogy parancsfájlok try-catch blokkok, hogy a kivételek szabályosan kezeli.
    - Ha a parancsfájl-kivételt, akkor leáll, és a feladat jeleníti meg, mert nem sikerült.
    - Ha hiba lép fel, a parancsfájl minden fennmaradó részét nem fut.
    - Ha hiba lép fel, ha futtatja a nem tervezett feladatátvétel, a helyreállítási terv továbbra is.
    - Ha hiba történik, amikor a tervezett feladatátvételt, leállítja a helyreállítási terv. Javítsa ki a parancsprogramot, ellenőrizze, hogy a várt módon fut és a helyreállítási terv újra, majd futtassa kell.
- A Write-Host parancs egy helyreállítási tervhez tartozó parancsfájl nem működik, és a parancsfájl futtatása sikertelen lesz. Kimeneti, hozzon létre egy proxyparancsfájl, amely viszont fut a fő parancsfájlba. Győződjön meg arról, hogy az összes kimeneti adatcsatornán-e használni a >> parancsot.
  * A parancsfájl végrehajtásának időkorlátja, ha nem 600 másodpercen belül ad vissza.
  * Ha bármi STDERR ír, a parancsfájl besorolásának sikertelenként. Ezt az információt a parancsfájl végrehajtásának részletes adatai jelennek meg.

A VMM a központi telepítésben használata:

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
> Végrehajtási házirend a csak a 64 bites powershell célszerű figyelmen kívül hagyása. Ha beállította a 32 bites PowerShell, a parancsfájlok fog nem exeute.

## <a name="add-a-script-or-manual-action-to-a-plan"></a>Egy parancsfájl vagy manuális művelet hozzáadása egy csomaghoz

Virtuális gépek vagy a replikációs csoportok felvéve, és a csomag létrehozása után egy parancsfájl adhat hozzá a helyreállítási terv alapértelmezett csoport.

1. Nyissa meg a helyreállítási terv.
2. Bármely elemére a **lépés** listára, majd **parancsfájl** vagy **manuális művelet**.
3. Adja meg, hogy a parancsfájl vagy a művelet előtt vagy után a kijelölt elem hozzáadásához. Használja a **fel** és **le** gombok pozícióba való pozicionálására parancsfájl felfelé vagy lefelé.
4. Ha egy új VMM, válassza ki a **VMM parancsfájl feladatátvétel**. A **parancsfájl elérési útján**, írja be a relatív elérési utat a megosztáshoz. A VMM az alábbi példában, adja meg az elérési út: **\RPScripts\RPScript.PS1**.
5. Ha hozzáad egy Azure Automation szolgáltatásbeli könyv futtassa, adja meg, ahol a runbook is található Azure Automation-fiók, és válassza ki a megfelelő Azure-forgatókönyvek parancsfájlt.
6. A helyreállítási terv feladatátvétellel, győződjön meg arról, hogy a parancsfájl megfelelően működik-e.


### <a name="add-a-vmm-script"></a>A VMM-parancsfájl hozzáadása

Ha a VMM-forráshelyről, parancsfájl létrehozása a VMM-kiszolgálón, és adja hozzá a helyreállítási tervben.

1. Hozzon létre egy új mappát a könyvtármegosztáson. Például \<VMMServerName > \MSSCVMMLibrary\RPScripts. Helyezze el a forrás és cél VMM-kiszolgálók.
2. Hozzon létre a parancsfájlt (például RPScript), és ellenőrizze, hogy megfelelően működik-e.
3. A helyen helyezze el a parancsfájl \<VMMServerName > \MSSCVMMLibrary, a forrás és cél VMM-kiszolgálókon.


## <a name="next-steps"></a>Következő lépések

[További](site-recovery-failover.md) feladatátvételek futtatásával kapcsolatos.
