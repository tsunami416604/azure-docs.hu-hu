---
title: "Az Azure API Management – gyakori kérdések |} Microsoft Docs"
description: "Ismerje meg a választ gyakran ismételt kérdések (GYIK) kombinációját, és gyakorlati tanácsok az Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 1903655a262583f1ba78b728bf404a81278e2275
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="azure-api-management-faqs"></a>Az Azure API Management – gyakori kérdések
Válaszok a gyakori kérdéseket, a mintákat és ajánlott eljárások az Azure API Management.

## <a name="contact-us"></a>Kapcsolat
* [Hogyan lehet I kérdés a Microsoft Azure API Management csapata?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Gyakori kérdések
* [Mit jelent, ha a szolgáltatás egyelőre?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Hogyan biztosíthassa a kapcsolatot az API Management-átjáró és a háttér-szolgáltatásaihoz?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Hogyan másolja az API Management service-példány egy új példányt?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Programozott módon is kezelhető az API Management példányt?](#can-i-manage-my-api-management-instance-programmatically)
* [Hogyan adja hozzá a felhasználó a rendszergazdák csoporthoz?](#how-do-i-add-a-user-to-the-administrators-group)
* [Miért van a házirendet, amely a felvenni kívánt nem érhető el a Helyicsoportházirend-szerkesztő?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Hogyan állíthatom be több környezeteknek a egyetlen API-val?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Használhatok SOAP API Management?](#can-i-use-soap-with-api-management)
* [Az API Management gateway IP cím állandó? Képes használni a tűzfalszabályok?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* [Beállítható az OAuth 2.0 hitelesítési kiszolgáló AD FS biztonsági?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
* [Milyen útválasztási módszert használni a API-kezelés a több földrajzi helyen történő telepítését?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Az Azure Resource Manager-sablon használatával hozzon létre egy API-kezelés szolgáltatás példányt?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Használhatók a háttérből az önaláírt SSL-tanúsítvány?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Miért jelenik meg hitelesítési hiba, a GIT-tárház klónozása közben?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Az API Management működik az Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Miért azt igényelnek erőforrás-kezelő stílusban Vnetek dedikált alhálózat amikor API Management központilag telepítik őket?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Mi az az API Management egy VNETET történő telepítésekor szükséges minimális alhálózat méretét?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Áthelyezhető egy API-kezelés szolgáltatás a egy előfizetés másik?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Korlátozások vagy importálása a API ismert problémái vannak-e?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Hogyan lehet I kérdés a Microsoft Azure API Management csapata?
Akkor is kapcsolatba velünk a következő beállítások egyikét:

* A kérdéseit a [API Management MSDN fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* E-mail küldése <mailto:apimgmt@microsoft.com>.
* Funkció kérés küldése a [Azure visszajelzési fórumon](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Mit jelent, ha a szolgáltatás egyelőre?
A szolgáltatás egyelőre, az azt jelenti, hogy azt aktívan kérdése megválaszolásában kapcsolatos visszajelzéseket a funkció az Ön alakulását. Egy előzetes szolgáltatás funkcionálisan befejeződött, de előfordulhat, hogy az ügyfelek visszajelzései alapján megváltozik a legfrissebb lesz biztosítjuk. Azt javasoljuk, hogy nincs-e függ egy szolgáltatás, amely jelenleg előzetes verzióban érhető az éles környezetben. Ha olyan visszajelzést az előzetes verziójú funkciókat, tudassa velünk, a kapcsolattartási lehetőségek egyikével [hogyan is szeretnék kérdés a Microsoft Azure API Management csapata?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hogyan biztosíthassa a kapcsolatot az API Management-átjáró és a háttér-szolgáltatásaihoz?
Több lehetőség közül választhat az API Management-átjáró és a háttér-szolgáltatásaihoz közötti kapcsolat biztonságossá tétele érdekében. A következőket teheti:

* Egyszerű HTTP-hitelesítést használjon. További információkért lásd: [importálása és az első API-t közzétenni](import-and-publish.md).
* SSL kölcsönös hitelesítést használ, a [háttérszolgáltatások ügyfél segítségével biztonságossá tétele a tanúsítványhitelesítés az Azure API Management](api-management-howto-mutual-certificates.md).
* IP-engedélyezése a háttér-szolgáltatáshoz felhasználhat. Minden csomagban API-kezelés, az IP-címet az átjáró állandó marad, néhány [figyelmeztetések](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules). Az engedélyezett címekhez, hogy az IP-cím engedélyezése állíthatja be. A IP-címet a API Management példány kaphat az irányítópulton az Azure portálon.
* Az API Management-példány csatlakozni az Azure virtuális hálózat.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Hogyan másolja az API Management service-példány egy új példányt?
Erre számos lehetősége van, ha át kívánja másolni az API Management példány új példányára. A következőket teheti:

* A biztonsági mentése és visszaállítása az API Management függvény. További információkért lásd: [valósítja meg a vész-helyreállítási szolgáltatás biztonsági másolat használatával, és az Azure API Management visszaállítása](api-management-howto-disaster-recovery-backup-restore.md).
* A saját biztonsági mentést, majd állítsa vissza a szolgáltatás használatával a [API Management REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx). A REST API használatával mentése és visszaállítása az entitásokat, amelyet a szolgáltatás példányból.
* Töltse le a szolgáltatás konfigurációját a Git használatával, és töltse fel azt egy új példányát. További információkért lásd: [mentése, és az API Management szolgáltatás konfigurációs beállítása a Git használatával](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Programozott módon is kezelhető az API Management példányt?
Igen, akkor az API Management programozott módon segítségével kezelheti:

* A [API Management REST API-t](https://msdn.microsoft.com/library/azure/dn776326.aspx).
* A [ApiManagement szolgáltatás könyvtár Microsoft Azure SDK](http://aka.ms/apimsdk).
* A [szolgáltatás telepítését](https://msdn.microsoft.com/library/mt619282.aspx) és [szolgáltatásfelügyelet](https://msdn.microsoft.com/library/mt613507.aspx) PowerShell-parancsmagokkal.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hogyan adja hozzá a felhasználó a rendszergazdák csoporthoz?
Itt látható, hogyan adhat hozzá a felhasználó a Rendszergazdák csoportnak:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ugrás az erőforráscsoport, amely már a frissíteni kívánt API Management-példányt.
3. Az API Management, rendelje hozzá a **Api Management közreműködői** a felhasználói szerepkört.

Az újonnan hozzáadott közreműködői használhatja az Azure PowerShell most [parancsmagok](https://msdn.microsoft.com/library/mt613507.aspx). Jelentkezzen be rendszergazdaként hogyan itt található:

1. Használja a `Login-AzureRmAccount` parancsmag futtatásával jelentkezzen be.
2. A környezet beállítása az előfizetéshez, amely rendelkezik a szolgáltatás használatával `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Egyszeri bejelentkezési URL-cím segítségével könnyebben nyerhet `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Az URL-címet használja a felügyeleti portál eléréséhez.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Miért van a házirendet, amely a felvenni kívánt nem érhető el a Helyicsoportházirend-szerkesztő?
Ha hozzá szeretne adni a házirend megjelenik a szürkén jelenik meg, vagy a Helyicsoportházirend-szerkesztő árnyékolt, arról, hogy a házirend a megfelelő hatókörben vannak-e. Minden egyes házirend-utasítás célja a hatókörökre és házirend szakaszok használata. A házirend szakaszok és egy házirend hatókörök ellenőrzéséhez tekintse meg a Csoportházirend használata című szakaszában található [API-felügyeleti házirendek](https://msdn.microsoft.com/library/azure/dn894080.aspx).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hogyan állíthatom be több környezeteknek a egyetlen API-val?
Állítsa be több környezetekben, például egy tesztkörnyezetben és éles környezetben a egyetlen API-val, két lehetősége van. A következőket teheti:

* Gazdagép másik API-k ugyanannál a bérlőnél.
* A különböző bérlők azonos API-k üzemeltetéséhez.

### <a name="can-i-use-soap-with-api-management"></a>Használhatok SOAP API Management?
[SOAP-áteresztő](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) a rendszer támogatja. -Rendszergazdák importálni tudják a WSDL SOAP szolgáltatása, és az Azure API Management létrehoz egy SOAP-előtér. Fejlesztői portál dokumentációjában, a teszt konzol, a házirendek és a analytics az összes elérhető SOAP-szolgáltatások.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>Az API Management gateway IP cím állandó? Képes használni a tűzfalszabályok?
Az API Management összes rétege a nyilvános IP-cím (VIP) az API Management-bérlő statikus a bérlő teljes néhány kivétellel. Az IP-cím módosításainak ilyen körülmények között:

* A szolgáltatás törlődik, és újból létrehozza majd.
* A szolgáltatás az előfizetés [felfüggesztve](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) vagy [figyelmezteti](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (például nonpayment) és majd érvényességét.
* Vegye fel vagy távolítsa el az Azure Virtual Network (is használja a virtuális hálózat csak a fejlesztői és a prémium csomagban).

Több területi telepítések esetén a regionális címet érintő változásról, ha a régió vacated és majd érvényességét (használhat több területi telepítés csak a prémium csomagban).

Prémium szint bérlők több területi telepítési konfigurált rendelt régiónként egy nyilvános IP-címet.

Az IP-cím (vagy címek, a központi telepítés több területi) az Azure-portálon a bérlő oldalon kaphat.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Beállítható az OAuth 2.0 hitelesítési kiszolgáló AD FS biztonsági?
Az OAuth 2.0 hitelesítési kiszolgáló konfigurálása az Active Directory összevonási szolgáltatások (AD FS) biztonsági további tudnivalókért lásd: [AD FS segítségével az API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Milyen útválasztási módszert használni a API-kezelés a több földrajzi helyen történő telepítését?
API-kezelési funkciója a [teljesítmény forgalom-útválasztási módszer](../traffic-manager/traffic-manager-routing-methods.md#priority) telepítések több földrajzi helyekre. Bejövő továbbítódik a legközelebbi API-átjáró. Ha egy régió tartozik offline állapotba kerül, bejövő automatikusan továbbítódik a következő legközelebbi átjáró. További tudnivalók az útválasztási metódusait [Traffic Manager útválasztási módjai](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Az Azure Resource Manager-sablon használatával hozzon létre egy API-kezelés szolgáltatás példányt?
Igen. Tekintse meg a [Azure API Management Service](http://aka.ms/apimtemplate) gyorsindítási sablonok.

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Használhatók a háttérből az önaláírt SSL-tanúsítvány?
Igen. Ez a PowerShell segítségével, vagy közvetlenül elküldése az API által végezhető. Ez letiltja a tanúsítványlánc érvényesítése, és lehetővé teszi az API Management a háttér-szolgáltatásokhoz való kommunikációhoz használni önaláírt vagy közvetlenül a Microsoftnak által aláírt tanúsítványokat.

#### <a name="powershell-method"></a>PowerShell-módszer ####
Használja a [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (az új háttér) vagy [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (a meglévő háttér) PowerShell-parancsmagok és állítsa be a `-SkipCertificateChainValidation` paramétert `True`. 

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Közvetlen API frissítési módszer ####
1. Hozzon létre egy [háttér](https://msdn.microsoft.com/library/azure/dn935030.aspx) entitás API Management használatával.       
2. Állítsa be a **skipCertificateChainValidation** tulajdonságot **igaz**.     
3. Már nem szeretne önaláírt tanúsítványok használatának engedélyezése, ha a háttérkiszolgáló entitást, vagy állítsa a **skipCertificateChainValidation** tulajdonságot **hamis**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Miért jelenik meg hitelesítési hiba, a Git-tárház klónozása közben?
Ha Git hitelesítőadat-kezelő, vagy egy Git-tárház klónozása a Visual Studio használatával próbál, mutatjuk be egy ismert hiba, mely a Windows hitelesítő adatok párbeszédpanel. A párbeszédpanel korlátozza a jelszó hossza 127 karakter hosszúságú lehet, és hogy csonkolja a Microsoft által létrehozott jelszót. Jelenleg is dolgozunk lerövidíteni a jelszót. Most használja a Git bash eszközt a Git-tárház klónozása.

### <a name="does-api-management-work-with-azure-expressroute"></a>Az API Management működik az Azure ExpressRoute?
Igen. Az API Management Azure ExpressRoute működik.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Miért azt igényelnek erőforrás-kezelő stílusban Vnetek dedikált alhálózat amikor API Management központilag telepítik őket?
Az API Management dedikált alhálózati szükségessége, hogy be van építve (PAAS V1 réteg) klasszikus telepítési modell származik. Telepíthetünk egy erőforrás-kezelő virtuális hálózat (V2 réteg) azokat, amíg nincsenek következményekkel jár-e. A Resource Manager modellt együtt nem szorosan használja a klasszikus telepítési modellt az Azure-ban, és így V2 réteg erőforrás hoz létre, ha a V1 réteg nem ismert, és probléma akkor fordulhat elő, például az API Management próbálja használni az IP-cím már le van foglalva a hálózati adapterhez  (V2 épülő).
Ismerje meg, klasszikus és Resource Manager modellt az Azure-ban különbségét bővebben lásd [különbség az üzembe helyezési modellel](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Mi az az API Management egy VNETET történő telepítésekor szükséges minimális alhálózat méretét?
Az API Management üzembe helyezéséhez szükséges minimális alhálózat mérete [/29](../virtual-network/virtual-networks-faq.md#configuration), vagyis a minimális alhálózat méretét, amely támogatja az Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Áthelyezhető egy API-kezelés szolgáltatás a egy előfizetés másik?
Igen. Megtudhatja, hogyan: [erőforrások áthelyezése egy új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Korlátozások vagy importálása a API ismert problémái vannak-e?
[Ismert problémák és korlátozások](api-management-api-import-restrictions.md) nyitott API(Swagger), a WSDL és WADL formázza.
