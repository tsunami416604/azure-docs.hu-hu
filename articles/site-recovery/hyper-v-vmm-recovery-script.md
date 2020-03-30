---
title: Parancsfájl hozzáadása helyreállítási tervhez az Azure Site Recovery szolgáltatásban
description: Ismerje meg, hogyan adhat hozzá egy VMM-parancsfájlt a Hyper-V virtuális gépek vmm-felhőkben lévő vész-helyreállítási tervéhez.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 6902876e066649ae4dff4134fb8cc462f30dd0b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084873"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>VMM-parancsfájl hozzáadása helyreállítási tervhez

Ez a cikk azt ismerteti, hogy miként hozhat létre system centeres virtuálisgép-kezelői (VMM) parancsfájlt, és hogyan veheti fel azt az Azure Site Recovery helyreállítási [tervéhez.](site-recovery-overview.md)

A cikk alján vagy az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)található megjegyzéseket és kérdéseket tehet közzé.

## <a name="prerequisites"></a>Előfeltételek

A PowerShell-parancsfájlok a helyreállítási tervekben használhatja. Ahhoz, hogy a helyreállítási tervből elérhető legyen, meg kell írnia a parancsfájlt, és el kell helyeznie a parancsfájlt a VMM-tárba. A parancsfájl írásakor tartsa szem előtt az alábbi szempontokat:

* Győződjön meg arról, hogy a parancsfájlok try-catch blokkokat használnak, hogy a kivételek kezelése szabályosan legyen.
    - Ha kivétel történik a parancsfájlban, a parancsfájl leáll, és a feladat sikertelenként jelenik meg.
    - Hiba esetén a parancsfájl többi része nem fut.
    - Ha nem tervezett feladatátvétel futtatásakor hiba lép fel, a helyreállítási terv folytatódik.
    - Ha egy tervezett feladatátvétel futtatásakor hiba történik, a helyreállítási terv leáll. Javítsa ki a parancsfájlt, ellenőrizze, hogy a várt módon fut-e, majd futtassa újra a helyreállítási tervet.
        - A `Write-Host` parancs nem működik a helyreállítási terv parancsfájljában. Ha parancsfájlban `Write-Host` használja a parancsot, a parancsfájl sikertelen lesz. Kimenet létrehozásához hozzon létre egy proxy parancsfájlt, amely viszont futtatja a fő parancsfájlt. Annak érdekében, hogy az összes ** \> ** kimenet ki legyen irányítva, használja a parancsot.
        - A parancsfájl időtúljár, ha 600 másodpercen belül nem tér vissza.
        - Ha valami van írva STDERR, a szkript minősül sikertelen. Ez az információ a parancsfájl végrehajtási részleteiben jelenik meg.

