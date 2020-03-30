---
title: Kapcsolatok – Nagykapacitású (Citus) – Azure Database for PostgreSQL –
description: Megtudhatja, hogy miként háríthatók el a kapcsolatokkal kapcsolatos problémák az Azure Database for PostgreSQL - Hyperscale (Citus) rendszerrel
keywords: postgresql-kapcsolat,kapcsolati karakterlánc,kapcsolódási problémák,átmeneti hiba,csatlakozási hiba
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: c064aca484f85c44dada9888012140784a96863f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977505"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Kapcsolati problémák elhárítása az Azure Database for PostgreSQL - Hyperscale (Citus) kapcsolatokkal kapcsolati problémák kal

A csatlakozási problémákat több dolog is okozhatja, például:

* Tűzfalbeállítások
* Csatlakozási időtúllépés
* Helytelen bejelentkezési adatok
* A kiszolgálócsoporthoz elért kapcsolati korlát
* A szolgáltatás infrastruktúrájával kapcsolatos problémák
* Szerviz karbantartása
* A koordinátor csomópontja átfelel az új hardvernek

A nagy kapacitással kapcsolatos csatlakozási problémák általában a következőképpen sorolhatók be:

* Átmeneti hibák (rövid életű vagy szakaszos)
* Állandó vagy nem átmeneti hibák (rendszeresen visszatérő hibák)

## <a name="troubleshoot-transient-errors"></a>Átmeneti hibák elhárítása

Több okból is átmeneti hibák fordulnak elő. A leggyakoribb a rendszer karbantartása, hiba a hardver vagy szoftver, és a koordinátor csomópont virtuálismag-frissítések.

A nagy kapacitású kiszolgálócsoport-csomópontok magas rendelkezésre állásának engedélyezése automatikusan csökkentheti az ilyen típusú problémákat. Az alkalmazásnak azonban továbbra is készen kell állnia arra, hogy rövid időre megszakadjon a kapcsolata. Más események is hosszabb időt vehet igénybe, például ha egy nagy tranzakció hosszú ideig tartó helyreállítást okoz.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Az átmeneti kapcsolódási problémák megoldásának lépései

1. Ellenőrizze a [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) minden olyan ismert kimaradások, amelyek során az alkalmazás hibákat jelentett.
2. Alkalmazások, amelyek egy felhőalapú szolgáltatáshoz, például a nagy kapacitású (Citus) kell várni a tranziens hibákat, és kecsesen reagálnak. Például az alkalmazások nak újrapróbálkozási logikát kell megvalósítaniuk a hibák kezeléséhez, ahelyett, hogy alkalmazáshibákként kezelnék őket a felhasználók számára.
3. Ahogy a kiszolgálócsoport megközelíti az erőforráskorlátokat, a hibák átmeneti kapcsolódási problémáknak tűnhetnek. A csomópont RAM-jának növelése, illetve a munkavégző csomópontok hozzáadása és az adatok kiegyensúlyozása segíthet.
4. Ha a kapcsolódási problémák továbbra is fennállnak, vagy 60 másodpercnél tovább tartanak, vagy naponta többször történnek, az Azure-támogatási kérelem benyújtásához nyújtson be egy Azure-támogatási kérelmet az [Azure-támogatási](https://azure.microsoft.com/support/options) webhely **támogatásának bekérése** lehetőség kiválasztásával.

## <a name="troubleshoot-persistent-errors"></a>Állandó hibák elhárítása

Ha az alkalmazás tartósan nem tud csatlakozni a nagy kapacitású (Citus) rendszerhez, a leggyakoribb okok a tűzfal helytelen beállítása vagy a felhasználói hiba.

* A koordinátor csomópont tűzfalának konfigurációja: Győződjön meg arról, hogy a nagy kapacitású kiszolgáló tűzfala úgy van beállítva, hogy engedélyezze az ügyféltől érkező kapcsolatokat, beleértve a proxykiszolgálókat és az átjárókat is.
* Ügyféltűzfal-konfiguráció: Az ügyfél tűzfalának engedélyeznie kell az adatbázis-kiszolgálóhoz való csatlakozást. Egyes tűzfalak megkövetelik, amely lehetővé teszi nem csak az alkalmazás név szerint, de lehetővé teszi az IP-címek és portok a szerver.
* Felhasználói hiba: Ellenőrizze duplán a kapcsolati karakterláncot. Lehet, hogy elgépelt paraméterek, mint például a kiszolgáló neve. Különböző nyelvi keretrendszerek és a psql kapcsolati karakterláncait az Azure Portalon találhatja meg. Nyissa meg a **Kapcsolati karakterláncok** lapot a Nagykapacitású (Citus) kiszolgálócsoportban. Azt is vegye szem előtt, hogy a Nagykapacitású (Citus) fürtök csak egy adatbázissal rendelkeznek, és előre definiált neve **citus**.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Az állandó kapcsolódási problémák megoldásának lépései

1. Állítsa be [a tűzfalszabályokat](howto-hyperscale-manage-firewall-using-portal.md) az ügyfél IP-címének engedélyezéséhez. Csak ideiglenes tesztelési célokra állítson be egy 0.0.0.0-s tűzfalszabályt kezdő IP-címként, és a 255.255.255.255-ös t a záró IP-címet. Ez a szabály megnyitja a kiszolgálót az összes IP-címre. Ha a szabály megoldja a kapcsolódási problémát, távolítsa el, és hozzon létre egy tűzfalszabályt egy megfelelően korlátozott IP-címhez vagy címtartományhoz.
2. Az ügyfél és az internet közötti összes tűzfalon győződjön meg arról, hogy az 5432-es port nyitva van a kimenő kapcsolatok számára.
3. Ellenőrizze a kapcsolati karakterláncot és az egyéb csatlakozási beállításokat.
4. Ellenőrizze a szolgáltatás állapotát az irányítópulton.

## <a name="next-steps"></a>További lépések

* A tűzfalszabályok fogalmai az [Azure Database for PostgreSQL – Hyperscale (Citus) szolgáltatásban](concepts-hyperscale-firewall-rules.md)
* Tekintse meg, hogyan [kezelheti az Azure Database for PostgreSQL tűzfalszabályait – Nagykapacitású (Citus)](howto-hyperscale-manage-firewall-using-portal.md)
