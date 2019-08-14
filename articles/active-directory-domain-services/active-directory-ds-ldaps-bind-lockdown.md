---
title: Kötés Secure LDAP (LDAPs) használatával Azure AD Domain Services felügyelt tartományhoz | Microsoft Docs
description: Kötés Azure AD Domain Services felügyelt tartományhoz biztonságos LDAP (LDAPs) használatával
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: cbc5bee0f4cc59f59af6e3f57219279cd8fcb030
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988585"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Kötés Azure AD Domain Services felügyelt tartományhoz biztonságos LDAP (LDAPs) használatával

## <a name="before-you-begin"></a>Előkészületek
[4. feladat – a DNS konfigurálása a felügyelt tartomány internetről való eléréséhez](active-directory-ds-ldaps-configure-dns.md).


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>5\. feladat: Kötés a felügyelt tartományhoz LDAP protokollon keresztül az LDP. exe használatával
Használhatja az LDP. exe eszközt, amely a Távoli kiszolgálófelügyelet eszközei csomag részét képezi a kötéshez és az LDAP-kereséshez.

Először nyissa meg az LDP eszközt, és kapcsolódjon a felügyelt tartományhoz. Kattintson a **kapcsolat** elemre, majd a menüben kattintson a **Csatlakoztatás...** elemre. Adja meg a felügyelt tartomány DNS-tartománynevét. A kapcsolatokhoz használandó port megadása. Az LDAP-kapcsolatok esetében használja az 389-es portot. Az LDAPs-kapcsolatok esetében használja az 636-es portot. Kattintson az **OK** gombra a felügyelt tartományhoz való kapcsolódáshoz.

Ezután Kössük a felügyelt tartományhoz. Kattintson a **kapcsolatok** elemre, majd a menüben a **kötés...** elemre. Adja meg az "HRE DC rendszergazdák" csoportba tartozó felhasználói fiók hitelesítő adatait.

> [!IMPORTANT]
> A felhasználók (és a szolgáltatásfiókok) nem hajthatnak végre LDAP egyszerű kötéseket, ha letiltotta az NTLM-jelszó kivonatának szinkronizálását a Azure AD Domain Services-példányon.  Az NTLM jelszó-kivonatok szinkronizálásának letiltásával kapcsolatos további információkért olvassa el [a Azure ad Domain Services felügyelt tartomány biztonságossá tétele](secure-your-domain.md)című témakört.
>
>

Válassza a **nézet**lehetőséget, majd a menüben válassza a **fa** lehetőséget. Hagyja üresen az Alap DN mezőt, majd kattintson az OK gombra. Navigáljon a keresni kívánt tárolóhoz, kattintson a jobb gombbal a tárolóra, majd válassza a keresés lehetőséget.

> [!TIP]
> - Az Azure AD-ből szinkronizált felhasználókat és csoportokat a **AADDC-felhasználók** szervezeti egység tárolja. A szervezeti egység keresési útvonala így néz ki ```OU=AADDC Users,DC=CONTOSO100,DC=COM```.
> - A felügyelt tartományhoz csatlakoztatott számítógépekhez tartozó számítógép-fiókok tárolása a **AADDC Computers** szervezeti egységben történik. A szervezeti egység keresési útvonala így néz ki ```OU=AADDC Computers,DC=CONTOSO100,DC=COM```.
>
>

További információ – az [LDAP-lekérdezés alapjai](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>6\. feladat: Biztonságos LDAP-hozzáférés zárolása a felügyelt tartományhoz az interneten keresztül
> [!NOTE]
> Ha nem engedélyezte az LDAP-hozzáférést a felügyelt tartományhoz az interneten keresztül, ugorja át ezt a konfigurációs feladatot.
>
>

A feladat megkezdése előtt végezze el a [3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md). feladatban ismertetett lépéseket.

Ha engedélyezi az LDAP-hozzáférést az interneten keresztül a felügyelt tartományhoz, akkor biztonsági fenyegetést hoz létre. A felügyelt tartomány elérhető az internetről a biztonságos LDAP (azaz a 636-as port) által használt porton. Dönthet úgy is, hogy a felügyelt tartományhoz való hozzáférést bizonyos ismert IP-címekre korlátozza. Hozzon létre egy hálózati biztonsági csoportot (NSG), és társítsa azt az alhálózathoz, amelyhez engedélyezte Azure AD Domain Services.

Az alábbi táblázatban szereplő NSG a biztonságos LDAP-hozzáférést zárolja az interneten keresztül. A NSG lévő szabályok engedélyezik a bejövő Secure LDAP-hozzáférést a 636-as TCP-porton keresztül, csak az IP-címek megadott készletéről. Az alapértelmezett "DenyAll" szabály az internetről érkező összes többi bejövő forgalomra vonatkozik. A megadott IP-címekről az interneten keresztül az LDAPs elérést engedélyező NSG-szabály magasabb prioritású, mint a DenyAll NSG szabály.

![Minta-NSG az LDAP-hozzáférés biztonságossá tételéhez az interneten keresztül](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**További információ** - [hálózati biztonsági csoportok](../virtual-network/security-overview.md).


## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD Domain Services – Első lépések útmutató](create-instance.md)
* [Azure AD Domain Services tartomány kezelése](manage-domain.md)
* [Az LDAP-lekérdezés alapjai](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)
* [Azure AD Domain Services Csoportházirend kezelése](manage-group-policy.md)
* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md)
* [Hálózati biztonsági csoport létrehozása](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Következő lépés
[Felügyelt tartomány biztonságos LDAP-hibáinak megoldása](tshoot-ldaps.md)
