---
title: Támogatott verziók – Azure Database for PostgreSQL – rugalmas kiszolgáló
description: A Azure Database for PostgreSQL-rugalmas kiszolgáló támogatott postgres fő-és alverzióit ismerteti.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 9d651a38361ccaa7f8788fb4a375b39b2953228b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934891"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Támogatott PostgreSQL főverziók Azure Database for PostgreSQL-rugalmas kiszolgálón

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Azure Database for PostgreSQL – a rugalmas kiszolgáló jelenleg a következő főbb verziókat támogatja:

## <a name="postgresql-version-12"></a>PostgreSQL 12-es verzió

Az aktuális alverzió 12,1. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/12/static/release-12-1.html) , ahol további információt talál az ebben a másodlagos kiadásban található javításokról és javításokról.

## <a name="postgresql-version-11"></a>PostgreSQL 11-es verzió

Az aktuális alverzió 11,8. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/11/static/release-11-8.html) , ahol további információt talál az ebben a másodlagos kiadásban található javításokról és javításokról.

## <a name="postgresql-version-10-and-older"></a>PostgreSQL 10-es és régebbi verzió

Azure Database for PostgreSQL rugalmas kiszolgáló esetén nem támogatott a PostgreSQL 10-es és régebbi verziója. Ha régebbi verzióra van szüksége, használja az [egykiszolgálós](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) központi telepítési lehetőséget.

## <a name="managing-upgrades"></a>Frissítések kezelése

A PostgreSQL-projekt rendszeresen kiad kisebb kiadásokat a jelentett hibák kijavítása érdekében. Azure Database for PostgreSQL a szolgáltatás havi üzembe helyezése során a rendszer a kisebb kiadású kiszolgálók automatikus javítását végzi.

Az automatikus főverzió frissítése még nem támogatott. Például jelenleg nincs automatikus frissítés a PostgreSQL 11-ről a PostgreSQL 12-re.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->
