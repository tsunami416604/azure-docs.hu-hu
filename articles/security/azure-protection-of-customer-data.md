---
title: Az Azure-ban felhasználói adatok védelme
description: Ez a cikk foglalkozik, hogyan Azure felhasználói adatok védelmét.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 9a3b00e39f78f65b05b7d730447440d481979539
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102322"
---
# <a name="protection-of-customer-data-in-azure"></a>Az Azure-ban felhasználói adatok védelme   
A Microsoft üzemeltetési és támogató személyzete alapértelmezés szerint nem férhet hozzá a vevőadatokhoz. Felhasználói adatok hozzáférésének megadását vezetőségi jóvá kell, és majd hozzáférés gondosan felügyelt, és a rendszer naplózza. A hozzáférés-vezérlési követelményeinek létrehozása a következő Microsoft Azure biztonsági házirend alapján történik:

- Nem érhető el alapértelmezés szerint a felhasználói adatok
- Az ügyfél virtuális gépek nem felhasználói vagy rendszergazdai fiók
- Adja meg a feladat elvégzése; szükséges legalacsonyabb jogosultsági szint naplózási és hozzáférési kérelmek naplózása

Microsoft Azure technikai támogatási csoporthoz rendelt egyedi vállalati AD-fiókok a Microsoft által. A Microsoft Azure a Microsoft vállalati Active Directory, MSIT történő hozzáférés szabályozása érdekében fontos információk rendszerek által kezelt támaszkodik. Többtényezős hitelesítés szükséges, és csak hozzáférés biztonságos konzol.

Minden hozzáférések figyeli, és egy alapvető házirendcsoport jelentések keresztül jeleníthető meg.

## <a name="data-protection"></a>Adatvédelem
Azure köszönhetően a felhasználók erős adatbiztonság – mind alapértelmezés szerint, és a felhasználói beállítások szerint.

**Az adatok elkülönítése** -Azure a több-bérlős szolgáltatást, ami azt jelenti, hogy ugyanazon a fizikai hardveren több ügyfél központi telepítések és a virtuális gépek kell tárolni. Azure logikai elkülönítés használatával elkülönítse az adatok mások egyes ügyfelek adatait. Elkülönítése biztosít a méretezés és a több-bérlős szolgáltatások gazdasági előnyei szigorú megakadályozza az ügyfél egy másik adatok elérése közben.

**Az adatvédelem nyugalmi** -ügyfelek felelőssége annak biztosítása, hogy az Azure-ban tárolt adatok titkosítása az előírásoknak. Az Azure számos különböző titkosítási képességeit, így ügyfelei rugalmasan válassza ki az igényeinek leginkább megfelelő megoldást kínál. Az Azure Key Vault segítségével az ügyfelek könnyen az adatok titkosítása a felhőalapú alkalmazások és szolgáltatások által használt kulcsok irányítást tarthat fenn. Az Azure Disk Encryption lehetővé teszi az ügyfelek virtuális gépek titkosításához. Az Azure Storage szolgáltatás titkosítási lehetővé teszi egy ügyfél tárfiókjával csoportosítson összes adatot titkosítják.

**Az átvitel közbeni adatvédelem** -ügyfelek is engedélyezheti a titkosítást a saját virtuális gépek és a végfelhasználók közötti forgalmat. Azure védi az adatokat átvitel közben, vagy a külső összetevőket, és az adatok átmenő belső, például két virtuális hálózatok közötti. Azure 2048 bites RSA/SHA-256 titkosítási kulcsokkal, ajánlott által CESG/NCSC, mint a szabványos Transport Layer Security (TLS) 1.2-es vagy újabb operációs rendszerre protokoll közötti kommunikáció titkosítására használja:

- az ügyfél és a felhő
- belső Azure rendszerek és adatközpontok között

**Titkosítási** -titkosítás az adatok tárolási és átvitel közben elvégezhető az ügyfelek titkosítás és az adatok integritásának biztosításához az ajánlott eljárás. Az ügyfelek számára a kommunikáció védelméhez az internetről érkező SSL használatára az Azure felhőszolgáltatások konfigurálása egyszerű, és akár az Azure közötti üzemeltetett virtuális gépeket.

**Adatredundanciát** -a Microsoft biztosítja az adatok védelmét, ha egy cyberattack vagy a fizikai sérüléstől adatközpontba. Az ügyfelek is választhat:

