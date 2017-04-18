---
title: "Azure Active Directory Domain Services: az Azure virtuális hálózat DNS-beállításainak frissítése | Microsoft Docs"
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
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: abb27292d4b5533fe6f3d66d6921fea8c82f18dd
ms.lasthandoff: 04/12/2017


---
# <a name="update-dns-settings-for-the-azure-virtual-network"></a>Az Azure virtuális hálózat DNS-beállításainak frissítése
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>4. feladat: Az Azure virtuális hálózat DNS-beállításainak frissítése
Az előző konfigurációs feladatokban sikeresen engedélyezte az Azure Active Directory Domain Services-t a címtárhoz. A következő feladat annak biztosítása, hogy a virtuális hálózaton lévő összes számítógép képes legyen csatlakozni ezekhez a szolgáltatásokhoz, és használhassa azokat. Ez a cikk bemutatja, hogyan frissítheti a virtuális hálózat DNS-kiszolgálójának beállításait, hogy arra a két IP-címre mutassanak, amelyeken az Azure Active Directory Domain Services elérhető a virtuális hálózaton.

> [!NOTE]
> Miután engedélyezte az Azure Active Directory Domain Services-t a címtárhoz, jegyezze fel az Azure Active Directory Domain Services a címtár **Configure** (Konfigurálás) lapján megjelenített IP-címeit.
>
>

A DNS-kiszolgáló azon virtuális hálózatra vonatkozó beállításainak frissítéséhez, amelyiken engedélyezte az Azure Active Directory Domain Services-t, tegye a következőket:

1. Nyissa meg a [klasszikus Azure portált](https://manage.windowsazure.com).
2. A bal oldali panelen válassza ki a **Networks** (Hálózatok) elemet.  
    Ekkor megnyílik a **Networks** (Hálózatok) ablak.

    ![Virtual networks (Virtuális hálózatok) ablak](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. A **Virtual Networks** (Virtuális hálózatok) lapon válassza ki a virtuális hálózatot, amelyiken engedélyezte az Azure Active Directory Domain Services-t, és tekintse meg a tulajdonságait.
4. Kattintson a **Configure** (Konfigurálás) lapra.

    ![Virtual networks (Virtuális hálózatok) ablak](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. A **DNS servers** (DNS-kiszolgálók) szakaszban adja meg mindkét IP-címet, amely a címtár **Configure** (Konfigurálás) lapjának **Domain Services** (Tartományi szolgáltatások) szakaszában megjelentek.
6. A DNS-kiszolgáló virtuális hálózatra vonatkozó beállításainak mentéséhez kattintson a **Save** (Mentés) gombra az ablak alján található feladatpanelen.

   ![A DNS-kiszolgáló virtuális hálózatra vonatkozó beállításainak frissítése](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> A DNS-kiszolgáló a virtuális hálózatra vonatkozó beállításainak frissítését követően némi időt vehet igénybe, míg a hálózaton lévő virtuális gépek megkapják a frissített DNS-konfigurációt. Ha egy virtuális gép nem tud csatlakozni a tartományhoz, kiürítheti a DNS gyorsítótárat (az „ipconfig /flushdns” paranccsal) a virtuális gépen. Ez a parancs kikényszeríti a DNS-beállítások frissítését a virtuális gépen.
>
>

## <a name="next-steps"></a>Következő lépések
5. feladat: [Jelszavak szinkronizálásának engedélyezése az Azure Active Directory Domain Services-re](active-directory-ds-getting-started-password-sync.md)

