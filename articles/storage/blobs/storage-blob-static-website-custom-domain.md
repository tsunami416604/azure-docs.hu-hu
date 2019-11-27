---
title: 'Oktatóanyag: egyéni tartomány & SSL engedélyezése statikus webhely számára az Azure-ban'
description: 'Útmutató: egyéni tartomány statikus webhelyüzemeltetésre konfigurálja.'
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 893ac53dc9f0b6b162c5ec22e478cd15706e50fb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327497"
---
# <a name="tutorial-enable-custom-domain--ssl-for-a-static-website-in-azure"></a>Oktatóanyag: egyéni tartomány & SSL engedélyezése statikus webhely számára az Azure-ban

Ez az oktatóanyag egy sorozat második része. Bemutatja egy egyéni tartomány végpont a statikus webhely SSL-lel való engedélyezéséhez. 

Az oktatóanyag bemutatja, hogyan használható a [Azure CDN](../../cdn/cdn-overview.md) az egyéni tartomány végpontjának a statikus webhelyhez történő konfigurálásához. Az Azure CDN egyéni SSL-tanúsítványokat hozhat létre, egy egyéni tartományt használja, és egyszerre az összes egyéni újraírási szabályok konfigurálása. Konfigurálása Azure CDN további díjakat eredményez, de itt egységesen rövid késleltetésével webhelyéhez bárhol a világon. Az Azure CDN emellett saját tanúsítványt az SSL-titkosítást. További információ a Azure CDN díjszabásáról: [Azure CDN díjszabása](https://azure.microsoft.com/pricing/details/cdn/).

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * CDN-végpont létrehozása a statikus webhely-végponton
> * Egyéni tartományt és SSL engedélyezése

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené ezt az oktatóanyagot, fejezze be a következő [oktatóanyagot: statikus webhely üzemeltetése blob Storageon](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A kezdéshez jelentkezzen be a [Azure Portalba](https://portal.azure.com/) .

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>CDN-végpont létrehozása a statikus webhely-végponton

1. Keresse meg a Storage-fiókját a Azure Portalban, és jelenítse meg a fiók áttekintését.
1. A Azure CDN konfigurálásához válassza **Azure CDN** a **blob szolgáltatás** menüjében.
1. A **CDN-profil** szakaszban válasszon egy új vagy meglévő CDN-profilt. További információk: [Gyors útmutató: Azure CDN-profil és -végpont létrehozása](../../cdn/cdn-create-new-endpoint.md)
1. Határozza meg a CDN-végpont díjszabási szintjét. Ez az oktatóanyag a **standard Akamai** díjszabási szintet használja, mert gyorsan propagálja, jellemzően néhány percen belül. Az egyéb díjszabási csomagok továbbra is hosszabb ideig tarthatnak, de más előnyöket is biztosíthatnak. További információ: [Azure CDN termék funkcióinak összehasonlítása](../../cdn/cdn-features.md).
1. A **CDN-végpont neve** mezőben adja meg a CDN-végpont nevét. A CDN-végpontnak egyedinek kell lennie az Azure-ban.
1. Itt adhatja meg a statikus webhely végpontját a **forrás állomásneve** mezőben. A statikus webhely végpontjának megkereséséhez keresse meg a **statikus webhely** beállításait a Storage-fiókhoz. Másolja az elsődleges végpontot, és illessze be a CDN-konfigurációba, és távolítsa el a protokoll azonosítóját (*például*: https).

    Az alábbi képen egy példa végpont-konfiguráció látható:

    ![A példa CDN-végpont konfigurációját bemutató képernyőkép](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Hozza létre a CDN-végpontot, és várjon, amíg propagálja.
1. Annak ellenőrzéséhez, hogy a CDN-végpont megfelelően van-e konfigurálva, kattintson a végpontra, és navigáljon a beállításaihoz. A Storage-fiókhoz tartozó CDN áttekintésében keresse meg a végpont állomásnevét, és navigáljon a végponthoz az alábbi ábrán látható módon. A CDN-végpont formátuma hasonló lesz a `https://staticwebsitesamples.azureedge.net`hoz.

    ![A CDN-végpont áttekintését bemutató képernyőkép](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    A CDN-végpont propagálásának befejeződése után a CDN-végpontra való navigálás megjeleníti a statikus webhelyre korábban feltöltött index. html fájl tartalmát.

1. A CDN-végpont forrás-beállításainak áttekintéséhez navigáljon a **forráshoz** a CDN-végpont **Beállítások** szakaszában. Látni fogja, hogy a **forrás típusa** mező *Egyéni forrásként* van beállítva, és a **forrás állomásnév** mező megjeleníti a statikus webhely végpontját.

    ![A CDN-végpont forrás-beállításainak megjelenítését bemutató képernyőkép](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Egyéni tartományt és SSL engedélyezése

1. Hozzon létre egy CNAME rekordot a tartomány neve szolgáltatónál átirányítja az egyéni tartomány a CDN-végponthoz. A *www* altartományhoz tartozó CNAME-rekordnak a következőhöz hasonlónak kell lennie:

    ![Adja meg a CNAME rekordot adott altartomány www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. A Azure Portalban jelenítse meg a CDN-végpont beállításait. Az egyéni tartomány és az SSL-tanúsítvány konfigurálásához navigáljon a **Beállítások** területen lévő **Egyéni tartományokra** .
1. Válassza az **egyéni tartomány hozzáadása** lehetőséget, és adja meg a tartománynevet, majd kattintson a **Hozzáadás**gombra.
1. Válassza ki az új egyéni tartomány-hozzárendelést az SSL-tanúsítvány kiépítéséhez.
1. Állítsa be az **Egyéni tartományhoz tartozó HTTPS** -t, majd kattintson **a** **Mentés**gombra. Az egyéni tartomány konfigurálása több óráig is eltarthat. A portál a következő képen látható módon jeleníti meg a folyamatot.

    ![Az egyéni tartomány konfigurációjának előrehaladását bemutató képernyőkép](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Az egyéni tartomány URL-címének elérésével tesztelheti a statikus webhely leképezését az egyéni tartományhoz.

További információ a HTTPS egyéni tartományokhoz való engedélyezéséről [: oktatóanyag: https konfigurálása Azure CDN egyéni tartományon](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag második részében megtanulta, hogyan egyéni tartomány konfigurálása az Azure CDN SSL-lel a statikus webhely.

A Azure CDN konfigurálásával és használatával kapcsolatos további információkért lásd: [Mi az Azure CDN?](../../cdn/cdn-overview.md)