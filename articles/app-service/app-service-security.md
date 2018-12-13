---
title: Biztonsági áttekintés – az Azure App Service |} A Microsoft Docs
description: Ismerje meg hogyan App Service segítségével biztonságos alkalmazását, és hogyan lehet további zárolását az alkalmazás a fenyegetésekkel szemben.
keywords: az Azure app service, webalkalmazás, mobilalkalmazás, api-alkalmazás, függvényalkalmazás, biztonság, biztonságos, biztonságos, megfelelőség, a megfelelő, a tanúsítványt, a tanúsítványok, https, ftps, tls, a megbízhatóság, a titkosítás, titkosítás, titkosított, ip-korlátozást, hitelesítés, engedélyezés, authn, autho, msi, felügyeltszolgáltatás-identitás, felügyelt identitás, titkos kulcsok, titkos kulcsot, javítás, patch, javítások, verzió, elkülönítés, hálózatelkülönítés, ddos, mitm
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
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 7c2297fa5f7ebaae1c54794ebbbd37e25d3931ca
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53251470"
---
# <a name="security-in-azure-app-service-and-azure-functions"></a>Biztonság az Azure App Service és az Azure Functions

Ez a cikk bemutatja, hogyan [Azure App Service](app-service-web-overview.md) segít a webalkalmazások, mobilalkalmazások háttérrendszerének, API-alkalmazásba, biztonságos és [Azure Functions](/azure/azure-functions/). Azt is bemutatja, hogyan tovább növelheti az alkalmazás az App Service-szolgáltatások beépített.

Az App Service, Azure virtuális gépek, tárolási, hálózati kapcsolatok, webes keretrendszerek, felügyeleti és az integrációs szolgáltatások, beleértve a platform összetevők aktívan védett és megerősített. App Service-ben győződjön meg arról, hogy rendszeresen erőteljes megfelelőségi ellenőrzést végighalad:

