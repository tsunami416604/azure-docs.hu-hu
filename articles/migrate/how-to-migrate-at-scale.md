---
title: Az áttelepítési gépek áttelepítésének automatizálása az Azure Áttelepítése szolgáltatásban
description: A parancsfájlok használatával nagyszámú gép áttelepítése az Azure Migrate szolgáltatásban
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: 317b6e8aa799b7982e9897c6a504d6092491c7ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196360"
---
# <a name="scale-migration-of-vms"></a>Virtuális gépek áttelepítésének méretezése 

Ez a cikk segít megérteni, hogyan parancsfájlok segítségével nagyszámú virtuális gép (VM) áttelepítése. Az áttelepítés méretezéséhez használja az [Azure Site Recovery](../site-recovery/site-recovery-overview.md)szolgáltatást. 

A Site Recovery-parancsfájlok letölthetők az [Azure PowerShell-minták](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) tárházán a GitHubon. A parancsfájlok segítségével a VMware, Az AWS, a GCP virtuális gépek és a fizikai kiszolgálók az Azure-ban felügyelt lemezekre telepíthetők át. Ezeket a parancsfájlokat a Hyper-V virtuális gépek áttelepítéséhez is használhatja, ha a virtuális gépeket fizikai kiszolgálóként telepíti át. Az Azure Site Recovery PowerShellt használó parancsfájlokat [itt](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell)dokumentálja.

## <a name="current-limitations"></a>Aktuális korlátozások
- Támogatás, amely csak a cél virtuális gép elsődleges hálózati adapterének statikus IP-címét adja meg
- A parancsfájlok nem veszik az Azure Hybrid Benefit-hez kapcsolódó bemeneteket, manuálisan kell frissítenie a replikált virtuális gép tulajdonságait a portálon

## <a name="how-does-it-work"></a>Hogyan működik?

### <a name="prerequisites"></a>Előfeltételek
A kezdés előtt a következő lépéseket kell tennie:
- Annak ellenőrzése, hogy a Site Recovery-tároló az Azure-előfizetésben van-e létrehozva
- Annak ellenőrzése, hogy a konfigurációs kiszolgáló és a folyamatkiszolgáló telepítve van-e a forráskörnyezetben, és hogy a tároló képes-e felderíteni a környezetet
- Replikációs házirend létrehozása és a konfigurációs kiszolgálóhoz társítása
- Győződjön meg arról, hogy hozzáadta a virtuális gép felügyeleti fiókját a konfigurációs kiszolgálóhoz (amely a helyszíni virtuális gépek replikálására szolgál)
- Győződjön meg arról, hogy az Azure-ban létrehozott célösszetevők
    - Cél erőforráscsoport
    - Céltárfiók (és erőforráscsoportja) – Prémium szintű tárfiók létrehozása, ha prémium szintű felügyelt lemezekre szeretne áttérni
    - Gyorsítótár-tárfiók (és erőforráscsoportja) – Hozzon létre egy standard szintű tárfiókot ugyanabban a régióban, mint a tároló
    - Cél virtuális hálózat feladatátvételhez (és erőforráscsoportjához)
    - Cél alhálózat
    - Célvirtuális hálózat a tesztfeladat-átvételhez (és erőforráscsoportjához)
    - Rendelkezésre állási csoport (ha szükséges)
    - Célhálózati biztonsági csoport és erőforráscsoportja
- Győződjön meg arról, hogy a cél virtuális gép tulajdonságairól döntött
    - Cél virtuális gép neve
    - A virtuális gépek célmérete az Azure-ban (az Azure Migrate felmérés használatával eldönthető)
    - A virtuális gép elsődleges hálózati adapterének privát IP-címe
- A parancsfájlok letöltése az [Azure PowerShell-minták](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) tárházából a GitHubon

### <a name="csv-input-file"></a>CSV bemeneti fájl
Miután az összes előfeltétel befejeződött, létre kell hoznia egy CSV-fájlt, amely minden áttelepíteni kívánt forrásgéphez adatokat tartalmazó adatokat. A bemeneti CSV-nek rendelkeznie kell egy fejlécsorral a bemeneti részletekkel, és egy sort, amely tartalmazza az áttelepítendő gépek részleteit. Az összes szkriptek célja, hogy működjön ugyanazon a CSV fájlt. A parancsfájlok mappájában egy minta CSV-sablon érhető el.

### <a name="script-execution"></a>Parancsfájl-végrehajtás
Miután a CSV készen áll, a következő lépéseket hajthatja végre a helyszíni virtuális gépek áttelepítésének végrehajtásához:

**Lépés #** | **Parancsfájl neve** | **Leírás**
--- | --- | ---
1 | asr_startmigration.ps1 | Engedélyezze a replikációt a csv-ben felsorolt összes virtuális gép számára, a parancsfájl létrehoz egy CSV-kimenetet az egyes virtuális gépek feladatrészleteivel
2 | asr_replicationstatus.ps1 | Ellenőrizze a replikáció állapotát, a parancsfájl létrehoz egy csv-t az egyes virtuális gépek állapotával
3 | asr_updateproperties.ps1 | A virtuális gépek replikálása/védelme után ezzel a parancsfájllal frissítheti a virtuális gép (Számítási és hálózati tulajdonságok) céltulajdonságait.
4 | asr_propertiescheck.ps1 | Annak ellenőrzése, hogy a tulajdonságok megfelelően frissültek-e
5 | asr_testmigration.ps1 |  Indítsa el a teszt feladatátvétela a virtuális gépek szerepel a csv, a parancsfájl létrehoz egy CSV-kimenet a feladat részleteit az egyes virtuális gépek
6 | asr_cleanuptestmigration.ps1 | Miután manuálisan érvényesítette a sikertelennek tesztelt virtuális gépeket, ezzel a parancsfájllal megtisztíthatja a tesztfeladat-átvételi virtuális gépeket.
7 | asr_migration.ps1 | Nem tervezett feladatátvétel végrehajtása a csv-ben felsorolt virtuális gépek, a parancsfájl létrehoz egy CSV-kimenet a feladat részleteit az egyes virtuális gépek. A parancsfájl nem állítja le a helyszíni virtuális gépeket a feladatátvétel előtt, az alkalmazás konzisztenciája érdekében ajánlott manuálisan leállítani a virtuális gépeket a parancsfájl végrehajtása előtt.
8 | asr_completemigration.ps1 | A véglegesítési művelet végrehajtása a virtuális gépeken és az Azure Site Recovery entitások törlése
9 | asr_postmigration.ps1 | Ha azt tervezi, hogy hálózati biztonsági csoportokat rendel a hálózati adapterek hez a feladatátvétel után, ezt a parancsfájlt használhatja erre. NSG-t rendel a célvirtuális gép bármely hálózati adapteréhez.

## <a name="how-to-migrate-to-managed-disks"></a>Hogyan lehet áttelepíteni a felügyelt lemezekre?
A parancsfájl alapértelmezés szerint áttelepíti a virtuális gépeket az Azure-beli felügyelt lemezekre. Ha a megadott céltárfiók prémium szintű tárfiók, a prémium szintű felügyelt lemezek áttelepítés után jönnek létre. A gyorsítótár-tárfiók továbbra is lehet egy általános jogú fiók. Ha a céltárfiók egy normál tárfiók, a szabványos lemezek áttelepítés után jönnek létre. 

## <a name="next-steps"></a>További lépések

[További információ](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) a kiszolgálók Azure-ba való áttelepítéséről az Azure Site Recovery használatával
