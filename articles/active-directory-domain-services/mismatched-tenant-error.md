---
title: Nem egyező címtárbeli hibák megoldása a Azure AD Domain Servicesban | Microsoft Docs
description: A meglévő Azure AD Domain Services felügyelt tartományokhoz tartozó eltérő címtárbeli hibák megismerése és megoldása
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 676efa155c85ab371ec41c49ad0c15eb2bd5a24a
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234008"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>A meglévő Azure AD Domain Services felügyelt tartományokhoz tartozó eltérő címtárbeli hibák elhárítása
Meglévő Azure AD Domain Services felügyelt tartománya van. Amikor megnyitja a Azure Portal és megtekinti a felügyelt tartományt, a következő hibaüzenet jelenik meg:

![Nem egyező könyvtári hiba](./media/getting-started/mismatched-tenant-error.png)

Ez a felügyelt tartomány csak a hiba feloldása után felügyelhető.


## <a name="whats-causing-this-error"></a>Mi okozza ezt a hibát?
Ezt a hibát a felügyelt tartomány és a virtuális hálózat két különböző Azure AD-bérlőhöz való bekapcsolása okozta. Tegyük fel, hogy rendelkezik egy "contoso.com" nevű felügyelt tartománnyal, és engedélyezve volt a contoso Azure AD-bérlője számára. Azonban a felügyelt tartományt engedélyező Azure-beli virtuális hálózat a fabrikam – egy másik Azure AD-bérlőhöz tartozik.

Az új Azure Portal (és különösen a Azure AD Domain Services bővítmény) a Azure Resource Managerra épül. A modern Azure Resource Manager környezetekben bizonyos korlátozások kényszerítve lettek a nagyobb biztonság és a szerepköralapú hozzáférés-vezérlés (RBAC) erőforrásokhoz való továbbítására. Az Azure AD-bérlők Azure AD Domain Servicesának engedélyezése érzékeny művelet, mivel a hitelesítő adatok kivonatait szinkronizálja a felügyelt tartományba. Ehhez a művelethez a címtár bérlői rendszergazdájának kell lennie. Emellett rendszergazdai jogosultságokkal kell rendelkeznie a felügyelt tartományt engedélyező virtuális hálózaton. Ahhoz, hogy a RBAC-ellenőrzések konzisztensen működjenek, a felügyelt tartománynak és a virtuális hálózatnak ugyanahhoz az Azure AD-bérlőhöz kell tartoznia.

Röviden, nem engedélyezhető felügyelt tartomány a "contoso.com" Azure AD-bérlő számára egy másik Azure AD-bérlő (fabrikam.com) tulajdonában lévő Azure-előfizetéshez tartozó virtuális hálózatban. 

**Érvényes konfiguráció**: Ebben az üzembe helyezési forgatókönyvben a contoso által felügyelt tartomány engedélyezve van a contoso Azure AD-bérlő számára. A felügyelt tartomány egy, a contoso Azure AD-bérlőhöz tartozó Azure-előfizetéshez tartozó virtuális hálózaton elérhető. Ezért mind a felügyelt tartomány, mind a virtuális hálózat ugyanahhoz az Azure AD-bérlőhöz tartozik. Ez a konfiguráció érvényes és teljes mértékben támogatott.

![Érvényes bérlői konfiguráció](./media/getting-started/valid-tenant-config.png)

Nem **egyező bérlői konfiguráció**: Ebben az üzembe helyezési forgatókönyvben a contoso által felügyelt tartomány engedélyezve van a contoso Azure AD-bérlő számára. A felügyelt tartomány azonban a fabrikam Azure AD-bérlő által birtokolt Azure-előfizetéshez tartozó virtuális hálózatban érhető el. Ezért a felügyelt tartomány és a virtuális hálózat két különböző Azure AD-bérlőhöz tartozik. Ez a konfiguráció nem egyezik a bérlői konfigurációval, és nem támogatott. A virtuális hálózatot felügyelt tartományként át kell helyezni ugyanahhoz az Azure AD-bérlőhöz (azaz contoso). A részletekért tekintse meg a [megoldási](#resolution) szakaszt.

![Nem egyező bérlői konfiguráció](./media/getting-started/mismatched-tenant-config.png)

Ezért ha a felügyelt tartomány és a virtuális hálózat engedélyezve van a-ben, két különböző Azure AD-bérlőhöz tartozik, ezt a hibát látja.

A Resource Manager-környezetben a következő szabályok érvényesek:
- Az Azure AD-címtár több Azure-előfizetéssel is rendelkezhet.
- Az Azure-előfizetések több erőforrással rendelkezhetnek, például virtuális hálózatokkal.
- Egyetlen Azure AD Domain Services felügyelt tartomány van engedélyezve egy Azure AD-címtárhoz.
- Egy Azure AD Domain Services felügyelt tartomány engedélyezhető egy olyan virtuális hálózaton, amely az adott Azure AD-bérlőn belül bármely Azure-előfizetéshez tartozhat.


## <a name="resolution"></a>Megoldás:
Két lehetőség közül választhat a nem egyező könyvtár hibáinak elhárításához. A következőket teheti:

- A meglévő felügyelt tartomány törléséhez kattintson a **Törlés** gombra. Hozza létre újból a [Azure Portal](https://portal.azure.com)használatával, hogy a felügyelt tartomány és virtuális hálózat elérhető legyen az Azure ad-címtárban. Csatlakoztassa a korábban a törölt tartományhoz csatlakozó összes gépet az újonnan létrehozott felügyelt tartományhoz.

- Helyezze át a virtuális hálózatot tartalmazó Azure-előfizetést az Azure AD-címtárba, amelyhez a felügyelt tartomány tartozik. Kövesse az [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../billing/billing-subscription-transfer.md) című cikket.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD Domain Services – Első lépések útmutató](create-instance.md)
* [Hibaelhárítási útmutató – Azure AD Domain Services](troubleshoot.md)
