---
title: Azure FarmBeats architektúra
description: Az Azure FarmBeats architektúrájának ismertetése
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d64c2175072d9979cfda2ea5f75beb34d3ad0d6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482373"
---
# <a name="integration-patterns"></a>Integrációs minták

Az Azure FarmBeats egy vállalkozáson belüli ajánlat, amely elérhető az Azure Piactéren. A FarmBeats lehetővé teszi a mezőgazdasági adatkészletek összesítését a szolgáltatók között, és a mesterséges intelligencia (AI) vagy a machine learning (ML) modellek létrehozásával az adatkészletek egyesítésével használható műveletek et.

![Projekt Farm Beats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

A következő szakaszok ismertetik az Azure FarmBeats integrációs mintáját.

## <a name="why-integrate-with-azure-farmbeats"></a>Miért érdemes integrálni az Azure FarmBeats-szel?

Ez a szakasz azokra a partnerekre összpontosít, akik adatrendszereiket (például érzékelők, drónok, meteorológiai állomások) integrálni szeretnék az Azure FarmBeats-be.

Az Azure FarmBeats egy bővíthető ajánlat, amely lehetővé teszi a mezőgazdasági vállalkozások számára, hogy a különböző korábbi és valós idejű mezőgazdasági adatkészleteiket egyetlen platformba adják hozzá. Az Azure FarmBeats segít egy mezőgazdasági vállalkozás normalizálásában, környezettanizálásában és az adatok egy farm környezetben való összesítésében.

Az Azure FarmBeats adatpartnerévé válásával megnyithatja rendszereit a szélesebb körű bevezetés érdekében, és az adatajánlatokkal több ügyfelet érhet el. Az Azure FarmBeats egy bővíthető API-réteget, a Datahubot biztosít, amely segít az eszközökről rendszeresen és szabványosított sémába történő adatok betöltésével.

Miután az adatok elérhetővé tisza az ügyfelek Azure FarmBeats-példányát, az ügyfelek gazdagabb elemzéseket és eszközöket hozhatnak létre az adatok on-t.

## <a name="next-steps"></a>További lépések

Az érzékelők adatainak integrálásáról további információt az [érzékelőadatok integrálásáról](sensor-partner-integration-in-azure-farmbeats.md) és a képláncokkal kapcsolatos partnerintegrációról a [Képpartnerek integrációja](imagery-partner-integration-in-azure-farmbeats.md)című témakörben talál.
