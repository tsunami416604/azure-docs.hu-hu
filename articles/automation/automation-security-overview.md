---
title: Bevezetés az Azure Automation szolgáltatásbeli hitelesítéshez
description: Ez a cikk az Automation biztonságáról és az Azure Automation-fiókok számára elérhető különböző hitelesítési módszerekről nyújt áttekintést.
keywords: automation-biztonság, automation biztonságossá tétele; automation-hitelesítés
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ROBOTS: NOINDEX
ms.openlocfilehash: 327bb15ab8536dca85b4cbb07216080b135c769a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194903"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Bevezetés a hitelesítési folyamatba az Azure Automationben  
Az Azure Automation lehetővé teszi a feladatok automatizálását az Azure erőforrásain, továbbá olyan felhőszolgáltatókkal, mint az Amazon webszolgáltatások (AWS).  Annak érdekében, hogy a forgatókönyv elvégezze a szükséges műveleteket, engedélyekkel kell rendelkeznie az erőforrások biztonságos eléréséhez az előfizetésben szükséges minimális jogokkal.

Ez a cikk lefedi az Azure Automation által támogatott különböző hitelesítési forgatókönyveket, és megmutatja, hogyan kell megtenni az első lépéseket a kezelendő környezettől vagy környezetektől függően.  

## <a name="automation-account-overview"></a>Az Automation-fiókok áttekintése
Amikor először indítja el az Azure Automationt, legalább egy Automation-fiókot létre kell hoznia. Az Automation-fiókok lehetővé teszik, hogy elkülönítse az Automation erőforrásait (forgatókönyvek, adategységek, konfigurációk) a más Automation-fiókokban tárolt erőforrásoktól. Az Automation-fiókok segítségével külön logikai környezetekre választhatja szét az erőforrásokat. Használhat például egy fiókot fejlesztéshez, egy másikat az üzemi használatra, egy harmadikat pedig a helyszíni környezethez.  Az Azure Automation-fiók különbözik a Microsoft-fiókjától vagy az Azure-előfizetésében létrehozott fiókoktól.

Az Azure-fiókokhoz tartozó Automation-erőforrások egy Azure-régióhoz tartoznak, de az Automation-fiókok képesek az előfizetés összes erőforrását kezelni. A különböző régiókban levő Automation-fiókok létrehozásának fő oka az lehet, ha a házirendeknek adatokra és erőforrásokra van szükségük ahhoz, hogy külön régióra különülhessenek el.

Az összes feladatot, amelyet az Azure Resource Manager és az Azure Automation parancsmagjainak használatával az erőforrásokon végrehajt, hitelesíteni kell az Azure Active Directory szervezetiidentitás-hitelesítésével.  A tanúsítványalapú hitelesítés volt az eredeti hitelesítési módszer a klasszikus Azure-ban, de azt bonyolult volt beállítani.  Az Azure felé egy Azure AD-felhasználóval történő hitelesítés lehetőségét 2014-ben vezettük be, nem csak a hitelesítési fiókok konfigurálási folyamatának leegyszerűsítéséért, hanem hogy az Azure-ba nem interaktívan, egyetlen felhasználói fiókkal történő hitelesítés képességét is támogassa, amely működött az Azure Resource Managerrel és klasszikus erőforrásokkal is.   

Jelenleg, amikor létrehoz egy új Automation-fiókot az Azure Portalon, azzal automatikusan létrehozza az alábbiakat:

* Egy futtató fiókot, amely létrehoz egy új egyszerű szolgáltatást az Azure Active Directoryban, létrehoz egy tanúsítványt, valamint kiosztja a Közreműködő szerepköralapú hozzáférés-vezérlést (RBAC), amelynek használatával a Resource Manager-erőforrások kezelhetők runbookokkal.
* Egy klasszikus futtató fiókot egy felügyeleti tanúsítvány feltöltésével, amely segítségével a klasszikus Azure-erőforrások kezelhetők runbookokkal.  

A szerepköralapú hozzáférés-vezérlés az Azure Resource Managerben érhető el, hogy hozzáférést adjon az engedélyezett műveleteknek egy Azure AD-felhasználói fiókhoz és futtatófiókhoz, és hitelesítse az egyszerű szolgáltatást.  Az Automation-engedélyek kezelésére használt modell fejlesztéséhez további információkért olvassa el [Az Azure Automation szerepköralapú hozzáférés-vezérlése](automation-role-based-access-control.md) című cikket.  

Az adatközpontban egy hibrid forgatókönyv-feldolgozón vagy az AWS számítástechnikai szolgáltatásain futó forgatókönyvek nem használhatják ugyanazt a módszert, amelyet az Azure-erőforrásokon hitelesítő forgatókönyvek használnak.  Ennek oka az, hogy azok az erőforrások az Azure-on kívül futnak, és emiatt az Automation szolgáltatásban meghatározott saját biztonsági hitelesítő adataikra van szükség a helyileg elérhető erőforrásokhoz történő hitelesítéshez.  

## <a name="authentication-methods"></a>Hitelesítési módszerek
A következő táblázat összefoglalja az Azure Automation által támogatott összes környezet különböző hitelesítési módszereit, valamint az azt ismertető cikket, hogy miként lehet beállítani a hitelesítést a forgatókönyvekhez.

| Módszer | Környezet | Cikk |
| --- | --- | --- |
| Azure AD felhasználói fiók |Az Azure Resource Manager és a klasszikus Azure |[Runbookok hitelesítése Azure AD-felhasználói fiókkal](automation-create-aduser-account.md) |
| Azure-futtatófiók |Azure Resource Manager |[Runbookok hitelesítése Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) |
| Klasszikus Azure-futtatófiók |Klasszikus Azure portál |[Runbookok hitelesítése Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) |
| Windows-hitelesítés |Helyszíni adatközpont |[Runbookok hitelesítése hibrid runbook-feldolgozókhoz](automation-hybrid-runbook-worker.md) |
| AWS hitelesítő adatok |Amazon webszolgáltatások |[Runbookok hitelesítése az Amazon webszolgáltatásokkal (AWS)](automation-config-aws-account.md) |
