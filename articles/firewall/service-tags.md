---
title: Azure-tűzfal szolgáltatáscímkék áttekintése
description: Ez a cikk az Azure-tűzfal szolgáltatáscímkék áttekintést.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 6/27/2019
ms.author: victorh
ms.openlocfilehash: d0ac36e415c056dffc9c75d00968ff74c2156e63
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450170"
---
# <a name="azure-firewall-service-tags"></a>Az Azure tűzfal szolgáltatáscímkék

A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége. Nem hozhat létre saját szolgáltatáscímkéket, és nem határozhatja meg, hogy melyik IP-címeket jelöljék az egyes címkék. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket.

A hálózati szabályok célmező Azure tűzfal szolgáltatáscímkék is használható. Adott IP-címek helyett használhatja őket.

## <a name="supported-service-tags"></a>Támogatott szolgáltatáscímkék

Lásd: [biztonsági csoportok](../virtual-network/security-overview.md#service-tags) , amely használható az Azure hálózati tűzfalszabályok szolgáltatáscímkék listáját.

## <a name="next-steps"></a>További lépések

Azure tűzfalszabályokkal kapcsolatos további tudnivalókért lásd: [Azure tűzfalszabály feldolgozó logika](rule-processing.md).