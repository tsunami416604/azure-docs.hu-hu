---
title: Az Azure Stack adatközpont integrációja – végpontok közzététele |} A Microsoft Docs
description: A helyi adatközpontban Azure Stack-végpontok közzététele
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 12/06/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 12/06/2018
keywords: ''
ms.openlocfilehash: 5946f62821d05bd9036b9fc0e6b0fc8daa74c5dc
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241202"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Az Azure Stack adatközpont integrációja – végpontok közzététele

Az Azure Stack állít be az infrastruktúra-szerepkör virtuális IP-címek (VIP). A virtuális IP-címek vannak lefoglalva a nyilvános IP-címkészletből. Minden egyes virtuális IP-CÍMEK hozzáférés-vezérlési lista (ACL) a szoftveresen definiált hálózati rétegben védi. Hozzáférés-vezérlési listák is használhatók a fizikai kapcsolón (szűrése és BMC) tovább erősíti a megoldást. DNS-bejegyzés jön létre a külső DNS-zónában, hogy az üzembe helyezéskor meghatározott végpontot.


A következő architekturális diagram látható a különböző hálózati rétegek és ACL-ek:

![Szerkezeti kép](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Portok és protokollok (bejövő)

Infrastruktúra készlete virtuális IP-cím megadása kötelező közzététele az Azure Stack-végpontok külső hálózatokhoz. A *végpontot (VIP)* táblázat minden egyes végpontot, a szükséges port és protokoll. Tekintse meg az adott erőforrás szolgáltató telepítési dokumentációban további erőforrás-szolgáltatók, például az SQL erőforrás-szolgáltató igénylő végpontok.

Virtuális IP-címek nem jelennek meg, mert azok még nem szükséges a közzététel az Azure Stack belső infrastruktúra.

> [!Note]  
> Felhasználói virtuális IP-címek dinamikusak, maguk a felhasználók számára nem az Azure Stack operátorait szerint határozza meg.

|Endpoint (VIP)|DNS host A record|Protokoll|Portok|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portál (rendszergazda)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Az Azure Resource Manager (rendszergazda)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Portál (felhasználó)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Az Azure Resource Manager (felhasználó)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Visszavont tanúsítványok listája|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|A TCP ÉS UDP|53|
|Üzemeltetés | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|A Key Vault (felhasználó)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|A Key Vault (rendszergazda)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Tárolási üzenetsor|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage-táblából|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL típusú erőforrás-szolgáltató|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL erőforrás-szolgáltató|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172-es (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (az azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|VPN-átjárók|     |     |[Tekintse meg a VPN gateway – gyakori kérdések](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Portok és URL-címek (kimenő)

Az Azure Stack csak transzparens proxy kiszolgálók támogatja. A központi telepítés, ha egy transzparens proxy kimenő portokhoz hagyományos proxykiszolgálónak engedélyeznie kell a következő portokat és URL-címek a kimenő kommunikáció:

> [!Note]  
> Az Azure Stack nem támogatja az Express Route használatával az alábbi táblázatban felsorolt Azure-szolgáltatások eléréséhez.

|Cél|URL-cím|Protokoll|Portok|
|---------|---------|---------|---------|
|Identitás|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com<br>office.com|HTTP<br>HTTPS|80<br>443|
|Marketplace-en szindikálás|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Javítás & frissítése|https://&#42;.azureedge.net|HTTPS|443|
|Regisztráció|https://management.azure.com|HTTPS|443|
|Használat|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net|HTTPS|443|
|Windows Defender|.wdcp.microsoft.com<br>.wdcpalt.microsoft.com<br>*. updates.microsoft.com<br>*. jövőben a Microsoft<br>https://msdl.microsoft.com/download/symbols<br>`https://www.microsoft.com/pkiops/crl`<br>`https://www.microsoft.com/pkiops/certs`<br>`https://crl.microsoft.com/pki/crl/products`<br>`https://www.microsoft.com/pki/certs`<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|
|NTP|(IP az NTP-kiszolgáló a megadott központi telepítés)|UDP|123|
|DNS|(IP-megadott DNS-kiszolgáló üzembe helyezéshez)|TCP<br>UDP|53|
|CRL|(A tanúsítvány CRL terjesztési pontok alapján URL)|HTTP|80|
|Infrastruktúra biztonsági mentése|(IP vagy FQDN-jének külső cél fájlkiszolgálón)|SMB|445|
|     |     |     |     |

> [!Note]  
> Kimenő URL-címek az Azure traffic Managerrel a földrajzi hely alapján a legjobb lehetséges csatlakozási elosztott terhelésű rendszer. Az elosztott terhelésű URL-címek betöltése, a Microsoft update, és a háttéralkalmazás végpontjainak módosítása ügyfelek befolyásolása nélkül. A Microsoft nem oszt meg IP-címek listája az elosztott terhelésű URL-címek számára. Használjon olyan eszköz, amely támogatja a szűrést, URL-cím helyett IP-cím alapján.

> [!Note]  
> A 1809 a biztonsági mentési infrastruktúra-szolgáltatás kommunikál a külső fájlkiszolgáló a nyilvános VIP hálózatra. Mielőtt 1809 a szolgáltatás továbbítani a nyilvános infrastruktúra-hálózaton keresztül. Ha a környezet nem engedélyezi a hozzáférést az infrastruktúra-erőforrások a nyilvános VIP hálózatra, a alkalmazni a legújabb [1809 gyorsjavítás](azure-stack-update-1809.md#post-update-steps) az Azure Stackhez. Ez a gyorsjavítás áthelyezi az nyilvános infrastruktúra-hálózaton a biztonsági mentési infrastruktúra-szolgáltatás. A 1811 Ha gyorsjavítást a 1809, a biztonsági mentési infrastruktúra-szolgáltatás marad a nyilvános infrastruktúra-hálózaton. A gyorsjavítás csak akkor érvényesíthetők, ha a frissítés Visszalépés a szolgáltatás a nyilvános infrastruktúra-hálózathoz.

## <a name="next-steps"></a>További lépések

[Az Azure Stack nyilvános kulcsokra épülő infrastruktúra követelményei](azure-stack-pki-certs.md)
