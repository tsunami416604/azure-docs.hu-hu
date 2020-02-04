---
title: Azure-fiók csatolása egy partnerazonosítóhoz | Microsoft Docs
description: Kövesse nyomon az Azure-ügyfelekkel való együttműködéseket a partnerazonosító csatolásával ahhoz a felhasználói fiókhoz, amelyet az ügyfélerőforrások kezelésére használ.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 10/01/2019
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a67f2985e2db8c48d7e50a91d20c76b88c1c55e6
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "75991918"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Partnerazonosítók csatolása az Azure-fiókhoz

A Microsoft-partnerek olyan szolgáltatásokat biztosítanak, amelyek segítségével az ügyfelek a Microsoft termékeinek használatával elérhetik üzleti és stratégiai célkitűzéseiket. Ha egy ügyfél nevében az Azure-szolgáltatásokat kezeli, konfigurálja és támogatja, akkor a partnerfelhasználóknak hozzá kell férniük az ügyfél környezetéhez. A partnerrendszergazdai hivatkozás használatával a partnerek társítani tudják a partnerhálózati azonosítójukat a szolgáltatás teljesítéséhez használt hitelesítőadatokkal.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Hozzáférés kérése az ügyféltől

A partnerazonosító csatolása előtt ügyfelének hozzáférést kell adnia Önnek az Azure-erőforrásokhoz a következő lehetőségek valamelyikének használatával:

- **Vendégfelhasználó**: Ügyfele hozzáadhatja Önt vendégfelhasználóként, és bármilyen szerepköralapú hozzáférés-vezérlési (RBAC) szerepkört hozzárendelhet. További információkért tekintse meg a [Vendégfelhasználók hozzáadása másik címtárból](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) szakaszt.

- **Címtárfiók**: Az ügyfél létrehozhat egy felhasználói fiókot az Ön számára a saját címtárában, és bármilyen RBAC-szerepkört hozzárendelhet.

- **Egyszerű szolgáltatás**: Az ügyfél a címtárában hozzáadhat az Ön szervezetéből származó alkalmazást vagy szkriptet, és hozzárendelhet bármilyen RBAC-szerepkört. Az alkalmazás vagy szkript identitása szolgáltatásnévként ismert.

## <a name="link-to-a-partner-id"></a>Csatolás partnerazonosítóhoz

Amikor hozzáfér az ügyfelek erőforrásaihoz, az Azure Portalt, a PowerShellt vagy az Azure CLI-t használja, hogy Microsoft Partner Network-azonosítóját (MPN ID) a fiókazonosítóhoz vagy a szolgáltatásnévhez csatolja. Partnerazonosító csatolása az egyes ügyfélbérlőkben.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Az Azure Portal használata egy új partnerazonosítóhoz való csatolásához

1. Lépjen a [Csatolás partnerazonosítóhoz](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) területre az Azure Portalon.

2. Jelentkezzen be az Azure portálra.

3. Adja meg a Microsoft-partnerazonosítót. A partnerazonosító megegyezik szervezet [Microsoft Partner Network](https://partner.microsoft.com/)-azonosítójával.

   ![A Csatolás partnerazonosítóhoz funkciót megjelenítő képernyőkép](./media/link-partner-id/link-partner-id01.png)

4. Ha egy másik ügyfélhez szeretne partnerazonosítót kapcsolni, váltson át a címtárra. A **Címtár váltása** területen válassza ki a címtárat.

   ![A Címtár váltása funkciót mutató képernyőkép](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>A PowerShell használata egy új partnerazonosítóhoz való csatolásához

1. Telepítse az [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) PowerShell-modult.

2. Jelentkezzen be a felhasználói fiókkal vagy a szolgáltatásnévvel az ügyfél bérlőjébe. További információkért lásd: [Bejelentkezés a PowerShell-lel](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Csatolás az új partnerazonosítóhoz. A partnerazonosító megegyezik szervezet [Microsoft Partner Network](https://partner.microsoft.com/)-azonosítójával.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>A csatolt partnerazonosító lekérése
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>A csatolt partnerazonosító frissítése
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>A csatolt partnerazonosító törlése
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Az Azure CLI használata egy új partnerazonosítóhoz való csatolásához
1. Telepítse az Azure CLI-bővítményt.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Jelentkezzen be a felhasználói fiókkal vagy a szolgáltatásnévvel az ügyfél bérlőjébe. További információkért lásd: [Bejelentkezés az Azure CLI-vel](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Csatolás az új partnerazonosítóhoz. A partnerazonosító megegyezik szervezet [Microsoft Partner Network](https://partner.microsoft.com/)-azonosítójával.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>A csatolt partnerazonosító lekérése
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>A csatolt partnerazonosító frissítése
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>A csatolt partnerazonosító törlése
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>További lépések

A hírekért és visszajelzések küldéséhez csatlakozzon a [Microsoft-partnerközösség](https://aka.ms/PALdiscussion) vitafórumához.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Ki csatolhat partnerazonosítót?**

A partnerszervezet bármely, az ügyfél Azure-erőforrásait kezelő felhasználója csatolhat a fiókhoz partnerazonosítót.

**A csatolás után módosítható a partnerazonosító?**

Igen. A csatolt partnerazonosító módosítható, hozzáadható vagy eltávolítható.

**Mi történik, ha egy felhasználó több ügyfélbérlőben is rendelkezik fiókkal?**

A partnerazonosító és a fiók közötti hivatkozás minden ügyfél bérlője számára elkészül. Partnerazonosító csatolása az egyes ügyfélbérlőkben.

**Más partnerek vagy ügyfelek is szerkeszthetik vagy eltávolíthatják a partnerazonosítóra mutató hivatkozást?**

A hivatkozás a felhasználói fiók szintjéhez kapcsolódik. Csak Ön szerkesztheti vagy távolíthatja el a partnerazonosítóra mutató hivatkozást. Az ügyfél vagy más partnerek nem szerkeszthetik vagy távolíthatják el a partnerazonosítóra mutató hivatkozást.


**Melyik MPN-azonosítót kell használnom, ha a vállalat többel is rendelkezik?**

A partnerazonosító csatolásához a partneri hely fiókjait és a hozzájuk tartozó MPN-azonosítókat kell használnia.  További tudnivalók a [partnerfiókokról](https://docs.microsoft.com/partner-center/account-structure)

**Hol találhatok befolyásolt bevételjelentést a csatolt partnerazonosítóhoz?**

A Cloud Product Performance jelentéskészítő szolgáltatás a Partner Centerben a [Saját információk irányítópultján](https://partner.microsoft.com/membership/reports/myinsights) érhető el a partnerek számára. Ott a partner társítási típusaként a partnerrendszergazda hivatkozást kell kiválasztania.

**Miért nem látom az ügyfelemet a jelentésekben?**

A következő lehetséges okok miatt nem jelenik meg az ügyfél a jelentésekben

1. A csatolt felhasználói fiók nem rendelkezik [szerepköralapú hozzáféréssel](https://docs.microsoft.com/azure/role-based-access-control/overview) egyik ügyfél Azure-előfizetéséhez vagy erőforrásához sem.

2. Nincs használatban az Azure-előfizetés, amelyben a felhasználó [szerepköralapú hozzáféréssel](https://docs.microsoft.com/azure/role-based-access-control/overview) rendelkezik.

**Működik-e a partnerazonosító csatolása funkció az Azure Stackkel?**

Igen, partnerazonosítóját csatolhatja az Azure Stackhez.
