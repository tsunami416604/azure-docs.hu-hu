---
title: Biztonságos LDAP (LDAPS) az Azure AD tartományi szolgáltatások engedélyezése |} A Microsoft Docs
description: Biztonságos LDAP (LDAPS) engedélyezése az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: df189e405dcd5277c1ccbd94e9d5d302660be79b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171422"
---
# <a name="enable-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Engedélyezze a biztonságos LDAP (LDAPS-t) számára az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz

## <a name="before-you-begin"></a>Előkészületek
Teljes [2. feladat – exportálja a secure LDAP-tanúsítványt egy. PFX-fájl](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3-enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>3. feladat: Az Azure portal használatával a felügyelt tartomány secure LDAP engedélyezése
Biztonságos LDAP engedélyezése, hajtsa végre az alábbi konfigurációs lépéseket:

1. Keresse meg a  **[az Azure portal](https://portal.azure.com)**.

2. Keressen rá a "domain services" kifejezésre a **erőforrások keresése** keresőmezőbe. Válassza ki **Azure AD tartományi szolgáltatások** a keresési eredmény. A **Azure AD tartományi szolgáltatások** lap felsorolja a felügyelt tartományra.

    ![Keresse meg a felügyelt tartomány kiépítése folyamatban](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Kattintson a nevére, a felügyelt tartomány (például "contoso100.com"), a tartománnyal kapcsolatban további részletek megtekintéséhez.

    ![Tartományi szolgáltatások – üzembe helyezési állapota](./media/getting-started/domain-services-provisioning-state.png)

3. Kattintson a **Secure LDAP** a navigációs ablaktáblán.

    ![Tartományi szolgáltatások – biztonságos LDAP lapozható](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Alapértelmezés szerint le van tiltva a felügyelt tartomány secure LDAP-hozzáférését. Váltógomb **biztonságos LDAP** való **engedélyezése**.

    ![Biztonságos LDAP engedélyezése](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Alapértelmezés szerint le van tiltva az interneten keresztül a felügyelt tartomány secure LDAP-hozzáférését. Váltógomb **lehetővé teszik a secure LDAP-hozzáférését az interneten keresztül** való **engedélyezése**, ha szeretné.

    > [!WARNING]
    > Ha engedélyezi a secure LDAP-hozzáférését az interneten keresztül, a tartomány téve a jelszó találgatásos támadásokkal szemben az interneten keresztül. Ezért ajánlott egy NSG-t a szükséges forrás IP-címtartományok való hozzáférés zárolása beállítását. Című témakör útmutatását [LDAPS-t a hozzáférést a felügyelt tartományhoz az interneten keresztül zároljuk](active-directory-ds-ldaps-bind-lockdown.md#task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Kattintson a mappa ikont következő **. Secure LDAP-tanúsítványt a PFX-fájl**. A PFX-fájl elérési útját adja meg a tanúsítványt a felügyelt tartomány secure LDAP-hozzáférését.

7. Adja meg a **visszafejtéséhez szükséges jelszó. PFX-fájl**. Adja meg ugyanazt a jelszót használt a tanúsítvány exportálása PFX-fájlba.

8. Amikor elkészült, kattintson a **mentése** gombra.

9. Megjelenik egy értesítés, amely tájékoztatja, hogy biztonságos LDAP konfigurálása a felügyelt tartományhoz tartozó történik. Amíg ez a művelet befejeződik, a tartomány más beállítások nem módosíthatók.

    ![A felügyelt tartomány secure LDAP konfigurálása](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> A felügyelt tartomány secure LDAP engedélyezése körülbelül 10 – 15 percig tart. Ha a megadott secure LDAP-tanúsítvány nem felel meg a szükséges feltételeknek, a címtárhoz nincs engedélyezve a biztonságos LDAP, és, egy hibaüzenet jelenik meg. Ha például a tartomány neve helytelen, a tanúsítvány már lejárt vagy hamarosan lejár. Ebben az esetben próbálkozzon újra egy érvényes tanúsítványt.
>
>

## <a name="next-step"></a>Következő lépés
[4. feladat: A DNS konfigurálása a felügyelt tartomány internetről való elérésére](active-directory-ds-ldaps-configure-dns.md)
