---
title: "Active Directory Domain Services: Az Active Directory Domain Services engedélyezése | Microsoft Docs"
description: "Első lépések az Azure Active Directory tartományi szolgáltatások használatával"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c659da59-f4b5-4edd-b702-1727a8ccb36f
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: e5f1fe51d8931985fa55b2d8c0a3fd25bb93f20f
ms.lasthandoff: 04/12/2017


---
# <a name="enable-azure-active-directory-domain-services"></a>Az Active Directory Domain Services engedélyezése
## <a name="task-3-enable-azure-active-directory-domain-services"></a>3. feladat: Az Active Directory Domain Services engedélyezése
Ennek a feladatnak a keretében engedélyezi az Active Directory Domain Servicest (Azure AD DS) a címtár számára a következők végrehajtásával:

1. Nyissa meg a [klasszikus Azure portált](https://manage.windowsazure.com).
2. A bal oldali panelen válassza az **Active Directory** gombot.
3. Válassza ki az Azure Active Directory (Azure AD) bérlőt (címtárat), amelyiken engedélyezni kívánja az Azure AD DS szolgáltatásokat.

    ![Azure AD címtár kiválasztása](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. Az **előzetes verzió** címtár oldalán kattintson a **Konfigurálás** lapra.

    ![Címtár lapjának konfigurálása](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. A **tartományi szolgáltatások** alatt váltsa át a **Tartományi szolgáltatások engedélyezése a címtáron** beállítást **Igen** értékre.  
    További Active Directory Domain Services konfigurációs beállítások jelennek meg az oldalon.

    ![Tartományi szolgáltatások engedélyezése](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

   > [!NOTE]
   > Amikor engedélyezi az Active Directory Domain Servicest a bérlő számára, az Azure AD létrehozza és tárolja a felhasználók hitelesítéséhez szükséges Kerberos és NTLM hitelesítő adatok kivonatait.
   >
   >
6. Adja meg a **tartományi szolgáltatások DNS-tartománynevét**.

   * Alapértelmezés szerint a címtár alapértelmezett tartományneve (azaz az **.onmicrosoft.com** utótaggal végződő) lesz kiválasztva.

   * A lista tartalmazza az Azure AD címtárhoz konfigurált összes tartományt, beleértve az ellenőrzött és a nem ellenőrzött tartományokat egyaránt, amelyeket a **Tartományok** lapon konfigurált.

   * Egy egyéni tartománynevet is megadhat. Ebben a példában az egyéni tartománynév a *contoso100.com*.

     > [!WARNING]
     > A megadott tartománynév előtagja (a *contoso100.com* tartománynévben például a *contoso100* tag) legfeljebb 15 karaktert tartalmazhat. Nem hozhat létre olyan Active Directory Domain Services tartományt, amelynek az előtagja 15 karakternél többet tartalmaz.
     >
     >
7. Ellenőrizze, hogy a felügyelt tartomány számára választott DNS-tartománynév még nem létezik a virtuális hálózatban. A következőket ellenőrizze:

   * Már létezik-e tartomány ugyanezzel a DNS-tartománynévvel a virtuális hálózaton.

   * A kiválasztott virtuális hálózat rendelkezik-e VPN-kapcsolattal a helyszíni hálózattal, és hogy már létezik-e tartomány ugyanezzel a DNS-tartománynévvel a helyi hálózaton.

   * Létezik-e egy már meglévő felhőszolgáltatás ugyanezen a néven a virtuális hálózaton.
8. Válasszon egy virtuális hálózatot, amelyen szeretné, ha az Active Directory Domain Services elérhető lenne. Válassza ki a létrehozott virtuális hálózatot és a kijelölt alhálózatot a **Tartományi szolgáltatások csatlakoztatása a virtuális hálózathoz** legördülő listában. Ügyeljen a következőkre is:

   * Ellenőrizze, hogy a megadott virtuális hálózat az Active Directory Domain Services által támogatott Azure-régióba tartozik-e. Az Azure-régiók listájáért, ahol az Active Directory Domain Services elérhető, lásd: [Azure-szolgáltatások régiónként](https://azure.microsoft.com/regions/#services/).

   * Az Active Directory Domain Services által nem támogatott régiókba tartozó virtuális hálózatok nem jelennek meg a legördülő listában.

   * Az Active Directory Domain Serviceshez a virtuális hálózaton belüli kijelölt alhálózatot használjon. *Ne* válassza az átjáró alhálózatot. Lásd: [hálózati szempontok](active-directory-ds-networking.md).

   * Az Azure Resource Manager használatával létrehozott virtuális hálózatok szintén nem jelennek meg a legördülő listában. A Resource Manager-alapú virtuális hálózatokat az Active Directory Domain Services jelenleg nem támogatja.
9. Az Active Directory Domain Services engedélyezéséhez kattintson a **Mentés** gombra a feladatpanelen a lap alján. 
    * Amíg a rendszer engedélyezi az Active Directory Domain Servicest a címtáron, az oldal *Függőben* állapotot mutat.

        ![A Tartományi szolgáltatások engedélyezése ablak](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

        > [!NOTE]
        > Az Active Directory Domain Services biztosítja a felügyelt tartományok magas rendelkezésre állását. Miután engedélyezte az Active Directory Domain Servicest, megfigyelheti, hogy az IP-címek, amelyeken a tartományi szolgáltatások a virtuális hálózaton elérhetők, egyenként jelennek meg. Az első után a második IP-cím is hamarosan megjelenik, amint a szolgáltatás engedélyezi a magas rendelkezésre állást a tartomány számára. Ha a magas rendelkezésre állás konfigurálva van és aktív a tartományon, két IP-címet kell látnia a **Configure** (Konfigurálás) lap **domain services** (tartományi szolgáltatások) szakaszában.
        >
        >
    * Mintegy 20–30 perc múltán a **Konfigurálás** lap **IP-cím** mezőjében láthatja az első IP-címet, amelyen a tartományi szolgáltatások elérhetők a virtuális hálózaton.

        ![A Tartományi szolgáltatások ablak, rajta az első kiosztott IP-címmel](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)
    * Ha a magas rendelkezésre állás működik a tartomány esetén, két IP-cím látható a lapon. A felügyelt tartomány a kiválasztott virtuális hálózaton ezen a két IP-címen érhető el. 
    
10. Jegyezze fel a két IP-címet a virtuális hálózat DNS-beállításainak frissítéséhez. Ez lehetővé teszi, hogy a virtuális hálózaton lévő virtuális gépek a tartományhoz kapcsolódhassanak a csatlakozás tartományhoz és hasonló műveletek céljából.

    ![A Tartományi szolgáltatások ablak, rajta a két kiosztott IP-címmel](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [!NOTE]
> Az Azure AD-bérlő méretétől (például a felhasználók vagy csoportok számától) függően a felügyelt tartományhoz való szinkronizálás eltarthat egy ideig. Ez a szinkronizálási folyamat a háttérben zajlik. A több tízezer objektumot tartalmazó nagy méretű bérlők esetében az összes felhasználó, csoporttagság és hitelesítő adat szinkronizálása egy vagy két napot is igénybe vehet.
>
>

## <a name="next-steps"></a>Következő lépések
4. feladat: [Az Azure virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)

