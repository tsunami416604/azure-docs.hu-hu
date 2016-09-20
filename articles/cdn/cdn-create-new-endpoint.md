<properties
     pageTitle="Az Azure CDN szolgáltatás használata | Microsoft Azure"
     description="Ez a témakör azt mutatja be, hogyan engedélyezhető a CDN (Content Delivery Network – tartalomkézbesítési hálózat) az Azure számára. Az oktatóanyag végigvezeti Önt egy új CDN-profil és -végpont létrehozásán."
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="07/28/2016" 
     ms.author="casoper"/>

# Az Azure CDN szolgáltatás használata  

Ez a témakör egy új CDN-profil és -végpont létrehozásán keresztül vezeti Önt végig az Azure CDN aktiválásán.

>[AZURE.IMPORTANT] A CDN működésének bemutatásáért, valamint az általa nyújtott szolgáltatások listájáért tekintse meg a [Tartalomkézbesítési hálózat (CDN) – áttekintés](./cdn-overview.md) című dokumentumot.

## Új CDN-profil létrehozása

A CDN-profil CDN-végpontok gyűjteménye.  Minden profil egy vagy több CDN-végpontot tartalmaz.  Előfordulhat, hogy több profil használatával rendszerezni szeretné a CDN-végpontokat internetes tartomány, webalkalmazás vagy más feltétel alapján.

> [AZURE.NOTE] Alapértelmezés szerint egy Azure-előfizetéssel nyolc CDN-profil használható. Az egyes CDN-profilokban legfeljebb tíz CDN-végpont lehet.
>
> A CDN-díjszabást a CDN-profilok szintjén alkalmazzuk. Ha több Azure CDN-tarifacsomagot szeretne vegyesen használni, több CDN-profilra lesz szüksége.

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## Új CDN-végpont létrehozása

**Új CDN-végpont létrehozása**

