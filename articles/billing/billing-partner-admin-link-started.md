---
title: Azure-fiókot azonosító partnernek hivatkozás |} Microsoft Docs
description: Azure-ügyfél bevonására nyomon Partnerazonosító létrehozhatja, az ügyfél-erőforrások kezeléséhez használt felhasználói fiók.
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: abab0e63f91ad34d2671c37773d47c31eeeb8339
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Az Azure-fiókra való hivatkozást Partnerazonosító 
Partnerként nyomon követheti a hatás között a felhasználói kapcsolattartás során létrehozhatja a partner Partnerazonosítóját, az ügyfél-erőforrások kezelése által használt fiókok.

Ez a szolgáltatás egy nyilvános előzetes verziójában érhető el. 

## <a name="get-access-from-your-customer"></a>Az ügyfél elérheti 
Mielőtt a hivatkozás a partner Partnerazonosítóját, az ügyfél kell hozzáférést biztosít az Azure-erőforrások az alábbi lehetőségek egyikének használatával:

- **Vendég felhasználó:** az ügyfél vendégfelhasználóként egy hozzáadhat, és Szerepalapú szerepköröket hozzárendelni. További információkért lásd: [egy másik címtárból adja hozzá a vendégfelhasználók](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Fiók:** az ügyfél új felhasználó létrehozása a könyvtárban a szervezet, és Szerepalapú szerepköröket hozzárendelni. 

- **Szolgáltatásnév:** az ügyfél egy alkalmazás vagy a parancsfájl hozzáadása a saját szervezetétől a könyvtárban, és bármely RBAC szerepkör hozzárendelése. Az alkalmazás vagy parancsprogram egyszerű néven ismert.

## <a name="link-partner-id"></a>Hivatkozás Partnerazonosítót.
Ha az ügyfél erőforrásokhoz való hozzáférést, az a Microsoft Partner hálózati azonosító (MPN azonosító) kapcsolódik a felhasználói azonosító vagy az egyszerű szolgáltatásnév használja a PowerShell vagy a parancssori felület. Kell kapcsolni a Partnerazonosítót. az egyes felhasználói bérlők számára. 

### <a name="use-powershell-to-link-new-partner-id"></a>Csatolás új Partnerazonosító a PowerShell használatával

1. Telepítse a [AzurePartnerRP](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner/0.1.0-preview) PowerShell-modult.

2. Jelentkezzen be a felhasználói fiók, valamint az egyszerű szolgáltatásnév, további információ az ügyfél bérlői című [Powershell történő bejelentkezés](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Hivatkozás az új partnerazonosítójával. A partner-azonosító a [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) a szervezet azonosítója.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>A csatolt Partnerazonosítót beolvasása
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>A csatolt Partnerazonosítót frissítése
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>A csatolt Partnerazonosítót törlése
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>Új Partnerazonosító hivatkozás parancssori felület használatával
1.  A CLI-kiterjesztés telepítése.

    ```azure-cli
    C:\ az extension add --name managementpartner
    ``` 

2.  Jelentkezzen be a felhasználói fiók vagy az egyszerű szolgáltatásnév az ügyfél-bérlő. További információkért lásd: [jelentkezzen be Azure CLI 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azure-cli
    C:\ az login --tenant <tenant>
    ``` 


3.  Hivatkozás az új partnerazonosítójával. A partner-azonosító a [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) a szervezet azonosítója.

     ```azure-cli
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>A csatolt Partnerazonosítót beolvasása
```azure-cli
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>A csatolt Partnerazonosítót frissítése
```azure-cli
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>A csatolt Partnerazonosítót törlése
```azure-cli
C:\ az managementpartner delete --partner-id 12345
``` 


## <a name="frequently-asked-questions"></a>Gyakori kérdések

**A partner Partnerazonosítóját kapcsolhatja ki?**

A fiókpartner-szervezet ügyfél erőforrás kezelő felhasználói a fiókjához társíthatja a Partnerazonosítót.
  

**Miután a partner Partnerazonosítóját kapcsolódó azt módosíthatja?**

Igen, a csatolt Partnerazonosító módosítható, hozzá, vagy eltávolítani.

**Mi történik, ha egy felhasználó több ügyfél bérlő rendelkezik fiókkal?**

A hivatkozás a partner Partnerazonosítóját, és a fiók között az egyes felhasználói bérlők történik.  Akkor kell kapcsolni a partner Partnerazonosítóját, minden ügyfél-bérlőben.

**Más partner vagy ügyfél módosíthatja vagy eltávolíthatja a partner Partnerazonosítóját mutató hivatkozást?**

A hivatkozás a fiók szintjén társítva. Csak szerkessze vagy távolítsa el a hivatkozásra kattintva a partnerazonosítójával. Az ügyfél és a másik partnert nem állítható be a hivatkozást a partnerazonosítójával. 


