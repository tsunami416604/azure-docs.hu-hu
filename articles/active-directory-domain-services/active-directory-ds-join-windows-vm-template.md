---
title: "A Windows Server virtuális gép csatlakoztatása az Azure Active Directory tartományi szolgáltatások |} Microsoft Docs"
description: "A Windows Server virtuális gép csatlakoztatása felügyelt tartományhoz Azure Resource Manager-sablonok használatával."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: 8cfa13ee028b5e367158de42ceab0a1cd99d45c2
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>A Windows Server virtuális gép csatlakoztatása felügyelt tartományhoz Resource Manager-sablon használatával
Ez a cikk bemutatja, hogyan Windows Server virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz Resource Manager-sablonok használatával.

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:
1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy egy helyszíni címtár vagy egy csak felhőalapú directory szinkronizálva.
3. **Azure AD tartományi szolgáltatások** az Azure AD-címtár engedélyezni kell. Ha még nem tette meg, az összes ismertetett feladatok végrehajtásával a [első lépések útmutató](active-directory-ds-getting-started.md).
4. Győződjön meg arról, hogy a virtuális hálózat DNS-kiszolgálóként konfigurálta a felügyelt tartományra IP-címét. További információkért lásd: [az Azure virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)
5. Végezze el a szükséges lépéseket [szinkronizálja a jelszavakat az Azure AD tartományi szolgáltatások által kezelt tartomány](active-directory-ds-getting-started-password-sync.md).


