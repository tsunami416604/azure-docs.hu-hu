---
title: Az örökölt alkalmazás AAD-regisztráció használata az Azure digitális Twins |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan hoz létre az alkalmazás regisztrációját az Azure Active Directory az Azure digitális Twins telepítő örökölt mód használatára.
author: alinamstanciu
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: alinast
ms.openlocfilehash: ed6c540cedd21bb5154f8746050d5eaa9444b818
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484724"
---
# <a name="register-your-azure-digital-twins-app-with-azure-active-directory-legacy"></a>Az Azure digitális Twins alkalmazás regisztrálása az Azure Active Directoryval örökölt

Ez a cikk bemutatja, hogy a régi vagy az örökölt módja a mintaalkalmazást az Azure Active Directory (Azure AD) regisztrálja, hogy hozzá tudjon férni a digitális Twins-példány is használhatja. Érdemes ezt a módszert, abban az esetben az új Azure AD-alkalmazás regisztrációját a telepítő nem működik.

[!INCLUDE [Digital Twins legacy AAD](../../includes/digital-twins-permissions-legacy.md)]

## <a name="next-steps"></a>További lépések

Miután az alkalmazás regisztrálva van az AAD-ben, képes majd csatlakozni a digitális Twins-példány és a Súgó ebben az esetben azt mutatja be. Tekintse az [rövid](quickstart-view-occupancy-dotnet.md#build-application) vagy a [oktatóanyag](tutorial-facilities-setup.md#configure-the-digital-twins-sample) bővebben a következő lépéseket. 