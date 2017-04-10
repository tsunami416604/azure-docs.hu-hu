---
title: "Webapp csatlakoztatása tartalomkézbesítési hálózathoz | Microsoft Docs"
description: "Webapp csatlakoztatása tartalomkézbesítési hálózathoz a statikus fájlok élcsomópontokból történő kézbesítéséhez."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: d616ca49da7a909a1a104409c5845f39c4a5377a
ms.lasthandoff: 04/04/2017

---
# <a name="connect-a-web-app-to-a-content-delivery-network"></a>Webapp csatlakoztatása tartalomkézbesítési hálózathoz

Az oktatóanyag során létre fog hozni egy Azure CDN-profilt és egy Azure CDN-végpontot a webappban lévő statikus fájlok kézbesítéséhez az Azure CDN POP-helyeken keresztül.

> [!TIP]
> Ellenőrizze az [Azure CDN POP-helyek](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations) frissítéseinek aktuális listáját.
>

## <a name="step-1---login-to-azure-portal"></a>1. lépés – Belépés az Azure Portalba

Először nyissa meg a kedvenc böngészőjét, és keresse fel az [Azure Portalt](https://portal.azure.com).

## <a name="step-2---create-a-cdn-profile"></a>2. lépés – CDN-profil létrehozása

A bal oldali navigációs menüben kattintson az `+ New` gombra, majd kattintson a **Web és mobil** elemre. A Web és mobil kategória alatt válassza ki a **CDN** lehetőséget.

Adja meg a **Név**, **Hely**, **Erőforráscsoport**, **Tarifacsomag** adatokat, majd kattintson a **Létrehozás** parancsra.

Nyissa meg az erőforráscsoportok központját a bal oldali navigációs menüben, majd válassza ki a **myResourceGroup** elemet. Az erőforrások listájából válassza ki a **myCDNProfile** elemet.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>3. lépés – CDN-végpont létrehozása

Kattintson a `+ Endpoint` elemre a keresőmező melletti parancsok között, ezzel elindítja a végpontlétrehozási panelt.

Adja meg a **Név**, **Forrástípus**, **Forrás gazdaneve** adatokat, majd kattintson a **Hozzáadás** parancsra.

A végpont ekkor létrejön. Ha a tartalomkézbesítési hálózat végpontja létrejön, az állapot **futás** állapotúra frissül.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---leveraging-cdn"></a>4. lépés – A CDN használata

Most, hogy a végpont már fut, biztosítsa a tartalom elérhetőségét a POP-kiszolgálón. Ehhez keressen egy statikus fájlt a webkiszolgálón, majd változtassa meg a gazdanevet erről: `http://<app_name>.azurewebsites.net/path/to-static-file` a következőre: `http://<endpoint_name>.azureedge.net/path/to-static-file`.

![app-service-web-url-to-cdn-endpoint-url](media/app-service-web-tutorial-content-delivery-network/app-service-web-url-to-cdn-endpoint-url.png)

## <a name="next-steps"></a>Következő lépések


