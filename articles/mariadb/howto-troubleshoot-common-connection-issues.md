---
title: Csatlakozási problémák elhárítása – Azure Database for MariaDB
description: Megtudhatja, hogyan háríthatja el a kapcsolattal kapcsolatos problémákat a MariaDB Azure Database szolgáltatással, például az újrapróbálkozást, a tűzfalproblémákat és a kimaradásokat igénylő átmeneti hibákat.
author: jasonwhowell
ms.author: jasonh
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 6817807d0a85b1465beafc86045ea1e0dd8a31ec
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770346"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>Az Azure Database for MariaDB-hez való csatlakozás hibáinak elhárítása

A csatlakozási problémákat számos dolog okozhatja, többek között:

* Tűzfalbeállítások
* Csatlakozási időtúllépés
* Helytelen bejelentkezési adatok
* A MariaDB-erőforrások egyes Azure-adatbázisában elért maximális korlát
* A szolgáltatás infrastruktúrájával kapcsolatos problémák
* Karbantartás a szervizben
* A kiszolgáló számítási lefoglalása a virtuális magok számának méretezésével vagy egy másik szolgáltatási szintre való áthelyezéssel változik

A MariaDB Azure Database szolgáltatással kapcsolatos csatlakozási problémák általában a következőképpen sorolhatók be:

* Átmeneti hibák (rövid életű vagy szakaszos)
* Állandó vagy nem átmeneti hibák (rendszeresen visszatérő hibák)

## <a name="troubleshoot-transient-errors"></a>Átmeneti hibák elhárítása

Átmeneti hibák fordulnak elő a karbantartás végrehajtásakor, a rendszer hibát észlel a hardverrel vagy a szoftverrel, vagy módosítja a kiszolgáló virtuális magjait vagy szolgáltatási szintjét. Az Azure Database for MariaDB szolgáltatás beépített magas rendelkezésre állású, és célja, hogy az ilyen típusú problémák automatikusan mérsékelhető. Az alkalmazás azonban rövid, általában legfeljebb 60 másodpercre megszakad a kapcsolata a kiszolgálóval. Egyes események időközben hosszabb időt vehet igénybe, például ha egy nagy tranzakció hosszú ideig tartó helyreállítást okoz.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Az átmeneti kapcsolódási problémák megoldásának lépései

1. Ellenőrizze a [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) minden olyan ismert kimaradások során, amikor a hibákat jelentett az alkalmazás.
2. Alkalmazások, amelyek egy felhőalapú szolgáltatáshoz, például az Azure Database for MariaDB kell várni a tranziens hibákat, és valósítsa meg újrapróbálkozási logika kezelni ezeket a hibákat, ahelyett, hogy ezeket a hibákat alkalmazáshibák a felhasználók számára. Tekintse át [az Azure Database for MariaDB átmeneti kapcsolódási hibáinak kezelését](concepts-connectivity.md) az ajánlott eljárásokért és az átmeneti hibák kezeléséhez vezető tervezési irányelvekért.
3. Akiszolgáló az erőforráskorlátok hoz közeledtével a hibák átmeneti kapcsolódási problémának tűnhetnek. Lásd: [A MariaDB Azure-adatbázisának korlátai.](concepts-limits.md)
4. Ha a kapcsolódási problémák továbbra is fennállnak, vagy ha az az időtartam, amelyre az alkalmazás a hibába ütközik, meghaladja a 60 másodpercet, vagy ha egy adott napon a hiba több előfordulása jelenik meg, az [Azure-támogatás](https://azure.microsoft.com/support/options) **kérése** lehetőséget választva nyújtson be egy Azure-támogatási kérelmet.

## <a name="troubleshoot-persistent-errors"></a>Állandó hibák elhárítása

Ha az alkalmazás tartósan nem tud csatlakozni a MariaDB Azure Database szolgáltatásához, az általában az alábbi probléma egyikével kapcsolatos problémát jelez:

* Tűzfal konfigurációja: A MariaDB-kiszolgáló vagy az ügyféloldali tűzfal Azure-adatbázisa blokkolja a kapcsolatokat.
* Hálózati újrakonfigurálás az ügyféloldalon: Új IP-cím vagy proxykiszolgáló lett hozzáadva.
* Felhasználói hiba: Előfordulhat például, hogy elgépelt kapcsolatparamétereket, például a kapcsolati karakterláncban lévő kiszolgálónevet vagy a felhasználónévben hiányzó * \@kiszolgálónév-utótagot.*

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Az állandó kapcsolódási problémák megoldásának lépései

1. Állítsa be [a tűzfalszabályokat](howto-manage-firewall-portal.md) az ügyfél IP-címének engedélyezéséhez. Csak ideiglenes tesztelési célokra állítson be egy 0.0.0.0-s tűzfalszabályt kezdő IP-címként, és a 255.255.255.255-ös t a záró IP-címet. Ezzel megnyitja a kiszolgálót az összes IP-cím hez. Ha ez megoldja a kapcsolódási problémát, távolítsa el ezt a szabályt, és hozzon létre egy tűzfalszabályt egy megfelelően korlátozott IP-címhez vagy címtartományhoz.
2. Az ügyfél és az internet közötti összes tűzfalon győződjön meg arról, hogy a 3306-os port meg van nyitva a kimenő kapcsolatok számára.
3. Ellenőrizze a kapcsolati karakterláncot és az egyéb csatlakozási beállításokat. Tekintse át [az alkalmazások csatlakoztatása a MariaDB Azure Database szolgáltatáshoz való csatlakoztatása](howto-connection-string.md)is.
4. Ellenőrizze a szolgáltatás állapotát az irányítópulton. Ha úgy gondolja, hogy van egy regionális kimaradás, [olvassa el az üzletmenet folytonosságának áttekintése](concepts-business-continuity.md) az Azure Database for MariaDB lépéseket egy új régióba helyreállítása.

## <a name="next-steps"></a>További lépések

* [Átmeneti kapcsolódási hibák kezelése a MariaDB Azure Database szolgáltatásában](concepts-connectivity.md)
