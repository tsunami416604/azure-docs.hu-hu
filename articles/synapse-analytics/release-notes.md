---
title: 'Kibocsátási megjegyzések: Azure szinapszis Analytics (munkaterületek – előzetes verzió)'
description: Az Azure szinapszis Analytics kibocsátási megjegyzései (munkaterületek előzetes verzió)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f2050bf671af35df4faec8b0b1d53f4c10a9075e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90031670"
---
# <a name="azure-synapse-analytics-workspaces-preview-release-notes"></a>Az Azure szinapszis Analytics (munkaterületek előzetes verzió) kibocsátási megjegyzései

Ez a cikk az Azure szinapszis Analytics (munkaterületek) korlátozásait és problémáit ismerteti. Kapcsolódó információk: [Mi az az Azure szinapszis Analytics (munkaterületek)](overview-what-is.md) ?

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-cli"></a>Azure CLI

- Probléma és az ügyfelek hatása: az SDK által létrehozott munkaterületek nem tudják elindítani a szinapszis Studio alkalmazást

- Áthidaló megoldás: hajtsa végre a következő lépéseket: 
  1.    Hozzon létre egy munkaterületet a futtatásával `az synapse workspace create` .
  2.    A felügyelt azonosító AZONOSÍTÓjának kinyerése a futtatásával `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Munkaterület hozzáadása szerepkörként a Storage-fiókhoz futtatásával ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` .
  4.    Tűzfalszabály hozzáadása a futtatásával ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>További lépések

* [Mi az az Azure szinapszis?](overview-what-is.md)
* [Első lépések](get-started.md)
* [Gyakori kérdések](overview-faq.md)