## <a name="install-and-configure-required-tools"></a>Telepítse és konfigurálja a szükséges eszközök
Ebben a dokumentumban leírt lépések végrehajtásához a következő beállítások bármelyikét használhatja:
* **Az Azure PowerShell**: [telepítése és konfigurálása](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Az Azure platformfüggetlen parancssori felületre**: [telepítése és konfigurálása](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>1. lehetőség: Egy új Windows Server virtuális gép kiépítése és csatlakoztatása felügyelt tartományhoz
**Gyors üzembe helyezési sablonnév**: [201-vm-tartományhoz való csatlakozást](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Telepítse a Windows Server virtuális gépet, majd csatlakoztatása felügyelt tartományhoz, hajtsa végre az alábbi lépéseket:
1. Keresse meg a [gyors üzembe helyezési sablon](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.
3. Az a **egyéni telepítési** lapján adja meg a szükséges adatokat a virtuális gép kiépítéséhez.
4. Válassza ki a **Azure-előfizetés** , amelyben a virtuális gép kiépítéséhez. Válassza ki az Azure AD tartományi szolgáltatások engedélyezését, amelyben azonos Azure-előfizetés.
5. Válasszon egy meglévő **erőforráscsoport** vagy hozzon létre egy újat.
6. Válasszon egy **hely** , amelyben az új virtuális gép telepítéséhez.
7. A **meglévő VNET neve**, adja meg a virtuális hálózatot, amelyen telepítette az Azure AD tartományi szolgáltatások által kezelt tartomány.
8. A **meglévő alhálózati név**, adja meg az alhálózatot, hová szeretné telepíteni ezt a virtuális gépet a virtuális hálózaton belül. Ne válassza az átjáró-alhálózatot a virtuális hálózat. Emellett nem engedélyezi a dedikált alhálózat, a felügyelt tartományok van telepítve.
9. A **DNS-címke előtagja**, adja meg az állomásnevet, a virtuális gép telepítése folyamatban. Például "contoso-win".
10. Válassza ki a megfelelő **Virtuálisgép-méretet** a virtuális géphez.
11. A **tartományhoz való csatlakozás**, adja meg a felügyelt tartományok DNS-tartomány nevét.
12. A **tartományi felhasználónevet**, adja meg a felhasználói fiók nevét, amellyel a virtuális gép csatlakoztatása felügyelt tartományhoz felügyelt tartományra.
13. A **tartományi jelszó**, adja meg a "domainUsername" paraméter által hivatkozott tartományi felhasználói fiók jelszavát.
14. Nem kötelező: Megadhatja az **OU elérési** egyéni szervezeti egységhez, amelyhez a virtuális gép hozzá. Ha nem adja meg a paraméter értéke, a virtuális gép hozzáadódik az alapértelmezett **AAD DC számítógépek** szervezeti Egységet a felügyelt tartományra.
15. Az a **VM-rendszergazda felhasználóneve** mezőben adja meg a virtuális gép helyi rendszergazda fiók nevét.
16. Az a **virtuális gép rendszergazdai jelszavát** mezőben adja meg a virtuális gép helyi rendszergazdai jelszót. Adjon meg egy erős helyi rendszergazda jelszavát, a virtuális gép jelszavát találgatásos támadások ellen.
17. Kattintson a **elfogadom a feltételeket és a fenti feltételek**.
18. Kattintson a **beszerzési** a virtuális gép kiépítéséhez.

> [!WARNING]
> **Körültekintően jelszavak kezelésére.**
> A sablonfájl paraméter tartományi fiókok jelszavait, valamint a virtuális gép helyi rendszergazdai jelszót tartalmaz. Gondoskodjon arról, hogy ne maradjon a fájl fekszik körül fájlmegosztásokon vagy más megosztott helyen. Javasoljuk, hogy rendelkezési tartja ezt a fájlt, miután a virtuális gépek telepítése.
>

Miután a telepítés sikeresen befejeződött, az újonnan kiépített Windows rendszerű virtuális gép a felügyelt tartomány tagja.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>2. lehetőség: Egy meglévő Windows Server virtuális gép csatlakoztatása felügyelt tartományhoz
**Gyors üzembe helyezési sablon**: [201-es vm-tartományi-illesztési-létező](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Egy meglévő Windows Server virtuális gép csatlakoztatása felügyelt tartományhoz, hajtsa végre a következő lépéseket:
1. Keresse meg a [gyors üzembe helyezési sablon](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.
3. Az a **egyéni telepítési** lapján adja meg a szükséges adatokat a virtuális gép kiépítéséhez.
4. Válassza ki a **Azure-előfizetés** , amelyben a virtuális gép kiépítéséhez. Válassza ki az Azure AD tartományi szolgáltatások engedélyezését, amelyben azonos Azure-előfizetés.
5. Válasszon egy meglévő **erőforráscsoport** vagy hozzon létre egy újat.
6. Válasszon egy **hely** , amelyben az új virtuális gép telepítéséhez.
7. Az a **VM lista** mezőben adja meg a meglévő virtuális gépek tartományhoz csatlakoztatni a felügyelt tartomány nevét. Az egyes virtuális gépek nevénél elválasztásához használjon vesszőt. Például **contoso-webalkalmazás, a contoso-api**.
8. A **csatlakoztatni kívánt tartomány felhasználói nevét**, adja meg a felhasználói fiók nevét, amellyel a virtuális gép csatlakoztatása felügyelt tartományhoz felügyelt tartományra.
9. A **tartományi csatlakozáshoz jelszó**, adja meg a "domainUsername" paraméter által hivatkozott tartományi felhasználói fiók jelszavát.
10. A **tartomány teljesen minősített Tartományneve**, adja meg a felügyelt tartományok DNS-tartomány nevét.
11. Nem kötelező: Megadhatja az **OU elérési** egyéni szervezeti egységhez, amelyhez a virtuális gép hozzá. Ha nem adja meg a paraméter értéke, a virtuális gép hozzáadódik az alapértelmezett **AAD DC számítógépek** szervezeti Egységet a felügyelt tartományra.
12. Kattintson a **elfogadom a feltételeket és a fenti feltételek**.
13. Kattintson a **beszerzési** a virtuális gép kiépítéséhez.

> [!WARNING]
> **Körültekintően jelszavak kezelésére.**
> A sablonfájl paraméter tartományi fiókok jelszavait, valamint a virtuális gép helyi rendszergazdai jelszót tartalmaz. Gondoskodjon arról, hogy ne maradjon a fájl fekszik körül fájlmegosztásokon vagy más megosztott helyen. Javasoljuk, hogy rendelkezési tartja ezt a fájlt, miután a virtuális gépek telepítése.
>

A telepítés sikeres befejezése után a megadott Windows virtuális gépek csatlakoznak a felügyelt tartományhoz.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure PowerShell áttekintése](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.0)
* [Azure gyors üzembe helyezési sablon - tartományhoz való csatlakozás egy új virtuális gép](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Azure gyors üzembe helyezési sablon - tartományhoz való csatlakozás meglévő virtuális gépek](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/resource-group-template-deploy.md)
