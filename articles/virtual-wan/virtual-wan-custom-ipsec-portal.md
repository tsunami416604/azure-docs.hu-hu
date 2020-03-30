---
title: 'Egyéni IPsec-házirend konfigurálása az Azure Virtual WAN: Portal hoz | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyéni IPsec-szabályzatot az Azure Virtual WAN-hoz a portál használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515745"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Egyéni IPsec-házirend konfigurálása a virtuális wan-hoz a portál használatával

Az Azure Portalon konfigurálhatja a Virtual WAN egyéni IPsec-szabályzatát. Az egyéni szabályzatok akkor hasznosak, ha azt szeretné, hogy mindkét oldal (a helyszíni és az Azure VPN-átjáró) ugyanazokat a beállításokat használja az IKE 1.

## <a name="working-with-custom-policies"></a>Egyéni házirendek használata

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Házirend konfigurálása

1. **Keresse meg a virtuális központot.** Egy böngészőből lépjen az [Azure Portalra](https://aka.ms/azurevirtualwanpreviewfeatures), majd jelentkezzen be az Azure-fiókjával. Keresse meg a webhely virtuális központját.
2. **Válassza ki a VPN-helyet**. A központi lapon válassza ki azt a VPN-helyet, amelyhez egyéni házirendet szeretne beállítani.

   ![Válassza ki](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **A VPN-kapcsolat szerkesztése**. A **Helyi menüben** **...** válassza **a VPN-kapcsolat szerkesztése parancsot.**

   ![szerkesztés](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Konfigurálja a beállításokat**. A **VPN-kapcsolat szerkesztése** lapon adja meg a beállításokat. A beállítások mentéséhez válassza a **Mentés** gombot.

   ![konfigurálás és mentés](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.