---
title: Windows Server rendszerű virtuális gép csatlakoztatása Azure Active Directory Domain Serviceshoz | Microsoft Docs
description: Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz Azure Resource Manager sablonok használatával.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 599d474b7c45274c87878c622149a86bc93af318
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612269"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz Resource Manager-sablon használatával
Ez a cikk bemutatja, hogyan csatlakozhat egy Windows Server rendszerű virtuális géphez egy Azure AD Domain Services felügyelt tartományhoz Resource Manager-sablonok használatával.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben felsorolt feladatok elvégzéséhez a következőkre lesz szüksége:
1. Érvényes **Azure-előfizetés**.
2. Egy **Azure ad-címtár** – szinkronizálva van egy helyszíni címtárral vagy egy csak felhőalapú címtárral.
3. **Azure ad Domain Services** engedélyezni kell az Azure ad-címtárat. Ha még nem tette meg, kövesse az [első lépések útmutatóban](tutorial-create-instance.md)ismertetett összes feladatot.
4. Győződjön meg arról, hogy a felügyelt tartomány IP-címeit a virtuális hálózat DNS-kiszolgálóinak megfelelően konfigurálta. További információ: [Az Azure-beli virtuális hálózat DNS-beállításainak frissítése](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Hajtsa végre a [jelszavak Azure ad Domain Services felügyelt tartományhoz](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)való szinkronizálásához szükséges lépéseket.


## <a name="install-and-configure-required-tools"></a>Szükséges eszközök telepítése és konfigurálása
A jelen dokumentumban ismertetett lépések végrehajtásához a következő lehetőségek bármelyikét használhatja:
* **Azure PowerShell**: [Telepítés és konfigurálás](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Azure CLI**: [Telepítés és konfigurálás](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>1\. lehetőség: Új Windows Server-alapú virtuális gép kiépítése és csatlakoztatása felügyelt tartományhoz
rövid útmutató **sablonjának neve**: [201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Windows Server rendszerű virtuális gép üzembe helyezéséhez és egy felügyelt tartományhoz való csatlakoztatásához hajtsa végre a következő lépéseket:
1. Navigáljon a [Gyorsindítás sablonhoz](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.
3. Az **Egyéni telepítés** lapon adja meg a virtuális gép kiépítéséhez szükséges adatokat.
4. Válassza ki azt az **Azure** -előfizetést, amelyben ki szeretné építeni a virtuális gépet. Válassza ki ugyanazt az Azure-előfizetést, amelyben engedélyezte a Azure AD Domain Services.
5. Válasszon egy meglévő **erőforráscsoportot** , vagy hozzon létre egy újat.
6. Válasszon egy **helyet** az új virtuális gép üzembe helyezéséhez.
7. A **meglévő VNET neve**mezőben adja meg azt a virtuális hálózatot, amelyben a Azure ad Domain Services felügyelt tartományt telepítette.
8. A **meglévő alhálózat neve**mezőben adja meg azt a virtuális hálózaton belüli alhálózatot, amelyre telepíteni szeretné a virtuális gépet. Ne válassza az átjáró alhálózatát a virtuális hálózatban. Ne jelölje ki azt a dedikált alhálózatot, amelyben a felügyelt tartományt üzembe helyezi.
9. A **DNS-címke előtagja**mezőben határozza meg az üzembe helyezni kívánt virtuális gép állomásnevét. Például: "contoso-win".
10. Válassza ki a virtuális gép megfelelő virtuálisgép- **méretét** .
11. A **tartományhoz való csatlakozáshoz**adja meg a felügyelt tartomány DNS-tartománynevét.
12. A **tartomány felhasználóneve**mezőben adja meg annak a felhasználói fióknak a nevét a felügyelt tartományon, amelyet a virtuális gép felügyelt tartományhoz való csatlakoztatásához kíván használni.
13. A **tartományi jelszó**mezőben határozza meg az "domainUsername" paraméter által hivatkozott tartományi felhasználói fiók jelszavát.
14. Nem kötelező: Megadhat egy **szervezeti útvonalat** egy egyéni szervezeti egységhez, amelyben a virtuális gépet hozzá lehet adni. Ha nem ad meg értéket ehhez a paraméterhez, a rendszer hozzáadja a virtuális gépet az alapértelmezett **HRE DC számítógépek** szervezeti egységhez a felügyelt tartományon.
15. A virtuális gép **rendszergazdai felhasználóneve** mezőben adja meg a virtuális gép helyi rendszergazdai fiókjának nevét.
16. A virtuális gép **rendszergazdai jelszava** mezőben válasszon egy helyi rendszergazdai jelszót a virtuális géphez. Erős helyi rendszergazdai jelszót adjon meg a virtuális géphez a jelszó-kényszerített támadásokkal szembeni védelem érdekében.
17. Kattintson **az Elfogadom a fenti feltételeket és**kikötéseket lehetőségre.
18. A virtuális gép kiépítéséhez kattintson a **vásárlás** elemre.

> [!WARNING]
> **A jelszavakat körültekintően kezelheti.**
> A sablon-paraméter fájl a tartományi fiókok jelszavait, valamint a virtuális gép helyi rendszergazdai jelszavait tartalmazza. Ügyeljen arra, hogy ne hagyja a fájlt a fájlmegosztás vagy más megosztott tárolóhelyek köré. Javasoljuk, hogy a virtuális gépek üzembe helyezése után távolítsa el ezt a fájlt.
>

Miután az üzembe helyezés sikeresen befejeződött, az újonnan kiosztott Windows-alapú virtuális gép csatlakoztatva van a felügyelt tartományhoz.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>2\. lehetőség: Meglévő Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz
rövid útmutató sablonja: [201-vm-domain-join-existing](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Ha meglévő Windows Server rendszerű virtuális gépet szeretne csatlakoztatni egy felügyelt tartományhoz, hajtsa végre a következő lépéseket:
1. Navigáljon a [Gyorsindítás sablonhoz](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.
3. Az **Egyéni telepítés** lapon adja meg a virtuális gép kiépítéséhez szükséges adatokat.
4. Válassza ki azt az **Azure** -előfizetést, amelyben ki szeretné építeni a virtuális gépet. Válassza ki ugyanazt az Azure-előfizetést, amelyben engedélyezte a Azure AD Domain Services.
5. Válasszon egy meglévő **erőforráscsoportot** , vagy hozzon létre egy újat.
6. Válasszon egy **helyet** az új virtuális gép üzembe helyezéséhez.
7. A virtuálisgép- **lista** mezőben határozza meg a felügyelt tartományhoz csatlakoztatni kívánt meglévő virtuális gépek nevét. Használjon vesszőt az egyes virtuális gépek nevének elkülönítéséhez. Például: **contoso-web, contoso-API**.
8. A **tartományhoz való csatlakozás felhasználóneve**mezőben adja meg a felügyelt tartomány felhasználói fiókjának nevét, amelyet a virtuális gép a felügyelt tartományhoz való csatlakoztatásához kíván használni.
9. A **tartományhoz való csatlakozás felhasználói jelszava**mezőben határozza meg az "domainUsername" paraméter által hivatkozott tartományi felhasználói fiók jelszavát.
10. A **tartomány teljes tartományneve**mezőben adja meg a felügyelt tartomány DNS-tartománynevét.
11. Nem kötelező: Megadhat egy **szervezeti útvonalat** egy egyéni szervezeti egységhez, amelyben a virtuális gépet hozzá lehet adni. Ha nem ad meg értéket ehhez a paraméterhez, a rendszer hozzáadja a virtuális gépet az alapértelmezett **HRE DC számítógépek** szervezeti egységhez a felügyelt tartományon.
12. Kattintson **az Elfogadom a fenti feltételeket és**kikötéseket lehetőségre.
13. A virtuális gép kiépítéséhez kattintson a **vásárlás** elemre.

> [!WARNING]
> **A jelszavakat körültekintően kezelheti.**
> A sablon-paraméter fájl a tartományi fiókok jelszavait, valamint a virtuális gép helyi rendszergazdai jelszavait tartalmazza. Ügyeljen arra, hogy ne hagyja a fájlt a fájlmegosztás vagy más megosztott tárolóhelyek köré. Javasoljuk, hogy a virtuális gépek üzembe helyezése után távolítsa el ezt a fájlt.
>

Miután az üzembe helyezés sikeresen befejeződött, a megadott Windows-alapú virtuális gépek csatlakoznak a felügyelt tartományhoz.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure PowerShell áttekintése](/powershell/azure/overview)
* [Azure gyors üzembe helyezési sablon – tartomány csatlakoztatása egy új virtuális géphez](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Azure gyors üzembe helyezési sablon – tartományhoz való csatlakozás meglévő virtuális gépekhez](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/resource-group-template-deploy.md)
