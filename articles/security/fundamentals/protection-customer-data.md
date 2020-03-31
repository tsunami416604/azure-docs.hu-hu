---
title: Az ügyféladatok védelme az Azure-ban
description: Ez a cikk foglalkozik, hogyan védi az Azure az ügyféladatokat.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 741cbc82f2ed3ffffb553b146d981b4e35a273f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726681"
---
# <a name="azure-customer-data-protection"></a>Az Azure-beli ügyfelek adatainak védelme   
A Microsoft műveletei és támogatási személyzete alapértelmezés szerint nem fér hozzá az ügyféladatokhoz. Az ügyféladatokhoz való hozzáférés megadásakor vezetői jóváhagyásra van szükség, majd a hozzáférést gondosan kezelik és naplózzák. A hozzáférés-vezérlési követelményeket a következő Azure biztonsági szabályzat határozza meg:

- Alapértelmezés szerint nincs hozzáférés az ügyféladatokhoz.
- Nincs felhasználói vagy rendszergazdai fiók az ügyfél virtuális gépeken (Virtuális gépek).
- Adja meg a feladat elvégzéséhez szükséges legkisebb jogosultságot; naplózási és naplózási hozzáférési kérelmek.

Az Azure támogatási munkatársaihoz egyedi vállalati Active Directory-fiókokat rendel a Microsoft. Az Azure a Microsoft Information Technology (MSIT) által felügyelt Microsoft vállalati Active Directory ra támaszkodik a kulcsfontosságú információs rendszerekhez való hozzáférés szabályozásához. Többtényezős hitelesítés szükséges, és a hozzáférés csak biztonságos konzolokról érhető el.

Minden hozzáférési kísérlet figyelt, és egy alapjelentés-készleten keresztül jeleníthető meg.

## <a name="data-protection"></a>Adatvédelem
Az Azure erős adatbiztonságot nyújt az ügyfeleknek, mind alapértelmezés szerint, mind ügyfélként.

**Adatszegregáció:** Az Azure egy több-bérlős szolgáltatás, ami azt jelenti, hogy több ügyfél-telepítések és virtuális gépek ugyanazon a fizikai hardveren tárolják. Az Azure logikai elkülönítést használ az egyes ügyfelek adatainak elkülönítéséhez mások adataitól. A szegregáció a többbérlős szolgáltatások nagyságrendjét és gazdasági előnyeit biztosítja, miközben szigorúan megakadályozza, hogy az ügyfelek hozzáférjenek egymás adataihoz.

**Inin belüli adatvédelem:** Az ügyfelek felelősek annak biztosításáért, hogy az Azure-ban tárolt adatok a szabványaiknak megfelelően titkosítva legyenek. Az Azure a titkosítási képességek széles skáláját kínálja, így az ügyfelek rugalmasan választhatják meg az igényeiknek leginkább megfelelő megoldást. Az Azure Key Vault segítségével az ügyfelek könnyedén kézben tarthassák a felhőalapú alkalmazások és szolgáltatások által az adatok titkosításához használt kulcsokat. Az Azure Disk Encryption lehetővé teszi az ügyfelek számára a virtuális gépek titkosítását. Az Azure Storage Service Encryption lehetővé teszi az ügyfél tárfiókjába helyezett összes adat titkosítását.

**A továbbítás i-továbbítási adatvédelem:** Az ügyfelek engedélyezhetik a saját virtuális gépek és a végfelhasználók közötti forgalom titkosítását. Az Azure védi az adatokat az átvitel során, vagy a külső összetevők és adatok az átvitel belső, például két virtuális hálózatok közötti adatok. Az Azure a CESG/NCSC ajánlásának megfelelően az iparági szabványnak megfelelő Transport Layer Security (TLS) 1.2-es vagy újabb protokollt használja a 2048 bites RSA/SHA256 titkosítási kulcsokkal a CESG/NCSC ajánlásának megfelelően a következők közötti kommunikáció titkosítására:

- Az ügyfél és a felhő.
- Belsőleg az Azure-rendszerek és az adatközpontok között.

**Titkosítás:** A tárolás és az átvitel során lévő adatok titkosítását az ügyfelek az adatok titkosságának és integritásának biztosításához ajánlott eljárásként helyezhetik üzembe. Az ügyfelek egyszerűen konfigurálhatják az Azure felhőszolgáltatásait, hogy SSL-t használjanak az internetről, sőt az Azure által üzemeltetett virtuális gépek közötti kommunikáció védelmére.

**Adatredundancia: A**Microsoft segít az adatok védelmében, ha kibertámadás vagy fizikai sérülés történik egy adatközpontban. Az ügyfelek választhatnak:

