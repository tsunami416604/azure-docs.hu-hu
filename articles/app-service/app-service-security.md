---
title: Az Azure App Service és az Azure Functions biztonsági |} Microsoft Docs
description: További tudnivalók hogyan App Service segítségével biztonságos, az alkalmazás, és hogyan tovább zárolhatja az alkalmazás a érintő fenyegetésekkel szemben.
keywords: az Azure app service, a webalkalmazás, a mobilalkalmazás, a api-alkalmazás, a függvény alkalmazás, a biztonság, biztonságos, biztosítva, a megfelelőségi szabályzatnak megfelelő, a tanúsítványt, a tanúsítványok, https, ftps, a tls, a megbízhatósági kapcsolat, a titkosítás, titkosítás, titkosított, ip-korlátozás, hitelesítési, engedélyezési, authn, autho, msi, felügyelt szolgáltatásidentitás, titkos kulcsok, titkos kulcs, javítását, javítás, javítások, verzió, elkülönítés, hálózati elkülönítési, ddos, mitm
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: cephalin
ms.openlocfilehash: 2ca1c1518589e60a03570e1c2063381f749ed9aa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655077"
---
# <a name="security-in-azure-app-service-and-azure-functions"></a>Az Azure App Service és az Azure Functions biztonsági

Ez a cikk bemutatja, hogyan [Azure App Service](app-service-web-overview.md) segítséget nyújt a webes alkalmazás, mobilalkalmazás háttér, API-alkalmazás, biztonságos és [Azure Functions](/azure/azure-functions/). Azt is bemutatja, hogyan tovább növelheti az alkalmazás a beépített App Service-szolgáltatások.

Az App Service, Azure virtuális gépeken, tárolási, hálózati kapcsolatok, webes keretrendszerek, felügyeleti és integrációs szolgáltatásai, beleértve a webplatform-összetevők aktívan védett és megerősítve. App Service végighalad az erőteljes megfelelőségi ellenőrzés, és győződjön meg arról, hogy folyamatosan:

- Az alkalmazás erőforrások [biztonságos](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) a többi ügyfél Azure-erőforrások.
- [Virtuálisgép-példányok és futásidejű szoftver rendszeresen frissülnek](app-service-patch-os-runtime.md) a cím újonnan felderített biztonsági réseinek. 
- A titkos kulcsokat (például a kapcsolati karakterláncok) az alkalmazás és más Azure-erőforrások közötti kommunikációs (például [SQL-adatbázis](/services/sql-database/)) Azure-ban marad, és nem telephelyek közötti hálózati határokat. Titkos kulcsok mindig titkosítását, ha a tárolja.
- Az App Service-kapcsolaton keresztül minden kommunikáció, hasonló funkcióknak [a hibrid kapcsolat](app-service-hybrid-connections.md), titkosítva van. 
- Kapcsolatok távoli felügyeleti eszközökkel, például az Azure PowerShell, az Azure parancssori felület, az Azure SDK-k, REST API-k, az összes titkosított.
- 24 órás threat management megvédi az infrastruktúra és a kártevő szoftverek, elleni platform elosztott szolgáltatásmegtagadásos (DDoS-), man az-a-elkövetett (MITM), és más fenyegetésekkel szemben.

Az Azure-infrastruktúra és a platform biztonsági további információkért lásd: [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/overview/trusted-cloud/).

Az alábbi szakaszok bemutatják a fokozott védelme érdekében az App Service-alkalmazás fenyegetések ellen.

## <a name="https-and-certificates"></a>HTTPS- és tanúsítványok

