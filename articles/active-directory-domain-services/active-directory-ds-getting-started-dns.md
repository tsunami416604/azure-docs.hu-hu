---
title: "Azure AD tartományi szolgáltatások: az Azure virtuális hálózat DNS-beállításainak frissítése | Microsoft Docs"
description: "Első lépések az Azure Active Directory tartományi szolgáltatások használatával"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/21/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 81c0564390c57c7ee001ce5a574ab670faf898b4


---
# <a name="azure-ad-domain-services-update-dns-settings-for-the-azure-virtual-network"></a>Azure AD tartományi szolgáltatások: az Azure virtuális hálózat DNS-beállításainak frissítése
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>4. feladat: Az Azure virtuális hálózat DNS-beállításainak frissítése
A korábbi konfigurációs feladatokban sikeresen engedélyezte az Azure AD tartományi szolgáltatásokat a címtárhoz. A következő feladat annak biztosítása, hogy a virtuális hálózaton lévő összes számítógép képes legyen csatlakozni ezekhez a szolgáltatásokhoz, és használhassa azokat. Frissítse a virtuális hálózat DNS-kiszolgálójának beállításait, hogy arra a két IP-címre mutassanak, amelyeken az Azure AD tartományi szolgáltatások elérhetők a virtuális hálózaton.

> [!NOTE]
> Jegyezze fel az Azure AD tartományi szolgáltatásokban a címtár **Configure** (Konfigurálás) lapján megjelenő IP-címet, miután engedélyezte az Azure AD tartományi szolgáltatásokat a címtárhoz.
> 
> 

Hajtsa végre az alábbi konfigurációs lépéseket a DNS-kiszolgáló azon virtuális hálózatra vonatkozó beállításainak frissítéséhez, amelyiken engedélyezte az Azure AD tartományi szolgáltatásokat.

1. Nyissa meg a **klasszikus Azure-portált** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Válassza a **Networks** (Hálózat) csomópontot a bal oldali panelen.
   
    ![Virtual networks (Virtuális hálózatok) csomópont](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. A **Virtual Networks** (Virtuális hálózatok) lapon válassza ki a virtuális hálózatot, amelyiken engedélyezte az Azure AD tartományi szolgáltatásokat, és tekintse meg a tulajdonságait.
4. Kattintson a **Configure** (Konfigurálás) lapra.
   
    ![Virtuális hálózatok csomópont](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. A **DNS servers** (DNS-kiszolgálók) szakaszban adja meg az Azure AD tartományi szolgáltatások IP-címeit.
6. Mindenképp adja meg mindkét IP-címet, amely a címtár **Configure** (Konfigurálás) lapjának **Domain Services** (Tartományi szolgáltatások) szakaszában megjelentek.
7. A DNS-kiszolgáló virtuális hálózatra vonatkozó beállításainak mentéséhez kattintson a **Save** (Mentés) gombra a lap alján található feladatpanelen.
   
   ![Frissítse a DNS-kiszolgáló virtuális hálózatra vonatkozó beállításait.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> A DNS-kiszolgáló a virtuális hálózatra vonatkozó beállításainak frissítését követően némi időt vehet igénybe, míg a hálózaton lévő virtuális gépek megkapják a frissített DNS-konfigurációt. Ha egy virtuális gép nem tud csatlakozni a tartományhoz, kiürítheti a DNS gyorsítótárat (az „ipconfig /flushdns” paranccsal) a virtuális gépen. Ez a parancs kikényszeríti a DNS-beállítások frissítését a virtuális gépen.
> 
> 

## <a name="task-5-enable-password-synchronization-to-azure-ad-domain-services"></a>5. feladat – Jelszavak szinkronizálásának engedélyezése az Azure AD tartományi szolgáltatásokra
A következő konfigurációs feladat a [jelszavak szinkronizálásának engedélyezése az Azure AD tartományi szolgáltatásokra](active-directory-ds-getting-started-password-sync.md).




<!--HONumber=Nov16_HO2-->


