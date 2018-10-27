---
title: 'Az Azure Active Directory tartományi szolgáltatások: Első lépések |} A Microsoft Docs'
description: Engedélyezze az Active Directory Domain Servicest az Azure portal használatával
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: ergreenl
ms.openlocfilehash: 70dee552ec575f4969593eac4e91fdbb18b426f1
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156187"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Engedélyezze az Active Directory Domain Servicest az Azure portal használatával


## <a name="before-you-begin"></a>Előkészületek
Tekintse át a [Hálózati megfontolások az Azure Active Directory Domain Services-hez](active-directory-ds-networking.md) című dokumentumot.


## <a name="task-2-configure-network-settings"></a>2. feladat: a hálózati beállítások konfigurálása
A következő konfigurációs feladat, hogy hozzon létre egy Azure virtuális hálózat és a egy dedikált alhálózatán. Engedélyezze az Azure Active Directory Domain Services-t a virtuális hálózatának ezen az alhálózatán. Előfordulhat, hogy válasszon ki egy meglévő virtuális hálózatot, és hozzon létre a dedikált alhálózatán.

1. Kattintson a **virtuális hálózati** , válasszon ki egy virtuális hálózatot.
    > [!NOTE]
    > **A klasszikus virtuális hálózatok nem támogatottak az új üzembe helyezésekhez.** A klasszikus virtuális hálózatok nem támogatottak az új üzembe helyezésekhez. Klasszikus virtuális hálózatokon üzembe helyezett meglévő felügyelt tartományok továbbra is támogatottak. A Microsoft lehetővé teszi, hogy át egy meglévő felügyelt tartományhoz klasszikus virtuális hálózat a Resource Manager virtuális hálózatot a közeljövőben.
    >

2. Az a **válasszon egy virtuális hálózatot** lapon láthatja az összes meglévő virtuális hálózatot. Csak az erőforráscsoportot és az Azure-helyen, a kiválasztott tartozik virtuális hálózatainak megtekintéséhez a **alapjai** varázsló lapja.
3. Válassza ki a virtuális hálózatot, amelyben az Azure AD tartományi szolgáltatások engedélyezve van. Válasszon ki egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

  > [!TIP]
  > **A felügyelt tartomány nem helyezhető át egy másik virtuális hálózatot, Azure AD tartományi szolgáltatások engedélyezése után.** Válassza ki a megfelelő virtuális hálózatot engedélyezése a felügyelt tartományra. Miután létrehozott egy felügyelt tartományon, nem helyezhető egy másik virtuális hálózathoz, a felügyelt tartomány törlése nélkül. Ajánlott áttekinteni a [hálózati szempontok az Azure Active Directory Domain Services](active-directory-ds-networking.md) folytatás előtt.  
  >

4. **Virtuális hálózat létrehozása:** kattintson **új létrehozása** egy új virtuális hálózat létrehozásához. Kijelölt alhálózatot használjon az Azure AD tartományi szolgáltatásokhoz. Hozzon létre például egy alhálózat neve "DomainServices", így az tudni, mi helyezünk üzembe az alhálózaton belüli más rendszergazdák. Kattintson a **OK** befejezése.

    ![Válasszon virtuális hálózatot](./media/getting-started/domain-services-blade-network-pick-vnet.png)

  > [!WARNING]
  > Ellenőrizze, hogy válasszon, amely a privát IP-címtéren belüli címteret. Ön nem tulajdonosa, amelyek a nyilvános címtér az IP-címek az Azure AD Domain Services hibát okoznak.

5. **Meglévő virtuális hálózat:** Ha azt tervezi, hogy egy meglévő virtuális hálózatot válasszon [hozzon létre egy dedikált alhálózatot a virtuális hálózatok bővítménnyel](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet), majd válasszon az alhálózatban. Kattintson a **virtuális hálózat** a meglévő virtuális hálózat kiválasztásához. Kattintson a **alhálózati** választja ki a kijelölt alhálózatot a meglévő virtuális hálózaton belül, amely lehetővé teszi az új felügyelt tartományt. Kattintson a **OK** befejezése.

    ![A virtuális hálózatban lévő alhálózat kiválasztása](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **Útmutató az alhálózat kiválasztása**
  > 1. Kijelölt alhálózatot használjon az Azure AD tartományi szolgáltatásokhoz. Ne telepítsen más virtuális gépek ennek az alhálózatnak. Ez a konfiguráció lehetővé teszi hálózati biztonsági csoportok (NSG-k) konfigurálása a számítási feladatok/virtuális gépek számára a felügyelt tartomány megszakítása nélkül. További információkért lásd: [hálózati szempontok az Azure Active Directory Domain Services](active-directory-ds-networking.md).
  2. Ne válasszon üzembe helyezéséhez az Azure AD tartományi szolgáltatásokat, az átjáró-alhálózat, mivel nem támogatott konfiguráció.
  3. A kiválasztott alhálózat rendelkeznie kell legalább 3-5 elérhető IP-címek címterében.
  >

6. Amikor elkészült, kattintson a **OK** lépjen a **rendszergazdai csoport** lapján.


## <a name="next-step"></a>Következő lépés
[3. feladat: felügyeleti csoport konfigurálása és az Azure AD Domain Services engedélyezése](active-directory-ds-getting-started-admingroup.md)