- Az alkalmazás-erőforrások vannak [biztonságos](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) az Azure-erőforrások a más ügyfelek adataitól.
- [Virtuálisgép-példányok és a futtatókörnyezet szoftver rendszeresen frissülnek](app-service-patch-os-runtime.md) cím újonnan felfedezett biztonsági rések. 
- A titkos kulcsokat (például a kapcsolati karakterláncok) az alkalmazás és más Azure-erőforrások közötti kommunikáció (például [SQL Database](https://azure.microsoft.com/services/sql-database/)) Azure-ban marad, és nem közötti hálózati határok. Titkos kódok mindig titkosítva vannak tárolt.
- Az App Service-kapcsolatot minden kommunikációhoz használt szolgáltatásokat, például [hibrid kapcsolat](app-service-hybrid-connections.md), titkosítva van. 
- Kapcsolatok a távoli felügyeleti eszközökkel, mint például az Azure PowerShell-lel, az Azure CLI, Azure SDK, REST API-k, az összes titkosított.
- kezelés gondoskodik az infrastruktúra és a platform, a kártevők elleni 24 órás threat elosztott szolgáltatásmegtagadásos (DDoS-), man-in-the-middle (MITM) és más fenyegetések ellen.

Az Azure-beli infrastruktúra- és platformszolgáltatások biztonsági további információkért lásd: [Azure adatvédelmi központ](https://azure.microsoft.com/overview/trusted-cloud/).

A következő szakaszok bemutatják, hogyan védheti még jobban az App Service-alkalmazást a fenyegetésekkel szemben.

## <a name="https-and-certificates"></a>HTTPS protokoll és tanúsítványok

App Service lehetővé teszi az alkalmazások biztonságos [HTTPS](https://wikipedia.org/wiki/HTTPS). Az alkalmazás létrehozásakor az alapértelmezett tartomány nevét (\<alkalmazás_neve >. azurewebsites.net) már elérhető a HTTPS-en keresztül. Ha Ön [az alkalmazás egyéni tartomány konfigurálása](app-service-web-tutorial-custom-domain.md), akkor is [egyéni tanúsítvánnyal biztosított](app-service-web-tutorial-custom-ssl.md) , hogy az ügyfelek böngészőin teheti a biztonságos HTTPS-kapcsolatok az egyéni tartományban. Kétféleképpen teheti meg:

- **App Service-tanúsítvány** -tanúsítvány létrehozása közvetlenül az Azure-ban. A tanúsítvány a védett [Azure Key Vault](/azure/key-vault/), és az App Service alkalmazásba importálható. További információkért lásd: [vásárlása és konfigurálása az Azure App Service SSL-tanúsítvány](web-sites-purchase-ssl-web-site.md).
- **Külső tanúsítvány** – egy megbízható hitelesítésszolgáltatótól vásárolt egyéni SSL-tanúsítvány feltöltése, és kösse az App Service-alkalmazást. App Service-ben egyetlen tartományból álló tanúsítványokat és a helyettesítő tanúsítványok is támogatja. Tesztelési célokra önaláírt tanúsítványokat is támogatja. További információkért lásd: [meglévő egyéni SSL-tanúsítvány kötése az Azure Web Appshez](app-service-web-tutorial-custom-ssl.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Nem biztonságos protokollt (HTTP, a TLS 1.0, FTP)

Az alkalmazását az összes titkosítatlan (HTTP) kapcsolat biztonságos, az App Service biztosítja egyetlen kattintással konfigurációját, és HTTPS kényszerítése. Nem biztonságos kérelmek azonnal ki vannak kapcsolva, mielőtt elérnék még az alkalmazás kódjában. További információkért lásd: [HTTPS kényszerítése](app-service-web-tutorial-custom-ssl.md#enforce-https).

[A TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 már nem tekinthető biztonságos szerint iparági szabványok, például [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). App Service lehetővé teszi, akkor tiltsa le az elavult protokollokat [a TLS 1.1/1.2 kényszerítése](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions).

App Service-ben FTP és a FTPS támogatja a fájlok telepítéséhez. Azonban FTPS kell helyett használja az FTP, ha lehetséges. Ha az egyik vagy mindkét ezeket a protokollokat, amelyek nem használja, akkor [őket tiltani](app-service-deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Statikus IP-korlátozások

Alapértelmezés szerint az App Service-alkalmazás fogadja az internetről érkező összes IP-cím kéréseit, de a férjenek hozzá az IP-címek részhalmazát. App Service-ben Windows férhessenek hozzá az alkalmazás IP-címek listájának definiálását teszi lehetővé. Az engedélyezett listára egyedi IP-címek hozzáadhatja, vagy egy IP-címtartomány határozzák meg egy alhálózati maszkot. További információkért lásd: [Azure App Service statikus IP-korlátozások](app-service-ip-restrictions.md).

A Windows App Service, az korlátozhatja is IP-címeket dinamikusan konfigurálásával a _web.config_. További információkért lásd: [dinamikus IP-biztonság <dynamicIpSecurity> ](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Ügyfél-hitelesítés és engedélyezés

Az Azure App Service biztosít teljes körű hitelesítése és engedélyezése a felhasználók vagy -ügyfél alkalmazásokat. Ha engedélyezve van, a felhasználók és az ügyfélalkalmazások alig vagy egyáltalán nem alkalmazás-kóddal jelentkezhetnek. Előfordulhat, hogy a saját hitelesítési és engedélyezési megoldást valósíthat meg, vagy inkább kezelésére, az App Service lehetővé teszi. A hitelesítési és engedélyezési modul webes kéréseket kezeli az alkalmazás kódjának őket leadása előtt, és a jogosulatlan kérések, megtagadja, mielőtt elérnék a kódot.

App Service-hitelesítés és -engedélyezés támogatja a több hitelesítési szolgáltatók, beleértve az Azure Active Directory, Microsoft fiókok, Facebook, Google, és a Twitter. További információkért lásd: [hitelesítése és engedélyezése Azure App Service-ben](app-service-authentication-overview.md).

## <a name="service-to-service-authentication"></a>Szolgáltatások közötti hitelesítés

A háttérszolgáltatás hitelesítésekor az App Service biztosítja a szükséges függően két különböző mechanizmus:

- **Identitás szolgáltatás** – jelentkezzen be a távoli erőforrás maga az alkalmazás identitását használja. Az App Service lehetővé teszi, hogy egyszerűen létrehozhatja egy [identitás](app-service-managed-service-identity.md), amelyet más szolgáltatásokkal, például hitelesítést használhat [Azure SQL Database](/azure/sql-database/) vagy [Azure Key Vault](/azure/key-vault/). Ez a megközelítés egy teljes körű Útmutató: [App Service-ben egy felügyelt identitás használata Azure SQL Database biztonságos kapcsolatot](app-service-web-tutorial-connect-msi.md).
- **A alapú meghatalmazásos (OBO)** – győződjön meg arról, delegált hozzáférést a távoli erőforrásokhoz a felhasználó nevében. Az Azure Active Directoryval, a hitelesítésszolgáltató, az App Service-alkalmazást hajthat végre delegált bejelentkezési egy távoli szolgáltatásnak, például [Azure Active Directory Graph API](../active-directory/develop/active-directory-graph-api.md) vagy egy távoli API-alkalmazást az App Service-ben. Ez a megközelítés egy teljes körű Útmutató: [hitelesítése és engedélyezése a felhasználók teljes körű Azure App Service-ben](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Kapcsolat a távoli erőforrásokhoz

A távoli erőforrások eléréséhez szükséges lehet, hogy a az alkalmazás három típusa van: 

- [Azure-erőforrások](#azure-resources)
- [Egy Azure virtuális hálózatban lévő erőforrások](#resources-inside-an-azure-virtual-network)
- [A helyszíni erőforrásokhoz](#on-premises-resources)

A minden egyes ezekben az esetekben App Service-ben is biztosítja, hogy biztonságos kapcsolatot, de továbbra is érdemes betartania bevált biztonsági gyakorlatokat. Például mindig használhatja titkosított kapcsolatokat, ha a háttér-erőforrás lehetővé teszi a nem titkosított kapcsolatokat. Továbbá győződjön meg arról, hogy a háttér-Azure-szolgáltatás lehetővé teszi, hogy az IP-címek csoportját. A kimenő IP-címek megtalálja az alkalmazást [bejövő és kimenő IP-címek az Azure App Service](app-service-ip-addresses.md).

### <a name="azure-resources"></a>Azure-erőforrások

Ha az alkalmazás csatlakozik Azure-erőforrások, például [SQL Database](https://azure.microsoft.com/services/sql-database/) és [Azure Storage](/azure/storage/), a kapcsolat Azure-ban marad, és nem közötti hálózati határok. Azonban a kapcsolatot az Azure-ban a megosztott hálózati áramlik, ezért mindig ellenőrizze, hogy a kapcsolat titkosítva van. 

Ha az alkalmazás üzemel egy [App Service Environment-környezet](environment/intro.md), akkor [támogatott Azure-szolgáltatások virtuális hálózati Szolgáltatásvégpontok használatával csatlakozhat](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Egy Azure virtuális hálózatban lévő erőforrások

Az alkalmazás hozzáférhessen-e az erőforrások egy [Azure Virtual Network](/azure/virtual-network/) keresztül [virtuális hálózati integráció](web-sites-integrate-with-vnet.md). Az integráció jön létre egy virtuális hálózathoz pont – hely típusú VPN-kapcsolattal. Az alkalmazás ezután hozzáférhetnek az erőforrásokat a virtuális hálózat privát IP-címeik használatával. A pont – hely kapcsolatot, azonban továbbra is járja a megosztott hálózatok az Azure-ban. 

Az erőforrás kapcsolat teljes egészében az Azure-ban a közös hálózatok elkülönítésére, létrehozni az alkalmazást egy [App Service Environment-környezet](environment/intro.md). Mivel az App Service-környezet mindig egy dedikált virtuális hálózaton üzembe, az alkalmazás és a virtuális hálózatban lévő erőforrások közötti kapcsolat elszigetelt. Az App Service Environment-környezetek hálózati biztonság más aspektusait, lásd: [Hálózatelkülönítés](#network-isolation).

### <a name="on-premises-resources"></a>A helyszíni erőforrásokhoz

Biztonságosan elérhet a helyszíni erőforrásokat, például adatbázisokat, három módon: 

- [Hibrid kapcsolatok](app-service-hybrid-connections.md) – a távoli erőforrás egy TCP-alagúton keresztül egy pont-pont típusú kapcsolatot hoz létre. A TCP-alagút létrehozása a TLS 1.2 használatával közös hozzáférésű jogosultságkód (SAS) kulcsokkal.
- [Virtuális hálózati integráció](web-sites-integrate-with-vnet.md) site-to-site VPN - leírtak [erőforrások egy Azure virtuális hálózaton belül](#resources-inside-an-azure-virtual-network), de a virtuális hálózat is kapcsolódik a helyszíni hálózaton keresztül egy [ site-to-site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). A hálózati topológia az alkalmazás csatlakozhat a helyszíni erőforrásokhoz, mint az egyéb erőforrások a virtuális hálózatban.
- [App Service Environment-környezet](environment/intro.md) site-to-site VPN - leírtak [erőforrások egy Azure virtuális hálózaton belül](#resources-inside-an-azure-virtual-network), de a virtuális hálózat is kapcsolódik a helyszíni hálózaton keresztül egy [site-to-site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). A hálózati topológia az alkalmazás csatlakozhat a helyszíni erőforrásokhoz, mint az egyéb erőforrások a virtuális hálózatban.

## <a name="application-secrets"></a>Titkos alkalmazáskulcsok

Titkos alkalmazáskulcsok, például az adatbázis hitelesítő adatait, API-jogkivonatai és titkos kulcsok ne tároljon a kódot, vagy a konfigurációs fájlokban. Az általánosan elfogadott megközelítés érheti el őket, mint a következő [környezeti változók](https://wikipedia.org/wiki/Environment_variable) tetszőleges nyelven a szokásos mintájának használatával. Az App Service-ben a környezeti változókat határozhat meg módja keresztül [Alkalmazásbeállítások](web-sites-configure.md#app-settings) (és a .NET-alkalmazásokban, különösen [kapcsolati karakterláncok](web-sites-configure.md#connection-strings)). Az alkalmazásbeállítások és a kapcsolati karakterláncok titkosítva tárolódnak az Azure-ban, és azokat csak előtt folyamatban kártevő program férkőzik be az alkalmazás folyamata memória az alkalmazás indításakor már visszafejteni. A titkosítási kulcsokat a rendszer rendszeresen elforgatva.

Azt is megteheti, integrálható az App Service-alkalmazás az [Azure Key Vault](/azure/key-vault/) speciális titkos kulcsok kezeléséhez. Által [felügyelt identitással a Key Vault elérése](../key-vault/tutorial-web-application-keyvault.md), az App Service-alkalmazást biztonságosan férhet hozzá a titkos kulcsok van szüksége.

## <a name="network-isolation"></a>A hálózatok elkülönítéséhez

Az alábbiakat kivéve a **elkülönített** tarifacsomag, minden szinten az alkalmazások futtatása az App Service-ben a megosztott hálózati infrastruktúrán. Ha például a nyilvános IP-címek és terheléselosztók előtérbeli más bérlők felhasználóival vannak megosztva. A **elkülönített** szint biztosít teljes körű hálózatelkülönítés az alkalmazásokat egy dedikált futtatásával [App Service Environment-környezet](environment/intro.md). App Service-környezet fut, a saját példányát [Azure Virtual Network](/azure/virtual-network/). Lehetővé teszi, hogy: 

- A hálózati hozzáférés korlátozása [hálózati biztonsági csoportok](../virtual-network/virtual-networks-dmz-nsg.md). 
- Dedikált előtér-szolgálja ki a segítségével egy dedikált nyilvános végpontot.
- Belső alkalmazás használatával egy belső terheléselosztó (ILB), amely lehetővé teszi a hozzáférés csak az Azure virtuális hálózaton belül szolgálnak. Az ILB a privát alhálózatra, így az alkalmazások teljes elkülönítés az internetről származó IP-címmel rendelkezik.
- [Használja az ILB mögötti webalkalmazási tűzfal (WAF)](environment/integrate-with-application-gateway.md). A WAF a nyilvánosan elérhető alkalmazások, például DDoS elleni védelem, URI szűrési és SQL-injektálás megelőzési nagyvállalati szintű védelmet biztosít.

További információkért lásd: [Introduction to Azure App Service Environment-környezetek](environment/intro.md). 
