---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 537c86512db4706077d0089a4f71ee945623c26b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081429"
---
### <a name="configure-your-notification-hub-with-apns-information"></a>Az értesítési központ konfigurálása APNs-információkkal

A **Notification Services**alatt válassza az **Apple** lehetőséget, majd kövesse a megfelelő lépéseket a [tanúsítvány létrehozása Notification Hubs](#creating-a-certificate-for-notification-hubs) szakaszban korábban kiválasztott megközelítés alapján.  

> [!NOTE]
> Csak akkor használja az **éles** **alkalmazási módot** , ha leküldéses értesítéseket szeretne küldeni az alkalmazást az áruházból megvásárolt felhasználóknak.

### <a name="option-1-using-a-p12-push-certificate"></a>1. lehetőség:. P12 leküldéses tanúsítvány használata

1. Válassza a **Tanúsítvány** elemet.

1. Válassza a fájl ikont.

1. Válassza ki a korábban exportált. P12 fájlt, majd kattintson a **Megnyitás**gombra.

1. Ha szükséges, a helyes jelszót kell megadnia.

1. Válassza a **Védőfal** módot.

1. Kattintson a **Mentés** gombra.

### <a name="option-2-using-token-based-authentication"></a>2. lehetőség: jogkivonat-alapú hitelesítés használata

1. Válassza a **token**lehetőséget.
1. Adja meg a korábban beszerzett következő értékeket:

    - **Kulcs azonosítója**
    - **Csomagazonosító**
    - **Csoport azonosítója**
    - **Jogkivonat**

1. Válassza a **homokozó**lehetőséget.
1. Kattintson a **Mentés** gombra.
