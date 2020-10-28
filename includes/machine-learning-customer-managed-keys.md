---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 1fde2947719079e411bc233bcce426feec8fa996
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631043"
---
> [!IMPORTANT]
> A Cosmos DB példány egy Microsoft által felügyelt erőforráscsoport keretében jön létre az __előfizetésében__ , valamint a szükséges erőforrásokkal együtt. Ez azt jelenti, hogy erre a Cosmos DB példányra számítunk fel díjat. A felügyelt erőforráscsoport formátuma a neve `<AML Workspace Resource Group Name><GUID>` . Ha a Azure Machine Learning-munkaterület privát végpontot használ, a rendszer a Cosmos DB példányhoz is létrehoz egy virtuális hálózatot. Ez a VNet a Cosmos DB és Azure Machine Learning közötti kommunikáció biztonságossá tételére szolgál.
> 
> * Ne __törölje az__ ezt a Cosmos db példányt tartalmazó erőforráscsoportot, vagy a csoportban automatikusan létrehozott összes erőforrást. Ha törölnie kell az erőforráscsoportot, Cosmos DB példányt stb., törölnie kell az azt használó Azure Machine Learning-munkaterületet. A társított munkaterület törlésekor az erőforráscsoport, a Cosmos DB-példány és az egyéb automatikusan létrehozott erőforrások törlődnek.
> * Ehhez a Cosmos DB-fiókhoz az alapértelmezett [__kérési egység__](../articles/cosmos-db/request-units.md) __8000__ -kor van beállítva. Az __érték módosítása nem támogatott__ .
> * __Nem adhat meg saját VNet__ a létrehozott Cosmos db-példánnyal való használatra. __A virtuális hálózat nem módosítható__ . Például nem módosíthatja az általa használt IP-címtartományt.