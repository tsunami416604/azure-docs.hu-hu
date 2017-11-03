---
title: "Engedélyezze az átlátható adattitkosítást az Azure Security Centerben |} Microsoft Docs"
description: "Ez a dokumentum azt ismerteti, hogyan valósítja meg az Azure Security Center ajánlás ** engedélyezése átlátszó adatok titkosítás **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 2a2963affdbff3710ad08f86c6ed4e6304335559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Engedélyezze az átlátható adattitkosítást az Azure Security Centerben
Azure Security Center javasolni fogja engedélyezése átlátszó Data Encryption (TDE) az SQL-adatbázisok, ha a TDE nincs engedélyezve. TDE védi az adatokat, és segítséget nyújt a megfelelőségi követelményeknek való megfelelést az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok nyugalmi, anélkül, hogy az alkalmazást módosítani kellene történő. További részletek további [átlátható adattitkosítást az Azure SQL Database](https://msdn.microsoft.com/library/dn948096).

Ez a javaslat vonatkozik az Azure SQL-szolgáltatás csak; a virtuális gépeken futó SQL nem tartalmaz.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
1. Az a **javaslatok** panelen válassza **átlátható adattitkosítási engedélyezése**.
   ![Transzparens adattitkosítás engedélyezése][1]
2. Ekkor megnyílik a **átlátható adattitkosítási engedélyezése az SQL-adatbázisok** panelen. Válassza ki a TDE engedélyezése az SQL-adatbázis.
   ![Válassza ki az SQL-adatbázis a TDE engedélyezése][2]
3. Az a **átlátható adattitkosítás** panelen válassza **ON** adatok titkosítását, és válassza a **mentése** a panel felső szalagon.
   ![Kapcsolja be a TDE][3]

   Ha a TDE engedélyezve van a kiválasztott SQL-adatbázis a **titkosítási állapotát** módosul **titkosított**.    

   ![A titkosítás állapota][4]

## <a name="see-also"></a>Lásd még:
Ez a cikk bemutatta megvalósításához a Security Center ajánlás "Engedélyezés átlátható adattitkosítási." Erőforráscsoportoknál kapcsolatos további tudnivalókért olvassa el a következőket:

* [Az Azure SQL Database átlátható adattitkosítás](https://msdn.microsoft.com/library/dn948096)
* [Ismerkedés a transzparens adatok titkosítás (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának figyelésére.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) --az Azure biztonsági legfrissebb hírek és információ.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
