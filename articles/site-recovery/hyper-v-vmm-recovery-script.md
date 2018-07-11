---
title: A szkript hozzáadása az Azure Site Recovery helyreállítási terv |} A Microsoft Docs
description: Tudnivalók a System Center Virtual Machine Manager (VMM) új parancsfájl hozzáadása az Azure-ban a helyreállítási terv előfeltételeinek.
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: rochakm
editor: ''
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: 71991347ffaf036065aae9e1a93b7eb83a14b15c
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917333"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>A VMM-parancsfájl hozzáadása a helyreállítási terv

Ez a cikk bemutatja, hogyan hozhat létre a System Center Virtual Machine Manager (VMM) parancsfájl, és adja hozzá a helyreállítási terv [Azure Site Recovery](site-recovery-overview.md).

Ez a cikk, vagy az alsó megjegyzések vagy kérdések közzététele a [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Előfeltételek

A helyreállítási tervekben használhatja a PowerShell-parancsfájlokat. A helyreállítási tervből érhető el, hozhat létre a parancsfájlt, és helyezze el a szkriptet a VMM-erőforrástárban. Az alábbi szempontokat tartsa szem előtt a parancsprogram írása közben:

* Győződjön meg arról, parancsfájlok try-catch blokkok, így kivételek szabályosan kezeli.
    - Ha a parancsfájl-kivétel lép fel, a parancsfájl futása áll le, és a feladat jeleníti meg, mert nem sikerült.
    - Ha hiba történik, a többi része a szkript nem fut le.
    - Ha hiba történik, ha nem tervezett feladatátvétel, a helyreállítási terv továbbra is.
    - Ha hiba történik egy tervezett feladatátvétel, a helyreállítási terv leáll. Javítsa ki a parancsfájl-, ellenőrizze, hogy a várt módon fut, és a helyreállítási terv újra futtassa.
        - A `Write-Host` parancs nem működik a helyreállítási terv parancsfájlt. Ha használja a `Write-Host` parancsot egy parancsfájlban a parancsfájl futtatása sikertelen. Kimeneti, hozzon létre egy proxy parancsprogramot, amelynek viszont a fő parancsfájlba. Győződjön meg arról, hogy minden kimenetet eredményez-e ki, használja a **\> \>** parancsot.
        - A parancsfájl időkorlátja, ha nem 600 másodperc ad vissza.
        - Ha STDERR írt semmit, a parancsfájl sikertelenként van besorolva. Ezeket az információkat szkriptvégrehajtás részletei a jelenik meg.

* A helyreállítási terv a parancsfájlok futtatása a VMM-szolgáltatásfiók kontextusában. Győződjön meg arról, hogy ennek a fióknak olvasási engedéllyel rendelkezik az a távoli megosztás, amelyen a parancsfájlt is található. Tesztelje az azonos szintű felhasználói jogosultságokkal a VMM szolgáltatásfiókját mint futtatandó szkriptként.
* VMM-parancsmagok a Windows PowerShell-modul érkeznek. A modul telepítve van a VMM-konzol telepítése során. A modul betöltése a parancsfájlba, használja a parancsfájl a következő parancsot: 

    `Import-Module -Name virtualmachinemanager`

    További információkért lásd: [Ismerkedés a Windows PowerShell és a VMM-ben](https://technet.microsoft.com/library/hh875013.aspx).
* Győződjön meg arról, hogy rendelkezik-e legalább egy erőforrástár-kiszolgálót a VMM-telepítésben. Alapértelmezés szerint a VMM-kiszolgáló erőforrástár útvonalán található helyileg a VMM-kiszolgálón. A mappa neve MSCVMMLibrary.

  Ha az erőforrástár-megosztás elérési útja nem távoli (vagy helyi azonban nem megosztott MSCVMMLibrary esetén), állítsa be a megosztás az alábbiak szerint használatával \\libserver2.contoso.com\share\ példaként:
  
  1. Nyissa meg a Beállításszerkesztőt, és folytassa a **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure hely Recovery\Registration**.

  2. Módosítsa az értéket a **ScriptLibraryPath** való  **\\\libserver2.contoso.com\share\\**. Adja meg a teljes Tartománynevet. Adja meg a megosztás helyéhez engedélyeket. Ez az a csomópont, a megosztáshoz. A VMM-ben a gyökércsomópont kereséséhez nyissa meg a gyökércsomópont a könyvtárban. A megnyíló elérési út, az útvonal gyökerének. Ez az az elérési utat, amely a változóban kell használnia.

  3. A parancsprogram teszteléséhez egy, a VMM-szolgáltatásfiók azonos szintű felhasználói jogosultságokkal rendelkező felhasználói fiókkal. Ezen jogosultság segítségével ellenőrzi, hogy önálló, tesztelt parancsfájlok futtatása ugyanúgy, mint a helyreállítási terv futnak. A VMM-kiszolgálón állítsa be a végrehajtási házirend kihagyásához a következő:

     a. Nyissa meg a **64 bites Windows PowerShell** konzolt rendszergazdaként.
     
     b. Adja meg **Set-executionpolicy Mellőzés**. További információkért lásd: [a Set-ExecutionPolicy parancsmag használatával](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Állítsa be **Set-executionpolicy Mellőzés** csak a 64 bites PowerShell-konzolt. Állítja be azt a PowerShell-konzol 32 bites, a parancsfájlok nem futnak.

## <a name="add-the-script-to-the-vmm-library"></a>A szkript hozzáadása a VMM-erőforrástárban

Ha a forrás VMM-helyről, létrehozhat egy parancsfájlt a VMM-kiszolgálón. Ezután vegye fel a parancsfájl a tervet.

1. Az erőforrástár-megosztásban hozzon létre egy új mappát. Ha például \<VMM-kiszolgáló neve > \MSSCVMMLibrary\RPScripts. A mappa helye a forrás és cél a VMM-kiszolgálókon.
2. A parancsprogram létrehozásához. Például nevezze el a szkript RPScript. Győződjön meg arról, hogy a parancsfájl a várt módon működik-e.
3. Helyezze el a szkriptet a \<VMM-kiszolgáló neve > \MSSCVMMLibrary mappát a forrás- és VMM-kiszolgálókon.

## <a name="add-the-script-to-a-recovery-plan"></a>A szkript hozzáadása a helyreállítási terv

Miután hozzáadta a virtuális gépek vagy a replikációs csoportok egy helyreállítási tervhez és a terv létre, a parancsfájl is hozzáadhat a csoporthoz.

1. Nyissa meg a helyreállítási tervben.
2. Az a **lépés** válasszon ki egy elemet. Ezután válassza ki vagy **parancsfájl** vagy **manuális műveletek**.
3. Adja meg, hogy a parancsfájl vagy a művelet előtt vagy után a kijelölt elem hozzáadásához. A parancsfájl pozícióját felfelé vagy lefelé áthelyezéséhez jelölje ki a **feljebb** és **lejjebb** gombok.
4. Ha egy új VMM-ben, válasszon **a VMM-szkript feladatátvételi**. A **parancsfájl elérési útján**, adja meg a relatív elérési utat a megosztáshoz. Adja meg például **\RPScripts\RPScript.PS1**.
5. Ha hozzáad egy Azure Automation-runbook, adja meg az Automation-fiókot, ahol a runbook is található. Ezután válassza ki a használni kívánt Azure-forgatókönyv parancsprogramja.
6. Győződjön meg arról, hogy a parancsfájl a várt módon működik-e, tegye a helyreállítási terv feladatátvételi tesztet.


## <a name="next-steps"></a>További lépések
* Tudjon meg többet [futó feladatátvételi teszteket](site-recovery-failover.md).

