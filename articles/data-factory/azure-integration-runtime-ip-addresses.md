---
title: Azure Integration Runtime – IP-címek
description: Megtudhatja, hogy mely IP-címeken kell engedélyezni a bejövő forgalmat, hogy megfelelően konfigurálja a tűzfalakat az adattárakhoz való hálózati hozzáférés biztonságossá tételéhez.
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
ms.openlocfilehash: 842fcaf6ea06ed4aaaff04f6bb5d7d38a672573c
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78162351"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Azure Integration Runtime – IP-címek

A Azure Integration Runtime által használt IP-címek attól a régiótól függenek, ahol az Azure Integration Runtime található. *Az összes* Az azonos régióban található Azure Integration Runtime azonos IP-címtartományt használ.

> [!IMPORTANT]  
> Az adatfolyamatok jelenleg nem használják ezeket az IP-címeket. 
>
> Ezeket az IP-tartományokat az adatáthelyezéshez, a folyamathoz és a külső tevékenységek végrehajtásához is használhatja. Ezek az IP-címtartományok az Azure Integration Runtime-ból bejövő hozzáféréshez használt adattárakban/hálózati biztonsági csoportban (NSG)/tűzfalakon való engedélyezési feladatokhoz használhatók. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime IP-címek: adott régiók

Az Azure Integration Runtime számára felsorolt IP-címekről érkező forgalom engedélyezése az adott Azure-régióban, ahol az erőforrások találhatók:

|                | Régió              | IP-címek                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| Ázsia           | Kelet-Ázsia           | 20.189.104.128/25, </br>20.189.106.0/26, </br>13.75.39.112/28 |
| &nbsp;         | Délkelet-Ázsia      | 20.43.128.128/25, </br>20.43.130.0/26, </br>40.78.236.176/28 |
| Ausztrália      | Kelet-Ausztrália      | 20.37.193.0/25,</br>20.37.193.128/26,</br>13.70.74.144/28    |
| &nbsp;         | Délkelet-Ausztrália | 20.42.225.0/25,</br>20.42.225.128/26,</br>13.77.53.160/28    |
| Brazília         | Dél-Brazília        | 191.235.224.128/25,</br>191.235.225.0/26,</br>191.233.205.160/28 |
| Kanada         | Közép-Kanada      | 52.228.80.128/25,</br>52.228.81.0/26,</br>13.71.175.80/28    |
| Európa         | Észak-Európa        | 20.38.82.0/23,</br>20.38.80.192/26,</br>13.69.230.96/28      |
| &nbsp;         | Nyugat-Európa         | 40.74.26.0/23,</br>40.74.24.192/26,</br>13.69.67.192/28      |
| Franciaország         | Közép-Franciaország      | 20.43.40.128/25,</br>20.43.41.0/26,</br>40.79.132.112/28     |
| India          | Közép-India       | 52.140.104.128/25,</br>52.140.105.0/26,</br>20.43.121.48/28  |
| Japán          | Kelet-Japán          | 20.43.64.128/25,</br>20.43.65.0/26,</br>13.78.109.192/28     |
| Dél-Korea          | Dél-Korea középső régiója       | 20.41.64.128/25,</br>20.41.65.0/26,</br>52.231.20.64/28      |
| Dél-afrikai Köztársaság   | Dél-Afrika északi régiója  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| Egyesült Királyság | Az Egyesült Királyság déli régiója            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| Egyesült Államok  | USA középső régiója          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | USA keleti régiója             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | USA 2. keleti régiója            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | USA 2. keleti – EUAP      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | USA északi középső régiója    | 40.80.185.0/24,</br>40.80.186.0/25,</br>52.162.111.48/28      |
|                | USA déli középső régiója    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | USA nyugati középső régiója     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | USA nyugati régiója             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | USA 2. nyugati régiója            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |
|                | USA-beli államigazgatás – Virginia     | 52.127.45.96/28,</br>52.127.48.128/25,</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Ismert probléma az Azure Storage-ban

* Az Azure Storage-fiókhoz való csatlakozáskor az IP-hálózati szabályok nincsenek hatással az Azure Integration Runtime-ból származó, a Storage-fiókkal azonos régióban található kérelmekre. További részletekért [tekintse meg ezt a cikket](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  Ehelyett javasoljuk, [hogy megbízható szolgáltatásokat használjon az Azure Storage-hoz való csatlakozáskor](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Következő lépések

* [A Azure Data Factory adatáthelyezésének biztonsági szempontjai](data-movement-security-considerations.md)
