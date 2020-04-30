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
ms.openlocfilehash: 059e77c063d00ef850a171507ca2e06422ade426
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82191770"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Az Azure szinapszis Analytics (előzetes verzió) kibocsátási megjegyzései

Ez a cikk az Azure szinapszis Analytics (munkaterületek) korlátozásait és problémáit ismerteti. Kapcsolódó információk: [Mi az az Azure szinapszis Analytics (munkaterületek)](overview-what-is.md) ?

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure szinapszis (munkaterületek) 

### <a name="azure-synapse-cli"></a>Azure szinapszis CLI

- Probléma és az ügyfelek hatása: az SDK által létrehozott munkaterületek nem tudják elindítani a szinapszis Studio alkalmazást

- Áthidaló megoldás: hajtsa végre a következő lépéseket: 
  1.    Hozzon létre egy `az synapse workspace create`munkaterületet a futtatásával.
  2.    A felügyelt azonosító azonosítójának `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`kinyerése a futtatásával.
  3.    Munkaterület hozzáadása szerepkörként a Storage-fiókhoz ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`futtatásával.
  4.    Tűzfalszabály hozzáadása a futtatásával ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `.

## <a name="next-steps"></a>További lépések

* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [A szinapszis Studio használata](quickstart-synapse-studio.md)
* [SQL-készlet létrehozása](quickstart-create-sql-pool.md)
* [Az igény szerinti SQL használata](quickstart-sql-on-demand.md)
* [Apache Spark-készlet létrehozása](quickstart-create-apache-spark-pool.md)