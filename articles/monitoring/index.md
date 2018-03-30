---
title: Az Azure felügyeleti - figyelési |} Microsoft Docs
description: Azure több szolgáltatásokat és eszközöket, amelyek együttműködve biztosítják a teljes felügyeleti nem csak a futó Azure-ban, hanem más felhők és a helyszíni alkalmazások rendelkezik.  Ez a cikk ismerteti magas szintű felügyeleti és hivatkozások olyan témakörökre mutatnak a különböző területek az Azure-eszközök a felhőalapú alkalmazások és erőforrások kezelésére.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: bwren
ms.openlocfilehash: b20283e1189e4f1a3555e2dd8d25972c9a677cd6
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="azure-management---monitoring"></a>Az Azure felügyeleti - figyelés

Figyelése az Azure-ban az Azure felügyeleti egyik tulajdonsága.  Ez a cikk röviden ismerteti a különböző területek telepíteni és fenntartani az alkalmazások és erőforrások az Azure-ban dokumentációjára mutató hivatkozásokat, az első lépésekhez szükséges felügyeleti.

## <a name="management-in-azure"></a>Kezelés az Azure-ban

Felügyeleti feladatok és az üzleti alkalmazások és az őket támogató erőforrások fenntartásához szükséges folyamatok hivatkozik.  Azure több szolgáltatásokat és eszközöket, amelyek együttműködve biztosítják a teljes felügyeleti nem csak a futó Azure-ban, hanem más felhők és a helyszíni alkalmazások rendelkezik.  Megértése, elérhető különböző eszközökről, és hogyan ezek együtt is használható a különböző felügyeleti lehetőségeket, a a teljes felügyeleti környezet tervezésének első lépése.

A következő ábra szemlélteti a különböző területek felügyeleti bármilyen alkalmazás vagy erőforrás fenntartásához szükséges.  Ezek a különböző területek-re egy életciklussal tekintetében amelyeknél minden szükséges folyamatos egymás után felett erőforrást élettartamát.  Ezzel elindítja a kezdeti telepítés keresztül annak folyamatos működését, és végül a kivont mikor.

![Kezelési képességek](media/management-overview/management-capabilities.png)


Nincs egyetlen Azure szolgáltatás teljesen beírja egy adott felügyeleti területen követelményeinek, de ehelyett minden egyes vannak megvalósítania több szolgáltatásban működik együtt.  Egyes szolgáltatások célzott funkciók, például az Application Insights, amely figyelést biztosít a webalkalmazások biztosítanak.  Mások például a felügyeleti adatokat más szolgáltatásaihoz, hogy lehetővé teszi a különböző szolgáltatások által gyűjtött különböző típusú adatok elemzésére tároló Naplóelemzési közös funkciókat biztosít.  

A következő szakaszok röviden ismerteti a különböző kezelési területekre, és tartalmazza a fő Azure-szolgáltatás részletes tartalomra mutató hivatkozásokat célja őket.

## <a name="monitor"></a>Figyelés
Figyelési összegyűjtése és annak meghatározásához, a teljesítmény, állapotának és rendelkezésre állását, valamint az üzleti alkalmazás attól függ, az erőforrások elemzéséhez. Egy hatékony felügyeleti stratégia segítenek megérteni a különböző összetevőket az alkalmazás és a hasznos üzemidő növeléséhez a proaktív értesítés, kritikus fontosságú problémáit, hogy a megoldásuk mielőtt azok veszélyeztetnék részletes működését.  El tudja olvasni, amely azonosítja a különböző szolgáltatásokat a felügyeleti stratégia, használja az Azure-ban Figyelés áttekintése [figyelése Azure-alkalmazások és erőforrások](monitoring-overview.md).


