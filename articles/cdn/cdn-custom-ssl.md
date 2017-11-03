---
title: "Engedélyezheti vagy tilthatja le a HTTPS, az Azure Content Delivery Network egyéni tartomány |} Microsoft Docs"
description: "Megtudhatja, hogyan engedélyezheti vagy tilthatja le a HTTPS az Azure CDN-végpont egy egyéni tartomány."
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: casoper
ms.openlocfilehash: c92f1e20acf55b8bd791fad43f17e162a5cb3847
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="enable-or-disable-https-on-an-azure-content-delivery-network-custom-domain"></a>Engedélyezheti vagy tilthatja le a HTTPS, az Azure Content Delivery Network egyéni tartományt

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

A Microsoft Azure tartalom Delivery Network (CDN) egyéni tartomány HTTPS támogatása lehetővé teszi, hogy a tartalmak biztonságos saját tartománynév használatával, melyekkel biztonságosabbá teheti az átvitel során az adatok SSL használatával. A végpontok közötti munkafolyamat HTTPS engedélyezése az egyéni tartomány egyszerűsödött, egy kattintással engedélyezését, teljes és minden további költség nélkül.

Nagyon fontos adatvédelmi és a webes alkalmazások bizalmas adatok az átvitel alatt adatok integritásának biztosításához. A HTTPS protokoll használatával biztosítja, hogy a bizalmas adatok titkosítva van, amikor az interneten keresztül továbbítja azokat. Biztosít megbízhatónak, hitelesítés, és a webalkalmazások a támadások ellen védi. Azure CDN jelenleg a CDN-végpont támogatja a HTTPS PROTOKOLLT. Például, ha a CDN-végpontot hoz létre az Azure CDN (például https://contoso.azureedge.net), HTTPS alapértelmezés szerint engedélyezve van. Most az egyéni tartomány HTTPS, engedélyezheti a biztonságos kézbesítés egy egyéni tartomány (például https://www.contoso.com) is. 

A legfőbb attribútumai a HTTPS-szolgáltatás a következők:

- További költség nélkül: a tanúsítvány-beszerzéssel vagy megújítása nem költségekkel és a HTTPS-forgalmat további költség nélkül. Ugyanúgy kell fizetnie GB kilépő a CDN.

- Egyszerű engedélyezése: egy kattintson kiépítés érhető el a [Azure-portálon](https://portal.azure.com). REST API vagy egyéb fejlesztői eszközök segítségével is engedélyezze a szolgáltatást.

- Fejezze be a Tanúsítványkezelő: az összes tanúsítvány beszerzésének és kezelik a kulcsokat meg. A tanúsítványok automatikusan kiépítése és megújítani lejárta előtt. Ez teljesen eltávolítja a szolgáltatás megszakítás miatt a tanúsítvány lejárati ideje kockázatát.

>[!NOTE] 
>Mielőtt engedélyezné a HTTPS PROTOKOLLT támogatja, akkor kell már létrehozott egy [egyéni Azure CDN-tartomány](./cdn-map-content-to-custom-domain.md).

## <a name="enabling-https"></a>HTTPS engedélyezése

Ahhoz, hogy a HTTPS, kövesse az alábbi lépéseket:

### <a name="step-1-enable-the-feature"></a>1. lépés: A funkció engedélyezése 

1. Az a [Azure-portálon](https://portal.azure.com), tallózással keresse meg a Verizon standard vagy prémium szintű CDN-profilt.

2. A végpontok, kattintson az egyéni tartomány tartalmazó végpont.

3. Kattintson az egyéni tartománynak a HTTPS engedélyezéséhez.

    ![Végpont panel](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Kattintson a **a** ahhoz, hogy a HTTPS, majd kattintson a **alkalmaz**.

    ![Egyéni HTTPS párbeszédpanel](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>2. lépés: A tartomány ellenőrzése

>[!IMPORTANT] 
>Tartomány ellenőrzéséhez kell végeznie, mielőtt a saját egyéni tartománynévre HTTPS aktív lesz. Hat nap jóváhagyása a tartományban van. Jóvá nem hagyott hat munkanapon belül kérelmek automatikusan törölve lesz. 

Után az egyéni tartomány HTTPS engedélyezi, a HTTPS-tanúsítvány-szolgáltatója DigiCert érvényesíti tulajdonjogát, a tartomány kapcsolatba lépve a bejegyzés a tartományba, a tartomány megfelelően [WHOIS](http://whois.domaintools.com/) bejegyzés információkat. Forduljon az e-mail cím keresztül történik (alapértelmezés), vagy a telefonszámot, a WHOIS regisztrációs szerepel. 

![WHOIS-rekord](./media/cdn-custom-ssl/whois-record.png)

Ezenkívül DigiCert a megerősítési e-mailt küld a következő címekre. Személyes WHOIS bejegyzés információk esetén ellenőrizze, hogy jóváhagyhatja-közvetlenül az egyik ezeknél a címeknél:

Admin @&lt;a tartomány name.com&gt;  
rendszergazda @&lt;a tartomány name.com&gt;  
@ gazdáját&lt;a tartomány name.com&gt;  
hostmaster @&lt;a tartomány name.com&gt;  
@ postamester&lt;a tartomány name.com&gt;  

Kap egy e-mailt néhány perc múlva, az alábbi példához hasonló kéri, hogy hagyja jóvá a kérést. Ha levélszemétszűrőt használ, adja hozzá admin@digicert.com számára az engedélyezési lista. Ha 24 órán belül nem kap egy e-mailt, a Microsoft támogatási szolgálatához.
    
![Egyéni HTTPS párbeszédpanel](./media/cdn-custom-ssl/domain-validation-email.png)

Ha a jóváhagyási hivatkozásra kattint, a rendszer kéri, a következő online-hoz jóváhagyási űrlapon: 
    
![Egyéni HTTPS párbeszédpanel](./media/cdn-custom-ssl/domain-validation-form.png)

Kövesse az utasításokat az űrlapon; ellenőrzési két lehetőség közül választhat:

- Ugyanazt a fiókot az azonos gyökértartományban; keresztül az összes jövőbeni megrendelések jóváhagyhatja például: contoso.com. Ez az az ajánlott módszer, abban az esetben, ha azt tervezi, hogy további egyéni tartományokat a jövőben ugyanazon gyökértartományának hozzáadása.

- Csak a megadott állomás nevét, amelyet a kérés jóváhagyása További jóváhagyás lesz szükséges további kérelmeknél.

A jóváhagyást követően DigiCert felveszi az egyéni tartománynevet a tulajdonos alternatív neve (SAN) tanúsítvány. A tanúsítvány egy évre érvényes, és automatikus-megújításra kerül lejárta előtt.

### <a name="step-3-wait-for-propagation"></a>3. lépés: Várakozás terjesztése

A tartománynév érvényesítését követően akár az egyéni tartomány HTTPS szolgáltatás aktiválása 6 – 8 órát is igénybe vehet. Ha a folyamat befejeződik, a "Custom HTTPS" az Azure portálon beállítás "Engedélyezett" vannak, és az egyéni tartomány HTTPS panel négy művelet lépéseit el azokat. Az egyéni tartomány már készen áll a HTTPS PROTOKOLLT használja.

![Engedélyezze a HTTPS párbeszédpanel](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>A művelet folyamatban van

A következő táblázat, amely akkor fordul elő, ha engedélyezi a HTTPS művelet végrehajtási állapotát tartalmazza. Miután engedélyezte a HTTPS, négy működtetéshez szükséges lépéseken az egyéni tartomány HTTPS panelen jelennek meg. Az egyes lépések válik aktívvá, további részletek a lépésben jelennek végrehajtás során. Ha egy lépés sikeresen befejeződött, egy zöld pipa jelenik meg látható. 

| A művelet lépés | A művelet lépés részletei | 
| --- | --- |
| 1 Submitting kérelem | Kérés elküldése |
| | A HTTPS-kérelem küldése folyamatban. |
| | A HTTPS-kérést a sikeresen elküldte. |
| 2 tartomány ellenőrzéséhez | Elküldtünk azt kéri, hogy ellenőrizze a tartomány tulajdonosa e-mailt. A megerősítésre vár. |
| | A tartomány tulajdonosa sikeresen érvényesítve. |
| | Tartomány tulajdonjoga ellenőrzési kérelem lejárt (ügyfél valószínűleg nem válaszolt 6 napon belül). HTTPS-tartományra nem lesz engedélyezve. * |
| | Tartomány tulajdonjoga ellenőrzési kérelem vissza lett utasítva, az ügyfél által. HTTPS-tartományra nem lesz engedélyezve. * |
| 3 Tanúsítványtelepítés | A hitelesítésszolgáltató jelenleg kiállító a HTTPS-tartományra engedélyezéséhez szükséges tanúsítvány. |
| | A tanúsítvány már ki van állítva, és telepítése folyamatban van a CDN-hálózathoz. Ez akár 6 órába is telhet. |
| | A tanúsítvány telepítése sikerült CDN hálózathoz. |
| 4 kész | HTTPS sikeresen engedélyezve van a tartományon. |

\*Ez az üzenet nem jelenik meg, kivéve, ha hiba történt. 

Ha a hiba akkor fordul elő, a küldés előtt, a következő hibaüzenet jelenik meg:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="disabling-https"></a>HTTPS letiltása

Miután engedélyezte a HTTPS, később letilthatja. Tiltsa le a HTTPS, kövesse az alábbi lépéseket:

### <a name="step-1-disable-the-feature"></a>1. lépés: A funkció letiltásához 

1. Az a [Azure-portálon](https://portal.azure.com), tallózással keresse meg a Verizon standard vagy prémium szintű CDN-profilt.

2. A végpontok, kattintson az egyéni tartomány tartalmazó végpont.

3. Kattintson az egyéni tartományt, amelynek le szeretné tiltani a HTTPS.

    ![Végpont panel](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Kattintson a **ki** HTTPS letiltásához kattintson a **alkalmaz**.

    ![Egyéni HTTPS párbeszédpanel](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>2. lépés: Várakozás terjesztése

Után az egyéni tartomány HTTPS funkció le van tiltva, amíg érvénybe lépéséhez akár 6 – 8 órát is igénybe vehet. Ha a folyamat befejeződik, a "Custom HTTPS" az Azure portálon beállítás "letiltott" vannak, és a három működtetéshez szükséges lépéseken az egyéni tartomány HTTPS panelen el azokat. Az egyéni tartomány már nem használható a HTTPS.

![Tiltsa le a HTTPS párbeszédpanel](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>A művelet folyamatban van

A következő táblázat, amely akkor fordul elő, ha letiltja az HTTPS művelet végrehajtási állapotát tartalmazza. Tiltsa le a HTTPS, három működtetéshez szükséges lépéseken az egyéni tartomány HTTPS panelen jelennek meg. Az egyes lépések válik aktívvá, további részleteket a lépésben jelennek meg. Ha egy lépés sikeresen befejeződött, egy zöld pipa jelenik meg látható. 

| A művelet folyamatban van | Művelet részletei | 
| --- | --- |
| 1 Submitting kérelem | A kérés elküldése |
| 2 tanúsítvány megszüntetés | Tanúsítvány törlése |
| 3 kész | A tanúsítvány törlése |

## <a name="frequently-asked-questions"></a>Gyakori kérdések

1. *A tanúsítványszolgáltató és használt tanúsítvány típusa?*

    Tulajdonos alternatív neve (SAN) tanúsítvány DigiCert által biztosított használjuk. SAN-tanúsítványnak biztonságossá teheti az egy tanúsítvánnyal rendelkező több teljesen minősített tartománynév.

2. *A dedikált tanúsítvány is használható?*
    
    Jelenleg nem de a programba.

3. *Mi történik, ha a tartomány megerősítési e-mailt nem jelenik meg a DigiCert?*

    Ha 24 órán belül nem kap egy e-mailt, forduljon a Microsoft támogatási szolgálatához.

4. *Kevésbé biztonságos, mint egy dedikált tanúsítvány SAN tanúsítványt használ?*
    
    Egy SAN-tanúsítvány, egy dedikált cert azonos titkosítási és biztonsági szabványok követi. Összes kiállított SSL-tanúsítvány SHA-256 algoritmust használ a fokozott biztonság.

5. *Használható-e az egyéni tartomány HTTPS Akamai Azure CDN?*

    Ez a funkció jelenleg csak Azure CDN Verizon érhető el. Ez a szolgáltatás Akamai Azure CDN támogatása az elkövetkező hónapokban dolgozunk.


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan állíthat be egy [Azure CDN-végpont egyéni tartományhoz](./cdn-map-content-to-custom-domain.md)


