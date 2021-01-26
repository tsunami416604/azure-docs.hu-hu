---
title: Kerberos kulcsszolgáltató proxy Windows rendszerű virtuális asztal beállítása – Azure
description: Windows rendszerű virtuális asztali címkészlet beállítása Kerberos kulcsszolgáltató proxy használatára.
author: Heidilohr
ms.topic: how-to
ms.date: 01/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: f123659941eaeb3b8ceeb6a999abf836eb9cf5ea
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798384"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy"></a>Kerberos kulcsszolgáltató proxy konfigurálása

Ez a cikk bemutatja, hogyan konfigurálhatja a Kerberos Key Distribiution Center (KDC) proxyt a gazdagép-készlethez. Ez a proxy lehetővé teszi a szervezetek számára, hogy a vállalat határain kívül Kerberos hitelesítést végezzenek. A KDC-proxy használatával például engedélyezheti a külső ügyfelek intelligens kártyás hitelesítését.

## <a name="how-to-configure-the-kdc-proxy"></a>A KDC-proxy konfigurálása

A KDC-proxy konfigurálása:

1. Jelentkezzen be rendszergazdaként a Azure Portalba.

2. Nyissa meg a Windows rendszerű virtuális asztali lapot.

3. Válassza ki azt az alkalmazáskészletet, amely számára engedélyezni szeretné a KDC-proxyt, majd válassza az **RDP-tulajdonságok** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A Azure Portal lap képernyőképe, amely egy felhasználót jelöl ki a gazdagépeken, majd a példaként szolgáló címkészlet nevét, majd az RDP-tulajdonságokat.](media/rdp-properties.png)

4. Válassza a **speciális** fület, majd adjon meg egy értéket a következő formátumban szóközök nélkül:

    > kdcproxyname: s:\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![A speciális lapot kijelölő képernyőkép, amely a 4. lépésben leírtak szerint megadott értéket tartalmazza.](media/advanced-tab-selected.png)

5. Kattintson a **Mentés** gombra.

6. A kiválasztott gazdagépnek most meg kell kezdenie az RDP-kapcsolati fájlok kiírását a megadott kdcproxyname-mezővel.

## <a name="next-steps"></a>További lépések

A Távoli asztali szolgáltatások RDGateway szerepköre egy KDC proxy szolgáltatást tartalmaz. Lásd: [a RD-átjáró szerepkör telepítése a Windows rendszerű virtuális asztalon](rd-gateway-role.md) , hogy a Windows rendszerű virtuális asztali környezetnek hogyan kell beállítania egyet.
