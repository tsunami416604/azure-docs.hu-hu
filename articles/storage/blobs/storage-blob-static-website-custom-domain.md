---
title: 'Oktatóanyag: Engedélyezze az egyéni tartományt állít be egy Azure CDN – Azure Storage-statikus webhely SSL-lel'
description: 'Útmutató: egyéni tartomány statikus webhelyüzemeltetésre konfigurálja.'
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
ms.openlocfilehash: 2b0bb94be2ba8ea983cda8fd015d05fcd532f2bc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226108"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>Oktatóanyag: Statikus webhely egyéni tartomány SSL engedélyezése az Azure CDN használatával

Ez az oktatóanyag egy sorozat második része. Bemutatja egy egyéni tartomány végpont a statikus webhely SSL-lel való engedélyezéséhez. 

Ebből az oktatóanyagból megtudhatja, hogyan használható [Azure CDN](../../cdn/cdn-overview.md) az egyéni tartomány végpontját a statikus webhely konfigurálása. Az Azure CDN egyéni SSL-tanúsítványokat hozhat létre, egy egyéni tartományt használja, és egyszerre az összes egyéni újraírási szabályok konfigurálása. Konfigurálása Azure CDN további díjakat eredményez, de itt egységesen rövid késleltetésével webhelyéhez bárhol a világon. Az Azure CDN emellett saját tanúsítványt az SSL-titkosítást. Információ az Azure CDN-tarifacsomag: [Azure CDN-tarifacsomag](https://azure.microsoft.com/pricing/details/cdn/).

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * CDN-végpont létrehozása a statikus webhely-végponton
> * Egyéni tartományt és SSL engedélyezése

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elkezdéséhez hajtsa végre az első rész, [oktatóanyag: A Blob Storage statikus webhely üzemeltetése](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be a [az Azure portal](https://portal.azure.com/) a kezdéshez.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>CDN-végpont létrehozása a statikus webhely-végponton

1. Az Azure Portalon keresse meg a tárfiók, és megjeleníti a fiók áttekintése.
1. Válassza ki **Azure CDN** alatt a **Blob Service** konfigurálása Azure CDN menü.
1. Az a **CDN-profil** adja meg az új vagy meglévő CDN-profil. További információkért lásd: [a rövid útmutató: Azure CDN-profil és -végpont létrehozása](../../cdn/cdn-create-new-endpoint.md).
1. Adja meg a CDN-végpont tarifacsomagot. Ebben az oktatóanyagban a **Standard Akamai** tarifacsomagban van, mivel ez tölti ki gyorsan, általában néhány percen belül. További tarifacsomagok hosszabb időt vehet igénybe, terjesztése, de előfordulhat, hogy emellett egyéb előnyök. További információkért lásd: [hasonlítsa össze az Azure CDN termékszolgáltatásai](../../cdn/cdn-features.md).
1. Az a **CDN-végpont neve** mezőben adjon meg egy nevet a CDN-végponthoz. A CDN-végpont Azure egyedinek kell lennie.
1. Adja meg a statikus webhely végpont az Ön a **forrás gazdaneve** mező. Keresse meg a statikus webhely-végpontra, nyissa meg a **statikus webhely** a tárfiók beállításait. Másolja az elsődleges végpontot, és illessze be a CDN-konfiguráció, a identifikátor protokolu eltávolítása (*például*, HTTPS).

    Az alábbi képen egy példa végpont-konfiguráció:

    ![Képernyőkép ábrázoló minta CDN végpont-konfiguráció](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. A CDN-végpont létrehozása, és várjon annak propagálására.
1. Győződjön meg arról, hogy a CDN-végpont megfelelően van konfigurálva, kattintson a végpontot, hogy lépjen a beállítások. A CDN áttekintése a tárfiókhoz keresse meg a végpont gazdaneve, és keresse meg a végpont a következő képen látható módon. A CDN-végpont formátuma hasonló lesz `https://staticwebsitesamples.azureedge.net`.

    ![Képernyőkép megjelenítő áttekintés CDN-végpont](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    A CDN-végpontok propagálása befejeződése után ellenőrizheti, hogy a CDN-végpont jeleníti meg a statikus webhely korábban feltöltött index.html fájl tartalmát.

1. Tekintse át a forrás beállításainak a CDN-végpont, navigáljon a **forrás** alatt a **beállítások** a CDN-végpont a következő szakaszban. Látni fogja, hogy a **forrástípus** mező értéke *egyéni forrás* , és hogy a **forrás gazdaneve** mezőben jelenik meg a statikus webhely-végponthoz.

    ![Képernyőfelvétel: a forrás beállításainak a CDN-végpont](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Egyéni tartományt és SSL engedélyezése

1. Hozzon létre egy CNAME rekordot a tartomány neve szolgáltatónál átirányítja az egyéni tartomány a CDN-végponthoz. A CNAME rekordot a *www* altartomány a következőhöz hasonló lesz:

    ![Adja meg a CNAME rekordot adott altartomány www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Az Azure Portalon a CDN-végpont beállításainak megjelenítéséhez. Navigáljon a **egyéni tartományok** alatt **beállítások** az egyéni tartomány és az SSL-tanúsítvány konfigurálása.
1. Válassza ki **egyéni tartomány hozzáadása** , és adja meg a tartomány nevét, majd kattintson a **Hozzáadás**.
1. Válassza ki az új egyéni tartományleképezést SSL-tanúsítvány létrehozásához.
1. Állítsa be **egyéni tartományi HTTPS** való **ON**, majd kattintson a **mentése**. Az egyéni tartomány konfigurálása több óráig is eltarthat. A portál megjeleníti a folyamat a következő képen látható módon.

    ![Képernyőfelvétel: folyamatban van az egyéni tartomány beállítása](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Tesztelje a statikus webhelyeket, hogy az egyéni tartomány leképezése fér hozzá az egyéni tartomány URL-CÍMÉT.

Egyéni tartományok HTTPS engedélyezésével kapcsolatos további információkért lásd: [oktatóanyag: HTTPS konfigurálása Azure CDN egyéni tartományon](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag második részében megtanulta, hogyan egyéni tartomány konfigurálása az Azure CDN SSL-lel a statikus webhely.

Konfigurálásával és az Azure CDN használatával kapcsolatos további információkért lásd: [Mi az Azure CDN?](../../cdn/cdn-overview.md).