---
title: Tudnivalók a titkosítási követelményeket és az Azure VPN-átjárók |} A Microsoft Docs
description: Ez a cikk ismerteti a titkosítási követelményeket és az Azure VPN Gateway átjárók
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2017
ms.author: yushwang
ms.openlocfilehash: 060e647badcc3bad7b44d7cef3530c36b8ecdf57
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "23884250"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Tudnivalók a titkosítási követelményeket és az Azure VPN Gateway átjárók

Ez a cikk ismerteti, hogyan konfigurálhatja a titkosítási megfelelnek a létesítmények közötti S2S VPN-alagutat, mind az Azure-on belüli virtuális hálózatok közötti kapcsolatok az Azure VPN Gateway-átjárókhoz. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Azure VPN gatewayek IPsec és az IKE szabályzat paraméterekről
Szabványos IPsec és az IKE protokoll különböző kombinációival titkosítási algoritmusok széles skáláját támogatja. Titkosítási algoritmusokkal és paraméterekkel, adott kombinációk nem kérő ügyfelek, ha az Azure VPN-átjárók használ egy alapértelmezett javaslatokat. Az alapértelmezett házirendjét számos külső VPN-eszközök alapértelmezett beállításokkal való együttműködés maximalizálása érdekében lettek kiválasztva. Ennek eredményeképpen a szabályzatok és javaslatok számát nem fedhetik le az összes lehetséges kombinációjának elérhető titkosítási algoritmusokat és kulcserősségeket.

Az alapértelmezett szabályzat beállítása az Azure VPN gateway a dokumentum szerepel: [Tudnivalók a VPN-eszközök és IPsec/IKE-paramétereinek Site-to-Site VPN Gateway-kapcsolatok](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Kriptográfiai követelményekről
A megadott titkosítási algoritmusok vagy paraméterek igénylő kommunikációhoz általában megfelelőségi vagy biztonsági követelményekkel, ügyfelek most már konfigurálhatják specifikus titkosítási egy egyéni IPsec/IKE-házirend használata az Azure VPN Gateway átjárók algoritmusokkal és kulcserősségekkel, nem pedig az Azure alapértelmezett házirendjét.

Ha például az IKEv2 fő módú szabályzatok Azure VPN gatewayek használják-e csak Diffie-Hellman csoport 2 (1024 bit), mivel előfordulhat, hogy kell IKE, például a csoport 14 (2048-bites), a csoport 24 (2048-bites MODP csoport) vagy a ECP (elliptikus használandó erősebb csoportok megadása görbe csoportok) 256 vagy 384 bites (19 és 20 csoport-csoport jelölik). IPSec-gyorsmódú házirendeket is hasonló követelmények érvényesek.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Egyéni IPsec/IKE-házirendet az Azure VPN-átjárókkal
Az Azure VPN-átjárók mostantól támogatják a kapcsolatok, egyéni IPsec/IKE-házirendet. Helyek közötti és VNet – VNet kapcsolat esetében választhatja titkosítási algoritmusok, adott kombinációk IPsec és az IKE a kívánt kulcs erősségét, az az alábbi példában látható módon:

![IPSec-ike-szabályzat](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Hozzon létre egy IPsec/IKE-házirendet, és a egy új vagy meglévő kapcsolatot a alkalmazni. 

### <a name="workflow"></a>Munkafolyamat

1. A virtuális hálózatok, a VPN-átjárókhoz vagy a helyi hálózati átjárók más útmutató dokumentumok leírtak szerint a kapcsolat-topológia létrehozása
2. Egy IPsec/IKE-szabályzat létrehozása
3. A szabályzatot alkalmazhat egy S2S vagy VNet – VNet kapcsolat létrehozásakor
4. Ha a kapcsolat már létrejött, alkalmazhat, vagy egy létező kapcsolatot, a házirend módosítása


## <a name="ipsecike-policy-faq"></a>IPsec/IKE-házirend – gyakori kérdések

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="next-steps"></a>További lépések
Lásd: [konfigurálja az IPsec/IKE-házirend](vpn-gateway-ipsecikepolicy-rm-powershell.md) kapcsolatos lépésenkénti útmutatót a kapcsolat egyéni IPsec/IKE-szabályzat konfigurálása.

Lásd még: [több házirendalapú VPN-eszköz csatlakoztatása](vpn-gateway-connect-multiple-policybased-rm-ps.md) további információ a UsePolicyBasedTrafficSelectors lehetőséget.