---
title: Adjon hozzá egy egyéni Hitelesítésszolgáltatói tanúsítvány – az Azure API Management |} A Microsoft Docs
description: 'Útmutató: egyéni CA-tanúsítvány hozzáadása az Azure API Management.'
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 5161a35fd52b2f3d8374c76bdab60281e33dacf6
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730647"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Egyéni Hitelesítésszolgáltatói tanúsítvány hozzáadása az Azure API Management szolgáltatásban

Az Azure API Management lehetővé teszi, hogy a CA-tanúsítványok telepítése a megbízható legfelső szintű és a köztes tanúsítványtárolókban található a gépen. Ez a funkció kell használni, ha a szolgáltatások egyéni CA-tanúsítvány szükséges.

A cikk bemutatja, hogyan kezelheti a CA-tanúsítványok az Azure API Management-példányok az Azure Portalon.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Egy hitelesítésszolgáltató-tanúsítvány feltöltése

![Adja hozzá a CA-tanúsítványok](media/api-management-howto-ca-certificates/00.png)

Kövesse az alábbi lépéseket egy új hitelesítésszolgáltató-tanúsítvány feltöltéséhez. Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg az oktatóanyag [az API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).

1. Keresse meg az Azure API Management szolgáltatáspéldányt, az Azure Portalon.

2. Válassza ki **CA-tanúsítványok** a menüből.

3. Kattintson a **+ Hozzáadás** gombra.  

    ![Adja hozzá a CA-tanúsítványok](media/api-management-howto-ca-certificates/01.png)  

4. Keresse meg a tanúsítványt, és döntse el, a tanúsítvány-áruházban. Csak a nyilvános kulcs szükséges, ezért a jelszó nem szükséges.

    ![Adja hozzá a CA-tanúsítványok](media/api-management-howto-ca-certificates/02.png)  

5. Kattintson a **Save** (Mentés) gombra. A művelet eltarthat néhány percig.

    ![Adja hozzá a CA-tanúsítványok](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Feltölthet egy Hitelesítésszolgáltatói tanúsítvány használatával a `New-AzApiManagementSystemCertificate` Powershell-parancsot.

## <a name="step1a"> </a>Ügyfél-tanúsítvány törlése

A tanúsítvány törléséhez kattintson a helyi menü **...**  válassza **törlése** mellett a tanúsítványt.

![CA-tanúsítványok törlése](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
