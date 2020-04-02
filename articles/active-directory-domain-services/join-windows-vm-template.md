---
title: Windows Virtuális gép csatlakozása az Azure AD DS-hez | Microsoft dokumentumok
description: Megtudhatja, hogy miként csatlakozhat új vagy meglévő Windows Server virtuális géphez egy Új Vagy meglévő Windows Server virtuális géphez egy Azure Active Directory tartományi szolgáltatások által felügyelt tartományhoz.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 19e4033b462cda9511ee45420bd4c1a76cc36b92
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518955"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Csatlakozás Windows Server virtuális géphez egy Azure Active Directory tartományi szolgáltatások által kezelt tartományhoz Erőforrás-kezelő sablon használatával

Az Azure virtuális gépek (VM-ek) üzembe helyezésének és konfigurálásának automatizálásához használhatja a Resource Manager-sablont. Ezek a sablonok lehetővé teszik, hogy minden alkalommal egységes központi telepítéseket hozzon létre. A bővítmények is szerepelhetnek a sablonokban, hogy automatikusan konfigurálja a virtuális gép a központi telepítés részeként. Egy hasznos bővítmény csatlakozik a virtuális gépek egy tartományhoz, amely használható az Azure Active Directory tartományi szolgáltatások (Azure AD DS) felügyelt tartományok.

Ez a cikk bemutatja, hogyan hozhat létre és csatlakozhat egy Windows Server virtuális géphez egy Azure AD DS által felügyelt tartományhoz az Erőforrás-kezelő sablonok használatával. Azt is megtudhatja, hogyan csatlakozhat egy meglévő Windows Server virtuális géphez egy Azure AD DS-tartományhoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén az első oktatóanyag [létrehoz és konfigurál egy Azure Active Directory tartományi szolgáltatások példányát.][create-azure-ad-ds-instance]
* Egy felhasználói fiók, amely az Azure AD DS felügyelt tartomány ának része.

## <a name="azure-resource-manager-template-overview"></a>Az Azure Resource Manager-sablon – áttekintés

Az Erőforrás-kezelő sablonjai lehetővé teszik az Azure-infrastruktúra kódban való definiálását. A szükséges erőforrások, hálózati kapcsolatok vagy a virtuális gépek konfigurációja egy sablonban definiálható. Ezek a sablonok minden alkalommal egységes, reprodukálható központi telepítéseket hoznak létre, és a módosítások végrehajtása kor verzióváltozattal is verziózhatók. További információt az [Azure Resource Manager-sablonok – áttekintés című témakörben talál.][template-overview]

Minden erőforrás javascript-objektumnotúcés (JSON) használatával egy sablonban van definiálva. A következő JSON-példa a *Microsoft.Compute/virtualMachines/extensions* erőforrástípust használja az Active Directory tartományillesztésbővítmény telepítéséhez. A rendszer a központi telepítéskor megadott paramétereket használja. A bővítmény üzembe helyezésekor a virtuális gép csatlakozik a megadott Azure AD DS felügyelt tartományhoz.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Ez a virtuálisgép-bővítmény akkor is telepíthető, ha nem hoz létre virtuális gép ugyanabban a sablonban. A jelen cikkben szereplő példák az alábbi módszereket mutatják be:

* [Windows Server virtuális gép létrehozása és csatlakozás felügyelt tartományhoz](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Meglévő Windows Server virtuális gép csatlakoztatása felügyelt tartományhoz](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Windows Server virtuális gép létrehozása és csatlakozás felügyelt tartományhoz

Ha Windows Server virtuális gépre van szüksége, létrehozhat és konfigurálhat egyet egy Erőforrás-kezelő sablon használatával. A virtuális gép üzembe helyezésekor egy bővítmény telepítve lesz, hogy csatlakozzon a virtuális géphez egy Azure AD DS felügyelt tartományhoz. Ha már rendelkezik olyan virtuális gépsel, amelyhez csatlakozni szeretne egy Azure AD DS felügyelt tartományához, ugorjon [egy meglévő Windows Server virtuális géphez egy felügyelt tartományhoz való csatlakozása lehetőségre.](#join-an-existing-windows-server-vm-to-a-managed-domain)

Windows Server virtuális gép létrehozásához, majd csatlakozzon egy Azure AD DS felügyelt tartományhoz, hajtsa végre az alábbi lépéseket:

1. Tallózással keresse meg a [rövid útmutató sablont.](https://azure.microsoft.com/resources/templates/201-vm-domain-join/) Válassza ki az **Azure-ba való üzembe helyezés**lehetőségét.
1. Az **Egyéni telepítés** lapon adja meg a következő adatokat a Windows Server virtuális gép létrehozásához és az Azure AD DS felügyelt tartományhoz való csatlakozásához:

    | Beállítás                   | Érték |
    |---------------------------|-------|
    | Előfizetés              | Válassza ki ugyanazt az Azure-előfizetést, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat. |
    | Erőforráscsoport            | Válassza ki a virtuális gép erőforráscsoportját. |
    | Hely                  | Válassza ki a helyét a virtuális gép. |
    | Meglévő virtuális hálózat neve        | A virtuális gépet csatlakoztatni, például a *myVnet-hez*csatlakoztatandó meglévő virtuális hálózat neve. |
    | Meglévő alhálózat neve      | A meglévő virtuális hálózati alhálózat neve, például *a Munkaterhelések.* |
    | DNS-címke előtagja          | Adjon meg egy DNS-nevet a virtuális géphez, például *a myvm.* |
    | Virtuális gép mérete                   | Adja meg a virtuális gép méretét, például *Standard_DS2_v2.* |
    | A csatlakozáshoz csatlakozó tartomány            | Az Azure AD DS felügyelt tartomány DNS-neve, például *aaddscontoso.com*. |
    | Tartomány felhasználóneve           | Az Azure AD DS felügyelt tartományának felhasználói fiókja, amelyet a virtuális gépnek a felügyelt tartományhoz való csatlakoztatásához kell használni, például `contosoadmin@aaddscontoso.com`. Ennek a fióknak az Azure AD DS felügyelt tartományának részét kell, hogy legyen. |
    | Tartományi jelszó           | Az előző beállításban megadott felhasználói fiók jelszava. |
    | Választható szervezeti egység elérési útja          | Az egyéni szervezeti egység, amelyben hozzáadja a virtuális gép. Ha nem ad meg értéket ehhez a paraméterhez, a virtuális gép hozzáadódik az alapértelmezett *AAD DC-számítógépek szervezeti* egységéhez. |
    | Virtuális gép felügyeleti felhasználóneve         | Adja meg a helyi rendszergazdai fiókot, amelyet a virtuális gépen kell létrehozni. |
    | Virtuális gép rendszergazdai jelszava         | Adja meg a helyi rendszergazdai jelszót a virtuális géphez. Hozzon létre egy erős helyi rendszergazdai jelszót a jelszónyers támadások elleni védelem érdekében. |

1. Tekintse át a feltételeket, majd jelölje be a Bejelölő **az Elfogadom a fent meghatározott feltételeket**. Ha készen áll, válassza **a Vásárlás** lehetőséget a virtuális gép létrehozásához és az Azure AD DS felügyelt tartományhoz való csatlakozásához.

> [!WARNING]
> **Óvatosan kezelje a jelszavakat.**
> A sablon paraméterfájl az Azure AD DS felügyelt tartományának részét tartalmazó felhasználói fiók jelszavát kéri. Ne adjon meg manuálisan értékeket ebbe a fájlba, és ne hagyja elérhetővé a fájlmegosztásokon vagy más megosztott helyeken.

A telepítés sikeres befejezéséhez néhány percet vesz igénybe. Ha elkészült, a Windows virtuális gép jön létre, és csatlakozott az Azure AD DS felügyelt tartományhoz. A virtuális gép tartományi fiókok használatával kezelhető vagy bejelentkezhető.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Meglévő Windows Server virtuális gép csatlakoztatása felügyelt tartományhoz

Ha rendelkezik egy meglévő virtuális gép, vagy a virtuális gépek csoportja, amely szeretne csatlakozni egy Azure AD DS felügyelt tartományhoz, használhatja a Resource Manager sablon csak a virtuálisgép-bővítmény üzembe helyezéséhez.

Ha meglévő Windows Server virtuális géphez szeretne csatlakozni egy Azure AD DS által felügyelt tartományhoz, hajtsa végre az alábbi lépéseket:

1. Tallózással keresse meg a [rövid útmutató sablont.](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/) Válassza ki az **Azure-ba való üzembe helyezés**lehetőségét.
1. Az **egyéni üzembe helyezés** lapon adja meg a következő adatokat, hogy csatlakozzon a virtuális gép az Azure AD DS felügyelt tartományhoz:

    | Beállítás                   | Érték |
    |---------------------------|-------|
    | Előfizetés              | Válassza ki ugyanazt az Azure-előfizetést, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat. |
    | Erőforráscsoport            | Válassza ki az erőforráscsoportot a meglévő virtuális gép. |
    | Hely                  | Válassza ki a meglévő virtuális gép helyét. |
    | Virtuális gép listája                   | Adja meg a meglévő virtuális gép(ek) vesszővel tagolt listáját, hogy csatlakozzon az Azure AD DS felügyelt tartományához, például *a myVM1,myVM2*tartományhoz. |
    | Tartományhoz való csatlakozás felhasználóneve     | Az Azure AD DS felügyelt tartományának felhasználói fiókja, amelyet a virtuális gépnek a felügyelt tartományhoz való csatlakoztatásához kell használni, például `contosoadmin@aaddscontoso.com`. Ennek a fióknak az Azure AD DS felügyelt tartományának részét kell, hogy legyen. |
    | Tartományi csatlakozás felhasználói jelszava | Az előző beállításban megadott felhasználói fiók jelszava. |
    | Választható szervezeti egység elérési útja          | Az egyéni szervezeti egység, amelyben hozzáadja a virtuális gép. Ha nem ad meg értéket ehhez a paraméterhez, a virtuális gép hozzáadódik az alapértelmezett *AAD DC-számítógépek szervezeti* egységéhez. |

1. Tekintse át a feltételeket, majd jelölje be a Bejelölő **az Elfogadom a fent meghatározott feltételeket**. Ha készen áll, válassza **a Vásárlás** lehetőséget, hogy csatlakozzon a virtuális géphez az Azure AD DS felügyelt tartományhoz.

> [!WARNING]
> **Óvatosan kezelje a jelszavakat.**
> A sablon paraméterfájl az Azure AD DS felügyelt tartományának részét tartalmazó felhasználói fiók jelszavát kéri. Ne adjon meg manuálisan értékeket ebbe a fájlba, és ne hagyja elérhetővé a fájlmegosztásokon vagy más megosztott helyeken.

A központi telepítés sikeres befejezéséhez néhány percet vesz igénybe. Ha elkészült, a megadott Windows virtuális gépek csatlakoznak az Azure AD DS felügyelt tartományhoz, és tartományi fiókok használatával kezelhetők vagy bejelentkezhetők.

## <a name="next-steps"></a>További lépések

Ebben a cikkben az Azure Portal használatával konfigurálhatja és üzembe helyezte az erőforrásokat sablonok használatával. Erőforrásokat is üzembe helyezhet az [Azure PowerShell][deploy-powershell] vagy az [Azure CLI][deploy-cli]használatával Erőforrás-kezelő sablonokkal.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