## <a name="configure"></a>Konfigurálás
Konfigurálja a kezdeti üzembe helyezése és az alkalmazások és erőforrások és a frissítések és javítások folyamatos karbantartási konfigurációs hivatkozik.  Automatizálási ezeket a feladatokat, parancsfájl és házirend segítségével lehetővé teszi a redundanciát, amelyekkel lecsökkentheti a időt és erőfeszítést, és a pontosság és a hatékonyság növelése megszüntetéséhez.  [Azure Automation szolgáltatásbeli](..\automation\automation-intro.md) biztosít a tömeges szolgáltatások a konfigurációs feladatok automatizálásához.  Runbookok a folyamatok automatizálásához, mellett biztosít konfigurációs és a frissítések kezelése, amely segítséget nyújt a házirendeken keresztül, és megpróbálja majd azonosítani és frissítéseinek telepítéséhez konfiguráció kezelése.

## <a name="govern"></a>Govern
Cégirányítási biztosít olyan mechanizmusok és folyamatok, így átfogóan szabályozhatja az alkalmazások és erőforrások az Azure-ban.  A kezdeményezések tervezési és a stratégiai prioritások beállítása magában foglalja.  Az Azure-ban irányítás két szolgáltatás elsősorban hajtanak végre.  [Az Azure házirend](../azure-policy/azure-policy-introduction.md) hozhat létre, meg és, kezelheti a házirend-definíciók száma, amelyeket eltérő szabályok és a műveletek keresztül az erőforrásokat, ezért ezeket az erőforrásokat a a vállalati előírásoknak való megfelelőségének marad, és szolgáltatási szintek. [Az Azure költség-kezelést úgy Cloudyn](../cost-management/overview.md) nyomon követheti a felhőalapú használata és az Azure-erőforrások és más szolgáltatók beleértve AWS és Google költségekkel jár.

## <a name="secure"></a>Biztonságos
Az alkalmazások, az erőforrások és az adatok biztonsági kezelése magában foglalja a értékelésének fenyegetéseket, összegyűjtése és biztonsági adatok elemzése, és győződjön meg arról, hogy az alkalmazások és erőforrások tervezhetők meg és biztonságos módon konfigurált kombinációja.  Biztonsági figyelést és a fenyegetések elemzés által biztosított [az Azure Security Center](../security-center/security-center-intro.md) tartalmaz, amely egységes biztonsági kezelése és az advanced threat protection hibrid felhő-munkaterhelések között.  Emellett meg kell jelennie [Bevezetés az Azure biztonsági](../security/azure-security.md) átfogó Azure-ban és útmutatást biztonságosan konfigurálása az Azure-erőforrások biztonsági információt.


## <a name="protect"></a>Védelem
Védelmi annak biztosításához, hogy az alkalmazások és adatok mindig elérhető, a vezérlő felett kimaradások esetén is hivatkozik.  Az Azure-ban védelmi két szolgáltatást biztosítja.  [Azure biztonsági mentés](../backup/backup-introduction-to-azure-backup.md) biztosít a biztonsági mentési és helyreállítási adatait, vagy a felhőben, vagy a helyszínen.    [Az Azure Site Recovery](../site-recovery/site-recovery-overview.md) magas rendelkezésre állás, az alkalmazás biztosítja az üzletmenet folytonossága és vészhelyreállítás esetén azonnali helyreállítási megadásával.

## <a name="migrate"></a>Migrate (Áttelepítés) 
Áttelepítési hivatkozik a jelenleg futó Azure felhőben történő helyszíni munkaterhelések tér át.  [Az Azure áttelepítése](../migrate/migrate-overview.md) egy szolgáltatás, amely segítséget nyújt az áttelepítési alkalmasság, beleértve a teljesítmény-alapú méretezési értékeléséhez, ráadásul a költségek becslése a helyszíni virtuális gépek Azure-bA.  Az Azure Site Recovery segítségével a tényleges áttelepítése a virtuális gépek [helyszíni](../site-recovery/migrate-tutorial-on-premises-azure.md) vagy [az Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md).  [Az Azure adatbázis áttelepítési](../dms/dms-overview.md) érhető el több adatbázis forrás áttelepítése az Azure platformra.