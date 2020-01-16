---
title: Azure API Management GYIK | Microsoft Docs
description: Ismerje meg az Azure API Management gyakori kérdéseire (GYIK), mintákra és ajánlott eljárásokra adott válaszokat.
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
ms.openlocfilehash: 21b46ba0012b71ed0e09dc09d041ceb020824843
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75967457"
---
# <a name="azure-api-management-faqs"></a>Azure API Management – gyakori kérdések
Választ kaphat az Azure API Management gyakori kérdéseire, mintáinak és ajánlott eljárásaira.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="contact-us"></a>Kapcsolatfelvétel
* [Hogyan kérhetem le a Microsoft Azure API Management a csapatot?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Gyakori kérdések
* [Mit jelent, ha egy funkció előzetes verzióban van?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Hogyan védhetem meg a kapcsolatot az API Management-átjáró és a háttérszolgáltatások között?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Hogyan másolhatom át az API Management-szolgáltatáspéldányomat új példányra?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Kezelhetem a API Management-példányt programozott módon?](#can-i-manage-my-api-management-instance-programmatically)
* [Hogyan adhatok hozzá felhasználót a rendszergazdák csoportjához?](#how-do-i-add-a-user-to-the-administrators-group)
* [Miért nem érhető el a szabályzat a házirend-szerkesztőben?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Hogyan több környezetet is beállíthat egyetlen API-ban?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Használhatom a SOAP-t a API Management?](#can-i-use-soap-with-api-management)
* [Konfigurálható a OAuth 2,0-es engedélyezési kiszolgáló AD FS biztonsággal?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Milyen útválasztási módszert használ a központi telepítések API Management több földrajzi helyhez?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Használhatok Azure Resource Manager sablont egy API Management Service-példány létrehozásához?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Használhatok önaláírt SSL-tanúsítványt a háttérrendszerhez?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Miért kapok hitelesítési hibát a GIT-tárház klónozásakor?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Működik API Management az Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Miért van szükség egy dedikált alhálózatra a Resource Manager-stílusú virtuális hálózatok, amikor a API Management üzembe helyezése folyamatban van?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Milyen minimális alhálózati méretre van szükség a API Management VNET való telepítésekor?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Áthelyezhetek egy API Management szolgáltatást az egyik előfizetésből egy másikba?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Van korlátozás vagy ismert probléma az API-k importálásakor?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Hogyan kérhetem le a Microsoft Azure API Management a csapatot?
A következő lehetőségek egyikének használatával felveheti velünk a kapcsolatot:

* Tegye fel kérdéseit a [API Management MSDN-fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Küldjön egy e-mailt a következő címre: <mailto:apimgmt@microsoft.com>.
* Küldjön nekünk egy szolgáltatási kérelmet az [Azure feedback fórumában](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Mit jelent, ha egy funkció előzetes verzióban van?
Ha egy funkció előzetes verzióban érhető el, az azt jelenti, hogy aktívan keresünk visszajelzést arról, hogy a szolgáltatás hogyan működik. Az előzetes verzióban egy funkció működik, de lehetséges, hogy a vásárlói visszajelzésekre adott válaszként feltörhető változást teszünk elérhetővé. Azt javasoljuk, hogy ne függjön az éles környezetben előzetes verzióban elérhető szolgáltatástól. Ha visszajelzést szeretne küldeni az előzetes verziójú szolgáltatásokról, tudassa velünk az egyik kapcsolatfelvételi lehetőséggel, [hogy hogyan kérhetem a Microsoft Azure API Management a csapatot? című témakört](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hogyan biztosíthatom a kapcsolatot a API Management átjáró és a saját háttérbeli szolgáltatások között?
Több lehetőség áll rendelkezésére a kapcsolat biztonságossá tételéhez a API Management-átjáró és a háttér-szolgáltatások között. Előnyök:

* HTTP alapszintű hitelesítés használata. További információ: [az első API importálása és közzététele](import-and-publish.md).
* SSL kölcsönös hitelesítés használata a következő témakörben ismertetett módon: a [háttér-szolgáltatások biztonságossá tétele az ügyféltanúsítvány-alapú hitelesítés használatával az Azure API Managementban](api-management-howto-mutual-certificates.md).
* Használja az IP-engedélyezési szolgáltatást a háttérben futó szolgáltatásban. Az API Management minden olyan szintjében, amely a felhasználási szintet is tartalmazza, az átjáró IP-címe állandó marad, és néhány, [az IP-dokumentációban](api-management-howto-ip-addresses.md)leírt figyelmeztetéssel rendelkezik.
* API Management példányának összekapcsolásához Azure-Virtual Network.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Hogyan másolja a API Management Service-példányt egy új példányra?
Több lehetőség is rendelkezésre áll, ha API Management példányt szeretne másolni egy új példányra. Előnyök:

* Használja a API Management biztonsági mentési és visszaállítási függvényt. További információ: a vész- [helyreállítás megvalósítása a szolgáltatás biztonsági mentésével és visszaállításával az Azure API Managementban](api-management-howto-disaster-recovery-backup-restore.md).
* Hozza létre saját biztonsági mentési és visszaállítási funkcióját a [API Management REST API](/rest/api/apimanagement/)használatával. A REST API használatával mentse és állítsa vissza az entitásokat a kívánt szolgáltatás-példányból.
* Töltse le a szolgáltatás konfigurációját a git használatával, majd töltse fel egy új példányba. További információ: [a API Management szolgáltatás konfigurációjának mentése és konfigurálása a git használatával](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Módosíthatom programozottan az API Management-példányomat?
Igen, API Management programozott módon felügyelheti a használatával:

* A [API Management REST API](/rest/api/apimanagement/).
* A [Microsoft Azure ApiManagement Service Management Library SDK](https://aka.ms/apimsdk).
* A [szolgáltatás központi telepítésével](https://docs.microsoft.com/powershell/module/wds) és a [Service Management](https://docs.microsoft.com/powershell/azure/servicemanagement/overview) PowerShell-parancsmagokkal.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hogyan adjon hozzá egy felhasználót a rendszergazdák csoporthoz?
A következőképpen adhat hozzá felhasználókat a rendszergazdák csoporthoz:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Lépjen a frissíteni kívánt API Management-példánnyal rendelkező erőforráscsoporthoz.
3. A API Management-ben rendelje hozzá az **API Management szolgáltatás közreműködői** szerepkört a felhasználóhoz.

Az újonnan hozzáadott közreműködő mostantól Azure PowerShell [parancsmagokat](https://docs.microsoft.com/powershell/azure/overview)is használhat. A következőképpen jelentkezhet be rendszergazdaként:

1. A bejelentkezéshez használja a `Connect-AzAccount` parancsmagot.
2. Állítsa a kontextust arra az előfizetésre, amely a szolgáltatással rendelkezik `Set-AzContext -SubscriptionID <subscriptionGUID>`használatával.
3. Egyszeri bejelentkezési URL-cím beszerzése `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`használatával.
4. A felügyeleti portál eléréséhez használja az URL-címet.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Miért nem érhető el a szabályzat a házirend-szerkesztőben?
Ha a hozzáadni kívánt szabályzat szürkén vagy árnyékolva jelenik meg a házirend-szerkesztőben, ügyeljen arra, hogy a szabályzat megfelelő hatóköre legyen. Minden házirend-utasítás meghatározott hatókörökben és házirend-szakaszban való használatra készült. A szabályzatok szabályzat-szakaszainak és hatókörének áttekintéséhez tekintse meg a házirend használatáról szóló szakaszt [API Management házirendek](/azure/api-management/api-management-policies)részben.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hogyan több környezetet is beállíthat egyetlen API-ban?
Több környezet (például egy tesztkörnyezet és egy éles környezet) egyetlen API-ban való beállításához két lehetőség közül választhat. Előnyök:

* Különböző API-k üzemeltetése ugyanazon a bérlőn.
* Ugyanazon API-k üzemeltetése különböző bérlők esetében.

### <a name="can-i-use-soap-with-api-management"></a>Használhatok SOAP protokollt az API Managementtel?
A [SOAP pass-through](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) támogatás mostantól elérhető. A rendszergazdák importálhatók a SOAP-szolgáltatáshoz tartozó WSDL-t, az Azure API Management pedig létrehoz egy SOAP-előtérben. A fejlesztői portál dokumentációja, a tesztelési konzol, a szabályzatok és az elemzések mind elérhetők a SOAP-szolgáltatásokhoz.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Konfigurálható a OAuth 2,0-es engedélyezési kiszolgáló AD FS biztonsággal?
Az OAuth 2,0 engedélyezési kiszolgáló Active Directory összevonási szolgáltatások (AD FS) (AD FS) biztonsággal való konfigurálásával kapcsolatos információkért lásd: az [ADFS használata a API Management-ben](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Milyen útválasztási módszert használ a központi telepítések API Management több földrajzi helyhez?
API Management a központi telepítések [teljesítmény-forgalmi útválasztási módszerét](../traffic-manager/traffic-manager-routing-methods.md#performance) használja több földrajzi helyhez. A bejövő forgalom a legközelebbi API-átjáróhoz lesz irányítva. Ha az egyik régió offline állapotba kerül, a rendszer automatikusan átirányítja a bejövő forgalmat a következő legközelebbi átjáróhoz. További információ az útválasztási módszerekről [Traffic Manager útválasztási módszerekben](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Használhatok Azure Resource Manager sablont egy API Management Service-példány létrehozásához?
Igen. Tekintse meg az [Azure API Management Service](https://aka.ms/apimtemplate) Gyorsindítás sablonjait.

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Használhatok önaláírt SSL-tanúsítványt a háttérrendszer számára?
Igen. Ez a PowerShell használatával vagy az API-hoz való közvetlen beküldéssel végezhető el. Ezzel a beállítással letilthatja a tanúsítványlánc érvényesítését, és lehetővé teszi önaláírt vagy önaláírt tanúsítványok használatát a API Managementról a háttér-szolgáltatásokhoz való kommunikáció során.

#### <a name="powershell-method"></a>PowerShell-módszer ####
Használja a [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) (az új háttérrendszer esetében) vagy [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (a meglévő háttérrendszer esetében) PowerShell-parancsmagokat, és állítsa a `-SkipCertificateChainValidation` paramétert `True`értékre.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Közvetlen API-frissítési módszer ####
1. Hozzon létre egy [háttérbeli](/rest/api/apimanagement/) entitást API Management használatával.     
2. Állítsa a **skipCertificateChainValidation** tulajdonságot **true (igaz**) értékre.     
3. Ha már nem szeretné engedélyezni az önaláírt tanúsítványokat, törölje a háttérbeli entitást, vagy állítsa a **skipCertificateChainValidation** tulajdonságot **hamis**értékre.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Miért kapok hitelesítési hibát a git-tárház klónozásakor?
Ha a git hitelesítőadat-kezelőt használja, vagy ha a Visual Studióval próbál meg klónozási tárházat használni, előfordulhat, hogy a Windows hitelesítő adatok párbeszédpanel ismert problémába ütközhet. A párbeszédpanel a jelszó hosszát 127 karakterre korlátozza, és levágja a Microsoft által generált jelszót. Dolgozunk a jelszó lerövidítésén. A git-tárház klónozásához használja a git Basht.

### <a name="does-api-management-work-with-azure-expressroute"></a>Működik API Management az Azure ExpressRoute?
Igen. A API Management az Azure ExpressRoute működik.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Miért van szükség egy dedikált alhálózatra a Resource Manager-stílusú virtuális hálózatok, amikor a API Management üzembe helyezése folyamatban van?
A API Management dedikált alhálózat-követelménye a tény, hogy klasszikus (Pásti v1-es) üzembe helyezési modellre épül. Noha üzembe helyezhető egy Resource Manager-VNET (v2-réteg), ennek következményei vannak. Az Azure-beli klasszikus üzemi modell nincs szorosan összekapcsolva a Resource Manager-modellel, így ha a v2-rétegben hoz létre egy erőforrást, akkor a v1-réteg nem tudja, milyen problémák léphetnek fel, például API Management olyan IP-címet próbálnak használni, amely már le van foglalva egy hálózati adapterhez.  (a v2-re épül).
Ha többet szeretne megtudni a klasszikus és a Resource Manager-modellekről az Azure-ban, tekintse át az [üzembe helyezési modellek különbségét](../azure-resource-manager/management/deployment-models.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Milyen minimális alhálózati méretre van szükség a API Management VNET való telepítésekor?
Az API Management a [/29](../virtual-network/virtual-networks-faq.md#configuration)telepítéséhez szükséges minimális alhálózati méret, amely az Azure által támogatott minimális alhálózat-méret.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Áthelyezhetek egy API Management szolgáltatást egyik előfizetésből a másikba?
Igen. További információ: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Van korlátozás vagy ismert probléma az API-k importálásakor?
[Ismert problémák és korlátozások](api-management-api-import-restrictions.md) az Open API (hencegő), a WSDL és a WADL formátumokhoz.
