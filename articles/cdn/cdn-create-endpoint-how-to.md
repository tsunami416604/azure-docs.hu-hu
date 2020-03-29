---
title: Azure CDN-végpont létrehozása | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan hozhat létre egy új Azure Content Delivery Network (CDN) végpontot, beleértve a speciális beállításokat.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: afd9599dd1e396dea6975c397688e4b97371fd6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593936"
---
# <a name="create-an-azure-cdn-endpoint"></a>Azure CDN-végpont létrehozása
Ez a cikk ismerteti az [Azure Content Delivery Network (CDN)](cdn-overview.md) végpont egy meglévő CDN-profil létrehozásához megadott összes beállítást. Miután létrehozott egy profilt és egy végpontot, elkezdheti a tartalom kézbesítését az ügyfelek számára. A profil és a végpont létrehozásának rövid [útmutatója: Azure CDN-profil és végpont létrehozása](cdn-create-new-endpoint.md)című témakörben található.

## <a name="prerequisites"></a>Előfeltételek
CdN-végpont létrehozása előtt létre kell hoznia legalább egy CDN-profilt, amely egy vagy több CDN-végpontot tartalmazhat. Ha rendszerezni szeretné a CDN-végpontokat internetes tartomány, webalkalmazás vagy más feltétel alapján, több profilt is használhat. Mivel a CDN-díjszabás a CDN-profil szintjén van alkalmazva, több CDN-profilt kell létrehoznia, ha az Azure CDN-díjszabási szintek keverékét szeretné használni. CDN-profil létrehozásáról az [Új CDN-profil létrehozása](cdn-create-new-endpoint.md#create-a-new-cdn-profile)című témakörben található.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-a-new-cdn-endpoint"></a>Új CDN-végpont létrehozása

1. Az [Azure Portalon](https://portal.azure.com) lépjen a CDN-profilra. Lehetséges, hogy a profilt az előző lépésben az irányítópulton rögzítette. Ha mégsem, akkor a **Minden szolgáltatás**, majd a **CDN-profilok** elemre kattintva megkeresheti. A **CDN-profilok** ablaktáblán válassza ki azt a profilt, amelyhez a végpontot hozzá szeretné adni. 
   
    Megjelenik a CDN-profil ablaktábla.

2. Válassza ki a **Végpont** beállítását.
   
    ![CDN-végpont kijelölése](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    Megjelenik a **Végpont hozzáadása** oldal.
   
    ![Végpontlap hozzáadása](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. A **Név** mezőben adjon meg egy egyedi nevet a CDN-végpont számára. Ezzel a névvel érhető el a gyorsítótárazott erőforrások a>.azureedge.net _ \<tartományvégpont-hívó helyen. _

4. Az **Origin típushoz**válasszon a következő forrástípusok közül: 
   - Az Azure Storage **tárolása**
   - **Felhőszolgáltatás** az Azure Felhőszolgáltatásokhoz
   - **Web App** for Azure Web Apps alkalmazásokhoz
   - **Egyéni eredet** bármely más nyilvánosan elérhető forráswebkiszolgálóhoz (az Azure-ban vagy máshol üzemeltetve)

5. Az **Origin állomásnév**területen válassza ki vagy adja meg az eredeti kiszolgálótartományát. A legördülő lista a 4. Ha **az Egyéni eredetet** választotta forrástípusként, adja meg az egyéni forráskiszolgáló tartományát.
    
6. Az **Origin elérési úthoz**adja meg a gyorsítótárazni kívánt erőforrások elérési útját. Ha engedélyezni szeretné az 5.
    
7. A **Forrás állomásfejléce** mezőben adja meg azt az állomásfejlécet, amelyet az Azure CDN-nek minden egyes kérelemmel el kell küldenie, vagy hagyja meg az alapértelmezett értéket.
   
   > [!NOTE]
   > Bizonyos típusú források – például az Azure Storage és a Web Apps – esetén az állomásfejlécnek egyeznie kell a forrás tartományával. Hacsak a forrás a tartománytól eltérő állomásfejléc használatát nem igényli, hagyja meg az alapértelmezett értéket.
   > 
    
8. **Protokoll-** és **Origin-port**esetén adja meg azokat a protokollokat és portokat, amelyeket az erőforrások hoz való hozzáféréshez az eredeti kiszolgálón kell használni. Legalább egy protokollt (a HTTP vagy a HTTPS protokollt) ki kell választani. A HTTPS-tartalom eléréséhez használja a CDN által biztosított tartományt_\<(végpontnév>_.azureedge.net). 
   
   > [!NOTE]
   > Az **Origin port** értéke csak azt a portot határozza meg, amelyet a végpont az eredeti kiszolgálóról származó információk lekéréséhez használ. Magát a végpontot a végfelhasználók – a **Forrásport** értékétől függetlenül – csak az alapértelmezett HTTP- és HTTPS-porton (azaz a 80-as és a 443-as porton) érik el.  
   > 
   > Az **Akamai Azure CDN**-profilok végpontjai esetén a forrásportok számára nem áll rendelkezésre a teljes TCP-porttartomány. A nem engedélyezett forrásportok listáját lást: [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)) (Az Akamai Azure CDN engedélyezett forrásportjai).  
   > 
   > Az Azure CDN egyéni tartományok HTTPS-támogatása nem támogatott az **Akamai-termékekből származó Azure CDN-ben.** További információkért lásd: [HTTPS konfigurálása az Azure CDN egyéni tartományon](cdn-custom-ssl.md).
    
9. Az **Optimalizált mezőben**válasszon olyan optimalizálási típust, amely a legjobban megfelel a végpont által kézbesíteni kívánt forgatókönyvnek és tartalomtípusnak. További információ: [Az Azure CDN optimalizálása a tartalomkézbesítés típusához.](cdn-optimization-overview.md)

    A következő optimalizálási típusbeállítások támogatottak a profiltípusszerint:
    - **Azure CDN Standard microsoftos profilokból:**
       - [**Általános webes kézbesítés**](cdn-optimization-overview.md#general-web-delivery)

    - **Azure CDN Standard a Verizontól** és **az Azure CDN Premium a Verizon-profilokból:**
       - [**Általános webes kézbesítés**](cdn-optimization-overview.md#general-web-delivery)
       - [**Dinamikus helygyorsulás**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - **Azure CDN Standard akamai profilokból:**
       - [**Általános webes kézbesítés**](cdn-optimization-overview.md#general-web-delivery)
       - [**Általános médiaadatfolyam-továbbítás**](cdn-optimization-overview.md#general-media-streaming)
       - [**Igény szerinti videómédia-adatfolyam-továbbítás**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Nagy fájl letöltése**](cdn-optimization-overview.md#large-file-download)
       - [**Dinamikus helygyorsulás**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Új végpont létrehozásához kattintson a **Hozzáadás** gombra.
   
    A végpont a létrehozás után megjelenik a profil végpontjainak listájában.
    
    ![CDN-végpont](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Mivel némi időre van szükség a regisztráció propagálásához, a végpont nem vehető használatba azonnal: 
    - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
    - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
    - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 90 percen belül fejeződik be. 
   
    Ha még a végpontkonfiguráció propagálása előtt próbálja meg használni a CDN-tartománynevet, előfordulhat, hogy HTTP 404-es válaszállapotot kap. Ha már több óra telt el a végpont létrehozása óta, és még mindig 404-es válaszállapotot kap, olvassa [el a 404-es állapotkódot visszaadó Azure CDN-végpontok hibaelhárítása című témakört.](cdn-troubleshoot-endpoint.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha törölni szeretne egy végpontot, amikor már nincs rá szükség, jelölje ki, majd válassza a **Törlés**lehetőséget. 

## <a name="next-steps"></a>További lépések
Az egyéni tartományokról az oktatóanyaggal, amely egyéni tartományt aCDN-végponthoz ad hozzá.

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](cdn-map-content-to-custom-domain.md)


