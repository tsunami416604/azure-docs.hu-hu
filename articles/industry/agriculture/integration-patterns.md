---
title: Azure FarmBeats architektúra
description: Az Azure FarmBeats architektúráját ismerteti
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 8eaba4c2a987f47c56169af138ef380835d56740
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797984"
---
# <a name="integration-patterns"></a>Integrációs minták

Az Azure FarmBeats az Azure piactéren elérhető, üzleti és üzleti célú ajánlat. A FarmBeats lehetővé teszi a mezőgazdasági adatkészletek összevonását a szolgáltatók között, és a gyakorlatban hasznosítható elemzések létrehozását mesterséges intelligencia (AI) vagy Machine Learning (ML) modellek létrehozásával az adatkészletek elutasításával.

![A Project Farm veri](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Az alábbi szakaszok az Azure FarmBeats integrációs mintáját írják le.

## <a name="why-integrate-with-azure-farmbeats"></a>Miért érdemes integrálni az Azure FarmBeats?

Ez a szakasz olyan partnerekre összpontosít, akik integrálni szeretnék az adatrendszereiket (például érzékelők, herék, meteorológiai állomások) az Azure FarmBeats.

Az Azure FarmBeats egy bővíthető ajánlat, amely lehetővé teszi, hogy a mezőgazdasági vállalkozások a különböző történelmi és valós idejű mezőgazdasági adatkészleteket egyetlen platformon adják hozzá. Az Azure FarmBeats segítségével a mezőgazdasági vállalkozások egy farm környezetében normalizálják, contextualize és összesítik az adatokat.

Az Azure FarmBeats rendelkező adatpartnerek révén szélesebb körű bevezetéssel nyithatja meg a rendszereket, és több ügyfelet is elérhet az adatajánlatokkal. Az Azure FarmBeats az adatközpont nevű bővíthető API-réteget nyújtja, amely lehetővé teszi az eszközök rendszeres és szabványos sémába való betöltését.

Ha az adatai elérhetők az ügyfelek Azure FarmBeats-példányán belül, az ügyfelek gazdagabb elemzéseket és eszközöket hozhatnak létre az adataik felett.

## <a name="next-steps"></a>További lépések

Az érzékelői adatok integrálásával kapcsolatos további információkért [lásd: az](imagery-partner-integration.md) [érzékelő adatintegrációja](sensor-partner-integration.md) és a képekkel kapcsolatos partneri integráció.
