---
title: Azure Automation fiók hitelesítésének áttekintése
description: Ez a cikk az Automation-fiókok Azure Automation biztonsági és hitelesítési módszereinek áttekintését tartalmazza.
keywords: automation-biztonság, automation biztonságossá tétele; automation-hitelesítés
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: c9e3c2b15b45eb15b40782a5d0ecfe1e736fc013
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683518"
---
# <a name="azure-automation-account-authentication-overview"></a>Azure Automation fiók hitelesítésének áttekintése

Az Azure Automation lehetővé teszi a feladatok automatizálását az Azure erőforrásain, továbbá olyan felhőszolgáltatókkal, mint az Amazon webszolgáltatások (AWS). Ahhoz, hogy egy runbook végrehajtsa a szükséges műveleteket, rendelkeznie kell engedéllyel az erőforrásokhoz való biztonságos hozzáféréshez az előfizetésen belül szükséges minimális jogosultságokkal.

Amikor először indítja el az Azure Automationt, legalább egy Automation-fiókot létre kell hoznia. Az Automation-fiókok lehetővé teszik, hogy elkülönítse az Automation erőforrásait (forgatókönyvek, adategységek, konfigurációk) a más Automation-fiókokban tárolt erőforrásoktól. Az Automation-fiókok segítségével külön logikai környezetekre választhatja szét az erőforrásokat. Használhat például egy fiókot fejlesztéshez, egy másikat az üzemi használatra, egy harmadikat pedig a helyszíni környezethez. Az Azure Automation-fiók különbözik a Microsoft-fiókjától vagy az Azure-előfizetésében létrehozott fiókoktól.

Az Azure-fiókokhoz tartozó Automation-erőforrások egy Azure-régióhoz tartoznak, de az Automation-fiókok képesek az előfizetés összes erőforrását kezelni. A különböző régiókban levő Automation-fiókok létrehozásának fő oka az lehet, ha a házirendeknek adatokra és erőforrásokra van szükségük ahhoz, hogy külön régióra különülhessenek el.

Az erőforrásokon Azure Resource Manager és az Azure Automation Azure-parancsmagok használatával végrehajtott összes feladatnak az Azure-ban Azure Active Directory (Azure AD) szervezeti hitelesítő adatokon alapuló hitelesítéssel kell hitelesítenie az Azure-ban. A Azure Automation futtató fiókok az Azure-parancsmagok használatával biztosítanak hitelesítést az Azure-erőforrások kezeléséhez. Amikor létrehoz egy futtató fiókot, létrehoz egy új egyszerű szolgáltatást az Azure AD-ben, és hozzárendeli a közreműködő szerepkört a felhasználóhoz az előfizetés szintjén. Az Azure Virtual Machines szolgáltatásban hibrid Runbook-feldolgozókat használó runbookok esetében az Azure-erőforrások hitelesítéséhez a futtató fiókok helyett [felügyelt identitásokkal rendelkező Runbook-hitelesítést](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) használhat.

A futtató fiókhoz tartozó egyszerű szolgáltatásnév nem rendelkezik az Azure AD alapértelmezett olvasási engedélyeivel. Ha engedélyeket szeretne adni az Azure AD olvasásához vagy kezeléséhez, meg kell adnia az engedélyeket az egyszerű szolgáltatáshoz az **API-engedélyek**alatt. További információ: [a webes API-k eléréséhez szükséges engedélyek hozzáadása](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

A szerepköralapú hozzáférés-vezérlés az Azure Resource Managerben érhető el, hogy hozzáférést adjon az engedélyezett műveleteknek egy Azure AD-felhasználói fiókhoz és futtatófiókhoz, és hitelesítse az egyszerű szolgáltatást. Az Automation-engedélyek kezelésére használt modell fejlesztésére vonatkozó további információkért olvassa el [Az Azure Automation szerepköralapú hozzáférés-vezérlése](automation-role-based-access-control.md) című cikket.  

Az adatközpontban vagy más felhőalapú környezetekben, például az AWS-ben működő hibrid Runbook-feldolgozón futó runbookok nem használhatja ugyanazt a metódust, amelyet általában az Azure-erőforrásokhoz való runbookok-hitelesítéshez használ. Ennek oka az, hogy azok az erőforrások az Azure-on kívül futnak, és emiatt az Automation szolgáltatásban meghatározott saját biztonsági hitelesítő adataikra van szükség a helyileg elérhető erőforrásokhoz történő hitelesítéshez. A runbook-feldolgozókkal végzett runbook-hitelesítéssel kapcsolatos további információkért lásd: [Runbookok hitelesítése hibrid runbook-feldolgozók számára](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>További lépések

* [Hozzon létre egy Automation-fiókot a Azure Portal](automation-create-standalone-account.md).
* [Hozzon létre egy Automation-fiókot Azure Resource Manager sablon használatával](automation-create-account-template.md).
* [Hitelesítés Amazon Web Services (AWS)](automation-config-aws-account.md)használatával.
