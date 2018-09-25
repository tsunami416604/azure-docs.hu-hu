---
title: Egy Azure Service Management-tanúsítvány feltöltése |} A Microsoft Docs
description: Megtudhatja, hogyan töltse fel a Service Management az Azure Portalon.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeconnoc
ms.openlocfilehash: 788ce1f2543b9ed76d718cf01d28292f49594414
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037354"
---
# <a name="upload-an-azure-service-management-certificate"></a>Egy Azure Service Management-tanúsítvány feltöltése
Felügyeleti tanúsítványok lehetővé teszik a klasszikus üzemi modellben az Azure biztosítja a hitelesítéshez. Számos programok telepítése és eszközök (például a Visual Studio vagy az Azure SDK-t) konfigurálása és a különböző Azure-szolgáltatások üzembe helyezésének automatizálása ezek a tanúsítványok használatával. 

> [!WARNING]
> légy óvatos! Az ilyen típusú tanúsítványok engedélyezése bárki, aki az őket a velük társított előfizetés kezelése céljából végzi a hitelesítést.
>
>

Ha szeretne további információ az Azure tanúsítványokról (többek között önaláírt tanúsítvány létrehozása), lásd: [tanúsítványok áttekintése az Azure Cloud Services](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Is [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) automation célokra Ügyfélkód hitelesítéséhez.

**Megjegyzés:** alatt a felügyeleti tanúsítványok bármilyen műveletek végrehajtásához az előfizetés társrendszergazdájának kell lennie. [További](https://go.microsoft.com/fwlink/?linkid=849300) hozzáadása vagy eltávolítása Társrendszergazdák módjáról az új Azure Portalon 

## <a name="upload-a-management-certificate"></a>Felügyeleti tanúsítvány feltöltése
Ha már van egy felügyeleti tanúsítvány létrehozása, a (.cer-fájl a nyilvános kulccsal) feltöltheti a portálra való. Ha a tanúsítvány a portálon elérhető, bárki a megfelelő tanúsítványhoz (titkos kulcsot) a felügyeleti API-val csatlakozhat, és elérhessék az erőforrásokat a társított előfizetés.

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Kattintson a **minden szolgáltatás** , az Azure-szolgáltatás alsó listát, majd válassza ki **előfizetések** a a _általános_ szolgáltatási csoport.

    ![Előfizetés menüben](./media/azure-api-management-certs/subscriptions_menu.png)

3. Ellenőrizze, hogy helyes-e a tanúsítvány társítani kívánt előfizetés kiválasztásához.     
4. Miután kiválasztotta a megfelelő előfizetés, nyomja le az ENTER **felügyeleti tanúsítványok** a a _beállítások_ csoport.

    ![Beállítások](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Nyomja le az **feltöltése** gombra.

    ![A lapon a tanúsítványok feltöltése](./media/azure-api-management-certs/certificates_page.png)
6. Töltse ki a párbeszédpanelen információkat, majd nyomja le **feltöltése**.

    ![Beállítások](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>További lépések
Most, hogy egy előfizetés társított felügyeleti tanúsítvánnyal, helyileg a megfelelő tanúsítvány telepítése) (után programozott módon csatlakozhat a [klasszikus üzemi modell – REST API](https://msdn.microsoft.com/library/azure/mt420159.aspx) és automatizálhatja a különböző Azure-erőforrások, amelyek is az adott előfizetéshez.
