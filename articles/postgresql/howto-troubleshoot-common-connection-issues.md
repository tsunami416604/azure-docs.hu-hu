---
title: Kapcsolatok – Azure Database for PostgreSQL – Single Server –
description: Ismerje meg, hogyan háríthatja el a kapcsolatokkal kapcsolatos problémákat az Azure Database for PostgreSQL – Single Server szolgáltatással.
keywords: postgresql-kapcsolat,kapcsolati karakterlánc,kapcsolódási problémák,átmeneti hiba,csatlakozási hiba
author: jasonwhowell
ms.author: jasonh
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 593dc060ff92c7f4bc338e235dd1a0dd9f303ab3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767611"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---single-server"></a>Kapcsolati problémák elhárítása az Azure Database for PostgreSQL - Single Server szolgáltatással

A csatlakozási problémákat számos dolog okozhatja, többek között:

* Tűzfalbeállítások
* Csatlakozási időtúllépés
* Helytelen bejelentkezési adatok
* A PostgreSQL-erőforrások egyes Azure-adatbázisában elért maximális korlát
* A szolgáltatás infrastruktúrájával kapcsolatos problémák
* Karbantartás a szervizben
* A kiszolgáló számítási lefoglalása a virtuális magok számának méretezésével vagy egy másik szolgáltatási szintre való áthelyezéssel változik

Az Azure Database for PostgreSQL-hez való kapcsolódási problémák általában a következőképpen sorolhatók be:

* Átmeneti hibák (rövid életű vagy szakaszos)
* Állandó vagy nem átmeneti hibák (rendszeresen visszatérő hibák)

## <a name="troubleshoot-transient-errors"></a>Átmeneti hibák elhárítása

Átmeneti hibák fordulnak elő a karbantartás végrehajtásakor, a rendszer hibát észlel a hardverrel vagy a szoftverrel, vagy módosítja a kiszolgáló virtuális magjait vagy szolgáltatási szintjét. Az Azure Database for PostgreSQL szolgáltatás beépített magas rendelkezésre állású, és célja, hogy az ilyen típusú problémák automatikusan mérsékelhető. Az alkalmazás azonban rövid, általában legfeljebb 60 másodpercre megszakad a kapcsolata a kiszolgálóval. Egyes események időközben hosszabb időt vehet igénybe, például ha egy nagy tranzakció hosszú ideig tartó helyreállítást okoz.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Az átmeneti kapcsolódási problémák megoldásának lépései

1. Ellenőrizze a [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) minden olyan ismert kimaradások során, amikor a hibákat jelentett az alkalmazás.
2. Alkalmazások, amelyek egy felhőalapú szolgáltatáshoz, például az Azure Database for PostgreSQL-hez csatlakozik kell várni a tranziens hibákat, és valósítsa meg újrapróbálkozási logika ezeket a hibákat, ahelyett, hogy ezeket az alkalmazáshibák a felhasználók számára. Tekintse át [az Azure Database for PostgreSQL átmeneti kapcsolódási hibáinak kezelését](concepts-connectivity.md) az ajánlott eljárásokért és az átmeneti hibák kezeléséhez vezető tervezési irányelvekért.
3. Akiszolgáló az erőforráskorlátok hoz közeledtével a hibák átmeneti kapcsolódási problémának tűnhetnek. Lásd: [A PostgreSQL Azure-adatbázisának korlátai.](concepts-limits.md)
4. Ha a kapcsolódási problémák továbbra is fennállnak, vagy ha az az időtartam, amelyre az alkalmazás a hibába ütközik, meghaladja a 60 másodpercet, vagy ha egy adott napon a hiba több előfordulása jelenik meg, az [Azure-támogatás](https://azure.microsoft.com/support/options) **kérése** lehetőséget választva nyújtson be egy Azure-támogatási kérelmet.

## <a name="troubleshoot-persistent-errors"></a>Állandó hibák elhárítása

Ha az alkalmazás tartósan nem tud csatlakozni az Azure Database for PostgreSQL-hez, az általában az alábbiak egyikével kapcsolatos problémát jelez:

* Kiszolgáló tűzfalának konfigurálása: Győződjön meg arról, hogy az Azure Database for PostgreSQL kiszolgáló tűzfal a kiszolgáló, beleértve a proxykiszolgálókat és átjárókat is, lehetővé teszi az ügyféltől érkező kapcsolatok engedélyezését.
* Ügyféltűzfal-konfiguráció: Az ügyfél tűzfalának engedélyeznie kell az adatbázis-kiszolgálóhoz való csatlakozást. A kiszolgáló IP-címeit és portjait, amelyeket nem lehet engedélyezni, valamint az olyan alkalmazásneveket, mint a PostgreSQL, egyes tűzfalakon.
* Felhasználói hiba: Lehet, hogy elgépelt kapcsolatparaméterek, például a kiszolgáló * \@* neve a kapcsolati karakterláncban vagy egy hiányzó kiszolgálónév-utótag a felhasználónévben.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Az állandó kapcsolódási problémák megoldásának lépései

1. Állítsa be [a tűzfalszabályokat](howto-manage-firewall-using-portal.md) az ügyfél IP-címének engedélyezéséhez. Csak ideiglenes tesztelési célokra állítson be egy 0.0.0.0-s tűzfalszabályt kezdő IP-címként, és a 255.255.255.255-ös t a záró IP-címet. Ezzel megnyitja a kiszolgálót az összes IP-cím hez. Ha ez megoldja a kapcsolódási problémát, távolítsa el ezt a szabályt, és hozzon létre egy tűzfalszabályt egy megfelelően korlátozott IP-címhez vagy címtartományhoz.
2. Az ügyfél és az internet közötti összes tűzfalon győződjön meg arról, hogy az 5432-es port nyitva van a kimenő kapcsolatok számára.
3. Ellenőrizze a kapcsolati karakterláncot és az egyéb csatlakozási beállításokat.
4. Ellenőrizze a szolgáltatás állapotát az irányítópulton. Ha úgy gondolja, hogy van egy regionális kimaradás, [olvassa el az üzletmenet folytonosságának áttekintése az Azure Database for PostgreSQL](concepts-business-continuity.md) lépéseket egy új régióba helyreállítása.

## <a name="next-steps"></a>További lépések

* [Átmeneti kapcsolódási hibák kezelése a PostgreSQL Azure Database for PostgreSQL-hez](concepts-connectivity.md)
