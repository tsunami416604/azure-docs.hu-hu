---
title: "Az Azure CDN használatának első lépései | Microsoft Docs"
description: "Ez a témakör azt mutatja be, hogyan engedélyezhető az Azure Content Delivery Network (CDN). Az oktatóanyag végigvezeti Önt egy új CDN-profil és -végpont létrehozásán."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2018
ms.author: mazha
ms.openlocfilehash: 81a88f6495ca9092ca3b55b8ffb3e41def3b4623
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="getting-started-with-azure-cdn"></a>Az Azure CDN használatának első lépései
Ez a cikk egy új CDN-profil és -végpont létrehozásán keresztül ismerteti az Azure Content Delivery Network (CDN) aktiválását.

> [!IMPORTANT]
> A CDN bemutatását, valamint az általa nyújtott szolgáltatások listáját lásd: [Tartalomkézbesítési hálózat (CDN) – áttekintés](cdn-overview.md).
> 
> 

## <a name="create-a-new-cdn-profile"></a>Új CDN-profil létrehozása
A CDN-profil CDN-végpontok gyűjteménye. Minden profil egy vagy több CDN-végpontot tartalmazhat. Ha rendszerezni szeretné a CDN-végpontokat internetes tartomány, webalkalmazás vagy más feltétel alapján, több profilt is használhat.

