---
title: "Az Azure külső szolgáltatási díjak megismeréséhez |} Microsoft Docs"
description: "További információk a számlázási külső szolgáltatások, korábbi nevén a piactéren, az Azure-ban díjakat."
services: 
documentationcenter: 
author: adpick
manager: tonguyen
editor: 
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: adpick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64bfd8581141001aa6c11ca17ec1af681054f490
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Az Azure számlázás külső szolgáltatás költségek ismertetése
Külső szolgáltatások az Azure piactéren harmadik féltől származó szoftver szállítója kerülnek közzétételre. Például ClearDB és SendGrid a külső szolgáltatások, Azure-ban vásárolhatnak, de nem a Microsoft által közzétett.

Ha egy új külső szolgáltatás vagy az erőforrás, figyelmeztetés jelenik meg:

![Piactér-beszerzési figyelmeztetés](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Külső vállalatok, amelyek nem Microsoft által közzétett szolgáltatások, de más Microsoft-termékek is kategóriába sorolni külső szolgáltatások.
> 
> 

## <a name="how-external-services-are-billed"></a>Hogyan külső szolgáltatások számlázása
- Külső szolgáltatások külön vannak számlázva. Az Azure-előfizetéshez belül egyedi rendelések tekintendők. A számlázott időszak minden egyes szolgáltatás beállítása során vásárol a szolgáltatás. Nem keverendő össze a számlázott időszak az előfizetés, amely alatt amelytől vásárolta. Külön váltók is kap, és a hitelkártya felszámítása külön történik.
- Egyes külső szolgáltatásnak van egy másik számlázási modellt. Egyes szolgáltatások számlázása a használatalapú módon történt mások havi alapján fizetési modell használatára. Hitelkártya Azure külső-szolgáltatások van szüksége, külső szolgáltatások számla fizetéssel nem vásárolhat.
- Ingyenes havi krediteket külső szolgáltatások nem használható. Ha használ, amely tartalmazza az Azure-előfizetés [kreditek szabad](https://azure.microsoft.com/pricing/spending-limits/), azok külső szolgáltatás váltók nem alkalmazható. Hitelkártya segítségével vásárolja meg a külső szolgáltatásait.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Nézet külső szolgáltatás költségeik és az Azure portálon előzményei
A külső szolgáltatások, az egyes előfizetések belül listáját megtekintheti a [Azure-portálon](https://portal.azure.com/): 

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) a fiókadminisztrátora.
2. A központ menüben válassza ki **előfizetések**.
   
    ![A központ menüben válassza az előfizetések](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. Az a **előfizetések** panelen, jelölje ki a megtekinteni kívánt előfizetést, és válassza **külső szolgáltatások**.
   
    ![Válassza ki az előfizetés számlázási a panelen](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Minden, a külső szolgáltatás rendelések, a közzétevő neve, szolgáltatásréteg vásárolt, az erőforrás, és a rendelés állapota megadott névnek meg kell jelennie. Korábbi számlák megtekintéséhez válasszon ki egy külső szolgáltatást.
   
    ![Válasszon ki egy külső szolgáltatást](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. Itt tekintheti meg többek között a adó lebontása számlázási összegek részhez.
   
    ![Külső szolgáltatások számlázási előzmények megtekintése](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Nagyvállalati Szerződés (EA) ügyfelek esetén ebből külső tartalom megtekintése
Nagyvállalati ügyfelek Lásd: a külső szolgáltatás beszállítói költségeit, és töltse le a EA portál jelentéseiben. Lásd: [Azure piactér a Nagyvállalati ügyfelek](https://ea.azure.com/helpdocs/azureMarketplace) a kezdéshez.

## <a name="manage-payment-methods-for-external-service-orders"></a>Külső szolgáltatás rendelések fizetési módok kezelése
Frissíti a fizetési módok, a külső szolgáltatás rendelést a [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> Ha a munkahelyi vagy iskolai fiókkal vásárolt [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) módosíthatja a fizetési módot.
> 
> 

1. Jelentkezzen be a [Account Center](https://account.windowsazure.com/) és [navigáljon a **piactér** lap](https://account.windowsazure.com/Store)
   
    ![Válassza ki az account center piactér](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Válassza ki a kezelni kívánt külső szolgáltatást
   
    ![Válassza ki a kezelni kívánt külső szolgáltatást](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Kattintson a **fizetési mód megváltoztatása** a lap jobb oldalán. Ez a hivatkozás egy másik portálon kezelheti a fizetési mód elérését.
   
    ![Rendelés összegzése](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Kattintson a **adatainak szerkesztése** és útmutatást a fizetési adatok.
   
    ![Válassza ki a Szerkesztés adatai](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Egy külső szolgáltatás rendelés visszavonása
Ha meg kívánja szakítani a külső szolgáltatás sorrendben, az erőforrás törlése a [Azure-portálon](https://portal.azure.com).

![Erőforrás törlése](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha további kérdései, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.

