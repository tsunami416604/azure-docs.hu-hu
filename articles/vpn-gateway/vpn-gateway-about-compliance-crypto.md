---
title: Kriptográfiai követelményeiről és az Azure VPN gatewayek |} Microsoft Docs
description: Ez a cikk ismerteti a titkosítási követelményeket és az Azure VPN gatewayek
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
ms.openlocfilehash: d2f3da47f1d4eebe1b81964790ff6612dd78155d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Kriptográfiai követelményeiről és az Azure VPN gatewayek

A cikk ismerteti, hogyan konfigurálhatja az Azure VPN gatewayek a kriptográfiai megfelelnek a létesítmények közötti S2S VPN-alagutat, mind az Azure VNet – VNet kapcsolatokhoz. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Az Azure VPN gatewayek IPsec és az internetes KULCSCSERE házirend paraméterek
Standard IPsec és IKE protokoll titkosítási algoritmusok számos különböző kombinációkban támogatja. Ha az ügyfelek nem kérő titkosítási algoritmusok és a paraméterek megadott kombinációja, az Azure VPN gatewayek alapértelmezett javaslatokat készletének használata. Az alapértelmezett házirend beállítása azokat a külső VPN-eszközök alapértelmezett beállításokkal való együttműködés maximalizálása volt kiválasztva. Ennek eredményeképpen a szabályzatok és javaslatok számát nem tér ki az összes lehetséges kombinációjának elérhető titkosítási algoritmusok és a kulcs szintjeiről.

Az alapértelmezett házirend beállítása az Azure VPN-átjáró, a dokumentum szerepel: [kapcsolatos VPN-eszközök és webhelyek közötti VPN átjáró kapcsolatok IPsec/IKE paramétereinek](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Titkosítási követelmények
A titkosítási algoritmusokat vagy paraméterek igénylő kommunikációhoz általában megfelelőségi és biztonsági követelmények miatt az ügyfelek segítségével mostantól beállíthatja az Azure VPN gatewayek egy egyéni IPsec/IKE házirend használatához az adott kriptográfiai algoritmusok és a kulcs szintjeiről helyett az Azure alapértelmezett házirend beállítása.

Például az Azure VPN gatewayek IKEv2 alapmódú házirendeket használják-e csak Diffie-Hellman csoport 2 (1024 bit), mivel az ügyfelek internetes KULCSCSERE, például csoport 14 (2048 bites), a csoport 24 (2048 bites MODP csoport) vagy a ECP (elliptikus használandó erősebb csoportok megadására is szükség görbe csoportok) 384 vagy 256 bit (csoportos 19 csoport 20, illetve). Hasonló követelmények gyorsmódú házirendeket is érvényesek.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Az Azure VPN gatewayek egyéni IPsec/IKE-házirend
Az Azure VPN gatewayek mostantól támogatják az kapcsolatonként, egyéni IPsec/IKE-házirendet. Hely-hely vagy VNet – VNet-kapcsolatot választhat titkosítási algoritmusok egyedi kombinációja IPsec és az internetes KULCSCSERE a kívánt kulcs erősségét és a következő példában látható módon:

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

IPsec/IKE-házirend létrehozása, és egy új vagy meglévő kapcsolat alkalmazható. 

### <a name="workflow"></a>Munkafolyamat

1. A virtuális hálózatok, VPN-átjárók vagy a kapcsolat topológia más útmutatókat a helyi hálózati átjáró létrehozása
2. IPsec/IKE-házirend létrehozása
3. Alkalmazhatja a szabályzatot, amikor egy S2S vagy VNet – VNet-kapcsolatot hoz létre
4. Ha a kapcsolat már létrejött, alkalmazni, vagy egy létező kapcsolatra a házirend frissítése


## <a name="ipsecike-policy-faq"></a>IPsec/IKE szabályzata – GYIK

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="next-steps"></a>További lépések
Lásd: [konfigurálása IPsec/IKE házirend](vpn-gateway-ipsecikepolicy-rm-powershell.md) lépésenkénti egyéni IPsec/IKE-házirend konfigurálása a kapcsolatot.

Lásd még: [csatlakozás több csoportházirend-alapú VPN-eszközök](vpn-gateway-connect-multiple-policybased-rm-ps.md) további információt a UsePolicyBasedTrafficSelectors lehetőséget.