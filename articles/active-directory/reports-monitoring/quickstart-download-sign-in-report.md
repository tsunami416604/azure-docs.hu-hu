---
title: 'Rövid útmutató: Bejelentkezési jelentés letöltése az Azure portál használatával | Microsoft Docs'
description: Ismerje meg, hogyan tölthet le bejelentkezési jelentést az Azure portál használatával
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0e6e72424530d18b55f68077ba7c3328d9a2e549
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621428"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Rövid útmutató: Bejelentkezési jelentés letöltése az Azure portál használatával

Ebben a rövid útmutatóban megismerheti, hogyan lehet az elmúlt 24 órára vonatkozóan a bérlő bejelentkezési adatait letölteni.

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

* Azure Active Directory-bérlő prémium licenccel a bejelentkezési tevékenységek jelentésének megtekintéséhez. 
* A felhasználó, aki az a **biztonsági rendszergazda**, **biztonsági olvasó**, **jelentés olvasó** vagy **globális rendszergazdai** szerepkör a bérlő számára. Ezen felül a bérlő minden felhasználója elérheti a magára vonatkozó bejelentkezéseket.

## <a name="quickstart-download-a-sign-in-report"></a>Első lépések: Bejelentkezési jelentés letöltése

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **Azure Active Directoryt** a bal oldali navigációs ablaktáblában, és a **címtár váltása** gombra kattintva válassza ki az aktív címtárat.
3. Az irányítópulton az **Azure Active Directory** kiválasztása után válassza a **Bejelentkezéseket**. 
4. Válassza az **elmúlt 24 órát** a **Dátum** szűrő legördülő listájából, majd az **Alkalmaz** választása után megtekintheti az utolsó 24 óra bejelentkezéseit. 
5. A **Letöltés** gombra kattintva töltse le a szűrt rekordokat tartalmazó CSV-fájl. 

![Jelentéskészítés](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>További lépések

* [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon](concept-sign-ins.md)
* [Az Azure Active Directory jelentéskészítés megőrzése](reference-reports-data-retention.md)
* [Az Azure Active Directory jelentéskészítés késése](reference-reports-latencies.md)