* A helyreállítási tervparancsfájljai a VMM szolgáltatásfiók környezetében futnak. Győződjön meg arról, hogy ez a fiók olvasási engedéllyel rendelkezik ahhoz a távoli megosztáshoz, amelyen a parancsfájl található. Tesztelje a parancsfájl futtatásához ugyanolyan szintű felhasználói jogokat, mint a VMM szolgáltatásfiók.
* A VMM-parancsmagok egy Windows PowerShell-modulban kerülnek kézbesítésre. A modul a VMM konzol telepítésekor települ. A modul betöltéséhez használja a következő parancsot a parancsfájlban: 

    `Import-Module -Name virtualmachinemanager`

    További információt a Windows PowerShell és a VMM – első lépések című [témakörben talál.](https://technet.microsoft.com/library/hh875013.aspx)
* Győződjön meg arról, hogy legalább egy könyvtárkiszolgálóval rendelkezik a VMM-telepítésben. Alapértelmezés szerint a VMM-kiszolgáló könyvtármegosztási elérési útja helyileg található a VMM-kiszolgálón. A mappa neve MSCVMMLibrary.

  Ha a tármegosztáselérési út távoli (vagy helyi, de nem megosztott az MSCVMMLibrary \\könyvtárral), konfigurálja a megosztást a következőképpen a következő ként: libserver2.contoso.com\share\ (például:
  
  1. Nyissa meg a Rendszerleíróadatbázis-szerkesztőt, majd lépjen **a HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration mappába.**

  1. Módosítsa a **ScriptLibraryPath** értékét ** \\\libserver2.contoso.com\share értékre.\\** Adja meg a teljes teljes teljes tartománynát. Engedélyek megadása a megosztási helyhez. Ez a megosztás gyökércsomópontja. A gyökércsomópont ellenőrzéséhez a VMM-ben lépjen a könyvtár gyökércsomópontjára. A megnyíló elérési út az elérési út gyökere. Ezt az elérési utat kell használnia a változóban.

  1. Tesztelje a parancsfájlt egy olyan felhasználói fiókkal, amely ugyanolyan szintű felhasználói jogokkal rendelkezik, mint a VMM szolgáltatásfiók. Ezek a felhasználói jogok használatával ellenőrzi, hogy az önálló, tesztelt parancsfájlok ugyanúgy futnak-e, mint a helyreállítási tervekben. A VMM-kiszolgálón állítsa be a végrehajtási házirendet a megkerülésre az alábbiak szerint:

     a. Nyissa meg a **64 bites Windows PowerShell-konzolt** rendszergazdaként.
     
     b. Írja be **a Set-executionpolicy bypass (Set-executionpolicy bypass ) című értéket.** További információ: [A Set-ExecutionPolicy parancsmag használata.](https://technet.microsoft.com/library/ee176961.aspx)

     > [!IMPORTANT]
     > **Set-executionpolicy bypass beállítása** csak a 64 bites PowerShell-konzolon. Ha a 32 bites PowerShell-konzolhoz állítja be, a parancsfájlok nem futnak.

## <a name="add-the-script-to-the-vmm-library"></a>A parancsfájl hozzáadása a VMM-tárhoz

Ha vmm forráshellyel rendelkezik, parancsfájlt hozhat létre a VMM-kiszolgálón. Ezután a parancsfájlt is beillesztheti a helyreállítási tervbe.

1. A tármegosztásban hozzon létre egy új mappát. A VMM-kiszolgáló neve például \<>\MSSCVMMLibrary\RPScripts. Helyezze a mappát a forrás- és a cél VMM-kiszolgálókra.
1. Hozza létre a parancsfájlt. Például nevezze el a parancsfájlt RPScript. Ellenőrizze, hogy a parancsfájl a várt módon működik-e.
1. Helyezze a parancsfájlt a \<VMM-kiszolgáló nevébe>\MSSCVMMLibrary mappába a forrás- és a cél VMM-kiszolgálókon.

## <a name="add-the-script-to-a-recovery-plan"></a>A parancsfájl hozzáadása helyreállítási tervhez

Miután virtuális gépeket vagy replikációs csoportokat adott hozzá egy helyreállítási tervhez, és létrehozta a tervet, hozzáadhatja a parancsfájlt a csoporthoz.

1. Nyissa meg a helyreállítási tervet.
1. A **Lépés** listában jelöljön ki egy elemet. Ezután válassza a **Parancsfájl** vagy **a Kézi művelet lehetőséget.**
1. Adja meg, hogy a parancsfájlt vagy a műveletet a kijelölt elem elé vagy után szeretné-e hozzáadni. A parancsfájl pozíciójának felfelé vagy lefelé mozgatásához jelölje be a **Fel** és **a Le** gomb.
1. Ha VMM-parancsfájlt ad hozzá, válassza **a Feladatátvétel VMM-parancsfájlba lehetőséget.** A **Parancsfájl elérési útja**mezőbe írja be a megosztás relatív elérési útját. Írja be például az **\RPScripts\RPScript.PS1 parancsot.**
1. Ha hozzáad egy Azure Automation-runbookot, adja meg azt az Automation-fiókot, amelyben a runbook található. Ezután válassza ki az Azure Runbook-parancsfájlt, amelyet használni szeretne.
1. Annak érdekében, hogy a parancsfájl a várt módon működjön, végezze el a helyreállítási terv tesztfeladat-átvételét.


## <a name="next-steps"></a>További lépések
* További információ a [feladatátvételek futtatásáról.](site-recovery-failover.md)

