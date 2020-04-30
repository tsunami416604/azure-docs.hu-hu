---
title: A Azure Automation hitelesítésének bemutatása
description: Ez a cikk az Automation biztonságáról és az Azure Automation-fiókok számára elérhető különböző hitelesítési módszerekről nyújt áttekintést.
keywords: automation-biztonság, automation biztonságossá tétele; automation-hitelesítés
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 109bb6dd29ea9c4239e0abcfc668f1185f7e9783
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82114530"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Bevezetés a hitelesítési folyamatba az Azure Automationben

Az Azure Automation lehetővé teszi a feladatok automatizálását az Azure erőforrásain, továbbá olyan felhőszolgáltatókkal, mint az Amazon webszolgáltatások (AWS). Annak érdekében, hogy a forgatókönyv elvégezze a szükséges műveleteket, engedélyekkel kell rendelkeznie az erőforrások biztonságos eléréséhez az előfizetésben szükséges minimális jogokkal.

Ez a cikk a Azure Automation által támogatott különböző hitelesítési forgatókönyveket ismerteti, és bemutatja, hogyan kezdheti el az első lépéseket a felügyelni kívánt környezet vagy környezetek alapján.  

## <a name="automation-account-overview"></a>Az Automation-fiókok áttekintése

Amikor először indítja el az Azure Automationt, legalább egy Automation-fiókot létre kell hoznia. Az Automation-fiókok lehetővé teszik, hogy elkülönítse az Automation erőforrásait (forgatókönyvek, adategységek, konfigurációk) a más Automation-fiókokban tárolt erőforrásoktól. Az Automation-fiókok segítségével külön logikai környezetekre választhatja szét az erőforrásokat. Használhat például egy fiókot fejlesztéshez, egy másikat az üzemi használatra, egy harmadikat pedig a helyszíni környezethez. Az Azure Automation-fiók különbözik a Microsoft-fiókjától vagy az Azure-előfizetésében létrehozott fiókoktól.

Az Azure-fiókokhoz tartozó Automation-erőforrások egy Azure-régióhoz tartoznak, de az Automation-fiókok képesek az előfizetés összes erőforrását kezelni. A különböző régiókban levő Automation-fiókok létrehozásának fő oka az lehet, ha a házirendeknek adatokra és erőforrásokra van szükségük ahhoz, hogy külön régióra különülhessenek el.

Az összes feladatot, amelyet az Azure Resource Manager és az Azure Automation parancsmagjainak használatával az erőforrásokon végrehajt, hitelesíteni kell az Azure Active Directory szervezetiidentitás-hitelesítésével. A Azure Automation futtató fiókok az Azure-parancsmagok használatával biztosítanak hitelesítést az Azure-erőforrások kezeléséhez. A futtató fiók létrehozásakor létrehoz egy új egyszerű szolgáltatásnevet a Azure Active Directory (AD) szolgáltatásban, és hozzárendeli a közreműködői szerepkört a felhasználóhoz az előfizetés szintjén. Az Azure Virtual Machines hibrid Runbook-feldolgozóit használó runbookok esetében a futtató fiókok helyett [felügyelt identitásokat](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) használhat az Azure-erőforrásokhoz való hitelesítéshez.

A futtató fiókhoz tartozó egyszerű szolgáltatásnév nem rendelkezik az Azure AD alapértelmezett olvasási engedélyeivel. Ha engedélyeket szeretne adni az Azure AD olvasásához vagy kezeléséhez, meg kell adnia az engedélyeket az egyszerű szolgáltatáshoz az **API-engedélyek**alatt. További információ: [a webes API-k eléréséhez szükséges engedélyek hozzáadása](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

A szerepköralapú hozzáférés-vezérlés az Azure Resource Managerben érhető el, hogy hozzáférést adjon az engedélyezett műveleteknek egy Azure AD-felhasználói fiókhoz és futtatófiókhoz, és hitelesítse az egyszerű szolgáltatást. Az Automation-engedélyek kezelésére használt modell fejlesztésére vonatkozó további információkért olvassa el [Az Azure Automation szerepköralapú hozzáférés-vezérlése](automation-role-based-access-control.md) című cikket.  

Az adatközpontban vagy más felhőalapú környezetekben, például az AWS-ben működő hibrid Runbook-feldolgozón futó runbookok nem használhatja ugyanazt a metódust, amelyet általában az Azure-erőforrásokhoz való runbookok-hitelesítéshez használ. Ennek oka az, hogy azok az erőforrások az Azure-on kívül futnak, és emiatt az Automation szolgáltatásban meghatározott saját biztonsági hitelesítő adataikra van szükség a helyileg elérhető erőforrásokhoz történő hitelesítéshez. A runbook-feldolgozókkal végzett runbook-hitelesítéssel kapcsolatos további információkért lásd: [Runbookok hitelesítése hibrid runbook-feldolgozók számára](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>További lépések

* [Hozzon létre egy Automation-fiókot a Azure Portal](automation-create-standalone-account.md).

* [Hozzon létre egy Automation-fiókot Azure Resource Manager sablon használatával](automation-create-account-template.md).

* [Hitelesítés Amazon Web Services (AWS)](automation-config-aws-account.md)használatával.
