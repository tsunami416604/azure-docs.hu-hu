---
title: Az Azure Active Directory naplóinak integrálása az Azure Log |} A Microsoft Docs
description: Ismerje meg, hogyan telepítse az Azure Log Integration szolgáltatást, és az Azure-auditnaplók naplóinak integrálása
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 01/14/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 473f9db7eb507fa910657696590bb656a8aff52f
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109877"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Az Azure Active Directory naplóinak integrálása

Az Azure Active Directory (Azure AD) naplózott események segít azonosítani a privilegizált műveletek, amelyek az Azure Active Directoryban történt. Láthatja, hogy milyen típusú eseményeket, amelyek áttekintésével nyomon követheti [Azure Active Directory auditnaplójának jelentési eseményei](../active-directory/reports-monitoring/concept-audit-logs.md).


>[!IMPORTANT]
> Az Azure Log integration szolgáltatás 06/01/2019 elavulttá válik. 2018. június 27. AzLog letöltések letiltottuk. Mi a teendő mozgatása előre tekintse át a hozzászólás útmutatást [SIEM-eszközök integrálása az Azure monitor](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Azure Active Directory integrálásának lépései auditnaplók

> [!NOTE]
> Mielőtt megkísérli a jelen cikkben ismertetett lépések, nézze át a [Ismerkedés](security-azure-log-integration-get-started.md) című cikket, és hajtsa végre a megfelelő lépéseket.

1. Nyissa meg a parancssort, és futtassa a következő parancsot:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Futtassa ezt a parancsot: 
 
   ``azlog createazureid``

   Ez a parancs az Azure bejelentkezési kéri. A parancs majd létrehoz egy Azure Active Directory egyszerű szolgáltatást az Azure AD-bérlőt, amelyek az Azure-előfizetést, amelyben a bejelentkezett felhasználó a rendszergazda, társ-rendszergazda vagy tulajdonos. A parancs sikertelen lesz, ha a bejelentkezett felhasználó csak az Azure AD-bérlő vendégfelhasználó. Hitelesítés az Azure-bA az Azure AD-n keresztül történik. Az Azure AD-identitásnak, hogy az Azure-előfizetésekből olvasási hozzáférést kap az Azure Log Integration egyszerű szolgáltatás létrehozása hoz létre.

3. Futtassa a következő parancsot, adja meg a bérlő azonosítóját. A parancs futtatásához a bérlői rendszergazda szerepkör tagjának lennie kell.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Példa:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Ellenőrizze a következő mappák győződjön meg arról, hogy az Azure Active Directory naplózási log JSON-fájlok jönnek létre számukra:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

A következő videó bemutatja a cikkben szereplő lépéseket:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> További fejlesztéseket végzünk az adatokat a biztonsági információk és eseménykezelő (SIEM) rendszer JSON-fájljaiban szereplő információk arra vonatkozóan lépjen kapcsolatba a SIEM gyártójával.

Közösségi támogatás érhető el a [Azure Log Integration MSDN-fórumában](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Ezen a fórumon lehetővé teszi, hogy az Azure Log Integration Közösség támogatására, egymással kérdések, válaszok, tippek és trükkök a tagjai. Emellett az Azure Log Integration csapat ezen a fórumon figyeli, és minden alkalommal, amikor azt is segít.

Megnyithatja a [támogatási kérelem](../azure-supportability/how-to-create-azure-support-request.md). Válassza ki **Naplóintegráció** a kért támogatási szolgáltatással.

## <a name="next-steps"></a>További lépések
Az Azure Log Integration kapcsolatos további információkért lásd:

* [A Microsoft Azure Log Integration az Azure-naplók](https://www.microsoft.com/download/details.aspx?id=53324): A letöltőközpontlapon nyújt részleteket, a rendszerkövetelmények és telepítési utasításokat az Azure Log Integration.
* [Bevezetés az Azure Log Integration](security-azure-log-integration-overview.md): Ez a cikk bemutatja, Azure Log Integration, annak főbb funkcióit és működését.
* [Gyakori kérdések az Azure Log Integration](security-azure-log-integration-faq.md): Ez a cikk az Azure Log Integration kapcsolatos kérdésekre ad választ.
* [Új funkciók az Azure Diagnostics és az Azure-auditnaplók](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): Ebben a blogbejegyzésben bemutatja az Azure-auditnaplók, és más funkciók, amelyek segítségével betekintést nyerhet az Azure-erőforrások műveletei.
