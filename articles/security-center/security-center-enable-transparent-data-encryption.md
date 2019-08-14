---
title: transzparens adattitkosítás engedélyezése a Azure Security Centerban | Microsoft Docs
description: Ebből a dokumentumból megtudhatja, hogyan valósítja meg a **transzparens adattitkosítás engedélyezése**Azure Security Center ajánlást.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d5ddec40a1b20e377ec18ce871018f674557e7b4
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "60703999"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>transzparens adattitkosítás engedélyezése Azure Security Center
Azure Security Center azt javasolja, hogy engedélyezze a transzparens adattitkosítás (TDE) szolgáltatást az SQL-adatbázisokon, ha a TDE még nincs engedélyezve. A TDE megvédi adatait, és segít a megfelelőségi követelmények teljesítésében azáltal, hogy az adatbázist, a kapcsolódó biztonsági mentéseket és a tranzakciós naplófájlokat a REST-ben titkosítja, anélkül, hogy módosítania kellene az alkalmazást. További információ: [transzparens adattitkosítás Azure SQL Database](https://msdn.microsoft.com/library/dn948096).

Ez az ajánlat csak az Azure SQL szolgáltatásra vonatkozik; nem tartalmazza a virtuális gépeken futó SQL-t.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslat implementálása
1. A **javaslatok** panelen válassza a **transzparens adattitkosítás engedélyezése**lehetőséget.
   ![Transzparens adattitkosítás engedélyezése][1]
2. Ekkor megnyílik a **transzparens adattitkosítás engedélyezése az SQL-adatbázisok** panelen. Válasszon ki egy SQL-adatbázist a TDE engedélyezéséhez.
   ![Válassza az SQL DB lehetőséget a TDE engedélyezéséhez][2]
3. Az **átlátszó adattitkosítás** panelen válassza **a** be lehetőséget az adattitkosítás területen, majd a panel felső szalagján kattintson a **Mentés** elemre.
   ![TDE bekapcsolása][3]

   Ha a TDE engedélyezve van a kiválasztott SQL-adatbázisban, a **titkosítási állapot** titkosítottraváltozik.    

   ![Titkosítás állapota][4]

## <a name="see-also"></a>Lásd még
Ez a cikk bemutatja, hogyan valósítja meg a "transzparens adattitkosítás engedélyezése" Security Center ajánlást. Az SQL TDE kapcsolatos további tudnivalókért tekintse meg a következőket:

* [transzparens adattitkosítás a Azure SQL Database](https://msdn.microsoft.com/library/dn948096)
* [Ismerkedés a transzparens adattitkosításrel (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md) – megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](https://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági Hírek és információk beszerzése.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
