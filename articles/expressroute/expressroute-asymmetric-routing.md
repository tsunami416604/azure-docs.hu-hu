<properties
   pageTitle="Aszimmetrikus útválasztás | Microsoft Azure"
   description="Ebben a cikkben arra adunk választ, hogy hogyan oldja meg a problémákat, amelyek akkor lépnek fel, ha az aszimmetrikus útválasztást használó hálózatban ugyanazon célhelyre több kapcsolat is mutat."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="osamazia"/>

# Aszimmetrikus útválasztás több hálózati elérési úttal

A cikk leírja, hogy hogyan követhet a kimenő és a bejövő forgalom különböző utakat, amikor a forrás és a cél között több elérési út is rendelkezésre áll.

Az aszimmetrikus útválasztás megértéséhez két gondolatot kell átlátnunk. Elsőként azt, hogy milyen hatással van a hálózatra, ha több elérési út is létezik. A másik pedig, hogy hogyan működnek ilyenkor az állapotot tartó eszközök, például a tűzfalak. Ezeket az eszközöket állapot-nyilvántartó eszközöknek nevezzük. A fent említett két tényező ahhoz vezethet, hogy egy állapot-nyilvántartó eszköz eldobja a forgalmat, mivel nem látja, hogy az a forgalom önmagától származik.

## Több hálózati elérési út

Ha a vállalati hálózat csupán egyetlen, az internetszolgáltatón átmenő kapcsolattal rendelkezik az internethez, minden, az internet felé irányuló, illetve az internetről visszaérkező forgalom ugyanazt az utat járja be. A vállalatok azonban gyakran több kapcsolatcsoportot is vásárolnak, mivel ezek redundáns elérési útként szolgálhatnak, illetve hozzájárulnak a hálózat állásidejének csökkentéséhez. Ebben az esetben előfordulhat, hogy a hálózatról kilépő, az internet felé irányuló forgalom az egyik kapcsolaton, míg a visszatérő forgalom egy másik kapcsolaton halad át. Ezt a jelenséget – vagyis amikor a visszatérő forgalom más utat jár be, mint az eredeti – nevezzük aszimmetrikus útválasztásnak.

