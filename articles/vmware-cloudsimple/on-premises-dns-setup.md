---
title: Azure VMware-megoldások (AVS) – DNS konfigurálása AVS Private Cloud-hoz
description: Ismerteti, hogyan állítható be a DNS-névfeloldás a vCenter-kiszolgálóhoz való hozzáféréshez a helyszíni munkaállomásokról származó AVS privát felhőben
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 12c4362ae1b075af132d5971f4fe0461c9d91733
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083084"
---
# <a name="configure-dns-for-name-resolution-for-avs-private-cloud-vcenter-access-from-on-premises-workstations"></a>DNS beállítása a névfeloldáshoz az AVS Private Cloud vCenter hozzáférés a helyszíni munkaállomásokról

Ha a vCenter-kiszolgálót egy, a helyszíni munkaállomásokról származó AVS Private-felhőben szeretné elérni, konfigurálnia kell a DNS-cím feloldását, hogy a vCenter-kiszolgálót az állomásnév, valamint az IP-cím is megcélozhatja.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-avs-private-cloud"></a>Szerezze be a DNS-kiszolgáló IP-címét az AVS Private Cloud-hoz

1. Jelentkezzen be az [AVS-portálra](access-cloudsimple-portal.md).

2. Navigáljon az **erőforrásokhoz** > az **AVS privát felhők** elemre, és válassza ki azt az AVS privát felhőt, amelyhez csatlakozni szeretne.

3. Az AVS Private Cloud **alapszintű információk** **Összefoglaló** lapján másolja az AVS Private Cloud DNS-kiszolgáló IP-címét.

    ![AVS Private Cloud DNS-kiszolgálók](media/private-cloud-dns-server.png)


A DNS-konfigurációhoz használja a következő lehetőségek egyikét.

* [Zóna létrehozása a (z) *. cloudsimple.io DNS-kiszolgálón](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Feltételes továbbító létrehozása a helyszíni DNS-kiszolgálón a *. cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Zóna létrehozása a (z) *. cloudsimple.io DNS-kiszolgálón

Létrehozhat egy zónát helyettes zónáként, és a saját felhőben lévő DNS-kiszolgálókra irányíthatja a névfeloldást. Ez a szakasz a kötési DNS-kiszolgáló vagy a Microsoft Windows DNS-kiszolgáló használatával kapcsolatos információkat tartalmaz.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Zóna létrehozása kötési DNS-kiszolgálón

A konfigurálandó fájl és paraméterek az egyéni DNS-beállítástól függően változhatnak.

Az alapértelmezett kötési kiszolgáló konfigurációja esetében például szerkessze a/etc/named.conf-fájlt a DNS-kiszolgálón, és adja hozzá a következő zónával kapcsolatos információkat.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Zóna létrehozása Microsoft Windows DNS-kiszolgálón

1. Kattintson a jobb gombbal a DNS-kiszolgálóra, és válassza az **új zóna**lehetőséget. 
  
    ![Új zóna](media/DNS01.png)
2. Válassza ki a **helyettes zónát** , és kattintson a **tovább**gombra.

    ![Új zóna](media/DNS02.png)
3. A környezettől függően válassza ki a megfelelő beállítást, majd kattintson a **tovább**gombra.

    ![Új zóna](media/DNS03.png)
4. Válassza a **címkeresési zóna** lehetőséget, majd kattintson a **tovább**gombra.

    ![Új zóna](media/DNS01.png)
5. Adja meg a zóna nevét, és kattintson a **tovább**gombra.

    ![Új zóna](media/DNS05.png)
6. Adja meg azon DNS-kiszolgálók IP-címeit, amelyek az AVS-portálról beszerzett AVS Private Cloud-felhőben vannak.

    ![Új zóna](media/DNS06.png)
7. A varázsló telepítésének befejezéséhez kattintson a **tovább** gombra.

## <a name="create-a-conditional-forwarder"></a>Feltételes továbbító létrehozása

A feltételes továbbító továbbítja a DNS-névfeloldási kérelmeket a kijelölt kiszolgálóra. Ezzel a beállítással a *. cloudsimple.io-re irányuló kérések továbbítása az AVS Private Cloud-on található DNS-kiszolgálókra történik. Az alábbi példák bemutatják, hogyan állíthat be továbbítókat különböző típusú DNS-kiszolgálókon.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Feltételes továbbító létrehozása egy kötési DNS-kiszolgálón

A konfigurálandó fájl és paraméterek az egyéni DNS-beállítástól függően változhatnak.

Az alapértelmezett kötési kiszolgáló konfigurációja esetében például szerkessze a/etc/named.conf fájlt a DNS-kiszolgálón, és adja hozzá a következő feltételes továbbítási adatokat.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Feltételes továbbító létrehozása Microsoft Windows DNS-kiszolgálón

1. Nyissa meg a DNS-kezelőt a DNS-kiszolgálón.
2. Kattintson a jobb gombbal a **feltételes továbbítók** elemre, és válassza az új feltételes továbbító hozzáadásának lehetőségét.

    ![Feltételes továbbító 1 Windows DNS](media/DNS08.png)
3. Adja meg a DNS-tartományt és a DNS-kiszolgálók IP-címét az AVS Private-felhőben, majd kattintson **az OK**gombra.