App Service lehetővé teszi az alkalmazások biztonságos [HTTPS](https://wikipedia.org/wiki/HTTPS). Az alkalmazás létrehozásakor, az alapértelmezett tartomány nevét (\<alkalmazás_neve >. azurewebsites.net) már HTTPS segítségével érhető el. Ha Ön [az alkalmazás az egyéni tartománynév konfigurálása](app-service-web-tutorial-custom-domain.md), ezenfelül [biztonságossá tenni a egyéni tanúsítvánnyal](app-service-web-tutorial-custom-ssl.md) , hogy az ügyfelek böngészőin teheti a saját egyéni tartománynévre történő biztonságos HTTPS-kapcsolatok. Két módon teheti meg:

- **App Service tanúsítvány** -tanúsítvány létrehozása közvetlenül az Azure-ban. A tanúsítvány a védett [Azure Key Vault](/azure/key-vault/), és az App Service alkalmazásba importálható. További információkért lásd: [vásárolnia és SSL-tanúsítvány konfigurálása az Azure App Service](web-sites-purchase-ssl-web-site.md).
- **Külső tanúsítvány** - töltsön fel egyéni SSL-tanúsítványt, amely egy megbízható hitelesítésszolgáltatótól vásárolta, és kösse App Service-alkalmazás. App Service egyetlen tartományból tanúsítványokat és a helyettesítő tanúsítványokat is támogatja. Tesztelési célokra önaláírt tanúsítványokat is támogatja. További információkért lásd: [egy meglévő egyéni SSL-tanúsítvány kötését az Azure Web Apps](app-service-web-tutorial-custom-ssl.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Nem biztonságos protokollok (HTTP, TLS 1.0, FTP)

Az alkalmazás (HTTP) az összes titkosítatlan kapcsolat elleni védelme érdekében az App Service biztosít egy kattintással konfigurációs HTTPS érvényesítését. Nem biztonságos kérelmeket számítógépnél ki vannak kapcsolva, mielőtt még az alkalmazás kódjában. További információkért lásd: [kényszerítése HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https).

[A TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 már nem számít biztonságos ipari szabványok, például a [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). App Service segítségével úgy elavult protokoll által letiltása [kényszerítése a TLS 1.1-es vagy 1.2-es](app-service-web-tutorial-custom-ssl.md#enforce-tls-1112).

App Service támogatja az FTP és FTPS-t a fájlok telepítéséhez. Azonban ftps-t használjon helyett FTP, ha lehetséges. Ha az egyik vagy mindkét ezeket a protokollokat nincsenek használatban, akkor [őket tiltani](app-service-deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Statikus IP-címkorlátozások

Alapértelmezés szerint az App Service alkalmazás összes IP-címet az internetről érkező kéréseket fogad el, de korlátozhatja, hogy az IP-címek egy szűk részhalmaza elérésére. A Windows App Service lehetővé teszi, amelyek jogosultak hozzáférni az alkalmazáshoz IP-címek listájának megadása. Az engedélyezett bővítmények listájához egyedi IP-címeket is tartalmazzák, vagy egy adott IP-címek határozzák meg egy alhálózati maszkot. További információkért lásd: [Azure App Service statikus IP-korlátozásokat](app-service-ip-restrictions.md).

A Windows App Service-ben is korlátozhatja IP-címeket dinamikusan úgy konfigurálja a _web.config_. További információkért lásd: [dinamikus IP-biztonság <dynamicIpSecurity> ](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Ügyfél-hitelesítés és engedélyezés

Az Azure App Service kulcsrakész hitelesítést és engedélyezést biztosít a felhasználók vagy az ügyfél alkalmazásokat. Ha engedélyezve van, a felhasználók és az ügyfél-alkalmazások kevéssé vagy egyáltalán ne alkalmazáskód jelentkezhetnek. Előfordulhat, hogy a saját hitelesítési és engedélyezési megoldás megvalósításának, vagy inkább kezelésére, az App Service engedélyezése. A hitelesítési és engedélyezési modul kezeli a webes kérelmek beadása őket, az alkalmazás kódjának előtt, és azt megtagadja jogosulatlan elérése előtti a kódot.

App Service hitelesítés és engedélyezés támogatja több hitelesítésszolgáltatókat, beleértve az Azure Active Directory, Microsoft fiókok, Facebook, Google, és a Twitterhez. További információkért lásd: [hitelesítési és engedélyezési az Azure App Service](app-service-authentication-overview.md).

## <a name="service-to-service-authentication"></a>Szolgáltatások közötti hitelesítés

Egy háttér-szolgáltatás hitelesítést, amikor az App Service biztosít két különböző módszerek attól függően, hogy a szükséges:

- **Szolgáltatás identitásának** -jelentkezzen be a távoli erőforrás maga az alkalmazás identitását használja. App Service segítségével egyszerűen létrehozhatja egy [identitás](app-service-managed-service-identity.md), más szolgáltatásokkal, például a hitelesítést használó [Azure SQL Database](/azure/sql-database/) vagy [Azure Key Vault](/azure/key-vault/). Ez a megközelítés végpont oktatóanyag lásd: [Azure SQL Database biztonságos kapcsolat az App Service segítségével felügyelt szolgáltatásidentitás](app-service-web-tutorial-connect-msi.md).
- **A-nevében-: (OBO)** -delegált hozzáférést ellenőrizze a távoli erőforrásokhoz a felhasználó nevében. Az Azure Active Directory hitelesítési szolgáltató App Service-alkalmazás hajthat végre delegált jelentkezzen be a távoli szolgáltatás, például a [Azure Active Directory Graph API](../active-directory/develop/active-directory-graph-api.md) vagy egy távoli API-alkalmazás az App Service-ben. Ez a megközelítés végpont oktatóanyag lásd: [hitelesítése és engedélyezése a felhasználók-végpont az Azure App Service](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Csatlakozást a távoli erőforrásokhoz

A távoli erőforrások eléréséhez az alkalmazás esetleg három típusa van: 

- [Azure-erőforrások](#azure-resources)
- [Egy Azure virtuális hálózaton belüli erőforrások](#resources-inside-an-azure-virtual-network)
- [A helyszíni erőforrások](#on-premises-resources)

Az egyes ezekben az esetekben alkalmazás szolgáltatás lehetőséget biztosít ahhoz, hogy biztonságos kapcsolatot, de azt kell ajánlott biztonsági eljárások továbbra is láthatja. Például mindig használjon titkosított kapcsolatok akkor is, ha a háttér-erőforrás lehetővé teszi, hogy a titkosított kapcsolatokat. Ezenkívül győződjön meg arról, hogy a háttér-Azure-szolgáltatás lehetővé teszi, hogy a minimális IP-címeket. A kimenő IP-címek keresése az alkalmazást [bejövő és kimenő IP-címek az Azure App Service](app-service-ip-addresses.md).

### <a name="azure-resources"></a>Azure-erőforrások

Amikor az alkalmazás csatlakozik Azure-erőforrások, például a [SQL-adatbázis](/services/sql-database/) és [Azure Storage](/azure/storage/), a kapcsolat Azure-ban marad, és nem telephelyek közötti hálózati határokat. A kapcsolat az Azure-ban a megosztott hálózati végig kell vinnie azonban, ezért mindig győződjön meg arról, hogy a kapcsolat van titkosítva. 

Ha az alkalmazás a egy [App Service Environment-környezet](environment/intro.md), akkor [csatlakozni az Azure-szolgáltatásokat használó virtuális hálózati szolgáltatási végpont támogatott](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Egy Azure virtuális hálózaton belüli erőforrások

Az alkalmazás által elérhető erőforrásokat egy [Azure Virtual Network](/azure/virtual-network/) keresztül [virtuális hálózati integráció](web-sites-integrate-with-vnet.md). Az integráció létrejön egy pont – hely VPN-kapcsolattal egy virtuális hálózattal. Az alkalmazás ezután hozzáférhetnek az erőforrásokat a magánhálózati IP-címek használata a virtuális hálózatban. A pont – hely kapcsolatot, azonban továbbra is hálózatokon haladnak át a megosztott az Azure-ban. 

Az erőforrás kapcsolat teljes mértékben az Azure-ban a megosztott hálózatok elkülönítésére, hozzon létre az alkalmazáshoz a egy [App Service Environment-környezet](environment/intro.md). Az App Service-környezetek mindig központi telepítése egy dedikált virtuális hálózatot, mert az alkalmazás és a virtuális hálózaton lévő erőforrások közötti kapcsolat teljesen elkülönített. Más vonatkozásai hálózati App Service-környezet, lásd: [Hálózatelkülönítés](#network-isolation).

### <a name="on-premises-resources"></a>A helyszíni erőforrások

A helyszíni erőforrásokat, például adatbázisokat, háromféle biztonságosan érhető el: 

- [Hibrid kapcsolatok](app-service-hybrid-connections.md) -kapcsolatot hoz létre, pont-pont a távoli erőforrás egy TCP-csatornán keresztül. A TCP-alagút jött létre a TLS 1.2 közös hozzáférésű jogosultságkód (SAS) kulcsokkal.
- [Virtuális hálózati integráció](web-sites-integrate-with-vnet.md) --webhelyek közötti VPN - leírtak a [egy Azure virtuális hálózaton belüli erőforrások](#resources-inside-an-azure-virtual-network), a virtuális hálózat lehet csatlakoztatni a helyszíni hálózaton keresztül, de egy [ telephelyek közötti VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). A hálózati topológia az alkalmazás képes csatlakozni a helyszíni erőforrásokhoz, mint a virtuális hálózatán lévő más erőforrásokat.
- [App Service Environment-környezet](environment/intro.md) --webhelyek közötti VPN - leírtak a [egy Azure virtuális hálózaton belüli erőforrások](#resources-inside-an-azure-virtual-network), a virtuális hálózat lehet csatlakoztatni a helyszíni hálózaton keresztül, de egy [pont-pont VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). A hálózati topológia az alkalmazás képes csatlakozni a helyszíni erőforrásokhoz, mint a virtuális hálózatán lévő más erőforrásokat.

## <a name="application-secrets"></a>Alkalmazás titkos kulcsok

Alkalmazás titkos adatait, például az adatbázis hitelesítő adatai, API-jogkivonatok és titkos kulcsok ne tároljon a kód vagy a konfigurációs fájlokban. Az általánosan elfogadott megoldás, mint azok eléréséhez [környezeti változók](https://wikipedia.org/wiki/Environment_variable) a szabványos minta használatával a választott nyelven. Az App Service-ben a környezeti változók megadása módja keresztül [Alkalmazásbeállítások](web-sites-configure.md#app-settings) (és .NET-alkalmazások, különösen a [kapcsolati karakterláncok](web-sites-configure.md#connection-strings)). Alkalmazásbeállítások és kapcsolati karakterláncok tárolja titkosított formában az Azure-ban, és azok még csak az éppen be a nézetmodellbe folyamat memória az alkalmazás az alkalmazás indításakor előtt visszafejti. A titkosítási kulcsok legyenek-e elforgatva rendszeresen.

Másik lehetőségként integrálhatja az App Service alkalmazás [Azure Key Vault](/azure/key-vault/) speciális titkos kulcsok kezeléséhez. Által [felügyelt szolgáltatásidentitás rendelkező Key Vaultból való hozzáférés](../key-vault/tutorial-web-application-keyvault.md), az App Service alkalmazás biztonságosan férhetnek hozzá a titkos kulcsok van szüksége.

## <a name="network-isolation"></a>Hálózatelkülönítés

Kivéve az **elszigetelt** IP-címek, minden csomagban futtassa az alkalmazások a megosztott hálózati infrastruktúrán, az App Service-ben. Például a nyilvános IP-címek és a terheléselosztó előtérbeli vannak megosztva, a többi bérlő. A **elszigetelt** réteg lehetővé teszi teljes hálózati elkülönítési belül egy dedikált alkalmazásai futtatásával [App Service Environment-környezet](environment/intro.md). Az App Service-környezetek fut, a saját példányát [Azure Virtual Network](/azure/virtual-network/). Lehetővé teszi: 

- A hálózati hozzáférés korlátozása [hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md). 
- A dedikált előtér-webkiszolgálóinak ellenőrizhető, hogy az alkalmazások egy dedikált nyilvános végpontot keresztül.
- Belső alkalmazást egy belső terheléselosztó (ILB), amely lehetővé teszi az elérést csak az Azure virtuális hálózaton belül szolgálnak. A ILB rendelkezik a titkos alhálózati, amely az alkalmazások teljes elkülönítést is biztosít az internetről IP-címeit.
- [Használjon egy ILB (WAF) webalkalmazási tűzfal mögött](environment/integrate-with-application-gateway.md). A WAF a nyilvánosan elérhető alkalmazások, például DDoS-védelem, URI szűrési és SQL injektálási megelőzése vállalati szintű védelmet biztosít.

További információkért lásd: [Bevezetés az Azure App Service Environment-környezetek](environment/intro.md).