> [!NOTE]
> Az Azure-előfizetések alapértelmezett korlátokkal rendelkeznek a következő erőforrásokhoz:
> - A létrehozható CDN-profilok száma
> - A CDN-profilokban létrehozható végpontok száma 
> - A végpontokra leképezhető egyéni tartományok száma
>
> A CDN-előfizetés korlátaival kapcsolatos információért lásd a [CDN-korlátokkal](https://docs.microsoft.com/azure/azure-subscription-service-limits#cdn-limits) kapcsolatos szakaszt.
>
> A CDN-díjszabást a CDN-profilok szintjén alkalmazzuk. Ezért ha több Azure CDN-tarifacsomagot szeretne vegyesen használni, több CDN-profilt kell létrehoznia.
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Új CDN-végpont létrehozása
**Új CDN-végpont létrehozása**

1. Az [Azure Portalon](https://portal.azure.com) lépjen a CDN-profilra. Lehetséges, hogy a profilt az előző lépésben az irányítópulton rögzítette. Ha mégsem, akkor a **Minden szolgáltatás**, majd a **CDN-profilok** elemre kattintva megkeresheti. A **CDN-profilok** ablaktáblán válassza ki azt a profilt, amelyhez a végpontot hozzá szeretné adni. 
   
    Megjelenik a CDN-profil ablaktábla.
   
    ![CDN-profil][cdn-profile-settings]

2. Válassza ki a **Végpont** beállítását.
   
    ![Végpont hozzáadása gomb][cdn-new-endpoint-button]
   
    Megjelenik a **Végpont hozzáadása** panel.
   
    ![Végpont hozzáadása panel][cdn-add-endpoint]

3. A **Név** mezőben adjon meg egy egyedi nevet a CDN-végpont számára. A rendszer ezt a nevet használja a gyorsítótárazott erőforrások eléréséhez a(z) `<endpointname>.azureedge.net` tartományban.

4. A **Forrása típusa** mezőben válassza ki a forrástípust. Azure Storage-fiók esetén válassza a **Storage**, Azure Cloud Service szolgáltatás esetén a **Cloud Service**, Azure Web Apps esetén a **Webalkalmazás**, az összes többi nyilvánosan elérhető (az Azure rendszerben vagy máshol tárolt) webkiszolgáló-forrás esetén pedig az **Egyéni forrás** lehetőséget.
   
    ![A CDN-forrása típusa](./media/cdn-create-new-endpoint/cdn-origin-type.png)

5. A **Forrás állomásneve** legördülő menüben válassza ki vagy adja meg a forrástartományt. A legördülő menüben a 4. lépésben megadott típusú összes rendelkezésre álló forrás szerepel. Ha a Forrása típusa számára az **Egyéni forrás** beállítást választotta, akkor adja meg az egyéni forrás tartományát.
    
6. A **Forrás elérési útja** mezőbe írja be a gyorsítótárazni kívánt erőforrások elérési útját. A mezőt üresen is hagyhatja, ha engedélyezni szeretné az 5. lépésben megadott tartomány bármely erőforrásának gyorsítótárazását.
    
7. A **Forrás állomásfejléce** mezőben adja meg azt az állomásfejlécet, amelyet az Azure CDN-nek minden egyes kérelemmel el kell küldenie, vagy hagyja meg az alapértelmezett értéket.
   
   > [!WARNING]
   > Bizonyos típusú források – például az Azure Storage és a Web Apps – esetén az állomásfejlécnek egyeznie kell a forrás tartományával. Hacsak a forrás a tartománytól eltérő állomásfejléc használatát nem igényli, hagyja meg az alapértelmezett értéket.
   > 
    
8. A **Protokoll** és a **Forrásport** mezőben adja meg a forráson az erőforrások eléréséhez használt protokollokat és portokat. Legalább egy protokollt (a HTTP vagy a HTTPS protokollt) ki kell választani. A HTTPS-tartalmak eléréséhez a CDN által biztosított tartományt (`<endpointname>.azureedge.net`) használja. 
   
   > [!NOTE]
   > A **Forrásport** értéke csak azt befolyásolja, hogy a végpont melyik portot használja az információk forrásról való lekéréséhez. Magát a végpontot a végfelhasználók – a **Forrásport** értékétől függetlenül – csak az alapértelmezett HTTP- és HTTPS-porton (azaz a 80-as és a 443-as porton) érik el.  
   > 
   > Az **Akamai Azure CDN**-profilok végpontjai esetén a forrásportok számára nem áll rendelkezésre a teljes TCP-porttartomány. A nem engedélyezett forrásportok listáját lást: [Azure CDN from Akamai Allowed Origin Ports](https://msdn.microsoft.com/library/mt757337.aspx) (Az Akamai Azure CDN engedélyezett forrásportjai).  
   > 
   > A CDN-tartalom HTTPS-kapcsolaton keresztüli elérésére a következő korlátozások vonatkoznak:
   > 
   > * Használja a CDN által biztosított SSL-tanúsítványt. A rendszer nem támogatja a harmadik féltől származó tanúsítványokat.
   > * Az Azure CDN egyéni tartományok HTTPS-támogatása csak a **Verizon Azure CDN** termékekkel érhető el (Standard és Prémium). Nem támogatott az **Akamai Azure CDN** termékeken. További információkért lásd: [HTTPS konfigurálása az Azure CDN egyéni tartományon](cdn-custom-ssl.md).
    
9. Új végpont létrehozásához kattintson a **Hozzáadás** gombra.
   
   A végpont a létrehozás után megjelenik a profil végpontjainak listájában.
    
   ![CDN-végpont][cdn-endpoint-success]
    
   > [!IMPORTANT]
   > Mivel némi időre van szükség a regisztráció propagálásához, a végpont nem vehető használatba azonnal. Az **Akamai Azure CDN** típusú profilok propagálása általában egy percen belül befejeződik. A **Verizon Azure CDN** típusú profilok propagálása általában 90 percen belül befejeződik, ám egyes esetekben több időt is igénybe vehet.
    > 
    > Előfordulhat, hogy 404-es HTTP-válaszkódot kap, ha a CDN-tartománynevet azelőtt próbálja meg használni, mielőtt végbement volna a végpont-konfiguráció POP-kre történő propagálása. Ha a végpont létrehozása óta már több óra is eltelt, mégis 404-es válaszkódot kap, olvassa el a [404-es állapotot visszaadó CDN-végpontok hibaelhárítása](cdn-troubleshoot-endpoint.md) című cikket.
    > 
    > 

## <a name="see-also"></a>Lásd még:
* [Lekérdezési karakterláncot tartalmazó kérelmek gyorsítótárazási viselkedésének vezérlése](cdn-query-string.md)
* [CDN-tartalom leképezése egyéni tartományra](cdn-map-content-to-custom-domain.md)
* [Eszközök előzetes betöltése Azure CDN-végponton](cdn-preload-endpoint.md)
* [CDN-végpont végleges törlése](cdn-purge-endpoint.md)
* [404-es állapotot visszaadó CDN-végpontok hibaelhárítása](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
