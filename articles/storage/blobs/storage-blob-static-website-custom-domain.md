---
title: 'Oktatóanyag: Egyéni tartomány SSL-lel az Azure CDN – Azure Storage használatával statikus webhely engedélyezése'
description: 'Útmutató: egyéni tartomány statikus webhelyüzemeltetésre konfigurálja.'
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: tamram
ms.custom: seodec18
ms.openlocfilehash: 6ccd33805fe4b62d3456121321edc4eec3bff2e5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109459"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>Oktatóanyag: Használja az Azure CDN engedélyezéséhez egy egyéni tartomány statikus webhely SSL-lel

Ez az oktatóanyag egy sorozat második része. Bemutatja egy egyéni tartomány végpont a statikus webhely SSL-lel való engedélyezéséhez. 

Ebből az oktatóanyagból megtudhatja, hogyan használható [Azure CDN](../../cdn/cdn-overview.md) az egyéni tartomány végpontját a statikus webhely konfigurálása. Az Azure CDN egyéni SSL-tanúsítványokat hozhat létre, egy egyéni tartományt használja, és egyszerre az összes egyéni újraírási szabályok konfigurálása. Konfigurálása Azure CDN további díjakat eredményez, de itt egységesen rövid késleltetésével webhelyéhez bárhol a világon. Az Azure CDN emellett saját tanúsítványt az SSL-titkosítást. Információ az Azure CDN-tarifacsomag: [Azure CDN-tarifacsomag](https://azure.microsoft.com/pricing/details/cdn/).

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * CDN-végpont létrehozása a statikus webhely-végponton
> * Egyéni tartományt és SSL engedélyezése

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elkezdéséhez hajtsa végre az első rész, [oktatóanyag: Blob Storage-statikus webhely üzemeltetése](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be a [az Azure portal](https://portal.azure.com/) a kezdéshez.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>CDN-végpont létrehozása a statikus webhely-végponton

1. Nyissa meg a [az Azure portal](https://portal.azure.com/) a böngészőben. 
1. Keresse meg a tárfiók, és megjeleníti a fiók áttekintése.
1. Válassza ki **Azure CDN** alatt a **Blob Service** konfigurálása Azure CDN menü.
1. Az a **új végpont** területen töltse ki a mezőket egy új CDN-végpont létrehozása.
1. Adja meg a végpont neve, például *mystaticwebsiteCDN*.
1. Adja meg a webhely tartomány szerint az állomásnevet a CDN-végponthoz.
1. A forrás állomásnevének adja meg a statikus webhely végpont Ön. Keresse meg a statikus webhely-végpontra, nyissa meg a **statikus webhely** a tárfiók szakaszt, és másolja a végpont. 
1. Lépjen a CDN-végpont tesztelése *mywebsitecdn.azureedge.net* a böngészőben.

## <a name="enable-custom-domain-and-ssl"></a>Egyéni tartományt és SSL engedélyezése

1. Hozzon létre egy CNAME rekordot a tartomány neve szolgáltatónál átirányítja az egyéni tartomány a CDN-végponthoz. A CNAME rekordot a *www* altartomány a következőhöz hasonló lesz:

    ![Adja meg a CNAME rekordot adott altartomány www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Az Azure Portalon kattintson az újonnan létrehozott végpontot az egyéni tartomány és az SSL-tanúsítvány konfigurálása.
1. Válassza ki **egyéni tartomány hozzáadása** , és adja meg a tartomány nevét, majd kattintson a **Hozzáadás**.
1. Válassza ki az újonnan létrehozott egyéni tartományleképezést SSL-tanúsítvány létrehozásához.
1. Állítsa be **egyéni tartományi HTTPS** való **ON**. Válassza ki **CDN-en** szeretné, hogy az SSL-tanúsítvány kezelése az Azure CDN-t. Kattintson a **Save** (Mentés) gombra.
1. Webhelye tesztelésével a webhely URL-címe elérésével.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag második részében megtanulta, hogyan egyéni tartomány konfigurálása az Azure CDN SSL-lel a statikus webhely.

További információ a statikus webhely üzemeltetése az Azure Storage erre a hivatkozásra.

> [!div class="nextstepaction"]
> [További tudnivalók a statikus webhely](storage-blob-static-website.md)
