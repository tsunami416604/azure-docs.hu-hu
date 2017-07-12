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
ms.date: 06/27/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 8bee2a25f196d645b27f30f21305b1550e44e07a
ms.contentlocale: hu-hu
ms.lasthandoff: 06/30/2017


---
<a id="update-dns-settings-for-the-azure-virtual-network" class="xliff"></a>

# Az Azure virtuális hálózat DNS-beállításainak frissítése
<a id="task-4-update-dns-settings-for-the-azure-virtual-network" class="xliff"></a>

## 4. feladat: Az Azure virtuális hálózat DNS-beállításainak frissítése
Az előző konfigurációs feladatokban sikeresen engedélyezte az Azure Active Directory Domain Services-t a címtárhoz. A következő feladat annak biztosítása, hogy a virtuális hálózaton lévő összes számítógép képes legyen csatlakozni ezekhez a szolgáltatásokhoz, és használhassa azokat. Ez a cikk bemutatja, hogyan frissítheti a virtuális hálózat DNS-kiszolgálójának beállításait, hogy arra a két IP-címre mutassanak, amelyeken az Azure Active Directory Domain Services elérhető a virtuális hálózaton.

> [!NOTE]
> Miután engedélyezte az Azure Active Directory Domain Services-t a címtárhoz, jegyezze fel az Azure Active Directory Domain Services a címtár **Configure** (Konfigurálás) lapján megjelenített IP-címeit.
>
>

A DNS-kiszolgáló azon virtuális hálózatra vonatkozó beállításainak frissítéséhez, amelyiken engedélyezte az Azure Active Directory Domain Servicest, végezze el a következő műveleteket:

1. Nyissa meg a [klasszikus Azure portált](https://manage.windowsazure.com).
2. A bal oldali panelen válassza ki a **Networks** (Hálózatok) elemet.  
    Ekkor megnyílik a **Networks** (Hálózatok) ablak.

    ![Virtual networks (Virtuális hálózatok) ablak](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. A **Virtual Networks** (Virtuális hálózatok) lapon válassza ki a virtuális hálózatot, amelyiken engedélyezte az Azure Active Directory Domain Servicest, és tekintse meg a tulajdonságait.
4. Kattintson a **Configure** (Konfigurálás) lapra.

    ![Virtual networks (Virtuális hálózatok) ablak](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. A **DNS servers** (DNS-kiszolgálók) szakaszban adja meg mindkét IP-címet, amely a címtár **Configure** (Konfigurálás) lapjának **Domain Services** (Tartományi szolgáltatások) szakaszában megjelentek.
6. A DNS-kiszolgáló virtuális hálózatra vonatkozó beállításainak mentéséhez kattintson a **Save** (Mentés) gombra az ablak alján található feladatpanelen.

   ![A DNS-kiszolgáló virtuális hálózatra vonatkozó beállításainak frissítése](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
>  A hálózat virtuális gépei csak újraindítás után alkalmazzák az új DNS-beállításokat. Ha azt szeretné, hogy azonnal alkalmazzák a frissített DNS-beállításokat, kezdeményezzen újraindítást a portálon, a PowerShellen vagy a CLI-n keresztül.
>
>

<a id="next-steps" class="xliff"></a>

## Következő lépések
5. feladat: [Jelszavak szinkronizálásának engedélyezése az Azure Active Directory Domain Servicesre](active-directory-ds-getting-started-password-sync.md)

