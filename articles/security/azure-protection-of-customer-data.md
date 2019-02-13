---
title: Az Azure-ban tárolt ügyféladatok védelme
description: Ez a cikk foglalkozik, hogyan védi az Azure a vásárlói adatokat.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 49615dcb2f077d2e1d8b93a4bb900b435e4c87bf
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104490"
---
# <a name="azure-customer-data-protection"></a>Az Azure vásárlói adatok védelmére   
A Microsoft üzemeltetési és támogató személyzete alapértelmezés szerint nem férhet hozzá a vevőadatokhoz. Ha engedélyezett a hozzáférés a vásárlói adatokhoz, a rendszer vezetői jóváhagyásra szükség és a hozzáférés van gondosan felügyelt naplózza. A hozzáférés-vezérlésre vonatkozó követelményeket a következő Azure-biztonsági házirendet állítja be:

- Nem érhető el az ügyféladatokat, alapértelmezés szerint.
- Nincsenek felhasználói vagy rendszergazdai fiókok ügyfél virtuális gépeken (VM).
- Adja meg a minimális jogosultság; feladat végrehajtásához szükséges naplózási és hozzáférési kérelmek naplózása.

Az Azure támogatási csoporthoz rendelt egyedi vállalati Active Directory-fiókok a Microsoft által. Az Azure a Microsoft vállalati Active Directory, a Microsoft információkat technológia (MSIT), a kulcsadatokat rendszerekhez való hozzáférésének felügyelt támaszkodik. A multi-factor authentication megadása kötelező, és a hozzáférés csak a biztonságos konzolok.

Minden hozzáférési kísérleteket monitorozza, és a egy alapvető házirendcsoport jelentések keresztül is megjeleníthetők.

## <a name="data-protection"></a>Adatvédelem
Azure köszönhetően a felhasználók erős az adatbiztonságot, alapértelmezett vagy felhasználói közül.

**Az adatok elkülönítése**: Az Azure több-bérlős szolgáltatás, ami azt jelenti, hogy több ügyfél központi telepítések és ugyanazokon a hardvereszközökön tárolt virtuális gépek. Azure logikai elkülönítés használatával elkülönítheti az egyes ügyféladatot átviheti az adatokat, mások. Elkülönítése biztosítja a méretezési csoport és a több-bérlős szolgáltatások gazdasági előnyeinek ellenőrizteti megakadályozza, hogy ügyfelek hozzáférjenek egymás adataihoz.

**Inaktív adatok védelmére**: Ügyfelek felelőssége annak biztosítása, hogy az Azure-ban tárolt adatok titkosítva van-e a szabványoknak megfelelően. Az Azure számos titkosítási lehetőségeket, így a vevők rugalmasan választhatja ki az igényeinek leginkább megfelelő megoldást kínál. Az Azure Key Vault segítségével az ügyfelek könnyedén tarthatja kézben a felhőalapú alkalmazások és szolgáltatások által az adatok titkosításához használt kulcsokat. Az Azure Disk Encryption lehetővé teszi a vevők titkosíthatják a virtuális gépeket. Az Azure Storage Service Encryption lehetővé teszi kerül az ügyfél storage-fiók összes adat titkosítását.

**Adatok védelmére átvitel**: Felhasználók saját virtuális Gépeiket és a végfelhasználók közötti adatforgalomra is titkosítást. Az Azure védi az adatokat átvitel közben, illetve a külső összetevők és az adatok átvitel belső használatra, például két virtuális hálózat között. Az Azure az iparági szabványnak megfelelő Transport Layer Security (TLS) 1.2-es vagy újabb protokollt használja az 2048 bites RSA/SHA256-titkosítási kulcs, CESG/NCSC, által javasolt módon közötti kommunikáció titkosításához:

- Az ügyfél és a felhőben.
- Belsőleg az Azure és a közötti adatközpontok.

**Titkosítási**: Storage-ban, és az átvitt adatok titkosítása titoktartásra és az adatok sértetlenségének biztosítása érdekében ajánlott eljárásként ügyfelek általi is telepíthető. Az ügyfelek számára a kommunikáció védelméhez, az internetről, és akár az Azure-ban üzemeltetett virtuális gépek közötti SSL használatára az Azure cloud services konfigurálása egyszerű.

**Adatredundancia**: A Microsoft biztosítja, hogy adatok védelme a kibertámadás esetén vagy az egy kínai adatközpont fizikai sérülés esetén. Ügyfelek is választhat:

