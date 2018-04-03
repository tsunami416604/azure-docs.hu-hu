---
title: Azure datacenter integráció a verem - végpontok közzététele
description: 'Útmutató: Azure verem végpontok közzététele az adatközpontban található'
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: ''
ms.openlocfilehash: 136d78be3cddfd6fd4e491d5ea3f5d51d0dc611f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure datacenter integráció a verem - végpontok közzététele
Az Azure verem hoz létre az infrastruktúra-szerepkör virtuális IP-címek (VIP). A virtuális IP-címek a nyilvános IP-címkészletből foglal le. A szoftver által meghatározott hálózati rétegben hozzáférés-vezérlési listaként (ACL) minden egyes VIP védi. Hozzáférés-vezérlési listákat a megoldás további támogatnia kell a is használhatók a fizikai kapcsolók (TORs és bmc-n) keresztül. A DNS-bejegyzés jön létre a külső DNS-zónában, a központi telepítéskor megadott végpontok.


A következő architekturális ábrán az látható, a különböző hálózati rétegek és a hozzáférés-vezérlési listák:

![Szerkezeti kép](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Portok és protokollok (bejövő)

Infrastruktúra készlete virtuális IP-címek szükségesek a külső hálózatokat közzétételi Azure verem végpontok. A *végpont (VIP)* táblázat minden egyes végpont, a szükséges portot és protokollt. Tekintse meg a további erőforrás-szolgáltatók, köztük az SQL erőforrás-szolgáltató igénylő végpontok az adott erőforrás szolgáltató telepítési dokumentációját.

Virtuális IP-címek nem jelennek meg, mert nem Azure közzétételi verem szükséges belső infrastruktúra.

> [!NOTE]
> Felhasználói VIP általában dinamikusan változnak maguk a felhasználók számára nem az Azure-verem üzemeltető határozzák meg.


|A végpont (VIP)|DNS host A record|Protokoll|Portok|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (rendszergazda)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|Az Azure Resource Manager (rendszergazda)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Portal (felhasználó)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Az Azure Resource Manager (felhasználó)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Tanúsítvány-visszavonási listát|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP ÉS UDP|53|
|Key Vault (felhasználó)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault (rendszergazda)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Tárolási üzenetsor|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tárolási tábla|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL-erőforrás-szolgáltató|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL-erőforrás-szolgáltató|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172-es (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (az azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-101000 (FTP)<br>990 (FTPS)|

## <a name="ports-and-urls-outbound"></a>Portok és URL-címek (kimenő)

Az Azure verem csak a transzparens proxy kiszolgálókat támogatja. Egy központi telepítésben lévő Amennyiben egy transzparens proxy kimenő kapcsolatot biztosítani a hagyományos proxykiszolgálót, engedélyeznie kell a következő portokat és URL-címek kimenő kommunikációra:


|Cél|URL-cím|Protokoll|Portok|
|---------|---------|---------|---------|
|Identitás|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net|HTTP<br>HTTPS|80<br>443|
|Piactér szindikálási|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Javítás & frissítése|https://&#42;.azureedge.net|HTTPS|443|
|Regisztráció|https://management.azure.com|HTTPS|443|
|Használat|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|


## <a name="next-steps"></a>További lépések

[Azure verem PKI-követelmények](azure-stack-pki-certs.md)