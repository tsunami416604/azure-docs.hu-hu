<properties
    pageTitle="Media Services-fiók létrehozása | Microsoft Azure"
    description="Azt ismerteti, hogyan kell létrehozni az Azure rendszerben egy új Azure Media Services-fiókot."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="juliako"/>



# Azure Media Services-fiók létrehozása

> [AZURE.SELECTOR]
- [Portál](media-services-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)


> [AZURE.NOTE] Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](/pricing/free-trial/?WT.mc_id=A261C142F).
 
A klasszikus Azure portálon gyorsan létrehozhat egy Azure Media Services-fiókot. A fiókkal hozzáférhet olyan Media Services-szogáltatásokhoz, amelyekkel médiatartalmakat tárolhat, titkosíthat, kódolhat, kezelhet és továbbíthat az Azure környezetben. A Media Services-fiók létrehozásával egy időben létrejön egy kapcsolódó tárfiók is (illetve egy már meglévőt is használhat) ugyanabban a földrajzi régióban, ahol a Media Services-fiók is található.

Ez a cikk leírja, hogyan hozhat létre egy új Media Services-fiókot a Gyors létrehozás módszerrel, és hogyan kapcsolhat hozzá egy tárfiókot.

<a id="concepts"></a>
## Alapelvek

A Media Services szolgáltatásainak eléréséhez két kapcsolódó fiók szükséges:

