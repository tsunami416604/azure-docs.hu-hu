---
title: 'Oktatóanyag: Egyéni tartomány engedélyezése SSL használatával statikus webhelyeken Azure CDN-Azure Storage használatával'
description: 'Útmutató: egyéni tartomány statikus webhelyüzemeltetésre konfigurálja.'
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: a65c0e677182eb224f6bfa7ed834740458b97098
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847005"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>Oktatóanyag: Az SSL-t használó egyéni tartomány engedélyezése a Azure CDN használatával statikus webhelyeken

Ez az oktatóanyag egy sorozat második része. Bemutatja egy egyéni tartomány végpont a statikus webhely SSL-lel való engedélyezéséhez. 

Ebből az oktatóanyagból megtudhatja, hogyan használható [Azure CDN](../../cdn/cdn-overview.md) az egyéni tartomány végpontját a statikus webhely konfigurálása. Az Azure CDN egyéni SSL-tanúsítványokat hozhat létre, egy egyéni tartományt használja, és egyszerre az összes egyéni újraírási szabályok konfigurálása. Konfigurálása Azure CDN további díjakat eredményez, de itt egységesen rövid késleltetésével webhelyéhez bárhol a világon. Az Azure CDN emellett saját tanúsítványt az SSL-titkosítást. Információ az Azure CDN-tarifacsomag: [Azure CDN-tarifacsomag](https://azure.microsoft.com/pricing/details/cdn/).

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * CDN-végpont létrehozása a statikus webhely-végponton
> * Egyéni tartományt és SSL engedélyezése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt végezze [el a következő oktatóanyagot: Statikus webhely üzemeltetése Blob Storageon](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be a [az Azure portal](https://portal.azure.com/) a kezdéshez.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>CDN-végpont létrehozása a statikus webhely-végponton

1. Keresse meg a Storage-fiókját a Azure Portalban, és jelenítse meg a fiók áttekintését.
1. Válassza ki **Azure CDN** alatt a **Blob Service** konfigurálása Azure CDN menü.
1. A **CDN-profil** szakaszban válasszon egy új vagy meglévő CDN-profilt. További információ: gyors útmutató [: Hozzon létre egy Azure CDN profilt](../../cdn/cdn-create-new-endpoint.md)és végpontot.
1. Határozza meg a CDN-végpont díjszabási szintjét. Ez az oktatóanyag a **standard Akamai** díjszabási szintet használja, mert gyorsan propagálja, jellemzően néhány percen belül. Az egyéb díjszabási csomagok továbbra is hosszabb ideig tarthatnak, de más előnyöket is biztosíthatnak. További információ: [Azure CDN termék funkcióinak összehasonlítása](../../cdn/cdn-features.md).
1. A **CDN-végpont neve** mezőben adja meg a CDN-végpont nevét. A CDN-végpontnak egyedinek kell lennie az Azure-ban.
1. Itt adhatja meg a statikus webhely végpontját a **forrás állomásneve** mezőben. A statikus webhely végpontjának megkereséséhez keresse meg a **statikus webhely** beállításait a Storage-fiókhoz. Másolja az elsődleges végpontot, és illessze be a CDN-konfigurációba, és távolítsa el a protokoll azonosítóját (*például*: https).

    Az alábbi képen egy példa végpont-konfiguráció látható:

    ![A példa CDN-végpont konfigurációját bemutató képernyőkép](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Hozza létre a CDN-végpontot, és várjon, amíg propagálja.
1. Annak ellenőrzéséhez, hogy a CDN-végpont megfelelően van-e konfigurálva, kattintson a végpontra, és navigáljon a beállításaihoz. A Storage-fiókhoz tartozó CDN áttekintésében keresse meg a végpont állomásnevét, és navigáljon a végponthoz az alábbi ábrán látható módon. A CDN- `https://staticwebsitesamples.azureedge.net`végpont formátuma hasonló lesz a következőhöz:.

    ![A CDN-végpont áttekintését bemutató képernyőkép](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    A CDN-végpont propagálásának befejeződése után a CDN-végpontra való navigálás megjeleníti a statikus webhelyre korábban feltöltött index. html fájl tartalmát.

1. A CDN-végpont forrás-beállításainak áttekintéséhez navigáljon a **forráshoz** a CDN-végpont **Beállítások** szakaszában. Látni fogja, hogy a **forrás típusa** mező *Egyéni forrásként* van beállítva, és a **forrás állomásnév** mező megjeleníti a statikus webhely végpontját.

    ![A CDN-végpont forrás-beállításainak megjelenítését bemutató képernyőkép](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Egyéni tartományt és SSL engedélyezése

1. Hozzon létre egy CNAME rekordot a tartomány neve szolgáltatónál átirányítja az egyéni tartomány a CDN-végponthoz. A CNAME rekordot a *www* altartomány a következőhöz hasonló lesz:

    ![Adja meg a CNAME rekordot adott altartomány www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. A Azure Portalban jelenítse meg a CDN-végpont beállításait. Az egyéni tartomány és az SSL-tanúsítvány konfigurálásához navigáljon a **Beállítások** területen lévő **Egyéni tartományokra** .
1. Válassza ki **egyéni tartomány hozzáadása** , és adja meg a tartomány nevét, majd kattintson a **Hozzáadás**.
1. Válassza ki az új egyéni tartomány-hozzárendelést az SSL-tanúsítvány kiépítéséhez.
1. Állítsa be az **Egyéni tartományhoz tartozó HTTPS** -t, majd kattintson **a** **Mentés**gombra. Az egyéni tartomány konfigurálása több óráig is eltarthat. A portál a következő képen látható módon jeleníti meg a folyamatot.

    ![Az egyéni tartomány konfigurációjának előrehaladását bemutató képernyőkép](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Az egyéni tartomány URL-címének elérésével tesztelheti a statikus webhely leképezését az egyéni tartományhoz.

További információ a https egyéni tartományokhoz [való engedélyezéséről: oktatóanyag: Konfigurálja a HTTPS-t egy Azure CDN](../../cdn/cdn-custom-ssl.md)egyéni tartományon.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag második részében megtanulta, hogyan egyéni tartomány konfigurálása az Azure CDN SSL-lel a statikus webhely.

A Azure CDN konfigurálásával és használatával kapcsolatos további információkért lásd: [Mi az Azure CDN?](../../cdn/cdn-overview.md)