---
title: Az Azure AD tartományi szolgáltatások nem egyező címtárhibáinak kijavítása | Microsoft dokumentumok
description: Megtudhatja, hogy mit jelent a nem egyező címtárhiba, és hogyan oldható meg az Azure AD tartományi szolgáltatásokban
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 6ff12ce5fec8fcc49fa21ef5f3009fc2283300c4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654827"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Az Azure AD tartományi szolgáltatások meglévő felügyelt tartományai nem egyező címtárhibáinak megoldása

Ha egy Azure Active Directory tartományi szolgáltatások (Azure AD DS) felügyelt tartományban egy nem egyező bérlői hiba, nem tudja felügyelni a felügyelt tartományt, amíg fel nem oldotta. Ez a hiba akkor fordul elő, ha az alapul szolgáló Azure virtuális hálózat egy másik Azure AD-könyvtárba kerül.

Ez a cikk ismerteti, hogy miért fordul elő a hiba, és hogyan lehet megoldani.

## <a name="what-causes-this-error"></a>Mi okozza ezt a hibát?

Nem egyező címtárhiba akkor fordul elő, ha egy Azure AD DS felügyelt tartomány és virtuális hálózat két különböző Azure AD-bérlőhöz tartozik. Például előfordulhat, hogy egy Azure AD DS felügyelt tartomány nevű *aaddscontoso.com,* amely a Contoso Azure AD-bérlő. A felügyelt tartomány hoz tartozó Azure virtuális hálózat azonban a Fabrikam Azure AD-bérlő része.

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használatával korlátozza az erőforrásokhoz való hozzáférést. Ha engedélyezi az Azure AD DS-t egy Azure AD-bérlőben, a hitelesítő adatok kikeresései szinkronizálódnak a felügyelt tartománnyal. Ez a művelet megköveteli, hogy az Azure AD-címtár bérlői rendszergazdája legyen, és a hitelesítő adatokhoz való hozzáférést szabályozni kell. Erőforrások üzembe helyezéséhez egy Azure virtuális hálózatra, és a forgalom szabályozása, rendszergazdai jogosultságokkal kell rendelkeznie a virtuális hálózaton, amelyben az Azure AD DS üzembe helyezéséhez.

Ahhoz, hogy az RBAC következetesen és biztonságosan hozzáférhessen az Azure AD DS által használt összes erőforráshoz, a felügyelt tartománynak és a virtuális hálózatnak ugyanahhoz az Azure AD-bérlőhöz kell tartoznia.

Az Erőforrás-kezelő környezetben a következő szabályok érvényesek:

- Egy Azure AD-címtár ban több Azure-előfizetések is rendelkezhetnek.
- Egy Azure-előfizetés több erőforrással is rendelkezhet, például virtuális hálózatokkal.
- Egyetlen Azure AD tartományi szolgáltatások felügyelt tartomány engedélyezve van egy Azure AD-címtárban.
- Az Azure AD tartományi szolgáltatások által felügyelt tartomány engedélyezhető egy virtuális hálózaton tartozó bármely Azure-előfizetések ugyanazon Az Azure AD-bérlőn belül.

### <a name="valid-configuration"></a>Érvényes konfiguráció

A következő példában a központi telepítési forgatókönyvben a Contoso Azure AD DS felügyelt tartomány a Contoso Azure AD-bérlőben engedélyezve van. A felügyelt tartomány egy virtuális hálózatban van telepítve, amely a Contoso Azure AD-bérlő tulajdonában lévő Azure-előfizetéshez tartozik. A felügyelt tartomány és a virtuális hálózat is ugyanahhoz az Azure AD-bérlőhöz tartozik. Ez a példa konfiguráció érvényes és teljes mértékben támogatott.

![Érvényes Azure AD DS-bérlői konfiguráció ugyanannak az Azure AD-bérlőnek a felügyelt tartomány- és virtuális hálózati részével](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Nem egyező bérlői konfiguráció

Ebben a példában a központi telepítési forgatókönyvben a Contoso Azure AD DS felügyelt tartomány a Contoso Azure AD-bérlőben engedélyezve van. A felügyelt tartomány azonban egy virtuális hálózatban van telepítve, amely a Fabrikam Azure AD-bérlő tulajdonában lévő Azure-előfizetéshez tartozik. A felügyelt tartomány és a virtuális hálózat két különböző Azure AD-bérlőhöz tartozik. Ez a példa konfiguráció egy nem egyező bérlő, és nem támogatott. A virtuális hálózatot ugyanabba az Azure AD-bérlőbe kell áthelyezni, mint a felügyelt tartományba.

![Nem egyező bérlői konfiguráció](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Nem egyező bérlői hiba megoldása

A következő két lehetőség megoldja a nem egyező könyvtárhibát:

* [Törölje az Azure AD DS felügyelt tartományt](delete-aadds.md) a meglévő Azure AD-címtárból. [Hozzon létre egy helyettesítő Azure AD DS felügyelt tartományt](tutorial-create-instance.md) ugyanabban az Azure AD-címtárban, mint a használni kívánt virtuális hálózat. Ha készen áll, csatlakozzon a törölt tartományhoz korábban csatlakozott összes géphez az újralétrehozott felügyelt tartományhoz.
* Helyezze át a virtuális hálózatot tartalmazó [Azure-előfizetést](../cost-management-billing/manage/billing-subscription-transfer.md) ugyanabba az Azure AD-címtárba, mint az Azure AD DS felügyelt tartománya.

## <a name="next-steps"></a>További lépések

Az Azure AD DS szolgáltatással kapcsolatos problémák elhárításáról a [hibaelhárítási útmutatóban](troubleshoot.md)talál további információt.
