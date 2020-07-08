---
title: Azure Automation fiók hitelesítésének áttekintése
description: Ez a cikk áttekintést nyújt Azure Automation fiók hitelesítéséről.
keywords: automation-biztonság, automation biztonságossá tétele; automation-hitelesítés
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: d2b9f705c73e667f34e46fdeed3c80af1e65fb12
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83830106"
---
# <a name="automation-account-authentication-overview"></a>Az Automation-fiókok hitelesítésének áttekintése

Az Azure Automation lehetővé teszi a feladatok automatizálását az Azure erőforrásain, továbbá olyan felhőszolgáltatókkal, mint az Amazon webszolgáltatások (AWS). A runbookok segítségével automatizálhatja a feladatokat, vagy egy hibrid Runbook-feldolgozót, ha nem Azure-beli feladatokat szeretne kezelni. A környezetnek engedélyre van szüksége az erőforrásokhoz való biztonságos hozzáféréshez az Azure-előfizetéshez szükséges minimális jogosultságokkal.

Ez a cikk a Azure Automation által támogatott hitelesítési forgatókönyveket ismerteti, és elmagyarázza, hogyan kezdheti el az első lépéseket a felügyelni kívánt környezet vagy környezetek alapján.

## <a name="automation-account"></a>Automation-fiók 

Amikor először indítja el az Azure Automationt, legalább egy Automation-fiókot létre kell hoznia. Az Automation-fiókok lehetővé teszik, hogy elkülönítse az Automation-erőforrásokat, a runbookok, az eszközöket, a konfigurációkat más fiókok erőforrásaiból. Az Automation-fiókok segítségével külön logikai környezetekre választhatja szét az erőforrásokat. Használhat például egy fiókot fejlesztéshez, egy másikat az üzemi használatra, egy harmadikat pedig a helyszíni környezethez. Az Azure Automation-fiók különbözik a Microsoft-fiókjától vagy az Azure-előfizetésében létrehozott fiókoktól. Az Automation-fiók létrehozásának bevezetését az [Automation-fiók létrehozása](automation-quickstart-create-account.md)című témakörben tekintheti meg.

## <a name="automation-resources"></a>Automation-erőforrások

Az egyes Automation-fiókok automatizálási erőforrásai egyetlen Azure-régióhoz vannak társítva, de a fiók az Azure-előfizetésében lévő összes erőforrást képes kezelni. Az Automation-fiókok különböző régiókban való létrehozásának fő oka az, ha olyan házirendekkel rendelkezik, amelyek megkövetelik, hogy az egyes régiókban az adatforrások és az erőforrások el legyenek különítve.

A Azure Resource Manager és a Azure Automation PowerShell-parancsmagjai segítségével létrehozott összes feladatnak az Azure-ban Azure Active Directory (Azure AD) szervezeti identitású hitelesítő adatokon alapuló hitelesítéssel kell hitelesítenie az Azure-ban. 

## <a name="run-as-account"></a>Futtató fiók

A Azure Automation futtató fiókok az Azure-erőforrások PowerShell-parancsmagokkal történő kezeléséhez biztosítanak hitelesítést. Amikor létrehoz egy futtató fiókot, létrehoz egy új egyszerű szolgáltatást az Azure AD-ben, és hozzárendeli a közreműködő szerepkört a felhasználóhoz az előfizetés szintjén. Az Azure-beli virtuális gépeken hibrid Runbook-feldolgozókat használó runbookok esetében az Azure-erőforrások hitelesítéséhez a futtató fiókok helyett [felügyelt identitásokkal rendelkező Runbook-hitelesítést](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) használhat.

## <a name="service-principal-for-run-as-account"></a>Egyszerű szolgáltatásnév a futtató fiókhoz

A futtató fiókhoz tartozó egyszerű szolgáltatásnév nem rendelkezik az Azure AD alapértelmezett olvasási engedélyeivel. Ha engedélyeket szeretne adni az Azure AD olvasásához vagy kezeléséhez, meg kell adnia az engedélyeket az egyszerű szolgáltatáshoz az **API-engedélyek**alatt. További információ: [a webes API-k eléréséhez szükséges engedélyek hozzáadása](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A szerepköralapú hozzáférés-vezérlés Azure Resource Manager segítségével engedélyezheti az Azure AD felhasználói fiókjának és futtató fiókjának engedélyezett műveleteit, valamint hitelesítheti az egyszerű szolgáltatást. Az Automation-engedélyek kezelésére használt modell fejlesztésére vonatkozó további információkért olvassa el [Az Azure Automation szerepköralapú hozzáférés-vezérlése](automation-role-based-access-control.md) című cikket.  

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbook-hitelesítés hibrid Runbook-feldolgozóval 

Az adatközpontban vagy más felhőalapú környezetekben, például az AWS-ben működő hibrid Runbook-feldolgozón futó runbookok nem használhatja ugyanazt a módszert, amelyet általában az Azure-erőforrásokhoz történő runbookok-hitelesítéshez használ. Ennek oka az, hogy azok az erőforrások az Azure-on kívül futnak, és emiatt az Automation szolgáltatásban meghatározott saját biztonsági hitelesítő adataikra van szükség a helyileg elérhető erőforrásokhoz történő hitelesítéshez. A runbook-feldolgozókkal való runbook-hitelesítéssel kapcsolatos további információkért lásd: [Runbookok futtatása hibrid runbook-feldolgozón](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>További lépések

* Ha Automation-fiókot szeretne létrehozni a Azure Portalből, tekintse meg az [önálló Azure Automation fiók létrehozása](automation-create-standalone-account.md)című témakört.
* Ha sablon használatával szeretné létrehozni a fiókját, tekintse meg [az Automation-fiók létrehozása Azure Resource Manager sablonnal](automation-create-account-template.md)című témakört.
* Amazon Web Services használatával történő hitelesítéssel kapcsolatban lásd: [Runbookok hitelesítése Amazon Web Servicessal](automation-config-aws-account.md).