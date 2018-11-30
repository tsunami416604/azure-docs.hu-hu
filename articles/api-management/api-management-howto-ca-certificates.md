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
ms.openlocfilehash: b05713fd8acb49b25dba04781c1049c9f05115fb
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444690"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Egyéni Hitelesítésszolgáltatói tanúsítvány hozzáadása az Azure API Management szolgáltatásban

Az Azure API Management lehetővé teszi, hogy a CA-tanúsítványok telepítése a megbízható legfelső szintű és a köztes tanúsítványtárolókban található a gépen. Ez a funkció kell használni, ha a szolgáltatások egyéni CA-tanúsítvány szükséges.

A cikk bemutatja, hogyan kezelheti a CA-tanúsítványok az Azure API Management-példányok az Azure Portalon.

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
> Feltölthet egy Hitelesítésszolgáltatói tanúsítvány használatával a `New-AzureRmApiManagementSystemCertificate` Powershell-parancsot.

## <a name="step1a"> </a>Ügyfél-tanúsítvány törlése

A tanúsítvány törléséhez kattintson a helyi menü **...**  válassza **törlése** mellett a tanúsítványt.

![CA-tanúsítványok törlése](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a