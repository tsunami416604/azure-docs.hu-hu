---
title: 'Egyéni IPsec-házirend konfigurálása az Azure Virtual WAN-hoz: portál | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat Egyéni IPsec-házirendet az Azure Virtual WAN-hoz a portál használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73515745"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Egyéni IPsec-házirend konfigurálása a virtuális WAN-hoz a portál használatával

A Azure Portalban Egyéni IPsec-házirendet konfigurálhat a virtuális WAN-hoz. Az egyéni szabályzatok akkor hasznosak, ha azt szeretné, hogy mindkét oldal (a helyszíni és az Azure VPN Gateway) ugyanazokat a beállításokat használja az IKE 1. és a 2. fázisához.

## <a name="working-with-custom-policies"></a>Egyéni szabályzatok használata

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Házirend konfigurálása

1. **Keresse meg a virtuális hubot**. Egy böngészőből lépjen az [Azure Portalra](https://aka.ms/azurevirtualwanpreviewfeatures), majd jelentkezzen be az Azure-fiókjával. Keresse meg a helyhez tartozó virtuális hubot.
2. **Válassza ki a VPN-helyet**. A központ lapon válassza ki azt a VPN-helyet, amelyhez egyéni szabályzatot kíván beállítani.

   ![Válassza](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **A VPN-kapcsolat szerkesztése**. A **helyi menüben** válassza a **VPN-kapcsolat szerkesztése** **lehetőséget.**

   ![szerkesztés](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Konfigurálja a beállításokat**. A **VPN-kapcsolat szerkesztése** lapon konfigurálja a beállításokat a beállítások között. A beállítások mentéséhez kattintson a **Mentés** gombra.

   ![konfigurálás és mentés](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.