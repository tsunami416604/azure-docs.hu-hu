---
title: "Adja hozzá a bérlők használatának és számlázási Azure verem |} Microsoft Docs"
description: "A szükséges lépéseket a felhasználó hozzáadása a Felhőszolgáltató által felügyelt Azure-verem."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 67e5a67d7cd5caf6bd4d2625969b139411d62696
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Adja hozzá a bérlő használati és számlázási Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ez a cikk azt ismerteti, hogyan hozzáadása a végfelhasználó által egy Felhőszolgáltató (CSP) által felügyelt Azure-verem. Az új tenanthoz erőforrást használ, amikor Azure verem tartozik használati a CSP-előfizetést.

CSP gyakran szolgáltatást kínál az Azure Alkalmazásveremben üzembe több ügyfélnek (bérlőkkel). Bérlők felvétele az Azure-verem regisztráció biztosítja, hogy minden bérlő használati jelentett és a megfelelő CSP előfizetésre terhelve. Ha nem fejeződik be a cikkben leírt lépéseket, bérlők Azure verem kezdeti regisztrációhoz használt előfizetés van rendelve. Hozzáadása előtt az ügyfél egy záró Azure verem használat nyomon követése és a bérlő felügyeletéhez, szüksége lesz Azure verem beállítása egy CSP-hez. A lépéseket és az erőforrásokra, lásd: [kezelése a használati és a Felhőbeli szolgáltató Azure verem számlázási](azure-stack-add-manage-billing-as-a-csp.md).

Az alábbi ábrán látható, amelyet egy CSP-hez kell ahhoz, hogy egy új ügyfél Azure verem használható, és használat nyomon követése az ügyfél beállításához kövesse a lépéseket. Az end ügyfél hozzáadásával fogja verem Azure-erőforrások kezeléséhez. Az erőforrások kezelésére szolgáló két lehetőség közül választhat:

1. A záró ügyfél bérlő karbantartása, és adja meg a hitelesítő adatokat a helyi verem Azure-előfizetéshez az end ügyfélnek.  
2. Vagy az end ügyfél helyileg előfizetésének dolgozni, és adja hozzá a kriptográfiai Szolgáltató vendégként tulajdonos engedélyekkel.  

**Az end felhasználóinál felvételének lépéseiről**

![Használat nyomon követése és kezelheti a végfelhasználók felhasználói fiók Felhőbeli szolgáltatás szolgáltatója beállítása](media\azure-stack-csp-enable-billing-usage-tracking\process-csp-enable-billing.png)

## <a name="create-a-new-customer-in-partner-center"></a>Hozzon létre egy új ügyfél Partnerközpontjában

Hozzon létre egy új Azure-előfizetés Partnerközpontját, az ügyfél. Útmutatásért lásd: [hozzáadása egy új ügyfél](https://msdn.microsoft.com/partner-center/add-a-new-customer).


##  <a name="create-an-azure-subscription-for-the-end-customer"></a>Az end ügyfél Azure-előfizetés létrehozása

Miután létrehozta az ügyfél egy olyan rekordot a Partnerközpontját, is értékesít őket a katalógusban szereplő termékek előfizetések. Útmutatásért lásd: [létrehozása, felfüggeszteni, vagy szakítsa meg az ügyfél-előfizetések](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

## <a name="create-a-guest-user-in-the-end-customer-directory"></a>Az end ügyfél Directory Vendég felhasználó létrehozása

Az end ügyfél kezeli a saját fiókjukat, ha a Vendég felhasználó létrehozása a könyvtárban, és küldje el az adatokat. A végfelhasználó fog majd vegye fel a Vendég és a Vendég engedéllyel jogosultságszintjének **tulajdonos** az Azure verem CSP-fiókhoz.
 
## <a name="update-the-registration-with-the-end-customer-subscription"></a>A záró ügyfél-előfizetést a regisztráció frissítése

A regisztráció frissítése az új ügyfél-előfizetést. Azure a Partner központi felhasználói azonosítójának használatával az ügyfél használati jelentést. Ez a lépés biztosítja, hogy minden ügyfél-használati jelentett tartozó ügyfél egyedi CSP előfizetésen belül. Ez megkönnyíti a felhasználó használatának követéséről és számlázási sokkal.

> [!Note]  
> Ez a lépés végrehajtásához rendelkeznie kell [regisztrált Azure verem](azure-stack-register.md).

1. Nyissa meg a Windows PowerShell egy rendszergazdai jogú parancssorba, majd futtassa:  
    `Login-AzureRmAccount`
2. Írja be Azure hitelesítő adatait.
3. A PowerShell-munkamenetben futtassa:

```powershell
    New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```
### <a name="new-azurermresource-powershell-parameters"></a>Új AzureRmResource PowerShell paraméterek
| Paraméter | Leírás |
| --- | --- | 
|registrationSubscriptionID | Az Azure-előfizetés, amely a kezdeti regisztráció az Azure-vermet lett megadva. |
| customerSubscriptionID | Az Azure-előfizetés (nem Azure-verem) tartozó regisztrálni kell az ügyfél. Kell létrehozni a CSP ajánlat; a gyakorlatban ez azt jelenti, hogy Partner központon keresztül történik. Ha az ügyfél egynél több Azure Active Directory-bérlőt, ehhez az előfizetéshez kell létrehozni a bérlőt, jelentkezzen be Azure verem használható.
| resourceGroup | Az erőforráscsoport, amely a regisztrációs tárolja az Azure-ban. 
| registrationName | A regisztráció az Azure-készlet neve. Az Azure-ban tárolt objektum. | 


> [!Note]  
> Bérlők regisztrálva kell lennie az egyes Azure veremnek megfelelő használnak. Ha két Azure verem központi telepítések, és a bérlő üzemeltetéséhez kívánja használni, mindkettő, módosítania a kezdeti regisztráció az adott környezet a bérlői előfizetéssel.

## <a name="onboard-tenant-to-azure-stack"></a>A bevezetni bérlői Azure verem

Konfigurálja az Azure verem szolgáltatások használatához Azure-készletben több Azure AD-bérlő felhasználók támogatására. Útmutatásért lásd: [engedélyezése az Azure-készletben a több bérlős](azure-stack-enable-multitenancy.md).


## <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Hozzon létre egy helyi erőforrást az Azure verem vége ügyfél bérlőnél

Miután hozzáadta az új ügyfél Azure verem, vagy a záró ügyfél bérlő engedélyezve van a Vendég fiók tulajdonosának jogosultságokkal, győződjön meg arról, hogy egy erőforrás hozhat létre a bérlőben. Például a következőkre [Windows virtuális gép létrehozása Azure verem Portal](user\azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>További lépések

 - Tekintse át a hibaüzeneteket, ha a regisztrációs folyamat során el, lásd: [regisztrációs hibaüzenetek bérlői](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
 - Hogyan lehet lekérni az erőforrás-használati adatait az Azure oszlopból kapcsolatos további információkért lásd: [használati és számlázási Azure verem](/azure-stack-billing-and-chargeback.md).
 - Tekintse át, hogyan egy záró ügyfél lehet, hogy adja hozzá, a CSP-hez, az Azure-verem kezelőként bérlői. További információ: [engedélyezése az Azure-verem előfizetés kezelése céljából, a Felhőbeli szolgáltatás szolgáltatója](user\azure-stack-csp-enable-billing-usage-tracking.md).