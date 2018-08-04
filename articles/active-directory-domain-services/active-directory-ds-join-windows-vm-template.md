---
title: A Windows Server rendszerű virtuális gép csatlakoztatása az Azure Active Directory Domain Servicesben |} A Microsoft Docs
description: A Windows Server virtuális gépek csatlakoztatása felügyelt tartományhoz az Azure Resource Manager-sablonok használatával.
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 2b94496895c0ab06127b6c556b1240e3279e572f
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504413"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Egy Windows Servert futtató virtuális gép csatlakoztatása felügyelt tartományokhoz Resource Manager-sablon használatával
Ez a cikk bemutatja, hogyan egy Windows Servert futtató virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz Resource Manager-sablonok használatával.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:
1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy az egy helyszíni címtár vagy egy csak felhőalapú címtárral szinkronizálja.
3. **Az Azure AD Domain Services** engedélyezve kell lennie az Azure AD-címtárban. Ha még nem tette, minden ismertetett feladatok végrehajtásával a [a kezdeti lépések útmutatóban](active-directory-ds-getting-started.md).
4. Győződjön meg arról, hogy már konfigurálta az IP-címek a felügyelt tartomány a virtuális hálózat DNS-kiszolgálóként. További információkért lásd: [az Azure virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)
5. Végezze el a szükséges lépéseket [szinkronizálja a jelszavakat az Azure AD tartományi szolgáltatásokkal felügyelt tartományban](active-directory-ds-getting-started-password-sync.md).


