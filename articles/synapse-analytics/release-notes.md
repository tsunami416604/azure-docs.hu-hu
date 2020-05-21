---
title: Kibocsátási megjegyzések
description: Az Azure szinapszis Analytics (munkaterületek) kibocsátási megjegyzései
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 514694dc2e3f06db2fb80f6b3ba0106343be11d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658503"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Az Azure szinapszis Analytics (előzetes verzió) kibocsátási megjegyzései

Ez a cikk az Azure szinapszis Analytics (munkaterületek) korlátozásait és problémáit ismerteti. Kapcsolódó információk: [Mi az az Azure szinapszis Analytics (munkaterületek)](overview-what-is.md) ?

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure szinapszis (munkaterületek) 

### <a name="azure-synapse-cli"></a>Azure szinapszis CLI

- Probléma és az ügyfelek hatása: az SDK által létrehozott munkaterületek nem tudják elindítani a szinapszis Studio alkalmazást

- Áthidaló megoldás: hajtsa végre a következő lépéseket: 
  1.    Hozzon létre egy munkaterületet a futtatásával `az synapse workspace create` .
  2.    A felügyelt azonosító azonosítójának kinyerése a futtatásával `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Munkaterület hozzáadása szerepkörként a Storage-fiókhoz futtatásával ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` .
  4.    Tűzfalszabály hozzáadása a futtatásával ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Következő lépések

* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [A szinapszis Studio használata](quickstart-synapse-studio.md)
* [SQL-készlet létrehozása](quickstart-create-sql-pool-portal.md)
* [Az igény szerinti SQL használata](quickstart-sql-on-demand.md)
* [Apache Spark-készlet létrehozása](quickstart-create-apache-spark-pool-portal.md)