- Az ország tárolási megfelelőségi vagy késés kapcsolatos szempontokat.
- Tárolási ki az országot, biztonsági vagy vész-helyreállítási céllal.

Adatok replikálható a redundancia érdekében egy kiválasztott földrajzi területen belül, de nem lehet megadni azon kívül jelenik meg. Ügyfelek replikál adatokat, többek között a másolatokat és száma és helye replikációs adatközpontok több lehetősége van.

A tárfiók létrehozásakor válassza ki a következő replikációs lehetőségek egyikét:

- **Helyileg redundáns tárolás (LRS)**: A helyileg redundáns tárolás három másolatot tart fenn adatairól. A rendszer egy régió egyetlen létesítményén belül háromszor replikálja az LRS-t. LRS védi az adatokat a normál hardverleltárfájlok hibák után, de nem létesítményt hiba.
- **Zónaredundáns tárolás (ZRS)**: A zónaredundáns tárolás három másolatot tart fenn adatairól. A ZRS rendszer háromszor replikálja az LRS-nél nagyobb tartósságot biztosítanak két vagy három intézményben. A replikáció egy adott régión belül, vagy két régióban is. A ZRS segítségével, győződjön meg arról, hogy az adatok tartós, egy adott régión belül.
- **Georedundáns tárolás (GRS)**: Amikor létrehoz egy tárfiókot, a georedundáns tárolás alapértelmezés szerint engedélyezve van. A GRS hat másolatot tart fenn adatairól. A grs Tárolással az adatok rendszer háromszor replikálja az elsődleges régióban. Az adatok is háromszor replikálja a másodlagos régióban több száz mérfölddel erről az elsődleges régióban, így a legmagasabb szintű tartósságot. Az elsődleges régióban hiba esetén az Azure Storage átadja a feladatokat a másodlagos régióba. GRS segít az adatok tartósságának biztosítása két külön régióban.

**Adatok megsemmisítésének**: Amikor ügyfelek törli az adatokat, vagy hagyja meg az Azure, Microsoft követi felülírják a tárolási erőforrások, mielőtt azok újbóli, valamint a fizikai megsemmisítése leszerelt hardver vonatkozó szigorú szabványokat. A Microsoft végrehajtja az adatok egy teljes törlését, ügyfélkérés és a szerződés felmondása.

## <a name="customer-data-ownership"></a>Vásárlói adatok tulajdonjoga
A Microsoft nem vizsgálja meg, hagyja jóvá vagy figyelheti az alkalmazásokat, amelyek az ügyfelek üzembe helyezése az Azure-bA. Ezenkívül a Microsoft nem tudja típusú adatok ügyfél dönt az Azure-ban tárolja. A Microsoft nem tulajdonjogának adatokat meg kell adni az Azure-bA ügyfélinformációkat keresztül.

## <a name="records-management"></a>Rekordkezelés
Az Azure háttér-adatok belső rekordok-megőrzési követelményei hozott létre. Ügyfelek felelőssége saját rekordok megőrzési követelményeinek. Az Azure-ban tárolt rekordok ügyfelei felelősek az adatok kinyeréséhez és az Azure-on kívül a tartalom megőrzése az ügyfél által megadott megőrzési ideig.

Az Azure lehetővé teszi, hogy az ügyfelek számára, hogy az adatok exportálása és a naplózási jelentéseket a termék. A export helyben menti, és megőrizheti az adatait egy felhasználó által meghatározott megőrzési időszakot.

## <a name="electronic-discovery-e-discovery"></a>Elektronikus felderítés (ediscovery)
Az Azure-ügyfelek az Azure-szolgáltatások használata az elektronikus felderítés követelményeknek megfelelő felelős. Ha Azure-ügyfeleknek meg kell őrizniük a vásárlói adatokat, azok előfordulhat, hogy exportálása és az adatok helyi. Ügyfelek emellett az Azure ügyfélszolgálatának részleg használói kérhetnek exportálásai adataikat. Amellett, hogy lehetővé teszi az ügyfelek számára az adatok exportálása, az Azure széles körű naplózás és figyelés belsőleg hajt végre.

## <a name="next-steps"></a>További lépések
A Microsoft használ az Azure-infrastruktúra secure kapcsolatos további információkért lásd:

- [Azure létesítményekben, a helyi és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure information rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [Az Azure SQL Database biztonsági funkciók](azure-infrastructure-sql.md)
- [Azure éles környezetben való üzemeltetés és a felügyelet](azure-infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](azure-infrastructure-monitoring.md)
- [Az Azure infrastruktúra-integritás](azure-infrastructure-integrity.md)
