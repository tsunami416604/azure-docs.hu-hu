---
title: "Az Azure Active Directory tartományi szolgáltatások: Első lépések |} Microsoft Docs"
description: "Engedélyezze az Azure Active Directory tartományi szolgáltatások az Azure portál használatával"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 7d80049d4b6f7d57924522e3f273c42f4c887fee
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Engedélyezze az Azure Active Directory tartományi szolgáltatások az Azure portál használatával
Ez a cikk bemutatja, hogyan engedélyezze az Azure Active Directory tartományi szolgáltatások (az Azure Active Directory tartományi szolgáltatások) az Azure portál használatával.

Elindíthatja a **engedélyezése az Azure AD tartományi szolgáltatások** varázslóban kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. A bal oldali ablaktáblájában kattintson a **új**.
3. Az a **új** írja be **tartományi szolgáltatások** azokat a keresési sávon.

    ![Keresse meg a tartományi szolgáltatások](./media/getting-started/search-domain-services.png)

4. Kattintással jelölje ki **Azure AD tartományi szolgáltatások** keresési javaslatok listája. Az a **Azure AD tartományi szolgáltatások** lapján kattintson a **létrehozása** gombra.

    ![Tartományi szolgáltatások nézete](./media/getting-started/domain-services-blade.png)

5. A **engedélyezése az Azure AD tartományi szolgáltatások** varázsló elindul.


## <a name="task-1-configure-basic-settings"></a>1. feladat: konfigurálja az alapbeállításokat
Az a **alapjai** lap a varázslóban megadhatja a DNS-tartománynév, a felügyelt tartomány számára. Választhatja azt is, az erőforráscsoport és az Azure-beli hely, amelyhez a felügyelt tartományra kell telepíteni.

![Alapvető beállítások konfigurálása](./media/getting-started/domain-services-blade-basics.png)

1. Válassza ki a **DNS-tartománynév** a felügyelt tartományok.

   > [!NOTE]
   > **Egy DNS-tartománynév kiválasztása vonatkozó irányelvek**
   > * **Beépített tartománynév:** alapértelmezés szerint a varázsló adja meg a könyvtár alapértelmezett/beépített-a tartomány nevét (az egy **. onmicrosoft.com** utótag) meg. Ha az interneten keresztül a felügyelt tartományra biztonságos LDAP elérésének lehetővé tétele, már problémák egy nyilvános DNS-rekord létrehozása és biztonságos LDAP-tanúsítvány beszerzése a tartománynévhez tartozó nyilvános Hitelesítésszolgáltatótól származó. A Microsoft tulajdonában van a *. onmicrosoft.com* tartomány és a hitelesítésszolgáltatók nem állítanak ki tanúsítványokat, a tartomány biztosításáért.
   * **Egyéni tartománynevek:** a egy egyéni tartománynevet is megadható. Ebben a példában az egyéni tartománynév a *contoso100.com*.
   * **A nem átirányítható tartományutótagok:** általánosságban javasolt egy nem átirányítható tartománynév utótagja elkerülése. Például célszerűbb ne hozzon létre a tartomány a DNS neve "contoso.local tartományt". A ".local" DNS-utótag nem irányítható, és a DNS-feloldás problémákat okozhatnak.
   * **Tartománykorlátozások előtag:** a megadott tartománynév előtagja (például *contoso100* a a *contoso100.com* tartománynevet) kell tartalmaznia a 15 karaktert. Nem hozható létre egy felügyelt tartomány 15 karakternél hosszabb előtaggal kezdődik.
   * **A névütközések hálózati:** gondoskodjon arról, hogy a felügyelt tartomány számára választott DNS-tartománynév még nem létezik a virtuális hálózat. Pontosabban, ellenőrizze, hogy:
       * Már van egy Active Directory-tartomány az azonos DNS-tartománynévvel a virtuális hálózaton.
       * A virtuális hálózat, ahol a felügyelt tartományra engedélyezni szeretné a helyszíni hálózat VPN-kapcsolattal rendelkezik. Ebben a forgatókönyvben győződjön meg arról, nincs olyan tartomány ugyanezzel a DNS-tartománynévvel a helyi hálózaton.
       * Létezik-e egy már meglévő felhőszolgáltatás ugyanezen a néven a virtuális hálózaton.
    >

2. Válassza ki az Azure **előfizetés** a szeretné létrehozni a felügyelt tartományra.

3. Válassza ki a **erőforráscsoport** a felügyelt tartományra tartozik kell. Ön választhatja a **hozzon létre új** vagy **meglévő** lehetőséget választhat ki az erőforráscsoportot.

4. Válassza ki az Azure **hely** a a felügyelt tartományra meg kell létrehozni. Az a **hálózati** oldalon a varázsló csak virtuális hálózatok, amelyek a kijelölt helyre látja.

5. Amikor elkészült, kattintson a **OK** a áthelyezése a **hálózati** a varázsló.


## <a name="next-step"></a>Következő lépés
[2. feladat: a hálózati beállítások konfigurálása](active-directory-ds-getting-started-network.md)
