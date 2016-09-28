<properties
    pageTitle="Azure AD tartományi szolgáltatások: az Azure virtuális hálózat DNS-beállításainak frissítése | Microsoft Azure"
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
    ms.date="07/06/2016"
    ms.author="maheshu"/>


# Azure AD tartományi szolgáltatások *(előzetes kiadás)* – Az Azure virtuális hálózat DNS-beállításainak frissítése

## 4. feladat: Az Azure virtuális hálózat DNS-beállításainak frissítése
Miután sikeresen engedélyezte az Azure AD tartományi szolgáltatásokat a címtárhoz, a következő feladata, hogy biztosítsa, hogy a virtuális hálózaton lévő összes számítógép képes csatlakozni ezekhez a szolgáltatásokhoz, és használni azokat. Ehhez frissítenie kell a virtuális hálózat DNS-kiszolgálójának beállításait, hogy azok azokra az IP-címekre mutassanak, amelyiken az Azure AD tartományi szolgáltatások elérhetőek a virtuális hálózaton.

> [AZURE.NOTE] Jegyezze fel az Azure AD tartományi szolgáltatásokban a címtár **Configure** (Konfigurálás) lapján megjelenő IP-címet, miután engedélyezte az Azure AD tartományi szolgáltatásokat a címtárhoz.

Hajtsa végre az alábbi konfigurációs lépéseket a DNS-kiszolgáló adott virtuális hálózatra vonatkozó beállításainak frissítéséhez, amelyiken engedélyezte az Azure AD tartományi szolgáltatásokat.

1. Látogasson el a **klasszikus Azure portálra** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Válassza a **Networks** (Hálózat) csomópontot a bal oldali panelen.

    ![Virtual networks (Virtuális hálózatok) csomópont](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. A **Virtual Networks** (Virtuális hálózatok) lapon válassza ki a virtuális hálózatot, amelyiken engedélyezte az Azure AD tartományi szolgáltatásokat, és tekintse meg a tulajdonságait.

4. Kattintson a **Configure** (Konfigurálás) lapra.

    ![Virtual networks (Virtuális hálózatok) csomópont](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. A **DNS servers** (DNS-kiszolgálók) szakaszban adja meg az Azure AD tartományi szolgáltatások IP-címeit.

6. Mindenképp adja meg mindkét IP-címet, amely a címtár **Configure** (Konfigurálás) lapjának **Domain Services** (Tartományi szolgáltatások) szakaszában megjelentek.

7. A DNS-kiszolgáló a virtuális hálózatra vonatkozó beállításainak mentéséhez kattintson a **Save** (Mentés) gombra az oldal alján a feladatpanelen.

   ![Frissítse a DNS-kiszolgáló virtuális hálózatra vonatkozó beállításait.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] A DNS-kiszolgáló a virtuális hálózatra vonatkozó beállításainak frissítését követően némi időt vehet igénybe, míg a hálózaton lévő virtuális gépek megkapják a frissített DNS-konfigurációt. Ha egy virtuális gép nem tud csatlakozni a tartományhoz, kiürítheti a DNS gyorsítótárat (az „ipconfig /flushdns” paranccsal) a virtuális gépen, hogy kikényszerítse a DNS-beállítások frissítését a virtuális gépen.


## 5. feladat – Jelszavak szinkronizálásának engedélyezése az Azure AD tartományi szolgáltatásokra
A következő konfigurációs feladat a [jelszavak szinkronizálásának engedélyezése az Azure AD tartományi szolgáltatásokra](active-directory-ds-getting-started-password-sync.md).



<!--HONumber=Sep16_HO4-->


