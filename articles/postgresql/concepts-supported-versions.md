---
title: Támogatott verziók – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: A Azure Database for PostgreSQL-egyetlen kiszolgálón támogatott postgres fő-és alverziókat ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74792234"
---
# <a name="supported-postgresql-major-versions"></a>Támogatott PostgreSQL főverziók
A Microsoft célja, hogy támogassa a PostgreSQL-motor n-2 verzióját Azure Database for PostgreSQL – egyetlen kiszolgálón. A verziók a jelenlegi főverzió az Azure-ban (n) és a két korábbi főverzió (-2).

A Azure Database for PostgreSQL jelenleg a következő főbb verziókat támogatja:

## <a name="postgresql-version-11"></a>PostgreSQL 11-es verzió
Az aktuális alverzió 11,5. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/11/static/release-11-5.html) , ahol további információt talál az ebben a másodlagos kiadásban található javításokról és javításokról.

## <a name="postgresql-version-10"></a>PostgreSQL 10-es verzió
Az aktuális alverzió 10,10. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/10/static/release-10-10.html) , ahol további információt talál az ebben a másodlagos kiadásban található javításokról és javításokról.

## <a name="postgresql-version-96"></a>PostgreSQL 9,6-es verzió
Az aktuális másodlagos kiadás a 9.6.15. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) , ahol további információt talál az ebben a másodlagos kiadásban található javításokról és javításokról.

## <a name="postgresql-version-95"></a>PostgreSQL 9,5-es verzió
Az aktuális másodlagos kiadás a 9.5.19. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) , amely az ebben a másodlagos kiadásban található javításokat és javításokat ismerteti.

## <a name="managing-upgrades"></a>Frissítések kezelése
A PostgreSQL-projekt rendszeresen kiad kisebb kiadásokat a jelentett hibák kijavítása érdekében. Azure Database for PostgreSQL a szolgáltatás havi üzembe helyezése során a rendszer a kisebb kiadású kiszolgálók automatikus javítását végzi. 

A főverzió automatikus frissítése nem támogatott. Például a PostgreSQL 9.5-ös verziójáról a PostgreSQL 9.6-os verziójára történő automatikus frissítés nem érhető el. A következő főverzióra történő frissítéshez hozzon létre egy [adatbázismentése, és végezzen visszaállítást](./howto-migrate-using-dump-and-restore.md) az új motorverzióval létrehozott kiszolgálón.

### <a name="version-syntax"></a>Verzió szintaxisa
A PostgreSQL 10-es verziójának megkezdése előtt a [PostgreSQL verziószámozási házirendje](https://www.postgresql.org/support/versioning/) _jelentős_ verziófrissítést eredményezett, hogy az első _vagy_ a második szám növekszik. Például a 9,5 – 9,6 _fő_ verziófrissítésnek számít. A 10-es verziótól kezdve a rendszer csak az első szám változását tekinti jelentős verziófrissítésnek. Például a 10,0 – 10,1 egy _kisebb_ kiadású frissítés. A 10 – _11 verzió a főverzió frissítése_ .

## <a name="next-steps"></a>További lépések
A PostgreSQL támogatott bővítményeivel kapcsolatos információkért tekintse meg [a kiterjesztések dokumentumát](concepts-extensions.md).
