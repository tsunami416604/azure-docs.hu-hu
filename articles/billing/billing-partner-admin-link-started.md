---
title: Az Azure-fiók a Partnerazonosítóra mutató hivatkozás |} A Microsoft Docs
description: Nyomon követheti az Azure-ügyfelekkel marketingmódszerek Partnerazonosító társít ahhoz a felhasználói fiókhoz, amellyel az ügyfél-erőforrások kezelése.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 5a03805fc055583f1f965854d4da3ae6b0c90e20
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444893"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Partnerazonosító csatolása az Azure-fiókok

Partnerként követheti a hatás az ügyfélesetekből között. A Partnerazonosítóra kapcsolat egy ügyfél-erőforrások kezeléséhez használt fiókokhoz.

## <a name="get-access-from-your-customer"></a>Az ügyfél hozzáférhet

A Partnerazonosítóra kapcsol, mielőtt az ügyfél kell hozzáférést biztosít az Azure-erőforrások az alábbi lehetőségek egyikének használatával:

- **A vendégfelhasználók**: Az ügyfél egy hozzáadhat, és bármely szerepköralapú hozzáférés-vezérlés (RBAC) szerepkörök hozzárendelése. További információkért lásd: [vendég felhasználók hozzáadása másik címtárból](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Directory-fiók**: Az ügyfél is egy felhasználói fiók létrehozása az Ön számára a saját címtárban, és bármely RBAC szerepkör hozzárendelése.

- **Egyszerű szolgáltatás**: Az ügyfél hozzáadhat egy alkalmazást vagy szkriptet a szervezet a címtárban, és bármely RBAC szerepkör hozzárendelése. Az alkalmazást vagy szkriptet identitását egy egyszerű szolgáltatást is nevezik.

## <a name="link-to-a-partner-id"></a>Csatolás partnerazonosítóhoz

Az ügyfél-erőforrásokhoz való hozzáférést, ha használja az Azure portal, PowerShell vagy az Azure CLI a felhasználói azonosító vagy az egyszerű szolgáltatás a Microsoft Partner Network-azonosító (MPN-azonosító) mutat. Hivatkozás a Partnerazonosítót. minden egyes ügyfél-bérlőben.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Egy új Partnerazonosító csatolása az Azure portal használatával

1. Lépjen a [a Partnerazonosítóra mutató hivatkozás](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) az Azure Portalon.

2. Jelentkezzen be az Azure portálra.

3. Adja meg a Microsoft-partnerazonosítóját. A partner-azonosító a [Microsoft Partner Network](https://partner.microsoft.com/) Azonosítót a szervezet számára.

   ![A Partnerazonosítóra mutató hivatkozás megjelenítő képernyőkép](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Partnerazonosító csatolása egy másik ügyfél, váltson a címtárban. A **címtár váltása**, válassza ki a címtárat.

   ![Képernyőkép címtár váltása](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>A PowerShell használata egy új Partnerazonosító csatolása

1. Telepítse a [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) PowerShell-modult.

2. Jelentkezzen be az ügyfélbérlőn a felhasználói fiók vagy az egyszerű szolgáltatás. További információkért lásd: [jelentkezzen be a PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Hivatkozás az új partner. A partner-azonosító a [Microsoft Partner Network](https://partner.microsoft.com/) Azonosítót a szervezet számára.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>A csatolt partner azonosító beszerzése
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>A csatolt Partnerazonosító frissítése
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>Törölje a társított partnerazonosító Társítását
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Egy új Partnerazonosító csatolása az Azure CLI használatával
1. Az Azure CLI-bővítmény telepítése.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ``` 

2. Jelentkezzen be az ügyfélbérlőn a felhasználói fiók vagy az egyszerű szolgáltatás. További információkért lásd: [jelentkezzen be az Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ``` 

3. Hivatkozás az új partner. A partner-azonosító a [Microsoft Partner Network](https://partner.microsoft.com/) Azonosítót a szervezet számára.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>A csatolt partner azonosító beszerzése
```azurecli-interactive
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>A csatolt Partnerazonosító frissítése
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>Törölje a társított partnerazonosító Társítását
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
``` 

## <a name="next-steps"></a>További lépések

Csatlakozzon a beszélgetéshez a [Microsoft Partnerközösségéhez](https://aka.ms/PALdiscussion) kapják a frissítéseket, vagy küldjön visszajelzést.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**A partner Partnerazonosítóját kapcsolhatja ki?**

A fiók minden felhasználónak az ügyfél Azure-erőforrások felügyelő a fiókpartner-szervezet kapcsolat a partnerazonosító Társítását.

**Partnerazonosító után módosítható kapcsolt?**

Igen. Társított Partnerazonosító megváltozott, hozzáadni vagy eltávolítani.

**Mi történik, ha egy felhasználónak egynél több ügyfél-bérlőben van fiókja?**

A partner Partnerazonosítóját és a fiók közötti kapcsolat történik, az egyes vevő bérlők számára. Hivatkozás a Partnerazonosítót. minden egyes ügyfél-bérlőben.

**Más partnerekkel vagy az ügyfelek számára szerkesztheti vagy a Partnerazonosítóra mutató hivatkozás eltávolítása?**

A hivatkozás a felhasználói fiók szintjén társítva. Csak szerkesztheti, vagy távolítsa el a hivatkozásra kattintva a partnerazonosítóját. Az ügyfél és más partnerek nem lehet módosítani a hivatkozást a partnerazonosítóját. 


**Melyik MPN-azonosító kell használni, ha a vállalata rendelkezik több?**

Használhat bármilyen érvényes MPN-azonosító, kivéve az előzménylistákat orgnization(v-org) MPN-azonosító. A legtöbb partnerek az MPN-azonosító használata a földrajzi hely, ahol az ügyfél-alapú vagy szolgáltatások vannak célszolgáltatásnak válassza.

**Hol található a Partnerazonosítójával társított reporting befolyásolt bevétel?**

Befolyásolt bevételi jelentési címen található [saját irányítópult](https://partner.microsoft.com/membership/reports/myinsights). Válassza ki a Partner felügyeleti hivatkozás partner társítási típust kell.

**Miért nem látom a saját felhasználói a jelentések?**

Az ügyfél a következő okok miatt a jelentések nem láthatók

1. A társított felhasználói fiók nem rendelkezik [szerepkör alapú hozzáférés ](https://docs.microsoft.com/azure/role-based-access-control/overview) bármely ügyfél Azure-előfizetés vagy az erőforrás.

2. Ha a felhasználó nem rendelkezik Azure-előfizetés [szerepkör alapú hozzáférés ](https://docs.microsoft.com/azure/role-based-access-control/overview) hozzáférés nem rendelkezik a használatot.

**Nem hivatkozni azonosító használható az Azure Stack partner?**

Igen, a Partnerazonosítóra csatolhatja az Azure Stackhez.