![Útválasztás (3)](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Az előző definícióban az internetet említettük, ám a leírás bármilyen olyan hálózatra érvényes, amelyben több elérési út működik. További példák: ugyanahhoz a célhoz vezető internetes és privát útvonal, ugyanahhoz a célhoz vezető több privát útvonal és így tovább. 

A forrás és a cél közötti összes útválasztó kiszámítja, hogy melyik a cél eléréséhez rendelkezésre álló legrövidebb út. A legrövidebb út meghatározásában két tényező játszik szerepet.

1.  A külső hálózatok közötti útválasztást az általában csak BGP néven emlegetett útválasztási protokoll, a peremátjáró protokoll (Border Gateway Protocol) határozza meg. A BGP átveszi a szomszédok hirdetményeit, és különböző műveleteket futtat rajtuk, majd megállapítja a célhoz vezető legjobb utat, és beírja azt az útvonaltáblába.
2.  Az útvonal alhálózati maszkjának hossza. Ha ugyanahhoz az IP-címhez több hirdetményt, de különböző alhálózati maszkokat kap, a rendszer a hosszabb alhálózati maszkkal rendelkező hirdetményt részesíti előnyben, mivel ezt tartja pontosabb útvonalnak.

## Állapot-nyilvántartó eszközök

Az útválasztók az útválasztás megállapítása során a csomag IP-fejlécét veszik alapul. Bizonyos eszközök azonban mélyebben is megvizsgálják a csomagot. Ezek az eszközök általában a 4. réteg (TCP/UDP) vagy a 7. réteg (alkalmazásréteg) fejlécét ellenőrzik. Ezeknek az eszközöknek a célja általában a biztonság kialakítása vagy a sávszélesség optimalizálása. Gyakori állapot-nyilvántartó eszközök például a tűzfalak. A tűzfalak különböző mezők, például a protokoll, a TCP/UDP-port vagy az URL-fejlécek alapján engedik át vagy fordítják vissza az illesztőfelületekre érkező csomagokat. A csomagok vizsgálata jelentős számítási terhelést ró az eszközre. A teljesítmény javítása érdekében a tűzfalak általában csak egy adatfolyam első csomagját vizsgálják meg. Ha a csomag engedélyezhető, a tűzfal állapottáblázatában megőrzi az adatfolyam információit. Az adatfolyam tartozó további csomagokat az első csomagnál hozott döntés alapján engedélyezi vagy tiltja a tűzfal. Ez azt jelenti, hogy ha a tűzfal eldobja az olyan beérkező csomagokat, amelyek egy meglévő adatfolyam részei, de a tűzfalnak nincs róluk korábbról információja.

## Aszimmetrikus útválasztás az ExpressRoute-tal

Ha az ExpressRoute segítségével csatlakozik a Microsofthoz, a következő változások történnek hálózatában.

1.  Több kapcsolat jön létre a Microsofthoz. Az egyik kapcsolat a meglévő internetkapcsolat, a másik az ExpressRoute-on keresztüli kapcsolat. Előfordulhat, hogy a forgalom egy része az interneten keresztül jut el a Microsofthoz, de az ExpressRoute-on át tér vissza, és fordítva.
2.  Az ExpressRoute konkrétabb IP-címeket küld. Így az ExpressRoute-on keresztül kínált szolgáltatásokra vonatkozó, a hálózat és a Microsoft közötti forgalom mindig az ExpressRoute-ot fogja előnyben részesíteni. 

Hogy jobban megértsük a fenti két pont hatását, vegyünk át néhány példát. Tegyük fel, hogy csak egyetlen internet felé néző kapcsolatcsoport van a birtokában, és az összes Microsoft-szolgáltatást az interneten keresztül használja. A hálózatról a Microsoft felé küldött, majd visszatérő forgalom ugyanazon az internetes kapcsolaton halad át, és egy tűzfalon is keresztülmegy. A tűzfal az első csomag észlelésekor rögzíti az adatfolyamot, és engedélyezi a visszatérő csomagokat is, mivel a adatfolyam már szerepel az állapottáblázatban.

![Útválasztás (1)](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)


Most bekapcsoljuk az ExpressRoute-ot, és felhasználjuk a Microsoft által az ExpressRoute-on keresztül kínált szolgáltatásokat. A többi Microsoft-szolgáltatást az interneten keresztül használjuk fel. A peremen egy különálló tűzfalat helyezünk üzembe, amely csatlakozik az ExpressRoute-hoz. A Microsoft az ExpressRoute-on keresztül konkrétabb előtagokat hirdet meg a hálózaton a kérdéses szolgáltatásokhoz. Az útválasztási infrastruktúra ezért ezeknél az előtagoknál az ExpressRoute-útvonalat fogja előnyben részesíteni. Ha nem az ExpressRoute-on keresztül hirdeti meg nyilvános IP-címeit, a Microsoft az interneten keresztül fog kommunikálni a nyilvános IP-címekkel. Ezért a hálózatból a Microsoft felé irányuló kimenő forgalom az ExpressRoute-ot fogja használni, míg a Microsofttól visszaérkező forgalom az internetet. Ha a peremen működő tűzfal az állapottáblázatban nem szereplő adatfolyamhoz tartozó válaszcsomagot észlel, eldobja a visszatérő forgalmat. 

Ha ugyanazt a NAT-készletet használja az ExpressRoute-hoz és az internethez, hasonló problémák léphetnek fel a hálózatban működő, privát IP-címmel rendelkező ügyfeleknél. A Windows Update és más hasonló szolgáltatások felé irányuló kéréseket az interneten keresztül küldi el a hálózat, mivel ezeknek a szolgáltatásoknak az IP-címeit nem az ExpressRoute hirdeti meg. A visszatérő forgalom azonban az ExpressRoute-on keresztül fog érkezni. Ha a Microsoft az internettől és az ExpressRoute-tól megegyező alhálózati maszkkal ellátott IP-címet kap, az ExpressRoute-ot részesíti előnyben. Ha a peremhálózatban működő, az ExpressRoute felé néző tűzfalnak vagy más állapot-nyilvántartó eszköznek nincs előzetes információja egy adatfolyamról, eldobja az ahhoz az adatfolyamhoz tartozó csomagokat. 

## Megoldások az aszimmetrikus útválasztásra

Az aszimmetrikus útválasztás következtében fellépő problémákat két fő módon lehet megoldani. Az egyik az útválasztás, a másik a forrásalapú NAT (SNAT). 

1. Útválasztás 

    - Gondoskodjon róla, hogy a nyilvános IP-címeket a megfelelő távoli hálózati kapcsolat hirdesse meg. Tegyük fel, hogy az internetet szeretné használni a forgalom hitelesítéséhez, az ExpressRoute-ot pedig a levelezési forgalomhoz. Ebben az esetben nem jó, ha az ADFS nyilvános IP-címeket az ExpressRoute-on keresztül hirdeti meg. Ugyanígy a helyszíni ADFS-kiszolgáló sem kaphat ExpressRoute-on keresztül fogadott IP-címeket. Az ExpressRoute-on keresztül kapott útvonalak konkrétabbak, ezért ha ilyeneket kap, a rendszer az ExpressRoute-útvonalat részesíti előnyben a Microsoft felé irányuló hitelesítési forgalomnál, ami aszimmetrikus útválasztáshoz vezet.

    - Ha az ExpressRoute-ot szeretné használni a hitelesítéshez, ügyeljen rá, hogy az ExpressRoute-on keresztül, NAT nélkül hirdesse meg ADFS nyilvános IP-címeit. Így a Microsofttól származó, a helyszíni ADFS-kiszolgáló felé irányuló forgalom az ExpressRoute-on keresztül halad át, és az ügyféltől származó, a Microsoft felé irányuló forgalom is az ExpressRoute-ot fogja használni, mivel a rendszer azt részesíti előnyben az internettel szemben. 

2. Forrásalapú NAT

    Az aszimmetrikus útválasztás által okozott problémák megoldásának másik módját a forrásalapú NAT (SNAT) használata jelenti. Tegyük fel, hogy nem az ExpressRoute-on keresztül hirdeti meg a helyszíni SMTP-kiszolgáló nyilvános IP-címét, mivel az internetet szeretné használni a kommunikációhoz. A Microsoft felől érkező, a helyszíni SMTP-kiszolgáló irányába tartó kérés átszeli az internetet. A forrás NAT egy belső IP-címre irányítja a bejövő kéréseket. Az SMTP-kiszolgálóról visszaérkező forgalom a peremen működő, a NAT biztosítására használt tűzfalon halad át az ExpressRoute helyett. Így a visszatérő forgalom az interneten keresztül fog érkezni. 


![Útválasztás (2)](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## Az aszimmetrikus útválasztás észlelése

A Traceroute a legjobb módszer annak garantálására, hogy a forgalom a várt útvonalat járja be. Ha azt szeretné, hogy a helyszíni SMTP-kiszolgálóról a Microsoft felé haladó forgalom az internetes útvonalat kövesse, végezzen Traceroute műveletet az SMTP-kiszolgáló és az Office 365 között. Az eredmény alapján ellenőrizheti, hogy a forgalom az internet felé, és nem az ExpressRoute felé hagyja el a hálózatot. 





<!--HONumber=sep16_HO1-->


