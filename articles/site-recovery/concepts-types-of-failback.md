---
title: "Az Azure Site Recovery feladatátvételi |} Microsoft Docs"
description: "Ez a cikk feladat-visszavétel és figyelmeztetések vissza a helyszíni az Azure Site Recovery szolgáltatásban a megfelelő működése során figyelembe kell venni a különböző típusainak áttekintése."
services: site-recovery
author: rajani-janaki-ram
manager: guaravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanki
ms.openlocfilehash: 372a7867b47960338d7a1bf7e646fb9fffbe72e1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="overview-of-failback"></a>Feladat-visszavétel áttekintése

Miután az Azure-bA rendelkezik nem sikerült keresztül, sikertelen lehet a helyszíni webhelyre. Nincsenek két különböző típusú feladat-visszavétel lehetséges az Azure Site Recovery szolgáltatással: 

- Az eredeti helyén sikertelen 
- Visszaadják feladataikat másik helyre

Ha VMware virtuális gépek feladatátvételt, akkor sikertelen lehet az azonos forrás helyszíni virtuális gépre Ha továbbra is létezik. Ebben a forgatókönyvben csak a módosítások vissza replikálódnak. Ebben a forgatókönyvben az úgynevezett **eredeti helyre történő helyreállítást**. Ha a helyszíni virtuális gép nem létezik, a forgatókönyv, egy **eltérő helyre történő helyreállítást**.

> [!NOTE]
> Csak a feladat-visszavétel az eredeti vCenter és a konfigurációs kiszolgáló is. Nem telepít egy új konfigurációs kiszolgálót, majd vissza használata sikertelen. Is akkor nem adható hozzá egy új vCenter a meglévő konfigurációs kiszolgáló és a feladat-visszavétel az új vCenter.

## <a name="original-location-recovery-olr"></a>Eredeti helyre történő helyreállítást (OLR)
Ha visszaadják feladataikat az eredeti virtuális gép, a következő feltételeknek kell teljesülniük kell:

* Ha a virtuális gép vCenter-kiszolgáló felügyeli, a fő célkiszolgáló ESX-gazdagépet a virtuális gép hozzáférése kell rendelkeznie.
* Ha a virtuális gépet az ESX-gazdagépen, de nem felügyeli a vCenter, majd a merevlemezt a virtuális gép található a fő célkiszolgáló gazdagép el tudja érni adattárolót kell lennie.
* Ha a virtuális gép ESX-gazdagépen, és nem használja a vCenter, majd el kell végeznie az ESX-gazdagép, a fő célkiszolgáló felderítése ahhoz, hogy lássa el újból védelemmel. Ez igaz, ha a feladat-visszavétele vissza fizikai kiszolgálók túl.
* Sikertelen lehet virtuális tárolóhálózat (vSAN) vagy egy lemezt, amely alapján a mapping (RDM), ha a lemezek már létezik, és a helyszíni virtuális gép csatlakozik nyers eszköz.

> [!IMPORTANT]
> Fontos disk.enableUUID= TRUE engedélyezése, hogy a feladat-visszavétel során a Azure Site Recovery szolgáltatás nem lehet azonosítani az eredeti vmdk-fájl a virtuális gépen, amelyre a függőben lévő módosításokat fog szerepelni. Ha a változó értéke nem lehet igaz, akkor a szolgáltatás megpróbálja azonosítsa a megfelelő helyszíni VMDK a egy elérhetőség alapján történik. Ha a jobb oldali vmdk-fájl nem található, létrehoz egy extra lemezt, és az adatok továbbítása, amely lekérdezi bejegyzésre.

## <a name="alternate-location-recovery-alr"></a>Másik helyre történő helyreállítást (ALR)
Ha a helyszíni virtuális gép nem létezik a virtuális gép újbóli védelméhez előtt, a forgatókönyv neve egy másik helyre történő helyreállítást. A védelem-újrabeállítási munkafolyamat újra létrehozza a helyszíni virtuális gépet. Ennek hatására a teljes adatok letöltése.

* Ha nem vissza egy másik helyre, a virtuális gép helyre lett állítva a azonos ESX-gazdagépet, amelyre telepítve van a fő célkiszolgálón. Az adattároló lemez létrehozásához használt lesz az azonos adattárral választotta, amikor a virtuális gép újbóli védelméhez.
* Sikertelen lehet a vissza csak egy virtuális gép fájlrendszer (virtuálisgép-Fájlrendszereinek) vagy a vSAN-adattároló. Ha egy RDM, védelem-újrabeállítási és feladat-visszavétel fog működni.
* Védelem-újrabeállítási magában foglalja egy nagy kezdeti adatátvitelt, amely a változtatások követi. Ez a folyamat létezik, mert a virtuális gép nem létezik a helyszínen. A teljes adatokat tartalmaz vissza replikálni. A védelem-újrabeállítási veszik az eredeti helyre történő helyreállítás több időt is.
* Nem utasíthat el vissza az RDM-alapú lemezek. Új virtuálisgép-lemezeket (VMDKs) csak a virtuálisgép-Fájlrendszereinek/vSAN adattárolót hozhatók létre.

> [!NOTE]
> A fizikai gép, ha az Azure-ba, a feladatátvételt is sikertelen biztonsági csak a VMware virtuális gépként. Ez a következő ugyanabban a munkafolyamatban, mint a másodlagos hely helyreállítását. Győződjön meg arról, hogy legalább egy fő célkiszolgáló és a szükséges ESX/ESXi-gazdagépek, amelyhez a feladat-visszavételt kell felderíteni.

## <a name="next-steps"></a>További lépések

Kövesse a lépéseket hajtsa végre a [feladat-visszavételi művelet](vmware-azure-failback.md).

