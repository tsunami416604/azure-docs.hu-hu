---
title: Machine Learning Services főbb eltérései (előzetes verzió)
description: Ez a cikk az Azure SQL felügyelt példányain és a Machine Learning Services SQL Server Machine Learning Services közötti fő különbségeket ismerteti.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 10/26/2020
ms.openlocfilehash: adf454ac697f8cabf4256ebfc5baa5d0d1c76264
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782467"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>A felügyelt Azure SQL-példányon és az SQL Serveren elérhető Machine Learning Services közötti fő eltérések

Az [Azure SQL felügyelt példányának (előzetes verzió) Machine learning Services](machine-learning-services-overview.md) funkciója közel azonos a [SQL Server Machine learning Services](/sql/advanced-analytics/what-is-sql-server-machine-learning). A következőkben néhány kulcsfontosságú különbség van.

> [!IMPORTANT]
> Az Azure SQL felügyelt példányának Machine Learning Services jelenleg nyilvános előzetes verzióban érhető el. A regisztrációhoz lásd: [regisztráció az előzetes](machine-learning-services-overview.md#signup)verzióra.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

Az előzetes verziójú szolgáltatásra az alábbi korlátozások vonatkoznak:

- A visszacsatolási kapcsolatok nem működnek (lásd: a [SQL Server egy Python-vagy R-parancsfájlból történő visszacsatolási kapcsolata](/sql/machine-learning/connect/loopback-connection)).
- A külső erőforráskészletek nem támogatottak.
- Csak a Python és az R nyelv támogatott. Külső nyelvek (például Java) nem adhatók hozzá.
- A [Message Passing Interface](/message-passing-interface/microsoft-mpi) (MPI) felületet használó forgatókönyvek nem támogatottak.

A szolgáltatási szint célkitűzés (SLO) frissítése esetén frissítse az SLO-t, és egy támogatási jegyet, hogy engedélyezze újra az R/Python dedikált erőforrás-korlátait.

## <a name="language-support"></a>Nyelvi támogatás

Machine Learning Services a felügyelt SQL-példányon, és SQL Server támogatja a Python és az R [bővíthetőségi keretrendszert](/sql/advanced-analytics/concepts/extensibility-framework)is. A legfontosabb különbségek a következők:

- A Python és az R kezdeti verziói különböznek a felügyelt SQL-példányok Machine Learning Services és SQL Server:

  | Rendszer               | Python | R     |
  |----------------------|--------|-------|
  | SQL Managed Instance | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Nincs szükség a-on keresztül történő konfigurálásra `external scripts enabled` `sp_configure` . Miután [regisztrált](machine-learning-services-overview.md#signup) az előzetes verzióra, a Machine learning engedélyezve van az Azure SQL felügyelt példányain.

## <a name="packages"></a>Csomagok

A Python és az R csomagkezelő a felügyelt SQL-példány és a SQL Server között eltérően működik. Ezek a különbségek a következők:

- A külső futtatókörnyezettől (például Java) függő csomagok nem támogatottak, vagy az operációs rendszer API-jait kell telepíteni a telepítéshez vagy a használathoz.
- A csomagok elvégezhetik a kimenő hálózati hívásokat (az előzetes verzióról korábban is változhatnak). A kimenő hálózati hívások engedélyezéséhez beállíthatja a megfelelő kimenő biztonsági szabályokat a [hálózati biztonsági csoport](/azure/virtual-network/network-security-groups-overview) szintjén.

A Python és az R csomagok kezelésével kapcsolatos további információkért lásd:

- [Python-csomagadatok lekérése](/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [R-csomagadatok lekérése](/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Erőforrások szabályozása

Nem lehetséges az R-erőforrások korlátozása [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) és külső erőforráskészlet használatával.

A nyilvános előzetes verzióban az R-erőforrások a felügyelt SQL-példányok erőforrásainak legfeljebb 20%-át adják, és a választott szolgáltatásszinttől függenek. További információ: [Azure SQL Database vásárlási modellek](../database/purchasing-models.md).

### <a name="insufficient-memory-error"></a>Nincs elég memória-hiba

Ha nincs elegendő memória az R számára, hibaüzenet jelenik meg. Gyakori hibaüzenetek:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

A memóriahasználat az R-szkriptekben használt memória mennyiségétől és a párhuzamosan végrehajtott lekérdezések számától függ. Ha a fenti hibaüzeneteket kapja, skálázza az adatbázist magasabb szolgáltatási szintre a probléma elhárításához.

## <a name="next-steps"></a>Következő lépések

- Tekintse meg az [Azure SQL felügyelt példányának](machine-learning-services-overview.md)áttekintését Machine learning Services.
- Ha szeretné megtudni, hogyan használható a Python a Machine Learning Servicesban, tekintse meg a [Python-parancsfájlok futtatása](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)című témakört.
- Az R-Machine Learning Services használatának megismeréséhez tekintse meg az [r-parancsfájlok futtatása](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)című témakört.
