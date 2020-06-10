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
ms.openlocfilehash: f3eed03c585ba6b48a21b36c21cb77585456fc2c
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84660268"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Azure Integration Runtime – IP-címek

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Integration Runtime által használt IP-címek attól a régiótól függenek, ahol az Azure Integration Runtime található. *Az összes* Az azonos régióban található Azure Integration Runtime azonos IP-címtartományt használ.

> [!IMPORTANT]  
> Az adatfolyamatok nem támogatják a rögzített IP-címtartományok használatát.
>
> Ezeket az IP-tartományokat az adatáthelyezéshez, a folyamathoz és a külső tevékenységek végrehajtásához is használhatja. Ezek az IP-címtartományok az Azure Integration Runtime-ból bejövő hozzáféréshez használt adattárakban/hálózati biztonsági csoportban (NSG)/tűzfalakon való engedélyezési feladatokhoz használhatók. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime IP-címek: adott régiók

Az Azure Integration Runtime számára felsorolt IP-címekről érkező forgalom engedélyezése az adott Azure-régióban, ahol az erőforrások találhatók. A szolgáltatási címkék IP-címtartományok listáját a [szolgáltatás címkék IP-címtartomány letöltési hivatkozásával](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files)érheti el. Ha például az Azure-régió **AustraliaEast**, akkor a **DataFactory. AustraliaEast**IP-címtartomány listáját szerezheti be.


## <a name="known-issue-with-azure-storage"></a>Ismert probléma az Azure Storage-ban

* Az Azure Storage-fiókhoz való csatlakozáskor az IP-hálózati szabályok nincsenek hatással az Azure Integration Runtime-ból származó, a Storage-fiókkal azonos régióban található kérelmekre. További részletekért [tekintse meg ezt a cikket](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  Ehelyett javasoljuk, [hogy megbízható szolgáltatásokat használjon az Azure Storage-hoz való csatlakozáskor](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Következő lépések

* [A Azure Data Factory adatáthelyezésének biztonsági szempontjai](data-movement-security-considerations.md)
