---
title: Egyéni hitelesítésszolgáltatói tanúsítvány hozzáadása – Azure API Management | Microsoft dokumentumok
description: Ismerje meg, hogyan adhat hozzá egyéni hitelesítésszolgáltatói tanúsítványt az Azure API Managementben.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 21d5869f2bcdfb6383b6ef89869d8098135ea7ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073604"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Egyéni hitelesítésszolgáltatói tanúsítvány hozzáadása az Azure API Management ben

Az Azure API Management lehetővé teszi a hitelesítésszolgáltatói tanúsítványok telepítését a számítógépen a megbízható legfelső szintű és köztes tanúsítványtárolókban. Ezt a funkciót akkor kell használni, ha a szolgáltatásokhoz egyéni hitelesítésszolgáltatói tanúsítvány szükséges.

A cikk bemutatja, hogyan kezelheti az Azure API Management szolgáltatáspéldány hitelesítésszolgáltatói tanúsítványait az Azure Portalon.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Hitelesítésszolgáltatói tanúsítvány feltöltése

![Hitelesítésszolgáltatói tanúsítványok hozzáadása](media/api-management-howto-ca-certificates/00.png)

Új hitelesítésszolgáltatói tanúsítvány feltöltéséhez kövesse az alábbi lépéseket. Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg az [OKTATÓanyag Egy API Management szolgáltatáspéldány létrehozása című témakört.](get-started-create-service-instance.md)

1. Keresse meg az Azure API Management szolgáltatáspéldányát az Azure Portalon.

2. Válassza a **hitelesítésszolgáltatói tanúsítványok parancsát** a menüből.

3. Kattintson a **+ Hozzáadás** gombra.  

    ![Hitelesítésszolgáltatói tanúsítványok hozzáadása](media/api-management-howto-ca-certificates/01.png)  

4. Keresse meg a tanúsítványt, és döntse el a tanúsítványtárolót. Csak a nyilvános kulcs szükséges, így a jelszó nem szükséges.

    ![Hitelesítésszolgáltatói tanúsítványok hozzáadása](media/api-management-howto-ca-certificates/02.png)  

5. Kattintson a **Mentés** gombra. A művelet eltarthat néhány percig.

    ![Hitelesítésszolgáltatói tanúsítványok hozzáadása](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Hitelesítésszolgáltatói tanúsítványt a `New-AzApiManagementSystemCertificate` Powershell paranccsal tölthet fel.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Ügyféltanúsítvány törlése

Tanúsítvány törléséhez kattintson a helyi **menüre ...** és válassza a **Törlés** parancsot a tanúsítvány mellett.

![Hitelesítésszolgáltatói tanúsítványok törlése](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
