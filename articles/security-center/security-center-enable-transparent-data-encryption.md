---
title: Az Azure Security Centerben a transzparens adattitkosítás engedélyezése |} A Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan valósíthat meg az Azure Security Center javaslatait **transzparens adattitkosítás engedélyezése**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 1f49930449c5ca342159f2a4eca3d7167dfed925
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244644"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Az Azure Security Centerben a transzparens adattitkosítás engedélyezése
Azure Security Center javasolni fogja engedélyezése transzparens adattitkosítási (TDE) az SQL-adatbázisokon Ha TDE már nem engedélyezett. TDE védi az adatokat, és segít a megfelelőségi követelmények teljesítése érdekében az alkalmazás módosítása nélkül az adatbázisban, az azokhoz kapcsolódó biztonsági mentési, és a tranzakciós naplófájlokat az inaktív titkosításával. További tudnivalókért tekintse meg [az Azure SQL Database transzparens adattitkosítási](https://msdn.microsoft.com/library/dn948096).

Ez a javaslat vonatkozik; csak az Azure SQL szolgáltatásba nem tartalmazza a virtuális gépeken futó SQL.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslatok megvalósítása
1. Az a **javaslatok** panelen válassza ki **transzparens adattitkosítás engedélyezése**.
   ![Transzparens adattitkosítás engedélyezése][1]
2. Ekkor megnyílik a **transzparens adattitkosítás engedélyezése az SQL-adatbázisokon** panelen. Válassza ki az SQL-adatbázis is engedélyezni szeretné a TDE be.
   ![Válassza ki a TDE engedélyezése az SQL DB][2]
3. Az a **transzparens adattitkosítás** panelen válassza ki **ON** adattitkosítás, és válassza ki a **mentése** a panel a felső szalagon.
   ![Kapcsolja be a TDE][3]

   Miután TDE engedélyezve van a kiválasztott SQL-adatbázis a **titkosítási állapot** változik **titkosított**.    

   ![Titkosítás állapota][4]

## <a name="see-also"></a>Lásd még
Ez a cikk láthatta, hogyan valósíthat meg a Security Center javaslatait "Transzparens adattitkosítás engedélyezése." Az SQL TDE kapcsolatos további információkért tekintse meg a következőket:

* [Az Azure SQL Database transzparens adattitkosítás](https://msdn.microsoft.com/library/dn948096)
* [Ismerkedés a transzparens adattitkosítás (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági hírek és információ.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
