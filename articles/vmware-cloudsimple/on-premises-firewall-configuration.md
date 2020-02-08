---
title: Azure VMware-megoldások (AVS) elérése a helyszínen
description: Azure VMware-megoldások (AVS) elérése a helyszíni hálózatról tűzfalon keresztül
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a4a9760b5c7a70c58a1afe1b14b781a35f2b9b18
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77082968"
---
# <a name="accessing-your-avs-private-cloud-environment-and-applications-from-on-premises"></a>Az AVS Private Cloud Environment és a helyszíni alkalmazások elérése

A helyszíni hálózatról az Azure-ExpressRoute vagy a helyek közötti VPN-kapcsolaton keresztüli csatlakozást lehet beállítani. A kapcsolat használatával elérheti az AVS Private Cloud vCenter és az AVS Private-felhőben futtatott munkaterheléseket. Megadhatja, hogy milyen portok legyenek megnyitva a kapcsolatban a helyszíni hálózat tűzfala használatával. Ez a cikk a tipikus alkalmazások portokra vonatkozó követelményeit tárgyalja. Bármely más alkalmazás esetében tekintse meg az alkalmazás dokumentációjában a portokra vonatkozó követelményeket.

## <a name="ports-required-for-accessing-vcenter"></a>A vCenter eléréséhez szükséges portok

Az AVS Private Cloud vCenter és a NSX-T Manager eléréséhez az alábbi táblázatban definiált portokat meg kell nyitni a helyszíni tűzfalon. 

| Port       | Forrás                           | Cél                      | Cél                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Helyszíni DNS-kiszolgálók          | AVS Private Cloud DNS-kiszolgálók        | A *az.CLOUDSIMPLE.IO* DNS-keresésének továbbításához szükséges a helyszíni hálózatról származó AVS Private Cloud DNS-kiszolgálókra.     |
| 53 (UDP)   | AVS Private Cloud DNS-kiszolgálók        | Helyszíni DNS-kiszolgálók          | A DNS továbbításához szükséges a helyszíni tartománynevek megkeresése az AVS Private Cloud vCenter a helyszíni DNS-kiszolgálókra. |
| 80 (TCP)   | Helyszíni hálózat              | AVS Private Cloud Management Network | A vCenter URL-címének *http* -ről *https*-re való átirányításához szükséges.                                                         |
| 443 (TCP)  | Helyszíni hálózat              | AVS Private Cloud Management Network | A vCenter és a NSX-T kezelő helyszíni hálózatról való eléréséhez szükséges.                                           |
| 8000 (TCP) | Helyszíni hálózat              | AVS Private Cloud Management Network | A helyszíni rendszerből az AVS Private Cloud-ba irányuló virtuális gépek vMotion szükséges.                                          |
| 8000 (TCP) | AVS Private Cloud Management Network | Helyszíni hálózat              | A virtuális gépeknek az AVS privát felhőből a helyszíni rendszerbe való vMotion szükséges.                                          |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>A helyszíni Active Directory identitás forrásaként való használatához szükséges portok

Ha a helyszíni Active Directoryt identitás forrásaként szeretné konfigurálni az AVS Private Cloud vCenter, meg kell nyitni a táblázatban definiált portokat. Lásd: az [Azure ad használata identitás-szolgáltatóként a vCenter az AVS AVS-beli privát felhőben](https://docs.azure.cloudsimple.com/azure-ad/) a konfigurációs lépésekhez.

| Port         | Forrás                           | Cél                                         | Cél                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | AVS Private Cloud DNS-kiszolgálók        | Helyszíni DNS-kiszolgálók                             | A DNS továbbítása a helyszíni Active Directory-tartománynevek az AVS Private Cloud vCenter a helyszíni DNS-kiszolgálókra való megkereséséhez szükséges.        |
| 389 (TCP/UDP) | AVS Private Cloud Management Network | Helyszíni Active Directory-tartományvezérlők     | Az AVS Private Cloud vCenter-kiszolgáló és az Active Directory-tartományvezérlők közötti LDAP-kommunikációhoz szükséges a felhasználói hitelesítéshez.              |
| 636 (TCP)     | AVS Private Cloud Management Network | Helyszíni Active Directory-tartományvezérlők     | A biztonságos LDAP (LDAPs) kommunikációhoz szükséges az AVS Private Cloud vCenter-kiszolgálóról az Active Directory-tartományvezérlőkre a felhasználói hitelesítéshez. |
| 3268 (TCP)    | AVS Private Cloud Management Network | Helyszíni Active Directory globáliskatalógus-kiszolgálók | Több tartományvezérlős üzemelő példányokban történő LDAP-kommunikációhoz szükséges.                                                                      |
| 3269 (TCP)    | AVS Private Cloud Management Network | Helyszíni Active Directory globáliskatalógus-kiszolgálók | Több tartományvezérlős üzemelő példányokban történő LDAP-kommunikációhoz szükséges.                                                                     |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>A számítási feladatok virtuális gépei eléréséhez szükséges általános portok

Az AVS Private Cloud-on futó munkaterhelési virtuális gépek eléréséhez portok szükségesek a helyszíni tűzfalon való megnyitásához. Az alábbi táblázat a szükséges általános portok némelyikét és azok célját mutatja be. Az alkalmazás-specifikus portokra vonatkozó követelményekért tekintse meg az alkalmazás dokumentációját.

| Port         | Forrás                         | Cél                          | Cél                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Helyszíni hálózat            | AVS Private Cloud munkaterhelés-hálózat       | Biztonságos rendszerhéj-hozzáférés az AVS Private Cloud-on futó Linux rendszerű virtuális gépekhez.            |
| 3389 (TCP)    | Helyszíni hálózat            | AVS Private Cloud munkaterhelés-hálózat       | A Távoli asztalról az AVS Private Cloud-on futó Windows rendszerű virtuális gépekre.               |
| 80 (TCP)      | Helyszíni hálózat            | AVS Private Cloud munkaterhelés-hálózat       | Hozzáférés az AVS Private Cloud-on futó virtuális gépeken üzembe helyezett webkiszolgálókhoz.      |
| 443 (TCP)     | Helyszíni hálózat            | AVS Private Cloud munkaterhelés-hálózat       | Hozzáférés az AVS Private Cloud-on futó virtuális gépeken üzembe helyezett biztonságos webkiszolgálókhoz. |
| 389 (TCP/UDP) | AVS Private Cloud munkaterhelés-hálózat | Helyszíni Active Directory-hálózat | Csatlakoztassa a Windows munkaterhelés virtuális gépeket a helyszíni Active Directory-tartományhoz.     |
| 53 (UDP)      | AVS Private Cloud munkaterhelés-hálózat | Helyszíni hálózat                  | DNS-szolgáltatás elérése a számítási feladatok virtuális gépei számára a helyszíni DNS-kiszolgálókra.       |

## <a name="next-steps"></a>Következő lépések

* [VLAN-ok és alhálózatok létrehozása és kezelése](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Kapcsolódás helyszíni hálózathoz az Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Helyek közötti VPN beállítása a helyszíni környezetből](https://docs.azure.cloudsimple.com/vpn-gateway/)
