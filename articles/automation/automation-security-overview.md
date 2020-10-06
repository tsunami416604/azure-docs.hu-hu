---
title: Azure Automation fiók hitelesítésének áttekintése
description: Ez a cikk áttekintést nyújt Azure Automation fiók hitelesítéséről.
keywords: automation-biztonság, automation biztonságossá tétele; automation-hitelesítés
services: automation
ms.subservice: process-automation
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: bcb5f61c93bd4c3ff7c0f81ae808807f7deb71df
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766085"
---
# <a name="automation-account-authentication-overview"></a>Az Automation-fiókok hitelesítésének áttekintése

Az Azure Automation lehetővé teszi a feladatok automatizálását az Azure erőforrásain, továbbá olyan felhőszolgáltatókkal, mint az Amazon webszolgáltatások (AWS). A runbookok segítségével automatizálhatja a feladatokat, vagy egy hibrid Runbook-feldolgozót, ha üzleti vagy működési folyamatai vannak az Azure-on kívüli felügyeletre. Ezeknek a környezeteknek a használata megköveteli az erőforrások biztonságos elérését a minimálisan szükséges jogokkal.

Ez a cikk a Azure Automation által támogatott hitelesítési forgatókönyveket ismerteti, és elmagyarázza, hogyan kezdheti el az első lépéseket a felügyelni kívánt környezet vagy környezetek alapján.

## <a name="automation-account"></a>Automation-fiók

Amikor először indítja el az Azure Automationt, legalább egy Automation-fiókot létre kell hoznia. Az Automation-fiókok lehetővé teszik, hogy elkülönítse az Automation-erőforrásokat, a runbookok, az eszközöket, a konfigurációkat más fiókok erőforrásaiból. Az Automation-fiókok segítségével külön logikai környezetekre választhatja szét az erőforrásokat. Használhat például egy fiókot fejlesztéshez, egy másikat az üzemi használatra, egy harmadikat pedig a helyszíni környezethez. Az Azure Automation-fiók különbözik a Microsoft-fiókjától vagy az Azure-előfizetésében létrehozott fiókoktól. Az Automation-fiók létrehozásának bevezetését az [Automation-fiók létrehozása](automation-quickstart-create-account.md)című témakörben tekintheti meg.

## <a name="automation-resources"></a>Automation-erőforrások

Az egyes Automation-fiókok automatizálási erőforrásai egyetlen Azure-régióhoz vannak társítva, de a fiók az Azure-előfizetésében lévő összes erőforrást képes kezelni. Az Automation-fiókok különböző régiókban való létrehozásának fő oka az, ha olyan házirendekkel rendelkezik, amelyek megkövetelik, hogy az egyes régiókban az adatforrások és az erőforrások el legyenek különítve.

A Azure Resource Manager és a Azure Automation PowerShell-parancsmagjai segítségével létrehozott összes feladatnak az Azure-ban Azure Active Directory (Azure AD) szervezeti identitású hitelesítő adatokon alapuló hitelesítéssel kell hitelesítenie az Azure-ban.

## <a name="run-as-accounts"></a>Futtató fiókok

A Azure Automation futtató fiókok biztosítják a klasszikus üzemi modellben üzembe helyezett Azure Resource Manager erőforrások vagy erőforrások felügyeletének hitelesítését. A Azure Automationben kétféle futtató fiók létezik:

* Azure-beli futtató fiók
* Klasszikus Azure-beli futtató fiók

Ha többet szeretne megtudni a két üzembe helyezési modellről, tekintse meg a [Resource Manager és a klasszikus telepítés](../azure-resource-manager/management/deployment-models.md)című témakört.

>[!NOTE]
>A Azure Cloud Solution Provider (CSP) előfizetések csak a Azure Resource Manager modellt támogatják. A nem Azure Resource Manager szolgáltatások nem érhetők el a programban. Ha CSP-előfizetést használ, a klasszikus Azure-beli futtató fiók nem jön létre, de létrejön az Azure-beli futtató fiók. A CSP-előfizetésekkel kapcsolatos további tudnivalókért tekintse meg a [CSP-előfizetésekben elérhető szolgáltatások](/azure/cloud-solution-provider/overview/azure-csp-available-services)című témakört.

### <a name="run-as-account"></a>Futtató fiók

Az Azure-beli futtató fiók az Azure Azure Resource Manager üzembe helyezési és felügyeleti szolgáltatása alapján kezeli az Azure-erőforrásokat.

A futtató fiók létrehozásakor a következő feladatokat hajtja végre:

