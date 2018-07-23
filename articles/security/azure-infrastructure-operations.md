---
title: Azure éles környezetben való üzemeltetés és a felügyelet
description: Ez a cikk általános leírását, a felügyeleti és működtetése az Azure éles hálózati környezetben.
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
ms.openlocfilehash: 21ae81f1d8423a9d05208ec6d8c4f31d909d2f9f
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173159"
---
# <a name="azure-production-operations-and-management"></a>Azure éles környezetben való üzemeltetés és a felügyelet    
A felügyeleti és az éles Azure-hálózat művelet célja egy koordinált az üzemeltetési csapatok az Azure és az Azure SQL Database között. A teams használatát több rendszer és alkalmazás-teljesítményfigyelési eszköz a környezetben. És megfelelő eszközök használják a hálózati eszközök, kiszolgálók, szolgáltatások és alkalmazási folyamatok figyeléséhez.

Ahhoz, hogy az Azure-környezetben futó szolgáltatások biztonságos végrehajtásának, a üzemeltetési csapatok megvalósítása többszintű monitorozás, naplózás és jelentéskészítés, többek között a következő műveleteket:

- A Microsoft Monitoring Agent (MMA), elsősorban sok helyen, beleértve a hálóvezérlő (FC) és a gyökérszintű operációs rendszer (OS), figyelési és diagnosztikai adatait gyűjti össze, és írja a naplófájlokat. Az ügynök az adatokat egy emésztett részét végül leküldések előre konfigurált Azure storage-fiókra. Emellett a szabadon álló figyelési és diagnosztikai szolgáltatás beolvassa a különböző monitorozási és diagnosztikai naplóadatokat, és a tudnivalókat foglalja össze. A monitorozási és diagnosztikai szolgáltatás integrált naplóba beírja az információkat. Az Azure a személyre szabott Azure biztonsági figyelés, a rendszer figyelése az Azure kiterjesztése, amely használja. Figyelje meg, elemzéséhez és biztonsági profiljával kapcsolatos események különféle pontokról a platform jelentések összetevőket tartalmaz.

- Az Azure SQL Database Windows Fabric platformot biztosít a felügyeleti, üzembe helyezés, fejlesztési és üzemeltetési felügyeletet szolgáltatások az Azure SQL Database. A platform kínál elosztott, több lépésből álló-telepítési szolgáltatások, szolgáltatásállapot-figyelést, automatikus javítási és szolgáltatás verziója megfelelőségi. Ez a következő szolgáltatásokat nyújtja:

   - Szolgáltatás adatmodellezési képességekkel élethű fejlesztői környezetet (datacenter fürtök olyan költséges és szűkös).
   - Egykattintásos üzembe helyezéshez és a rendszerindítási szolgáltatás és a karbantartási frissítés munkafolyamatok.
   - Az automatikus javítási munkafolyamatra engedélyezéséhez az önjavítást szem előtt tartva reporting állapotát.
   - Valós idejű figyelés, riasztás és a létesítményekben hibakeresése egy elosztott rendszer csomópontjai között.
   - Operatív adatok és az elosztott alapvető metrikákat központi gyűjteményét elemzés és a szolgáltatás insight miatt.
   - Műveleti hibakeresését, üzembe helyezéshez változáskezelés és figyelését.
   - Az Azure SQL Database Windows Fabric platformot és a figyelő parancsfájlok folyamatosan fusson, és valós időben figyelheti.

Felismerheti a rendellenességeket fordulhat elő, ha az incidensmegoldási folyamatba az Azure incidens osztályozási csapat követ aktiválva van. Az incidens válaszol a megfelelő Azure-támogatási személyzet értesítést kap. Probléma- és névfeloldási dokumentált és kezelése a központi hibajegyalapú rendszert. Rendszer üzemidejét metrikák érhetők el a titoktartási szerződését (NDA) és a kérésre.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Vállalati hálózat és a többtényezős hozzáférés az éles környezetbe
A vállalati hálózat felhasználói bázis magában foglalja az Azure támogatási csoporthoz. A vállalati hálózat belső vállalati funkciók támogatja, és hozzáférést biztosít a belső alkalmazások által használt Azure ügyfélszolgálatához. A vállalati hálózaton fizikailag és logikailag is elkülönül az Azure éles hálózati környezetben. Az Azure munkatársai Azure munkaállomások és hordozható számítógépek hozzáférhetnek a vállalati hálózathoz. Valamennyi felhasználónak rendelkeznie kell egy Azure Active Directory (Azure AD-) fiók, beleértve a felhasználónevet és jelszót, a vállalati hálózati erőforrások eléréséhez. Vállalati hálózati hozzáférés az Azure AD-fiókokat, az összes Microsoft-alkalmazottak, alvállalkozók és beszállítók kiadott és kezeli a Microsoft informatikai használ. Egyedi felhasználói azonosítók tesz különbséget a csoporthoz a Microsoftnál. foglalkoztatottsági állapot alapján.

A belső Azure-alkalmazások hozzáférésének hitelesítéssel az Active Directory összevonási szolgáltatások (AD FS). Az AD FS üzemelteti, amely a vállalati hálózat felhasználók alkalmazása egy biztonságos token és a felhasználói jogcímek keresztül hitelesítést biztosít a Microsoft informatikai szolgáltatás a. Az AD FS lehetővé teszi, hogy a belső Azure-alkalmazások hitelesíteni tudja a felhasználókat a Microsoft vállalati active directory-tartományhoz. Szeretné elérni az éles hálózati környezetben a vállalati hálózati környezetben, a felhasználók a multi-factor authentication szolgáltatás használatával kell hitelesítenie.

## <a name="next-steps"></a>További lépések
A Microsoft használ az Azure-infrastruktúra secure kapcsolatos további információkért lásd:

- [Azure létesítményekben, a helyi és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure information rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [Az Azure SQL Database biztonsági funkciók](azure-infrastructure-sql.md)
- [Azure-infrastruktúra figyelése](azure-infrastructure-monitoring.md)
- [Az Azure infrastruktúra-integritás](azure-infrastructure-integrity.md)
- [Az Azure vásárlói adatok védelmére](azure-protection-of-customer-data.md)