1. Az [Azure portálon](https://portal.azure.com) lépjen a CDN-profilra.  Lehetséges, hogy a profilt az előző lépésben az irányítópulton rögzítette.  Amennyiben nem rögzítette, kattintson **Tallózás** gombra, majd a **CDN-profilok** elemre, végül kattintson arra a profilra, amelyet hozzá szeretne adni a végponthoz.

    Megjelenik a CDN-profil panelje.

    ![CDN-profil][cdn-profile-settings]

2. Kattintson a **Végpont hozzáadása** gombra.

    ![Végpont hozzáadása gomb][cdn-new-endpoint-button]

    Megjelenik a **Végpont hozzáadása** panel.

    ![Végpont hozzáadása panel][cdn-add-endpoint]

3. Adja meg a CDN-végpont kívánt nevét a **Név** mezőben.  A rendszer ezt a nevet fogja használni a gyorsítótárazott erőforrások eléréséhez a `<endpointname>.azureedge.net` tartományban.

4. A **Forrása típusa** legördülő menüben válassza ki a forrástípust.  Azure Storage-fiók esetén válassza a **Storage**, Azure Cloud Service szolgáltatás esetén a **Cloud Service **, Azure Web Apps esetén a **Webalkalmazás**, az összes többi nyilvánosan elérhető (az Azure rendszerben vagy máshol tárolt) webkiszolgáló-forrás esetén pedig az **Egyéni forrás** lehetőséget.

    ![A CDN-forrása típusa](./media/cdn-create-new-endpoint/cdn-origin-type.png)
        
5. A **Forrás állomásneve** legördülő menüben válassza ki vagy adja meg a forrástartományt.  A legördülő menüben szerepelni fog a 4. lépésben megadott típusú összes rendelkezésre álló forrás.  Ha a **Forrása típusa** számára az *Egyéni forrás* beállítást választotta ki, akkor be kell írnia az egyéni forrás tartományát.

6. A **Forrás elérési útvonala** szövegmezőbe írja be a gyorsítótárazni kívánt erőforrások elérési útját. A mezőt üresen is hagyhatja, ha engedélyezni szeretné az 5. lépésben megadott tartomány bármely erőforrásának gyorsítótárazását.

7. A **Forrás állomásfejléce** mezőben adja meg azt az állomásfejlécet, amelyet a CDN-nek minden egyes kérelemmel el kell küldenie; vagy hagyja meg az alapértelmezett értéket.

    > [AZURE.WARNING] Bizonyos típusú források – például az Azure Storage és a Web Apps – esetén az állomásfejlécnek egyeznie kell a forrás tartományával. Hacsak a forrás a tartománytól eltérő állomásfejléc használatát nem igényli, hagyja meg az alapértelmezett értéket.

8. A **Protokoll** és a **Forrásport** mezőben adja meg a forráson az erőforrások eléréséhez használt protokollokat és portokat.  Legalább egy protokollt (a HTTP vagy a HTTPS protokollt) ki kell választani.
    
    > [AZURE.NOTE] A **Forrásport** értéke csak azt befolyásolja, hogy a végpont melyik portot használja az információk forrásról való lekéréséhez.  Magát a végpontot a végügyfelek – a **Forrásport** értékétől függetlenül – csak az alapértelmezett HTTP- és HTTPS-porton (azaz a 80-as és a 443-as porton) keresztül érik el.  
    >
    > Az **Akamai Azure CDN** típusú végpontok esetén a források számára nem áll rendelkezésre a teljes TCP-porttartomány.  A nem engedélyezett forrásportok listáját lást: [Azure CDN from Akamai Allowed Origin Ports](https://msdn.microsoft.com/library/mt757337.aspx) (Az Akamai Azure CDN engedélyezett forrásportjai).  
    >
    > A CDN-tartalom HTTPS-kapcsolaton keresztüli elérésére a következő korlátozások vonatkoznak:
    > 
    > - A CDN által biztosított SSL-tanúsítványt kell használni. A rendszer nem támogatja a harmadik féltől származó tanúsítványokat.
    > - A HTTPS-tartalom eléréséhez a CDN által biztosított tartományt (`<endpointname>.azureedge.net`) kell használni. A HTTPS nem támogatott az egyéni tartománynevek (CNAME) esetén, mivel a CDN jelenleg nem támogatja az egyéni tanúsítványokat.

9. Az új végpont létrehozásához kattintson a **Hozzáadás** gombra.

10. A végpont a létrehozás után megjelenik a profil végpontjainak listájában. A listanézetben látható a gyorsítótárazott tartalom eléréséhez használható URL-cím, valamint a forrástartomány is.

    ![CDN-végpont][cdn-endpoint-success]

    > [AZURE.IMPORTANT] A végpont nem vehető használatba azonnal, mivel némi időre van szükség a regisztráció CDN-rendszeren belüli propagálásához.  Az <b>Akamai Azure CDN</b> típusú profilok propagálása általában egy percen belül befejeződik.  A <b>Verizon Azure CDN</b> típusú profilok propagálása általában 90 percen belül befejeződik, ám egyes esetekben több időt is igénybe vehet.
    >    
    > A felhasználók 404-es HTTP-válaszkódot kapnak, ha a CDN-tartománynevet azelőtt próbálják meg használni, mielőtt végbement volna a végpont-konfiguráció POP-kre történő propagálása.  Ha a végpont létrehozása óta már több óra is eltelt, mégis 404-es válaszkódot kap, olvassa el a [404-es állapotot visszaadó CDN-végpontok hibaelhárítása](cdn-troubleshoot-endpoint.md) című cikket.


##Lásd még:
- [Lekérdezési karakterláncot tartalmazó kérelmek gyorsítótárazási viselkedésének vezérlése](cdn-query-string.md)
- [CDN-tartalom leképezése egyéni tartományra](cdn-map-content-to-custom-domain.md)
- [Eszközök előzetes betöltése Azure CDN-végponton](cdn-preload-endpoint.md)
- [CDN-végpont végleges törlése](cdn-purge-endpoint.md)
- [404-es állapotot visszaadó CDN-végpontok hibaelhárítása](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png



<!--HONumber=sep16_HO1-->


