---
title: 'Rövid útmutató: Bejelentkezési jelentés letöltése az Azure portál használatával | Microsoft Docs'
description: Ismerje meg, hogyan tölthet le bejelentkezési jelentést az Azure portál használatával
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
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
ms.openlocfilehash: 86bc72f69903134afa3750ad6b72486a713b6cc0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "68989670"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Rövid útmutató: Bejelentkezési jelentés letöltése az Azure portál használatával

Ebben a rövid útmutatóban megismerheti, hogyan lehet az elmúlt 24 órára vonatkozóan a bérlő bejelentkezési adatait letölteni. Legfeljebb 250 000 rekordot tölthet le az Azure Portalról. A rekordok a legutóbbiak szerint vannak rendezve, így alapértelmezés szerint a legutóbbi 250 000 rekord lesz. 

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

* Azure Active Directory-bérlő prémium licenccel a bejelentkezési tevékenységek jelentésének megtekintéséhez. Az [Azure Active Directory-kiadás](../fundamentals/active-directory-get-started-premium.md) frissítéséhez olvassa el az Azure Active Directory Premium szolgáltatás első lépéseit. Vegye figyelembe, hogy ha a frissítés előtt nem rendelkezett tevékenységi adatokkal, az adatok nak néhány napig kell tartaniuk, amíg az adatok megjelennek a jelentésekben a prémium licencre való frissítés után.
* A **felhasználó,** aki a biztonsági rendszergazda , **biztonsági olvasó**, **jelentésolvasó** vagy **globális rendszergazdai** szerepkör a bérlő. Ezen felül a bérlő minden felhasználója elérheti a magára vonatkozó bejelentkezéseket.

## <a name="quickstart-download-a-sign-in-report"></a>Első lépések: Bejelentkezési jelentés letöltése

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. Válassza ki **Azure Active Directoryt** a bal oldali navigációs ablaktáblában, és a **címtár váltása** gombra kattintva válassza ki az aktív címtárat.
3. Az irányítópulton az **Azure Active Directory** kiválasztása után válassza a **Bejelentkezéseket**. 
4. Válassza az **elmúlt 24 órát** a **Dátum** szűrő legördülő listájából, majd az **Alkalmaz** választása után megtekintheti az utolsó 24 óra bejelentkezéseit. 
5. Válassza a **Letöltés** gombot, válassza a **CSV** fájlformátumot, és adjon meg egy fájlnevet a szűrt rekordokat tartalmazó CSV-fájl letöltéséhez. 

![Jelentéskészítés](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>További lépések

* [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon](concept-sign-ins.md)
* [Az Azure Active Directory jelentéskészítés megőrzése](reference-reports-data-retention.md)
* [Az Azure Active Directory jelentéskészítés késése](reference-reports-latencies.md)
