---
title: 'Rövid útmutató: Naplózási jelentés letöltése az Azure portál használatával | Microsoft Docs'
description: Ismerje meg, hogyan tölthet le naplózási jelentést az Azure portál használatával
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d702810ae7de03a14e50f56c2b8e819c31f3ea9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192710"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Gyors útmutató: Töltse le az auditnaplókat, az Azure portal használatával

Ebben a rövid útmutatóban megismerheti, hogyan lehet az elmúlt 24 órára vonatkozóan a bérlő auditnaplókat letölteni. Legfeljebb 5000 rekordok letöltheti az Azure Portalról. A rekordok legtöbb alapján rendezi a rendszer közelmúltbeli, így alapértelmezés szerint a legújabb 5000 rekordokat. 

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

* Egy Azure Active Directory-bérlő. 
* A felhasználó, aki az a **biztonsági rendszergazda**, **biztonsági olvasó**, vagy **globális rendszergazdai** szerepkör a bérlő számára. Ezen felül a bérlőnél minden felhasználó elérheti a magára vonatkozó auditnaplókat.

## <a name="quickstart-download-an-audit-report"></a>Gyors útmutató: Naplózási jelentés letöltése

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