-   **Egy Media Services-fiók** A fiók hozzáférést biztosít több felhőalapú, az Azure szolgáltatásban elérhető Media Services-szolgáltatáshoz. A Media Services-fiók nem tárol tényleges médiatartalmakat. Ehelyett metaadatokat tárol a fiókban található médiatartalmakról és médiafeldolgozási feladatokról. A fiók létrehozásakor ki kell választania egy elérhető Media Services-régiót. A választott régió egy adatközpont, amely a fiók metaadat-rekordjait tárolja.

    Az elérhető Media Services (AMS)-régiók a következők: Észak-Európa, Nyugat-Európa, USA nyugati régiója, USA keleti régiója, Délkelet-Ázsia, Kelet-Ázsia, Nyugat-Japán, Kelet-Japán. A Media Services nem használ affinitáscsoportokat.
    
    Az AMS már a következő adatközpontokban is elérhető: Dél-Brazília, Nyugat-India, Dél-India és Közép-India. A klasszikus Azure portált már [Media Services-fiókok létrehozására](media-services-create-account.md#create-a-media-services-account-using-quick-create) és az [itt](https://azure.microsoft.com/documentation/services/media-services/) leírt különböző feladatok elvégzésére is használhatja. Azonban ezekben az adatközpontokban a Live Encoding nem támogatott. Emellett ezekben az adatközpontokban a kódoláshoz fenntartott egységeknek sem minden típusa érhető el.
    
    - Dél-Brazília: Csak a standard és alapszintű kódoláshoz fenntartott egységek érhetők el
    - Nyugat-, Dél- és Közép-India: Csak az alapszintű kódoláshoz fenntartott egységek érhetők el


-   **Egy kapcsolódó tárfiók**. Az Ön tárfiókja egy Azure Storage-fiók, amely a Media Services-fiókjához van társítva. A tárfiók blobtárolást biztosít a médiafájlokhoz, és ugyanabban a földrajzi régióban kell elhelyezkednie, ahol a Media Services-fiók. Egy Media Services-fiók létrehozásakor választhat, hogy egy ugyanabban a régióban található, meglévő tárfiókot használ, vagy létrehoz egy újat ugyanabban a régióban. Egy Media Services-fiók törlésekor a kapcsolódó tárfiókban található blobok nem törlődnek.

<a id="quick"></a>
## Media Services-fiók létrehozása a Gyors létrehozással

1. A [klasszikus Azure portál][] kattintson az **Új** > **Média Services** > **Gyors létrehozás** lehetőségre.

![Media Services-fiók gyors létrehozása](./media/media-services-create-account/wams-QuickCreate.png)

2. A **NAME** (NÉV) mezőbe írja be az új fiók nevét. A Media Services-fiók nevének 3–24 karakter hosszúságúnak kell lennie, és csak kisbetűket és számokat tartalmazhat, szóközök nélkül.

3. A **REGION** (RÉGIÓ) mezőben válassza ki a földrajzi régiót, ahol a Media Services-fiók metaadatrekordjai tárolva lesznek. A legördülő listában kizárólag az elérhető Media Services-régiók jelennek meg.

4. A **STORAGE ACCOUNT** (TÁRFIÓK) mezőben válasszon ki egy tárfiókot, amely a Media Services-fiókhoz tartozó médiatartalmak blobtárolását biztosítja. Választhat, hogy egy meglévő, a Media Services-fiókkal azonos földrajzi régióban található tárfiókot használ, vagy létrehoz egy új tárfiókot. Az újonnan létrehozott tárfiókok ugyanabban a régióban jönnek létre.

5. Ha egy új tárfiókot hozott létre, a **NEW STORAGE ACCOUNT NAME** (ÚJ TÁRFIÓK NEVE) mezőben adjon meg egy nevet a tárfióknak. A tárfiókok elnevezési szabályai ugyanazok, mint a Media Services-fiókok esetében.

6. Az űrlap alsó részén kattintson a **Gyors létrehozás** elemre.

Az ablak alján az üzenetmezőben figyelemmel kísérheti a folyamat állapotát.

A fiók sikeres létrejöttekor az állapot Aktívra változik. Megnyílik a **Media Services** oldal, amelyen megjelenik az új fiók.

Az oldal alján megjelenik a **MANAGE KEYS** (KULCSOK KEZELÉSE) gomb. Ha erre a gombra kattint, egy lapon megjelenik a Media Services-fiók neve, valamint az elsődleges és másodlagos kulcsok. A Media Services-fiókhoz való szoftveres hozzáféréshez szükség lesz a fiók nevére és az elsődleges kulcsra.

![Media Services oldal](./media/media-services-create-account/wams-mediaservices-page.png)

A fiók nevére való dupla kattintáskor alapértelmezés szerint megjelenik a **Gyorsindítás** oldal. Ezen az oldalon elvégezhet néhány olyan felügyeleti feladatot, amelyek a portál más oldalain is elérhetők. Például feltölthet videofájlokat erről az oldalról, vagy a **CONTENT** (TARTALOM) oldalról is.

Emellett megtekinthet kódokat, amelyek az Azure Media Services SDK használatával végzik el a következő feladatokat: videók feltöltése, kódolása és közzététele. Ha a **WRITE SOME CODE** (KÓD ÍRÁSA) szakasz alatti hivatkozásokra kattint, kimásolhatja a kódokat és felhasználhatja őket a saját alkalmazásában.



##Media Services képzési tervek

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Visszajelzés küldése

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## Következő lépések

- [Megismerheti, hogyan továbbíthat Video-on-Demand (VoD) tartalmakat a .NET SDK használatával](media-services-dotnet-get-started.md)

- [Létrehozhat csatornákat a .NET SDK használatával, amelyek egyféle sávszélességű adatfolyamokat valós idejű kódolással többféle sávszélességűvé alakítanak](media-services-dotnet-creating-live-encoder-enabled-channel.md)

<!-- Reusable paths. -->

<!-- Anchors. -->
  [Alapelvek]: #concepts
  [Előkészületek]: #begin
  [Útmutató: Media Services-fiók létrehozása a Gyors létrehozással]: #quick

<!-- URLs. -->
  [Webplatform-telepítő]: http://go.microsoft.com/fwlink/?linkid=255386

  [klasszikus Azure portál]: http://manage.windowsazure.com/



<!--HONumber=Sep16_HO4-->


