---
title: 'Rövid útmutató: Naplózási jelentés letöltése az Azure portál használatával | Microsoft Docs'
description: Ismerje meg, hogyan tölthet le naplózási jelentést az Azure portál használatával
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 09/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d6ecf62b46c25ec7cf4ab80219d1bc220e4eb4cc
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367406"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Rövid útmutató: Naplózási jelentés letöltése Azure portál használatával

Ebben a rövid útmutatóban megismerheti, hogyan lehet az elmúlt 24 órára vonatkozóan a bérlő auditnaplókat letölteni.

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

* Egy Azure Active Directory-bérlő. 
* Egy felhasználó, aki a bérlőnél biztonsági rendszergazdai, biztonsági olvasó vagy globális rendszergazdai szerepkörben van. Ezen felül a bérlőnél minden felhasználó elérheti a magára vonatkozó auditnaplókat.

## <a name="quickstart-download-an-audit-report"></a>Első lépések: Naplózási jelentés letöltése

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **Azure Active Directoryt** a bal oldali navigációs ablaktáblában, és a **címtár váltása** gombra kattintva válassza ki az aktív címtárat.
3. Az irányítópulton az **Azure Active Directory** kiválasztása után válassza az **Auditnaplót**. 
4. Válassza az **elmúlt 24 órát** a **Dátumtartomány** szűrő legördülő listájából, majd az **Alkalmaz** választása után megtekintheti az utolsó 24 óra auditnaplóját. 
5. A **Letöltés** gombra kattintva töltse le a szűrt rekordokat tartalmazó CSV-fájl. 

![Jelentéskészítés](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>További lépések

* [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon](concept-sign-ins.md)
* [Az Azure Active Directory jelentéskészítés megőrzése](reference-reports-data-retention.md)
* [Az Azure Active Directory jelentéskészítés késése](reference-reports-latencies.md)
