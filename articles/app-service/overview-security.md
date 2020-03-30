---
title: Biztonság
description: Ismerje meg, hogyan segít az App Service az alkalmazás biztonságossá tétele, és hogyan zárhatja le még az alkalmazást a fenyegetésektől.
keywords: Azure app szolgáltatás, web app, mobil app, api app, függvény app, biztonság, biztonságos, biztonságos, megfelelőség, megfelelő, tanúsítvány, tanúsítványok, https, ftps, TLS, bizalom, titkosítás, titkosítás, titkosított, ip korlátozás, hitelesítés, engedélyezés, hitelesítés, autho, msi, felügyelt szolgáltatás identitás, felügyelt identitás, titkos, titkos, folt, javítás, javítások, verzió, elkülönítés, hálózati elkülönítés, ddos, mitm
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: e28935f73511e5ad973929517658cc626b5a6ea2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475374"
---
# <a name="security-in-azure-app-service"></a>Biztonság az Azure App Service-ben

Ebből a cikkből megtudhatja, hogy [az Azure App Service](overview.md) hogyan segíti a webalkalmazás, a mobilalkalmazás háttér- és háttér- és [függvényalkalmazásának](/azure/azure-functions/)biztonságossá tétele. Azt is bemutatja, hogyan biztosíthatja tovább az alkalmazást a beépített App Service-funkciókkal.

Az App Service platformösszetevői, beleértve az Azure virtuális gépeket, a tárolást, a hálózati kapcsolatokat, a webes keretrendszereket, a felügyeleti és integrációs funkciókat, aktívan védettek és megerősítettek. Az App Service folyamatosan intenzív megfelelőségi ellenőrzéseken megy keresztül, hogy megbizonyosodjon arról, hogy:

