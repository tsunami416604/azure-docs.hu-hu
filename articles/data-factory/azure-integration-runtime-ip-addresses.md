---
title: Azure Integration Runtime – IP-címek
description: Ismerje meg, hogy mely IP-címeket kell engedélyeznie a bejövő forgalomnak, hogy megfelelően konfigurálhassa a tűzfalakat az adattárak hálózati hozzáférésének védelmére.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 598876e12fe04129692d0c9a842f4edb2ec00768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086806"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Azure Integration Runtime – IP-címek

Az Azure-integrációs futásidő által használt IP-címek attól függ, hogy az Azure-integrációs futásidejű régióban található.The IP addresses that Azure Integration Runtime uses on the region where your Azure integration runtime located. *Az összes* Az azure-integrációs runtimes, amelyek ugyanabban a régióban használja ugyanazt az IP-címtartományokat.

> [!IMPORTANT]  
> Az adatfolyamok jelenleg nem használják ezeket az IP-ket. 
>
> Ezeket az IP-tartományokat adatmozgatási, folyamat- és külső tevékenységek végrehajtásához használhatja. Ezek az IP-tartományok az adattárakban/ a hálózati biztonsági csoportban (NSG) / tűzfalakon való engedélyezési listázáshoz használhatók az Azure-integrációs futásidejű bejövő hozzáféréshez. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure-integrációs futásidejű IP-címek: Adott régiók

Az Azure-integráció stafa-üzemidejéhez tartozó IP-címekről az erőforrások at tartalmazó adott Azure-régióban forgalmat engedélyezhet:

|                | Régió              | IP-címek                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| Ázsia           | Kelet-Ázsia           | 20.189.104.128/25, </br>20.189.106.0/26, </br>13.75.39.112/28 |
| &nbsp;         | Délkelet-Ázsia      | 20.43.128.128/25, </br>20.43.130.0/26, </br>40.78.236.176/28 |
| Ausztrália      | Kelet-Ausztrália      | 20.37.193.0/25,</br>20.37.193.128/26,</br>13.70.74.144/28    |
| &nbsp;         | Délkelet-Ausztrália | 20.42.225.0/25,</br>20.42.225.128/26,</br>13.77.53.160/28    |
| Brazília         | Dél-Brazília        | 191.235.224.128/25,</br>191.235.225.0/26,</br>191.233.205.160/28 |
| Kanada         | Közép-Kanada      | 52.228.80.128/25,</br>52.228.81.0/26,</br>13.71.175.80/28    |
| Kína          | Kína Keleti 2        | 40.73.172.48/28,</br>52.130.0.128/25,</br>52.130.1.0/26      |
| Európa         | Észak-Európa        | 20.38.82.0/23,</br>20.38.80.192/26,</br>13.69.230.96/28      |
| &nbsp;         | Nyugat-Európa         | 40.74.26.0/23,</br>40.74.24.192/26,</br>13.69.67.192/28      |
| Franciaország         | Közép-Franciaország      | 20.43.40.128/25,</br>20.43.41.0/26,</br>40.79.132.112/28     |
| India          | Közép-India       | 52.140.104.128/25,</br>52.140.105.0/26,</br>20.43.121.48/28  |
| Japán          | Kelet-Japán          | 20.43.64.128/25,</br>20.43.65.0/26,</br>13.78.109.192/28     |
| Dél-Korea          | Dél-Korea középső régiója       | 20.41.64.128/25,</br>20.41.65.0/26,</br>52.231.20.64/28      |
| Dél-afrikai Köztársaság   | Dél-Afrika Észak-Afrika  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| Egyesült Királyság | Az Egyesült Királyság déli régiója            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| Egyesült Államok  | USA középső régiója          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | USA keleti régiója             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | USA 2. keleti régiója            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | USA keleti része 2 EUAP      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | USA északi középső régiója    | 40.80.185.0/24,</br>40.80.186.0/25,</br>52.162.111.48/28      |
|                | USA déli középső régiója    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | USA nyugati középső régiója     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | USA nyugati régiója             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | USA 2. nyugati régiója            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |
|                | USA-beli államigazgatás – Virginia     | 52.127.45.96/28,</br>52.127.48.128/25,</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Ismert probléma az Azure Storage-szal

* Az Azure Storage-fiókhoz való csatlakozáskor az IP-hálózati szabályok nincsenek hatással az Azure-integrációs futásidejű, a tárfiókkal azonos régióban származó kérelmekre. További részletekért kérjük, [olvassa el ezt a cikket](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  Ehelyett azt javasoljuk, hogy [az Azure Storage-hoz való csatlakozás közben használjon megbízható szolgáltatásokat.](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993) 

## <a name="next-steps"></a>További lépések

* [Az Azure Data Factory adatmozgatásának biztonsági szempontjai](data-movement-security-considerations.md)
