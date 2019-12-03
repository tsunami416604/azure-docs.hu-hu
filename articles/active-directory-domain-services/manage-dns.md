---
title: DNS kezelése Azure AD Domain Serviceshoz | Microsoft Docs
description: Megtudhatja, hogyan telepítheti a DNS-kiszolgáló eszközöket egy Azure Active Directory Domain Services felügyelt tartomány DNS-kezeléséhez.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: f47f112991a4fe2e5b245920db98e5ae7617161a
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74704936"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>DNS felügyelete Azure AD Domain Services felügyelt tartományban

A Azure Active Directory Domain Services (Azure AD DS) egyik kulcsfontosságú összetevője a DNS (tartománynév-feloldás). Az Azure AD DS tartalmaz egy olyan DNS-kiszolgálót, amely névfeloldást biztosít a felügyelt tartomány számára. Ez a DNS-kiszolgáló beépített DNS-rekordokat és frissítéseket tartalmaz a szolgáltatás futtatását lehetővé tevő kulcsfontosságú összetevőkhöz.

A saját alkalmazások és szolgáltatások futtatásakor előfordulhat, hogy DNS-rekordokat kell létrehoznia a tartományhoz nem csatlakoztatott gépekhez, konfigurálnia kell a terheléselosztó virtuális IP-címeit, vagy külső DNS-továbbítókat kell beállítania. Az *HRE DC-rendszergazdák* csoportba tartozó felhasználók DNS-rendszergazdai jogosultságokat kapnak az Azure AD DS felügyelt tartományhoz, és létrehozhatnak és szerkeszthetnek egyéni DNS-rekordokat.

Hibrid környezetben a helyszíni AD DS környezetekben konfigurált DNS-zónák és-rekordok nincsenek szinkronizálva az Azure AD DSval. Saját DNS-bejegyzések definiálásához és használatához hozzon létre rekordokat az Azure AD DS DNS-kiszolgálón, vagy használjon olyan feltételes továbbítókat, amelyek a környezetében meglévő DNS-kiszolgálókra mutatnak.

Ez a cikk bemutatja, hogyan telepítheti a DNS-kiszolgálói eszközöket, majd a DNS-konzollal kezelheti a rekordokat az Azure AD DSban.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előzetes teendők

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, fejezze be az oktatóanyagot [egy Azure Active Directory Domain Services-példány létrehozásához és konfigurálásához][create-azure-ad-ds-instance].
* Az Azure AD DS felügyelt tartományhoz csatlakoztatott Windows Server Management VM.
    * Ha szükséges, fejezze be az oktatóanyagot [egy Windows Server rendszerű virtuális gép létrehozásához és egy felügyelt tartományhoz való csatlakoztatásához][create-join-windows-vm].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.

## <a name="install-dns-server-tools"></a>DNS-kiszolgálói eszközök telepítése

Az Azure AD DS DNS-rekordjainak létrehozásához és módosításához telepítenie kell a DNS-kiszolgáló eszközeit. Ezek az eszközök a Windows Server szolgáltatásként is telepíthetők. A felügyeleti eszközök Windows-ügyfélre történő telepítésével kapcsolatos további információkért lásd: install [Távoli kiszolgálófelügyelet eszközei (RSAT)][install-rsat].

1. Jelentkezzen be a felügyeleti virtuális gépre. A Azure Portal használatával történő kapcsolódás lépéseiért lásd: [Kapcsolódás Windows Server rendszerű virtuális géphez][connect-windows-server-vm].
1. Ha a **Kiszolgálókezelő** alapértelmezés szerint nem nyílik meg, amikor bejelentkezik a virtuális gépre, válassza a **Start** menüt, majd a **Kiszolgálókezelő**elemet.
1. A **Kiszolgálókezelő** ablak *irányítópult* paneljén válassza a **szerepkörök és szolgáltatások hozzáadása**lehetőséget.
1. A *szerepkörök és szolgáltatások hozzáadása varázsló*alapismeretek **lapján kattintson a** **Tovább gombra**.
1. A *telepítés típusa*beállításnál hagyja bejelölve a **szerepköralapú vagy a szolgáltatáson alapuló telepítési** beállítást, majd kattintson a **Tovább gombra**.
1. A **kiszolgáló kiválasztása** lapon válassza ki az aktuális virtuális gépet a kiszolgáló készletéből, például *myvm.aadds.contoso.com*, majd kattintson a **tovább**gombra.
1. A **kiszolgálói szerepkörök** lapon kattintson a **tovább**gombra.
1. A **szolgáltatások** lapon bontsa ki a **Távoli kiszolgálófelügyelet eszközei** csomópontot, majd bontsa ki a **szerepkör-felügyeleti eszközök** csomópontot. A szerepkör-felügyeleti eszközök listájából válassza a **DNS-kiszolgálói eszközök** lehetőséget.

    ![Válassza a DNS-kiszolgáló eszközeinek telepítését az elérhető szerepkör-felügyeleti eszközök listájáról](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. A **jóváhagyás** lapon válassza a **telepítés**lehetőséget. A Csoportházirend felügyeleti eszközök telepítése egy-két percet is igénybe vehet.
1. A szolgáltatás telepítésének befejezése után a **Bezárás** gombra kattintva lépjen ki a **szerepkörök és szolgáltatások hozzáadása** varázslóból.

## <a name="open-the-dns-management-console-to-administer-dns"></a>A DNS-kezelő konzol megnyitása a DNS felügyeletéhez

A DNS-kiszolgálói eszközök telepítése után felügyelheti a DNS-rekordokat az Azure AD DS felügyelt tartományon.

> [!NOTE]
> A DNS Azure AD DS felügyelt tartományban való felügyeletéhez be kell jelentkeznie egy olyan felhasználói fiókba, amely tagja az *HRE DC-rendszergazdák* csoportjának.

1. A kezdőképernyőn válassza a **felügyeleti eszközök**elemet. Megjelenik az elérhető felügyeleti eszközök listája, beleértve az előző szakaszban telepített **DNS-t** is. Válassza a **DNS** lehetőséget a DNS-kezelő konzol elindításához.
1. A **Kapcsolódás a DNS-kiszolgálóhoz** párbeszédpanelen válassza ki **a következő számítógépet**, majd adja meg a felügyelt tartomány DNS-tartománynevét, például *aadds.contoso.com*:

    ![Kapcsolódás az Azure AD DS felügyelt tartományhoz a DNS-konzolon](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. A DNS-konzol csatlakozik a megadott Azure AD DS felügyelt tartományhoz. Bontsa ki a **címkeresési zónák** vagy a **névkeresési zónák** elemet a szükséges DNS-bejegyzések létrehozásához vagy a meglévő rekordok szükség szerinti szerkesztéséhez.

    ![DNS-konzol – tartomány felügyelete](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> Ha a DNS-kiszolgáló eszközeivel kezeli a rekordokat, ügyeljen arra, hogy ne törölje vagy módosítsa az Azure AD DS által használt beépített DNS-rekordokat. A beépített DNS-rekordok közé tartoznak a tartományi DNS-rekordok, a Névkiszolgáló-rekordok és a TARTOMÁNYVEZÉRLŐk helyéhez használt egyéb rekordok. Ha módosítja ezeket a rekordokat, a tartományi szolgáltatások megszakadnak a virtuális hálózaton.

## <a name="next-steps"></a>Következő lépések

A DNS kezelésével kapcsolatos további információkért tekintse meg a következő témakört: [DNS-eszközök cikk a TechNeten](https://technet.microsoft.com/library/cc753579.aspx).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
