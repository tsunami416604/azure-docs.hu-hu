---
title: Azure szolgáltatáskezelési tanúsítvány feltöltése | Microsoft dokumentumok
description: Ismerje meg, hogyan töltheti fel a Service Management tanúsítványt az Azure Portalon.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329119"
---
# <a name="upload-an-azure-service-management-certificate"></a>Azure szolgáltatáskezelési tanúsítvány feltöltése
A felügyeleti tanúsítványok lehetővé teszik a hitelesítést az Azure által biztosított klasszikus üzembe helyezési modellel. Számos program és eszköz (például a Visual Studio vagy az Azure SDK) ezeket a tanúsítványokat használja a különböző Azure-szolgáltatások konfigurációjának és üzembe helyezésének automatizálására. 

> [!WARNING]
> légy óvatos! Az ilyen típusú tanúsítványok lehetővé teszik, hogy bárki, aki hitelesíti magát vele, kezelje azt az előfizetést, amelyhez társítva van.
>
>

Ha további információra van szüksége az Azure-tanúsítványokról (beleértve az önaláírt tanúsítvány létrehozását is), olvassa el [az Azure Cloud Services tanúsítványok – áttekintéscímű témakört.](cloud-services/cloud-services-certs-create.md#what-are-management-certificates)

Az Azure [Active Directory](https://azure.microsoft.com/services/active-directory/) használatával is hitelesítheti az ügyfélkódot automatizálási célokra.

**Megjegyzés:** Az előfizetésben társadminisztrátornak kell lennie ahhoz, hogy a Felügyeleti tanúsítványok csoportban bármilyen műveletet végrehajtson. [További információ](https://go.microsoft.com/fwlink/?linkid=849300) a társgazdák hozzáadásáról és eltávolításáról az új Azure Portalról 

## <a name="upload-a-management-certificate"></a>Felügyeleti tanúsítvány feltöltése
Miután létrehozott egy felügyeleti tanúsítványt(.cer fájl csak a nyilvános kulccsal), feltöltheti azt a portálra. Ha a tanúsítvány elérhető a portálon, bárki, aki rendelkezik egy megfelelő tanúsítvánnyal (személyes kulccsal), csatlakozhat a Felügyeleti API-n keresztül, és hozzáférhet a társított előfizetés erőforrásaihoz.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Kattintson az **Összes szolgáltatás** az alsó Azure-szolgáltatáslistában, majd válassza **az Előfizetések** az _Általános_ szolgáltatáscsoportban lehetőséget.

    ![Előfizetés menü](./media/azure-api-management-certs/subscriptions_menu.png)

3. Győződjön meg arról, hogy a tanúsítványhoz társítani kívánt megfelelő előfizetést választja ki.     
4. Miután kiválasztotta a megfelelő előfizetést, nyomja meg a Beállítások csoport **Felügyeleti tanúsítványok** _billentyűt._

    ![Beállítások](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Nyomja meg a **Feltöltés** gombot.

    ![Feltöltés a tanúsítványok lapra](./media/azure-api-management-certs/certificates_page.png)
6. Töltse ki a párbeszédpanel adatait, és nyomja **le a Feltöltés gombot.**

    ![Beállítások](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>További lépések
Most, hogy rendelkezik egy előfizetéshez társított felügyeleti tanúsítvánnyal, programozott módon csatlakozhat a [klasszikus üzembe helyezési modell REST](/azure/?pivot=sdkstools) API-hoz, és automatizálhatja az adott előfizetéshez kapcsolódó különböző Azure-erőforrásokat (miután telepítette a megfelelő tanúsítványt) programozott módon.