## <a name="install-and-configure-required-tools"></a>Telepítse és konfigurálja a szükséges eszközök
Az ebben a dokumentumban leírt lépések végrehajtásához a következő lehetőségek bármelyikét használhatja:
* **Az Azure PowerShell**: [telepítése és konfigurálása](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Az Azure többplatformos parancssori felület**: [telepítése és konfigurálása](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>1. lehetőség: Egy új Windows Server virtuális gép létrehozása és csatlakoztatása a felügyelt tartományhoz
**Gyors üzembe helyezési sablon neve**: [201-vm-tartományhoz való csatlakozás](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

A Windows Server virtuális gép üzembe helyezése és a felügyelt tartományhoz való csatlakozásra, hajtsa végre az alábbi lépéseket:
1. Keresse meg a [gyors üzembe helyezési sablon](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.
3. Az a **egyéni üzembe helyezés** lap, adja meg a szükséges információkat a virtuális gép kiépítése.
4. Válassza ki a **Azure-előfizetés** , amelyben üzembe helyezi a virtuális gépet. Válassza ki az Azure-előfizetéshez, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat.
5. Válasszon egy meglévő **erőforráscsoport** , vagy hozzon létre egy újat.
6. Válasszon ki egy **hely** melyben szeretné üzembe helyezni az új virtuális gépet.
7. A **meglévő virtuális hálózat neve**, adja meg a virtuális hálózatot, amelyben van üzembe helyezve az Azure AD tartományi szolgáltatásokkal felügyelt tartományban.
8. A **meglévő alhálózat neve**, adja meg az alhálózatot, ahol szeretné a virtuális gép üzembe helyezése virtuális hálózaton belül. Ne válassza az átjáró-alhálózatot a virtuális hálózatban. Nem jelölje be a kijelölt alhálózatot, amelyben a felügyelt tartomány üzembe helyezve.
9. A **DNS-címke előtagja**, adja meg a virtuális gép kiépítése folyamatban az állomásnevet. Például "contoso-win".
10. Válassza ki a megfelelő **Virtuálisgép-méret** a virtuális gép.
11. A **tartományhoz való csatlakozás**, adja meg a felügyelt tartomány DNS-tartomány nevét.
12. A **tartomány felhasználónév**, adja meg a felhasználói fiók nevét a felügyelt tartomány a virtuális gép csatlakoztatása a felügyelt tartományban használandó.
13. A **tartományi jelszó**, adja meg a "domainUsername" paraméter által hivatkozott tartományi felhasználói fiók jelszavát.
14. Nem kötelező: Megadhat egy **Szervezetiegység-útvonal** , amelyben a virtuális gép hozzáadása egy egyéni szervezeti egységbe. Ha nem ad meg egy értéket ehhez a paraméterhez, a virtuális gép bekerül az alapértelmezett **AAD DC számítógépek** szervezeti Egységet a felügyelt tartományon.
15. Az a **virtuális gép rendszergazdai felhasználónevét** mezőben adja meg a virtuális gép helyi rendszergazdai fiók nevét.
16. Az a **virtuális gép rendszergazdai jelszava** mezőben adja meg a virtuális gép helyi rendszergazdai jelszót. Adjon meg egy erős helyi rendszergazda jelszavát, a virtuális gép jelszava találgatásos támadásokkal szembeni védelem érdekében.
17. Kattintson a **elfogadom a feltételeket és a fenti feltételeket**.
18. Kattintson a **beszerzési** a virtuális gép kiépítése.

> [!WARNING]
> **Legyen körültekintő jelszavak kezelésére.**
> A sablon alkalmazásparaméter-fájlt a tartományi fiókok jelszavait, valamint a virtuális gép helyi rendszergazdai jelszavak tartalmazza. Győződjön meg arról, nem hagyja ezt a fájlt körül fekszik fájlmegosztások vagy más megosztott helyen. Javasoljuk, hogy megszabadulni tartja ezt a fájlt, ha elkészült a virtuális gépek üzembe helyezéséről.
>

Miután a telepítés sikeresen befejeződött, az újonnan létrehozott Windows virtuális gép a felügyelt tartományhoz csatlakozik.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>2. lehetőség: Egy meglévő Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományokhoz
**Gyors üzembe helyezési sablon**: [201-es vm-domain-illesztési-létező](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

A Windows Server virtuális gép csatlakoztatása felügyelt tartományokhoz, hajtsa végre az alábbi lépéseket:
1. Keresse meg a [gyors üzembe helyezési sablon](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.
3. Az a **egyéni üzembe helyezés** lap, adja meg a szükséges információkat a virtuális gép kiépítése.
4. Válassza ki a **Azure-előfizetés** , amelyben üzembe helyezi a virtuális gépet. Válassza ki az Azure-előfizetéshez, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat.
5. Válasszon egy meglévő **erőforráscsoport** , vagy hozzon létre egy újat.
6. Válasszon ki egy **hely** melyben szeretné üzembe helyezni az új virtuális gépet.
7. Az a **Virtuálisgép-lista** mezőben adja meg a meglévő virtuális gépek tartományhoz csatlakoztatni a felügyelt tartomány nevét. Az egyes Virtuálisgép-neveket használjon vesszőt. Ha például **contoso-webes, a contoso-api**.
8. A **tartományhoz való csatlakozás felhasználónév**, adja meg a felhasználói fiók nevét a felügyelt tartomány a virtuális gép csatlakoztatása a felügyelt tartományban használandó.
9. A **tartományi csatlakozás jelszó**, adja meg a "domainUsername" paraméter által hivatkozott tartományi felhasználói fiók jelszavát.
10. A **tartomány teljesen minősített Tartományneve**, adja meg a felügyelt tartomány DNS-tartomány nevét.
11. Nem kötelező: Megadhat egy **Szervezetiegység-útvonal** , amelyben a virtuális gép hozzáadása egy egyéni szervezeti egységbe. Ha nem ad meg egy értéket ehhez a paraméterhez, a virtuális gép bekerül az alapértelmezett **AAD DC számítógépek** szervezeti Egységet a felügyelt tartományon.
12. Kattintson a **elfogadom a feltételeket és a fenti feltételeket**.
13. Kattintson a **beszerzési** a virtuális gép kiépítése.

> [!WARNING]
> **Legyen körültekintő jelszavak kezelésére.**
> A sablon alkalmazásparaméter-fájlt a tartományi fiókok jelszavait, valamint a virtuális gép helyi rendszergazdai jelszavak tartalmazza. Győződjön meg arról, nem hagyja ezt a fájlt körül fekszik fájlmegosztások vagy más megosztott helyen. Javasoljuk, hogy megszabadulni tartja ezt a fájlt, ha elkészült a virtuális gépek üzembe helyezéséről.
>

A telepítés sikeres befejezése után a megadott Windows virtuális gépek csatlakoznak, a felügyelt tartományhoz.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure PowerShell áttekintése](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.0)
* [Azure gyors üzembe helyezési sablon - tartományhoz való csatlakozás egy új virtuális gép](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Azure gyors üzembe helyezési sablon - tartományhoz való csatlakozás meglévő virtuális gépek](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/resource-group-template-deploy.md)
