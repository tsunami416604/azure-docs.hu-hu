---
title: Útmutató, hogyan töltse le és elérése a bejelentkezési parancsfájl használatával naplózza |} A Microsoft Docs
description: Ismerje meg, hogyan töltheti le, és a egy PowerShell-szkripttel bejelentkezési naplók eléréséhez.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4afe0c73-aee8-47f1-a6cb-2d71fd6719d1
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 8b9097a62ca4bfa67fb5eb35e06f7834df6691e7
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622805"
---
# <a name="tutorial-how-to-download-and-use-a-script-to-access-sign-in-logs"></a>Oktatóanyag: Hogyan töltse le és parancsfájl használatával jelentkezzen be a naplók elérése

Letöltheti a bejelentkezési tevékenységek adatait, ha az Azure Portalon kívül szeretné használni őket. A **letöltése** lehetőség az Azure Portalon hoz létre egy CSV-fájlt a legutóbbi 5000 rekordok. Ha nagyobb rugalmasságot, például egyszerre több mint 5000 rekordok letöltése vagy ütemezett időközönként, a naplók letöltéséhez használhatja az **parancsfájl** adatok letöltése a PowerShell-szkriptet gombra.

Ebben az oktatóanyagban elsajátíthatja, hogyan hozhat létre egy parancsfájlt, amely minden bejelentkezési naplók letöltése az elmúlt 24 órában, és minden nap ütemezheti. 

## <a name="prerequisites"></a>Előfeltételek

szükséged van

* Az Azure Active Directory-bérlő prémium verzió (P1 vagy P2) licenccel. 
* A felhasználó, aki az a **globális rendszergazdai**, **biztonsági rendszergazda**, **biztonsági olvasó** vagy **jelentést olvasó** szerepkör a bérlő számára. Emellett bármely felhasználó hozzáférhet a saját bejelentkezések. 
* Ha azt szeretné, a Windows 10-es gépen a letöltött szkript futtatásához [beállítva a végrehajtási házirendet és az AzureRM-modul](concept-sign-ins.md#running-the-script-on-a-windows-10-machine).

## <a name="tutorial"></a>Oktatóanyag

1. Keresse meg a [az Azure portal](https://portal.azure.com) válassza ki azt a címtárat.
2. Válassza ki **Azure Active Directory** válassza **bejelentkezések** származó a **figyelés** szakaszban. 
3. Használja a **dátumtartomány** szűrőt a listából, és válassza ki **24 óra** le adatokat az elmúlt 24 órában. 
4. Válassza ki **alkalmaz** , és ellenőrizze, hogy a szűrő a várt módon. 
5. Válassza ki **parancsfájl** a felső menüben, a Powershell-szkript letöltése az alkalmazott szűrőket.

     ![Parancsprogram letöltése](./media/tutorial-signin-logs-download-script/download-script.png)
     
6. Nyissa meg a **Feladatütemező** alkalmazás a Windows-számítógépen, majd válassza ki a **alapszintű feladat létrehozása**.
7. Adjon meg egy nevet és leírást a feladat, és kattintson a **tovább**.
8. Válassza ki a **napi** , hogy a feladat futtatása naponta, és adja meg a kezdő dátum és idő választógombot.
9. A művelet menüben válassza ki a **programot elindítani** , és válassza ki a letöltött parancsprogramot, és válassza ki **tovább**. 
10. Tekintse át az ütemezett feladatot, és válassza ki **Befejezés** a feladat létrehozásához.

     ![Feladat létrehozása](./media/tutorial-signin-logs-download-script/create-task.png)

Most, a feladat minden nap futtatja és formátumú fájlba mentése a bejelentkezési rekordokat az elmúlt 24 órában **AAD_SignInReport_YYYYMMDD_HHMMSS.csv**. A letöltött PowerShell-parancsfájl egy másik fájllal néven mentheti, vagy letöltött rekordok számának módosításához szerkesztheti is. 

## <a name="next-steps"></a>További lépések

* [Az Azure Active Directory jelentésmegőrzési házirendje](reference-reports-data-retention.md)
* [Bevezetés az Azure Active Directory Premium Reporting API használatába](concept-reporting-api.md)
* [A jelentéskészítési API-hozzáférés tanúsítványokkal](tutorial-access-api-with-certificates.md)
