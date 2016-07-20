<properties
    pageTitle="Azure AD tartományi szolgáltatások: Az Azure AD tartományi szolgáltatások engedélyezése | Microsoft Azure"
    description="Első lépések az Azure Active Directory tartományi szolgáltatások (előzetes kiadás) használatával"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/25/2016"
    ms.author="maheshu"/>

# Azure AD tartományi szolgáltatások *(előzetes kiadás)* – Az Azure AD tartományi szolgáltatások engedélyezése

## 3. feladat: Az Azure AD tartományi szolgáltatások engedélyezése
Ennek a feladatnak a keretében engedélyezni fogja az Azure AD tartományi szolgáltatásokat a címtáron. Hajtsa végre az alábbi konfigurációs lépéseket az Azure AD tartományi szolgáltatások engedélyezéséhez a címtáron.

1. Látogasson el a **klasszikus Azure portálra** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Válassza az **Active Directory** csomópontot a bal oldali panelen.

3. Válassza ki az Azure AD bérlőt (címtárat), amelyiken engedélyezni kívánja az Azure AD tartományi szolgáltatásokat.

    ![Azure AD címtár kiválasztása](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Kattintson a **Configure** (Konfigurálás) lapra.

    ![Címtár lapjának konfigurálása](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Görgessen le a **domain services** (tartományi szolgáltatások) című szakaszig.

    ![Tartományi szolgáltatások konfigurációja szakasz](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. Váltsa át a **Enable domain services for this directory** (Tartományi szolgáltatások engedélyezése a címtáron) beállítást **YES** (IGEN) értékre. Megfigyelheti, hogy az Azure AD tartományi szolgáltatások néhány más konfigurációs beállítása is megjelenik a lapon.

    ![Tartományi szolgáltatások engedélyezése](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] Mikor engedélyezi az Azure AD tartományi szolgáltatásokat a bérlőre, az Azure AD létrehozza és eltárolja a felhasználók hitelesítéséhez szükséges Kerberos és NTLM hitelesítő adatok kivonatait.

7. Adja meg a **tartományi szolgáltatások DNS-tartománynevét**.

   - Alapértelmezés szerint a címtár alapértelmezett tartományneve (például a **.onmicrosoft.com** tartományi utótaggal végződő) lesz kiválasztva.

   - A lista tartalmazza az Azure AD címtárhoz konfigurált összes tartományt – beleértve az ellenőrzött és a nem ellenőrzött tartományokat is, amelyeket a „Domains” (Tartományok) lapon konfigurált.

   - Emellett hozzáadhat egy egyéni tartománynevet is a listához, ha beírja azt. Ebben a példában beírtuk a „contoso100.com” egyéni tartománynevet

     > [AZURE.WARNING] Bizonyosodjon meg róla, hogy a megadott tartománynév előtagja (például a „contoso100.com” tartománynévben a „contoso100”) nem haladja meg a 15 karakter hosszúságot. Nem hozhat létre olyan Azure AD tartományi szolgáltatások tartományt, amelynek az előtagja hosszabb, mint 15 karakter.

8. A következő lépés a virtuális hálózat kiválasztása, amelyben az Azure AD tartományi szolgáltatásokat el kívánja érni. Válassza ki az imént létrehozott virtuális hálózatot a **Tartományi szolgáltatások csatlakoztatása a virtuális hálózathoz** című legördülő listában.

   - Ellenőrizze, hogy a megadott virtuális hálózat az Azure AD tartományi szolgáltatások által támogatott Azure-régióba tartozik-e.

   - Az [Azure services by region](https://azure.microsoft.com/regions/#services/) (Azure-szolgáltatások régiónként) lapon tekintheti meg, hogy az Azure AD tartományi szolgáltatások mely Azure-régiókban érhetőek el.

   - Megjegyzendő, hogy az Azure AD tartományi szolgáltatások által nem támogatott régiókba tartozó virtuális hálózatok nem jelennek meg a legördülő listában.

   - Ehhez hasonlóan az Azure Resource Manager használatával létrehozott virtuális hálózatok (ARM-alapú virtuális hálózatok) szintén nem jelennek meg a legördülő listában. Ennek az oka, hogy az ARM-alapú virtuális hálózatokat az Azure AD tartományi szolgáltatások jelenleg nem támogatják.

9. Ellenőrizze, hogy a felügyelt tartomány számára választott DNS-tartománynév még nem létezik a virtuális hálózatban. Ez az alábbi esetek bármelyikében előfordulhat:

   - Ha már létezik tartomány ugyanezzel a DNS-tartománynévvel a virtuális hálózaton.

   - Ha a választott virtuális hálózat VPN-kapcsolattal rendelkezik a helyszíni hálózattal, és már létezik tartomány ugyanezzel a DNS-tartománynévvel a helyi hálózaton.

   - Ha létezik egy már meglévő felhőszolgáltatás ugyanezen a néven a virtuális hálózaton.

10. Miután kiválasztotta a fenti beállításokat, kattintson a **Save** (Mentés) gombra az oldal alján a feladatpanelen az Azure AD tartományi szolgáltatások engedélyezéséhez.

11. A lapon megjelenik a „Pending …” (Függőben …) állapotjelző, amíg a rendszer engedélyezi az Azure AD tartományi szolgáltatásokat a címtáron.

    ![Tartományi szolgáltatások engedélyezése – függő állapot](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Az Azure AD tartományi szolgáltatások biztosítják a felügyelt tartományok magas rendelkezésre állását. Amikor először engedélyezi az Azure AD tartományi szolgáltatásokat a tartományon, megfigyelheti, ahogy egyenként megjelennek az IP-címek, amelyiken a tartományi szolgáltatások a virtuális hálózaton elérhetőek. A második IP-cím is hamarosan megjelenik, amint a szolgáltatás engedélyezi a magas rendelkezésre állást a tartományra. Ha a magas rendelkezésre állás konfigurálva van és aktív a tartományon, két IP-címet kell látnia a **Configure** (Konfigurálás) lap **domain services** (tartományi szolgáltatások) szakaszában.

12. Mintegy 20–30 perc múltán a **Configure** (Konfigurálás) lap **IP address** (IP-cím) mezőjében láthatja az első IP-címet, amelyen a tartományi szolgáltatások a virtuális hálózaton elérhetőek.

    ![Tartományi szolgáltatások engedélyezve – első IP-cím kiosztva](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

13. Ha a magas rendelkezésre állás működik a tartományon, két IP-címet lát majd a lapon. Ezek az IP-címek, amelyeken az Azure AD tartományi szolgáltatások elérhetőek lesznek a kiválasztott virtuális hálózaton. Jegyezze fel ezeket az IP-címeket a virtuális hálózat DNS-beállításainak frissítéséhez. Ez a lépés lehetővé teszi, hogy a virtuális hálózaton lévő virtuális gépek a tartományhoz kapcsolódhassanak a csatlakozás tartományhoz és hasonló műveletek céljából.

    ![Tartományi szolgáltatások engedélyezve – mindkét IP-cím kiosztva](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] Az Azure AD címtár méretétől függően (felhasználók, csoportok száma stb.) eltarthat egy ideig, míg a címtár tartalma elérhető lesz az Azure AD tartományi szolgáltatásokban. Ez a szinkronizálási folyamat a háttérben zajlik. A több tízezer objektumot tartalmazó nagyméretű címtárak esetében egy vagy két napot is igénybe vehet, míg a rendszer az összes felhasználót, csoporttagságot és hitelesítő adatot szinkronizálja, és azok az Azure AD tartományi szolgáltatásokban elérhetőek lesznek.

<br>

## 4. feladat – Az Azure virtuális hálózat DNS-beállításainak frissítése
A következő konfigurációs feladat [az Azure virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md).



<!--HONumber=Jun16_HO2-->


