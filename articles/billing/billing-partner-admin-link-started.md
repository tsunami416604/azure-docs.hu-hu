---
title: Az Azure-fiókot azonosító partnerré |} A Microsoft Docs
description: Nyomon követheti az Azure-ügyfelekkel marketingmódszerek Partnerazonosító társít ahhoz a felhasználói fiókhoz, amellyel az ügyfél-erőforrások kezelése.
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ddfe83bf4d10eb3ee9b61ee1f60e60a965047cf9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980201"
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Az Azure-fiókokhoz való hivatkozás Partnerazonosítóra

Partnerként nyomon követheti a hatás az ügyfélesetekből között a Partnerazonosítóra létrehozhatja, ha az ügyfél-erőforrások kezeléséhez használt fiókok.

Ez a funkció nyilvános előzetes verzióban érhető el.

## <a name="get-access-from-your-customer"></a>Az ügyfél hozzáférhet

A Partnerazonosítóra kapcsol, mielőtt az ügyfél kell hozzáférést biztosít az Azure-erőforrások az alábbi lehetőségek egyikének használatával:

- **A vendégfelhasználók:** az ügyfelek meg vendégként hozzáadhat, és bármely RBAC-szerepkörök hozzárendelése. További információkért lásd: [vendég felhasználók hozzáadása másik címtárból](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Directory-fiók:** az ügyfél új felhasználó létrehozása a szervezet a címtárban, és bármely RBAC szerepkör hozzárendelése.

- **Szolgáltatásnév:** az ügyfél hozzáadhat egy alkalmazást vagy szkriptet a szervezet saját címtárban és bármely RBAC szerepkör hozzárendelése. Az alkalmazást vagy szkriptet identitása egyszerű szolgáltatás néven ismert.

## <a name="link-partner-id"></a>Hivatkozás partnerazonosítóra

Ha az ügyfél-erőforrásokhoz való hozzáférés, használatával az Azure portal, PowerShell vagy parancssori felület mutató hivatkozást a Microsoft Partner Network-azonosító (MPN-azonosító) a felhasználói azonosító vagy az egyszerű szolgáltatás. Hogy a partnerazonosító társítását, minden egyes ügyfél-bérlőben.

### <a name="use-azure-portal-to-link-new-partner-id"></a>Új Partnerazonosító csatolása az Azure portal használatával

1. Lépjen a [a Partnerazonosítóra mutató hivatkozás](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) az Azure Portalon.

2. Jelentkezzen be az Azure portálra.

3. Adja meg a Microsoft-partnerazonosítóját. A partner-azonosító a [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) a szervezet azonosítója.

  ![Hivatkozás Partnerazonosítóra bemutató képernyőkép](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Partnerazonosító csatolása egy másik ügyfél, hogy a címtárváltóval. Címtár váltása válassza ki a címtárban.

  ![Hivatkozás Partnerazonosítóra bemutató képernyőkép](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-new-partner-id"></a>Új Partnerazonosító csatolása a PowerShell használatával

1. Telepítse a [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) PowerShell-modult.

2. Jelentkezzen be a felhasználói fiók vagy szolgáltatás egyszerű, további információ az ügyfél-bérlőhöz, lásd: [bejelentkezési a PowerShell-lel](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Hivatkozás az új partner. A partner-azonosító a [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) a szervezet azonosítója.

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

### <a name="use-cli-to-link-new-partner-id"></a>Új Partnerazonosító csatolása a parancssori felület használatával
1.  A CLI-bővítmény telepítése.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ``` 

2.  Jelentkezzen be az ügyfélbérlőn a felhasználói fiók vagy az egyszerű szolgáltatás. További információkért lásd: [bejelentkezés az Azure CLI-vel](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ``` 

3.  Hivatkozás az új partner. A partner-azonosító a [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) a szervezet azonosítója.

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

A fiók a fiókpartner-szervezet ügyfél erőforrás kezelő felhasználói kapcsolat a partnerazonosító Társítását.

**Miután Partnerazonosító össze van kapcsolva, akkor módosítható?**

Igen, a csatolt Partnerazonosító módosítható, hozzáadva, vagy eltávolítani.

**Mi történik, ha egy felhasználói fiókkal rendelkezik több ügyfél-bérlő?**

A partner Partnerazonosítóját és a fiók közötti kapcsolat történik, az egyes vevő bérlők számára.  Hogy a partnerazonosító társítását, minden egyes ügyfél-bérlőben.

**Más partner vagy -ügyfél szerkesztheti vagy a Partnerazonosítóra mutató hivatkozás eltávolítása?**

A kapcsolat a fiók szintjén társítva. Csak szerkesztheti, vagy távolítsa el a hivatkozásra kattintva a partnerazonosítóját. Az ügyfelek és egyéb partneri nem lehet módosítani a hivatkozást a partnerazonosítóját. 
