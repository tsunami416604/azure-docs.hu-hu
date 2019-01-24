---
title: 'Az Azure Active Directory tartományi szolgáltatások: Az Azure virtuális hálózat DNS-beállításainak frissítése |} A Microsoft Docs'
description: Első lépések az Azure Active Directory tartományi szolgáltatások használatával
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: 84484be80ec0932b45f520482938f74ecf54d1e8
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856655"
---
# <a name="enable-azure-active-directory-domain-services"></a>Az Active Directory Domain Services engedélyezése

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>4. feladat: Az Azure virtuális hálózat DNS-beállításainak frissítése
Az előző konfigurációs feladatokban sikeresen engedélyezte az Azure Active Directory Domain Services-t a címtárhoz. Ez után tegye lehetővé, hogy a virtuális hálózaton lévő összes számítógép csatlakozzon ezekhez a szolgáltatásokhoz, és használhassa azokat. Ez a cikk bemutatja, hogyan frissítheti a virtuális hálózat DNS-kiszolgálójának beállításait, hogy arra a két IP-címre mutassanak, amelyeken az Azure Active Directory Domain Services elérhető a virtuális hálózaton.

A DNS-kiszolgáló azon virtuális hálózatra vonatkozó beállításainak frissítéséhez, amelyiken engedélyezte az Azure Active Directory Domain Servicest, végezze el a következő műveleteket:


1. A felügyelt tartomány teljes kiépítése után az **Overview** (Áttekintés) lapon jelenik meg az elvégzendő **szükséges konfigurációs lépések** listája. Az első konfigurációs lépés a **virtuális hálózathoz tartozó DNS-kiszolgáló beállításainak frissítése**.

    ![Domain Services – Áttekintő lap](./media/getting-started/domain-services-provisioned-overview.png)

    > [!TIP]
    > Nem látja ezt a konfigurációs lépést? Ha a DNS-kiszolgálónak a virtuális hálózatára vonatkozó beállításai naprakészek, az Áttekintő lapon nem jelenik meg a „DNS-kiszolgálói beállítások frissítése a virtuális hálózathoz” csempe.
    >
    >

2. A DNS-kiszolgáló virtuális hálózatra vonatkozó beállításainak frissítése a **Konfigurálás** gombra kattintva végezhető el.

> [!NOTE]
> A hálózat virtuális gépei csak újraindítás után alkalmazzák az új DNS-beállításokat. Ha azt szeretné, hogy azonnal alkalmazzák a frissített DNS-beállításokat, kezdeményezzen újraindítást a portálon, a PowerShellen vagy a CLI-n keresztül.
>
>

## <a name="next-step"></a>Következő lépés
[5. feladat: Az Azure Active Directory Domain Servicesre történő jelszókivonat-szinkronizálás engedélyezése](active-directory-ds-getting-started-password-sync.md)