* Létrehoz egy önaláírt tanúsítvánnyal rendelkező Azure AD-alkalmazást, létrehoz egy egyszerű szolgáltatásfiókot az alkalmazáshoz az Azure AD-ben, és hozzárendeli a [közreműködői](../role-based-access-control/built-in-roles.md#contributor) szerepkört a fiókhoz a jelenlegi előfizetésében. A tanúsítvány beállítását a tulajdonosra vagy bármely más szerepkörre módosíthatja. További információk: [Szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md).

* Létrehoz egy nevű Automation-tanúsítványt `AzureRunAsCertificate` a megadott Automation-fiókban. A tanúsítvány objektuma tartalmazza az Azure AD-alkalmazás által használt tanúsítvány titkos kulcsát.

* Létrehoz egy nevű Automation-összekötő eszközt `AzureRunAsConnection` a megadott Automation-fiókban. A szolgáltatás tartalmazza az alkalmazás AZONOSÍTÓját, a bérlő AZONOSÍTÓját, az előfizetés AZONOSÍTÓját és a tanúsítvány ujjlenyomatát.

### <a name="azure-classic-run-as-account"></a>Klasszikus Azure-futtatófiók

A klasszikus Azure-beli futtató fiók a klasszikus üzembe helyezési modell alapján kezeli a klasszikus Azure-erőforrásokat. Ilyen típusú futtató fiók létrehozásához vagy megújításához az előfizetés egyik társ-rendszergazda tagjának kell lennie.

Klasszikus Azure-beli futtató fiók létrehozásakor a következő feladatokat hajtja végre.

* Létrehoz egy felügyeleti tanúsítványt az előfizetésben.

* Létrehoz egy nevű Automation-tanúsítványt `AzureClassicRunAsCertificate` a megadott Automation-fiókban. Ez a tanúsítványobjektum tartalmazza a felügyeleti tanúsítvány által használt titkos tanúsítványkulcsot.

* Létrehoz egy nevű Automation-összekötő eszközt `AzureClassicRunAsConnection` a megadott Automation-fiókban. A szolgáltatás tartalmazza az előfizetés nevét, az előfizetés AZONOSÍTÓját és a tanúsítvány-eszköz nevét.

>[!NOTE]
>A klasszikus Azure-beli futtató fiók alapértelmezés szerint nem jön létre Automation-fiók létrehozásakor. Ezt a fiókot külön hozza létre a rendszer a [futtató fiók kezelése](manage-runas-account.md#create-a-run-as-account-in-azure-portal) cikkben leírt lépéseket követve.

## <a name="service-principal-for-run-as-account"></a>Egyszerű szolgáltatásnév a futtató fiókhoz

A futtató fiókhoz tartozó egyszerű szolgáltatásnév nem rendelkezik az Azure AD alapértelmezett olvasási engedélyeivel. Ha engedélyeket szeretne adni az Azure AD olvasásához vagy kezeléséhez, meg kell adnia az engedélyeket az egyszerű szolgáltatáshoz az **API-engedélyek**alatt. További információért lásd: [engedélyek hozzáadása a webes API-hoz való hozzáféréshez](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A szerepköralapú hozzáférés-vezérlés Azure Resource Manager segítségével engedélyezheti az Azure AD felhasználói fiókjának és futtató fiókjának engedélyezett műveleteit, valamint hitelesítheti az egyszerű szolgáltatást. Az Automation-engedélyek kezelésére használt modell fejlesztésére vonatkozó további információkért olvassa el [Az Azure Automation szerepköralapú hozzáférés-vezérlése](automation-role-based-access-control.md) című cikket.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbook-hitelesítés hibrid Runbook-feldolgozóval

Az adatközpontban vagy más felhőalapú környezetekben, például az AWS-ben működő hibrid Runbook-feldolgozón futó runbookok nem használhatja ugyanazt a módszert, amelyet általában az Azure-erőforrásokhoz történő runbookok-hitelesítéshez használ. Ennek oka az, hogy azok az erőforrások az Azure-on kívül futnak, és emiatt az Automation szolgáltatásban meghatározott saját biztonsági hitelesítő adataikra van szükség a helyileg elérhető erőforrásokhoz történő hitelesítéshez. A runbook-feldolgozókkal való runbook-hitelesítéssel kapcsolatos további információkért lásd: [Runbookok futtatása hibrid runbook-feldolgozón](automation-hrw-run-runbooks.md).

Az Azure-beli virtuális gépeken hibrid Runbook-feldolgozókat használó runbookok esetében az Azure-erőforrások hitelesítéséhez a futtató fiókok helyett [felügyelt identitásokkal rendelkező Runbook-hitelesítést](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) használhat.

## <a name="next-steps"></a>Következő lépések

* Ha Automation-fiókot szeretne létrehozni a Azure Portalből, tekintse meg az [önálló Azure Automation fiók létrehozása](automation-create-standalone-account.md)című témakört.
* Ha sablon használatával szeretné létrehozni a fiókját, tekintse meg [az Automation-fiók létrehozása Azure Resource Manager sablonnal](quickstart-create-automation-account-template.md)című témakört.
* Amazon Web Services használatával történő hitelesítéssel kapcsolatban lásd: [Runbookok hitelesítése Amazon Web Servicessal](automation-config-aws-account.md).