---
title: Az Azure API Management – gyakori kérdések |} A Microsoft Docs
description: Ismerje meg a válaszok a gyakori kérdések (GYIK), a mintákat, és gyakorlati tanácsok az Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: e80ebcd6de7a793450a0503c99af151e96658ea9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876742"
---
# <a name="azure-api-management-faqs"></a>Az Azure API Management – gyakori kérdések
Válaszok a gyakori kérdésekre, minták és ajánlott eljárások az Azure API Management.

## <a name="contact-us"></a>Kapcsolat
* [Hogyan tehetek fel a Microsoft Azure API Management csapata egy kérdést?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Gyakori kérdések
* [Mit jelent az, ha egy funkció előzetes verzióban érhető el?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Hogyan védhetem meg a kapcsolatot az API Management-átjáró és a háttérszolgáltatások között?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Hogyan másolja az API Management-szolgáltatáspéldány egy új példányt?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Programozott módon is kezelhető az API Management-példány?](#can-i-manage-my-api-management-instance-programmatically)
* [Hogyan adhatok hozzá a felhasználó a rendszergazdák csoporthoz?](#how-do-i-add-a-user-to-the-administrators-group)
* [Miért van a házirend, amelyet szeretnék hozzáadni a Helyicsoportházirend-szerkesztő nem érhető el?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Hogyan állíthatok be egy egyetlen API több környezetet?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Használhatom-e a SOAP API Management?](#can-i-use-soap-with-api-management)
* [Az API Management gateway IP cím állandó? Is használhatók a tűzfalszabályok?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* Konfigurálhatok az OAuth 2.0 engedélyezési kiszolgáló az AD FS biztonsági?
* [Milyen útválasztási módszert használni a az API Management a több földrajzi helyen történő központi telepítések?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Az Azure Resource Manager-sablonok segítségével az API Management szolgáltatáspéldány létrehozása?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Használható a háttéralkalmazás az önaláírt SSL-tanúsítvány?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Miért kapok hitelesítési hiba jelenik meg egy GIT-tárház klónozása?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Az API Management működik az Azure ExpressRoute használatával?](#does-api-management-work-with-azure-expressroute)
* [Miért azt igénylik kijelölt alhálózatot a virtuális hálózatok Resource Manager-stílus be központi telepítésekor az API Management?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Mi az API Management egy vnetbe telepítésekor szükséges minimális alhálózat méretét?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Áthelyezhető egy API Management-szolgáltatás egy előfizetésből egy másikba?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [A korlátozásokat és saját API importálása ismert problémái vannak-e?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Hogyan tehetek fel a Microsoft Azure API Management csapata egy kérdést?
Kapcsolatfelvétel az alábbi lehetőségek egyikét használja:

* Felteheti a kérdéseit a [API Management az MSDN-fórumokra](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Küldjön egy e-mailt a következő címre: <mailto:apimgmt@microsoft.com>.
* Küldjön nekünk szolgáltatással kapcsolatos kéréseit az [Azure visszajelzési fórumára](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Mit jelent az, ha egy funkció előzetes verzióban érhető el?
Ha egy funkció előzetes verzióban érhető el, az azt jelenti, hogy a Microsoft aktívan kérdése megválaszolásában visszajelzésnek, hogyan működik a funkció az Ön számára. A funkció előzetes verzióban érhető el funkcionálisan befejeződött, de lehetséges, hogy az ügyfelek visszajelzései alapján módosíthatja a kompatibilitástörő elkészítjük. Azt javasoljuk, hogy Ön nem függnek egy szolgáltatás, amely az éles környezetben előzetes verzióban érhető el. Ha visszajelzést szeretne az előzetes verziójú funkciók, vegye fel velünk a kapcsolattartási beállítások egyikével [Hogyan tehetek fel a Microsoft Azure API Management csapata egy kérdést?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hogyan lehet biztonságossá tenni a kapcsolatot az API Management-átjáró és a háttér-szolgáltatások között?
Az API Management-átjáró és a háttér-szolgáltatások közötti kapcsolat biztonságossá tételéhez számos lehetősége van. A következőket teheti:

* Egyszerű HTTP-hitelesítés használata. További információkért lásd: [importálása és közzététele az első API](import-and-publish.md).
* Ismertetett módon kihasználhassák a kölcsönös hitelesítés SSL [háttérszolgáltatások ügyfél használatával biztonságossá tétele a Tanúsítványalapú hitelesítés az Azure API Management](api-management-howto-mutual-certificates.md).
* IP-címei engedélyezési listára helyezhetők a háttér-szolgáltatáshoz felhasználhat. Minden szinten az API Management, az átjáró IP-címét állandó marad, néhány [figyelmeztetések](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules). Beállíthatja, hogy az IP-címet az engedélyezési listán. Az API Management-példány IP-címét is kap az Azure portal irányítópultján.
* Az API Management-példány csatlakozhat egy Azure virtuális hálózaton.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Hogyan másolja az API Management-szolgáltatáspéldány egy új példányt?
Ha szeretné másolni az API Management-példány új példányára számos lehetősége van. A következőket teheti:

* A biztonsági másolat, és állítsa vissza a függvény az API Management szolgáltatásban. További információkért lásd: [vész-helyreállítási szolgáltatás biztonsági mentése segítségével megvalósítható, és az Azure API Management visszaállítása](api-management-howto-disaster-recovery-backup-restore.md).
* Saját biztonsági mentést, és állítsa vissza a szolgáltatás használatával a [API Management REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx). A REST API használatával mentése és visszaállítása az entitásokat a kívánt szolgáltatás példányból.
* A Git használatával töltheti le a szolgáltatás konfigurációját, és töltse fel azt egy új példányát. További információkért lásd: [mentésére és a Git használatával az API Management szolgáltatás konfigurációs konfigurálása](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Programozott módon is kezelhető az API Management-példány?
Igen, kezelheti az API Management programozott módon használatával:

* A [az API Management REST API-val](https://msdn.microsoft.com/library/azure/dn776326.aspx).
* A [a Microsoft Azure az ApiManagement Service Management Library SDK](https://aka.ms/apimsdk).
* A [szolgáltatás telepítését](https://docs.microsoft.com/powershell/module/wds) és [szolgáltatásfelügyelet](https://docs.microsoft.com/powershell/azure/servicemanagement/overview) PowerShell-parancsmagokat.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hogyan adhatok hozzá a felhasználó a rendszergazdák csoporthoz?
Itt látható, hogyan adhat hozzá egy felhasználót a Rendszergazdák csoport:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg az erőforráscsoport, amely rendelkezik az API Management-példány frissíteni szeretné.
3. Az API Management, rendelje hozzá a **Api Management közreműködői** szerepkört a felhasználóhoz.

Az újonnan hozzáadott közreműködői használható Azure PowerShell most [parancsmagok](https://docs.microsoft.com/powershell/azure/overview). A következő rendszergazdai bejelentkezés:

1. Használja a `Connect-AzureRmAccount` parancsmagot, hogy jelentkezzen be.
2. A környezet beállítása az előfizetéshez, amely rendelkezik a szolgáltatás használatával `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Egy egyszeri bejelentkezési URL-cím beszerzése a `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. A felügyeleti portál eléréséhez használja az URL-címet.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Miért van a házirend, amelyet szeretnék hozzáadni a Helyicsoportházirend-szerkesztő nem érhető el?
Ha a szabályzatot, amely a hozzáadni kívánt halványan jelenik meg, vagy a Helyicsoportházirend-szerkesztő árnyékolt, arra, hogy a megfelelő terjed ki a szabályzat jelenik meg. Minden egyes házirendutasítás lett tervezve, bizonyos hatókörökre és szabályzat szakaszok. A szabályzat szakaszok és a egy házirend hatóköreinek című témakörben találja a szabályzat használata című szakaszában található [API Management házirendek](https://msdn.microsoft.com/library/azure/dn894080.aspx).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hogyan állíthatok be egy egyetlen API több környezetet?
Állítsa be több környezetet, például a tesztelési környezet és a egy éles környezetben egy API, két lehetősége van. A következőket teheti:

* Gazdagép másik API-k bérlőnél.
* A gazdagép a különböző bérlők számára az API-kkal.

### <a name="can-i-use-soap-with-api-management"></a>Használhatom-e a SOAP API Management?
[SOAP típusú továbbítás](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) már elérhető támogatás. -Rendszergazdák importálni tudják a WSDL a SOAP-szolgáltatás, és az Azure API Management létrehoz egy SOAP-előtérrendszerhez. Fejlesztői portál dokumentációja, a tesztelési konzol, a házirendek és a analytics mind elérhetők a SOAP-szolgáltatások.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>Az API Management gateway IP cím állandó? Is használhatók a tűzfalszabályok?
Az API Management minden szinten a nyilvános IP-cím (VIP) az API Management-bérlőkulcs statikus a bérlő teljes élettartama néhány kivétellel. Az IP-cím módosításainak ilyen körülmények között:

* A szolgáltatás törli és újból létrehozza majd.
* A szolgáltatás az előfizetés [felfüggesztve](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) vagy [figyelmezteti](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (például a fizetés elmaradása), és ezután vizsgadíj.
* Adja hozzá, vagy távolítsa el az Azure Virtual Network (virtuális hálózathoz használhat csak a fejlesztői és a prémium szintű).

Többrégiós üzemelő példányokhoz, a regionális cím megváltozik, ha a régió vacated, és ezután vizsgadíj (használhat több régióban történő üzembe helyezés csak a prémium szint).

Prémium szintű bérlők többrégiós üzembe helyezési konfigurált rendelt régiónkénti egy nyilvános IP-címet.

A bérlő oldalon az Azure Portalon kérheti le az IP-cím (vagy címek, a több régióból álló üzemelő).

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Konfigurálhatok az OAuth 2.0 engedélyezési kiszolgáló az AD FS biztonsági?
Az OAuth 2.0 engedélyezési kiszolgáló konfigurálása az Active Directory összevonási szolgáltatások (AD FS) biztonsági kapcsolatban lásd: [AD FS használatával az API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Milyen útválasztási módszert használni a az API Management a több földrajzi helyen történő központi telepítések?
Az API Management használja a [teljesítmény forgalom-útválasztási módszer](../traffic-manager/traffic-manager-routing-methods.md#performance) telepítések esetén, több földrajzi helyekre. A bejövő forgalmat irányítja a rendszer a legközelebbi API-átjáró. Ha egy régió elérhetetlenné válik, a bejövő forgalom automatikusan irányítja a rendszer a következő legközelebbi átjáró. További információ az útválasztási metódusok [Traffic Manager útválasztási módszerei](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Az Azure Resource Manager-sablonok segítségével az API Management szolgáltatáspéldány létrehozása?
Igen. Tekintse meg a [Azure API Management szolgáltatás](https://aka.ms/apimtemplate) gyorsindítási sablonok.

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Használható a háttéralkalmazás az önaláírt SSL-tanúsítvány?
Igen. Ez elvégezhető elküldésével közvetlenül az API-t vagy a Powershellen keresztül. Ez lesz a tanúsítványlánc érvényesítésének letiltása, és lehetővé teszi, hogy az önaláírt vagy privát módon által aláírt tanúsítványokat használ a háttérszolgáltatásoknak az API Management közötti kommunikáció során.

#### <a name="powershell-method"></a>PowerShell-módszer ####
Használja a [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (az új háttér-) vagy [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (a meglévő háttér) PowerShell-parancsmagok és állítsa be a `-SkipCertificateChainValidation` paramétert `True`. 

```powershell
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Közvetlen API frissítési módszer ####
1. Hozzon létre egy [háttérrendszer](https://msdn.microsoft.com/library/azure/dn935030.aspx) entitás API Management segítségével.       
2. Állítsa be a **skipCertificateChainValidation** tulajdonságot **igaz**.     
3. Ha már nem szeretné, hogy az önaláírt tanúsítványokat, a háttérbeli entitás törlése, vagy állítsa be a **skipCertificateChainValidation** tulajdonságot **hamis**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Miért kapok hitelesítési hiba jelenik meg egy Git-tárház klónozása?
Ha a Git Credential Managert használja, vagy ha egy Git-tárház klónozásához a Visual Studio használatával, előfordulhat, hogy tapasztal egy ismert probléma, amely a Windows hitelesítő adatok párbeszédpanel. A párbeszédpanel 127 karakter hosszú lehet a jelszó hossza korlátozza, és hogy csonkolja a Microsoft által létrehozott jelszót. Dolgozunk lerövidíteni a jelszót. Most használja a Git Bash a Git-tárház klónozásához.

### <a name="does-api-management-work-with-azure-expressroute"></a>Az API Management működik az Azure ExpressRoute használatával?
Igen. Az API Management az Azure ExpressRoute használatával működik.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Miért azt igénylik kijelölt alhálózatot a virtuális hálózatok Resource Manager-stílus be központi telepítésekor az API Management?
A kijelölt alhálózatot követelmény az API Management származik, hogy (PAAS V1 réteg) klasszikus üzembe helyezési modell alapján készült. Azt üzembe helyezhető Resource Manager VNET (2. réteg), amíg nincsenek, amely a következményekkel. A Resource Manager-modell nem szorosan összekapcsolt a klasszikus üzemi modellben az Azure-ban, és így V2 réteg egy erőforrást hoz létre, ha a V1 réteg nem ismert, és problémák fordulhat elő, például az API Management próbál használni, amely már hozzá van rendelve egy hálózati adapter IP-címet  (V2 alapul).
További információ az Azure-ban a klasszikus és Resource Manager modellek különbség lásd: [különbség az üzemi modellekben található](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Mi az API Management egy vnetbe telepítésekor szükséges minimális alhálózat méretét?
Az API Management üzembe helyezéséhez szükséges minimális alhálózat mérete [/29](../virtual-network/virtual-networks-faq.md#configuration), azaz az Azure által támogatott minimális alhálózat méretét.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Áthelyezhető egy API Management-szolgáltatás egy előfizetésből egy másikba?
Igen. További információ [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>A korlátozásokat és saját API importálása ismert problémái vannak-e?
[Ismert problémák és korlátozások](api-management-api-import-restrictions.md) az Open API(Swagger) WSDL és WADL formázza.
