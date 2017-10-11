---
title: "Az Azure CLI parancsfájl méretű Azure adatbázis a PostgreSQL |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - skálázási Azure adatbázis PostgreSQL-kiszolgáló a metrikák lekérdezése után más-más teljesítménybeli szintjét."
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 05/31/2017
ms.openlocfilehash: b847abb336cce5dd5516469dca58002d3ba265f0
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Figyelheti és méretezhető egy PostgreSQL egykiszolgálós Azure parancssori felület használatával
Ez a parancsfájlpélda CLI PostgreSQL-kiszolgáló egy másik teljesítményszintre egy Azure-adatbázis méretezi a metrikák lekérdezése után. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl
Ez a parancsfájlpélda módosítsa a kijelölt sorok testre szabhatja a rendszergazda felhasználónevét és jelszavát. Cserélje le a saját előfizetés-azonosító az figyelő utasítással használt előfizetés-azonosító. [!code-azurecli-interactive [fő](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "létrehozása és a skála PostgreSQL az Azure-adatbázis.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrást.
[!code-azurecli-interactive[fő](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "az erőforráscsoportot.")]

## <a name="script-explanation"></a>Parancsfájl ismertetése
A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| **A parancs** | **Megjegyzések** |
|---|---|
| [az csoport létrehozása](/cli/azure/group#create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az postgres kiszolgáló létrehozása](/cli/azure/postgres/server#create) | Az adatbázisokat üzemeltető PostgreSQL-kiszolgáló létrehozása. |
| [az a figyelő metrikák listája](/cli/azure/monitor/metrics#list) | Listázza az erőforrásokra vonatkozó Átjárómetrika értékeként. |
| [az csoport törlése](/cli/azure/group#delete) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések
- További információt az Azure parancssori felület: [Azure CLI-dokumentáció](/cli/azure/overview)
- Próbálja meg a további parancsfájlok: [PostgreSQL Azure-adatbázis Azure CLI-minták](../sample-scripts-azure-cli.md)
- További információt a méretezés: [szolgáltatásszintek](../concepts-service-tiers.md) és [egységek számítási és tárolási egység](../concepts-compute-unit-and-storage.md)
