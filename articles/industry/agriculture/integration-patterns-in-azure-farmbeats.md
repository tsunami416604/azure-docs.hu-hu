---
title: Azure FarmBeats architektúra
description: Az Azure FarmBeats architektúráját ismerteti
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d64c2175072d9979cfda2ea5f75beb34d3ad0d6b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75482373"
---
# <a name="integration-patterns"></a>Integrációs minták

Az Azure FarmBeats az Azure piactéren elérhető, üzleti és üzleti célú ajánlat. A FarmBeats lehetővé teszi a mezőgazdasági adatkészletek összevonását a szolgáltatók között, és a gyakorlatban hasznosítható elemzések létrehozását mesterséges intelligencia (AI) vagy Machine Learning (ML) modellek létrehozásával az adatkészletek elutasításával.

![A Project Farm veri](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Az alábbi szakaszok az Azure FarmBeats integrációs mintáját írják le.

## <a name="why-integrate-with-azure-farmbeats"></a>Miért érdemes integrálni az Azure FarmBeats?

Ez a szakasz olyan partnerekre összpontosít, akik integrálni szeretnék az adatrendszereiket (például érzékelők, herék, meteorológiai állomások) az Azure FarmBeats.

Az Azure FarmBeats egy bővíthető ajánlat, amely lehetővé teszi, hogy a mezőgazdasági vállalkozások a különböző történelmi és valós idejű mezőgazdasági adatkészleteket egyetlen platformon adják hozzá. Az Azure FarmBeats segítségével a mezőgazdasági vállalkozások egy farm környezetében normalizálják, contextualize és összesítik az adatokat.

Az Azure FarmBeats rendelkező adatpartnerek révén szélesebb körű bevezetéssel nyithatja meg a rendszereket, és több ügyfelet is elérhet az adatajánlatokkal. Az Azure FarmBeats egy Datahub nevű bővíthető API-réteget biztosít, amely lehetővé teszi, hogy a rendszer szisztematikusan és szabványosított sémába importálja az eszközöket az eszközökről.

Ha az adatai elérhetők az ügyfelek Azure FarmBeats-példányán belül, az ügyfelek gazdagabb elemzéseket és eszközöket hozhatnak létre az adataik felett.

## <a name="next-steps"></a>További lépések

Az érzékelői adatok integrálásával kapcsolatos további információkért [lásd: az](imagery-partner-integration-in-azure-farmbeats.md) [érzékelő adatintegrációja](sensor-partner-integration-in-azure-farmbeats.md) és a képekkel kapcsolatos partneri integráció.
