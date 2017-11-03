---
title: "Ismertető kiszámítani az Azure-adatbázis egység a PostgreSQL |} Microsoft Docs"
description: "Azure-adatbázis PostgreSQL: Ez a cikk ismerteti a számítási egységeket, és mi történik, ha a terhelést eléri a maximális számítási egység elveit."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: dbb9f733455fa0492358b24b178c8c637ff08c71
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="explaining-compute-units-in-azure-database-for-postgresql"></a>Azure-adatbázis PostgreSQL elmagyarázza számítási egység
Ez a témakör ismerteti a számítási egységeket, és mi történik, amikor a számítási feladatok eléri a maximális szintet számítási egység fogalma.

## <a name="what-are-compute-units"></a>Mik azok a számítási egység
Számítási egység garantáltan PostgreSQL-kiszolgáló egy Azure-adatbázis számára elérhető, a Processzor feldolgozási átviteli sebesség mértéke. A számítási egység mérőszáma kevert CPU és memória-erőforrásokat. Általánosságban elmondható 50 számítási egység egyenlő alapszintű felét. 100 számítási egység egy alapvető egyenlő. 2000 számítási egység 20 mag, hogy a kiszolgáló garantált adatfeldolgozást átviteli egyenlő.

A számítási egységenként memória mennyisége a Basic és Standard tarifacsomag szükséges van optimalizálva. Így dupla a számítási egység teljesítményszintjét növelésével így dupla Processzor- és, hogy egyetlen Azure az adatbázis számára elérhető memória mennyisége a PostgreSQL megfelel.

Például egy szabványos 800 számítási egység nyújt további 8 x CPU átviteli sebesség és a memóriát, mint egy normál 100 számítási egység konfigurációs. Míg szabványos 100 számítási egység ugyanaz a CPU átviteli nyújt, mint 100 alapvető számítási egység, memória, a Standard tarifacsomag előre konfigurált azonban az alapvető árképzési szint beállított memória összeg. Ezért a Standard tarifacsomag biztosít a munkaterhelés jobb teljesítményt és kisebb tranzakció késést biztosít, mint az alapszintű árképzési szint a kiválasztott azonos számítási egységek.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>Hogyan lehet eldönteni, hogy a munkaterhelés számára szükséges számítási egységek száma?
Ha szeretné, hogy egy meglévő futtató helyszíni PostgreSQL-kiszolgáló áttelepítése, vagy egy virtuális gépen úgy határozhatja meg számítási egységek számának becslése feldolgozási átviteli hány magok a számítási feladatok kell. 

Ha a meglévő helyszíni vagy a virtuális gép kiszolgálón van jelenleg okhoz 4 mag (nélkül számbavételi CPU hyperthread), első lépésként 400 számítási egység az Azure-adatbázis PostgreSQL-kiszolgáló konfigurálása. Számítási egység dinamikusan méretezhetők felfelé vagy lefelé gyakorlatilag alkalmazás állásidő nélkül munkaterhelés igényeitől függően. 

A metrikák grafikon az Azure portálon figyelése, vagy Azure parancssori felület parancsait mérésére számítási egység írható. Figyelésére vonatkozó adatok gyűjtése le a számítási egység százalékos és számítási egység korlátot.

>[!IMPORTANT]
> Ha tárolási IOPS teljesen nem használhatók a legnagyobb, fontolja meg a figyelés, valamint a számítási egység kihasználtsági. A számítási egység előléptetése magasabb IO átviteli tehetik lehetővé által korlátozott CPU és memória miatt a teljesítménybeli szűk keresztmetszetek csökkentését.

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>Mi történik, ha szeretnék elérte a maximális számítási egység?
Teljesítményszintet kalibrálni és szabályozott erőforrások kijelölt árképzési és teljesítményszintet szintjére vonatkozó maximális határértékekig a adatbázis számítási feladatok futtatásához. 

Ha a számítási feladatok eléri a maximális méretre a számítási egység vagy a kiosztott IOPS, továbbra is használhatja az erőforrásokat a megengedett maximális szintjét, de a lekérdezések nincsenek valószínű, hogy a megnövekedett késések fordulnak elő. Ezek a korlátozások nem eredményeznek ki a hibákat, de ahelyett, hogy a terhelés lassulást kivéve, ha a lassulást válik súlyos, amely lekérdezi túllépi az időkorlátot. 

Ha a terhelést eléri a kapcsolatok számának felső határai, explicit hibák aktiválódnak. Erőforrások korlátozások további információkért lásd: [PostgreSQL Azure adatbázis korlátozásai](concepts-limits.md).

## <a name="next-steps"></a>Következő lépések
A tarifacsomagok további információkért lásd: [Azure-adatbázis a tarifacsomagok PostgreSQL](./concepts-service-tiers.md).
