---
title: Azure VMware-megoldás a CloudSimple-től – A DNS konfigurálása a CloudSimple magánfelhőhöz
description: Ez a témakör azt ismerteti, hogy miként állítható be a DNS-névfeloldás a virtuálisközpont-kiszolgálóhoz való hozzáféréshez egy CloudSimple magánfelhőben helyszíni munkaállomásokról
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2d69d21eb46d502a45c9df1dfaaa947d26ef7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246109"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>Dns konfigurálása a magánfelhő-vCenter-hozzáférés névfeloldásához helyszíni munkaállomásokról

A vCenter-kiszolgáló helyszíni munkaállomásokról történő eléréséhez konfigurálnia kell a DNS-cím feloldását, hogy a vCenter-kiszolgáló állomásnév és IP-cím szerint is címezhető legyen.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>A MAGÁNFELHŐ DNS-kiszolgálójának IP-címének beszerzése

1. Jelentkezzen be a [CloudSimple portálra.](access-cloudsimple-portal.md)

2. Nyissa meg az **Erőforrások** > **magánfelhőket,** és válassza ki azt a magánfelhőt, amelyhez csatlakozni szeretne.

3. Az **Alapszintű** **adatok**csoport Magánfelhő összefoglaló lapján másolja a Magánfelhő DNS-kiszolgálóJÁNAK IP-címét.

    ![Magánfelhő DNS-kiszolgálói](media/private-cloud-dns-server.png)


A DNS-konfigurációhoz használja az alábbi beállítások egyikét.

* [Zóna létrehozása a DNS-kiszolgálón a *.cloudsimple.io számára](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Feltételes továbbító létrehozása a helyszíni DNS-kiszolgálón a *.cloudsimple.io feloldásához](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Zóna létrehozása a DNS-kiszolgálón a *.cloudsimple.io számára

A zónát helyettes zónaként állíthatja be, és névfeloldáshoz a magánfelhő DNS-kiszolgálóira mutathat. Ez a szakasz a BIND DNS-kiszolgáló vagy a Microsoft Windows DNS-kiszolgáló használatával kapcsolatos információkat tartalmazza.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Zóna létrehozása BIND DNS-kiszolgálón

A konfigurálandó fájl és paraméterek az egyes DNS-beállításoktól függően változhatnak.

Például az alapértelmezett BIND kiszolgáló konfiguráció, edit /etc/named.conf fájlt a DNS-kiszolgálón, és adja meg a következő zóna adatokat.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Zóna létrehozása Microsoft Windows DNS-kiszolgálón

1. Kattintson a jobb gombbal a DNS-kiszolgálóra, és válassza az **Új zóna parancsot.** 
  
    ![Új zóna](media/DNS01.png)
2. Válassza **a Csonkzóna lehetőséget,** és kattintson a **Tovább**gombra.

    ![Új zóna](media/DNS02.png)
3. A környezettől függően válassza a megfelelő beállítást, majd kattintson a **Tovább**gombra.

    ![Új zóna](media/DNS03.png)
4. Válassza **a Kereskövetési zóna lehetőséget,** majd kattintson a **Tovább**gombra.

    ![Új zóna](media/DNS01.png)
5. Írja be a zóna nevét, és kattintson a **Tovább gombra.**

    ![Új zóna](media/DNS05.png)
6. Adja meg a Private Cloud DNS-kiszolgálóinak IP-címét, amelyet a CloudSimple portálról kapott.

    ![Új zóna](media/DNS06.png)
7. A varázsló beállításának befejezéséhez kattintson a **Tovább** gombra.

## <a name="create-a-conditional-forwarder"></a>Feltételes továbbító létrehozása

A feltételes továbbító minden DNS-névfeloldási kérelmet továbbít a kijelölt kiszolgálónak. Ezzel a beállítással a *.cloudsimple.io-ra irányuló kéréseket a rendszer továbbítja a magánfelhőben található DNS-kiszolgálókra. Az alábbi példák bemutatják, hogyan állíthatók be a továbbítók különböző típusú DNS-kiszolgálókon.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Feltételes továbbító létrehozása BIND DNS-kiszolgálón

A konfigurálandó fájl és paraméterek az egyes DNS-beállításoktól függően változhatnak.

Például az alapértelmezett BIND kiszolgáló konfiguráció, edit /etc/named.conf fájlt a DNS-kiszolgálón, és adja hozzá a következő feltételes továbbítási információkat.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Feltételes továbbító létrehozása Microsoft Windows DNS-kiszolgálón

1. Nyissa meg a DNS-kezelőt a DNS-kiszolgálón.
2. Kattintson a jobb gombbal **a feltételes továbbítók elemre,** és válassza ki az új feltételes továbbító hozzáadásának lehetőségét.

    ![Feltételes továbbító 1 Windows DNS](media/DNS08.png)
3. Írja be a DNS-tartományt és a DNS-kiszolgálók IP-címét a magánfelhőben, majd kattintson az **OK**gombra.
