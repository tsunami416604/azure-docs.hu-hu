---
title: Azure Service Management-tanúsítvány feltöltése | Microsoft Docs
description: Megtudhatja, hogyan töltheti fel a Azure Portal szolgáltatás-felügyeleti tanúsítványát.
services: cloud-services
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: article
ms.date: 08/01/2017
ms.author: gwallace
ms.openlocfilehash: 4b49a9b391eeca2d2e249b171d99f231bda6fdff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78329119"
---
# <a name="upload-an-azure-service-management-certificate"></a>Azure Service Management-tanúsítvány feltöltése
A felügyeleti tanúsítványok lehetővé teszik az Azure által biztosított klasszikus üzembehelyezési modell hitelesítését. Számos program és eszköz (például a Visual Studio vagy az Azure SDK) ezeket a tanúsítványokat használja a különböző Azure-szolgáltatások konfigurációjának és üzembe helyezésének automatizálására. 

> [!WARNING]
> légy óvatos! Az ilyen típusú tanúsítványok lehetővé teszik, hogy bárki hitelesítse őket a velük társított előfizetés kezeléséhez.
>
>

Ha további információra van szüksége az Azure-tanúsítványokról (például önaláírt tanúsítvány létrehozásáról), tekintse meg [az azure Cloud Services tanúsítványok áttekintése](cloud-services/cloud-services-certs-create.md#what-are-management-certificates)című témakört.

Az ügyfél-kód automatizálási célból történő hitelesítéséhez [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) is használhatja.

**Megjegyzés:** A felügyeleti tanúsítványokban lévő műveletek végrehajtásához az előfizetés egyik társ-rendszergazda tagjának kell lennie. [További információ](https://go.microsoft.com/fwlink/?linkid=849300) a közös rendszergazdák új Azure Portalon való hozzáadásáról és eltávolításáról 

## <a name="upload-a-management-certificate"></a>Felügyeleti tanúsítvány feltöltése
Miután létrehozott egy felügyeleti tanúsítványt (. cer fájl csak a nyilvános kulccsal), feltöltheti a portálra. Ha a tanúsítvány elérhető a portálon, a megfelelő tanúsítvánnyal (titkos kulccsal) rendelkező bárki csatlakozhat a felügyeleti API-n keresztül, és hozzáférhet a társított előfizetés erőforrásaihoz.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Az alsó Azure-szolgáltatás listában kattintson az **összes szolgáltatás** lehetőségre, majd válassza az **előfizetések** lehetőséget az _általános_ szolgáltatás csoportban.

    ![Előfizetés menü](./media/azure-api-management-certs/subscriptions_menu.png)

3. Ügyeljen arra, hogy a megfelelő előfizetést válassza ki, amelyet hozzá szeretne rendelni a tanúsítványhoz.     
4. Miután kiválasztotta a megfelelő előfizetést, nyomja le a **felügyeleti tanúsítványok** elemet a _Beállítások_ csoportban.

    ![Beállítások](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Nyomja meg a **feltöltés** gombot.

    ![Feltöltés a tanúsítványok oldalon](./media/azure-api-management-certs/certificates_page.png)
6. Töltse ki a párbeszédpanel adatait, és nyomja meg a **feltöltés**gombot.

    ![Beállítások](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>További lépések
Most, hogy rendelkezik egy előfizetéshez társított felügyeleti tanúsítvánnyal, (miután telepítette a megfelelő tanúsítványt helyileg) programozott módon kapcsolódhat a [klasszikus üzemi modellhez REST API](/azure/?pivot=sdkstools) és automatizálhatja az adott előfizetéshez kapcsolódó különböző Azure-erőforrásokat.
