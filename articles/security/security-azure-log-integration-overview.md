---
title: "Az Azure-erőforrások naplók integrálja a SIEM-rendszerekről |} Microsoft Docs"
description: "Tudnivalók az Azure naplóelemzés integrációs, annak főbb funkcióit és annak működéséről."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/10/2017
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 6c3a2ac18fdb7a7a722447af720b9dee28adef08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Bevezetés a Microsoft Azure napló integráció
Tudnivalók az Azure naplóelemzés integrációs, annak főbb funkcióit és annak működéséről.

## <a name="overview"></a>Áttekintés

Azure naplóelemzés integrációra egy ingyenes megoldás, amely lehetővé teszi az Azure-erőforrások a nyers napló, a helyszíni biztonsági adatai és az esemény felügyeleti SIEM-rendszereken integrálhatja.

Azure naplóelemzés integrációs Windows-eseményeket gyűjti össze a Windows Eseménynapló-naplók [Azure tevékenységi naplóit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [Azure Security Center riasztásait](../security-center/security-center-intro.md), és [Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) a Azure-erőforrások. Ez az integráció a SIEM-megoldás adjon meg egy új, egységesített irányítópult minden eszköz segítségével, a helyszíni vagy a felhőben, így meg tudja-e összesíteni, összefüggéseket, elemzése és biztonsági eseményeket a riasztás.

>[!NOTE]
Jelenleg az egyetlen támogatott felhő hozzá Azure kereskedelmi és Azure Government. Nem támogatja a többi felhőből.

![Azure-naplók integrációja][1]

## <a name="what-logs-can-i-integrate"></a>Milyen naplókat is integrálhatja?
Azure kiterjedt naplózás minden Azure Service eredményez. Ezek a naplók naplók három típusú mutatják be:

* **Ellenőrzés/management-naplók** betekintést nyújtanak a [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) LÉTREHOZÁSI, frissítési és törlési műveletek. Az Azure tevékenységi naplóit például a következő napló.
* **Adatok sík naplók** adja meg az Azure-Erőforrás kihasználtsága részeként kiváltott események láthatósága. Példa az ilyen típusú napló: a Windows Eseménynapló **rendszer**, **biztonsági**, és **alkalmazás** csatornák Windows virtuális gépként. Egy másik példa a diagnosztikai naplózás Azure figyelő konfigurálva
* **A feldolgozott események** elemzett esemény és az Ön nevében feldolgozott riasztási adatokat. Például a következő esemény az Azure Security Center riasztásait, ahol az Azure Security Center feldolgozása és elemzése a adja meg a jelenlegi biztonságot vonatkozó riasztás-előfizetés.

Azure napló-integráció ArcSight, QRadar és Splunk támogatja. Minden esetben ellenőrizze a SIEM-forgalmazójával annak ellenőrzéséhez, hogy azok rendelkeznek egy natív összekötő. Néhány esetben nem kell Azure napló integrációs használandó, ha natív összekötők nem érhető el. További tájékoztatást adjon támogatott a napló típusok, látogasson el a gyakran ismételt kérdések.

>[!NOTE]
Míg Azure napló integrációs szabad megoldást, nincsenek eredő a napló fájl adatokat tároló Azure storage költségei.

Közösségi támogatás érhető el a [Azure napló integrációs MSDN fórumon](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). A fórum teszi lehetővé a AzLog közösségi egymás kérdéseket, válaszokat, tippeket és trükkök a legtöbbet hozhatja ki Azure napló integrációs való támogatása. Emellett az Azure napló integrációs csoport ezen a fórumon figyeli, és amikor azt is segít.

Is megnyithatja a [támogatási kérelem](../azure-supportability/how-to-create-azure-support-request.md). Ehhez az szükséges, válassza ki a **napló integrációs** a szolgáltatást, amelynek támogatási kérelmet.

## <a name="next-steps"></a>Következő lépések
Ebben a dokumentumban megismerhette Azure naplóelemzés integráció. Azure naplóelemzés integráció és támogatott naplók típusait kapcsolatos további tudnivalókért olvassa el a következőket:

* [A Microsoft Azure napló integrációs](https://www.microsoft.com/download/details.aspx?id=53324) – letöltőközpontból részletes rendszerkövetelményeket, és telepítése Azure naplóelemzés integrációs utasításokat.
* [Ismerkedés az Azure naplóelemzés integrációs](security-azure-log-integration-get-started.md) – Ez az oktatóanyag végigvezeti az Azure naplóelemzés integráció telepítése, és az Azure ÜVEGVATTA storage, Azure tevékenységi naplóit, naplók integrálása az Azure Security Center riasztások és az Azure Active Directory naplók.
* [Partner konfigurációs lépések](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – ebben a blogbejegyzésben bemutatja, hogyan használható Splunk, HP ArcSight és az IBM QRadar partneri megoldások Azure naplóelemzés-integráció konfigurálása. Ebben a blogban a partneri megoldások konfigurálásáról az aktuális pozíciót jelöli. Minden esetben tekintse partneri megoldás dokumentációját először.
* [A tevékenység, és az ASC érték riasztások adott QRadar a syslog](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) – ebben a blogbejegyzésben syslog keresztül küldendő tevékenységet és az Azure Security Center riasztások QRadar lépéseit
* [Gyakori kérdések (GYIK) integrációs Azure naplóelemzés](security-azure-log-integration-faq.md) -Ez gyakran ismételt kérdések Azure naplóelemzés integrációs kapcsolatos kérdésekre ad választ.
* [A Security Center integrálása az Azure-ral riasztások jelentkezzen integrációs](../security-center/security-center-integrating-alerts-with-log-integration.md) – Ez a dokumentum azt ismerteti, hogyan szinkronizálása az Azure Security Center riasztásait az Azure napló integráció.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
