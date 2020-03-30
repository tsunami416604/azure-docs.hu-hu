---
title: 'Rövid útmutató: Naplózási jelentés letöltése az Azure portál használatával | Microsoft Docs'
description: Ismerje meg, hogyan tölthet le naplózási jelentést az Azure portál használatával
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6cbea49fe39c92c8a2fc50e501cb4ef5cff74b1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "68989680"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Rövid útmutató: Naplózási jelentés letöltése Azure portál használatával

Ebben a rövid útmutatóban megtudhatja, hogyan töltheti le a naplózási naplók CSV-fájlját a bérlő számára az elmúlt 24 órában. Legfeljebb 250 000 rekordot tölthet le az Azure Portalról. A rekordok a legutóbbiak szerint vannak rendezve, így alapértelmezés szerint a legutóbbi 250 000 rekord lesz. 

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

* Egy Azure Active Directory-bérlő. 
* A bérlő **biztonsági rendszergazdája**, **biztonsági olvasója**vagy **globális rendszergazdai** szerepkörében lévő felhasználó. Ezen felül a bérlőnél minden felhasználó elérheti a magára vonatkozó auditnaplókat.

## <a name="quickstart-download-an-audit-report"></a>Első lépések: Naplózási jelentés letöltése

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. Válassza ki **Azure Active Directoryt** a bal oldali navigációs ablaktáblában, és a **címtár váltása** gombra kattintva válassza ki az aktív címtárat.
3. Az irányítópulton az **Azure Active Directory** kiválasztása után válassza az **Auditnaplót**. 
4. Válassza az **elmúlt 24 órát** a **Dátumtartomány** szűrő legördülő listájából, majd az **Alkalmaz** választása után megtekintheti az utolsó 24 óra auditnaplóját. 
5. Válassza a **Letöltés** gombot, válassza a **CSV** fájlformátumot, és adjon meg egy fájlnevet a szűrt rekordokat tartalmazó CSV-fájl letöltéséhez. 

![Jelentéskészítés](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>További lépések

* [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon](concept-sign-ins.md)
* [Az Azure Active Directory jelentéskészítés megőrzése](reference-reports-data-retention.md)
* [Az Azure Active Directory jelentéskészítés késése](reference-reports-latencies.md)
