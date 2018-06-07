---
title: 'Az Azure Active Directory tartományi szolgáltatások: Első lépések |} Microsoft Docs'
description: Engedélyezze az Azure Active Directory tartományi szolgáltatások az Azure portál használatával
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: maheshu
ms.openlocfilehash: 607ca1900200f3b163d69a7aa4cb700b842f9334
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34586831"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Engedélyezze az Azure Active Directory tartományi szolgáltatások az Azure portál használatával


## <a name="before-you-begin"></a>Előkészületek
Tekintse át a [Hálózati megfontolások az Azure Active Directory Domain Services-hez](active-directory-ds-networking.md) című dokumentumot.


## <a name="task-2-configure-network-settings"></a>2. feladat: a hálózati beállítások konfigurálása
A következő konfigurációs feladat, ha egy Azure virtuális hálózatra és egy dedikált alhálózaton belül. Engedélyezze az Azure Active Directory Domain Services-t a virtuális hálózatának ezen az alhálózatán. Is válasszon egy meglévő virtuális hálózathoz, és hozza létre az alhálózatot, amelyek dedikált belül.

1. Kattintson a **virtuális hálózati** virtuális hálózat kiválasztásához.
    > [!NOTE]
    > **Klasszikus virtuális hálózatok nem támogatottak az új központi telepítéseknél.** Klasszikus virtuális hálózatok nem támogatottak az új központi telepítéseknél. Klasszikus virtuális hálózatokat a rendszerbe meglévő felügyelt tartományok továbbra is használhatók. A Microsoft lehetővé teszi a telepítenek át egy meglévő felügyelt tartomány a klasszikus virtuális hálózatot egy erőforrás-kezelő virtuális hálózat a közeljövőben.
    >

2. Az a **válasszon virtuális hálózati** lapon megjelenik az összes meglévő virtuális hálózatot. Csak a virtuális hálózatok, amelyek a kijelölt erőforráscsoport és Azure-beli hely megjelenik a **alapjai** varázsló lapja.
3. Válassza ki a virtuális hálózatot, amelyben a Azure AD tartományi szolgáltatások engedélyezni kell. Válasszon egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

  > [!TIP]
  > **A felügyelt tartományok nem helyezhető át egy másik virtuális hálózathoz, miután engedélyezte az Azure AD tartományi szolgáltatásokat.** Válassza ki a megfelelő virtuális hálózatot a felügyelt tartományok engedélyezéséhez. Egy felügyelt tartomány létrehozása után nem helyezhető egy másik virtuális hálózathoz, a felügyelt tartományra törlése nélkül. Javasoljuk, hogy tekintse át a [hálózat az Azure Active Directory tartományi szolgáltatások szempontjai](active-directory-ds-networking.md) folytatás előtt.  
  >

4. **Virtuális hálózat létrehozása:** kattintson **hozzon létre új** hozhat létre új virtuális hálózatot. Használja a dedikált alhálózatot az Azure AD tartományi szolgáltatásokhoz. Hozzon létre például egy alhálózat neve "DomainServices", így könnyen megérteni, hogy mi történik, az alhálózaton belüli más rendszergazdák számára. Kattintson a **OK** befejezése.

    ![Válassza ki a virtuális hálózat](./media/getting-started/domain-services-blade-network-pick-vnet.png)

  > [!WARNING]
  > Ügyeljen arra, hogy válasszon, amely a magánhálózati IP-címtér belül címteret. Amely nem rendelkezik, amelyek a nyilvános címterületen belüli IP-címek az Azure AD tartományi szolgáltatások belül hibákat okozhatnak.

5. **Meglévő virtuális hálózat:** Ha azt tervezi, hogy egy meglévő virtuális hálózatot válasszon [hozzon létre egy külön alhálózatot a virtuális hálózatok kiterjesztéssel](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet), majd válassza ki az adott alhálózat és. Kattintson a **virtuális hálózati** a meglévő virtuális hálózat kiválasztásához. Kattintson a **alhálózati** a dedikált alhálózati válasszon a meglévő virtuális hálózaton belül, amely lehetővé teszi az új felügyelt tartomány számára. Kattintson a **OK** befejezése.

    ![Válassza ki az alhálózatot a virtuális hálózaton belül](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **Egy alhálózat kiválasztására vonatkozó irányelvek**
  > 1. Használja a dedikált alhálózatot az Azure AD tartományi szolgáltatásokhoz. Más virtuális gépek nem telepíti az alhálózathoz. Ez a konfiguráció lehetővé teszi hálózati biztonsági csoportokkal (NSG-k) konfigurálja a munkaterhelések virtuális gépek számára a felügyelt tartományok megszakítása nélkül. További információkért lásd: [hálózat az Azure Active Directory tartományi szolgáltatások szempontjai](active-directory-ds-networking.md).
  2. Ne válassza az átjáró alhálózatának az Azure AD tartományi szolgáltatások telepítése, mert nem támogatott konfiguráció.
  3. A kijelölt alhálózat rendelkeznie kell legalább 3-5 elérhető IP-címek a címterületen belülre.
  >

6. Amikor elkészült, kattintson a **OK** folytassa a **rendszergazdai csoport** a varázsló.


## <a name="next-step"></a>Következő lépés
[3. feladat: a felügyeleti csoport konfigurálása és Azure AD tartományi szolgáltatások engedélyezése](active-directory-ds-getting-started-admingroup.md)
