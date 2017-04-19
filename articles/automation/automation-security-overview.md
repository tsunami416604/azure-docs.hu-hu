---
title: "Bevezetés a hitelesítési folyamatba az Azure Automationben | Microsoft Docs"
description: "Ez a cikk az Automation biztonságáról és az Azure Automation-fiókok számára elérhető különböző hitelesítési módszerekről nyújt áttekintést."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
keywords: "automation-biztonság, automation biztonságossá tétele; automation-hitelesítés"
ms.assetid: 4a6bc2f5-c5a2-4dfb-b10d-7950d750dee8
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 1a0258e872449bf2f2f08345cbe86564e28d964e
ms.lasthandoff: 04/17/2017


---
# <a name="introduction-to-authentication-in-azure-automation"></a>Bevezetés a hitelesítési folyamatba az Azure Automationben  
Az Azure Automation lehetővé teszi a feladatok automatizálását az Azure erőforrásain, továbbá olyan felhőszolgáltatókkal, mint az Amazon webszolgáltatások (AWS).  Annak érdekében, hogy a forgatókönyv elvégezze a szükséges műveleteket, engedélyekkel kell rendelkeznie az erőforrások biztonságos eléréséhez az előfizetésben szükséges minimális jogokkal.

Ez a cikk lefedi az Azure Automation által támogatott különböző hitelesítési forgatókönyveket, és megmutatja, hogyan kell megtenni az első lépéseket a kezelendő környezettől vagy környezetektől függően.  

## <a name="automation-account-overview"></a>Az Automation-fiókok áttekintése
Amikor először indítja el az Azure Automationt, legalább egy Automation-fiókot létre kell hoznia. Az Automation-fiókok lehetővé teszik, hogy elkülönítse az Automation erőforrásait (forgatókönyvek, adategységek, konfigurációk) a más Automation-fiókokban tárolt erőforrásoktól. Az Automation-fiókok segítségével külön logikai környezetekre választhatja szét az erőforrásokat. Használhat például egy fiókot fejlesztéshez, egy másikat az üzemi használatra, egy harmadikat pedig a helyszíni környezethez.  Az Azure Automation-fiók különbözik a Microsoft-fiókjától vagy az Azure-előfizetésében létrehozott fiókoktól.

Az Azure-fiókokhoz tartozó Automation-erőforrások egy Azure-régióhoz tartoznak, de az Automation-fiókok képesek az előfizetés összes erőforrását kezelni. A különböző régiókban levő Automation-fiókok létrehozásának fő oka az lehet, ha a házirendeknek adatokra és erőforrásokra van szükségük ahhoz, hogy külön régióra különülhessenek el.

> [!NOTE]
> Az Azure portálon létrehozott Automation-fiókok, valamint a rajtuk tárolt erőforrások nem érhetők el a klasszikus Azure portálról. Ha ezeket fiókokat vagy az erőforrásaikat Windows PowerShellel felügyeli, az Azure Resource Manager modulokat kell használnia.
>

Az összes feladatot, amelyet az Azure Resource Manager és az Azure Automation parancsmagjainak használatával az erőforrásokon végrehajt, hitelesíteni kell az Azure Active Directory szervezetiidentitás-hitelesítésével.  A tanúsítványalapú hitelesítés volt az eredeti hitelesítési módszer az Azure szolgáltatásfelügyeleti módban, de azt bonyolult volt beállítani.  Az Azure felé egy Azure AD-felhasználóval történő hitelesítés lehetőségét 2014-ben vezettük be, nem csak a hitelesítési fiókok konfigurálási folyamatának leegyszerűsítéséért, hanem hogy az Azure-ba nem interaktívan, egyetlen felhasználói fiókkal történő hitelesítés képességét is támogassa, amely működött az Azure Resource Managerrel és klasszikus erőforrásokkal is.   

Jelenleg, amikor létrehoz egy új Automation-fiókot az Azure Portalon, azzal automatikusan létrehozza az alábbiakat:

* Egy futtatófiókot, amely létrehoz egy új egyszerű szolgáltatást az Azure Active Directoryban, létrehoz egy tanúsítványt, valamint kiosztja a Közreműködő szerepköralapú hozzáférés-vezérlést (RBAC), amelynek használatával a Resource Manager-erőforrások kezelhetők runbookokkal.
* Egy klasszikus futtatófiókot egy felügyeleti tanúsítvány feltöltésével, amelynek használatával az Azure szolgáltatásfelügyelet erőforrásai vagy a klasszikus erőforrások kezelhetők runbookokkal.  

A szerepköralapú hozzáférés-vezérlés az Azure Resource Managerben érhető el, hogy hozzáférést adjon az engedélyezett műveleteknek egy Azure AD-felhasználói fiókhoz és futtatófiókhoz, és hitelesítse az egyszerű szolgáltatást.  Az Automation-engedélyek kezelésére használt modell fejlesztéséhez további információkért olvassa el [Az Azure Automation szerepköralapú hozzáférés-vezérlése](automation-role-based-access-control.md) című cikket.  

Az adatközpontban egy hibrid forgatókönyv-feldolgozón vagy az AWS számítástechnikai szolgáltatásain futó forgatókönyvek nem használhatják ugyanazt a módszert, amelyet az Azure-erőforrásokon hitelesítő forgatókönyvek használnak.  Ennek oka az, hogy azok az erőforrások az Azure-on kívül futnak, és emiatt az Automation szolgáltatásban meghatározott saját biztonsági hitelesítő adataikra lesz szükség a helyileg elérhető erőforrásokhoz történő hitelesítéshez.  

## <a name="authentication-methods"></a>Hitelesítési módszerek
A következő táblázat összefoglalja az Azure Automation által támogatott összes környezet különböző hitelesítési módszereit, valamint az azt ismertető cikket, hogy miként lehet beállítani a hitelesítést a forgatókönyvekhez.

| Módszer | Környezet | Cikk |
| --- | --- | --- |
| Azure AD felhasználói fiók |Azure Resource Manager és Azure szolgáltatásfelügyelet |[Runbookok hitelesítése Azure AD-felhasználói fiókkal](automation-create-aduser-account.md) |
| Azure-futtatófiók |Azure Resource Manager |[Runbookok hitelesítése Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) |
| Klasszikus Azure-futtatófiók |Azure szolgáltatásfelügyelet |[Runbookok hitelesítése Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) |
| Windows-hitelesítés |Helyszíni adatközpont |[Runbookok hitelesítése hibrid runbook-feldolgozókhoz](automation-hybrid-runbook-worker.md) |
| AWS hitelesítő adatok |Amazon webszolgáltatások |[Runbookok hitelesítése az Amazon webszolgáltatásokkal (AWS)](automation-config-aws-account.md) |

