---
title: Hozzon létre egy Azure CDN-végponthoz |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre egy új Azure Content Delivery Network (CDN) végpontot, a speciális beállításokat is beleértve.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 16a939c69d9ed9be597306765f316ffe32db6665
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645792"
---
# <a name="create-an-azure-cdn-endpoint"></a>Hozzon létre egy Azure CDN-végponthoz
Ez a cikk bemutatja az összes beállítás létrehozásához egy [Azure Content Delivery Network (CDN)](cdn-overview.md) végpontot egy meglévő CDN-profilt. Miután létrehozott egy profilt és egy végpontot, elindíthatja a tartalomszolgáltatást az ügyfelek. A profil és -végpont létrehozása egy rövid útmutatóban talál [a rövid útmutató: Azure CDN-profil és -végpont létrehozása](cdn-create-new-endpoint.md).

## <a name="prerequisites"></a>Előfeltételek
CDN-végpont létrehozása előtt, létre kell hozni legalább egy CDN-profilt, amely egy vagy több CDN-végpontot tartalmazhat. Ha rendszerezni szeretné a CDN-végpontokat internetes tartomány, webalkalmazás vagy más feltétel alapján, több profilt is használhat. CDN díjszabása a CDN-profil szintjén van érvényben, mert ha azt szeretné használni az Azure CDN-tarifacsomag vegyesen több CDN-profilt kell létrehoznia. A CDN-profil létrehozásához lásd: [hozzon létre egy új CDN-profil](cdn-create-new-endpoint.md#create-a-new-cdn-profile).

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-a-new-cdn-endpoint"></a>Új CDN-végpont létrehozása

1. Az [Azure Portalon](https://portal.azure.com) lépjen a CDN-profilra. Lehetséges, hogy a profilt az előző lépésben az irányítópulton rögzítette. Ha mégsem, akkor a **Minden szolgáltatás**, majd a **CDN-profilok** elemre kattintva megkeresheti. A **CDN-profilok** ablaktáblán válassza ki azt a profilt, amelyhez a végpontot hozzá szeretné adni. 
   
    Megjelenik a CDN-profil ablaktábla.

2. Válassza ki a **Végpont** beállítását.
   
    ![CDN-végpont kiválasztása](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    Megjelenik a **Végpont hozzáadása** oldal.
   
    ![Végpont-weblap hozzáadása](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. A **Név** mezőben adjon meg egy egyedi nevet a CDN-végpont számára. Ezt a nevet használja a tartomány a gyorsítótárazott erőforrások eléréséhez _<endpointname>_. azureedge.net.

4. A **forrástípus**, válasszon a következő forrás-típusok közül: 
   - **Tárolási** az Azure Storage szolgáltatáshoz
   - **Felhőalapú szolgáltatás** az Azure Cloud Services
   - **Web App** az Azure Web Appshez
   - **Egyéni forrás** bármely más nyilvánosan elérhető-e forrás web Server (az Azure-ban vagy máshol tárolt)

5. A **forrás gazdaneve**, válassza ki vagy adja meg a forrástartományt kiszolgáló. A legördülő listák a 4. lépésben megadott típusú összes rendelkezésre álló szerverek. Ha a kiválasztott **egyéni forrás** a forrása típusa, adja meg az egyéni forrás kiszolgáló.
    
6. A **forrás elérési útja**, írja be a gyorsítótárazni kívánt erőforrások elérési. Ahhoz, hogy az 5. lépésben megadott tartomány bármely erőforrásának gyorsítótárazását, hagyja üresen ezt a beállítást.
    
7. A **Forrás állomásfejléce** mezőben adja meg azt az állomásfejlécet, amelyet az Azure CDN-nek minden egyes kérelemmel el kell küldenie, vagy hagyja meg az alapértelmezett értéket.
   
   > [!NOTE]
   > Bizonyos típusú források – például az Azure Storage és a Web Apps – esetén az állomásfejlécnek egyeznie kell a forrás tartományával. Hacsak a forrás a tartománytól eltérő állomásfejléc használatát nem igényli, hagyja meg az alapértelmezett értéket.
   > 
    
8. A **protokoll** és **forrásport**, adja meg a forráskiszolgáló az erőforrások eléréséhez használt portok és protokollok. Legalább egy protokollt (a HTTP vagy a HTTPS protokollt) ki kell választani. A CDN által biztosított tartományt (_<endpointname>_. azureedge.net) a HTTPS-tartalmak eléréséhez. 
   
   > [!NOTE]
   > A **forrásport** érték azt határozza meg, csak az a port, a végpontot használja az eredeti kiszolgálón lévő információk lekéréséhez. Magát a végpontot a végfelhasználók – a **Forrásport** értékétől függetlenül – csak az alapértelmezett HTTP- és HTTPS-porton (azaz a 80-as és a 443-as porton) érik el.  
   > 
   > Az **Akamai Azure CDN**-profilok végpontjai esetén a forrásportok számára nem áll rendelkezésre a teljes TCP-porttartomány. A nem engedélyezett forrásportok listáját lást: [Azure CDN from Akamai Allowed Origin Ports](https://msdn.microsoft.com/library/mt757337.aspx) (Az Akamai Azure CDN engedélyezett forrásportjai).  
   > 
   > Az Azure CDN egyéni tartományok HTTPS-támogatása nem támogatott az **Akamai Azure CDN** termékek. További információkért lásd: [HTTPS konfigurálása az Azure CDN egyéni tartományon](cdn-custom-ssl.md).
    
9. A **optimalizált**, válassza ki az optimalizálás típusa, amely a legjobban illik a forgatókönyv és a végponthoz továbbítására kívánt tartalom típusa. További információkért lásd: [optimalizálása az Azure CDN tartalomkézbesítési típusú](cdn-optimization-overview.md).

    A következő optimalizálás-típus beállításainak megfelelően profiltípus támogatottak:
    - **Az Azure CDN Standard a Microsoft** profilok:
       - [**Általános webes kézbesítés**](cdn-optimization-overview.md#general-web-delivery)

    - **Az Azure CDN Standard verizon** és **verizon Azure CDN Premium** profilok:
       - [**Általános webes kézbesítés**](cdn-optimization-overview.md#general-web-delivery)
       - [**Dinamikus helygyorsítás**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - **Az Azure CDN Akamai Standard** profilok:
       - [**Általános webes kézbesítés**](cdn-optimization-overview.md#general-web-delivery)
       - [**Általános médiastreaming**](cdn-optimization-overview.md#general-media-streaming)
       - [**A videó igényalapú videóstreaming**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Nagyméretű fájl letöltése**](cdn-optimization-overview.md#large-file-download)
       - [**Dinamikus helygyorsítás**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Új végpont létrehozásához kattintson a **Hozzáadás** gombra.
   
    A végpont a létrehozás után megjelenik a profil végpontjainak listájában.
    
    ![CDN-végpont](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Mivel némi időre van szükség a regisztráció propagálásához, a végpont nem vehető használatba azonnal: 
    - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
    - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
    - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 90 percen belül fejeződik be. 
   
    Ha megpróbálja használni a CDN-tartománynevet, mielőtt a végpont-konfiguráció a jelenléti pontok (POP) kiszolgálók, kaphat egy HTTP 404-es válasz állapota. Ha már több óra, mivel a végpont létrehozása és a egy 404-es válaszkódot kap, továbbra is küldtük láthatóak [Azure CDN-végpontok hibaelhárítása, amelyek 404-es állapotkódot vissza](cdn-troubleshoot-endpoint.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Végpont törlése, ha már nincs rá szükség, válassza ki, majd **törlése**. 

## <a name="next-steps"></a>További lépések
Egyéni tartományok kapcsolatos további információkért folytassa az oktatóanyag az egyéni tartomány hozzáadása a CDN-végpont.

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](cdn-map-content-to-custom-domain.md)


