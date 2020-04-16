---
title: Kibocsátási megjegyzések
description: Kibocsátási megjegyzések az Azure Synapse Analytics (munkaterületek)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c1b5b9ac5d7c3f04dd3ae2e843425a5ead0d4c07
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423858"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Az Azure Synapse Analytics (előzetes verzió) kiadási megjegyzések

Ez a cikk az Azure Synapse Analytics (munkaterületek) korlátozásokat és problémákat ismerteti. A kapcsolódó információkért lásd: [Mi az Azure Synapse Analytics (munkaterületek)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (munkaterületek) 

### <a name="azure-synapse-cli"></a>Azure Szinapsze CLI

- Probléma és ügyfélhatás: Az SDK által létrehozott munkaterületek nem indíthatják el a Synapse Studio-t

- Megoldás: Hajtsa végre az alábbi lépéseket: 
  1.    Hozzon létre `az synapse workspace create`munkaterületet a 2.    Felügyelt identitásazonosító kinyerése futással`$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`
  3.    Munkaterület hozzáadása szerepkörként a tárfiókhoz futással` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`
  4.    Tűzfalszabály hozzáadása futással` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `

## <a name="next-steps"></a>További lépések

* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [A Synapse Studio használata](quickstart-synapse-studio.md)
* [SQL-készlet létrehozása](quickstart-create-sql-pool.md)
* [Sql igény szerinti használata](quickstart-sql-on-demand.md)
* [Apache Spark-készlet létrehozása](quickstart-create-apache-spark-pool.md)