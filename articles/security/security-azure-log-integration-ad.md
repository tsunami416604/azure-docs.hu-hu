---
title: "Vizsgálati naplóit Azure Active Directory integrálása az Azure napló |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítse az Azure napló integrációs szolgáltatást, és integrálhatja a naplók az Azure felügyeleti naplók"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/08/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 8a1295cc86057ed72940e774d0bd423d61142e31
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrálása az Azure Active Directory-naplók

Az Azure Active Directory (Azure AD) naplózási események segítségével azonosíthatja a privilegizált műveletekhez, hogy megtörtént az Azure Active Directoryban. Láthatja, hogy milyen típusú eseményeket vizsgálatával nyomon követett [Azure Active Directory auditnaplójának jelentési eseményei](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md).

> [!NOTE]
> A cikkben ismertetett-megkezdése előtt át kell néznie a [Ismerkedés](security-azure-log-integration-get-started.md) cikk és a lépéseket van.

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Azure Active directory integrálásának lépései naplók

1. Nyissa meg a parancssort, és futtassa a parancsot:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Futtassa ezt a parancsot: 
 
   ``azlog createazureid``

   Ez a parancs kéri az Azure bejelentkezési. A parancs majd hoz létre egy Azure Active Directory szolgáltatás egyszerű az Azure AD-bérlőkkel, amely az Azure-előfizetéseket, amelyben a bejelentkezett felhasználó nem rendszergazda, a társadminisztrátorának vagy a tulajdonos tárolni. A parancs sikertelen lesz, ha a bejelentkezett felhasználó csak a Vendég felhasználó az Azure AD-bérlő. Hitelesítés az Azure-bA az Azure AD segítségével történik. Az Azure AD identity arra, hogy az Azure-előfizetések olvasási hozzáférést egy egyszerű Azure napló integrációs szolgáltatás létrehozásakor létrejön.

3. A következő parancsot adja meg a bérlő azonosítójával. A parancs futtatásához a bérlői rendszergazda szerepkör tagjának lennie kell.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Példa:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Ellenőrizze a következő mappák győződjön meg arról, hogy az Azure Active Directory JSON naplófájlok bennük jönnek létre:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

A következő videó bemutatja a cikkben szereplő lépéseket:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> A JSON-fájlokban szereplő információk üzembe a biztonsági információk és eseménykezelő rendszer (SIEM) kapcsolatos tudnivalókat forduljon a SIEM gyártójához.

Közösségi támogatás érhető el a [Azure napló integrációs MSDN fórumon](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Ezen a fórumon lehetővé teszi, hogy a személyek Azure napló integráció közösségi egymással kérdéseket, válaszokat, tippeket és trükkök támogatásához. Ezenkívül az Azure napló integrációs csoport ezen a fórumon figyeli, és amikor azt is segít.

Is megnyithatja a [támogatási kérelem](../azure-supportability/how-to-create-azure-support-request.md). Válassza ki **napló integrációs** a szolgáltatást, amelynek támogatási kérelmet.

## <a name="next-steps"></a>Következő lépések
Azure napló integrációs kapcsolatos további információkért lásd:

* [A Microsoft Azure napló integráció az Azure-naplók](https://www.microsoft.com/download/details.aspx?id=53324): A letöltőközpontból weblap, amely részleteit, rendszerkövetelmények és telepítési utasításokat az Azure napló integráció.
* [Bevezetés az Azure napló integrációs](security-azure-log-integration-overview.md): Ez a cikk bemutatja a Azure napló integrációs, annak főbb funkcióit és annak működéséről.
* [Partner konfigurációs lépések](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Ebben a blogbejegyzésben bemutatja, hogyan használható Splunk, HP ArcSight és az IBM QRadar partneri megoldások Azure napló-integráció konfigurálása.
* [Az Azure napló integrációs gyakran ismételt kérdések](security-azure-log-integration-faq.md): Ez a cikk Azure napló integrációs kapcsolatos kérdésekre ad választ.
* [A Security Center riasztásait integrálása Azure napló integrációs](../security-center/security-center-integrating-alerts-with-log-integration.md): Ez a cikk bemutatja, hogyan szinkronizálása a Security Center riasztásait, együtt a virtuális gép biztonsági események által gyűjtött Azure Diagnostics és az Azure naplózási naplókat, a naplóelemzési vagy a SIEM-megoldás.
* [Az Azure Diagnostics és az Azure új szolgáltatásai naplók](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): Ebben a blogbejegyzésben bemutatja Azure vizsgálati naplók és egyéb szolgáltatásokat, amelyek segítenek betekintést nyerhet az Azure-erőforrások működésére.
