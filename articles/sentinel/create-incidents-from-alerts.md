---
title: Incidensek létrehozása riasztásokból az Azure Sentinelben | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre incidenseket az Azure Sentinel riasztásaiból.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: b29b337d7487087bec268528ff26617f7a995235
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77587974"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Incidensek automatikus létrehozása a Microsoft biztonsági riasztásaiból

Az Azure Sentinelhez kapcsolódó Microsoft biztonsági megoldásokban, például a Microsoft Cloud App Security és az Azure Advanced Threat Protection szolgáltatásban kiváltott riasztások nem hoznak létre automatikusan incidenseket az Azure Sentinelben. Alapértelmezés szerint, amikor egy Microsoft-megoldást csatlakoztat az Azure Sentinelhez, az adott szolgáltatásban létrehozott riasztások nyers adatokként lesznek tárolva az Azure Sentinelben, az Azure Sentinel munkaterületén lévő Biztonsági riasztási táblában. Ezután ezeket az adatokat ugyanúgy használhatja, mint bármely más nyers adatot, amelyet a Sentinelhez csatlakoztat.

Egyszerűen beállíthatja az Azure Sentinel automatikusan incidensek et hozhat létre minden alkalommal, amikor egy riasztás aktiválódik egy csatlakoztatott Microsoft biztonsági megoldás, a jelen cikkben található utasításokat követve.

## <a name="prerequisites"></a>Előfeltételek
A [Microsoft biztonsági megoldásait csatlakoztatnia](connect-data-sources.md#data-connection-methods) kell az incidensek biztonsági szolgáltatásból történő létrehozásának engedélyezéséhez.

## <a name="using-microsoft-security-incident-creation-analytic-rules"></a>A Microsoft Security incidenslétrehozási analitikus szabályainak használata

Az Azure Sentinelben elérhető beépített szabályok segítségével kiválaszthatja, hogy mely csatlakoztatott Microsoft-biztonsági megoldások hozhassák létre automatikusan az Azure Sentinel-incidenseket valós időben. A szabályok at is szerkesztheti, hogy pontosabb szűrési lehetőségeket határozzon meg, hogy a Microsoft biztonsági megoldás által létrehozott riasztások közül melyik hozzon létre incidenseket az Azure Sentinelben. Például dönthet úgy, hogy az Azure Sentinel-incidenseket csak a súlyos Azure Security Center-riasztásokból hozza létre automatikusan.

1. Az Azure Sentinel alatti Azure Portalon válassza az **Analytics**lehetőséget.

1. Az összes beépített analitikus szabály megtekintéséhez válassza a **Szabálysablonok** lapot.

    ![Szabálysablonok](media/incidents-from-alerts/rule-templates.png)

1. Válassza ki a használni kívánt **Microsoft biztonsági** elemzési szabálysablont, majd kattintson a Szabály **létrehozása gombra.**

    ![Biztonsági elemzési szabály](media/incidents-from-alerts/security-analytics-rule.png)

1. Módosíthatja a szabály részleteit, és úgy dönt, hogy szűri a riasztásokat, amelyek riasztásokat hoznak létre a riasztás súlyossága vagy a riasztás nevében lévő szöveg szerint.  
      
    Ha például az **Azure Security Centert** választja a **Microsoft biztonsági szolgáltatás** mezőjében, és a **Súlyosság szerint szűrés** mezőben a **Magas** lehetőséget választja, csak a magas súlyosságú Azure Security Center-riasztások hoznak létre automatikusan incidenseket az Azure Sentinelben.  

    ![Szabály létrehozása varázsló](media/incidents-from-alerts/create-rule-wizard.png)

1. Létrehozhat egy új **Microsoft biztonsági** szabályt is, amely szűri a különböző Microsoft biztonsági szolgáltatásokriasztásait, ha a **+Create** gombra kattint, és kiválasztja a **Microsoft incidenslétrehozási szabályát.**

    ![Incidens-létrehozási szabály](media/incidents-from-alerts/incident-creation-rule.png)

  **Microsoft biztonsági szolgáltatástípusonként** több Microsoft **Biztonsági** analitikus szabályt is létrehozhat. Ez nem hoz létre ismétlődő incidenseket, mivel minden szabály szűrőként van használva. Még akkor is, ha egy riasztás egynél több **Microsoft Security** analitikus szabálynak felel meg, csak egy Azure Sentinel-incidenst hoz létre.

## <a name="enable-incident-generation-automatically-during-connection"></a>Incidensgenerálás automatikus engedélyezése kapcsolat közben
 Microsoft-biztonsági megoldás csatlakoztatásakor kiválaszthatja, hogy a biztonsági megoldásból származó riasztások automatikusan generálják-e az incidenseket az Azure Sentinelben.

1. Microsoft biztonsági megoldás adatforrásának csatlakoztatása. 

   ![Biztonsági incidensek létrehozása](media/incidents-from-alerts/generate-security-incidents.png)

1. Az **Incidensek létrehozása csoportban** válassza az **Engedélyezés** lehetőséget az alapértelmezett analitikus szabály engedélyezéséhez, amely automatikusan létrehozza az incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ezt a szabályt az **Analytics,** majd **az Aktív szabályok**csoportban szerkesztheti.

## <a name="next-steps"></a>További lépések

- Az Azure Sentinel első lépéseihez előfizetésre van szüksége a Microsoft Azure-ra. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Ismerje meg, hogyan [integrálhatja adatait az Azure Sentinelbe,](quickstart-onboard.md)és [hogyan láthatja az adatokat és a potenciális fenyegetéseket.](quickstart-get-visibility.md)