- megfelelőségi vagy késés szempontok az ország-tároló
- biztonsági vagy katasztrófa utáni helyreállításra ki az ország-tároló

Adatok előfordulhat, hogy replikálja a redundancia érdekében egy kijelölt földrajzi területen, de a rendszer nem továbbít azon kívül. Az ügyfelek adatreplikálás, beleértve a példányszám és száma és helye replikációs adatközpontok több lehetősége van.

A tárfiók létrehozásakor kell választania a következő replikációs lehetőségek egyikét:

- Helyileg redundáns tárolás (LRS) A helyileg redundáns tárolás három másolatot tart fenn adatairól. A rendszer egy régió egyetlen létesítményén belül háromszor replikálja az LRS-t. Az LRS megvédi adatait a normál hardverhibáktól, de nem nyújt védelmet a létesítményt érintő hibák ellen.
- Zónaredundáns tárolás (ZRS). A zónaredundáns tárolás három másolatot tart fenn adatairól. A ZRS rendszer háromszor replikálja az LRS-nél nagyobb tartósságot biztosít két vagy három intézményben között. A replikáció egy vagy két régióban. A ZRS biztosítja az adatok tartósságát egyetlen régión belül.
- Georedundáns tárolás (GRS). Amikor létrehoz egy tárfiókot, a georedundáns tárolás alapértelmezés szerint engedélyezve van. A GRS hat másolatot tart fenn adatairól. A GRS az adatok rendszer háromszor replikálja az elsődleges régióban. Az adatok is háromszor replikálja az elhagyja az elsődleges régióban, így a legmagasabb szintű tartósságot miles egy másodlagos régióban több száz. Ha az elsődleges régióban hiba történne, az Azure Storage feladatátvételt hajt végre a másodlagos régióba. A GRS biztosítja az adatok tartósságát két külön régióban.

**Adatok megsemmisítésének** – Ha az ügyfelek törli az adatokat, vagy hagyja Azure, a Microsoft következő szigorú szabványait felülírja a tárolási erőforrások újbóli, valamint a leszerelt hardver fizikai megsemmisítése előtt. Microsoft végrehajtja az adatok teljes törlését a felhasználói kérelem és a szerződés megszüntetése.

## <a name="customer-data-ownership"></a>Felhasználói adatok tulajdonjoga
A Microsoft nem vizsgálja meg, hagyja jóvá vagy figyelni, hogy az ügyfelek központi telepítése az Azure-alkalmazásokat. Ezenkívül a Microsoft nem tudja, milyen adatokat ügyfelek válassza az Azure-ban tárolja. A Microsoft nem tulajdonosi adatok az Azure megadott ügyfél-információ keresztül.

## <a name="records-management"></a>Rekordok kezelése
Azure létesített háttéradatokkal belső rekordok megőrzési követelményei. Az ügyfelek felelőssége saját rekordok megőrzési vonatkozó követelmények azonosításának. Az Azure-ban tárolt rekordok az ügyfél felelős az adatok kinyeréséhez, valamint az Azure-on kívüli tartalom megőrzése az ügyfél által megadott megőrzési ideig.

Azure teszi lehetővé az ügyfél exportálhatja az adatokat, és jelentéseket a termék naplózására. A kivitel helyileg menti, és tartsa meg az adatai egy felhasználó által meghatározott megőrzési időszakot.

## <a name="electronic-discovery-e-discovery"></a>Elektronikus felderítés (elektronikus felderítés)
Az Azure ügyfelei felelősek az elektronikus felderítés követelményeinek megfelelő azok használata Azure-szolgáltatásokhoz. Ha az Azure felhasználóinál meg kell őriznie az ügyféladatokat, akkor előfordulhat, hogy exportálni, és helyileg az adatok mentése. Az ügyfelek emellett Azure ügyfél-támogatási részleg használói kérhetnek exportálja az adatokat. Amellett, hogy az ügyfelek számára az adatok exportálása lehetővé, a Azure széles körű naplózás és figyelés belső végez.

## <a name="next-steps"></a>További lépések
Microsoft funkciója az Azure-infrastruktúra védelméhez kapcsolatos további információkért lásd:

- [Az Azure létesítményekben, a helyszíni és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure információk rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [A Microsoft Azure SQL Database biztonsági funkciói](azure-infrastructure-sql.md)
- [Az Azure éles műveletek és kezelése](azure-infrastructure-operations.md)
- [Azure-infrastruktúra megfigyelése](azure-infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](azure-infrastructure-integrity.md)
