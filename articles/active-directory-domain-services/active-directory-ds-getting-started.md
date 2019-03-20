---
title: 'Az Azure Active Directory tartományi szolgáltatások: Első lépések |} A Microsoft Docs'
description: Engedélyezze az Active Directory Domain Servicest az Azure portal használatával
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: ergreenl
ms.openlocfilehash: 637ad62744affa37630df9c841f3c7529674e788
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122909"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Engedélyezze az Active Directory Domain Servicest az Azure portal használatával
Ez a cikk bemutatja, hogyan lehet engedélyezni az Azure Active Directory Domain Services (Azure AD DS) az Azure portal használatával.


## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok végrehajtásához szükséges:

* Egy érvényes **Azure-előfizetés**.
* Egy **Azure AD-címtár** -vagy az egy helyszíni címtár vagy egy csak felhőalapú címtárral szinkronizálja.
* A **Azure-előfizetéshez kell rendelni az Azure AD-címtár**.
* Szükséges **globális rendszergazdai** jogosultsági szintű Azure AD tartományi szolgáltatások engedélyezése az Azure AD-címtárát.


## <a name="enable-azure-ad-domain-services"></a>Az Azure AD tartományi szolgáltatások engedélyezése

Elindíthatja a **engedélyezése az Azure AD Domain Services** varázsló a következő lépéseket:

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. A bal oldali panelen kattintson az **Erőforrás létrehozása** elemre.
3. Az a **új** írja be **Domain Services** kifejezést a keresősávba.

    ![Search for domain services](./media/getting-started/search-domain-services.png)

4. Kattintással jelölje ki **Azure AD tartományi szolgáltatások** keresési javaslatok listájáról. Az a **Azure AD tartományi szolgáltatások** lap, kattintson a **létrehozás** gombra.

    ![Tartományi szolgáltatások megtekintése](./media/getting-started/domain-services-blade.png)

5. A **engedélyezése az Azure AD Domain Services** varázsló elindul.


## <a name="task-1-configure-basic-settings"></a>1. feladat: az alapvető beállítások konfigurálása
Az a **alapjai** oldal a varázslóban adja meg a felügyelt tartományhoz tartozó DNS-tartománynév. Azt is beállíthatja az erőforráscsoportot és az Azure-beli hely, amelyhez a felügyelt tartomány üzembe szeretné helyezni.

![Alapvető beállítások konfigurálása](./media/getting-started/domain-services-blade-basics.png)

1. Válassza ki a **DNS-tartománynév** a felügyelt tartományhoz.

   > [!NOTE]
   > **Irányelveket talál a DNS-tartománynév**
   > * **Beépített tartománynév:** Alapértelmezés szerint a varázsló a címtár alapértelmezett/beépített-a tartomány nevét adja meg (az egy **. onmicrosoft.com** utótag) az Ön számára. Ha engedélyezése a felügyelt tartomány secure LDAP-hozzáférését az interneten keresztül lehetőséget választja, a várt egy nyilvános DNS-rekord létrehozása és egy biztonságos LDAP-tanúsítvány beszerzése a tartománynévhez tartozó nyilvános Hitelesítésszolgáltatótól származó problémákat. A Microsoft tulajdonában lévő a *.onmicrosoft.com* tartomány és a hitelesítésszolgáltatók nem tanúsítványokat kiállítják majd ezt a tartományt szavatolja.
   > * **Egyéni tartománynevek:** Egyéni tartománynevet is beírhat. Ebben a példában az egyéni tartománynév a *contoso100.com*.
   > * **A nem irányítható tartományutótagok:** Általában nem ajánlott olyan nem átirányítható tartománynév utótagja. Például érdemes ne hozzon létre egy tartomány DNS tartomány neve "contoso.local". A ".local" DNS-utótag nem irányítható, és a DNS-feloldás problémákat okozhatnak.
   > * **Tartománynév előtagja korlátozások:** A megadott tartománynév előtagja (a *contoso100.com* tartománynévben például a contoso100 tag) legfeljebb 15 karaktert tartalmazhat. Nem hozható létre egy felügyelt tartomány egy előtagja 15 karakternél hosszabb.
   > * **Hálózat neve ütközik:** Ellenőrizze, hogy a felügyelt tartomány számára választott DNS-tartománynév még nem létezik a virtuális hálózatban. Különösen azt ellenőrizze, hogy:
   >     * Már rendelkezik Active Directory-tartományban, azonos DNS-tartománynévvel a virtuális hálózaton.
   >     * A virtuális hálózat, ahol a felügyelt tartomány engedélyezni szeretné a helyszíni hálózathoz egy VPN-kapcsolattal rendelkezik. Ebben a forgatókönyvben ugyanazt a DNS-tartománynév-tartomány nem rendelkezik a helyszíni hálózaton biztosítása.
   >     * Létezik-e egy már meglévő felhőszolgáltatás ugyanezen a néven a virtuális hálózaton.

2. Válassza ki az Azure **előfizetés** a szeretné létrehozni a felügyelt tartományhoz.

3. Válassza ki a **erőforráscsoport** , amely a felügyelt tartományhoz kell tartoznia. Válasszon a **új létrehozása** vagy **meglévő** lehetőségeket válassza ki az erőforráscsoportot.

4. Válassza ki az Azure **hely** amely a felügyelt tartományhoz létre szeretné hozni. Az a **hálózati** oldalon a varázsló megjelenik a kiválasztott helyhez tartozó csak virtuális hálózatok.

5. Kattintson a **OK** a áthelyezése a **hálózati** lapján.


## <a name="next-step"></a>Következő lépés
[2. feladat: a hálózati beállítások konfigurálása](active-directory-ds-getting-started-network.md)
