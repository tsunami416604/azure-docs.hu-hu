---
title: "Ismertető kiszámítani az Azure-adatbázis egység a MySQL |} Microsoft Docs"
description: "Azure MySQL-adatbázis: Ez a cikk ismerteti a számítási egységeket, és mi történik, ha a terhelést eléri a maximális számítási egység elveit."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 2c4894ae9a4235f9ced4a8d9b991238543646f53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="explaining-compute-units-in-azure-database-for-mysql"></a>Azure MySQL-adatbázis elmagyarázza számítási egység
Ez a témakör ismerteti a számítási egységeket, és mi történik, ha a terhelést eléri a maximális számítási egység fogalma.

## <a name="what-are-compute-units"></a>Mik azok a számítási egység
Számítási egység garantáltan MySQL-kiszolgáló egy Azure-adatbázis számára elérhető, a Processzor feldolgozási átviteli sebesség mértéke. A számítási egység mérőszáma kevert CPU és memória-erőforrásokat. Általánosságban elmondható 50 számítási egység egyenlő alapszintű felét. 100 számítási egység egy alapvető egyenlő. 2000 számítási egység 20 mag, hogy a kiszolgáló garantált adatfeldolgozást átviteli egyenlő.

A számítási egységenként memória mennyisége a Basic és Standard tarifacsomag szükséges van optimalizálva. Így dupla a számítási egység teljesítményszintjét növelésével megfelel így dupla a MySQL, hogy egyetlen Azure az adatbázis számára elérhető erőforrások készletét.

Például egy szabványos 800 számítási egység nyújt további 8 x CPU átviteli sebesség és a memória, mint egy normál 100 számítási egység konfigurációs. Azonban szabványos 100 számítási egység az azonos CPU átviteli alapvető 100 számítási egység képest adja meg, amíg a memória, a Standard tarifacsomag előre konfigurált mérete az alapvető árképzési szint beállított memória összeg. Ezért a Standard tarifacsomag biztosít a munkaterhelés jobb teljesítményt és kisebb tranzakció késést biztosít, mint az alapszintű árképzési szint a kiválasztott azonos számítási egységek.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>Hogyan lehet eldönteni, hogy a munkaterhelés számára szükséges számítási egységek száma?
Ha egy meglévő MySQL kiszolgáló és a helyszíni az áttelepíteni kívánt, vagy egy virtuális gépen úgy határozhatja meg számítási egységek számának becslése feldolgozási átviteli hány magok a számítási feladat által igényelt. 

Ha a meglévő helyszíni vagy a virtuális gép kiszolgálón jelenleg használja 4 mag (nélkül számbavételi CPU hyperthread), első lépésként 400 számítási egység az Azure-adatbázis MySQL-kiszolgáló konfigurálása. Számítási egység méretezhetők felfelé vagy lefelé dinamikusan munkaterhelés igényeitől függően és gyakorlatilag alkalmazás állásidő nélkül. 

A metrikák grafikon az Azure portálon figyelése, vagy Azure parancssori felület parancsait mérésére számítási egység írható. Figyelésére vonatkozó adatok gyűjtése le a számítási egység százalékos és számítási egység korlátot.

>[!IMPORTANT]
> Ha tárolási IOPS teljesen nem használhatók a legnagyobb, fontolja meg a figyelés, valamint a számítási egység kihasználtsági. A számítási egység előléptetése előfordulhat, hogy engedélyezheti a magasabb IO átviteli sebesség eléréséhez a teljesítménybeli szűk keresztmetszetek korlátozott CPU és memória eredő csökkentését.

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>Mi történik, ha szeretnék elérte a maximális számítási egység?
Teljesítményszintet kalibrálni és szabályozott erőforrások kijelölt árképzési és teljesítményszintet szintjére vonatkozó maximális határértékekig a adatbázis számítási feladatok futtatásához. 

Ha a terhelést eléri a maximális méretre vagy a számítási egység vagy kiosztott IOPS-korlátok vonatkoznak, továbbra is használhatja az erőforrásokat a megengedett maximális szintjét, de a lekérdezések létesített nagyobb késéseket. Ezek a korlátozások hibák, a munkaterhelés helyett lassulást eredményez, hacsak a lassulást el súlyos, amely lekérdezi túllépi az időkorlátot. 

Ha a terhelést eléri a kapcsolatok számának felső határai, explicit hibák aktiválódnak. Erőforrások korlátozások további információkért lásd: [MySQL az Azure Database korlátozásai](concepts-limits.md).

## <a name="next-steps"></a>Következő lépések
A tarifacsomagok további információkért lásd: [tarifacsomagok MySQL az Azure-adatbázis](./concepts-service-tiers.md).
