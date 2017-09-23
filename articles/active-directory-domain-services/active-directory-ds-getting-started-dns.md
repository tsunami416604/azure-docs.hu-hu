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
ms.openlocfilehash: c704ee189072ce8ed196d1ef0a23edd528a10025
ms.contentlocale: hu-hu
ms.lasthandoff: 06/30/2017

---
# <a name="enable-azure-active-directory-domain-services-preview"></a>Az Azure Active Directory Domain Services engedélyezése (előzetes verzió)

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>4. feladat: Az Azure virtuális hálózat DNS-beállításainak frissítése
Az előző konfigurációs feladatokban sikeresen engedélyezte az Azure Active Directory Domain Services-t a címtárhoz. A következő feladat annak biztosítása, hogy a virtuális hálózaton lévő összes számítógép képes legyen csatlakozni ezekhez a szolgáltatásokhoz, és használhassa azokat. Ez a cikk bemutatja, hogyan frissítheti a virtuális hálózat DNS-kiszolgálójának beállításait, hogy arra a két IP-címre mutassanak, amelyeken az Azure Active Directory Domain Services elérhető a virtuális hálózaton.

A DNS-kiszolgáló azon virtuális hálózatra vonatkozó beállításainak frissítéséhez, amelyiken engedélyezte az Azure Active Directory Domain Services-t, végezze el a következő műveleteket:

1. A felügyelt tartomány teljes kiépítése után az **Overview** (Áttekintés) lapon jelenik meg az elvégzendő **szükséges konfigurációs lépések** listája. Az első konfigurációs lépés a **virtuális hálózathoz tartozó DNS-kiszolgáló beállításainak frissítése**.

    ![Tartományi szolgáltatások – Áttekintés lap a teljes kiépítés után](./media/getting-started/domain-services-provisioned-overview.png)

2. A tartomány teljes kiépítését követően két IP-cím jelenik meg ezen a csempén. Mindkét IP-cím a felügyelt tartományhoz tartozó tartományvezérlőt jelöli.

3. Az első IP-cím vágólapra való másolásához kattintson a mellette lévő másolás gombra. Ezt követően kattintson a **Configure DNS servers** (DNS-kiszolgálók konfigurálása) gombra.

4. Illessze be az első IP-címet a **DNS servers** (DNS-kiszolgálók) panelen található **Add DNS server** (DNS-kiszolgáló hozzáadása) szövegmezőbe. A második IP-cím másolásához görgessen vízszintesen balra, majd illessze be az **Add DNS server** (DNS-kiszolgáló hozzáadása) szövegmezőbe.

    ![Tartományi szolgáltatások – DNS frissítése](./media/getting-started/domain-services-update-dns.png)

5. Ha végzett, kattintson a **Save** (Mentés) elemre a virtuális hálózathoz tartozó DNS-kiszolgáló frissítéséhez.

> [!NOTE]
> A hálózat virtuális gépei csak újraindítás után alkalmazzák az új DNS-beállításokat. Ha azt szeretné, hogy azonnal alkalmazzák a frissített DNS-beállításokat, kezdeményezzen újraindítást a portálon, a PowerShellen vagy a CLI-n keresztül.
>
>

## <a name="next-step"></a>Következő lépés
[5. feladat: Jelszavak szinkronizálásának engedélyezése az Azure Active Directory Domain Services-re](active-directory-ds-getting-started-password-sync.md)

