---
title: Az Azure éles hálózatának kezelése - Microsoft Azure
description: Ez a cikk azt ismerteti, hogy a Microsoft hogyan kezeli és működteti az Azure éles hálózatot az Azure-adatközpontok védelme érdekében.
services: security
documentationcenter: n
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
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: d41fe409b4a44a4c2af3670d76dd3a83a300feae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727118"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Az Azure éles hálózatának kezelése és működtetése    
Ez a cikk azt ismerteti, hogy a Microsoft hogyan kezeli és működteti az Azure éles hálózatot az Azure-adatközpontok védelme érdekében.

## <a name="monitor-log-and-report"></a>Figyel, naplóz és jelentés

Az Azure éles hálózat ának kezelése és üzemeltetése az Azure és az Azure SQL Database műveleti csapatai közötti összehangolt erőfeszítés. A csapatok számos rendszer- és alkalmazásteljesítmény-figyelő eszközt használnak a környezetben. Megfelelő eszközöket használnak a hálózati eszközök, kiszolgálók, szolgáltatások és alkalmazásfolyamatok figyelésére.

Az Azure-környezetben futó szolgáltatások biztonságos végrehajtásának biztosítása érdekében a műveleti csapatok több szintű figyelést, naplózást és jelentést valósítanak meg, beleértve a következő műveleteket:

- A Microsoft Monitoring Agent (MMA) elsősorban számos helyről gyűjti a figyelési és diagnosztikai naplóadatokat, például a hálóvezérlőről (FC) és a gyökéroperációs rendszerről (OPERÁCIÓS rendszer), és naplófájlokba írja azokat. Az ügynök végül leküldéses egy emésztett részhalmaza az információ egy előre konfigurált Azure-tárfiókba. Emellett a szabadon álló figyelési és diagnosztikai szolgáltatás beolvassa a különböző figyelési és diagnosztikai naplóadatokat, és összegzi az információkat. A figyelési és diagnosztikai szolgáltatás az adatokat egy integrált naplóba írja. Az Azure az egyéni azure-biztonsági figyelést használja, amely az Azure figyelési rendszer bővítménye. Olyan összetevőket tartalmaz, amelyek megfigyelik, elemzik és jelentik a platform különböző pontjairól származó biztonsági szempontból releváns eseményeket.

- Az Azure SQL Database Windows Fabric platform felügyeleti, üzembe helyezési, fejlesztési és üzemeltetési felügyeleti szolgáltatásokat nyújt az Azure SQL Database számára. A platform elosztott, többlépéses üzembe helyezési szolgáltatásokat, állapotfigyelést, automatikus javításokat és szolgáltatásverzió-megfelelőséget kínál. A következő szolgáltatásokat nyújtja:

   - Szolgáltatásmodellezési képességek hi-fi fejlesztői környezettel (adatközpont-fürtök drágák és szűkösek).
   - Egy kattintásos üzembe helyezési és frissítési munkafolyamatok a szolgáltatásrendszerindítási és -karbantartási műveletekhez.
   - Állapotjelentés automatikus javítási munkafolyamatokkal az öngyógyítás érdekében.
   - Valós idejű figyelési, riasztási és hibakeresési létesítmények az elosztott rendszer csomópontjaiközött.
   - A működési adatok és metrikák központosított gyűjtése az elosztott kiváltó okok elemzéséhez és a szolgáltatásbetekintéshez.
   - Operatív eszközök a telepítéshez, a változáskezeléshez és a figyeléshez.
   - Az Azure SQL Database Windows Fabric platform és a watchdog parancsfájlok folyamatosan futnak, és folyamatosan figyelik.

Ha bármilyen anomáliák fordulnak elő, az incidens-válasz folyamat, amelyet az Azure incidens triage csapat aktiválódik. A megfelelő Azure-támogatási személyzet értesítést kap, hogy válaszoljon az incidensre. A problémakövetés és -megoldás dokumentálva és kezelésében egy központi jegyrendszer ben történik. A rendszer rendelkezésre állási mutatói a titoktartási megállapodás (NDA) keretében és kérésre érhetők el.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Vállalati hálózat és többtényezős hozzáférés a termeléshez
A vállalati hálózati felhasználói bázis tartalmazza az Azure támogatási személyzetét. A vállalati hálózat támogatja a belső vállalati funkciókat, és hozzáférést biztosít az Azure ügyfélszolgálatához használt belső alkalmazásokhoz. A vállalati hálózat logikailag és fizikailag is elkülönül az Azure éles hálózattól. Az Azure munkatársai azure-munkaállomások és laptopok használatával érhetik el a vállalati hálózatot. Minden felhasználónak rendelkeznie kell egy Azure Active Directory (Azure AD) fiókkal, beleértve a felhasználónevet és a jelszót, a vállalati hálózati erőforrások eléréséhez. A vállalati hálózati hozzáférés Azure AD-fiókokat használ, amelyeket a Microsoft minden munkatársa, alvállalkozója és szállítója kiállít, és amelyet a Microsoft Information Technology kezel. Az egyedi felhasználói azonosítók a Microsoftnál betöltött foglalkoztatási állapotuk alapján különböztetik meg a munkatársakat.

A belső Azure-alkalmazásokhoz való hozzáférést az Active Directory összevonási szolgáltatásokkal (AD FS) való hitelesítés szabályozza. Az AD FS a Microsoft Information Technology által üzemeltetett szolgáltatás, amely biztonságos jogkivonat és felhasználói jogcímek alkalmazásával biztosítja a vállalati hálózati felhasználók hitelesítését. Az AD FS lehetővé teszi, hogy a belső Azure-alkalmazások hitelesítsék a felhasználókat a Microsoft vállalati active directory tartományával szemben. Az éles hálózat vállalati hálózati környezetből való eléréséhez a felhasználóknak többtényezős hitelesítéssel kell hitelesíteniük magukat.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni arról, hogy a Microsoft mit tesz az Azure-infrastruktúra védelme érdekében, olvassa el a következő témakört:

- [Az Azure létesítményei, helyiségei és fizikai biztonsága](physical-security.md)
- [Az Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Az Azure információs rendszer összetevői és határai](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure éles hálózat](production-network.md)
- [Az Azure SQL Database biztonsági szolgáltatásai](infrastructure-sql.md)
- [Az Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Az Azure infrastruktúra integritása](infrastructure-integrity.md)
- [Az Azure-beli ügyfelek adatainak védelme](protection-customer-data.md)