- Az országon/régión belül tároló megfelelőségi vagy késési szempontok.
- Országon kívüli/régión kívüli tárolás biztonsági vagy vész-helyreállítási célokra.

Az adatok replikálhatók egy kiválasztott földrajzi területen belül redundancia esetén, de nem továbbíthatók azon kívül. Az ügyfelek több féle lehetőséget kínálnak az adatok replikálására, beleértve a másolatok számát, valamint a replikációs adatközpontok számát és helyét.

A tárfiók létrehozásakor válasszon az alábbi replikációs lehetőségek közül:

- **Helyileg redundáns tárolás (LRS)**: A helyileg redundáns tárolás az adatok három példányát tartja karban. A rendszer egy régió egyetlen létesítményén belül háromszor replikálja az LRS-t. Az LRS megvédi az adatokat a normál hardverhibáktól, de nem egyetlen létesítmény meghibásodásától.
- **Zónaredundáns tárolás (ZRS):** A zónaredundáns tárolás az adatok három példányát tartalmazza. A ZRS-t háromszor replikálják két-három létesítményben, hogy nagyobb tartósságot biztosítsanak, mint az LRS. A replikáció egyetlen régión vagy két régión belül történik. A ZRS segít biztosítani, hogy az adatok egyetlen régión belül is tartósak legyenek.
- **Georedundáns tárolás (GRS)**: A georedundáns tárolás alapértelmezés szerint engedélyezve van a tárfiókhoz, amikor létrehozza. A GRS hat másolatot tart fenn adatairól. A GRS segítségével az adatok replikálása háromszor az elsődleges régióban. Az adatok is replikálódik háromszor egy másodlagos régióban több száz mérföldre az elsődleges régiótól, amely a legmagasabb szintű tartósságot. Abban az esetben, ha az elsődleges régióban hiba, az Azure Storage átadja a másodlagos régió. A GRS segít biztosítani, hogy az adatok két különböző régióban is tartósak legyenek.

**Adatok megsemmisítése:** Amikor az ügyfelek adatokat törölnek, vagy elhagyják az Azure-t, a Microsoft szigorú szabványokat követ a tárolási erőforrások újbóli felhasználás előtti felülírására, valamint a leszerelt hardver fizikai megsemmisítésére. A Microsoft az ügyfél kérésére és a szerződés felmondásakor teljes adattörlést hajt végre.

## <a name="customer-data-ownership"></a>Ügyféladatok tulajdonjoga
A Microsoft nem vizsgálja, hagyja jóvá és nem figyeli azokat az alkalmazásokat, amelyeket az ügyfelek az Azure-ba telepítenek. Ezenkívül a Microsoft nem tudja, hogy az ügyfelek milyen típusú adatokat tárolnak az Azure-ban. A Microsoft nem követeli az adatok tulajdonjogát az Azure-ba megadott ügyféladatok felett.

## <a name="records-management"></a>Rekordkezelés
Az Azure belső rekordmegőrzési követelményeket állapított meg a háttéradatokhoz. Az ügyfelek felelősek saját rekordmegőrzési követelményeik azonosításáért. Az Azure-ban tárolt rekordok esetében az ügyfelek felelősek adataik kinyerésért és a tartalmuk megőrzéséért az Azure-on kívül egy ügyfél által megadott megőrzési időszakban.

Az Azure lehetővé teszi az ügyfelek számára, hogy adatokat exportáljanak és jelentéseket naplózzanak a termékből. Az exportálás helyileg mentésre kerül, hogy megőrizze az adatokat az ügyfél által meghatározott megőrzési időszakra.

## <a name="electronic-discovery-e-discovery"></a>Elektronikus felfedezés (e-felfedezés)
Az Azure-ügyfelek felelősek az e-felderítési követelményeknek való megfelelésért az Azure-szolgáltatások használata során. Ha az Azure-ügyfeleknek meg kell őrizniük az ügyféladataikat, exportálhatják és menthetik az adatokat helyileg. Emellett az ügyfelek kérhetik az adatok exportálását az Azure ügyfélszolgálati részlegétől. Amellett, hogy lehetővé teszi az ügyfelek számára az adataik exportálását, az Azure kiterjedt naplózást és figyelést is végez belsőleg.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni arról, hogy a Microsoft mit tesz az Azure-infrastruktúra védelme érdekében, olvassa el a következő témakört:

- [Az Azure létesítményei, helyiségei és fizikai biztonsága](physical-security.md)
- [Az Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Az Azure információs rendszer összetevői és határai](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure éles hálózat](production-network.md)
- [Az Azure SQL Database biztonsági szolgáltatásai](infrastructure-sql.md)
- [Az Azure éles üzemei és kezelése](infrastructure-operations.md)
- [Az Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Az Azure infrastruktúra integritása](infrastructure-integrity.md)
