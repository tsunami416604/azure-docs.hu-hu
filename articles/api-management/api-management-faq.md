---
title: Azure API Management gyIK | Microsoft dokumentumok
description: Ismerje meg a gyakran ismételt kérdésekre adott válaszokat, mintákat és gyakorlati tanácsokat az Azure API Managementben.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: c32fdc67c74e100e0e31dad3afde128c05c356d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335975"
---
# <a name="azure-api-management-faqs"></a>Az Azure API-kezelés – gyakori kérdések
Válaszok az Azure API Management gyakori kérdéseire, mintáira és bevált gyakorlataira.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="contact-us"></a>Kapcsolat
* [Hogyan tehetek fel kérdést a Microsoft Azure API Management csapatának?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Gyakori kérdések
* [Mit jelent, ha egy funkció előzetes verzióban érhető el?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Hogyan védhetem meg a kapcsolatot az API Management-átjáró és a háttérszolgáltatások között?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Hogyan másolhatom át az API Management-szolgáltatáspéldányomat új példányra?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Kezelhetem az API Management példányt programozott módon?](#can-i-manage-my-api-management-instance-programmatically)
* [Hogyan adhatok hozzá felhasználót a rendszergazdák csoportjához?](#how-do-i-add-a-user-to-the-administrators-group)
* [Miért nem érhető el a hozzáadni kívánt házirend a házirend-szerkesztőben?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Hogyan állíthatok be több környezetet egyetlen API-ban?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Használhatom a SOAP-ot az API Management szolgáltatással?](#can-i-use-soap-with-api-management)
* [Konfigurálhatok OAuth 2.0 engedélyezési kiszolgálót az AD FS biztonságával?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Milyen útválasztási módszert használ az API Management több földrajzi helyre történő telepítésben?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Létrehozhatok egy Azure Resource Manager-sablont egy API Management szolgáltatáspéldány létrehozásához?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Használhatok önaláírt TLS/SSL-tanúsítványt háttérrendszerhez?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Miért kapok hitelesítési hibát, amikor git-tárházat próbálok klónozni?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Működik az API Management az Azure ExpressRoute-tal?](#does-api-management-work-with-azure-expressroute)
* [Miért van szükség egy dedikált alhálózat erőforrás-kezelő stílusú VNETs, ha API Management telepítve van bennük?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Mi a minimális alhálózati méret szükséges az API Management virtuális hálózatba való üzembe helyezésekor?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Áthelyezhetek egy API Management szolgáltatást az egyik előfizetésből egy másikba?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Vannak korlátozások vagy ismert problémák az API importálásával kapcsolatban?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Hogyan tehetek fel kérdést a Microsoft Azure API Management csapatának?
Felveheti velünk a kapcsolatot az alábbi lehetőségek egyikével:

* Tegye fel kérdéseit [az API Management MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt)fórumunkban.
* Küldjön egy e-mailt a következő címre: <mailto:apimgmt@microsoft.com>.
* Küldjön nekünk egy funkciókérést az [Azure visszajelzési fórumában.](https://feedback.azure.com/forums/248703-api-management)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Mit jelent, ha egy funkció előzetes verzióban érhető el?
Ha egy funkció előzetes verzióban érhető el, az azt jelenti, hogy aktívan keresünk visszajelzést arról, hogyan működik a funkció. Az előzetes verzióban egy funkció funkcionálisan teljes, de lehetséges, hogy az ügyfelek visszajelzéseire reagálva megszakítjuk a változást. Azt javasoljuk, hogy ne függjön egy olyan funkciótól, amely előzetes verzióban érhető el az éles környezetben. Ha bármilyen visszajelzést előnézeti funkciók, kérjük, tudassa velünk az egyik kapcsolatfelvételi lehetőségek [Hogyan kérhetek fel a Microsoft Azure API Management csapat egy kérdést?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hogyan védhetem meg a kapcsolatot az API Management-átjáró és a háttérszolgáltatások között?
Számos lehetősége van az API Management átjáró és a háttérszolgáltatások közötti kapcsolat védelmére. A következőket teheti:

* Használja a HTTP alapfokú hitelesítést. További információt az [Első API importálása és közzététele](import-and-publish.md)című témakörben talál.
* A TLS kölcsönös hitelesítésének használata a [Háttérszolgáltatások védelme az Azure API Management ben az ügyféltanúsítvány-hitelesítés használatával](api-management-howto-mutual-certificates.md)című részében leírtak szerint.
* Használja az IP-engedélyezési listát a háttérszolgáltatásban. Az API Management minden rétegében, a felhasználási szint kivételével, az átjáró IP-címe állandó marad, néhány kikötéssel [az IP dokumentációs cikkben.](api-management-howto-ip-addresses.md)
* Csatlakoztassa az API Management-példányt egy Azure virtuális hálózathoz.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Hogyan másolhatom át az API Management-szolgáltatáspéldányomat új példányra?
Számos lehetősége van, ha egy API Management-példányt szeretne másolni egy új példányba. A következőket teheti:

* Használja a biztonsági mentési és visszaállítási funkciót az API Management ben. További információ: [Vészhelyreállítás megvalósítása szolgáltatásbiztonsági mentés és visszaállítás használatával az Azure API Management ben.](api-management-howto-disaster-recovery-backup-restore.md)
* Hozzon létre saját biztonsági mentési és visszaállítási szolgáltatást az [API Management REST API használatával.](/rest/api/apimanagement/) A REST API-val mentse és állítsa vissza az entitásokat a kívánt szolgáltatáspéldányból.
* Töltse le a szolgáltatás konfigurációját a Git használatával, majd töltse fel egy új példányba. További információ: [Az API Management szolgáltatás konfigurációjának mentése és konfigurálása a Git használatával](api-management-configuration-repository-git.md)című témakörben talál.

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Kezelhetem az API Management példányt programozott módon?
Igen, az API Management programozott módon kezelhető a következők használatával:

* Az [API Management REST API](/rest/api/apimanagement/).
* A [Microsoft Azure ApiManagement Szolgáltatásfelügyeleti könyvtár SDK](https://aka.ms/apimsdk).
* A [szolgáltatás üzembe helyezése](https://docs.microsoft.com/powershell/module/wds) és a [Szolgáltatáskezelés](https://docs.microsoft.com/powershell/azure/servicemanagement/overview) PowerShell-parancsmagok.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hogyan adhatok hozzá felhasználót a rendszergazdák csoportjához?
A következőképpen vehet fel felhasználót a Rendszergazdák csoportba:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Nyissa meg azt az erőforráscsoportot, amely rendelkezik a frissíteni kívánt API Management-példánysal.
3. Az API Management ben rendelje hozzá az **Api Management Service Közreműködői** szerepkört a felhasználóhoz.

Most az újonnan hozzáadott közreműködő használhatja az Azure [PowerShell-parancsmagokat.](https://docs.microsoft.com/powershell/azure/overview) A következőképpen jelentkezik be rendszergazdaként:

1. A `Connect-AzAccount` parancsmag segítségével jelentkezzen be.
2. Állítsa be a környezetet a szolgáltatást `Set-AzContext -SubscriptionID <subscriptionGUID>`tartalmazó előfizetéshez a használatával.
3. Egyetlen bejelentkezési URL-címet `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`kaphat a használatával.
4. Az URL-cím használatával érheti el a felügyeleti portált.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Miért nem érhető el a hozzáadni kívánt házirend a házirend-szerkesztőben?
Ha a hozzáadni kívánt házirend halványan vagy árnyékolva jelenik meg a házirend-szerkesztőben, győződjön meg arról, hogy a házirend megfelelő hatókörében van. Minden házirend-utasítás célja, hogy bizonyos hatókörökben és házirendszakaszokban használható. A házirendek házirendszakaszainak és hatóköreinek áttekintéséhez tekintse meg a szabályzat használati szakaszát az [API-kezelés házirendjeiben.](/azure/api-management/api-management-policies)

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hogyan állíthatok be több környezetet egyetlen API-ban?
Több környezet, például egy tesztkörnyezet és egy éles környezet beállításához egyetlen API-ban két lehetőség közül választhat. A következőket teheti:

* Különböző API-k at üzemeltetni ugyanazon a bérlőn.
* Ugyanazt az API-kat a különböző bérlők.

### <a name="can-i-use-soap-with-api-management"></a>Használhatom a SOAP-ot az API Management szolgáltatással?
[A SOAP áthaladási](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) támogatása már elérhető. A rendszergazdák importálhatják a SOAP-szolgáltatás WSDL-jét, és az Azure API Management soap előtér-kezelőt hoz létre. A SOAP-szolgáltatásokhoz fejlesztői portál dokumentációja, tesztkonzolja, szabályzatai és elemzései érhetők el.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Konfigurálhatok OAuth 2.0 engedélyezési kiszolgálót az AD FS biztonságával?
Az OAuth 2.0 engedélyezési kiszolgáló Active Directory összevonási szolgáltatások (AD FS) biztonságával történő konfigurálásáról az [ADFS használata az API Management ben című témakörben](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/)olvashat.

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Milyen útválasztási módszert használ az API Management több földrajzi helyre történő telepítésben?
Az API Management a [teljesítményforgalom-útválasztási módszert](../traffic-manager/traffic-manager-routing-methods.md#performance) használja a több földrajzi helyre történő telepítésekben. A bejövő forgalom a legközelebbi API-átjáróhoz van irányítva. Ha az egyik régió offline állapotba kerül, a bejövő forgalom automatikusan a legközelebbi átjáróhoz lesz irányítva. További információ az útválasztási módszerekről a [Traffic Manager útválasztási módszereiben.](../traffic-manager/traffic-manager-routing-methods.md)

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Létrehozhatok egy Azure Resource Manager-sablont egy API Management szolgáltatáspéldány létrehozásához?
Igen. Tekintse meg az [Azure API Management Service](https://aka.ms/apimtemplate) rövid útmutató sablonjait.

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Használhatok önaláírt TLS/SSL-tanúsítványt háttérrendszerhez?
Igen. Ez a PowerShellen keresztül vagy az API-ba való közvetlen elküldésútján végezhető el. Ez letiltja a tanúsítványlánc-érvényesítést, és lehetővé teszi az önaláírt vagy magántulajdonban aláírt tanúsítványok használatát az API Management és a háttérszolgáltatások közötti kommunikációsorán.

#### <a name="powershell-method"></a>Powershell-módszer ####
Használja [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) a (új háttérrendszerhez) vagy [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (meglévő háttérrendszerhez) PowerShell-parancsmagokat, és állítsa a `-SkipCertificateChainValidation` paramétert a értékre. `True`

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Közvetlen API-frissítési módszer ####
1. Hozzon létre egy [háttérentitást](/rest/api/apimanagement/) az API Management használatával.     
2. Állítsa a **skipCertificateChainValidation** tulajdonságot **true**értékre.     
3. Ha már nem szeretné engedélyezni az önaláírt tanúsítványokat, törölje a háttérentitást, vagy állítsa a **skipCertificateChainValidation** tulajdonságot **false**értékűre.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Miért jelenik meg hitelesítési hibaüzenet Git-adattár klónozásakor?
Ha a Git Credential Managert használja, vagy ha egy Git-tárházat próbál klónozni a Visual Studio használatával, előfordulhat, hogy a Windows hitelesítő adatok párbeszédpaneljével egy ismert probléma lép fel. A párbeszédpanel 127 karakterre korlátozza a jelszó hosszát, és csonkolja a Microsoft által létrehozott jelszót. Dolgozunk a jelszó lerövidítésén. Most, kérjük, használja Git Bash klónozni a Git repository.

### <a name="does-api-management-work-with-azure-expressroute"></a>Működik az API Management az Azure ExpressRoute-tal?
Igen. Az API Management együttműködik az Azure ExpressRoute-tal.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Miért van szükség egy dedikált alhálózat erőforrás-kezelő stílusú VNETs, ha API Management telepítve van bennük?
Az API Management dedikált alhálózati követelménye abból a tényből származik, hogy klasszikus (PAAS V1 réteg) telepítési modellre épül. Míg mi üzembe helyezheti a Resource Manager virtuális hálózat (V2 réteg), ennek következményei vannak. Az Azure-ban a klasszikus üzembe helyezési modell nincs szorosan párosítva az Erőforrás-kezelő modellel, ezért ha egy erőforrást hoz létre a V2-rétegben, a V1-réteg nem tud róla, és problémák merülhetnek fel, például az API Management olyan IP-címet próbál használni, amely már le van foglalva egy hálózati adapterhez ( épül V2).
Ha többet szeretne megtudni a klasszikus és erőforrás-kezelő modellek azure-beli különbségéről, olvassa el [a telepítési modellek közötti különbséget.](../azure-resource-manager/management/deployment-models.md)

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Mi a minimális alhálózati méret szükséges az API Management virtuális hálózatba való üzembe helyezésekor?
Az API Management üzembe helyezéséhez szükséges minimális alhálózati méret [/29,](../virtual-network/virtual-networks-faq.md#configuration)amely az Azure által támogatott minimális alhálózat-méret.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Áthelyezhetek egy API Management szolgáltatást az egyik előfizetésből egy másikba?
Igen. Ebből megtudhatja, hogyan, olvassa el [az Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Vannak korlátozások vagy ismert problémák az API importálásával kapcsolatban?
[Ismert problémák és korlátozások](api-management-api-import-restrictions.md) az Open API(Swagger), WSDL és WADL formátumokhoz.
