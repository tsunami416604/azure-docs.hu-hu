---
title: 'Rövid útmutató: Bejelentkezési jelentés letöltése az Azure portál használatával | Microsoft Docs'
description: Ismerje meg, hogyan tölthet le bejelentkezési jelentést az Azure portál használatával
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cbd71696c3508a464b1343d552cba4a3391066f
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989670"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Gyors útmutató: Bejelentkezési jelentés letöltése a Azure Portal használatával

Ebben a rövid útmutatóban megismerheti, hogyan lehet az elmúlt 24 órára vonatkozóan a bérlő bejelentkezési adatait letölteni. Legfeljebb 250 000 rekordot tölthet le a Azure Portalból. A rekordok a legutóbbiek szerint rendezve jelennek meg, így alapértelmezés szerint a legfrissebb 250 000 rekordokat kapja meg. 

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

* Azure Active Directory-bérlő prémium licenccel a bejelentkezési tevékenységek jelentésének megtekintéséhez. A Azure Active Directory kiadásának frissítéséhez tekintse meg a [prémium szintű Azure Active Directory első lépéseivel foglalkozó](../fundamentals/active-directory-get-started-premium.md) témakört. Vegye figyelembe, hogy ha a frissítés előtt nem rendelkezik tevékenységi adataival, a rendszer több napot is igénybe vesz, hogy az adatai megjelenjenek a jelentésekben a prémium szintű licencre való frissítés után.
* Egy felhasználó, aki a **biztonsági rendszergazda**, a **biztonsági olvasó**, a **jelentéskészítő** vagy a **globális rendszergazdai** szerepkör tagja a Bérlőnek. Ezen felül a bérlő minden felhasználója elérheti a magára vonatkozó bejelentkezéseket.

## <a name="quickstart-download-a-sign-in-report"></a>Gyors útmutató: Bejelentkezési jelentés letöltése

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **Azure Active Directoryt** a bal oldali navigációs ablaktáblában, és a **címtár váltása** gombra kattintva válassza ki az aktív címtárat.
3. Az irányítópulton az **Azure Active Directory** kiválasztása után válassza a **Bejelentkezéseket**. 
4. Válassza az **elmúlt 24 órát** a **Dátum** szűrő legördülő listájából, majd az **Alkalmaz** választása után megtekintheti az utolsó 24 óra bejelentkezéseit. 
5. Válassza a **Letöltés** gombot, válassza a **CSV** formátumot Fájlformátumként, és adjon meg egy fájlnevet a szűrt rekordokat tartalmazó CSV-fájl letöltéséhez. 

![Jelentéskészítés](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>További lépések

* [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon](concept-sign-ins.md)
* [Az Azure Active Directory jelentéskészítés megőrzése](reference-reports-data-retention.md)
* [Az Azure Active Directory jelentéskészítés késése](reference-reports-latencies.md)