- Az alkalmazás erőforrásai a többi ügyfél Azure-erőforrásaiból [vannak biztosítva.](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
- [A virtuálisgép-példányok és a futásidejű szoftverek rendszeresen frissülnek az](overview-patch-os-runtime.md) újonnan felfedezett biztonsági rések kezelése érdekében. 
- Az alkalmazás és más Azure-erőforrások (például az SQL [Database)](https://azure.microsoft.com/services/sql-database/)közötti titkos kulcsok (például kapcsolati karakterláncok) közötti kommunikáció az Azure-on belül marad, és nem lépi át a hálózati határokat. A titkok tárolásakor mindig titkosítva vannak.
- Az App Service-kapcsolati szolgáltatásokon keresztüli összes kommunikáció, például [a hibrid kapcsolat](app-service-hybrid-connections.md)titkosítva van. 
- A távoli felügyeleti eszközökkel, például az Azure PowerShell, az Azure CLI, az Azure SDK-k, a REST API-k használatával rendelkező kapcsolatok titkosítottak.
- A 24 órás fenyegetéskezelés megvédi az infrastruktúrát és a platformot a rosszindulatú programoktól, az elosztott szolgáltatásmegtagadástól (DDoS), a közbeékelődéstől (MITM) és más fenyegetésektől.

Az Azure infrastruktúrájáról és platformbiztonságáról az [Azure Adatvédelmi központban](https://azure.microsoft.com/overview/trusted-cloud/)talál további információt.

A következő szakaszok bemutatják, hogyan védheti meg tovább az App Service-alkalmazást a fenyegetésektől.

## <a name="https-and-certificates"></a>HTTPS és tanúsítványok

Az App Service lehetővé teszi az alkalmazások HTTPS-vel történő [védelmét.](https://wikipedia.org/wiki/HTTPS) Az alkalmazás létrehozásakor az alapértelmezett\<tartománynév (app_name>.azurewebsites.net) már elérhető https használatával. Ha [egyéni tartományt állít be az alkalmazáshoz,](app-service-web-tutorial-custom-domain.md) [ssl-tanúsítvánnyal](configure-ssl-bindings.md) is biztosítania kell azt, hogy az ügyfélböngészők biztonságos HTTPS-kapcsolatot létesíthessenek az egyéni tartománnyal. Az App Service többféle tanúsítványtípust támogat:

- Ingyenes app szolgáltatás felügyelt tanúsítvány
- App Service-tanúsítvány
- Külső tanúsítvány
- Tanúsítvány importált Az Azure Key Vault

További információ: [Ssl-tanúsítvány hozzáadása az Azure App Service szolgáltatásban](configure-ssl-certificate.md)című témakörben talál.

## <a name="insecure-protocols-http-tls-10-ftp"></a>Nem biztonságos protokollok (HTTP, TLS 1.0, FTP)

Az alkalmazás minden titkosítatlan (HTTP) kapcsolat elleni védelméhez az App Service egykattintásos konfigurációt biztosít a HTTPS kényszerítéséhez. A nem biztonságos kérelmeket még az alkalmazáskód elérése előtt elkell fordítani. További információ: [Https kényszerítése.](configure-ssl-bindings.md#enforce-https)

[A TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0-t már nem tekintik biztonságosnak az iparági szabványok, például a [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Az App Service lehetővé teszi az elavult protokollok letiltását [a TLS 1.1/1.2 kényszerítésével.](configure-ssl-bindings.md#enforce-tls-versions)

Az App Service támogatja az FTP és az FTPS szolgáltatást a fájlok telepítéséhez. Ftp helyett azonban FTP-t kell használni, ha egyáltalán lehetséges. Ha az egyik vagy mindkét protokoll nincs használatban, [tiltsa le őket.](deploy-ftp.md#enforce-ftps)

## <a name="static-ip-restrictions"></a>Statikus IP-korlátozások

Alapértelmezés szerint az App Service-alkalmazás elfogadja az összes IP-címről érkező kérelmeket az internetről, de korlátozhatja ezt a hozzáférést az IP-címek egy kis részhalmazára. A Windows Alkalmazásszolgáltatás lehetővé teszi az alkalmazás eléréséhez engedélyezett IP-címek listájának meghatározását. Az engedélyezett lista tartalmazhat egyedi IP-címeket vagy egy alhálózati maszk által meghatározott IP-címtartományt. További információ: [Azure App Service static IP restrictions](app-service-ip-restrictions.md).

A Windows App Service esetén az IP-címeket dinamikusan is korlátozhatja a _web.config_konfigurálásával. További információ: [Dynamic IP \<Security dynamicIpSecurity>. ](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/)

## <a name="client-authentication-and-authorization"></a>Ügyfél hitelesítése és engedélyezése

Az Azure App Service kulcsrakész hitelesítést és a felhasználók vagy ügyfélalkalmazások engedélyezését biztosítja. Ha engedélyezve van, akkor jelentkezzen be a felhasználók és az ügyfélalkalmazások kevés vagy semmilyen alkalmazáskódot. Saját hitelesítési és engedélyezési megoldását valósíthatja meg, vagy engedélyezheti, hogy az App Service kezelje azt. A hitelesítési és engedélyezési modul kezeli a webes kérelmeket, mielőtt átadja őket az alkalmazáskódnak, és megtagadja a jogosulatlan kérelmeket, mielőtt azok elérnék a kódot.

Az App Service hitelesítése és engedélyezése több hitelesítési szolgáltatót támogat, beleértve az Azure Active Directoryt, a Microsoft-fiókokat, a Facebookot, a Google-t és a Twittert. További információkért lásd: [Hitelesítés és engedélyezés az Azure App Service-ben](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Szolgáltatások közötti hitelesítés

A háttérszolgáltatás hitelesítésekénte az App Service az igényétől függően két különböző mechanizmust biztosít:

- **Szolgáltatásidentitás** – Jelentkezzen be a távoli erőforrásba az alkalmazás identitásának használatával. Az App Service segítségével egyszerűen létrehozhat egy [felügyelt identitást,](overview-managed-identity.md)amellyel hitelesíthető más szolgáltatásokkal, például az [Azure SQL Database vagy](/azure/sql-database/) az Azure Key Vault [használatával.](/azure/key-vault/) A megközelítés teljes körű oktatóanyaga: [Secure Azure SQL Database connection from App Service using a managed identity](app-service-web-tutorial-connect-msi.md).
- **Az (OBO) nevében** – Delegált hozzáférés biztosítása a távoli erőforrásokhoz a felhasználó nevében. Az Azure Active Directory hitelesítési szolgáltatóként az App Service-alkalmazás delegált bejelentkezést végezhet egy távoli szolgáltatásba, például a [Microsoft Graph API-ba](../active-directory/develop/microsoft-graph-intro.md) vagy egy távoli API-alkalmazásba az App Service-ben. A megközelítés végpontok között oktatóanyaga a [Felhasználók hitelesítése és engedélyezése az Azure App Service-ben](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Távoli erőforrásokhoz való kapcsolódás

Az alkalmazásnak háromféle távoli erőforráshoz kell hozzáférnie: 

- [Azure-erőforrások](#azure-resources)
- [Azure virtuális hálózaton belüli erőforrások](#resources-inside-an-azure-virtual-network)
- [Helyszíni erőforrások](#on-premises-resources)

Ezekben az esetekben az App Service lehetővé teszi a biztonságos kapcsolatok létesítését, de továbbra is be kell tartania a biztonsági ajánlott eljárásokat. Például mindig használjon titkosított kapcsolatokat akkor is, ha a háttérerőforrás lehetővé teszi a titkosítatlan kapcsolatokat. Továbbá győződjön meg arról, hogy a háttér-Azure-szolgáltatás lehetővé teszi az IP-címek minimális készletét. Az alkalmazás kimenő IP-címeit az [Azure App Service bejövő és kimenő IP-címei ben](overview-inbound-outbound-ips.md)találja.

### <a name="azure-resources"></a>Azure-erőforrások

Amikor az alkalmazás csatlakozik az Azure-erőforrásokhoz, például az [SQL Database-hez](https://azure.microsoft.com/services/sql-database/) és az [Azure Storage-hoz,](/azure/storage/)a kapcsolat az Azure-on belül marad, és nem lépi át a hálózati határokat. A kapcsolat azonban az Azure-beli megosztott hálózaton keresztül történik, ezért mindig győződjön meg arról, hogy a kapcsolat titkosítva van. 

Ha az alkalmazás [alkalmazásszolgáltatás-környezetben](environment/intro.md)van, [a virtuális hálózati szolgáltatás végpontjai segítségével csatlakoznia kell a támogatott Azure-szolgáltatásokhoz.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="resources-inside-an-azure-virtual-network"></a>Azure virtuális hálózaton belüli erőforrások

Az alkalmazás [a virtuális hálózatok integrációja](web-sites-integrate-with-vnet.md)révén hozzáférhet az [Azure virtuális hálózat](/azure/virtual-network/) erőforrásaihoz. Az integráció egy virtuális hálózattal jön létre egy pont-hely VPN használatával. Az alkalmazás ezután hozzáférhet a virtuális hálózat erőforrásaihoz a saját IP-címük használatával. A pont-hely kapcsolat azonban továbbra is áthalad a megosztott hálózatok az Azure-ban. 

Ha teljesen el szeretné különíteni az erőforrás-kapcsolatot az Azure megosztott hálózataitól, hozzon létre alkalmazást [egy App Service-környezetben.](environment/intro.md) Mivel egy App Service-környezet mindig egy dedikált virtuális hálózatra van telepítve, az alkalmazás és a virtuális hálózaton belüli erőforrások közötti kapcsolat teljesen el van különítve. Az App Service-környezetben a hálózati biztonság egyéb szempontjairól a [Hálózat elkülönítése](#network-isolation)című témakörben található.

### <a name="on-premises-resources"></a>Helyszíni erőforrások

A helyszíni erőforrásokat, például az adatbázisokat háromféleképpen érheti el biztonságosan: 

- [Hibrid kapcsolatok](app-service-hybrid-connections.md) – Pont-pont kapcsolatot hoz létre a távoli erőforrással egy TCP-alagúton keresztül. A TCP-alagút a TLS 1.2-es, megosztott hozzáférésű (SAS) kulcsokkal rendelkező használatával jön létre.
- [Virtuális hálózat integrációja](web-sites-integrate-with-vnet.md) a helyek közötti VPN-nel – Az [Azure virtuális hálózaton belüli erőforrások](#resources-inside-an-azure-virtual-network)című részben leírtak szerint, de a virtuális hálózat a helyek közötti [VPN-en](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)keresztül is csatlakoztatható a helyszíni hálózathoz. Ebben a hálózati topológiában az alkalmazás a virtuális hálózat más erőforrásaihoz hasonlóan a helyszíni erőforrásokhoz is csatlakozhat.
- [App Service-környezet](environment/intro.md) helyek közötti VPN-nel – Az [Azure virtuális hálózaton belüli erőforrások](#resources-inside-an-azure-virtual-network)című részben leírtak szerint, de a virtuális hálózat a helyek közötti [VPN-en](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)keresztül is csatlakoztatható a helyszíni hálózathoz. Ebben a hálózati topológiában az alkalmazás a virtuális hálózat más erőforrásaihoz hasonlóan a helyszíni erőforrásokhoz is csatlakozhat.

## <a name="application-secrets"></a>Alkalmazásttitok

Ne tárolja az alkalmazástitkos kulcsokat, például az adatbázis hitelesítő adatait, az API-jogkivonatokat és a személyes kulcsokat a kód- vagy konfigurációs fájlokban. Az általánosan elfogadott megközelítés az, hogy a hozzáférést a [környezeti változók](https://wikipedia.org/wiki/Environment_variable) a szabványos minta az Ön által választott nyelven. Az App Service-ben a környezeti változók definiálásának módja az [alkalmazásbeállítások](configure-common.md#configure-app-settings) (és különösen a .NET-alkalmazások, [a kapcsolati karakterláncok) révén](configure-common.md#configure-connection-strings)történik. Az alkalmazásbeállítások és a kapcsolati karakterláncok az Azure-ban titkosítottan tárolódnak, és csak az alkalmazás indításakor az alkalmazás folyamatmemóriájába való injektálás előtt kerülnek visszafejtésre. A titkosítási kulcsok rendszeresen elfordulnak.

Azt is megteheti, hogy integrálja az App Service-alkalmazást az [Azure Key Vault](/azure/key-vault/) fejlett titkos kulcsok kezelésére. A [Key Vault felügyelt identitással való elérésével](../key-vault/tutorial-web-application-keyvault.md)az App Service-alkalmazás biztonságosan hozzáférhet a szükséges titkokhoz.

## <a name="network-isolation"></a>Hálózati elkülönítés

Az **elkülönített** tarifacsomag kivételével az összes réteg futtatja az alkalmazásokat az App Service megosztott hálózati infrastruktúráján. Például a nyilvános IP-címek és a front-end terheléselosztók más bérlőkkel vannak megosztva. Az **Elkülönített** szint teljes hálózati elkülönítést biztosít az alkalmazások dedikált [App Service-környezetben](environment/intro.md)való futtatásával. Az App Service-környezet az [Azure Virtual Network](/azure/virtual-network/)saját példányában fut. A következőkre van lehetőség: 

- Az alkalmazásokat egy dedikált nyilvános végponton keresztül, dedikált előtér-végződésekkel szolgálhatja ki.
- Belső alkalmazás kiszolgálása egy belső terheléselosztó (ILB), amely lehetővé teszi a hozzáférést csak az Azure virtuális hálózaton belülről. Az ILB rendelkezik egy IP-címmel a privát alhálózatból, amely teljes elkülönítést biztosít az alkalmazásoknak az internetről.
- [Használjon ILB-t a webalkalmazás tűzfala (WAF) mögött.](environment/integrate-with-application-gateway.md) A WAF vállalati szintű védelmet nyújt a nyilvános alkalmazásoknak, például a DDoS-védelemnek, az URI-szűrésnek és az SQL-injektálás megelőzésének.

További információ: [Bevezetés az Azure App Service-környezetek bemutatkozása című témakörben.](environment/intro.md) 
