---
title: Azure-fiók összekapcsolása partner-AZONOSÍTÓval | Microsoft Docs
description: Az Azure-ügyfelekkel folytatott kapcsolatok nyomon követése a partner AZONOSÍTÓjának a felhasználói fiókhoz való összekapcsolásával, amelyet az ügyfél erőforrásainak kezeléséhez használ.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 03/12/2019
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 0448ffbccddc913bd6359f5f6bbf42988239afb4
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706408"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Partner-azonosító csatolása az Azure-fiókokhoz

A Microsoft-partnerek olyan szolgáltatásokat biztosítanak, amelyek segítségével az ügyfelek a Microsoft termékeivel üzleti és küldetési célokat érhetnek el. Ha az ügyfél nevében az Azure-szolgáltatásokat kezeli, konfigurálja és támogatja, akkor a partnereknek hozzá kell férniük az ügyfél környezetéhez. A partneri rendszergazdai hivatkozás használatával a partnerek a szolgáltatás kézbesítéséhez használt hitelesítő adatokkal társítják a partner hálózati AZONOSÍTÓját.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Hozzáférés kérése az ügyféltől

A partner-azonosító csatolása előtt az ügyfélnek az alábbi lehetőségek egyikével kell hozzáférést biztosítania az Azure-erőforrásokhoz:

- **Vendég felhasználó**: Az ügyfél felveheti Önt vendég felhasználóként, és hozzárendelhet bármilyen szerepköralapú hozzáférés-vezérlési (RBAC) szerepkört. További információ: [vendég felhasználók hozzáadása egy másik címtárból](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Directory-fiók**: Az ügyfél létrehozhat egy felhasználói fiókot saját címtárában, és hozzárendelhet bármely RBAC-szerepkört.

- **Egyszerű szolgáltatásnév**: Az ügyfél hozzáadhat egy alkalmazást vagy parancsfájlt a szervezetében a címtárában, és hozzárendelhet bármilyen RBAC-szerepkört. Az alkalmazás vagy a szkript identitása az egyszerű szolgáltatásnév.

## <a name="link-to-a-partner-id"></a>Csatolás partnerazonosítóhoz

Ha hozzáfér az ügyfél erőforrásaihoz, használja a Azure Portal, a PowerShell vagy az Azure CLI-t az Microsoft Partner Network-azonosító (MPN-azonosító) a felhasználói AZONOSÍTÓhoz vagy az egyszerű szolgáltatáshoz való csatolásához. A partner AZONOSÍTÓjának összekapcsolása minden ügyfél-bérlőben.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>A Azure Portal használata új partner-AZONOSÍTÓra való hivatkozáshoz

1. Válassza a Azure Portal [partner-azonosítóra mutató hivatkozást](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) .

2. Jelentkezzen be az Azure portálra.

3. Adja meg a Microsoft partner AZONOSÍTÓját. A partner azonosítója a szervezet [Microsoft Partner Network](https://partner.microsoft.com/) azonosítója.

   ![A partner-AZONOSÍTÓra mutató hivatkozást megjelenítő képernyőkép](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Ha egy másik ügyfélhez szeretne partner-azonosítót kapcsolni, váltson át a címtárra. A **váltás könyvtár**alatt válassza ki a címtárat.

   ![A Switch könyvtárat bemutató képernyőkép](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Új partner-AZONOSÍTÓra mutató hivatkozás használata a PowerShell használatával

1. Telepítse az az [. ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) PowerShell-modult.

2. Jelentkezzen be az ügyfél bérlője számára a felhasználói fiókkal vagy az egyszerű szolgáltatással. További információ: [Bejelentkezés a PowerShell-](https://docs.microsoft.com/powershell/azure/authenticate-azureps)lel.

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Az új partner-AZONOSÍTÓra mutató hivatkozás. A partner azonosítója a szervezet [Microsoft Partner Network](https://partner.microsoft.com/) azonosítója.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>A csatolt partner AZONOSÍTÓjának beolvasása
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>A csatolt partner AZONOSÍTÓjának frissítése
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>A csatolt partner AZONOSÍTÓjának törlése
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Új partner-AZONOSÍTÓra mutató hivatkozás használata az Azure CLI-vel
1. Telepítse az Azure CLI-bővítményt.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Jelentkezzen be az ügyfél bérlője számára a felhasználói fiókkal vagy az egyszerű szolgáltatással. További információ: [Bejelentkezés az Azure CLI-vel](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Az új partner-AZONOSÍTÓra mutató hivatkozás. A partner azonosítója a szervezet [Microsoft Partner Network](https://partner.microsoft.com/) azonosítója.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>A csatolt partner AZONOSÍTÓjának beolvasása
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>A csatolt partner AZONOSÍTÓjának frissítése
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>A csatolt partner AZONOSÍTÓjának törlése
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>További lépések

Csatlakozzon a [Microsoft partner Közösség](https://aka.ms/PALdiscussion) vitafórumához a frissítések fogadásához és a visszajelzések elküldéséhez.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Kik kapcsolhatják össze a partner AZONOSÍTÓját?**

Az ügyfél Azure-erőforrásait kezelő partnervállalat bármely felhasználója összekapcsolhatja a partner AZONOSÍTÓját a fiókkal.

**Módosulhat a partner azonosítója a csatolása után?**

Igen. A csatolt partner AZONOSÍTÓját megváltoztathatja, hozzáadhatja vagy eltávolíthatja.

**Mi a teendő, ha egy felhasználó több ügyfél-bérlőn is rendelkezik fiókkal?**

A partner-azonosító és a fiók közötti kapcsolat minden ügyfél bérlője számára elkészült. A partner AZONOSÍTÓjának összekapcsolása minden ügyfél-bérlőben.

**Más partnerek vagy ügyfelek szerkeszthetik vagy törölhetik a partner-AZONOSÍTÓra mutató hivatkozást?**

A hivatkozás a felhasználói fiók szintjén van társítva. Csak a partner-AZONOSÍTÓra mutató hivatkozást szerkesztheti vagy távolíthatja el. Az ügyfél és más partnerek nem változtathatják meg a partner-AZONOSÍTÓra mutató hivatkozást.


**Melyik MPN-azonosítót kell használni, ha a vállalatom több?**

A partneri hely fiókjait és a hozzájuk tartozó MPN-azonosítókat kell használni a partner-azonosító összekapcsolásához.  További információ a [partneri fiókokról](https://docs.microsoft.com/partner-center/account-structure)

**Hol találhatok befolyásolt bevételi jelentést a csatolt partner AZONOSÍTÓhoz?**

A Cloud Product Performance Reporting szolgáltatás a partner Centerben lévő partnerek számára érhető el a [saját](https://partner.microsoft.com/membership/reports/myinsights)bepillantások irányítópultján. Partneri társítási típusként ki kell választania a partneri rendszergazda hivatkozást.

**Miért nem látom az ügyfelem a jelentésekben?**

A következő okok miatt nem látja az ügyfelet a jelentésekben

1. A csatolt felhasználói fiók nem rendelkezik [szerepköralapú hozzáféréssel](https://docs.microsoft.com/azure/role-based-access-control/overview) az Azure-előfizetésekhez vagy-erőforrásokhoz.

2. Az Azure-előfizetés, amelyben a felhasználó [szerepkör-alapú hozzáférési](https://docs.microsoft.com/azure/role-based-access-control/overview) hozzáférése nincs használatban.

**Működik a link partner azonosítója a Azure Stack?**

Igen, összekapcsolhatja a partner AZONOSÍTÓját Azure Stackhoz.
