---
title: Machine Learning Services főbb eltérései (előzetes verzió)
description: Ez a témakör a Machine Learning Services az Azure SQL felügyelt példányban és a SQL Server Machine Learning Servicesban való főbb különbségeit ismerteti.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 05/27/2020
ms.openlocfilehash: da97938736e7a3719da9d280e60e6a636b86e0e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85254748"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>A felügyelt Azure SQL-példányon és az SQL Serveren elérhető Machine Learning Services közötti fő eltérések

Az [Azure SQL felügyelt példányának (előzetes verzió) Machine learning Services](machine-learning-services-overview.md) funkciója közel azonos a [SQL Server Machine learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). A következőkben néhány kulcsfontosságú különbség van.

> [!IMPORTANT]
> Az Azure SQL felügyelt példányának Machine Learning Services jelenleg nyilvános előzetes verzióban érhető el. A regisztrációhoz lásd: [regisztráció az előzetes](machine-learning-services-overview.md#signup)verzióra.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

Az előzetes verziójú szolgáltatásra az alábbi korlátozások vonatkoznak:

- A visszacsatolási kapcsolatok nem működnek (lásd: a [SQL Server egy Python-vagy R-parancsfájlból történő visszacsatolási kapcsolata](/sql/machine-learning/connect/loopback-connection)).
- A külső erőforráskészletek nem támogatottak.
- Csak a Python és az R nyelv támogatott. Külső nyelvek (például Java) nem adhatók hozzá.
- A [Message Passing Interface](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) (MPI) felületet használó forgatókönyvek nem támogatottak.

Szolgáltatásiszint-célkitűzés (SLO) frissítése esetén frissítse az SLO-t, és hozzon létre támogatási jegyet az R/Python dedikált erőforráskorlátainak újbóli engedélyezéséhez.

## <a name="language-support"></a>Nyelvi támogatás

Machine Learning Services a felügyelt SQL-példányon, és SQL Server támogatja a Python és az R [bővíthetőségi keretrendszert](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)is. A legfontosabb különbségek a következők:

- A Python és az R kezdeti verziói különböznek a felügyelt SQL-példányok Machine Learning Services és SQL Server:

  |                      | Python | R     |
  |----------------------|--------|-------|
  | Felügyelt SQL-példány | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Nincs szükség a-on keresztül történő konfigurálásra `external scripts enabled` `sp_configure` . Miután [regisztrált](machine-learning-services-overview.md#signup) az előzetes verzióra, a Machine learning engedélyezve van az Azure SQL felügyelt példányain.

## <a name="packages"></a>Csomagok

A Python és az R csomagkezelő a felügyelt SQL-példány és a SQL Server között eltérően működik. Ezek a különbségek a következők:

- A csomagok nem hajthatnak végre kimenő hálózati hívásokat. Ez a korlátozás hasonlít a SQL Server [Machine learning Services alapértelmezett tűzfalszabályok esetében](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) , de nem módosítható az SQL felügyelt példányában.
- A külső futtatókörnyezettől (például Java) függő csomagok nem támogatottak, vagy az operációs rendszer API-jait kell telepíteni a telepítéshez vagy a használathoz.

A Python és az R csomagok kezelésével kapcsolatos további információkért lásd:

- [Python-csomagadatok lekérése](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)
- [R-csomagadatok lekérése](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)

## <a name="resource-governance"></a>Erőforrások szabályozása

Nem lehetséges az R-erőforrások korlátozása [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) és külső erőforráskészlet használatával.

A nyilvános előzetes verzióban az R-erőforrások a felügyelt SQL-példányok erőforrásainak legfeljebb 20%-át adják, és a választott szolgáltatásszinttől függenek. További információ: [Azure SQL Database vásárlási modellek](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Nincs elég memória-hiba

Ha nincs elegendő memória az R számára, hibaüzenet jelenik meg. Gyakori hibaüzenetek:

- Nem lehet kommunikálni a futtatókörnyezettel a (z) * * * * * * * kérelem azonosítójának "R" parancsfájljában. Ellenőrizze az R-futtatókörnyezet követelményeit
- R-szkripthiba történt az „sp_execute_external_script” végrehajtása során (HRESULT 0x80004004). ...külső szkripthiba történt: „...nem sikerült memóriát (0 MB) lefoglalni az R_AllocStringBuffer C-függvényben”
- Külső parancsfájl hiba történt: hiba: nem foglalható le a méret vektora.

A memóriahasználat az R-szkriptekben használt memória mennyiségétől és a párhuzamosan végrehajtott lekérdezések számától függ. Ha a fenti hibaüzeneteket kapja, skálázza az adatbázist magasabb szolgáltatási szintre a probléma elhárításához.

## <a name="next-steps"></a>További lépések

- Tekintse meg az [Azure SQL felügyelt példányának](machine-learning-services-overview.md)áttekintését Machine learning Services.
- Ha szeretné megtudni, hogyan használható a Python a Machine Learning Servicesban, tekintse meg a [Python-parancsfájlok futtatása](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)című témakört.
- Az R-Machine Learning Services használatának megismeréséhez tekintse meg az [r-parancsfájlok futtatása](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)című témakört.
