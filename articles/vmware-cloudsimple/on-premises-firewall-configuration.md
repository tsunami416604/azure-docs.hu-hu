---
title: Access Azure VMware-megoldás a CloudSimple által a helyszínen
titleSuffix: Azure VMware Solution by CloudSimple
description: Az Azure VMware-megoldás elérése a CloudSimple által a helyszíni hálózatról tűzfalon keresztül
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df4c51953c6f50e30ba61b993cdb35856fcb8e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77082968"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>A CloudSimple privát felhőkörnyezet és alkalmazások elérése a helyszíni környezetből

A kapcsolat a helyszíni hálózatról a CloudSimple-re állítható az Azure ExpressRoute vagy a helyek közötti VPN használatával.  A CloudSimple Private Cloud vCenter és a magánfelhőn a kapcsolat használatával futtatott munkaterhelések elérése.  A helyszíni hálózat tűzfala segítségével szabályozhatja, hogy milyen portok nyílnak meg a kapcsolaton.  Ez a cikk ismerteti a tipikus alkalmazásport-követelmények.  Minden más alkalmazás, olvassa el az alkalmazás dokumentációját a port követelmények.

## <a name="ports-required-for-accessing-vcenter"></a>A vCenter eléréséhez szükséges portok

A Private Cloud vCenter és az NSX-T-kezelő eléréséhez az alábbi táblázatban meghatározott portokat meg kell nyitni a helyszíni tűzfalon.  

| Port       | Forrás                           | Cél                      | Cél                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Helyszíni DNS-kiszolgálók          | Magánfelhő DNS-kiszolgálói        | A *az.cloudsimple.io* DNS-keresési oldalának a helyszíni hálózatról a magánfelhő-DNS-kiszolgálóira történő továbbításához szükséges.       |
| 53 (UDP)   | Magánfelhő DNS-kiszolgálói        | Helyszíni DNS-kiszolgálók          | A HELYSZÍNI tartománynevek helyszíni tartománynevek továbbításához szükséges a Private Cloud vCenterből a helyszíni DNS-kiszolgálókra. |
| 80 (TCP)   | Helyszíni hálózat              | Privát felhőfelügyeleti hálózat | A vCenter URL-címének *http-ről https-re* történő átirányításához szükséges. *https*                                                           |
| 443 (TCP)  | Helyszíni hálózat              | Privát felhőfelügyeleti hálózat | A vCenter és az NSX-T-kezelő helyszíni hálózatról való eléréséhez szükséges.                                             |
| 8000 (TCP) | Helyszíni hálózat              | Privát felhőfelügyeleti hálózat | Virtuális gépek vMotion-jéhez szükséges a helyszíni és a privát felhőbeli.                                            |
| 8000 (TCP) | Privát felhőfelügyeleti hálózat | Helyszíni hálózat              | Virtuális gépek vMotion-jéhez szükséges a magánfelhőtől a helyszíni gényhelyekig.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>A helyszíni active directory identitásforrásként való használatához szükséges portok

A helyszíni active directory identitásforrásként való konfigurálásához a Private Cloud vCenter szolgáltatásban a táblában definiált portokat meg kell nyitni.  A konfigurációs lépésekért tekintse [meg az Azure AD használata identitásszolgáltatóként a felhőbeli magánfelhőben lévő vCentert.](https://docs.azure.cloudsimple.com/azure-ad/)

| Port         | Forrás                           | Cél                                         | Cél                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Magánfelhő DNS-kiszolgálói        | Helyszíni DNS-kiszolgálók                             | A HELYSZÍNI Active Directory tartománynevek helyszíni aktív címtártartomány-nevek továbbításához szükséges a Magánfelhő vCenterből a helyszíni DNS-kiszolgálókra.          |
| 389 (TCP/UDP) | Privát felhőfelügyeleti hálózat | Helyszíni active directory tartományvezérlők     | A Private Cloud vCenter-kiszolgálóés az active directory tartományvezérlők közötti LDAP-kommunikációhoz szükséges a felhasználói hitelesítéshez.                |
| 636 (TCP)     | Privát felhőfelügyeleti hálózat | Helyszíni active directory tartományvezérlők     | A magánfelhő-vCenter-kiszolgálóés az active directory tartományvezérlők közötti biztonságos LDAP-kommunikációhoz szükséges a felhasználói hitelesítéshez. |
| 3268 (TCP)    | Privát felhőfelügyeleti hálózat | Helyszíni active directory globáliskatalógus-kiszolgálók | Az LDAP-kommunikációhoz szükséges a többtartományvezérlős központi telepítésekben.                                                                        |
| 3269 (TCP)    | Privát felhőfelügyeleti hálózat | Helyszíni active directory globáliskatalógus-kiszolgálók | Az LDAPS-kommunikációhoz szükséges a többtartományvezérlős központi telepítésekben.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>A számítási feladatok virtuális gépei eléréséhez szükséges közös portok

A magánfelhőn futó hozzáférési számítási feladatok virtuális gépei használatához portokat kell megnyitni a helyszíni tűzfalon.  Az alábbi táblázat a szükséges közös portok némelyikét és céljukat mutatja be.  Az alkalmazásspecifikus portkövetelményekről az alkalmazás dokumentációjában olvashat.

| Port         | Forrás                         | Cél                          | Cél                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Helyszíni hálózat            | Privát felhőbeli számítási feladatok hálózata       | Biztonságos shell hozzáférést a Linux virtuális gépek fut a private cloud.              |
| 3389 (TCP)    | Helyszíni hálózat            | Privát felhőbeli számítási feladatok hálózata       | Távoli asztal a magánfelhőn futó virtuális gépekhez.                 |
| 80 (TCP)      | Helyszíni hálózat            | Privát felhőbeli számítási feladatok hálózata       | A magánfelhőn futó virtuális gépeken telepített webkiszolgálók elérése.        |
| 443 (TCP)     | Helyszíni hálózat            | Privát felhőbeli számítási feladatok hálózata       | A magánfelhőn futó virtuális gépeken telepített biztonságos webkiszolgálók elérése. |
| 389 (TCP/UDP) | Privát felhőbeli számítási feladatok hálózata | Helyszíni active directory hálózat | Csatlakozzon a Windows számítási feladatok virtuális gépeihez a helyszíni active directory tartományhoz.       |
| 53 (UDP)      | Privát felhőbeli számítási feladatok hálózata | Helyszíni hálózat                  | DNS-szolgáltatás-hozzáférés a számítási feladatok virtuális gépeihez a helyszíni DNS-kiszolgálókhoz.         |

## <a name="next-steps"></a>További lépések

* [VLAN-ok és alhálózatok létrehozása és kezelése](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Csatlakozás helyszíni hálózathoz az Azure ExpressRoute használatával](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Helyek közötti VPN beállítása a helyszínen](https://docs.azure.cloudsimple.com/vpn-gateway/)
